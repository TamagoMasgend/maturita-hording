# 3. Procesory (CPU – Central Processing Unit)

**CPU (Central Processing Unit - Centrální procesorová jednotka)** je primární, vysoce komplexní integrovaný obvod (čip) výpočetního systému. Jedná se o sekvenční logický obvod tvořený miliardami mikroskopických tranzistorů, jehož hlavním úkolem je načítání, dekódování a exekuce strojových instrukcí. Skrze vysokorychlostní sběrnice řídí a synchronizuje veškeré datové toky mezi operační pamětí, grafickým subsystémem a datovými úložišti.

## Výrobní proces procesoru

Výroba procesorů představuje jedno z technologicky nejnáročnějších odvětví lidstva. Proces se skládá z několika fází:

1. **Příprava křemíku a výroba waferu:** Základní surovinou je křemík, který se získává z písku. Písek se čistí a taví na monokrystalický křemík s extrémní čistotou 99,9999 %. Z taveniny se vytahuje ingot (rozsáhlý krystal) o průměru okolo 300 mm. Ten se následně řeže na tenké plátky, zvané **wafery**, silné méně než 1 mm. Wafery slouží jako základní podkladová vrstva.
2. **Fotolitografie:** Na wafer se nanese fotocitlivá chemická vrstva (fotorezist). Pomocí nesmírně drahého a přesného litografického stroje (např. od firmy ASML) se přes masku s návrhem procesoru promítá na wafer extrémní ultrafialové záření (**EUV**). Osvícená místa změní svoji strukturu a následně jsou odleptána. Vzniká tak přesný vzor budoucích tranzistorů o velikosti pouhých několika nanometrů.
3. **Iontová implantace (Doping):** Pro vytvoření přesných polovodičových vlastností (schopnosti usměrňovat a řídit proud) bombardujeme vytvořené kanálky vysokým napětím ionty příměsí, nejčastěji bóru, fosforu nebo arsenu. Tím se lokálně mění vodivost (vzniká oblast N a P typu) a získáváme funkční spínač – tranzistor.
4. **Metalizace:** Tranzistory musíme elektricky propojit pomocí měděných spojů. Ty propojují jednotlivé tranzistory nejprve do logických hradel a poté do funkčních celků (jako je jádro, ALU či cache paměť). Dnešní procesory mohou mít přes 10 až 15 mikroskopických vrstev těchto spojů (tzv. interconnects). Tyto vrstvy musí být izolovány pomocí dielektrika.
5. **Testování na waferu a řezání (Dicing):** Speciální automatizované stroje s jehlovými sondami otestují funkčnost každého mikročipu na desce ještě před jeho vyříznutím. Následně se wafer rozřeže přesnou diamantovou pilou na jednotlivé kousky křemíku (tzv. čipy/dies). Nefunkční nebo poškozené kusy se v této fázi vyřazují.
6. **Konečná sestava (Packaging):** Samotné křemíkové jádro (Die) je extrémně křehké, proto je umístěno na malou organickou destičku (substrát) se spoji, která vyvádí titěrné kontakty čipu na standardní velikost pinů nebo plošek do patice desky. Na křemíkové jádro se následně nanese teplovodivý materiál (často pasta, tekutý kov či pájka indium/galium) a přiklopí se rozsáhlý kovový rozvaděč tepla, tzv. **IHS (Integrated Heat Spreader)**, který jádro chrání před rozdrcením chladičem.
7. **Finální třídění (Binning):** Hotové procesory procházejí zátěžovými testy. Všechny čipy se vyrábějí na jedné lince, ale kvalita křemíku se různí. Čipy, které zvládnou nejvyšší frekvence při nízkém napětí a mají všechna jádra perfektní, se prodají jako nejvyšší a nejdražší modely (např. Intel Core i9 nebo Ryzen 9). Pokud se na čipu během testu ukáže vada některého jádra (nebo špatná stabilita na taktu), výrobce vadná jádra laserově "uzamkne", sníží frekvenci a procesor prodá levněji (např. jako Ryzen 5 nebo Core i5).

## Dělení procesorů podle instrukční sady (Architektura)

