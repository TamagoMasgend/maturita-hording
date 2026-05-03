# 6. Grafický subsystém a zobrazovací zařízení

**Grafický subsystém** je komplexní řetězec hardwarových a softwarových prvků odpovědných za zpracování, výpočet a vizuální prezentaci dat. Jeho jádrem je grafická karta (GPU), která transformuje digitální instrukce z procesoru na obrazový signál. Moderní grafické subsystémy se vyvinuly z prostých zobrazovačů textu na vysoce paralelní výpočetní jednotky využívané nejen v gamingu, ale i v umělé inteligenci, vědeckých simulacích a kryptografii.

## Architektura grafické karty (GPU)

Grafická karta (Video Adapter) je dedikovaná rozšiřující karta, jejímž nejdůležitějším prvkem je grafický procesor (**GPU**). Na rozdíl od CPU, které je navrženo pro sériové zpracování komplexních úloh, GPU disponuje tisíci malými jádry optimalizovanými pro masivně paralelní operace.

### Vnitřní komponenty a výpočetní jednotky
GPU není monolitický blok, ale skládá se z mnoha specializovaných subsystémů, které spolupracují na generování finálního obrazu.
- **Unifikované shadery (CUDA jádra / Stream procesory):** Základní výpočetní jednotky, které nahradily dřívější fixní pixel a vertex shadery. Jsou plně programovatelné a dokážou zpracovávat geometrii i barvu pixelů. NVIDIA je nazývá CUDA cores, AMD používá termín Stream Processors. U moderních architektur jsou tyto jednotky schopny provádět i výpočty pro umělou inteligenci či fyziku.
- **RT Cores (Ray Tracing Cores):** Specializované hardwarové jednotky pro akceleraci výpočtů sledování paprsků. Provádějí matematické operace pro detekci průsečíků paprsku s geometrií scény (BVH traversal), což umožňuje realistické odrazy, stíny a globální osvětlení v reálném čase bez drastického dopadu na výkon hlavních shaderů.
- **Tensor Cores:** Jednotky určené pro hluboké učení a maticové operace. Využívají se pro AI upscaling (NVIDIA DLSS), redukci šumu v obraze a zrychlení výpočtů v neurálních sítích. Dokážou efektivně pracovat s datovými formáty FP16 a INT8.
- **TMU (Texture Mapping Unit):** Jednotky zodpovědné za nanášení (mapování) 2D textur na 3D objekty, včetně jejich filtrování (anizotropní filtrování) a perspektivní korekce.
- **ROP (Render Output Unit):** Finální fáze pipeline, kde dochází k zápisu pixelů do frame-bufferu, provádění hardwarového antialiasingu (MSAA) a míchání barev (alpha blending). Počet ROP jednotek přímo ovlivňuje schopnost karty pracovat ve vysokých rozlišeních.
- **Rasterizace:** Proces převodu 3D modelů definovaných vektory a polygony na mřížku 2D pixelů zobrazenou na monitoru. Jedná se o standardní metodu vykreslování v reálném čase.

### Rozdělení podle integrace a napájení
1. **Integrovaná (iGPU / IGP):** Grafické jádro je součástí procesoru (APU u AMD) nebo čipové sady základní desky. Sdílí systémovou operační paměť RAM, což je hlavní omezující faktor výkonu. Moderní integrovaná řešení jsou však již dostatečná pro kancelářskou práci, video v 4K a nenáročné hry.
2. **Dedikovaná (dGPU):** Samostatná karta osazená do slotu PCI-Express (aktuálně standard PCIe 4.0/5.0 x16). Disponuje vlastní vysokorychlostní pamětí, napájecí kaskádou (VRM) a masivním chladičem. Vyžaduje přídavné napájení ze zdroje pomocí 6-pin, 8-pin nebo nového 16-pin (12VHPWR) konektoru.
3. **Externí (eGPU):** Grafická karta umístěná ve vnějším boxu s vlastním napájecím zdrojem. K počítači (typicky notebooku) se připojuje přes rozhraní s vysokou propustností jako Thunderbolt 3/4 nebo OCuLink, což umožňuje využít výkon stolní grafiky na přenosném zařízení.

## Grafická paměť (VRAM) a propustnost

