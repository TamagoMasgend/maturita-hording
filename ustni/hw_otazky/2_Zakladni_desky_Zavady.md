---
created: 2026-05-02T09:23
updated: 2026-05-08T07:22
---

z # 2. Základní desky (Motherboards), Sběrnice, Rozšiřující karty a Závady PC

**Základní deska (Motherboard / Mainboard)** je hlavní a nejkomplexnější vícevrstvý plošný spoj (PCB - Printed Circuit Board) počítačového systému. Funguje jako centrální komunikační a napájecí uzel, který zajišťuje mechanické uchycení, distribuci elektrické energie a vysokorychlostní datový přenos mezi všemi klíčovými hardwarovými komponentami (CPU, RAM, GPU, úložiště) a poskytuje I/O rozhraní pro připojení periferií.

## Hlavní části základní desky

Základní deska (obvykle sestavená z vysoce kvalitního sklolaminátu FR-4 a mědi, složená klidně i z více než 8 vodivých vrstev napresovaných na sebe) integruje desítky tisíc mikroobvodů, spojů a rozhraní:

1. **Patice procesoru (Socket):** Fyzický a elektrický konektor sloužící pro vložení procesoru. Každý výrobce má své standardy.
   - **Intel:** Využívá **LGA (Land Grid Array)** – piny jsou extrémně křehké a nachází se přímo v patici na desce. Např. patice LGA 1700 (pro 12. až 14. generaci Core), nová LGA 1851 (pro Core Ultra).
   - **AMD:** Historicky využívalo **PGA (Pin Grid Array)** (např. patice AM4, kde jsou piny přímo na CPU), dnes s platformou AM5 (od Ryzen 7000) přešlo kompletně na spolehlivější LGA. Pro servery a HEDT segment se využívají rozsáhlý patice (TRX4, SP5).
   - **BGA (Ball Grid Array):** Metoda používaná typicky u notebooků, malých NUC počítačů a konzolí. Procesor nemá měnitelnou patici, ale je napevno připájen k základní desce pomocí mřížky roztavených cínových kuliček (tzv. SoC - System on Chip).

2. **Paměťové sloty (DIMM / SO-DIMM):** Podlouhlé konektory určené k osazení operační pamětí (RAM). Dnešním absolutním standardem jsou paměti DDR5, na starších sestavách se nachází DDR4. Notebooky (nebo tzv. SFF minipočítače) používají jejich zkrácenou verzi zvanou SO-DIMM, případně dnes zcela nové, za chodu zapájené moduly CAMM2 (šroubované destičky paměti snižující elektromagnetické rušení a šetřící tloušťku). Paměti se vždy zapojují sudě do tzv. **Dual-Channel** (nebo Quad-Channel u serverů) architektury, což efektivně zdvojnásobuje šířku sběrnice (propustnost) mezi CPU a RAM.

3. **Napájení desky a kaskáda procesoru (VRM - Voltage Regulator Module):**
   Základní deska čerpá energii ze zdroje přes rozsáhlý **24-pinový ATX konektor** a specificky pro procesor využívá vyhrazené **4+4 pinové EPS (ATX12V) konektory**.
   - Zdroj dodává do konektoru EPS na desce surových 12 V. Dnešní procesory však pracují na velmi nízkém napětí (často kolem 1,0 V - 1,4 V). Kdyby do CPU prošlo přímo 12 V, čip by se okamžitě spálil a zničil.
   - Soustava **VRM (tzv. fáze)**, umístěná okolo patice pod hliníkovými chladiči (Heatsinky), toto napětí elektronicky "rozseká" a sníží s obrovskou účinností. Zároveň dochází k extrémnímu nárůstu proudu (špičkový procesor v zátěži odebírá i více než 200 Amperů).
   - **Kvalita VRM fází (Mosfety, cívky, japonské kondenzátory)** je primárním důvodem obrovských cenových rozdílů u desek. Určuje, jak čisté a naprosto stabilní napětí do CPU teče a určuje bezpečné limity přetaktování (Overclocking).