1. **CISC (Complex Instruction Set Computer):**
   - Architektura s komplexní sadou instrukcí, kde jedna instrukce může vykonat několik operací najednou (např. načtení dat z paměti, výpočet i opětovné uložení najednou).
   - Využívá klasickou PC platformu **x86/x64**. Důraz je kladen na hardware, který je na návrh nesmírně složitý, aby programování samotné aplikace bylo pro software jednodušší.
   - Současné CISC procesory uvnitř převádějí tyto komplexní instrukce na jednodušší "mikro-instrukce" (micro-ops), takže se hluboko v jádře chovají architektonicky jako moderní RISC, ale navenek zachovávají dokonalou zpětnou kompatibilitu se starými (legacy) programy.
2. **RISC (Reduced Instruction Set Computer):**
   - Architektura s redukovanou (ořezanou) sadou instrukcí. Každá instrukce je velmi jednoduchá a vykonává se ideálně v jednom hodinovém taktu.
   - Typickým představitelem je platforma **ARM** (Apple Silicon, Qualcomm Snapdragon, čipy v mobilních telefonech). Důraz je zde na ultimátní efektivitu a nízkou spotřebu. Hardware je zjednodušen, ale složitost úloh se přenáší na překladače softwaru.
   - Dříve byl RISC považován jen za úsporné řešení pro mobily, ale příchod **Apple Siliconu (řady M)** to zcela změnil. Jejich výkon byl najednou na stejné úrovni, ba dokonce často vyšší než většina CISC procesorů (Intel), a energetická efektivita byla bezkonkurenční. Dnes se rozdíly zmenšují – x86 (CISC) procesory přebírají efektivitu pomocí P a E jader, a RISC zas přidává specializované hardwarové bloky (akcelerátory).

## Součásti procesoru a jeho vnitřní architektura

- **Řadič (Control Unit):** Hlavní řídící jednotka procesoru. Načítá instrukce z paměti (Fetch), dekóduje je a řídí tok dat mezi ostatními částmi (aktivuje konkrétní obvody). Obsahuje i kritickou jednotku pro předvídání skoků (**Branch Prediction**). Ta se snaží uhodnout, jaký krok (větev if/else podmínky kódu) bude následovat, a začne zpracovávat data dopředu, aby se zabránilo zdržení běhu programu (tzv. pipeline stalls).
- **Sada registrů (Registers):** Titěrná, ale extrémně rychlá paměť umístěná přímo v obvodech ALU v jádře procesoru. Slouží k okamžitému uložení právě aktuálních operandů (čísla, se kterými se zrovna počítá) a mezivýsledků operací. Přístup k nim je doslova bez zpoždění oproti paměti RAM.
- **Aritmeticko-logická jednotka (ALU):** Vysoce výkonná jednotka provádějící elementární matematické (sčítání, odčítání) a logické operace (AND, OR, XOR) s celými čísly. V moderním jádře je nasazeno hned několik ALU bloků, aby čip zvládl zpracovávat rozsáhlý více instrukcí paralelně najednou.
- **FPU (Floating Point Unit):** Koprocesorová část specializovaná pro výpočty čísel s plovoucí desetinnou čárkou (zlomky a obrovská reálná čísla). Dříve byla samostatným čipem do desky, dnes je plně integrovaná a tvoří obrovskou část jádra. FPU výkon je naprosto klíčový pro 3D grafiku, simulace fyziky ve hrách a vědecké výpočty.
- **Jádra (Cores) a moderní asymetrický design:**
  - Aktuálně procesory na jediném čipu nesou hned několik kompletních jader, z nichž každé může řešit samostatné úkoly. U x86 procesorů (hlavně Intel) se prosazuje koncept **big.LITTLE**:
  - **P-Cores (Výkonná, Performance):** Velká, nekompromisně výkonná jádra s vysokou frekvencí a vysokou spotřebou proudu. Skvělá pro hry a rychlé dokončení náročné úlohy (např. video render).
  - **E-Cores (Úsporná, Efficient):** Fyzicky velmi malá, úsporná jádra s nižším taktem. Na stejnou plochu, kam by se vešlo jedno P-Core, se vejdou třeba čtyři E-Cores. Slouží pro lehký běh systému, prohlížeče na pozadí, stahování dat, a výrazně tak zlepšují spotřebu.