VRAM slouží jako ultra-rychlá vyrovnávací paměť pro textury, frame-buffery, geometrická data a instrukce shaderů. Na rozdíl od systémové RAM je optimalizována pro obrovskou datovou propustnost na úkor latence.

### Technologie pamětí a architektura
- **GDDR7:** Nejmodernější standard grafických pamětí (JEDEC 2024), který nasazuje revoluční modulaci PAM3 (přenos 3 úrovní napětí, 1.5 bitu na takt). Přináší obrovský skok v energetické efektivitě a propustnost startující na 32 Gbps (s potenciálem až 48 Gbps).
- **GDDR6 / GDDR6X:** Předchozí velmi rozšířený standard. Verze "X" (od Micronu) využívá modulaci PAM4 (2 bity na takt) pro zvýšení propustnosti až k 21+ Gbps.
- **HBM (High Bandwidth Memory):** Pokročilá technologie, kde jsou paměťové čipy vrstveny vertikálně (3D stacking) a umístěny na společný substrát přímo vedle GPU jádra (interposer). To umožňuje extrémně širokou sběrnici (např. 4096-bit) a propustnost přesahující 1-2 TB/s při nižší spotřebě než GDDR.
- **Sběrnice (Bus Width):** Datová dálnice mezi GPU a VRAM, udává se v bitech (64-bit u low-endu až 384-bit u high-endu). Širší sběrnice umožňuje přenést více dat v každém taktu.
- **Frame Buffer:** Specifická část VRAM vyhrazená pro uložení dat aktuálně zobrazovaného snímku a snímků připravených v řadě (Double/Triple Buffering), což zabraňuje trhání obrazu.
- **Z-Buffer:** Paměťová oblast uchovávající hloubkové souřadnice bodů ve scéně. Používá se k určení, které objekty jsou v popředí a které jsou skryté za jinými objekty, aby se nevykreslovaly zbytečně.


## Grafická API a technologie

API (Application Programming Interface) tvoří most mezi softwarem (hrou) a hardwarem. Moderní API se zaměřují na nízkoúrovňový přístup pro minimalizaci režie procesoru.

### Moderní standardy
- **DirectX 12 (Ultimate):** Standard od Microsoftu pro Windows a Xbox. Přináší nízkoúrovňový přístup k HW, lepší využití více jader CPU a technologie jako Variable Rate Shading (VRS) nebo Mesh Shaders.
- **Vulkan:** Multiplatformní, nízkoúrovňové API (nástupce OpenGL), které vyniká vysokou efektivitou a minimální režií procesoru. Široce využívané na Linuxu a Androidu.
- **OpenGL:** Starší multiplatformní standard specifikující rozhraní pro tvorbu 2D a 3D grafiky, dnes používaný spíše v profesionálních aplikacích.

### Technologie pro zvýšení výkonu
- **DLSS / FSR / XeSS:** Technologie rekonstrukce obrazu (upscaling). Hra se renderuje v nižším rozlišení a AI (NVIDIA) nebo pokročilé algoritmy (AMD/Intel) ji dopočítají do vyššího rozlišení.
- **Ray Tracing:** Fyzikálně přesná simulace světla (odrazy, lomy, stíny) nahrazující starší triky a statické nasvícení scény.

## Zobrazovací zařízení a monitory

Monitor je finálním článkem subsystému, který převádí digitální signál zpět na vizuální formu pro lidské oko. Kvalita zobrazení závisí na použité technologii panelu, typu podsvícení a elektronice monitoru.

### Technologie LCD panelů (Liquid Crystal Display)
LCD panely využívají vlastnosti tekutých krystalů, které pod vlivem elektrického napětí mění svoji orientaci a tím regulují množství procházejícího světla z podsvícení. Každý obrazový bod je ohraničen dvěma polarizačními filtry.
- **TN (Twisted Nematic):** Tradiční a nejlevnější technologie. Krystaly se v základním stavu natáčejí do šroubovice. Nabízí extrémně rychlou odezvu (vhodné pro e-sport), ale trpí na velmi špatné pozorovací úhly (barvy se mění při pohledu z boku) a nízký kontrast.
- **IPS (In-Plane Switching):** Krystaly rotují v jedné rovině rovnoběžně s panelem. Nabízí nejlepší podání barev a široké pozorovací úhly (až 178°). Nevýhodou je tzv. "IPS glow" (prosvítání podsvícení v rozích) a slabší statický kontrast (typicky 1000:1).
- **VA (Vertical Alignment):** Krystaly jsou v základním stavu orientovány vertikálně. Nabízí vynikající statický kontrast (3000:1 až 5000:1) a hlubokou černou barvu. Často však trpí na pomalou odezvu v tmavých přechodech, což způsobuje "mazání" obrazu (dark smearing).

