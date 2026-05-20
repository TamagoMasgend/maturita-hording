---
created: 2026-05-06T23:20
updated: 2026-05-20T13:19
---
# 6. Spojování tabulek v relačních databázích (Operátory JOIN a UNION)

Základní inženýrskou stavební architekturou relačních systémů (RDBMS), postavených na axiomech E.F. Codda, je matematický proces označovaný jako **Normalizace**. Ve snaze zajistit striktní datovou integritu, zamezit aktualizačním anomáliím a eliminovat jakoukoliv redundanci v záznamech (duplicity), databázoví architekti rozřezávají a strukturují komplexní firemní dokumenty do desítek izolovaných entitních tabulek. 

Tato architektura však pro klientský aplikační software (frontend nebo analytický dashboard) představuje problém. Zákazník nedokáže interpretovat holé referenční číslice cizích klíčů namísto reálných názvů produktů či jmen autorů. Analytická vrstva vyžaduje ucelené a srozumitelné reporty. RDBMS proto musí v operační paměti RAM provést komparační spojení a z desítek tabulek vybudovat jeden ucelený dočasný pohled (Result Set). 

K tomuto logickému sešití izolovaných entit operuje SQL s rodinou fúzních příkazů zvanou **JOIN**. Algoritmus pod kapotou funguje na principu kartézského součinu obou tabulek, který je okamžitě drasticky odfiltrován omezující klauzulí `ON`, jež vyžaduje exaktní shodu mezi Primárním klíčem jedné tabulky a Cizím klíčem tabulky druhé.

## Relační spojování do šířky: Typologie příkazu JOIN
Rozdíl v nasazování jednotlivých operátorů spočívá výhradně ve filozofii zpracování průniků nad množinami dat (lze si představit jako Vennovy diagramy). Rozhoduje se o tom, jak systém naloží se záznamy, které na druhé straně nenašly svůj párový protějšek.

### 1. Striktní a exaktní spojení (INNER JOIN)
Nejpoužívanější a matematicky naprosto nekompromisní metoda k vymezenému spojování polí. Představuje přímý logický průnik středů obou množin.
- **Zpracování**: Systém načte záznam z první (levé) tabulky a pokusí se vyhledat identický odkaz s definovaným klíčem ve druhé (pravé) tabulce. B-Tree indexy tento proces zkracují z minut na zlomky milisekund.
- **Návratová logika**: Aplikační vrstvě jsou odevzdány **pouze ty záznamy, které mají prokazatelnou logickou shodu na obou stranách**. Jakýkoliv záznam, jemuž chybí párovací klíč, je databázovým enginem nekompromisně odstraněn z finálního výsledku.
- *Inženýrské nasazení*: Typicky se využívá pro generování faktur. Dotaz spojí tabulku `Zakaznici` a tabulku `Objednavky`. Výsledkem jsou pouze aktivní nakupující. Zákazníci, kteří se pouze registrovali a nikdy nic nekoupili, jsou algoritmem z výpisu tiše zahozeni.

### 2. Ochrana primárních entit (LEFT JOIN / LEFT OUTER JOIN)
Rozšířená operace využívaná tam, kde vývojář nesmí přijít o výpis prioritních dat z levé entity (první uvedená tabulka za klauzulí `FROM`).
- **Zpracování**: Kompilátor vytvoří seznam ze všech záznamů levé tabulky a ke každému řádku se pokouší dotáhnout data z tabulky pravé na základě klauzule `ON`.
- **Návratová logika**: Systém vypíše **všechny existující záznamy z levé tabulky, zcela nezávisle na tom, zda pro ně vůbec existuje shoda na pravé straně**. Pokud na pravé straně shoda neexistuje, databáze řádek nesmaže. Místo toho jej propustí na výstup a do chybějících sloupců z pravé tabulky vloží zástupnou nulovou informaci `NULL`.
- *Inženýrské nasazení*: E-shop potřebuje vypsat kompletní katalog všech `Produktu` na skladě s připojenými hodnoceními z tabulky `Recenze`. Použití INNER JOIN by z katalogu odstranilo novinky, které ještě nikdo neohodnotil. LEFT JOIN naproti tomu vypíše všechny produkty; u novinek bude ve sloupci pro recenzi svítit `NULL`.