- **NPU (Neural Processing Unit):** Úplně nová hardwarová součást moderních procesorů s příchodem tzv. "AI PC". Jedná se o dedikovaný akcelerátor specializovaný na maticové výpočty (tenzory), které tvoří základ umělé inteligence (AI) a strojového učení (Machine Learning). NPU umožňuje běh AI jazykových modelů a chytrých filtrů (rozostření pozadí ve webkameře) zcela lokálně bez rozsáhlý zatěžování hlavních jader (CPU) nebo dedikované grafiky (GPU) s rozsáhlý spotřebou.
- **Integrované GPU (iGPU):** Dedikované grafické jádro integrované přímo na stejném kusu křemíku jako CPU. Dříve mělo výkon stěží dostačující na kancelářskou práci a obraz. Dnes sdílí moderní DDR5 systémovou paměť a u moderních "APU" od AMD disponuje iGPU takovým výkonem, že bez problémů plynule rozjede esportové a středně náročné hry i bez dokoupení drahé velké grafické karty do patice desky.

## Hierarchie vyrovnávací paměti (Cache)

Rychlost CPU a operační paměti RAM (typu DRAM) se drasticky liší – přístup do systémové RAM je z pohledu rychlého jádra extrémně pomalý (tzv. paměťové hrdlo). Proto CPU disponuje vlastní integrovanou vyrovnávací pamětí typu **SRAM (Static RAM)**, která je tvořena přímo spínacími tranzistory (nepotřebuje obnovování buněk kondenzátorů) a je bleskurychlá. Do ní se přes algoritmy hardwarové predikce dopředu stahují často užívaná data. Hierarchie se dělí do 3 vrstev:

- **L1 Cache (Level 1):** Fyzicky nejmenší paměť striktně rozdělená na instrukční a datovou část, umístěná přímo u ALU jednotek jádra (cca 32 až 64 KB). Přístup trvá řádově méně než 1 nanosekundu.
- **L2 Cache (Level 2):** Větší (často stovky KB až pár MB pro každé jádro) a nepatrně pomalejší. Může fungovat pro jedno jádro nebo sdíleně.
- **L3 Cache (Level 3):** Největší a nejpomalejší (přístup cca desítky nanosekund). Jedná se o rozsáhlý paměťovou dálnici na čipu s velikostí klidně deset až desítky megabytů, která je vždy **sdílená pro všechna jádra procesoru**. Zde dominuje AMD se svou technologií **3D V-Cache**, kdy pomocí metody vertikálního pájení nakladou rozsáhlý paměťový čip z vrchu přímo nad CPU jádra. Tím čip nabude kapacity L3 na cca 100 MB, což dodá výkonu ve specifických úlohách výraznou výhodu, neboť textury hry se drží rovnou na superrychlém čipu.

## Patice (Socket) na základní desce

Způsob, jakým se procesor fyzicky a elektricky propojuje s deskou, se dělí do 3 typů:

- **PGA (Pin Grid Array):** Procesor má na spodní straně zlaté výstupky (piny), které se zasouvají do drobných dírek na desce. Historicky populární u starších AMD (např. platforma AM4). Nevýhodou bylo snadné zničení a ohnutí pinů při neopatrném zvednutí chladiče.
- **LGA (Land Grid Array):** Na spodku procesoru jsou jen zlaté ploché dotykové podložky (kontakty). Všechny velmi jemné a křehké piny se nacházejí ve slotu na samotné základní desce. Dnes je to průmyslový standard pro moderní Intel (LGA 1700, 1851) i moderní AMD procesory (socket AM5).
- **BGA (Ball Grid Array):** Procesor nemá ani piny ani volné plošky. Nasadí se z výroby na malinkaté kuličky pájky a je trvale a nevratně přiletován k desce. Nelze ho doma uživatelsky vyměnit. Setkáme se s ním v noteboocích, tabletech a konzolích.

## Základní hodnoty a parametry

1. **Taktovací frekvence (Clock Speed):** Udává počet taktovacích cyklů oscilátoru za sekundu – v GHz (gigahertzích).
   - **Base Clock (Základní takt):** Garantovaná a bezpečná minimální rychlost při běžné stabilní dlouhodobé zátěži bez ohledu na chlazení.
   - **Boost / Turbo Clock:** Okamžitá špičková rychlost (dnes i přes 6,0 GHz). Procesor ji pomocí chytrých algoritmů zapne pouze automaticky na chvíli při náročné úloze, a to pokud mu to dovolí teplota (Thermal limit) a proud. Pokud se zahřeje, tak opět takt bezpečně automaticky zkrátí (tzv. Thermal Throttling).