### Podsvícení LCD panelů
Protože tekuté krystaly samy nesvítí, potřebují zdroj světla umístěný za panelem.
- **Edge LED:** Diody jsou umístěny pouze na okrajích monitoru a světlo je rozváděno pomocí difuzoru. Umožňuje velmi tenké displeje, ale podsvícení bývá často nerovnoměrné.
- **Direct LED:** Diody jsou rozmístěny v mřížce přímo za panelem. Poskytuje rovnoměrnější podsvícení.
- **Mini-LED:** Moderní evoluce Direct LED s tisíci malých diod rozdělených do stovek či tisíců zón lokálního stmívání (Local Dimming). Umožňuje dosáhnout vysokého jasu a kontrastu blízkého OLED panelům, ideální pro HDR obsah.

### Technologie OLED (Organic Light Emitting Diode)
Každý pixel u OLED panelu je tvořen organickou diodou, která při průchodu proudu sama vyzařuje světlo. Nepotřebuje tedy žádné podsvícení.
- **Absolutní černá:** Pokud má být pixel černý, jednoduše se vypne. Tím vzniká nekonečný kontrastní poměr a dokonalá černá barva.
- **AMOLED (Active Matrix OLED):** Verze s řídicí vrstvou tranzistorů, která umožňuje bleskovou odezvu (<0,03 ms) a extrémně syté barvy.
- **Vypalování (Burn-in):** Hlavní nevýhoda organických panelů. Při dlouhodobém zobrazení statických prvků (lišty ve Windows, loga televizí) může dojít k trvalé degradaci konkrétních pixelů.

### Dataprojektory a projekční technika
Zařízení pro velkoplošné zobrazení obrazu na plátno či zeď.
- **DLP (Digital Light Processing):** Srdcem je DMD čip s miliony mikroskopických zrcátek, která se bleskově naklánějí. Světlo prochází rotujícím barevným kotoučem (RGB), což u některých lidí způsobuje tzv. "duhový efekt".
- **3LCD:** Využívá tři samostatné LCD panely pro každou barevnou složku. Světlo z lampy je rozděleno dichroickými zrcadly, projde panely a spojí se v hranolu před objektivem. Netrpí duhovým efektem a má jasnější barvy než levné DLP.
- **LCoS (Liquid Crystal on Silicon):** Reflexní technologie kombinující přesnost LCD a rychlost DLP. Nabízí nejlepší obrazovou kvalitu, filmový vzhled a hlubokou černou, ale je technicky velmi náročná a drahá.

## Výroba, chlazení a napájení grafických karet

Grafické karty jsou dnes nejvíce energeticky náročnými komponentami v počítači. Výroba i následný provoz vyžadují pokročilé inženýrství pro zajištění stability a dlouhé životnosti.

### Napájecí kaskáda a PCB
- **VRM (Voltage Regulator Module):** Soustava fází, cívek (chokes) a kondenzátorů, která převádí napětí ze zdroje (12V) na velmi nízké napětí (kolem 1V) pro GPU a paměti. Kvalita VRM určuje stabilitu při přetaktování a provozní teploty napájecí části.
- **PCB (Printed Circuit Board):** Vícevrstvá deska plošných spojů (často 10 a více vrstev), která musí být navržena tak, aby minimalizovala elektromagnetické rušení (EMI) mezi extrémně rychlými signály GPU a VRAM.
- **Napájecí konektory:** Od tradičních 6-pin a 8-pin konektorů se přešlo k standardu 12VHPWR (16-pin), který dokáže jedním kabelem dodat až 600W výkonu.

