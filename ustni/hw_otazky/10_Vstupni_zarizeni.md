---
created: 2026-05-02T09:27
updated: 2026-05-08T13:07
---
# 10. Vstupní zařízení

Vstupní periferní zařízení převádějí fyzikální podněty (mechanický tlak, pohyb, akustické vlnění či fotony) na digitální elektrické signály reprezentované binárním kódem. Jde o jednosměrná komunikační rozhraní, skrze která uživatel předává instrukce či data výpočetnímu systému. Zpracování začíná detekcí fyzikální veličiny příslušným snímačem, po níž následuje A/D (analogově-digitální) převod v mikrokontroléru zařízení. Zkomprimovaný datový tok následně putuje po sériové sběrnici (typicky USB) nebo bezdrátovým spektrem (2,4 GHz, Bluetooth) do řadiče I/O operací a k přerušení (interrupt) procesoru. 

## Počítačová klávesnice

Klávesnice slouží k deterministickému vkládání alfanumerických znaků a systémových příkazů. Fyzicky sestává z maticového zapojení kontaktů. Mikrokontrolér uvnitř klávesnice kontinuálně prohledává (skenuje) tuto matici a při detekci spojení obvodu vygeneruje takzvaný scancode, který operační systém převede na konkrétní znak dle zvoleného kódování. Standardní PC formát tvoří 104 kláves dělených do alfanumerického, numerického, navigačního a funkčního bloku. Mezi základní fyzické layouty patří ISO (s velkým dvouřádkovým Enterem) a ANSI.

### Technologie detekce stisku
- **Membránové klávesnice:** Spoléhají na třívrstvou strukturu. Pod tlakem klávesy dojde k prohnutí silikonové kopule (Rubber Dome) a dotyku dvou vodivých vrstev oddělených distanční fólií. Vykazují nekonzistentní odpor a životnost kolem 10 milionů úhozů.
- **Mechanické klávesnice:** Každá klávesa obsluhuje vlastní fyzický spínač ukrývající kovové kontakty a kalibrovanou ocelovou pružinu. Poskytují precizní dráhu zdvihu a garanci desítek milionů sepnutí.
- **Kapacitní klávesnice:** Zaznamenávají stisk pomocí změny elektrické kapacity mezi dvěma destičkami plošného spoje, které se k sobě mechanicky přibližují. 
- **Optické a Hall Effect spínače:** Moderní bezkontaktní řešení. Optické spínače přerušují světelný paprsek, zatímco magnetické spínače (Hallův jev) kontinuálně měří intenzitu magnetického pole blížícího se magnetu. Umožňují nastavit aktivační bod (Actuation Point) s přesností na zlomky milimetru.

U klávesnic se rovněž sleduje parametr **N-Key Rollover (NKRO)**, označující hardwarovou propustnost stisku velkého množství kláves současně bez ztráty informace.

## Polohovací zařízení

Určena pro vektorové ovládání grafického uživatelského rozhraní v dvourozměrném souřadnicovém systému X a Y. V minulosti dominovala **optomechanická myš**, kde rotující kulička otáčela dvěma na sebe kolmými hřídelemi s děrovanými clonovými kotouči (enkodéry), skrze které prosvěcovala LED dioda. Fototranzistor z frekvence světelných pulzů následně počítal rychlost a směr posunu.

### Optické a laserové senzory
Moderní myš je v principu vysokofrekvenční mikrokamera kombinovaná s DSP (Digital Signal Processor) jednotkou. LED dioda (či koherentní paprsek laserové diody) nasvěcuje povrch a optický CMOS snímač pořizuje tisíce monochromatických snímků za sekundu (až 12 000 FPS). Integrovaný procesor snímky průběžně koreluje, hledá posun povrchových mikronerovností a zjištěné odchylky transformuje na vektory směru.

- **DPI (Dots Per Inch):** Hardwarové rozlišení snímače. Udává počet bodů nasnímaných při lineárním pohybu o jeden palec. Moderní senzory překračují 20 000 DPI.
- **Polling Rate:** Frekvence dotazování sběrnice. Běžná hodnota 1 000 Hz zajišťuje předání informací z myši do PC každou 1 milisekundu. 
- **IPS (Inches Per Second):** Mezní rychlost mechanického pohybu, jakou dokáže senzor zpracovat bez utržení interpolace. 

