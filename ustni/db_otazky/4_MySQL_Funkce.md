---
created: 2026-05-06T23:20
updated: 2026-05-20T13:18
---
# 4. Architektura a implementace vestavěných funkcí v MySQL

Zpracování surových dat v relačních databázových systémech (RDBMS) nevyžaduje vždy jejich kompletní přesun po síti do aplikační vrstvy (např. do Pythonu nebo PHP) za účelem výpočtu. Tento tradiční přesun by u milionů záznamů způsobil kolaps síťové propustnosti a neúměrné zatížení paměti RAM na straně klienta. RDBMS systémy, jakým je MySQL, proto disponují mohutným, v jazyce C nativně zkompilovaným aparátem **Vestavěných funkcí (Built-in Functions)**. Tyto funkce běží v nejnižší vrstvě databázového motoru, operují přímo nad bloky na disku či v systémové cache a vrací přes síť zpět klientovi již pouze konečný vypočítaný výsledek (často jediný bajt).

Z inženýrského a algoritmického hlediska se funkce dělí do kategorií primárně podle toho, na jak velký kontext řádků se daná matematická operace aplikuje a v jaké fázi exekuce dotazu (Execution Plan) je kompilátorem vyhodnocována.

## Matematické a logické operátory motoru
Databázový kompilátor interpretuje klauzuli `SELECT` nejen jako nástroj pro vytahování uložených dat ze sektorů disku (s navázáním na tabulku přes `FROM`), ale funguje nativně jako vysoce optimalizovaná aritmeticko-logická jednotka (ALU). 
Lze využít klasické matematické operátory pro sčítání (`+`), odčítání (`-`), dělení (`/`) a násobení (`*`). V relační databázi se tato logika obvykle implementuje na úrovni řádku za účelem vygenerování nového, tzv. "vypočítaného sloupce" (Calculated Column) přímo za běhu, aniž by tento sloupec musel zabírat fyzické místo na pevném disku. K získání zbytku po celočíselném dělení se nasazuje striktní modulární aritmetika operátoru `MOD()`, případně zástupný znak procenta `%`.

```sql
-- Izolovaný matematický výpočet na jádře DB bez nutnosti dotazování na jakoukoliv tabulku
SELECT 500 * 1.21 AS 'Vysledek_DPH';

-- Výpočet generovaný za běhu sekvenčně pro každý načtený záznam z fyzické tabulky
SELECT 
    identifikator_produktu, 
    nakupni_cena, 
    (nakupni_cena * 0.80) AS 'Aplikovana_Sleva_20' 
FROM seznam_skladove_techniky;
```

## Skalární (Řádkové) funkce
Třída funkcí, jejíž matematický algoritmus je exaktně uzavřen na kontext **jednoho jediného řádku (n-tice)**. Provede výpočet nad buňkou v aktuálně zkoumaném řádku, transformuje ji a vrátí jednu novou hodnotu, aniž by k tomu potřebovala znát obsah řádku předchozího nebo následujícího.

### Řetězcové funkce (String Manipulation)
Manipulace textových typů `VARCHAR` a `TEXT`.
- **`CONCAT(sl_1, sl_2, ...)`**: Provede zřetězení (zálepení) nekonečného počtu vstupních argumentů za sebe do jednoho textového výstupu. Kritické např. při skládání oddělených sloupců s adresou pro poštovní API.
- **`UPPER(sloupec)` / `LOWER(sloupec)`**: Transformuje veškeré alfanumerické znaky do podoby verzálek (velkých písmen) nebo minusek. Zásadní u unifikace vstupů před porovnáváním s regulárními výrazy.
- **`LENGTH(sloupec)`**: Změří a vrátí délku řetězce v bajtech (u kódování UTF-8 může diakritika zabrat více bajtů než běžný znak).
- **`SUBSTRING(sloupec, start_pozice, delka)`**: Výpočetně vyřízne přesný úsek z původního pole dat a předá jej dál (např. zobrazení pouze prvních 3 číslic směrovacího čísla).

### Funkce pro časovou a datumovou kalibraci
Při ukládání časových známek (Timestamps) se nelze spolehnout na čas klienta odesílajícího dotaz z prohlížeče. Databáze řeší čas na úrovni OS serveru.
- **`NOW()`**: Dotáže se systémového RTC obvodu (Real Time Clock) na serveru a vrací přesný aktuální čas spuštění transakce, typicky v normě `YYYY-MM-DD HH:MM:SS`. 
- **`DATEDIFF(datum_konce, datum_startu)`**: Nativní kalendářní funkce pro výpočet rozdílu dvou dat ve dnech. Engine sám aplikuje složitou matematiku pro řešení přestupných letů a měsíců o 31 dnech, což šetří stovky řádků aplikačního kódu.

