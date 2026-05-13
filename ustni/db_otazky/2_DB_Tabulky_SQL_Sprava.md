# 2. Architektura kontejnerů a tabulek v RDBMS

Při inženýrském i vývojářském přístupu k modernímu RDBMS prostředí (Oracle, PostgreSQL, MS SQL Server, MySQL) a k manipulaci skrze jazyk SQL je klíčové exaktně pochopit architektonickou izolaci prvků. Administrátor musí striktně oddělovat zastřešující kontejner zvaný "Databáze", který izoluje systémy od sebe, od vnitřních logických datových entit ukrytých bezprostředně v ní (Tabulek).

## 1. Koncepce a systémová definice Databáze (Database)
Na nejnižší úrovni z pohledu operačního systému serveru (linuxového jádra nebo OS Windows), se **Databáze** fyzicky na disku nerealizuje jako abstraktní koncept, nýbrž se ukládá jako zástup dedikovaných nestrukturovaných kompresních datových souborů (datafiles). Ty leží adresovány v kořenovém svazku úložiště a obsahují zkomprimované bloky (pages) operační paměti prohozené na disk. V masivně výkonných enterprise sálových počítačích u korporací dokonce databáze zabírá předdefinované, tzv. Raw alokované diskové oblasti – obchází klasický souborový subsystém OS (ext4, NTFS) a přímo řídí zápis bajtů na cylindry disku pro minimalizaci latence čtecí hlavičky.

Z logického programátorského hlediska tvoří **databáze** uzel s tou nejvyšší prioritou pro rozdělení podnikové infrastruktury. Administrátor na jednom spuštěném SQL stroji běžně spravuje celou desítku izolovaných a rozparcelovaných "Databází". Databáze slouží jako nepřekročitelná bezpečnostní bublina s unikátními restriktivními přístupovými právy (ACL) pro různé skupiny aplikačních uživatelů.
Běžně existuje architektura, kdy jedna instalace služby MySQL serveru obsahuje uvnitř nezávislou Databázi `HR_system` pro personální účetnictví. Hned vedle ní, v hierarchii zcela izolovaná, se nachází druhá Databáze `eshop_produkce` pro veřejný web. Data z obou databází nemohou jakkoli kolidovat a dotazy mezi nimi nelze míchat z jedné aplikační relace.

## 2. Koncepce a vlastnosti Tabulky (Table/Relation)
**Tabulka** (v matematické terminologii E.F. Codda "Relace") figuruje jako vnitřní primitivní stavební jednotka připravená k ukládání dat. Je uskladněná hierarchicky vždy uvnitř předem založené Databáze a nemůže existovat osamoceně vně něj. 

Na úrovni RAM paměti i blokového zápisu disků sestává z exaktní a silně nepřizpůsobivé struktury, složené ze dvou os:
- **Vertikální uspořádání pomocí Atributů (Sloupců)**: Sloupce tvoří pevnou kostru tabulky (tzv. Schéma) k ověření, formátování a ochraně integrity příchozích uživatelských dat. Každý sloupec musí mít inženýrem nařízený stálý datový typ, který určuje formát přijímané informace (např. sloupec celého čísla `INT` znemožní zápis textového znaku a vyhodí aplikační syntax chybu). Sloupce nesmí v průběhu provozu libovolně mizet nebo se měnit řádek od řádku (na rozdíl od moderních NoSQL enginů).
- **Horizontální ukládání přes Záznamy (n-tice / Řádky)**: Tabulka vrství příchozí hodnoty zvenčí jako izolované řádky pod sebe. Konkrétní řádek je n-tice obsahující ucelená data o jednom produktu nebo klientovi. 
Teprve referenčním křížením Cizích klíčů na Primární klíče mezi odlišnými tabulkami k sobě vzniká propojená matematická databázová pavučina – definující Relační model.

## Definice, destrukce a řízení strukturálních objektů pomocí SQL
Příkazový jazyk SQL se interně dělí na několik architektonických podjazyků. Operace striktně vázané na fyzickou definici schémat, inženýrské úpravy typologie sloupců nebo na vymazání diskové infrastruktury nespadají pod klasické každodenní datové dotazování. Fungují absolutně izolovaně formou příkazů zařazených do větve řízení infrastruktury pojmenované **DDL (Data Definition Language)**. 

Během aplikování instrukcí DDL (zejména v enginu MySQL) dochází k naprosto okamžitému a nevratnému zapsání veškerých metadat do transakčního diskového svazku. Této operaci se říká implicitní **Auto-Commit**. Znamená to, že úkony měnící architekturu tabulky už nelze vzít programátorsky zpět pomocí záchranných transakčních `Rollback` procedur. Provedená chyba ve smazání celé tabulky v produkci končí rovnou absolutní likvidací diskových dat z disků.

### A. Tvorba a inicializace prostoru Databáze (CREATE DATABASE)
Nejdříve je nutné na čistém serveru vygenerovat bezpečné kontejnerové prostředí – vytvořit databázi. Zároveň je kriticky důležité nastavit instrukce pro mapování znaků (Charset a Collation), aby systém bezpečně ukládal diakritiku.

