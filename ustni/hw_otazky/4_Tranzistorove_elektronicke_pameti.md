# 4. Tranzistorové elektronické paměti

Elektronické paměti tvoří kritickou součást každého výpočetního systému, neboť slouží k uchovávání binárních dat a instrukcí, které procesor (CPU) aktuálně zpracovává nebo které jsou nezbytné pro start a konfiguraci hardwaru. Z fyzikálního hlediska jsou tyto paměti realizovány jako polovodičové integrované obvody využívající vlastností tranzistorů a kondenzátorů k reprezentaci logických stavů 0 a 1. Moderní paměťová hierarchie je navržena tak, aby vyvažovala propastné rozdíly mezi rychlostí procesoru a kapacitou velkokapacitních úložišť, přičemž klíčovými parametry jsou přístupová doba, datová propustnost a energetická volatilita.

## Klasifikace pamětí podle volatility a přístupu

Paměti lze kategorizovat podle několika základních kritérií, která určují jejich nasazení v architektuře počítače. Nejdůležitějším rozlišením je chování paměti po odpojení elektrického napájení a způsob, jakým lze k datům přistupovat.

### Základní dělení pamětí
- **Volatilní paměti (Nestálé):** Tyto paměti vyžadují k udržení uložené informace neustálý přísun elektrické energie. Po vypnutí napájení dochází k okamžité ztrátě dat. Typickým zástupcem je operační paměť RAM.
- **Nevolatilní paměti (Stálé):** Informace zůstávají uchovány i bez napájení. Dříve se jednalo výhradně o paměti typu ROM, dnes tuto roli přebírají moderní Flash paměti (SSD, USB disky).
- **RAM (Random Access Memory):** Paměť s libovolným (přímým) přístupem. Doba potřebná ke čtení nebo zápisu libovolné buňky je konstantní, nezávisle na její fyzické pozici v paměťové matici. To je zásadní rozdíl oproti sekvenčním pamětím (např. magnetické pásky), kde se k datům musí "přetočit".
- **RWM (Read-Write Memory):** Technické označení pro paměti, které umožňují opakovaný zápis i čtení. V běžné praxi se pro ně vžil termín RAM.

## Operační paměti typu RAM

Operační paměť slouží jako pracovní prostor pro běžící procesy a operační systém. Z konstrukčního hlediska se dělí na dvě hlavní rodiny: statické (SRAM) a dynamické (DRAM). Každá z nich využívá odlišný princip uložení bitu, což drasticky ovlivňuje jejich rychlost, cenu a hustotu integrace.

### SRAM (Static RAM)
Statická paměť využívá pro uložení jednoho bitu tzv. bistabilní klopný obvod, který je realizován nejčastěji pomocí **6 tranzistorů (6T)**. Tento obvod drží svůj stav (0 nebo 1) tak dlouho, dokud je přítomno napájení.
- **Vysoká rychlost:** Díky čistě tranzistorové architektuře dosahuje přístupových dob v řádu 1–10 ns.
- **Vysoká cena a nízká hustota:** Kvůli počtu tranzistorů na jeden bit je čip fyzicky velký a drahý.
- **Použití:** Primárně jako vyrovnávací paměť procesoru (L1, L2, L3 cache) a v kritických vestavěných systémech.

### DRAM (Dynamic RAM)
Dynamická paměť ukládá informaci ve formě elektrického náboje v miniaturním kondenzátoru, který je ovládán jedním tranzistorem (**architektura 1T1C**). Jelikož kondenzátor přirozeně ztrácí náboj (svod), musí být paměť neustále "osvěžována" (Refresh cycle).
- **Destruktivní čtení:** Při každém čtení dojde k vybití kondenzátoru, proto musí být hodnota ihned po přečtení do buňky znovu zapsána, což prodlužuje pracovní cyklus.
- **Vysoká hustota a nízká cena:** Díky jednoduchosti buňky lze na malou plochu umístit miliardy bitů (GB kapacity).
- **Použití:** Hlavní operační paměť počítačů, serverů a grafických karet.

## Architektura a technologie moderních SDRAM

Současné paměťové moduly využívají technologii SDRAM (Synchronous DRAM), která synchronizuje svou činnost s hodinovým signálem sběrnice procesoru. Evoluce se soustředí na zvyšování frekvence a snižování napájecího napětí.