K alternativním zařízením patří **Touchpad** analyzující změny parazitní kapacity při dotyku prstu, **Trackball** fungující jako obrácená kuličková myš, nebo **Trackpoint** detekující tenzometrický tlak na joystick uložený ve středu klávesnice. Pro profesionální využití slouží **Grafický tablet** zachytávající absolutní souřadnice a úroveň přítlaku stylusu díky elektromagnetické rezonanci (EMR), či vektorová **3D Myš (Spaceball)** s šesti stupni volnosti pro CAD systémy.

## Snímače obrazu a webové kamery

Skener nebo webkamera digitalizují odražené nebo dopadající světelné fotony do maticové rastrové grafiky. Obrazový senzor (CCD nebo CMOS) je plošně rozdělen na miliony fotodiod (pixelů). Každá dioda přeměňuje dopadající fotony na elektrický náboj hromaděný v potenciálové jámě. Následný A/D převodník kvantifikuje napětí na digitální číselnou hodnotu reprezentující úroveň jasu. 

Barevného spektra se dosahuje použitím fyzického filtru (tzv. **Bayerova maska**). Dvě čtvrtiny subpixelů překrývá zelený filtr (člověk je na tuto část spektra nejcitlivější), jednu modrý a jednu červený. Výsledná RGB hodnota každého bodu je softwarově dopočítávána interpolací z okolních buněk. 

- **Rozlišení [DPI / Mpx]:** Hustota záznamové mřížky senzoru. U skenerů definuje fyzické dimenze čipu a počet kroků krokového motoru na jeden palec, u kamer celkový objem matice.
- **Bitová hloubka:** Objem vyhrazené paměti na jeden barevný kanál. Běžný 8-bitový signál nabízí 256 úrovní svítivosti jedné složky (16,7 milionu celkem).

CMOS technologie dnes dominuje díky lokální integraci převodníků napětí a zesilovačů ke každému pixelu zvlášť. Poskytuje vyšší vzorkovací rychlost, menší zahřívání i řádově nižší výrobní náklady než dřívější technologie CCD.

## Akustické převodníky (Mikrofony)

Mikrofon plní funkci elektroakustického měniče, konvertujícího mechanické změny tlaku zvukové vlny na proměnný elektrický signál o nízkém napětí (řádově milivolty). Analogový signál podléhá vzorkování a kvantování pro integraci do číslicové formy.

### Principy akustického snímání
- **Dynamický mikrofon:** Využívá princip elektromagnetické indukce. Na tenké, pohyblivé membráně je zavěšena měděná cívka zapuštěná v permanentním magnetickém poli. Akustický tlak vychyluje membránu a cívka protínající silokřivky indukuje střídavý elektrický proud. Vyznačují se hrubší frekvenční odezvou, ale obrovskou mechanickou a dynamickou odolností bez nutnosti fantomového napájení.
- **Kondenzátorový mikrofon:** Pracuje na základě změn elektrické kapacity. Jednu desku kondenzátoru tvoří zlacená kmitající membrána, druhou pevná elektroda, přičemž systém musí být trvale polarizován fantomovým napětím. Mikroskopické změny vzdálenosti mezi deskami mění kapacitu a vytváří modulační signál s extrémní frekvenční čistotou a vysokou citlivostí.
- **Elektretový mikrofon:** Odbourává nutnost externí polarizace pomocí izolantu (elektretu), do kterého je vnesen permanentní elektrostatický náboj. Obsahuje však integrovaný tranzistorový předzesilovač vyžadující drobný zdroj napětí (např. v USB portu nebo na 3,5mm Jacku).
- **Uhlíkový mikrofon:** Zastaralá technologie měnící přechodový odpor stlačených uhlíkových zrnek; v IT systémech zcela opuštěná pro obrovské zkreslení a vlastní šum.

Z fyzikální topologie konstrukce snímací vložky vychází **Směrová charakteristika** zařízení. Definuje závislost citlivosti mikrofonu na úhlu dopadajících zvukových vln. **Všesměrová** přijímá signál izotropně z celého okolí, zatímco asymetrická **Kardioidní** (ledvinová) účinně tlumí akustický výkon ze zadní hemisféry, což výrazně potlačuje zpětnou vazbu a okolní ruch z pozice od klávesnice.
