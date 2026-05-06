# 10. Vstupní zařízení

Vstupní zařízení jsou nezbytnou součástí počítačového systému, která umožňují uživateli interakci se strojem prostřednictvím vkládání dat, příkazů a řídicích signálů. Tato zařízení převádějí fyzické podněty, jako je stisk klávesy, pohyb ruky, dopadající světlo nebo zvukové vlny, do digitální podoby (binárního kódu), kterou je procesor schopen dále zpracovat. V moderní architektuře PC jsou tato periferní zařízení připojována nejčastěji pomocí sériového rozhraní USB (Universal Serial Bus) nebo bezdrátových technologií jako Bluetooth a Radio Frequency (2.4 GHz), přičemž jejich parametry přímo ovlivňují ergonomii práce, přesnost ovládání a kvalitu digitalizovaných výstupů. Historicky se používaly porty PS/2 pro klávesnice a myši nebo GamePort pro herní ovladače, dnes jsou však standardem vysokorychlostní sběrnice umožňující nízkou latenci a vysokou vzorkovací frekvence.

Celý proces vstupu začíná detekcí fyzické veličiny senzorem, následuje A/D převod a odeslání dat do operačního systému, který je interpretuje skrze příslušné ovladače (drivers). Tato zařízení jsou klíčová pro přenos informací z reálného světa do virtuálního prostředí, čímž tvoří most mezi uživatelem a hardwarem. Rozvoj technologií přináší stále vyšší citlivost snímačů, nižší energetickou náročnost a pokročilé možnosti personalizace, což vede k efektivnější práci a lepšímu uživatelskému zážitku v různých oblastech od kancelářské práce po vědecký výzkum.

## Počítačová klávesnice

Klávesnice představuje primární zařízení pro vkládání textových dat a ovládání operačního systému pomocí příkazových zkratek. Její konstrukce vychází z historického rozložení psacích strojů a dálnopisů, přičemž standardní modely obsahují 101 až 105 kláves rozdělených do několika funkčních bloků (alfanumerický, numerický, funkční F1-F12, navigační a speciální). Klíčovým aspektem moderních klávesnic je technologie spínačů pod jednotlivými klávesami, která určuje hmatovou odezvu, hlučnost a životnost zařízení, což je kritické zejména v profesionální sféře, administrativě a e-sportu.

Moderní trendy směřují k programovatelnosti každé klávesy zvlášť a k implementaci RGB podsvícení, které má kromě estetické funkce i praktický význam pro práci za šera. Pokročilé modely dnes nabízejí i integraci sekundárních displejů, otočných voličů (encoders) nebo programovatelných maker, které zvyšují efektivitu práce v kreativních aplikacích a hrách. Klávesnice se rovněž vyvíjejí z hlediska materiálů, kde se kromě plastů stále častěji využívají hliníkové slitiny pro vyšší tuhost a stabilitu při psaní.

### Technologie a typy spínačů
- **Membránové klávesnice:** využívají gumovou kupoli (rubber dome), která při stisku propojí kontakty na fólii, jsou levné a tiché, ale mají nižší životnost a nekonzistentní odpor.
- **Mechanické klávesnice:** každá klávesa má vlastní fyzický spínač s pružinou, nabízí vysokou odolnost (desítky milionů stisků) a specifickou hmatovou či zvukovou odezvu.
- **Hall Effect (Magnetické) spínače:** využívají senzory k měření intenzity magnetického pole při pohybu magnetu v klávese, což umožňuje zcela bezkontaktní registraci stisku.
- **Nastavitelný zdvih (Actuation Point):** magnetické klávesnice dovolují uživateli softwarově měnit výšku stisku (např. od 0,1 mm do 4,0 mm) potřebnou k registraci znaku.
- **Rapid Trigger:** funkce u magnetických spínačů umožňující okamžitý reset klávesy v momentě, kdy se začne vracet nahoru, bez nutnosti projít pevným resetovacím bodem.
- **Scissor spínače:** nízkoprofilová varianta membránových klávesnic s nůžkovým mechanismem pro stabilizaci stisku, běžná u notebooků a Apple Magic Keyboard.
- **Optické spínače:** registrují stisk přerušením nebo spojením světelného paprsku uvnitř spínače, eliminují mechanické opotřebení kontaktů a mají velmi rychlou odezvu.
- **Hybridní spínače:** kombinují prvky mechanických a membránových systémů ve snaze nabídnout pocit mechaniky za nižší výrobní cenu.
- **Low Profile spínače:** nízkoprofilové mechanické spínače navržené pro tenké klávesnice při zachování hmatové odezvy tradičních mechanických modelů.