2. **TDP (Thermal Design Power):** Udává se ve wattech (W). Představuje hrubý odhad tepelného odpadního tepla, které procesor v základu vyprodukuje a chladič ho musí být schopen bez potíží uchladit. Současné velmi výkonné čipy od Intelu (jako i9) mají sice napsané TDP třeba 125 W, avšak při plném Boost taktu (tzv. Power Limit 2) udrží reálnou špičkovou spotřebu 250 až 300 W, což vyžaduje rozsáhlý vodní chlazení (AIO).
3. **Výrobní proces (Nm):** Značí rozlišovací schopnost litografie, dříve skutečnou geometrickou velikost hradla. Udává se v nanometrech (nm). Dnes procesory Intel, AMD i Apple dosáhly miniaturizace zhruba 3 nm vrstev u firmy TSMC, čímž vznikají neuvěřitelně husté čipy (vyšší výkon s menším rizikem úniků napětí).
4. **IPS / FLOPS:** Míry čistého teoretického výkonu - IPS (Instructions Per Second) u běžných instrukcí, či FLOPS u výpočtů s plovoucí desetinnou čárkou (zásadní měřítko u superpočítačů a grafických čipů pro AI modelování).
5. **IPC (Instructions Per Clock):** Klíčový strukturální ukazatel efektivity architektury procesoru. Udává, kolik přesně instrukcí dokáže jádro vyřešit během jednoho jediného hodinového taktu (Hz). Vyšší IPC znamená, že i procesor s nižší frekvencí (např. moderní 3,5 GHz) v hrubém výkonu drtivě porazí pět let starý procesor běžící na 5,0 GHz.
6. **Vlákna a Multithreading (SMT / Hyper-Threading):** Technologie hardwarového plánování a dělení času. Jedno fyzické jádro procesoru se softwarově tváří pro operační systém (Windows) jako dvě logická jádra (vlákna). Zatímco jedna část fyzického jádra čeká na zpožděná data z RAM paměti, druhá "virtuální" polovina už aktivně počítá jiný proces. Výrazně to zvyšuje propustnost paralelních výpočtů a renderingu.

## Přehled trhu: Výrobci procesorů a složitý systém jmen

### 1. INTEL (x86-64 Architektura)

Dlouholetý hegemon klasických počítačů. V roce 2023 se společnost rozhodla po mnoha letech "zahodit" známé značení procesorů malým písmenem „i“. Dnes se člení takto:

- **Intel Core:** Levná řada pro běžné nasazení u notebooků, s trochu starší strukturou.
- **Intel Core Ultra:** Zcela prémiová nová řada (jak mobilní tak nově desktopová). Uvnitř se skrývá čipletový poslepovaný design ("tiles" - samostatná část pro GPU, pro NPU, pro CPU) – extrémně dobrá výdrž baterie na laptopech.
- Výkonové stupně jako dřív: **Core Ultra 5** (základ na běžné nároky i hry), **Core Ultra 7** (high-end tvorba, profesionální hraní), **Core Ultra 9** (nejdražší top model na naprosté maximum výkonu).

**Význam písmen u Intelu:**

- **K:** Značí "Unlocked" – procesor má odemčený násobič a jde na speciálních deskách doma manuálně přetaktovat nad garantované limity za cenu ohromné spotřeby tepla (např. _Core Ultra 9 285K_).
- **F:** Verze procesoru, u které je výrobní vadou či fyzicky natvrdo odstřihnuta integrovaná grafika (iGPU). Jsou levnější, ale musíte k nim koupit samostatnou GPU do PCIe, jinak na monitoru neuvidíte žádný obraz. **KF** je pak mix (odemčený + bez grafiky).
- **T:** Úsporný čip pro miniaturní firemní "krabičky", s velmi malým TDP kolem 35 W.
- **HX:** Absolutně extrémní notebookové monstrum, de facto desktopový nezmenšený čip natvrdo "nacpaný" do herního notebooku.
- **U / V:** Ultra úsporné (Low-voltage) varianty procesorů. Skvělé pro pasivně chlazené tenké firemní ultrabooky (dlouhá výdrž baterie, ale nejnižší stálý výkon).

