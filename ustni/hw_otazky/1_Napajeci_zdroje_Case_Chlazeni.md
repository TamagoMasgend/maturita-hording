# 1. Napájecí zdroje PC, záložní zdroje energie UPS, Case a chlazení PC

## Napájecí zdroje PC (PSU - Power Supply Unit)

Napájecí zdroj je primárním prvkem energetické infrastruktury počítače, fungující jako vysoce účinný usměrňovač a měnič. Transformuje střídavé síťové napětí z elektrické zásuvky (230 V, 50 Hz v Evropě) na nízké, přísně stabilizované stejnosměrné napětí potřebné pro napájení citlivých elektronických komponent (základní deska, procesor, grafická karta, disky). Počítačový zdroj pracuje na principu **spínaného zdroje** (SMPS - Switched-Mode Power Supply). Jeho princip spočívá v tom, že usměrněné síťové napětí je elektronickým spínačem (tranzistorem) "rozsekáno" (spínáno) na vysoké frekvenci (desítky až stovky kHz), transformováno pomocí vysokofrekvenčního feritového transformátoru na nižší úroveň a následně znovu usměrněno a vyhlazeno. Tento postup mu na rozdíl od starých těžkých lineárních transformátorů umožňuje dosahovat obrovských výkonů při zachování extrémně malých rozměrů, nízké hmotnosti a vysoké účinnosti.

### Výkon, příkon a účinnost zdroje

Důležité fyzikální veličiny při výběru zdroje:

- **Příkon (Pin):** Množství elektrické energie, které zdroj odebírá ze zásuvky (střídavý proud). Udává se ve Wattech (W).
- **Výkon (Pout):** Množství stejnosměrného proudu, které zdroj reálně a čistě dodá počítačovým komponentám. Udává se ve Wattech (W). Výpočet z napětí (U) a proudu (I) je podle Ohmova zákona `P = U * I`.
- **Účinnost (η - éta):** Vyjadřuje, jak velká část příkonu se přemění na užitečný výkon. Počítá se jako procentuální podíl výkonu a příkonu `η = (Pout / Pin) * 100 [%]`. Zbylá nepřevedená energie (tzv. ztrátový výkon) se uvnitř zdroje mění na odpadní teplo, které musí být chlazeno.

### Výstupní napěťové větve

Základní dodávaná napětí a jejich standardní barevné značení vodičů:

- **+12 V (žlutý kabel):** Dnes absolutně nejdůležitější a nejvytíženější větev. Slouží k napájení těch nejnáročnějších komponent – procesoru (CPU) a grafické karty (GPU), ale také pohání motory pevných disků (HDD) a ventilátory chlazení.
- **+5 V (červený kabel):** Napájení logických obvodů, USB portů, a elektroniky disků.
- **+3,3 V (oranžový kabel):** Využíváno pro napájení některých integrovaných obvodů na základní desce a pro M.2 SSD paměti.
- **+5Vsb (Stand-by, fialový kabel):** Udržuje základní desku neustále pod mírným proudem, i když je PC "vypnuté" softwarově. Umožňuje probuzení počítače přes síť (Wake-on-LAN), stiskem klávesnice nebo udržuje napájení v USB portech pro nabíjení periferií.
- **Zem (GND, černý kabel):** Tvoří uzavřený obvod pro návrat proudu, uzemnění.

### Vnitřní konstrukce a topologie moderního zdroje

Kvalitní moderní zdroj uvnitř nepoužívá jednoduchou transformaci, ale vysoce sofistikované elektrické obvody:

1. **EMI Filtr:** Odstraňuje elektromagnetické rušení přicházející ze sítě a zároveň brání, aby zdroj rušil okolní přístroje.
2. **Usměrňovač a Aktivní PFC (Power Factor Correction):** PFC obvod koriguje účiník (poměr mezi činným a zdánlivým výkonem). Aktivní PFC dosahuje účiníku až 0,99, což enormně snižuje ztráty a stabilizuje vstupní proud.
3. **Hlavní transformátor a LLC rezonanční měnič:** Přepíná proud na obrovské frekvenci, což umožňuje efektivní přenos obrovského výkonu.
4. **DC-DC konvertory:** Zatímco levné zdroje generují všechna napětí najednou (Group Regulation), prémiové zdroje dnes vyrobí pouze rozsáhlý 12V větev a napětí 5 V a 3,3 V z ní následně "vykrajují" pomocí nezávislých DC-DC měničů. To zaručuje naprostou stabilitu napětí i při extrémní asymetrické zátěži (když např. GPU odebírá nárazově stovky wattů).
5. **Kondenzátory:** Kritická součástka tlumící výkyvy (ripple). Špičkové zdroje se chlubí použitím tzv. japonských kondenzátorů (např. značky Nippon, Rubycon, Nichicon) certifikovaných na výdrž do 105 °C, což zaručuje mnohaletou životnost bez vytečení nebo nafouknutí.