### Logické a fyzické rozvržení
- **QWERTY / QWERTZ:** standardní rozložení znaků, česká varianta QWERTZ preferuje prohození Z a Y a obsahuje diakritický řádek v horní části.
- **ISO vs. ANSI:** fyzické standardy určující tvar klávesy Enter (dvouřádkový "zahnutý" u ISO, jednořádkový "podlouhlý" u ANSI) a velikost levého Shiftu.
- **Anti-ghosting:** technologie zajišťující správné rozpoznání stisku více kláves současně, zabraňuje ignorování signálů při rychlém psaní nebo hraní.
- **N-Key Rollover (NKRO):** schopnost klávesnice registrovat libovolný počet současně stisknutých kláves bez vynechání znaku, na rozdíl od levnějších 6KRO modelů.
- **Hot-swap:** konstrukce klávesnice umožňující výměnu mechanických spínačů za jiné bez nutnosti pájení, což prodlužuje životnost a umožňuje customizaci.
- **Tenkeyless (TKL):** typ klávesnice bez numerického bloku, oblíbený pro úsporu místa na stole a lepší ergonomii pro polohování myši.
- **60% / 65% Layout:** kompaktní klávesnice bez numerického bloku, funkční řady a navigačních kláves, zaměřené na přenosnost a minimální design.
- **Ergonomické rozložené:** klávesnice s rozděleným blokem kláves (split keyboard) nebo prohnutým profilem pro snížení napětí v zápěstích uživatele.

## Polohovací zařízení (Myš a alternativy)

Polohovací zařízení slouží k ovládání grafického uživatelského rozhraní (GUI) prostřednictvím pohybu kurzoru v dvourozměrném prostoru (osy X a Y). Moderní počítačová myš prošla vývojem od mechanických kuličkových systémů k pokročilým optickým a laserovým senzorům, které analyzují mikroskopické nerovnosti povrchu pod sebou. Kvalita myši je definována především přesností senzoru, schopností sledovat extrémně rychlé pohyby a stabilitou přenosu dat do počítače.

Ergonomie hraje zásadní roli v prevenci syndromu karpálního tunelu, proto existují modely vertikální nebo různě tvarované pro specifické úchopy (Palm, Claw, Fingertip). Přenos pohybu může být drátový (nízká latence, bez nutnosti nabíjení) nebo bezdrátový, kde moderní 2.4 GHz technologie již dosahují srovnatelných parametrů s kabelem. Moderní myši jsou vybaveny mikrospínači s vysokou životností, programovatelnými tlačítky a vyměnitelnými kluzáky (feets) z PTFE pro plynulý pohyb po podložce.

### Senzory a technické parametry
- **Optický senzor:** využívá LED diodu k osvětlení povrchu a CMOS kameru (DSP), která snímá tisíce obrazů za sekundu pro výpočet vektoru pohybu.
- **Laserový senzor:** varianta optického senzoru využívající koherentní laserový paprsek, schopná pracovat na lesklých površích, ale náchylná k přílišné citlivosti na prach.
- **Moderní snímače (PMW3389 / HERO):** špičkové optické senzory s extrémně vysokou věrností sledování (Tracking) bez umělého vyhlazování (Jitter) nebo akcelerace.
- **DPI (Dots Per Inch):** udává citlivost senzoru, tedy kolik pixelů na obrazovce ujede kurzor při fyzickém posunu myši o jeden palec; moderní myši dosahují až 26 000 DPI.
- **IPS (Inches Per Second):** maximální rychlost pohybu (např. 450 IPS), kterou je senzor schopen korektně zaznamenat bez "utržení" nebo selhání kurzoru.
- **Polling Rate (1000 Hz vs 8000 Hz):** frekvence odesílání informací o poloze do PC; standardních 1000 Hz znamená odezvu 1 ms, zatímco 8000 Hz snižuje latenci na 0,125 ms.
- **LOD (Lift-Off Distance):** výška, do které lze myš zvednout nad podložku, než senzor přestane sledovat pohyb, což je klíčové pro hráče s nízkou citlivostí.
- **Akcelerace:** jev, kdy rychlost pohybu kurzoru nezávisí pouze na uražené vzdálenosti myši, ale i na rychlosti jejího pohybu (v profesionálním použití nežádoucí).
- **MCU (Microcontroller Unit):** vnitřní procesor myši, který zpracovává data ze senzoru, řídí podsvícení a komunikuje s počítačem přes sběrnici.
- **Životnost mikrospínačů:** udává se v počtu kliknutí (např. 80 milionů), moderní optické spínače v myších eliminují problémy s dvojitým kliknutím (double-click).