4. **Čipová sada (Chipset):**
   Hlavní řídící a komunikační uzel pro řízení datových toků a periferií na desce. Historicky byla architektura rozdělena na dva klíčové čipy:
   - **Northbridge (Severní můstek):** Staral se o bleskovou vysokorychlostní komunikaci mezi CPU, operační pamětí (RAM) a grafickou kartou (PCIe). Dnes tento čip na desce nehledejte, z důvodu snížení latence byl kompletně absorbován přímo do křemíku samotného procesoru.
   - **Southbridge (Jižní můstek):** Přežil a dnes ho na desce označujeme jako **PCH (Platform Controller Hub)** (bývá zakrytý vlastním pasivním hliníkovým chladičem). Řídí rozsáhlý tok dat z pomalejších periferií – kontroluje veškeré USB porty, SATA řadiče, síťové karty a audio kodek.
   - S procesorem komunikuje přes dedikovanou vysokorychlostní dálnici (u Intelu tzv. **DMI rozhraní**, u AMD vyhrazená tlustá PCIe linka). Typ chipsetu určuje výbavu, počet PCIe linek a funkční schopnosti desky. Typicky Intel má řady: **H-série** (nejlevnější), **B-série** (mainstream) a **Z-série** (drahé modely odemykající overclocking). AMD má obdobně **A620**, **B650** a extrémní **X870**.

5. **BIOS / UEFI firmware a baterie (CMOS):**
   Na desce je připájený malý paměťový čip **(EEPROM, dnes výhradně typu SPI Flash)**, na němž je uložen kritický startovací mikrokód počítače. Archaický textový **BIOS (Basic Input/Output System)** byl před lety kompletně nahrazen moderním **UEFI (Unified Extensible Firmware Interface)**. UEFI přináší plnohodnotné grafické rozhraní ovládané myší, podporu zavádění OS z disků větších než 2 TB (díky tabulce GPT) a bezpečnostní funkci **Secure Boot** blokující rootkity.
   - K udržení času při vypnutém PC má deska malou plošnou 3V lithiovou baterii **CR2032**, která udržuje při životě obvod **RTC (Real Time Clock)** a malou paměť **CMOS**, kde jsou uložena tvoje uživatelská nastavení taktu a napětí. Pokud uživatel pokazí nastavení UEFI (PC nenastartuje), lze vytažením této baterie nebo zkratováním vyhrazených pinů provést tzv. **CMOS Clear** a resetovat desku do továrního nastavení.

6. **Úložná rozhraní a interní konektory (Headers):**
   - **SATA III (6 Gb/s):** Klasické "elkové" konektory pro plotnové disky (HDD) a 2.5" SSD disky. Dnes fyzicky naráží na rychlostní propustnost max. 600 MB/s.
   - **M.2 Slot:** Moderní vysokorychlostní plošný slot přímo na desce, napojený na PCIe linky (využívající protokol **NVMe**). Umožňuje rychlosti i přes 10 000 MB/s bez nutnosti tahat kabely. Nejběžnější délka disku nese kód **2280** (22 mm šířka, 80 mm délka).
   - **Front Panel (F_PANEL):** Shluk drobných pinů v rohu desky pro zapojení spouštěcího tlačítka (Power SW), resetu a indikačních LED diod aktivity z přední strany PC skříně.
   - **Interní USB a Audio (F_AUDIO):** Headery na desce pro vyvedení sluchátkového 3.5mm jacku a rychlých USB portů na přední masku skříně.
   - **Napájení ventilátorů (SYS_FAN, CPU_FAN):** 4-pinové konektory pro připojení chladičů. Čtvrtý pin zajišťuje tzv. **PWM (Pulse Width Modulation)** regulaci, díky které deska dynamicky plynule mění otáčky větráků na základě měřených teplot procesoru, což zajišťuje maximální ticho v klidu a plný výkon v zátěži.
   - **RGB/ARGB Headery:** Piny s 12V (RGB) nebo 5V (ARGB) napětím pro napájení a plnou synchronizaci LED světelných pásků a větráků se softwarem základní desky (např. Aura Sync).

7. **Rozšiřující sběrnice a sloty (PCIe):** Místa, kam zapojujeme všechny moderní grafické či rozšiřující adaptéry (více detailů v další sekci níže).

## Architektura sběrnic a PCIe (Peripheral Component Interconnect Express)

Sběrnice (Bus) není nic jiného než tlustý svazek komunikačních linek v mědi tištěného spoje. Moderní architektura opustila paralelní posílání (kdy signály přeskakovaly a rušily se na vyšších taktech) a přešla čistě na hyper-rychlé sériové spoje.