### Generace DDR (Double Data Rate)
Technologie DDR umožňuje přenášet data na vzestupné i sestupné hraně hodinového signálu, čímž efektivně zdvojnásobuje datovou propustnost při stejné frekvenci.
- **DDR4:** Standard od roku 2014, využívá 288 pinů. Pracovní napětí 1,2 V, frekvence typicky 2133–3200 MHz (efektivně). Přinesla vyšší kapacity modulů (až 64 GB na jeden DIMM).
- **DDR5:** Nejmodernější standard, který přináší revoluci v napájení (regulátor napětí PMIC je přímo na modulu, nikoliv na základní desce). Rozděluje modul na dva nezávislé 32bitové kanály, což zvyšuje efektivitu paralelních operací. Frekvence startují na 4800 MHz a míří k hranici 8400+ MHz.
- **LPDDR (Low Power DDR):** Specializované úsporné verze pro notebooky a mobilní telefony, které se často pájejí přímo na desku (není možné je upgradovat).

### Časování a latence pamětí (Timings)
Výkon paměti není určen pouze frekvencí (propustností), ale také zpožděním, než paměť začne data vydávat. Tato zpoždění se udávají v počtech hodinových cyklů.
- **CL (CAS Latency):** Počet cyklů od požadavku na data po jejich odeslání. Nejdůležitější parametr latence.
- **tRCD (RAS to CAS Delay):** Zpoždění mezi aktivací řádku a přístupem ke sloupci v paměťové matici.
- **tRP (RAS Precharge):** Čas potřebný k uzavření jednoho řádku a přípravě na otevření dalšího.
- **tRAS (Active to Precharge Delay):** Minimální doba, po kterou musí být řádek otevřený, aby nedošlo k chybě.

## Spolehlivost a speciální typy pamětí

V profesionálním nasazení a specifických úlohách se využívají mechanismy pro ochranu dat a dedikované čipy s vysokou propustností.

### ECC (Error Checking and Correcting)
Technologie ECC umožňuje detekovat a opravovat jednobitové chyby (Single Bit Flip) a detekovat chyby vícebitové. K tomu využívá kontrolní součty uložené v dodatečných bitech na modulu.
- **Princip:** Na každých 8 bitů dat připadá 1 bit navíc pro kontrolu parity nebo Hammingův kód.
- **Nasazení:** Servery a pracovní stanice, kde by pád systému způsobený náhodným kosmickým zářením (bit flip) mohl mít fatální následky. Vyžaduje podporu procesoru i základní desky.

### Paměti pro grafické karty (VRAM / GDDR)
Grafické paměti jsou optimalizovány pro rozsáhlý paralelní přenosy velkých bloků dat (textury, buffery).
- **GDDR6 / GDDR6X:** Využívají extrémně široké sběrnice a vysoké takty. Na rozdíl od systémové RAM jsou čipy letovány přímo u grafického procesoru (GPU).
- **HBM (High Bandwidth Memory):** Pokročilé řešení, kde jsou paměťové čipy naskládány vertikálně na sebe přímo na pouzdře procesoru a propojeny přes ultrarychlou sběrnici (Interposer). Nabízí nejvyšší možnou propustnost při nízké spotřebě.

## Nevolatilní paměti typu ROM a Flash

Paměti ROM (Read-Only Memory) prošly vývojem od pevných masek po elektricky přepisovatelné buňky. Dnes slouží primárně pro uložení startovacího kódu (BIOS/UEFI) a jako trvalá úložiště.

### Evoluce ROM technologií
- **ROM (Mask ROM):** Data vložena při výrobě fotolitografií. Nelze přepsat.
- **PROM (Programmable ROM):** Čistý čip, do kterého lze "vypálit" data jednou pomocí speciálního programátoru (přetavení mikropojistek).
- **EPROM (Erasable PROM):** Mazatelná silným UV zářením skrze křemíkové okénko na pouzdře čipu.
- **EEPROM (Electrically EPROM):** Mazatelná a přepisovatelná elektricky po jednotlivých bajtech. Pomalá, s omezeným počtem cyklů, ale ideální pro drobné konfigurace.

