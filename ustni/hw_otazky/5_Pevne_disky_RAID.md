---
created: 2026-05-02T18:19
updated: 2026-05-08T12:29
---
# 5. Pevné disky, SSD a disková pole RAID

Sekundární paměťová média zajišťují energeticky nezávislé (non-volatile) uchovávání dat i po odpojení napájení. Zatímco pevné disky operují s magnetickou indukcí a elektromechanickým pohybem, polovodičová úložiště sázejí na ukládání elektrického náboje v tranzistorech.

## Pevné disky (HDD)

Hard Disk Drive ukládá binární data změnou orientace magnetického pole na miniaturních doménách feromagnetické vrstvy.

### Konstrukce a parametry HDD
Základem jsou **diskové plotny** z pevné kovové slitiny nebo skla, rotující na společné ose vřetenového motoru (**Spindle Motor**) běžnými rychlostmi 5 400 až 15 000 otáček za minutu. Nad plotnami se pohybují **čtecí a zápisové hlavy**, upevněné na ramenech vystavovacího mechanismu (**Actuator**), který využívá lineární elektromagnet (kmitací cívku) pro přesné a rychlé polohování. Hlavy udržují mikrometrový odstup od datové plochy díky aerodynamickému polštáři tvořenému rotujícím vzduchem. Disky s vysokou kapacitou často využívají hermetické uzavření s **héliem** (HelioSeal), jež má sedminásobně nižší hustotu než vzduch. Toto prostředí snižuje tření i aerodynamické turbulence a umožňuje osadit do stejného formátu více tenkých ploten, aniž by docházelo k tepelnému přetížení.

Data jsou na povrchu plotny logicky dělena na **sektory**, což je nejmenší adresovatelná jednotka (typicky 512 bajtů nebo 4 KB u novějších modelů). Sektory do sebe skládají soustředné kružnice zvané **stopy** (Tracks). Souhrn stop o stejném poloměru přes obě strany všech ploten tvoří **válec** (Cylinder). Pro plynulé adresování bloků dat operačním systémem se aplikuje režim **LBA** (Logical Block Addressing), který hardwarovou geometrii zcela abstrakčně ukrývá za sekvenční lineární pole. Jakmile dojde k plánovanému nebo náhlému odpojení zdroje energie, zbytkový proud roztočeného motoru zaktivuje mechanismus stažení hlaviček do bezpečné **parkovací pozice**, zamezující ničivému pádu hlav na datovou vrstvu.

Přístupová doba HDD je definována jako součet času na vystavení hlavičky nad danou stopu (**Seek time**) a čekání na natočení požadovaného sektoru přímo pod ni (**Latency**), což omezuje hodnoty náhodných operací (IOPS) na řádově stovky úkonů za sekundu.

### Technologie magnetického záznamu
Když fyzikální limity tradičního podélného záznamu vyústily ve spontánní ztrátu dat kvůli superparamagnetismu, zavedl se **kolmý záznam** (PMR / CMR), jenž orientuje vektory magnetické indukce vertikálně do hloubky povrchu. Tímto se ušetřila plošná kapacita. K další extenzivní kompresi dat dochází nasazením **šindelového záznamu** (SMR). U tohoto typu jsou zápisové stopy širší než čtecí a částečně se vzájemně překrývají. Výsledkem je razantní nárůst kapacity. Technologie má však zásadní omezení při náhodném zápisu; aktualizace jednoho bitu vynutí nutnost přečíst, dočasně uložit a zpětně lineárně zapsat velký svazek sousedících stop, což markantně navyšuje časové latence operací.

Prolomení fyzikálních limitů očekávají budoucí metody **HAMR** (Heat-Assisted Magnetic Recording) nebo **MAMR** (Microwave-Assisted). Laserový pulz či mikrovlnný rezonátor na okamžik rozehřeje konkrétní miniaturní doménu. Přidáním tepelné energie dojde ke snížení koercitivity kovu, materiál se stane magneticky tvárným, umožní zápis úzkou hlavou a během nanosekund vychladne zpět do rigidního, stabilního stavu.

## Polovodičová úložiště (SSD)

Solid State Drive zcela postrádá mechanické prvky a k zachování logických stavů nula a jedna vnáší elektrický náboj do mřížky NAND flash pamětí. Poskytují bezprecedentní přístupovou dobu a mechanickou rezistenci vůči otřesům.

### Architektura paměťových buněk NAND
Ústřední prvek SSD představuje tranzistor typu MOSFET modifikovaný o plovoucí hradlo (Floating Gate) či vrstvu oxidu křemíku (Charge Trap). Zachycením a uvězněním elektronů v izolovaném hradle dochází k modifikaci prahového napětí, což řídicí elektronika při čtení překládá do binární informace.

