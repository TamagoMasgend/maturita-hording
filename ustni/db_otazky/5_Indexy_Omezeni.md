---
created: 2026-05-06T23:20
updated: 2026-05-20T13:19
---
# 5. Indexy a integritní omezení v relačních databázích

Fyzický výkon relační databáze RDBMS se neopírá o prostou rychlost pevných disků a procesorů. Pokud administrátor nechá strukturu tabulky běžet ve výchozím stavu bez inženýrského optimalizačního zásahu (tzv. architektura Heap), databázový démon musí při každém dotazu s klauzulí `WHERE` z disku přesunout do paměti RAM postupně absolutně každý uložený řádek a u každého sekvenčně testovat podmínku shody. Tento proces se nazývá **Full Table Scan**. Při tabulkách o 50 milionech záznamech by jedno ověření loginu mohlo paralyzovat serverové procesy na celé minuty.

## Indexování a struktura B-stromů (B-Trees)
Nekompromisní hardwarovou eliminaci tohoto prohledávacího problému řeší inženýrský konstrukt nazývaný **Index**. Index tvoří asymetrickou, nezávislou zrcadlovou datovou strukturu oddělenou od původní tabulky. Zmenšená struktura udržuje ve svém stromovém zápisu výhradně kopii vybraného optimalizovaného sloupce a příslušný paměťový pointer na exaktní blok a sektor v hardwaru disku, kde fyzicky leží onen hledaný plný řádek s ostatními sloupci.

V profesionálních motorech (jako InnoDB pro MySQL či PostgreSQL) se indexy neukládají lineárně, nýbrž do komplexní a matematicky precizně vybalancované hierarchie **B+ stromů (B+Trees)**. Uzly (Nodes) jsou řazeny od kořene dolů do listů. Jakmile klient vyhledává konkrétní číslo, procesor díky matematickým nerovnostem v každém uzlu vyloučí stovky tisíc nerelevantních výsledků jediným odbočením vpravo či vlevo. Prohledávání tak funguje s asymptotickou výpočetní časovou složitostí O(log n), kdy vyhledání 1 záznamu z miliardy zabere hardwaru procesoru pouhých několik mikrosekund (typicky průchod třemi až čtyřmi hladinami stromu).

### Typologie aplikovaných klíčů a indexů
1. **Primární index (Primary Key - PK)**: Nejkritičtější struktura definující podstatu relací. Tabulka musí nést právě jeden absolutně exaktní identifikátor pro n-tici (zpravidla celočíselný atribut `id` s alokačním parametrem `AUTO_INCREMENT`). Hodnota primárního klíče fyzicky nesmí být chybějící (`NOT NULL`) a nesmí se nikdy duplikovat.
2. **Cizí klíč a cizí index (Foreign Key - FK)**: Přebírá hodnotu primárního klíče z jiné entity a tvoří pro něj lokální odkaz. Cizí klíč je stěžejní nástroj k obraně celistvosti sítě – mechanismus nazývaný **Referenční integrita**. Mnoho moderních RDBMS automaticky vytváří na pozadí index pro sloupec cizího klíče (často označovaný jako cizí index), což výrazně zrychluje vyhledávání při operacích JOIN a kontrolách integrity.
3. **Unikátní index (Unique Index)**: Inženýrsky nasazován s cílem zablokovat duplicitu nad jiným atributem, než kterým je primární klíč. Ideální využití je nad polem s e-mailem uživatelů u registračních formulářů. Strom Unique indexu bleskově hlídá vkládaná data a při detekci již existující hodnoty zruší operaci vyhozením chybové výjimky.
4. **Obyčejný index (Secondary Index / Non-Clustered)**: Účelový strom na míru pro zrychlení frekventovaných datových operací čtení `SELECT` do klauzule `WHERE`. Typicky se nasazuje nad sloupcem `prijmeni_osoby`, pokud aplikace často vyhledává uživatele podle tohoto pole.

Při inženýrských optimalizacích se využívá tzv. **Kompozitního klíče (Složeného klíče)**. Primární či Obyčejný index nemusí být tvořen z hodnoty jednoho sloupce. Motor může zřetězit dva nebo více atributů dohromady (např. složit index nad sloupci `id_kurzu` a `id_studenta` v křižovatkové tabulce relace M:N) do jednoho celku a zajistit rychlé prohledávání napříč více podmínkami současně.

### Sdružený vs. Nesdružený index (Clustered vs. Non-Clustered Index)
Z hlediska fyzického uložení dat na disku rozlišujeme dva základní typy indexů:
- **Sdružený (Clustered) index**: Fyzicky organizuje a řadí samotná datová řádky tabulky na disku podle indexovaného klíče. Listové uzly sdruženého indexu obsahují přímo kompletní datové řádky tabulky. Protože fyzická data na disku mohou být seřazena pouze jedním způsobem, každá tabulka může mít **nejvýše jeden sdružený index** (zpravidla na primárním klíči).
- **Nesdružený (Non-Clustered) index**: Má strukturu oddělenou od datových řádků. Listové uzly tohoto indexu obsahují pouze indexovanou hodnotu a ukazatel (pointer / RID / primární klíč) na skutečné umístění celého řádku v tabulce. Tabulka může mít **libovolný počet nesdružených indexů** (např. pro vyhledávání podle příjmení, data registrace apod.).