- **Současný standard PCIe (PCI Express):**
  Nejdůležitější sériová sběrnice všech dob. Na rozdíl od starého sdíleného PCI (kde každá karta ubírala kapacitu ostatním), používá PCIe exkluzivní **"Point-to-Point"** spojení pomocí switchů – zařízení se mezi sebou o dálnici neperou. Přenáší data obousměrně ve formě tkz. **Linek (Lanes - značené x1, x4, x8, x16)**.
  - Grafické karty se připojují výhradně přes plnohodnotný dlouhý slot velikosti **PCIe x16**. Propustnost tohoto hlavního slotu komunikuje bez žádného zpoždění rovnou s procesorem, čímž se minimalizuje latence.
  - **Evoluce verzí a propustnost:** Sběrnice extrémně progresuje. PCIe 3.0 měla 1 GB/s na linku, PCIe 4.0 měla 2 GB/s, a současná nejmodernější specifikace **PCIe 5.0** má propustnost 4 GB/s na jedinou linku (grafický slot x16 propustí závratných 64 GB/s za sekundu v jednom směru). Umožnilo to vznik obrovských rychlostí **NVMe (Non-Volatile Memory Express)** pevných disků nové generace. Nyní se ve výrobním sektoru specifikuje **PCIe 6.0**, které přidává pokročilou chybovou modulaci signálu **PAM-4** (namísto klasického binárního NRZ).
  - Starší překonané sloty byly **ISA** (historie v 80. letech), **PCI** a pro grafiky dřívě fungovalo vyhrazené **AGP (Accelerated Graphics Port)**, které však výkonnostně zahynulo počátkem tisíciletí.

## Zvukový a Síťový kodek (Integrované obvody)

Základní deska dnes nefiguruje jako prázdná plocha. Běžný uživatel si drtivou většinu dedikovaných rozšiřujících karet pro každodenní práci ušetří, neboť jsou tyto funkce zmenšené v integrovaných čipech na samotné desce.

- **Zvukový čip (ALC Realtek nebo SupremeFX):** Kvalitní základní desky dnes dokážou generovat obdivuhodný audio výstup (často Hi-Res 7.1 až 32-bit), a plošný spoj je fyzicky navržen tak, aby audio čip byl galvanicky separován (často podsvícenou rýhou v desce) od obrovského rušení CPU kaskády (prevence vrčení zvuku a statického praskání do sluchátek přes audio jack zadek).
- **Síťové prvky (NIC):** Zvládá komunikaci. Minimální standard jsou dnes 2,5 GbE Ethernet konektory (dříve běžný Gigabit 1,0 GbE). Bohatší desky nesou rozsáhlý hliníkové krabičky antén (M.2 Key E slot) pro revoluční bezdrátové standardy **Wi-Fi 6E** či nejnovější **Wi-Fi 7** s 6 GHz pásmem. Dále nechybí bezdrátový Bluetooth.

## Formáty základních desek (Form Factors)

Umožňují kompatibilitu s různě velkými skříněmi a diktují, co se do nich vejde:

- **E-ATX (Extended ATX):** rozsáhlý 305 × 330 mm drahé extrémní stroje s více grafickými sloty, ohromnou kaskádou a často osmi RAM sloty (HEDT).
- **ATX:** Běžný mainstreamový trh počítačů, (305 × 244 mm), dostatek 7 rozšiřujících slotů.
- **Micro-ATX (mATX):** Velmi populární volba. Kratší verze ATX s uříznutým spodkem (3 rozšiřující sloty na grafiku a Wi-Fi kartu bohatě stačí) šetřící rozpočet i místo (244 × 244 mm).
- **Mini-ITX:** Čtvercové malinkaté (170 × 170 mm) inženýrské mistrovské kousky určené pro velmi krásné, malé PC o objemu jako konzole. Má vždy jen jeden velký PCIe slot pro GPU a kvůli miniaturnímu rozložení komponent občas komplikuje odvod tepla od rozsáhlý kapacit M.2 SSD disků.

## Konektivita I/O panelu (Zadní konektory)

Panel konektorů (Input/Output), kterým deska vykukuje vzadu ze skříně:

- **USB (Universal Serial Bus):** Různé druhy pro přenos dat i proudu. Nejnovější USB4 propustí monstrózních 40 Gbps, oblíbené USB-C 3.2 Gen 2x2 zvládá 20 Gbps (často pro rychlé připojení externích NVMe disků) a pro klávesnici stačí staré verze USB 2.0 typ A.
- **Konektory pro obličej:** Audio Jacky 3,5 mm pro repro / mikrofon, optický port (TOSLINK) (S/PDIF) do soundbaru s Hi-Fi receivers.
- **Zobrazovací porty:** Tzv. DisplayPort (DP) nebo standardní HDMI pro připojení monitoru na malou integrovanou grafickou jednotku, skrytou přímo pod plíškem v procesoru. Kdo koupí procesor "F", ten v těchto portech obraz do monitoru mít nebude.
- **Bleskurychlý Thunderbolt 4 / 5:** Ochranná známka Intelu pro konektor USB-C s rozsáhlý šířkou pásma, umožňující připojit grafické karty externě nebo sériově navázat deset 4K monitorů za sebou z jednoho kabelu (Daisy Chaining).