### Standardy a Formáty zdrojů

- **ATX (Advanced Technology eXtended):** Naprostý standard pro stolní PC s rozměry 150 × 86 × 140 mm. Dlouhou dobu trhu dominoval standard **ATX 2.0 / 2.4** s důrazem na rozsáhlý 12V větev. Dnes je aktuální moderní specifikace **ATX 3.0 a ATX 3.1**, která vyžaduje schopnost zdroje absorbovat okamžité rozsáhlý výkonové špičky (Power Excursions) moderních grafických karet, které mohou na zlomek sekundy dosáhnout až dvojnásobku celkového udávaného výkonu zdroje.
- **SFX a SFX-L:** Zmenšené verze pro kompaktní "Mini-ITX" sestavy.
- **TFX / Flex ATX:** Úzké podlouhlé zdroje do malých firemních SFF krabiček.

### Konektory a kabeláž (Modularita a chlazení)

Moderní i starší zdroje využívají standardizované konektory:

- **24-pin ATX Main Power:** rozsáhlý konektor napájející celou základní desku.
- **8-pin (4+4) EPS/ATX12V:** Dedikované napájení výlučně pro napájecí kaskádu procesoru (VRM). High-end desky vyžadují dva tyto konektory.
- **PCIe (6-pin, 8-pin):** Tradiční napájení pro grafické karty (každý 8-pin dodá bezpečně až 150 W, slot na desce dodá dalších 75 W).
- **12VHPWR (nebo nový 12V-2x6):** Revoluční 16-pinový konektor zavedený se standardem PCIe 5.0 a kartami série Nvidia RTX 4000. Jediný tenký kabel dokáže propustit bezprecedentních 600 W čistého výkonu. Novější revize (12V-2x6) upravila délku datových (sense) pinů, aby nedocházelo k protavování konektoru při špatném zastrčení uživatelem.
- **SATA Power:** Tenký plochý konektor s napětím 3,3V, 5V a 12V určený pro datová úložiště a SSD disky.
- **Molex (4-pin):** Starší, velmi rozsáhlý a dnes již ustupující konektor (dodává 12 V a 5 V). Používal se historicky pro staré PATA/IDE pevné disky, staré CD/DVD mechaniky, a často jako hrubá síla pro přídavné ventilátory nebo vodní pumpy.
- **Modularita a chlazení:** Zdroje se dělí na _nemodulární_ (všechny kabely trčí natvrdo ven a zbytečné konektory musí uživatel schovat do skříně), _semi-modulární_ (kabel pro desku a CPU je napevno, zbytek se dá odpojit) a _plně modulární_ (všechny kabely se zacvakávají přímo do těla zdroje). Modularita absolutně zlepšuje uspořádání kabelů uvnitř počítače a nebrání proudění vzduchu. Samotný zdroj se chladí vlastním vestavěným ventilátorem (nejčastěji 120 mm nebo 140 mm), přičemž prémiové zdroje disponují tzv. **Zero RPM (Semi-pasivním) módem** – ventilátor se zcela zastaví a zdroj je do zhruba 30 % až 40 % zátěže chlazen naprosto pasivně a bezhlučně.

### Certifikace účinnosti (80 PLUS a Cybenetics)

Účinnost vyjadřuje, jak velká část elektřiny odebrané ze zásuvky se skutečně dodá do PC a kolik se ztratí jako odpadní teplo. Např. pokud PC odebírá 500 W a zdroj má účinnost 80 %, odebírá ze sítě 625 W (125 W se vyzáří jako teplo a zdroj se musí více chladit).

- Standardní program **80 PLUS** dělí zdroje do tříd: _White (80%), Bronze (85%), Silver, Gold (90%), Platinum (92%), a Titanium (94-96%)_.
- Dnes se stále častěji objevuje nová nezávislá a přísnější certifikace od firmy **Cybenetics**, která hodnotí nejen reálnou účinnost (třídy Bronze až Diamond), ale také vydávaný hluk zdroje (certifikace Lambda).