### 2. AMD (Advanced Micro Devices - platforma x64)

AMD představuje aktuálně hlavní motor inovací, velmi proslulé architekturou **Zen** (momentálně v generaci Zen 5 jako řada _Ryzen 9000_). AMD má číslování jasnější:

- **Ryzen 3:** Základ (často se již nově u desktopů vynechává), vhodný spíš na občasný YouTube a kancelář.
- **Ryzen 5:** Tzv. Sweet spot, ideální zlatá střední cesta pro drtivou většinu obyčejných herních PC i produktivity. (Oblíbený 6jádrový model).
- **Ryzen 7:** Pro náročné gamery a multitasking.
- **Ryzen 9:** Brutus mezi běžnými CPU. Může mít 16 jader. Určen pro editory 4K videí a 3D grafiku.
- **Threadripper:** Třída procesorů HEDT (High-End Desktop) / Pracovní stanice. Má obrovskou fyzickou patici (TRX4) a klidně přes neskutečných 96 jader. Absolutní špička pro filmová studia a renderování na serverech v řádech desítek tisíc dolarů za čip.

**Význam písmen u AMD:**

- **X:** Vysoký takt přímo z výroby a odemčený boost (tzv. verze Enthusiast).
- **X3D:** Zdaleka nejoblíbenější pro hráče. Verze s revoluční na křemíku nalepenou rozsáhlý pamětí 3D V-Cache pro likvidování výpadků snímkové frekvence ve hrách (aktuální král trhu je _Ryzen 7 9800X3D_).
- **G:** APU verze. Zaměřena na silnou integrovanou grafiku třídy Radeon přímo v procesoru pro ty, kdo nechtějí drahé grafické karty (tzv. "Graphics" přípona).
- **HX:** Nejsilnější mobilní herní notebookové verze s posunutým napětím TDP k hranici 55 W.

### 3. APPLE (ARM Architektura - Apple Silicon)

Apple v roce 2020 udělal revoluci, odešel od pomalých a žhavých Intel procesorů do MacBooků, a začal si navrhovat sám extrémně úsporné, na mobilních ARMech postavené procesory s rozsáhlý sběrnicí na paměť tzv. přímo na těle CPU (SoC – System on Chip) uvnitř pouzdra. K CPU a paměti tak nevede žádná pomalá dráha přes celou základní desku, všechno je v jednom jediném čtverci.

- Generace jdou číselně: **M1, M2, M3, M4**.
- Modely výkonu od spodu:
  - **M(x):** Klasický čip bez větráku do ultrabooků MacBook Air.
  - **M(x) Pro:** Dvojnásobek paměťových kanálů, širší rozestupy GPU a CPU jader (např. MacBook Pro 14").
  - **M(x) Max:** rozsáhlý GPU obvody, s podporou třeba 128 GB rychlé paměti přímo v "soC" destičce. rozsáhlý datová propustnost sloužící převážně střihačům celovečerních filmů a grafikům.
  - **M(x) Ultra:** Architektura v podobě fyzicky propojených dvou "M(x) Max" čipů skrze extrémní rychlou můstkovou sběrnici (UltraFusion) fungující jako procesor o mamutí ploše. Určeno pouze do stolních velkých pracovních stanic (Mac Studio).

### 4. QUALCOMM (ARM pro Windows)

Reakce ekosystému PC světa na famózní výdrž Maců. Qualcomm vzal technologii ARM z mobilů a v roce 2024 vydal výkonné PC čipy do notebooků "Copilot+ PC" pod systémem Windows 11 on ARM.

- Jedná se o úsporné čipy, které mají hardwarovou simulaci klasických x86 programů.
- Zahrnují série: **Snapdragon X Elite** (nejvýkonnější, bojuje o přežití vedle M3 a Core Ultra 7) a ořezanou sérii **Snapdragon X Plus**.
- Přestože je u nich spotřeba bezkonkurenční (100% tiché tenoučké a lehké notebooky s 20hodinovou výdrží u YouTube), potýkají se prozatím s tím, že na nich spoustu klasických a specifických Windows programů a starých her jednoduše nefunguje (nebo běží trhaně přes emulátor vrstvu nazvanou "Prism"). Obyčejný uživatel se spíše drží Intelu a AMD s x86 instrukční sadou.
