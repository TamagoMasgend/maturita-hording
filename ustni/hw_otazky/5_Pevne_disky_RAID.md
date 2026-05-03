# 5. Pevné disky (HDD), SSD a disková pole RAID

Sekundární úložiště dat představují klíčovou komponentu výpočetního systému určenou k dlouhodobému a energeticky nezávislému (non-volatile) uchovávání informací. Zatímco operační paměť RAM slouží pro okamžité zpracování dat procesorem, pevné disky a SSD jednotky tvoří permanentní archiv operačního systému, aplikací a uživatelských souborů. V současné architektuře se setkáváme s koexistencí mechanických magnetických disků (HDD) s vysokou kapacitou a polovodičových disků (SSD) s extrémní přenosovou rychlostí.

## HDD – Hard Disk Drive (Magnetická úložiště)

Klasický pevný disk je elektromechanické zařízení využívající principu magnetického záznamu. Data jsou uložena na rotujících kovových nebo skleněných deskách, tzv. **plotnách**, pokrytých tenkou feromagnetickou vrstvou. Informace se zaznamenávají změnou orientace magnetického pole v miniaturních doménách na povrchu plotny pomocí čtecích a zápisových hlav.

### Fyzická konstrukce a mechanika HDD
- **Plotny (Platters):** Kotouče upevněné na společné ose, rotující konstantní rychlostí. Moderní disky mají 1 až 8 ploten.
- **Spindle Motor:** Vysoce přesný motor otáčející plotnami. Standardní rychlosti jsou 5 400 RPM (úsporné), 7 200 RPM (standardní) a 10 000–15 000 RPM (enterprise sféra).
- **Vystavovací mechanismus (Actuator):** Lineární motor pohybující rameny, na jejichž koncích jsou čtecí a zápisové hlavy. Hlavy se nedotýkají povrchu, ale "plavou" na aerodynamickém polštáři ve výšce několika nanometrů.
- **HelioSeal (Helium):** Disky s vysokou kapacitou jsou hermeticky uzavřeny a naplněny heliem namísto vzduchu. Helium má 7x nižší hustotu, což snižuje turbulence, tření a zahřívání, umožňující osazení více ploten do stejného 3,5" těla.

### Metody magnetického záznamu a technologie hustoty
- **CMR (Conventional Magnetic Recording):** Tradiční metoda, kde jsou magnetické stopy zapsány vedle sebe s dostatečným rozestupem. Nabízí stabilní výkon při zápisu i čtení.
- **SMR (Shingled Magnetic Recording):** Technologie "šindelového" zápisu, kdy se stopy částečně překrývají jako tašky na střeše. Tím se zvyšuje hustota dat, ale drasticky klesá rychlost náhodného zápisu, protože při změně jedné stopy se musí přepsat celý blok překrytých stop.
- **HAMR (Heat-Assisted Magnetic Recording):** Zápis s pomocí tepla. Laserová dioda na hlavě lokálně zahřeje médium na 450 °C, čímž dočasně sníží jeho koercitivitu a umožní zápis do extrémně malých domén.
- **MAMR (Microwave-Assisted Magnetic Recording):** Využívá mikrovlnné záření generované "spin-torque" oscilátorem k usnadnění změny magnetické orientace bez nutnosti vysokého zahřívání.
- **LBA (Logical Block Addressing):** Moderní způsob adresace, kdy jsou sektory číslovány lineárně od 0 do N, čímž se odstiňuje fyzická geometrie disku od operačního systému.

### Logické členění dat na HDD
- **Stopa (Track):** Jedna z tisíců soustředných kružnic na povrchu plotny.
- **Sektor:** Nejmenší fyzicky adresovatelný blok dat (tradičně 512 B, dnes standard Advanced Format 4 KB).
- **Cylinder (Válec):** Soubor všech stop se stejným poloměrem na všech površích ploten, které jsou přístupné bez pohybu ramen hlav.
- **Cluster (Alokační blok):** Logická jednotka souborového systému tvořená jedním nebo více sektory.

## SSD – Solid State Drive (Polovodičová úložiště)

SSD disky neobsahují žádné pohyblivé součásti. Data jsou uchovávána v čipech typu **NAND Flash**, což jsou nevolatilní paměti založené na tranzistorech s plovoucím hradlem (Floating Gate) nebo technologii Charge Trap. Absence mechaniky přináší řádově nižší přístupovou dobu a vysokou odolnost proti nárazům.

