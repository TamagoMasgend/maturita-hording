# 3. Zpracování dat a relační dotazy jazyka SQL (SELECT)

K interakci a čtení uložených dat ze struktury relační databáze se z hlediska normy SQL nevyužívají moduly DDL, nýbrž se uplatňuje flexibilní a matematicky precizní rodina příkazů nazývaná **DML (Data Manipulation Language)**. Operátor odesílaný enginu uvozený příkazovým slovem `SELECT` tvoří naprostou páteř celého moderního analytického světa.

Hlavní filozofií dotazu `SELECT` je neporušit a neměnit fyzická data uložená na disku (odpovídá operaci Read v paradigmatu CRUD). Cílem je nakázat kompilátoru a procesorům RDBMS (např. MySQL), aby na základě aplikovaných filtrů vyhodnotily relační algebru a vytvořily pro programátora zcela virtuální a matematicky izolovanou množinu odpovídajících n-tic (záznamů). Tuto vyfiltrovanou sadu, zvanou **Result Set**, pak databáze pošle v síťovém paketu klientovi a z paměti RAM ji okamžitě zahodí.

## Základní syntaxe a Aliasování (AS)
Dotaz začíná určením sloupců, které chce aplikace vytáhnout z konkrétní tabulky za klauzulí `FROM`. Použití zástupného znaku `*` (hvězdička) pro vytažení všech sloupců se v produkčním softwarovém inženýrství přísně zakazuje, protože masivně a zbytečně přetěžuje I/O sběrnici i síťovou propustnost.

Deklarací zástupného štítku pomocí klauzule `AS` (nebo jen mezerou) proces přepíše hlavičkové nadpisy exportovaných sloupců do čitelnější podoby pro prezentační vrstvu v aplikačním kódu klienta.

```sql
-- Neefektivní dotaz vytěžující datovou sběrnici (Anti-pattern)
SELECT * FROM seznam_faktur;

-- Bezpečně exaktní vylovení jen potřebných informací s přejmenováním sloupce pro frontend
SELECT unikatni_nazev_kusu, hruba_prodejni_cena AS 'Cena_Faktury_Bez_DPH' FROM produkty;
```

## Odstranění sémantických duplicit (DISTINCT) a stránkování (LIMIT)
V databázích se data v mnoha sloupcích cyklicky opakují (např. desítky zaměstnanců ze stejného "Kraje"). Přeje-li si analytik získat pouze unikátní výčet existujících kategorií na jeden průchod z databázových strojů, musí vynutit matematicky precizní potlačení zrcadlených dat pomocí komprimačního operátoru `DISTINCT`.

Pokud procesní výpis z pamětí zablokuje dotaz s vrácením milionů hodnot zasílaných sítí přímo na frontend, vzniká zahlcení sítě a pád aplikace. RDBMS tomu brání zaříznutím počtu záznamů ze součtu čtení za chodu klauzulí `LIMIT`. Toto hardwarové omezení hraje kritickou roli při optimalizovaném stránkování (Paginaci) webových gridů a tabulek.

```sql
-- Optimalizované profiltrování napříč všemi řádky vracející pouze unikátní jména měst
SELECT DISTINCT mesto_bydliste FROM zakaznici;

-- Databázový ořez načítaných procesů alokovaných v cache na prvních 50 nalezených záznamů
SELECT id_chyby, text_logu FROM soubory_logovani_chyb LIMIT 50;
```

## Systémy řízení filtrů a relačních operátorů (WHERE klauzule)
Zmenšení parsovací fáze diskových zpráv na pouze uživatelem vyžádané relevantní sady obstarává fundamentální filtrační uzávěr pod klauzulí `WHERE`. Stroj aplikuje operátory (`>`, `<`, `=`, `!=` nebo `<>`) a pokud má sloupec definovaný B-Tree index, propaluje rychlostí disky bez sekvenčního hledání milionů prvků.

U aplikací se kombinují další logické moduly operátorů SQL na filtrování dotazů při čtení:
- **Vyhodnocení shody IN**: Softwarové řešení k eliminaci rozsáhlého řetězení OR podmínek. Engine RDBMS prohledává sloupeček a komparuje buňku s množinou prvků.
- **Komparace uzavřených okruhů BETWEEN**: Extrémně účinné filtrování kalendářních a numerických databázových intervalových os. SQL (např. MySQL) bezpečně zahrnuje hraniční stavy s vypsáním rovnou včetně obou dodaných extrémních okrajových mezních zadání.
- **Zástupné masky LIKE (Wildcards)**: Zavedený rovnítkový operátor selhává při vyhledávání nekompletních slov. RDBMS aplikuje porovnávání pomocí vzoru s divokými kartami:
  - `%` (Procento): Zastupuje exaktně po sobě jdoucí libovolnou soustavu znaků (i nulovou délku).  
  - `_` (Podtržítko): Zastupuje exaktní matematickou propustnost jen pro jeden jediný chybějící znak.

```sql
-- Použití logické komparace IN namísto nečitelných řetězců OR
SELECT * FROM firemni_auta WHERE specifikace_barvy IN ('Cervena', 'Modra', 'Cerna');

-- Použití ohraničeného uzavřeného intervalu BETWEEN pro filtrování ceny (včetně krajních 5000 a 15000)
SELECT * FROM evidovane_faktury WHERE cena_k_uhrade BETWEEN 5000 AND 15000;

-- Použití masky LIKE s procentem: Najde všechny záznamy začínající na 'Nov' (Novák, Novotný)
SELECT * FROM personalni_zamestnanci WHERE prijmeni LIKE 'Nov%';

-- Použití podtržítka na exaktní zastoupení jednoho chybějícího bajtového znaku (Pítr, Petr)
SELECT * FROM personalni_zamestnanci WHERE krestni_jmeno LIKE 'P_tr';
```

## Kompilace posloupností a řazení výstupů (ORDER BY)
N-tice vytažené neoptimalizovanými příkazy z diskových řadičů do výstupního proudu nemají fyzicky ani logicky garantované seřazení pořadí (propadávají nečitelně na přeskáčku z fragmentovaných diskových sektorů, jak je hlavička disku zrovna načetla z NVMe / ploten).

K inženýrsky čitelné setříděné posloupnosti sestavy se na straně databázového motoru musí naordinovat organizační klauzule `ORDER BY`. Engine dokáže propustit ke čtení logiku s filtry pro vzestupný růst (`ASC` - Ascending, výchozí chování od nejmenšího nahoru) anebo specifikaci s klesající inženýrskou logikou (`DESC` - Descending, směrem od velkých účtů v odpovědi dolů k nejmenším).

Z hlediska optimalizované podkladové logiky a fáze kompilace (Execution Plan) je `ORDER BY` matematicky ohraničující úkon prováděný jako jeden z úplně posledních kroků kompilace dotazu, tedy až poté, co databáze ořezala všechny tabulky podmínkami `WHERE` a `HAVING`.

## Komplexní praktický expertní příklad podnikového DML dotazu
Ukázková integrace logických dotazů u izolačních struktur v podnikové RDBMS analytice na propojeních filtrů.

```sql
SELECT
    id_objednavky AS 'Cislo Faktury',
    mesto_doruceni,
    cena
FROM 
    objednavky
WHERE 
    status != 'Doruceno' 
    AND mesto_doruceni IN ('Brno', 'Ostrava', 'Olomouc')
    AND cena BETWEEN 100 AND 50000
    AND poznamka LIKE '%VIP%'
ORDER BY 
    cena DESC
LIMIT 5;
```