### Hardwarová daň za implementaci indexů
Implementace indexů představuje v systémové architektuře klasický inženýrský kompromis (trade-off) mezi časem a prostorem (Time/Space tradeoff).
- **Extrémní benefit**: Zcela revoluční zkrácení doby pro čtení (operace `SELECT`).
- **Skrytá degradace výkonu zápisu**: Zpomalení zcela všech operací modifikace paměti (`INSERT`, `UPDATE`, `DELETE`). Ve chvíli vložení nového řádku od aplikace nesmí engine RDBMS provést obyčejné slepé uložení na konec pásky pole disku. RDBMS musí otevřít všechny stromy všech vytvořených indexů patřících tabulce a algoritmicky je překopat, přepočítat B+ větve a posunout pointry. Aplikace tabulky obestavěná 15 indexy tak na serveru čte bleskově, ale zápis jediného logu prodlužuje serverový transakční čas stonásobně na zpracování. Indexy navíc ukrajují na hardwaru znatelnou fyzickou kapacitu diskových polí.

## Integritní a datová omezení (Constraints)
Zodpovědnost za správnost ukládaných dat nepadá jen na validační kontroly v jazyce na webovém backendu (např. Python/PHP). Pro bezpečí struktury databáze si kontrolu nekompromisně vynucuje samotná disková struktura pomocí pevných omezujících mantinelů.

1. **Omezující Datové typy (Data Types)**: Přidělený datový typ tabulce automaticky filtruje nesrovnalosti. Definován formát pro `DATETIME` odmítne alfanumerický řetězec z vnější aplikace a ochrání pole před narušením formátu (Corruption). K limitování obsazení paměti pro řetězce se v 99 % případů využívá typ `VARCHAR(limit)`, který dynamicky seřízne spotřebované místo jen a výhradně na přesný počet zabraných bytů od zadaného znaku, na rozdíl od starého pole `CHAR`, které slepě rezervovalo blok po disku na pevnou max délku doplněnou systémovými neviditelnými prázdnými oddělovači znaků nezávisle na vloženém kratším znění parametru.
2. **Povinnost definice (NOT NULL)**: K zachování matematické logiky u systémů blokuje operaci zápisu pole na prázdno. U kritických buněk pro odeslání (např. číslo pasu pro let) brání RDBMS aplikace operátoru zápisu a po dotazu bez hodnoty pole přeruší transakci jako chybu s výpisem `Column cannot be null`.
3. **Alokování výchozích záruk do buněk (DEFAULT)**: Pokud programovací zpráva při spojování řádků omylem vynechá definici a neodeslala k zápisu určitému sloupci parametr, OS enginu do volné buňky na discích po vložení ihned podvrhne a aplikuje stabilní předvyplněnou systémovou konstantu, aby tabulka netrpěla pro chybějící data.
4. **Logické testy a ověřování operací motoru s CHECK**: Databázový démon je technicky schopen operovat složité výpočetní verifikace u ukládání bez ohledu k datům pro dotaz programátorů zvenčí. Vyhodnocování příchozí logiky ohlídá kontrolní brána zvaná s omezujícím mantinelem na `CHECK`. Vyžaduje splnění matematické formule u odeslání do fronty na paměť pro zapsání dat k uložení na pole u parametru řádku k úspěšné operaci do databází, (například kontrola `CHECK (konečná_cena_produktu > 0)` k eliminování nákupů pod hodnoty z frontendu).

```sql
-- DDL zápis do architektury zakládající stabilní, inženýrsky chráněnou tabulkovou mřížku pro vkládání na pevný disk serverů s integrovanými omezujícími zámky proti zničeným hodnotám z frontendu 
CREATE TABLE certifikovani_uzivatele (
    id_ucastnika INT AUTO_INCREMENT PRIMARY KEY,          -- Celočíselný limitovaný primární identifikátor klíčové zrychlené datové struktury a B+ strom k zamezení opomenutí do ID z RDBMS
    identifikacni_cislo VARCHAR(20) UNIQUE NOT NULL,      -- Kombinace omezení: Hodnota pole nevyhnutelně smí a nesmí chybět pro zadání a zároveň musí bezchybně propadnout indexovým stromem na duplicitu pro RČ klienta
    prijmeni_osoby VARCHAR(80) NOT NULL,                  -- Sloupec s příjmením pro vyhledávání, nad kterým bude vytvořen sekundární index
    vek_v_registraci INT CHECK (vek_v_registraci >= 18),  -- Zajištění striktní limitace s validací v samotném jádře před provedením zapsání z hodnoty na pole k eliminaci osob s propadem omezujícího limitu na uložení k diskovému záznamu pole do DB
    datum_zalozeneho_uctu DATETIME DEFAULT CURRENT_TIMESTAMP -- Systematické generování k uložení stálého časového razítka propadem přes RTC serverovou techniku pro logování chodu z aplikací pro tabulkovou n-tici z OS
);

-- Vyčlenění extra obyčejného databázového indexu navázaného pro zrychlenou alokaci k odesílání do pole vyhledávačů k minimalizování časů pro Full Table Scan čtení disku 
CREATE INDEX idx_analyza_hledani_prijmeni_osob ON certifikovani_uzivatele (prijmeni_osoby);
```