### Ochranné systémy (Safety Protections)

Kvalitní zdroj musí při poruše chránit zbytek drahého PC před spálením:

- **OVP/UVP:** Ochrana proti přepětí / podpětí.
- **OCP/OPP:** Ochrana proti nadproudu a přetížení (Over-Power Protection) – zdroj se sám vypne, pokud po něm PC požaduje víc výkonu, než zvládne.
- **SCP:** Ochrana proti zkratu (Short Circuit).
- **OTP:** Ochrana proti přehřátí zdroje.

---

## Záložní zdroje energie (UPS - Uninterruptible Power Supply)

Pokud zdroj v PC nechrání plně před výpadky celých bloků elektřiny (tzv. Blackouts), kolísáním frekvence či silnými bleskovými přepětími ze sítě, nastupuje UPS. Uvnitř obsahuje rozsáhlý olověné baterie, ze kterých střídač generuje vlastní napětí.

1. **Off-line (Standby) UPS:** Nejlevnější typ. Za normálního provozu počítač jede ze sítě, a pokud UPS detekuje výpadek, mechanické relé "přepne" na baterii. Prodleva přepnutí (transfer time) je okolo 5–10 milisekund. Většina PC tento kratičký pokles ustojí pomocí kondenzátorů ve svém vnitřním zdroji, ale není to stoprocentní.
2. **Line-interactive UPS:** Standard v domácnostech a malých firmách. Zařízení dokáže pomocí technologie AVR (Automatic Voltage Regulation) chytře "přihlazovat" nebo "podsekávat" výkyvy napětí ve střídavé síti pomocí transformátoru, aniž by muselo ihned plýtvat energii z baterie. Na baterii přepíná až při úplném výpadku proudu.
3. **On-line (Double Conversion) UPS:** Profesionální drahé řešení určené primárně pro serverovny a lékařské přístroje. Střídavý proud ze zdi jde neustále do nabíječky (mění ho na stejnosměrný do baterie) a z baterie neustále jede střídač (z baterie vyrábí dokonalý střídavý proud pro počítač). Díky této dvojité konverzi je výstupní křivka dokonalou amplitudou (čistá sinusoida) a při výpadku sítě **je prodleva přepnutí přesně 0 milisekund**.

- **Komunikace:** Moderní UPS posílají přes datový USB nebo LAN kabel informace přímo do Windows. Uživatel nastaví chování – např. po 5 minutách výpadku sítě dá Windows pokyn korektně uložit všechny soubory a PC se bezpečně samo vypne (tzv. Graceful Shutdown).

---

## Počítačové skříně (Case)

Skříň neslouží jen jako designový obal, ale jako sofistikovaný aerodynamický tunel a štít. Její úkol je tlumit vibrace (HDD a větráků), chránit elektroniku před prachem a elektromagnetickým zářením a hlavně definovat směr toku chladného vzduchu (Airflow).

### Parametry, čelní panel a konstrukce

- **Materiál:** Dříve béžové ocelové SECC plechy, dnes dominují vyztužené ocelové šasi, prémiový kartáčovaný hliník, prosklené bočnice z poctivého tvrzeného skla (Tempered Glass), a spousta jemného meshe (děrovaného plechu) na čelním panelu pro průchod vzduchu.
- **Čelní panel (Front I/O Panel):** Uživatelsky přístupná konektorová část skříně, umístěná nejčastěji nahoře nebo vpředu. Obsahuje spínač napájení (Power Button), tlačítko pro tvrdý restart (Reset Button), audiokonektory pro sluchátka a mikrofon (3.5mm jack) a sadu rychlých USB portů (USB-A 3.0, moderně i vysokorychlostní USB-C). Tyto porty a spínače se interně napojují kabely na "headery" piny přímo na základní desce.
- **Cable Management:** Velmi důležitá vlastnost moderních skříní – za hlavní stěnou desky (tray) je vyhrazený prostor pro protažení tlustých kabelů ze zdroje, čímž se vyhne hromadění kabelů u procesoru, které by ničily Airflow a zadržovaly teplo.
- **Filtry proti prachu:** Skříně mají zespodu (u nasávání PSU) a vpředu (u hlavních větráků) omyvatelné prachové filtry, často chycené magneticky.
- **Formáty (Velikosti) a typy:** Dělí se primárně podle kompatibilní základní desky:
  - **Full Tower a Mid Tower:** Klasické velkoformátové ATX skříně pro herní sestavy, s výškou pro mnoho disků a velké vodní radiátory.
  - **Mini Tower:** Pro kompaktní mATX nebo ITX desky.
  - **Cube (Kostka):** Široké dvoudílné skříně ve tvaru kostky ("dual-chamber" design), kde je základní deska vepředu za sklem, zatímco zdroj, kabely a disky jsou neviditelně skryté ve druhé komoře za ní, což poskytuje neuvěřitelně čistý estetický vzhled a skvělý airflow.
  - **HTPC (Home Theater PC) a SFF (Small Form Factor):** Úzké a velmi malé krabičky určené primárně do obýváku pod televizi k přehrávání médií nebo jako rozsáhlý úsporné kancelářské řešení na malý stůl.
  - **Rackové skříně:** Profesionální ploché rozsáhlý servery zasouvané do 19" ocelových rozvaděčů v datacentrech (výška označovaná v "U" - např. 1U, 2U).
