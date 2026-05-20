---
created: 2026-05-06T23:20
updated: 2026-05-20T13:18
---
# 1. Databázové systémy a relační databáze

## Účel, princip databáze a zajištění bezpečnosti v IT
Z pohledu softwarového inženýrství nelze data dlouhodobě udržovat a modifikovat pouze v operační paměti RAM procesoru. Pokud by naopak aplikace ukládala gigabajty dat čistě do nestrukturovaných, lokálních textových souborů na pevném disku (tzv. Flat Files, např. CSV), narazila by v komerčním nasazení okamžitě na neřešitelné výpočetní a fyzikální limity. Vyhledávání by vyžadovalo neefektivní sekvenční čtení bajt po bajtu. Navíc pokus stovek uživatelů přepsat stejný řádek textového souboru ve stejný okamžik by vedl k nevyhnutelné destrukci (korupci) dat kvůli konfliktním zámkům v jádře operačního systému. Zabezpečení proti neoprávněnému přístupu by v prostém textu bylo matematicky nemožné.

**Databáze** je v informatice navržena k řešení přesně těchto problémů. Z inženýrského hlediska představuje přísně organizovanou a strukturovanou množinu logických entit, uskladněnou na blokovém zařízení. Mezi surovými daty na discích a aplikačním kódem operuje výpočetně výkonná softwarová vrstva nazývaná **DBMS (Database Management System)**.

Úkolem systému DBMS (např. běžícího démona MySQL serveru) je fungovat jako správce hardwarových operací a zajišťovat, aby transakce vždy splňovaly absolutní pravidla logické a fyzické spolehlivosti systému. Tato pravidla jsou v teoretické počítačové vědě známá pod akronymem **ACID**:
- **A (Atomicity - Atomicita)**: Transakce je neoddělitelná a jednorázová sekvence. Pokud jediný příkaz SQL transakce selže, databázový stroj provede automatický *Rollback* a vrátí tabulky do nedotčeného stavu. Buď se provede všechno bezchybně, nebo vůbec nic.
- **C (Consistency - Konzistence)**: Jakákoliv manipulace se záznamem garantuje, že databáze přejde pouze z jednoho platného stavu do druhého. Vynucují se pravidla navržených tabulek (Cizí klíče, datové typy). Systém nedovolí neplatná odkazování do prázdna.
- **I (Isolation - Izolace)**: Schopnost enginu řešit vysoký souběh (Concurrency). Desítky aplikačních procesů mohou přistupovat ke stejné buňce, avšak DBMS je obsluhuje bezpečně za pomoci složitých technik zamykání řádků (Row-level Locks). Striktně zajišťuje, aby jeden proces během čtení mezivýsledků nedeformoval data procesu dalšímu.
- **D (Durability - Trvanlivost)**: Jakmile engine klientovi přes API potvrdí uložení dat (Commit), data fyzicky přežijí i okamžitou následnou ztrátu napájení celého serveru. Na diskové úrovni se využívá vnitřní žurnál (Redo-Log / Write-Ahead Logging), kam databáze zapíše kroky dříve, než upraví reálné tabulky.

## Diverzifikace databázových systémů (RDBMS vs. NoSQL)
Neexistuje univerzální model uložení dat pro všechny IT potřeby. Podle způsobu vnitřního ukládání informací se systémy dělí na:

- **Relační databáze (RDBMS)**: Dominantní průmyslový standard pro uchování pevných, jasně strukturovaných dat. Informace jsou uchovávány pod striktním schématem tvořeným sloupci, dvourozměrnými tabulkami a matematickými vazbami přes primární a cizí klíče. Ke správě využívá deklarativní jazyk SQL. Data jsou zde silně normalizovaná.
- **Objektové databáze**: Inženýrský pokus implementovat do perzistence na pevné disky přímo softwarové objekty z RAM paměti bez mapování zpět do tabulek. Tyto modely trpěly složitostí správy a komerčně neuspěly oproti relačnímu modelu.
- **NoSQL databáze (Not Only SQL)**: Agilní, asymetrické systémy, postavené pro extrémně škálovatelné distribuované nasazení (Horizontal Scaling) u společností typu Google či Facebook. Zpracovávají neschématická "Big Data", kde pevné sloupce RDBMS nefungují. Obětují část ACID konzistence ve prospěch absolutní rychlosti.
  - *Dokumentové DB*: Ukládají data jako hierarchické JSON (resp. BSON) struktury. Nevyžadují schéma předem (Schema-less).
  - *Klíč-Hodnota (Key-Value DB)*: Extrémně rychlé databáze výhradně v RAM. Ukládají pouze vazbu vyhledávacího řetězce a dlouhého hodnotového bloku (využití jako Cache).
  - *Grafové DB*: Zaměřené výhradně na rychlost prohledávání vazeb a uzlů (kdo s kým interaguje na sociální síti). Při tomto typu prohledávání RDBMS obvykle výkonově havaruje kvůli složitosti operací JOIN.