### 3. Zrcadlové převrácení priorit (RIGHT JOIN / RIGHT OUTER JOIN)
Identická logická struktura a technická funkčnost zpoza zrcadlového matematického obratu oproti `LEFT JOIN`.
- **Zpracování**: Databázový vyhledávač upřednostní pro ochranu k nevyřazení výhradně strukturu sekundárně zařazenou (tabulka na pravé straně zápisu za slovem `RIGHT JOIN`).
- **Návratová logika**: Dojde ke zpracování a propsání všech záznamů pravé větve. Chybějící kousky dat ze slepé levé tabulky jsou v Result Setu doplněny hodnotami `NULL`. 
- *Zvyklosti v praxi*: Programátoři se tomuto zápisu v praxi často vyhýbají. Dotazy psané pro čtení shora dolů a zleva doprava jsou přirozenější. Cílový `RIGHT JOIN` se dá pohodlně simulovat otočením pořadí tabulek v kódu a aplikováním klauzule `LEFT JOIN`.

### 4. Obousměrná komparační fúze (FULL OUTER JOIN)
Specifická a výpočetně vysoce zatěžující metoda průniku nad disky paměti serverového clusteru, představující kompletní matematické sjednocení množin se zachováním všeho z obou zdrojů.
- **Návratová logika**: Systém nejprve spojí všechny záznamy, které sedí logickými klíči (chování INNER JOIN). Následně připojí i všechny osamocené záznamy z levé tabulky a chybějící pravá data doplní o `NULL` (chování LEFT JOIN). Nakonec připojí i všechny osamocené záznamy z pravé tabulky a chybějící levá data doplní o `NULL` (chování RIGHT JOIN).
- *Upozornění k MySQL*: Nejpoužívanější open-source engine na světě, **MySQL**, tuto klauzuli nativně ve svém parseru vůbec nepodporuje. Plné vnější spojení se v MySQL programuje oklikou pomocí nástroje pro vertikální spojení: provede se `LEFT JOIN`, následně `UNION` a pod něj se připíše `RIGHT JOIN`.

## Vertikální fúze nezávislých dotazů (Operátor UNION)
Zatímco příkazy z rodiny `JOIN` svazují sloupce tabulek horizontálně a propojují je logicky "do šířky" do rozsáhlé výsledné tabulky na základě cizích klíčů, operátor `UNION` vykonává proces odlišný. Jde o sjednocení "do výšky".

Operátor `UNION` poskládá na sebe (vertikálně sešije shora dolů) výsledky ze dvou zcela izolovaných dotazů `SELECT`. Tvoří vrstvení dat na jeden štos bez vzájemných referenčních vazeb. K této operaci platí tvrdé inženýrské pravidlo: **Struktury obou spojovaných dotazů musí obsahovat absolutně shodný počet sloupců a vracet shodné (nebo konvertovatelné) datové typy**. Jinak RDBMS proces zruší s chybou `Syntax Error`.

Standardní `UNION` má v enginu MySQL zabudovanou skrytou funkci – automaticky před odesláním výsledku spustí těžkou deduplikační filtraci a z výsledku vyškrtne naprosto stejné, duplikované řádky. Tento skrytý krok zatěžuje procesor. Pokud si je vývojář jistý, že duplicity neexistují, nebo je na výstupu naopak požaduje, musí nasadit odlehčující příkaz **`UNION ALL`**, který kompilátoru nařídí deduplikaci přeskočit a poslat data k síti okamžitě.

## Praktická inženýrská ukázka nasazení DML operátorů

```sql
-- 1. Exaktní INNER JOIN: Zákazníci se spárovanými fakturami.
-- Využití zástupných aliasů (z, f) pro kratší zápis tabulek a optimalizaci dotazu.
SELECT
    z.krestni_jmeno AS 'Zakaznik',
    f.vyfakturovana_cena AS 'Suma Faktury'
FROM
    databaze_zakazniku z
INNER JOIN
    ucetni_knihy f
    ON z.unikatni_id_zakaznika = f.cizi_klic_zakaznika;

-- 2. Asymetrická ochrana přes LEFT JOIN: Výpis všech knih z katalogu,
-- včetně těch, které dosud nemají žádné záznamy o počtu kusů na lokálním skladě.
SELECT 
    katalog.nazev_knihy, 
    sklady.aktualni_pocet_kusu
FROM 
    katalog_vydanych_knih katalog
LEFT JOIN 
    skladove_zasoby sklady 
    ON katalog.id_knihy = sklady.kniha_id;

-- 3. Izolovaná fúze bez relační vazby přes UNION:
-- Sjednocení e-mailových adres ze dvou odlišných a logicky nepropojených systémů podniku pro potřeby hromadného rozeslání newsletteru.
SELECT firemni_email FROM seznam_aktivnich_zamestnancu
UNION
SELECT kontaktni_email FROM seznam_byvalych_uchazecu_o_praci;
```