### Metody chlazení
- **Pasivní chlazení:** Využívá pouze masivní hliníkový nebo měděný blok s žebrováním. Je naprosto tiché, ale vhodné pouze pro karty s velmi nízkým tepelným vyzařováním (low-end).
- **Aktivní chlazení (Vzduchové):** Nejběžnější typ využívající jeden až tři ventilátory, které ženou vzduch přes žebra pasivního chladiče. Moderní karty využívají tzv. "Zero RPM" režim, kdy se ventilátory zastaví, pokud teplota klesne pod určitou mez.
- **Heatpipes:** Měděné trubičky naplněné kapalinou s nízkým bodem varu, které extrémně rychle přenášejí teplo z jádra GPU do vzdálenějších částí pasivního bloku.
- **Vapor Chamber (Odpařovací komora):** Pokročilejší verze heatpipe, kde celá základna chladiče funguje jako plochá dutina pro efektivnější rozvod tepla u extrémně výkonných karet.
- **Vodní chlazení (AIO / Custom Loop):** Využívá čerpadlo a kapalinu k přenosu tepla do radiátoru. Nabízí nejnižší teploty a tichý provoz i u karet s TDP přes 400W.

### Trh a výrobci
- **Výrobci čipů (GPU Vendors):** Pouze tři firmy na světě navrhují moderní herní grafické čipy – NVIDIA (řada GeForce), AMD (řada Radeon) a Intel (řada Arc).
- **AIB partneři (Board Partners):** Firmy jako ASUS, MSI, Gigabyte nebo Sapphire, které od výrobců kupují samotné čipy a osazují je na vlastní desky s vlastním chlazením a designem.
- **Profesionální karty:** Kromě herních karet existují i speciální řady jako NVIDIA RTX (dříve Quadro) nebo AMD Radeon Pro, které jsou certifikovány pro práci v CAD programech a disponují ECC pamětí pro eliminaci výpočetních chyb.


### Moderní digitální rozhraní
- **HDMI 2.1 (High-Definition Multimedia Interface):** Univerzální standard pro přenos videa i audia. Podporuje propustnost 48 Gbps, rozlišení 4K při 120 Hz, 8K při 60 Hz, variabilní obnovovací frekvenci (VRR) a eARC (zpětný audiokanál ve vysoké kvalitě).
- **DisplayPort 2.1:** Dominantní standard v PC světě. Nabízí propustnost až 80 Gbps, což umožňuje přenos obrazu v 16K (s kompresí DSC) nebo více monitorů přes jeden kabel (Daisy Chaining).
- **USB-C (Alt Mode):** Umožňuje přenos DisplayPort signálu skrze univerzální USB-C konektor, což je standard u moderních notebooků a monitorů, často doprovázený napájením (Power Delivery).

### Historická a analogová rozhraní
- **VGA (D-Sub):** Patnáctipinový konektor pro analogový přenos obrazu. Signál je náchylný na elektromagnetické rušení a při vyšších rozlišeních dochází k viditelné degradaci ostrosti.
- **DVI (Digital Visual Interface):** Existuje ve více variantách. DVI-D přenáší pouze digitální signál, DVI-A pouze analogový a DVI-I kombinuje oba typy. Dual-Link varianta nabízí vyšší propustnost pro monitory s vysokým rozlišením.

### Klíčové parametry a terminologie
- **Rozlišení:** Celkový počet pixelů (např. QHD 2560x1440). Vyšší hustota pixelů (PPI - Pixels Per Inch) znamená ostřejší obraz.
- **Obnovovací frekvence (Refresh Rate):** Počet snímků, které monitor zobrazí za sekundu (Hz). Hráči preferují 144 Hz a více pro plynulý pohyb a nižší vstupní zpoždění (Input Lag).
- **Adaptive Sync (G-Sync / FreeSync):** Technologie, které dynamicky mění frekvenci monitoru podle aktuálního výkonu grafické karty, čímž eliminují trhání obrazu (tearing) a zasekávání (stuttering).
- **HDR (High Dynamic Range):** Umožňuje zobrazení širšího rozsahu jasu a barev. Vyžaduje panel s vysokým maximálním jasem (minimálně 400 nitů, ideálně 1000+) a hlubokou černou.
- **Barevný gamut:** Rozsah barev, které zařízení dokáže zobrazit. Mezi standardy patří sRGB (běžná práce), AdobeRGB (tisk) a DCI-P3 (digitální kino/filmy).