Podle množství bitů uvězněných v jedné buňce rozdělujeme NAND čipy do generací: **SLC** (Single Level Cell – 1 bit), **MLC** (2 bity), **TLC** (3 bity) a nejnovější **QLC** (4 bity). Implementace vícebitových vrstev geometricky zvyšuje kapacitu a sráží výrobní cenu. Nese s sebou však kompromis – nutnost rozlišovat mezi šestnácti různými stavy prahového napětí u čipu QLC degraduje výkon zápisu a snižuje životnost buňky, definovanou přes parametr **TBW** (Total Bytes Written). Vzhledem k rychlému fyzikálnímu vyčerpání izolační vrstvy tranzistorů přešli výrobci k integraci do hloubky. Namísto další miniaturizace obvodů v ploše čipu se buňky staví vertikálně pomocí technologií **3D NAND** (v desítkách až stovkách aktivních vrstev nad sebou).

### Řadič a optimalizační nástroje
Paměť Flash nedokáže přímo přepsat stav jedné buňky bez jejího předchozího kompletního vymazání. Mazací procedury jsou navíc architektonicky prováděny plošně po blocích. Koordinaci proto musí bezvýhradně zastat logický **řadič** vybavený vrstvou **FTL** (Flash Translation Layer).

FTL abstrahuje fyzické bloky paměti před operačním systémem a zajišťuje procesy optimalizace. Algoritmus **Wear Leveling** sleduje počty přepisů napříč čipem a směruje zápisy do nejméně využitých domén, čímž rovnoměrně prodlužuje hardwarovou výdrž disku. Během zátěže operačního systému pomáhá instrukce **TRIM**. Umožňuje z nadřazené úrovně softwaru oznámit řadiči adresy odstraněných souborů. Interní mechanismus **Garbage Collection** pak ve volném čase tyto označené datové bloky interně seskupí a preventivně smaže. Udržuje tak bloky čisté, díky čemuž při reálném zápisu odpadá latence cyklu mazání. 

Fyzická rozhraní SSD kopírují vývoj sběrnic. Pro starší nasazení trvá kompatibilita skrze formát 2.5" pro sběrnici **SATA** (maximum 600 MB/s). Současný aplikační standard diktuje sběrnice PCI-Express pod specifikací karet **M.2**. Ty komunikují s procesorem v režimu protokolu **NVMe** (Non-Volatile Memory Express), otevírají obrovskou hloubku fronty instrukcí a poskytují propustnost dosahující teoretických limitů PCIe.

## Disková pole RAID

Kritickým aspektem síťových systémů, produkčních databází nebo NAS stanic je bezpečnost a paralelizace toku dat. **RAID** (Redundant Array of Independent Disks) abstrahuje pole tvořené z více magnetických disků či SSD jednotek na jediné logické úložné zařízení. Management řeší dedikovaný hardwarový řadič vybavený vlastním procesorem s cache pamětí, nebo softwarová utilita nad operačním systémem generující zátěž systémovému CPU.

### Bezpečnostní a paralelizované úrovně (Topologie)
- **RAID 0 (Proužkování / Striping):** Data se deterministicky dělí do fixních bloků napříč celým polem. Architektura teoreticky násobí čtecí i zápisovou rychlost dle počtu disků. Defektem jediného člena padá logická integrita celého řetězce (zcela chybí redundance).
- **RAID 1 (Zrcadlení / Mirroring):** Dva nebo více disků udržují identické bitové kopie na sekundární zrcadla. Selhání primárního zařízení nemá vliv na funkčnost produkce. Celková dostupná kapacita odpovídá specifikacím nejmenšího osazeného disku, zatímco zápis limituje rychlost nejpomalejší jednotky. Zaručuje vysokou čtecí propustnost.
- **RAID 5 (Distribuovaná parita):** Distribuuje data a matematické samoopravné kódy (paritu vypočtenou logickou operací XOR) střídavě napříč alespoň třemi disky. Pole udržuje funkčnost vůči fatálnímu výpadku libovolné jednotky při celkové efektivní kapacitě (N-1 disků). Systém generuje drobnou výpočetní latenci u zápisu z důvodu kalkulace chybových kódů.
- **RAID 6 (Dvojitá parita):** Robustní integrace dvou ortogonálních sad paritních součtů s požadavkem na minimum čtyř disků. Toleruje permanentní zničení dvou fyzických jednotek současně za cenu mírné degradace čtecích a zápisových dob oproti RAID 5.
- **RAID 10 (1+0):** Kaskádová topologie aplikující spolehlivost logického zrcadlení z vrstvy 1 do distribučních prvků vrstvy 0 (minimum čtyři disky). Výsledkem je vysoká aplikační propustnost chráněná redundancí s inherentní padesátiprocentní ztrátou hrubé kapacity zrcadlením. Náhradu selhaného zařízení obstarává rutinní fáze **Rebuild**, u níž subsystém exaktně dopočítává chybějící data.