- **Casemodding:** Výrazná technická disciplína spojená se stavbou PC (PC Building), kdy si nadšenci svá PC upravují z estetického a funkčního hlediska. Řežou či ohýbají plechy, instalují rozsáhlý adresovatelné ARGB osvětlení řiditelné deskou, frézují vlastní komponenty, vytvářejí unikátní lakování a tvoří ze své skříně technologické umělecké dílo.

### Tlak vzduchu ve skříni (Airflow)

Jak nastavíme směr otáčení ventilátorů zepředu dozadu určuje tzv. vnitřní tlak:

- **Pozitivní (přetlak):** Nasáváme více vzduchu (více ventilátorů fouká dovnitř zepředu), než vyfukujeme. Zabraňuje to vnikání prachu škvírami, ale vzduch hůře opouští skříň.
- **Negativní (podtlak):** Výkonné ventilátory táhnou horký vzduch intenzivně ven. Výborné chlazení komponent, ale do skříně se velmi silně saje prach přes každý netěsný otvor.
- **Neutrální (balancovaný):** Ideální stav – přední mesh saje studený vzduch rovnou přes filtry a zadní a horní větráky ho po ohřátí o čipy okamžitě tahají ven.

---

## Chlazení počítačových komponent

Vzhledem k faktu, že procesor o rozměru nehtu běžně spotřebuje i více než 200 Wattů, generuje extrémně vysokou tepelnou hustotu (větší než plotýnka na vařiči). Musíme dodržovat zákony termodynamiky k odvedení tepla pryč, jinak by se čip za 2 sekundy přehřál, vypnul, nebo fyzicky spálil (zničil).

### Metody převodu tepla a styčné plochy

- **Teplovodivá pasta (Thermal Paste / Compound):** Mikroskopické nerovnosti na kovu chladiče i na povrchu čipu (IHS) způsobují, že by se dotýkaly jen v pár bodech (vzduch izoluje teplo). Pasta na bázi mikroskopických částeček stříbra, keramiky či diamantu v silikonovém oleji tyto spáry dokonale vyplní a tepelná energie volně přechází do kovu chladiče.
- **Tekutý kov (Liquid Metal):** Extrémně drahé řešení založené primárně na indiu a galiu. Má neuvěřitelnou tepelnou vodivost, ale je elektricky vodivé a reaguje (rozežírá) hliníkové chladiče. Nasazuje se opatrně v top herních noteboocích (ASUS ROG, PlayStation 5) nebo si ho extrémní tuneři bourající rekordy nanášejí sami po "delidu" (sejmutí obalu procesoru z výroby).

### Typy chlazení v PC