### Flash paměť
Moderní nástupce EEPROM, který umožňuje mazání dat po celých blocích, což drasticky zrychluje zápis.
- **NOR Flash:** Umožňuje přímý přístup k jednotlivým bajtům (vhodné pro uložení kódu BIOSu, lze z ní přímo spouštět programy - Execute-in-Place).
- **NAND Flash:** Data se čtou po stránkách/blocích. Má extrémní vysokou hustotu a je základem pro SSD disky, SD karty a USB klíčenky.
### SLC, MLC, TLC, QLC technologie
Technologie ukládání více bitů do jedné fyzické buňky určuje klíčové vlastnosti moderních SSD disků. S rostoucím počtem bitů v buňce roste hustota dat a klesá cena, avšak za cenu nižší životnosti a rychlosti.
- **SLC (Single Level Cell):** Ukládá 1 bit na buňku (2 stavy napětí). Nabízí nejvyšší rychlost a životnost (cca 100 000 přepisů). Používá se v high-end serverech.
- **MLC (Multi Level Cell):** Ukládá 2 bity (4 stavy napětí). Dobrý kompromis mezi cenou a spolehlivostí (cca 10 000 přepisů).
- **TLC (Triple Level Cell):** Ukládá 3 bity (8 stavů napětí). Dnešní standard pro běžná SSD (cca 3 000 přepisů).
- **QLC (Quad Level Cell):** Ukládá 4 bity (16 stavů napětí). Nejvyšší kapacita, ale nejnižší životnost (cca 1 000 přepisů).

## Fyzická konstrukce a organizace paměťové matice

Vnitřní struktura paměťového čipu je navržena pro efektivní adresování a minimální zpoždění signálu. Tranzistory jsou organizovány do přísně geometrických celků.

### Paměťová buňka a matice
Základem je matice tvořená řádky (Word Lines) a sloupci (Bit Lines). Průsečík těchto vodičů definuje konkrétní paměťovou buňku, která je přístupná přes adresní dekodér.
- **Adresní dekodér:** Logický obvod, který převádí binární adresu z procesoru na aktivaci konkrétního vodiče v matici.
- **Sense Amplifier (Snímací zesilovač):** Kritická součást DRAM, která detekuje a zesiluje nepatrný náboj z kondenzátoru na úroveň standardní logické hodnoty.
- **Banky a Ranky:** Moderní moduly jsou rozděleny na banky, ke kterým lze přistupovat paralelně, což zvyšuje celkovou propustnost systému.

## Sběrnice a rozhraní paměťového systému

Komunikace mezi procesorem a pamětí probíhá přes specializované cesty, které musí splňovat přísné požadavky na integritu signálu při vysokých frekvencích.

### Typy sběrnic
- **Datová sběrnice:** Přenáší samotné informace. U moderních PC má šířku 64 bitů na jeden kanál.
- **Adresní sběrnice:** Přenáší informaci o tom, se kterou buňkou chce procesor komunikovat. Šířka určuje maximální adresovatelnou kapacitu paměti.
- **Řídicí sběrnice:** Přenáší signály jako READ, WRITE nebo CLOCK, které synchronizují operace mezi komponentami.

### Formáty modulů
- **DIMM (Desktop):** Plná velikost pro stolní počítače, standardně 288 pinů pro DDR4/DDR5.
- **SO-DIMM (Laptop):** Poloviční velikost pro přenosné počítače a mini-PC (NUC).
- **CAMM2:** Zcela nový standard (2024), který nahrazuje SO-DIMM. Nabízí vyšší frekvence a nižší profil, ideální pro tenké herní notebooky.

## Budoucnost a alternativní technologie

Vývoj směřuje k překonání limitů současných technologií, zejména v oblasti energetické náročnosti a rychlosti nevolatilních úložišť.

### Nástupnické technologie
- **MRAM (Magnetoresistive RAM):** Využívá magnetické stavy k uložení dat. Spojuje rychlost SRAM s nevolatilností Flash pamětí.
- **PRAM (Phase-change RAM):** Mění skupenství materiálu mezi amorfním a krystalickým stavem. Slibuje vyšší životnost než současné SSD.
- **Optické paměti:** Využití fotonů místo elektronů pro dosažení rychlostí v řádech terabitů za sekundu.