### Architektura paměťových buněk NAND
- **SLC (Single Level Cell):** 1 bit na buňku. Nejvyšší rychlost, extrémní životnost (cca 100 000 cyklů), ale nejnižší hustota a vysoká cena.
- **MLC (Multi Level Cell):** 2 bity na buňku. Vyvážený poměr mezi výkonem a cenou, dnes ustupující.
- **TLC (Triple Level Cell):** 3 bity na buňku. Aktuální standard pro běžná PC. Dobrá kapacita, životnost kolem 3 000 cyklů.
- **QLC (Quad Level Cell):** 4 bity na buňku. Vysoká hustota a nízká cena, ale nižší rychlost zápisu a životnost (cca 1 000 cyklů).
- **3D NAND (V-NAND):** Čipy nejsou stavěny pouze ploše, ale buňky jsou vrstveny vertikálně nad sebou (klidně 232 vrstev), což umožňuje obrovské kapacity na malé ploše.
- **Charge Trap Flash:** Alternativa k Floating Gate technologii, využívající nevodivou vrstvu nitridu křemíku k zachycení elektronů. Je stabilnější při extrémní miniaturizaci a vertikálním vrstvení.
- **Z-NAND / XL-FLASH:** Specializované typy NAND s extrémně nízkou latencí (blížící se Intel Optane), určené pro nejnáročnější databázové operace.


### Řadič a správa SSD (Controller)
- **Wear Leveling:** Algoritmus, který rovnoměrně rozděluje zápisy do všech buněk disku. Zabraňuje tomu, aby se některé buňky opotřebovaly dříve než ostatní.
- **TRIM:** Příkaz operačního systému, který informuje SSD, které bloky dat již nejsou platné (např. po smazání souboru). Řadič je pak může vyčistit na pozadí, což udržuje vysoký výkon zápisu.
- **Garbage Collection (GC):** Interní proces SSD, který na pozadí čistí bloky obsahující smazaná data a konsoliduje je, aby uvolnil místo pro nové zápisy. Tento proces je úzce spjat s funkcí TRIM.
- **Over-provisioning:** Vyhrazená část kapacity disku (neviditelná pro uživatele), kterou řadič používá pro nahrazování vadných buněk a režijní operace jako Garbage Collection.
- **Write Amplification (WA):** Nežádoucí jev, kdy se kvůli nutnosti mazat celé bloky před zápisem do stránek zapíše do flash paměti reálně více dat, než kolik poslal operační systém.
- **HMB (Host Memory Buffer):** Technologie u levnějších "DRAM-less" SSD, která umožňuje disku využít malou část systémové RAM jako vyrovnávací paměť pro mapovací tabulky.


### Rozhraní a formáty SSD
- **2.5" SATA:** Starší formát kompatibilní s HDD. Limitován propustností sběrnice SATA III (cca 560 MB/s).
- **M.2 NVMe:** Moderní standard využívající sběrnici **PCI Express**. Protokol NVMe (Non-Volatile Memory express) je navržen přímo pro flash paměti, podporuje vysoké fronty příkazů a dosahuje rychlostí přes 7 000 MB/s (Gen4) až 12 000 MB/s (Gen5).

## RAID – Redundant Array of Independent Disks

RAID je technologie spojující několik fyzických pevných disků do jednoho logického celku. Cílem je zvýšení výkonu (rychlosti čtení/zápisu), zvýšení bezpečnosti dat (odolnost proti selhání disku) nebo kombinace obojího.

### Základní úrovně RAID
- **RAID 0 (Stripping - Proužkování):**
  - Data jsou rozdělena na bloky a střídavě zapisována na dva nebo více disků.
  - **Výhoda:** Sumace rychlostí všech disků, plná kapacita.
  - **Nevýhoda:** Nulová redundance. Selhání jediného disku znamená okamžitou ztrátu všech dat v poli.
- **RAID 1 (Mirroring - Zrcadlení):**
  - Stejná data se zapisují současně na dva disky.
  - **Výhoda:** Vysoká bezpečnost, při selhání jednoho disku systém běží dál. Rychlejší čtení (lze číst z obou disků najednou).
  - **Nevýhoda:** Kapacita je rovna pouze velikosti nejmenšího disku (50 % u dvou disků).