### Alternativní polohovací zařízení
- **Touchpad:** dotyková plocha integrovaná v noteboocích, reagující na změnu kapacity mezi plochou a prstem, podporuje gesta více prsty pro ovládání systému.
- **Trackball:** stacionární zařízení s kuličkou na horní nebo boční straně, kterou uživatel otáčí prsty, šetří místo na stole a omezuje pohyb celého ramene.
- **Grafický tablet:** podložka reagující na polohu a tlak speciálního pera (stylusu), nezbytný nástroj pro digitální kreslení, retuše a precizní grafické práce.
- **Trackpoint:** malý gumový joystick umístěný uprostřed klávesnice, reagující na směr a intenzitu tlaku, umožňuje ovládání kurzoru bez sundání rukou z klávesnice.
- **Joystick:** pákový ovladač primárně pro letecké simulátory, snímající náklon ve více osách a často obsahující otočnou rukojeť (osa Z).
- **3D Myš (SpaceMouse):** specializované zařízení pro CAD modelování, umožňující pohyb ve všech šesti stupních volnosti (posun i rotace ve třech osách).
- **Dotykový displej:** kombinuje vstupní a výstupní zařízení, využívá odporovou nebo častěji kapacitní vrstvu pro detekci dotyku prstu nebo stylusu.

## Skener a digitalizace obrazu

Skener je zařízení určené k převodu plošných předloh (papírové dokumenty, fotografie, negativy) do digitální rastrové podoby. Proces digitalizace probíhá pomocí lineárního snímače, který se mechanicky pohybuje pod skleněnou deskou a snímá odražené světlo z osvětlené předlohy pomocí kmitající nebo posuvné hlavy. Klíčovým parametrem je optické rozlišení (v DPI), hloubka barev a dynamický rozsah snímače, přičemž volba technologie snímače zásadně ovlivňuje tloušťku zařízení, energetickou náročnost a výslednou kvalitu obrazových detailů.

Aktuálně jsou samostatné skenery vytlačovány multifunkčními zařízeními, ale pro archivaci fotografií nebo digitalizaci filmů zůstávají dedikované stroje nenahraditelné díky své přesnosti a hloubce barevného podání. Pokročilé systémy digitalizace využívají i technologii skenování pomocí digitálních kamer (Camera Scanning), která je výrazně rychlejší u velkých objemů dokumentů nebo historických knih.

### Technologie snímačů
- **CCD (Charge-Coupled Device):** využívá soustavu zrcadel a čoček k přenesení obrazu na malý čip, nabízí vysokou kvalitu, velkou hloubku ostrosti a věrné podání barev.
- **CIS (Contact Image Sensor):** snímač tvořený řadou fotodiod v těsné blízkosti skla, umožňuje konstrukci velmi tenkých skenerů napájených pouze z USB portu.
- **Rozdíl v hloubce ostrosti:** CCD dokáže zaostřit i předměty, které nejsou přímo na skle (např. vazba knihy), zatímco CIS vyžaduje dokonalý kontakt s podložkou pro ostrý obraz.
- **PMT (Photomultiplier Tube):** technologie bubnových skenerů používaná v profesionální předtiskové přípravě pro extrémně vysoké rozlišení a dynamický rozsah.
- **Snímače pro filmy:** speciální adaptéry s prosvětlovací jednotkou (Transparency Unit) umožňující skenování transparentních předloh jako jsou diapozitivy a negativy.
- **CMOS snímače:** využívány v moderních přenosných nebo rychloběžných dokumentových skenerech pro jejich vysokou rychlost čtení dat a nízkou spotřebu.
- **LIDAR:** senzor využívaný pro 3D skenování okolí pomocí odrazu laserových paprsků, běžný v mobilních zařízeních vyšší třídy pro rozšířenou realitu.