---

## Diagnostika, řešení Závad a Chyb (Troubleshooting PC)

Počítače nejsou neomylné a selhání hardwaru může paralyzovat jakoukoli moderní organizaci či firmu. K úspěšné diagnóze selhání (troubleshootingu) na servisním stole musíme přesně určit místo poruchy.
Při startu prochází základní deska speciální sekvencí **POST (Power-On Self Test)**, kde během první sekundy od zapnutí hardwarově "proměří" a inicializuje všechna instalovaná zařízení (probudí CPU, adresuje RAM bloky a zjistí GPU inicializaci).

### POST diagnostika na základní desce

- **Beep Codes (Zvukové signály):** Nejtradičnější old-schoolová metoda. Počítačová skříň obsahuje na pinu malý PC Speaker ("pípák"). Ten vydává tzv. morseovku (jedno krátké pípnutí = vše naprosto OK. Dlouhé nebo opakující se pípání = hrubá hardwarová závada). Kombinaci určuje manuál výrobce, obecně např. 3 dlouhá pípnutí typicky znamenají špatný dotyk RAM u platformy AMI.
- **Debug LED kontrolky:** Novější, nejpohodlnější a nejběžnější systém na moderních deskách. Malé čtyři žárovky vedle ATX konektoru nesou štítky "CPU, DRAM, VGA, BOOT". Pokud PC přestane startovat u vadné RAM paměti, rozsvítí se a trvale zasekne červené LED u DRAM a PC neukáže obraz. Ihned víte, co je vadné.
- **POST Display (7-segmentový ciferník):** Prémiové nejdražší desky nesou malý dvoumístný digitální displej (např. chyba kód "0d" nebo "A2"). Poskytuje obrovské rozlišovací detaily při rozsáhlý a nestabilním paměťovém overclockingu do zničeného BIOSu.

### Typické Závady komponent a jejich indikace

- **Tepelné vypínání (Thermal Shutdown):** Počítač jede, spustíte hru a za minutu se okamžitě a zcela utne ze sekundy na sekundu "natvrdo". Často zaschlá teplovodivá pasta, neaktivní zastavená pumpa ve vodním AIO chlazení nebo zanesený žebrovaný chladič způsobí přehřátí, po kterém moderní CPU natvrdo aktivuje nouzové utnutí (OTP).
- **BSOD (Blue Screen of Death) - Modrá smrt ve Windows:** Často softwarová záležitost poškozeného ovladače. Může jít ale i o hardware. Náhodné občasné BSOD při zapnutí Chrome prohlížeče je často selhávající blok operační RAM, která propouští chybová data do čipsetu desky nebo unavěné časování RAM obvodů.
- **Poruchy Grafického výstupu (VGA):** Zapnete PC, bedna svítí, ventilátory běží, ale monitor svítí černě "No Signal". Pokud pípák signalizuje vadu grafiky a konektory i napájení PCIe kabelů sedí, grafické jádro ztratilo datové spojení k RAM nebo se přepálilo vlivem přetížení BGA letovacích kuliček.
- **Artefakty ve 3D:** Při puštění hry se obraz trhá a přes obrazovku létají "barevné polygony", černé obdélníčky s šachovnicemi nebo "sníh a duha". Toto je s železnou přesností indikace tzv. usmažených obvodů VRAM pamětí na těžce zkoušené grafické kartě (vlivem kryptotěžby např.).

### Fyzické nástroje na testy

- **Digitální Multimetr:** Základní měřicí nástroj diagnostiky napětí a obvodů. K prověření nefunkčního PC můžeme přímo změřit výstup napětí napájecího zdroje (PSU). Odpojíme ho, vložíme kancelářskou sponku do 24-pinu (spojení zeleného "PS_ON" a černého "COM" pinu pro probuzení). Tím roztočíme zdroj bez zapojeného PC a na měřících hrotech ověříme žluté dráty (zda mají stabilně kolem 12,05V). U levných nebo starých vadných zdrojů napětí často plave pod 11,2V a tím hroutí stabilitu rozsáhlý procesoru ve zbrani. Dále lze měřit kapacitu, odpor či průchodnost diod a přerušených kabelů v myši. Lze také odhalit zničený ochranný "šváb" (transil) na prasklém disku způsobující zkrat.
- **Tester kabelů k sítím LAN:** K analýze špatně zalisovaného RJ-45 síťového kabelu způsobující kolaps či vysoký packet loss při zkoušce na stěně od UTP spojky.