- **RAID 5 (Stripping with Distributed Parity):**
  - Vyžaduje minimálně 3 disky. Data a paritní informace jsou rozprostřeny mezi všechny disky.
  - **Výhoda:** Odolnost proti selhání jednoho disku. Efektivnější využití kapacity než u zrcadlení (kapacita je n-1 disků).
  - **Nevýhoda:** Výrazné zpomalení zápisu kvůli výpočtu parity. Při selhání disku pole běží v degradovaném režimu a obnova (rebuild) trvá dlouho.
- **RAID 6 (Stripping with Double Parity):**
  - Podobné jako RAID 5, ale používá dva nezávislé paritní bloky. Vyžaduje minimálně 4 disky.
  - **Výhoda:** Odolá současnému selhání až dvou disků.
- **RAID 10 (1+0):**
  - Kombinace zrcadlení a proužkování. Vyžaduje minimálně 4 disky.
  - **Výhoda:** Maximální výkon i vysoká bezpečnost.
  - **Nevýhoda:** Velmi drahé řešení (využitelná je pouze polovina kapacity).

### Implementace a obnova pole
- **Hardware RAID:** Pole řídí dedikovaný řadič s vlastním procesorem a vyrovnávací pamětí (často zálohovanou baterií BBU). Nezatěžuje CPU počítače a je nejstabilnější.
- **Software RAID:** Pole vytváří operační systém (např. Windows Storage Spaces nebo Linux mdadm). Levné řešení, ale spotřebovává výkon hlavního procesoru.
- **Rebuild (Obnova):** Proces po výměně vadného disku za nový. Řadič dopočítává chybějící data z paritních informací nebo zrcadla a zapisuje je na nový disk. Během této doby je pole extrémně vytížené a náchylné k dalšímu selhání.
- **Hot Swap / Hot Spare:** Možnost výměny disku za chodu systému a přítomnost záložního disku, který se do pole zapojí automaticky ihned po detekci závady.

## Pokročilé parametry a výkonnostní metriky

Při výběru a diagnostice úložných systémů hrají roli specifické ukazatele, které definují reálnou propustnost a odezvu systému v závislosti na typu zátěže (sekvenční vs. náhodný přístup).

### Metriky výkonu
- **IOPS (Input/Output Operations Per Second):** Počet operací čtení a zápisu, které disk zvládne vykonat za jednu sekundu. Zatímco mechanické HDD dosahují stovek IOPS, moderní NVMe SSD běžně překonávají hranici milionu IOPS.
- **Latence (Access Time):** Časová prodleva mezi vysláním požadavku a zahájením přenosu dat. U HDD je tvořena součtem doby vystavení hlav (seek time) a čekáním na otočení plotny (latence), u SSD je díky absenci mechaniky téměř nulová (v řádech mikrosekund).
- **Sekvenční rychlost:** Maximální rychlost přenosu velkých souborů. U HDD je limitována hustotou záznamu a RPM, u SSD šířkou pásma sběrnice PCIe a výkonem řadiče.

### Spolehlivost a životnost
- **TBW (Total Bytes Written):** Hodnota u SSD udávající celkový objem dat v terabytech, který lze na disk zapsat před pravděpodobným selháním paměťových buněk. Je to klíčový parametr pro záruku a nasazení v serverech.
- **MTBF (Mean Time Between Failures):** Střední doba mezi poruchami, udávaná v hodinách. Statistický údaj o spolehlivosti mechanických i elektronických komponent.
- **S.M.A.R.T. (Self-Monitoring, Analysis and Reporting Technology):** Monitorovací systém integrovaný v disku, který sleduje kritické parametry (teplota, počet realokovaných sektorů, chyby při čtení) a varuje uživatele před hrozícím kolapsem.
- **Bit Rot (Degradace dat):** Postupné a samovolné poškození dat na médiu vlivem vnějších faktorů (magnetismus, ztráta náboje). Moderní souborové systémy a RAID pole proti němu bojují pomocí kontrolních součtů a periodické kontroly (Data Scrubbing).
- **ECC (Error Correction Code):** Pokročilé algoritmy v řadiči disku, které dokáží detekovat a opravit drobné chyby vzniklé při přenosu nebo ukládání dat do paměťových buněk.