### Operace s plovoucí desetinnou čárkou a celými čísly
- **`ROUND(hodnota, pocet_mist)`**: Striktní matematické zaokrouhlení. Pokud následuje číslo 5 a vyšší, zaokrouhluje vždy logicky nahoru.
- **`CEIL(hodnota)`**: Vynucené zaokrouhlení na nejbližší vyšší celé číslo nezávisle na desetinných místech (strop).
- **`FLOOR(hodnota)`**: Vynucené zaokrouhlení na nejbližší nižší celé číslo (podlaha).

```sql
-- Sloučení textových polí a úprava na velikost písmen před vrácením síti
SELECT UPPER(CONCAT(jmeno, ' ', prijmeni)) AS formatovane_jmeno FROM klienti;

-- Použití skalární funkce pro zjištění celkového dluhu na dnešních dnech od data vystavení faktury
SELECT DATEDIFF(NOW(), termin_splatnosti) AS 'Dny_po_splatnosti' FROM seznam_faktur;
```

## Agregační funkce: Matematická redukce rozsáhlých datových sad
Kritický protipól k řádkovým funkcím. Agregační algoritmy neshromažďují data izolovaně na jedné n-tici, nýbrž převezmou celý alokovaný sloupec z milionu vyfiltrovaných řádků naráz, spustí nad ním sekvenční výpočetní cyklus uvnitř registru CPU a výsledný výpočet zkomprimují do **přesně jedné jediné návratové hodnoty**. Redukují tak stovky megabajtů dat na pár bajtů odpovědi.

- **`COUNT(sloupec)`**: Slouží k secvičení celkového počtu nalezených a platných řádků. Variantní zápis `COUNT(*)` počítá fyzické řádky bez ohledu na to, zda jsou vnitřní buňky prázdné (`NULL`), kdežto `COUNT(nazev_sloupce)` ignoruje řádky, kde v zadaném sloupci hodnota chybí.
- **`SUM(sloupec)`**: Sečte celkovou numerickou hodnotu napříč všemi prozkoumanými řádky pole.
- **`AVG(sloupec)`**: Vygeneruje aritmetický průměr zkoumané sady (opět zcela algoritmicky vyřazuje prázdné buňky `NULL` z dělitele).
- **`MAX(sloupec)` a `MIN(sloupec)`**: Vrátí mezní (hraniční) hodnoty z daného sloupce. Neplatí pouze pro čísla, ale efektivně dokážou nalézt nejpozdější datum (`MAX`) nebo abecedně poslední písmeno.

### Logické dělení zpracování přes GROUP BY a restrikce s HAVING
Spuštění agregace na celou tabulku (např. zjištění celkových tržeb nadnárodního řetězce) vrátí jediné číslo. Síla RDBMS spočívá ve schopnosti tabulku těsně před výpočtem logicky "rozřezat" na specifické kategorie (shluky). Klauzule `GROUP BY` instruuje parser, aby nejprve roztřídil všechny záznamy podle stejné nalezené hodnoty v určitém sloupci (např. roztřídit milion faktur do hromádek podle ID pobočky) a teprve následně spustil agregační funkci nezávisle na každou hromádku zvlášť.

Z inženýrského pohledu na fáze kompilace dotazu vyvstává zásadní limitace při snaze odfiltrovat výsledky. Klauzule `WHERE` zpracovává řádky na samém začátku dotazu (hrubé řezání u disku) ještě **předtím**, než se vůbec spustí agregační funkce. Pokud tedy analytik potřebuje vyfiltrovat pobočky, které měly po sečtení průměr tržeb vyšší než 50 000, nelze tento filtr zapsat do `WHERE`, protože průměr v tu chvíli ještě neexistuje. Architektura SQL k tomuto účelu nasazuje dedikovanou post-procesing klauzuli **`HAVING`**, která se spouští v čase výpočtů až po aplikaci `GROUP BY`.

```sql
-- 1. Zploštění celé databáze na jeden záznam: výpočet celkových faktur
SELECT COUNT(*), SUM(castka_k_uhrade) FROM logy_fakturace;

-- 2. Shlukování přes GROUP BY: Výstupem není jedno číslo, ale přesný součet tržeb pro každého jednotlivého identifikovaného prodejce separátně
SELECT identifikator_prodejce, SUM(castka_k_uhrade) AS 'Globalni_Trzba_Zamestnance' 
FROM seznam_transakci 
GROUP BY identifikator_prodejce;

-- 3. Pokročilý inženýrský dotaz s nasazením restrikční klauzule HAVING po provedení logiky shluku
-- Skript vybere a na terminál pošle pouze a výhradně ty identifikátory krajů, které na průměrných prodejích přesáhly limitní hranici 100 tisíc. Ostatní kraje algoritmus po výpočtu z RAM paměti odstraní.
SELECT oznaceni_kraje, AVG(cena_faktury) AS prumerna_cena_kraje 
FROM adresar_zaznamu_pobocek 
GROUP BY oznaceni_kraje 
HAVING AVG(cena_faktury) > 100000;
```