### Parametry skenování
- **Optické rozlišení:** skutečný počet fyzických senzorů na palec délky (např. 4800x9600 DPI), na rozdíl od interpolovaného (softwarově dopočítaného) rozlišení.
- **Barevná hloubka:** udává se v bitech (např. 48-bit), určuje kolik miliard odstínů barev je zařízení schopno zaznamenat a rozlišit v RAW datech.
- **ADF (Automatic Document Feeder):** automatický podavač listů umožňující rychlé skenování vícestránkových dokumentů bez nutnosti ručního zvedání víka.
- **TWAIN / WIA:** standardní softwarové protokoly a ovladače zajišťující kompatibilitu skeneru s různými operačními systémy a grafickými programy.
- **OCR (Optical Character Recognition):** softwarová technologie pro rozpoznávání tištěného textu v naskenovaném obrazu a jeho převod do editovatelné podoby.
- **Dmax (Dynamický rozsah):** schopnost skeneru rozlišit detaily v nejtmavších částech předlohy, zásadní pro skenování filmů a umělecké fotografie.
- **Interpolace:** proces digitálního dopočítávání pixelů mezi naskenovanými body pro zvýšení zdánlivého rozlišení, kvalitu však nezvyšuje.

## Ostatní vstupní zařízení

Kromě standardních rozhraní existuje celá řada specializovaných zařízení pro zachycení multimediálního obsahu, prostorových dat nebo biometrických údajů pro zabezpečení. Tato zařízení rozšiřují možnosti počítače o vnímání vizuálního a zvukového okolí, což je základem pro moderní videokonference, streamování obsahu, zabezpečení přístupu a profesionální záznam zvuku pro postprodukci.

Rozvoj technologií umělé inteligence dále rozšiřuje využití těchto vstupů pro automatické rozpoznávání obličejů nebo hlasové ovládání chytrých domácností a asistentů. Moderní systémy integrují více vstupů současně (Multimodal Input), což umožňuje intuitivnější ovládání a vyšší úroveň zabezpečení skrze vícefaktorovou autentizaci. Tato zařízení často využívají specializované signálové procesory pro zpracování dat v reálném čase ještě před jejich odesláním do hlavního procesoru počítače.

### Multimediální vstup
- **Webová kamera:** digitalizuje video v reálném čase, klíčovými parametry jsou rozlišení (Full HD, 4K), snímková frekvence (30/60 fps) a světelnost objektivu (clonové číslo f).
- **Mikrofon:** převádí akustický tlak na elektrický signál; dělí se na dynamické (odolné) a kondenzátorové (citlivé, vyžadující napájení, typické pro studiovou práci).
- **Směrová charakteristika:** vlastnost mikrofonu snímat zvuk z určitých směrů (Kardioidní - zepředu, Kulová - ze všech stran, Osmičková - zepředu i zezadu).
- **Digitální fotoaparát:** ačkoliv jde o samostatný stroj, po připojení k PC slouží jako vstupní zařízení pro přenos dat nebo jako vysoce kvalitní kamera přes HDMI/USB.
- **MIDI klávesnice:** specializované zařízení pro vkládání hudebních informací (noty, dynamika, tempo) do počítače pro další zpracování v hudebních programech (DAW).
- **Digitalizační karta:** zařízení pro záznam video signálu z externích zdrojů (konzole, kamery) do počítače ve vysoké kvalitě.

### Biometrické a prostorové vstupy
- **Snímač otisků prstů:** biometrické zařízení pro autentizaci uživatele, fungující na principu optického, kapacitního nebo ultrazvukového snímání papilárních linií.
- **Infračervená kamera (Windows Hello):** využívá IR světlo pro 3D mapování obličeje, což zajišťuje bezpečnou autentizaci nezávislou na okolním osvětlení.
- **Čtečka čárových / QR kódů:** specializovaný skener (často laserový) pro rychlé čtení zakódovaných informací v logistice, obchodě a výrobě.
- **3D skener:** zařízení snímající geometrii reálného objektu pomocí laserových paprsků nebo strukturovaného světla pro vytvoření digitálního 3D modelu (CAD/STL).
- **Grafický tablet s displejem:** pokročilá verze tabletu, kde uživatel kreslí přímo na obrazovku, což kombinuje výhody vstupního a výstupního zařízení.
- **VR ovladače:** zařízení snímající polohu a rotaci rukou v 3D prostoru pomocí infračervených senzorů nebo interních akcelerometrů a gyroskopů (IMU senzory).
- **Hlasové asistenty:** využívají pole mikrofonů a algoritmy pro zpracování přirozeného jazyka (NLP) k provádění příkazů na základě hlasových vstupů uživatele.
- **Snímač oční duhovky:** vysoce bezpečná biometrická metoda autentizace založená na jedinečném vzoru duhovky, využívaná v kritických bezpečnostních systémech.
- **Haptické rukavice:** pokročilé vstupní zařízení pro virtuální realitu, které snímá pohyb jednotlivých prstů a zároveň poskytuje zpětnou vazbu uživateli.