1. **Pasivní (Fanless) chladič:** Blok extrudovaného kovu (hliníku nebo drahé, ale velmi tepelně vodivé mědi). Rozděluje teplo do tenkých žeber (Fins) a spoléhá na běžné proudění okolního vzduchu. Zcela bezhlučné řešení (nulový pohyb). Najdeme ho na M.2 discích, VRM kaskádách základních desek, nebo ve velmi úsporných noteboocích.
2. **Aktivní vzduchové chladiče:** Na pasivní žebrování (Heatsink) namontujeme ventilátor. Větráky dnes mají průměr nejčastěji 120 mm nebo 140 mm. Propojení hliníkových věží a procesoru probíhá pomocí **Heatpipes (Teplovodivých trubic)**. Je to uzavřená měděná trubička, uvnitř které je vakuum a kapička kapaliny. Kapalina se u horkého CPU okamžitě odpaří (změní skupenství = nabere spoustu tepla), pára vylétne nahoru k věži, ventilátor ji ofoukne studeným vzduchem, pára zkondenzuje a díky kapilárním silám knotu uvnitř stéká dolů na další cyklus. Pokročilejší verze, **Vapor Chamber** (Odpařovací komora), se hojně používá v moderních highendových GPU pro rozptýlení tepla na velkou plochu najednou.
3. **Vodní chlazení (Liquid Cooling):** Zcela neefektivnější metoda, vzhledem k tomu že voda má mnohonásobně vyšší tepelnou kapacitu (schopnost absorbovat energii) než vzduch. Dělí se na dvě kategorie:
   - **AIO (All-In-One) sety:** Uzavřený bezúdržbový okruh koupený v celku. Skládá se z bloku na CPU s integrovanou pumpou z jedné strany, a hadicemi k hliníkovému radiátoru (rozměry jako 240 mm nebo oblíbených 360 mm délky) osazenému běžnými větráky z druhé strany.
   - **Custom Loop (Vlastní okruh):** Stavebnice pro velmi pokročilé. Uživatel si okruh vodního chlazení konstruuje přesně na míru z oddělených zakoupených částí. Patří sem poniklované měděné bloky na CPU a GPU, velký hliníkový či měděný radiátor k odvádění tepla, a velmi důležitá vodní pumpa (čerpadlo), která tlačí tekutinu systémem, přičemž standardem mezi nadšenci jsou výkonná čerpadla typu D5. K čerpadlu často přiléhá průhledná expanzní nádržka pro uchování vody (rezervoár). Komponenty propojují hadice, nebo stále častěji pevné průhledné akrylové a PETG trubice (hard tubing), které si uživatelé ohýbají a tvarují nad horkovzdušnou pistolí do přesných úhlů přes těsnící fitinky. Jako kapalina se nedává obyčejná voda, ale čistá chemická **destilovaná voda** (nesmí obsahovat minerály, aby nevodila elektrický proud) s biocidy proti růstu řas. K docílení dokonalého moddingového vizuálu se místo čiré destilky lijí speciálně upravené probarvené tekutiny, jako je neprůhledná **pastelová voda**, nebo rovnou hotovou namíchanou **premixovanou tekutinu** určenou od výrobců přímo pro počítačové chlazení, jež září pod UV osvětlením. Výsledkem je exkluzivní absolutní vrchol chlazení za cenu ohromné obtížnosti, rizika úniku kapaliny a astronomické finanční ceny.
4. **Peltierovy články (TEC) a Fázové měniče:** Termoelektrické chlazení využívající proud pro stlačení tepla na jednu stranu destičky (druhá strana hluboko mrzne). Objevuje se výjimečně, protože žere moc elektřiny a produkuje kondenzaci vody uvnitř PC, což je smrtelné pro desku.
5. **Extrémní a Sub-Zero chlazení (LN2):** Tzv. chlazení tekutým dusíkem. Na procesor se nasadí rozsáhlý měděný "komín" a ručně se do něj leje dusík o teplotě -196 °C. Jedná se čistě o krátkodobý sport na soutěžích k překonávání světových rekordů ve frekvenci (overclocking k 9,0 GHz), komponenty by takto běžně nevydržely.

### Zásady u ventilátorů (Airflow fans vs Static Pressure fans)

Ventilátory se dělí podle geometrie lopatek. Někdy chceme přesouvat moc vzduchu přes prázdnou bednu (**Airflow** ventilátor - hodně oblých lopatek, rozsáhlý průtok kubických stop CFM), jindy naopak potřebujeme vzduch přímo protlačit silou skrz velmi tlustý ucpávající radiátor vodního chlazení či heatpipes, aniž by se nám odrážel zpět (**Static Pressure** ventilátor – široké ploché agresivně zalomené lopatky). Dnešní prémiové ventilátory, např. od firem Noctua či Phanteks, využívají technologii Magnetické levitace nebo kapalinových ložisek (FDB - Fluid Dynamic Bearing) doplněnou o **PWM regulaci** ze základní desky (4-pin kabel dodávající pulzy pro regulaci otáček RPM dle aktuální teploty bez ztráty výkonu) pro dokonalý balanc neslyšného ticha a hurikánové síly.