**Významné tržní produkty (Enginy) v praxi:**
- *Relační RDBMS platformy*: **Oracle Database** (absolutní komerční enterprise vrchol), **Microsoft SQL Server (MSSQL)** (oblíbený pro .NET/C# ekosystém), open-source systémy jako je vysoce populární **MySQL** a jeho logicky i inženýrsky nejvyspělejší konkurent **PostgreSQL**.
- *NoSQL platformy*: **MongoDB** (světová špička dokumentových databází využívaná v JavaScript MERN stacku), **Redis** (in-memory databáze typu klíč-hodnota pro cachování webu) a Neo4j (grafový systém).

## Relační model a inženýrství zakladatele E.F. Codda
Vynálezcem relační algebry, ze které vychází celý svět moderních SQL databází, je britský inženýr a matematik **Edgar Frank Codd**. Působil jako výzkumník v laboratořích IBM a tento koncept publikoval v roce 1970 v práci "A Relational Model of Data for Large Shared Data Banks". V roce 1981 obdržel Turingovu cenu.

Coddův model je postaven na predikátové logice a inženýrském procesu zvaném **Normalizace**. Jde o formátování navržené databázové sítě tak, aby zaručila úplnou eliminaci datové redundance (zbytečné duplicity). Zamezuje se situaci, kdy by byl uložen stejný textový název Města tisíckrát u každé faktury. Redundance zabírá drahé diskové místo a ohrožuje konzistenci dat. Na základě Coddových pravidel jsou databáze štěpeny do **Normálních forem (1NF, 2NF, 3NF)**.

Základní složkou této organizace je **Relace**, kterou v reálné SQL sféře představuje dvourozměrná **Tabulka**.
- Rádky tabulky se odborně nazývají **n-tice (Záznamy / Tuples)**. Každý řádek představuje strukturovaný shluk dat příslušící jednomu sledovanému objektu (např. jedno konkrétní auto). N-tice (řádky) se nesmí mezi sebou uvnitř jedné tabulky absolutně shodovat (odlišeno primárním klíčem) a nedisponují mezi sebou vůbec žádným logickým pořadím.
- Sloupce relace se odborně označují jako **Atributy**. Definují stálou logickou vlastnost objektu s přesně vymezeným doménovým typem dat.

## Grafické modelování a typy kardinality (Relací)
Před tvorbou tabulek navrhují vývojáři softwaru podnikovou logiku na vizuální mapě zvané **ER diagram (Entity-Relationship Diagram)**. Čtvercové bloky představují subjekty – Entity (budoucí tabulky), do kterých se píší jejich Atributy (sloupce). Integrace dvou entit je vyznačena spojnicí nesoucí informaci o **kardinalitě (násobnosti)** spoje, jež diktuje chování stroje při dotazu (často používána notace Crow's Foot).

Základní inženýrské druhy vazeb v relacích:
1. **Relace 1:1 (Jedna ku jedné)**:
   - Každý záznam v primární tabulce A odpovídá výhradně jednomu a naprosto jedinému záznamu v tabulce B, a naopak.
   - *Příklad:* Tabulka entit `Zamestnanec` a `Sluzebni_Vuz`. Jeden ředitel má přiděleno přesně jedno firemní auto. To auto řídí výhradně onen ředitel.
   - Z důvodu úspory diskových operací se tyto entity často slučují do jedné širší tabulky (Denormalizace). Oprávněně se oddělují jen v případě přísné izolace citlivých dat (oddělení sloupce "mzda").

2. **Relace 1:N (Jedna ku mnoha)**:
   - Absolutní, nekompromisní standard tvořící naprostou většinu struktury databází. Jeden řádek z hlavní nadřazené entity (strana Jedna), je referenčně napojen a svázán se stovkami řádků v dceřiné tabulce (strana Mnoho). Databázový stroj tuto vazbu zajišťuje vložením Primárního klíče z nadřazené tabulky jako nového sloupce "Cizího klíče" do tabulky dětí.
   - *Příklad:* Relace mezi `Kategorie` a `Zbozi`. Kategorie "Elektronika" obsahuje stovky různých produktů. Tudíž její referenční "ID 5" figuruje opakovaně ve sloupci `id_kategorie` u všech těchto produktů. Monitor ASUS však patří vždy jen do jedné mateřské kategorie.

3. **Relace M:N (Mnoha ku mnoha)**:
   - Komplexní neschématické struktury, které v RDBMS platformě na úrovni primárních polí a cizích klíčů **nelze do přímého vztahu logicky zapsat ani fyzicky zkonstruovat**. Zastupuje situaci, kdy entita na obou stranách má nekonečně mnoho vazeb na protější záznamy vzájemně.
   - *Příklad:* Entita `Studenti` a `Kurzy`. Jeden student studuje třicet kurzů v semestru. A jeden kurz obsahuje sto zapsaných studentů.
   - *Řešení v RDBMS (Vazební tabulka)*: Původní teoretický svazek `M:N` inženýři zruší a místo něj vygenerují novou, zcela prázdnou fyzickou **třetí (pomocnou) tabulku** – tzv. křižovatkovou tabulku (Junction Table). Tento svazek se odborně rozpadne na dvě samostatné zrcadlově křižující se relace `1:N`. Třetí tabulka neukládá uživatelská data, pouze ukládá vedle sebe celočíselné reference: Cizí klíč studenta a Cizí klíč kurzu. Teprve tato střední křižovatka poskytne databázovému enginu informace k propárování bez pádu struktury. Analytik z ní data vytěží přes složené dotazy příkazem JOIN.

## Praktická inženýrská ukázka tvorby relací (DDL)
Ukázka zápisu vztahů 1:N (vydavatelé -> knihy) a M:N (studenti <-> zápisy <-> kurzy) v jazyce SQL:

```sql
-- 1. Relace 1:N (Jeden vydavatel má mnoho knih, kniha má jednoho vydavatele)
CREATE TABLE vydavatele (
    id_vydavatele INT AUTO_INCREMENT PRIMARY KEY,
    nazev_firmy VARCHAR(100) NOT NULL
);

CREATE TABLE knihy (
    id_knihy INT AUTO_INCREMENT PRIMARY KEY,
    titul_knihy VARCHAR(150) NOT NULL,
    cizi_klic_vydavatele INT,
    -- Definice cizího klíče pro zajištění referenční integrity (1:N)
    FOREIGN KEY (cizi_klic_vydavatele) REFERENCES vydavatele(id_vydavatele)
);

-- 2. Relace M:N rozpadnutá na dvě relace 1:N přes vazební tabulku
CREATE TABLE studenti (
    id_studenta INT AUTO_INCREMENT PRIMARY KEY,
    jmeno_studenta VARCHAR(100) NOT NULL
);

CREATE TABLE kurzy (
    id_kurzu INT AUTO_INCREMENT PRIMARY KEY,
    nazev_predmetu VARCHAR(100) NOT NULL
);

-- Křižovatková (vazební) tabulka pro relaci M:N
CREATE TABLE zapisy_kurzu (
    cizi_klic_studenta INT,
    cizi_klic_kurzu INT,
    datum_zapisu DATE DEFAULT (CURRENT_DATE),
    -- Složený primární klíč (Composite Key) brání duplicitnímu zápisu stejného studenta do stejného kurzu
    PRIMARY KEY (cizi_klic_studenta, cizi_klic_kurzu),
    FOREIGN KEY (cizi_klic_studenta) REFERENCES studenti(id_studenta),
    FOREIGN KEY (cizi_klic_kurzu) REFERENCES kurzy(id_kurzu)
);
```