```sql
-- Fyzické založení logické matice zvané 'firemni_system'.
-- CHARACTER SET vygeneruje alokaci paměti přes Unicode sadu (dnes utf8mb4 pro emoji a široké znaky). 
-- COLLATE spravuje pravidla setřiďování pro abecedy a ignorování velkých/malých písmen při LIKE vyhledávání.
CREATE DATABASE firemni_system CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

-- Nasměrování toku relace (session) do vytvořené dedikované složky před dalším tvořením tabulek.
USE firemni_system;

-- Extrémně nebezpečná DDL operace končící likvidací a trvalým de-alokováním paměti disku. 
-- Odstraní databázi včetně všech podřízených stromových tabulek bez možnosti odvolání.
DROP DATABASE firemni_system;
```

### B. Inicializační fáze pro budování vnitřní formy Tabulky (CREATE TABLE)
Deklarace vytvoření relace vyžaduje přesný plošný návrh. Každý sloupec musí mít specifikované datové pole, aby minimalizoval zátěž RAM a diskových bloků do budoucna.

```sql
-- Startovní DDL deklarace zhotovení nové entity do enginu OS
CREATE TABLE zamestnanci (
    -- Identifikační atribut formátu celého čísla (INT).
    -- Klauzule AUTO_INCREMENT nařizuje internímu enginu vkládat s každým novým řádkem navyšované číslo, aby předešla kolizním duplikacím. Primární klíč zajišťuje fyzickou stavbu nejrychlejšího B-Tree indexu na disku.
    id_evidence INT AUTO_INCREMENT PRIMARY KEY,          

    -- Omezující atribut určující textový blok s limitem padesáti znaků.
    -- Logické pravidlo NOT NULL uvaluje nekompromisní zákaz uložení řádku s prázdným údajem.
    osobni_jmeno VARCHAR(50) NOT NULL,                 

    -- Těsný paměťový alokátor vyhrazený pro ukládání osmibitového numerického znaku do maxima hodnoty 255 (šetří zdroje oproti INT).
    vek_narozeni TINYINT,                                

    -- Sloupec vyhrazený na standard data.
    -- Integrovaný obranný štít DEFAULT – pokud vývojář v aplikaci zapomene do kódu poslat čas, engine MySQL vloží bezpečně aktuální den z hodin serveru.
    datum_oficialniho_nastupu DATE DEFAULT (CURRENT_DATE)   
);
```

### C. Revize a modifikace těla schématu (Modifikace přes ALTER)
Softwarové aplikace nasazené dlouhá léta podnikovém zatížením naráží na požadavky přidání nových políček. DDL podkategorie `ALTER TABLE` brání v nutnosti smazat a znovu zakládat tabulku při potřebě úprav.

```sql
-- DDL rozšíření aktuálního formátu entity k začlenění nového pole. 
-- Posunuje celou architektonickou šířku tabulky. Každý starší existující záznam na disku dostane po operaci prázdnou zástupnou hodnotu NULL na pozici nového sloupce.
ALTER TABLE zamestnanci ADD COLUMN firemni_email VARCHAR(100);

-- Smazání celého svislého bloku (sloupce), vymazání z metadat a zrušení uložených dat všech řádků v daném sloupci.
ALTER TABLE zamestnanci DROP COLUMN vek_narozeni;

-- Úprava definice pojmenování samotné cílové systémové entity po strukturální reorganizaci.
ALTER TABLE zamestnanci RENAME TO administrativni_pracovnici;
```

### D. De-alokace obřích pamětí a hromadné smazání dat
Vymazání nepoužívaných tabulkových uzlů si žádá rozeznávat ohromné a nevratné rozdíly v příkazech, které se liší podle zátěže na diskovém I/O a na logovacích souborech enginu. 
U standardního příkazu `DELETE FROM` (z rodiny DML dotazů) se databáze trápí sekvenčním mazáním každého jednotlivého řádku do Undo-Logu pro případný Rollback, což trvá u milionů záznamů věčnost a zanechává fragmentaci. Pro efektivní vyčištění dat inženýři nasazují strukturu DDL operací.

```sql
-- DDL Operátor ničící natrvalo absolutně veškeré přidělené místo, zavedené omezující logiky, indexové diskové stromy B+Tree pro prohledávání. 
-- Souběžně maže všechny uchovávané uživatelské řádkové n-tice se sesbíranými daty. Odkaz z klientské aplikace následně padá syntaktickou chybou neexistující tabulky.
DROP TABLE administrativni_pracovnici;

-- Ultimátní inženýrský příkaz databázových strojů spadající pod DDL, koncipovaný pro softwarově optimalizovaný obchvat (bypass) proti těžkému logování na pevném disku. 
-- Databázový systém TRUNCATE neúprosně a okamžitě zahodí veškeré zaplněné n-tice dat, a to bez transakční ochrany, v operaci trvající zlomek milisekundy. 
-- Oproti funkci DROP TABLE se systém po destrukci pole se strukturou tabulky nerozloučí. Zachová zcela prázdnou tabulku, její inženýrské formátové omezení sloupečků a zrestaurované indexové stromy připravené pro okamžité uložení čerstvých řádků z aplikace. 
-- Navíc provádí reset systémového čítače generátoru ID AUTO_INCREMENT bezpečně zpět na nulovou startovní hodnotu.
TRUNCATE TABLE administrativni_pracovnici;
```