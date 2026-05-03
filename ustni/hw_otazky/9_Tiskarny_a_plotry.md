# 10. Tiskové a velkoformátové výstupní systémy

Tiskárna představuje základní hardwarové **výstupní zařízení**, které slouží k transformaci digitálních dat z elektronické podoby na fyzický nosič, nejčastěji papír, speciální fólie nebo trojrozměrný materiál. Moderní kancelářské prostředí často využívá tzv. multifunkční zařízení (MFP), která integrují tiskovou jednotku s plochým skenerem, čímž umožňují přímé kopírování bez nutnosti zapojení počítače. Základním principem všech tiskových technologií je nanášení barviva v rastru bodů, jejichž hustota a kvalita definují výslednou věrnost reprodukce. Vývoj těchto zařízení směřuje k vyšší energetické efektivitě, snížení ekologické stopy spotřebního materiálu a integraci bezdrátových technologií pro přímý tisk z mobilních platforem a cloudových úložišť.


## 1. Technické parametry a standardy tisku

Kvalita a efektivita tisku jsou definovány souborem standardizovaných parametrů, které umožňují objektivní srovnání různých technologií a modelů. Tyto metriky určují vhodnost zařízení pro konkrétní nasazení, od rychlého tisku administrativních dokumentů po precizní grafické výstupy. Výrobci tiskáren často uvádějí teoretické maxima těchto parametrů, přičemž reálný výkon závisí na nastavení ovladačů a použitém médiu.

- **DPI (Dots Per Inch):** Počet tiskových bodů na jeden palec (2,54 cm). Udává prostorové rozlišení tisku; pro běžný text postačuje 300–600 DPI, profesionální fotografie vyžadují 1200–4800 DPI pro potlačení viditelného rastru.
- **PPM (Pages Per Minute):** Počet stran vytištěných za jednu minutu. Tato hodnota se liší pro černobílý a barevný tisk a je ovlivněna složitostí grafiky a nastavením kvality.
- **CMYK Barevný model:** Subtraktivní model míchání barev používaný při tisku (na rozdíl od aditivního RGB u monitorů). Využívá azurovou (Cyan), purpurovou (Magenta), žlutou (Yellow) a černou (Key/Black) barvu.
- **Duplex:** Schopnost tiskárny automaticky potiskovat obě strany papíru bez nutnosti ručního obracení uživatelem. Tato funkce šetří náklady na spotřební materiál a snižuje objem tištěné dokumentace.
- **Tisková paměti (Buffer):** Vnitřní paměť zařízení sloužící k ukládání tiskových úloh, což uvolňuje prostředky počítače ihned po odeslání dat. U profesionálních tiskáren se tato paměť pohybuje v řádech gigabajtů.
- **PDL (Page Description Language):** Jazyk, kterým počítač popisuje tiskárně vzhled stránky (např. PostScript nebo PCL). Umožňuje přenos složitých grafických prvků a fontů s vysokou přesností nezávisle na konkrétním hardware.
- **Měsíční zatížení (Duty Cycle):** Maximální počet stran, které je tiskárna schopna vytisknout za jeden měsíc bez rizika mechanického poškození. Je to klíčový ukazatel robustnosti konstrukce.
- **Gramáž papíru:** Rozsah tloušťky a hustoty papíru (v g/m²), který je podávací mechanismus schopen bezpečně zpracovat bez zaseknutí (běžný kancelářský papír má 80 g/m²). Profesionální tiskárny zvládnou i gramáže nad 300 g/m² (vizitky, kartony).
- **Konektivita:** Rozhraní používaná pro připojení k počítači nebo síti, zahrnující historické LPT (paralelní port), moderní USB 2.0/3.0, Ethernet (RJ-45) pro síťové sdílení a bezdrátové standardy Wi-Fi nebo Bluetooth.


## 2. Inkoustová technologie tisku (Inkjet)

Inkoustový tisk je založen na technologii vstřikování mikroskopických kapiček tekutého barviva přímo na povrch média. Tato metoda dominuje v oblasti domácího tisku a profesionální fotografie díky schopnosti plynulého míchání barevných odstínů a vysokému nativnímu rozlišení. Moderní inkoustové systémy využívají pokročilé algoritmy pro dithering (rozptylování bodů), které simulují plynulé přechody barev i při omezeném počtu základních inkoustů. Proces probíhá v tiskové hlavě, která se pohybuje horizontálně nad papírem, zatímco podavač zajišťuje precizní vertikální posun papíru v krocích odpovídajících řádkům tiskových trysek.

### Typy tiskových hlav
- **Termální tisková hlava (Bubble-jet):** Pracuje na principu rychlého zahřátí inkoustu topným tělískem v trysce. Vzniklá parní bublina vytlačí kapku inkoustu ven. Tato technologie je konstrukčně jednodušší, ale hlavy mají omezenou životnost (často integrované na cartridge).
- **Piezoelektrická tisková hlava:** Využívá piezoelektrický krystal, který při průchodu elektrického proudu mění svůj objem a mechanicky vypuzuje inkoust. Umožňuje přesnější kontrolu velikosti kapky a vyšší frekvenci tisku, hlavy jsou obvykle trvalou součástí tiskárny.
- **Micro-Electro-Mechanical Systems (MEMS):** Moderní technologie výroby tiskových hlav využívající litografické postupy podobné výrobě procesorů, což umožňuje integraci tisíců mikroskopických trysek na extrémně malou plochu pro dosažení ultra-vysokého rozlišení.
- **Konstrukce trysek:** Každá tryska obsahuje filtr nečistot a zpětnou komůrku, která zabraňuje odrazu tlakové vlny zpět do zásobníku, což zajišťuje konzistentní objem vystřelované kapky i při vysokých rychlostech tisku.

### Charakteristika inkoustů
- **Dye-based (rozpouštědlový):** Barvivo je zcela rozpuštěno v kapalině. Nabízí brilantní barvy a vynikající přechody na fotopapíru, ale je náchylný k blednutí vlivem UV záření a rozpíjení při kontaktu s vodou.
- **Pigmentový inkoust:** Obsahuje pevné částice barviva suspendované v nosné kapalině. Je vysoce odolný proti vodě, otěru a UV záření, což jej činí ideálním pro tisk textových dokumentů a archivačních tisků.
- **UV-vytvrditelné inkousty:** Speciální polymery, které po dopadu na médium okamžitě vytvrzují pod UV lampou integrovanou v tiskové hlavě. Používají se především v průmyslovém tisku na nesavé materiály (sklo, kov, plast).
- **Sublimační inkousty:** Pevná barviva, která se teplem mění v plyn. Používají se pro přenos grafiky na textilní materiály (polyester) nebo upravené pevné povrchy (hrnky, plechy).

### Provozní vlastnosti
- **Výhody:** Nízká pořizovací cena zařízení, špičková kvalita fotografií, možnost tisku na širokou škálu médií včetně CD/DVD.
- **Nevýhody:** Vysoké náklady na originální spotřební materiál, riziko zaschnutí inkoustu v tryskách při delší nečinnosti (vyžaduje pravidelné pročišťování).
- **Tankové systémy (ITS):** Moderní řešení nahrazující malé cartridge velkými doplňovatelnými nádržkami, které radikálně snižují náklady na stránku.
- **Čištění hlav:** Automatizovaný proces, při kterém tiskárna odsává malé množství inkoustu přes trysky, aby odstranila bublinky vzduchu nebo zaschlé zbytky, což zvyšuje spotřebu barviva při nepravidelném používání.

## 3. Laserová a LED technologie tisku

Laserový tisk využívá elektrografický princip, kdy je obraz přenášen pomocí statické elektřiny a následně fixován teplem. Tato technologie je preferována v kancelářském prostředí pro svou vysokou rychlost, preciznost textu a nízké provozní náklady při vysokých objemech. Celý proces probíhá v uzavřeném cyklu, který zahrnuje čištění, nabíjení, osvit, vyvolání, přenos a fixaci, přičemž moderní stroje dokáží tyto kroky provádět s milimetrovou přesností při rychlostech přesahujících 50 stran za minutu.

### Konstrukční prvky a proces
- **Nabíjení válce:** Světlocitlivý polovodičový válec (OPC) je nabit na vysoký elektrostatický potenciál.
- **Corona Wire vs. Charge Roller:** Starší systémy využívaly korónový drát, který ionizoval vzduch (produkce ozónu); moderní tiskárny používají nabíjecí váleček (PCR), který je v přímém kontaktu s válcem, je tišší a ekologičtější.
- **Expozice:** Laserový paprsek nebo řada LED diod vykresluje obraz na válec, čímž lokálně mění jeho náboj (vybíjí jej).
- **Vyvolávání:** Na vybitá místa se přichytí jemný plastový prášek – toner.
- **Fixace (Fuser):** Papír s naneseným tonerem prochází zapékací jednotkou, kde je vystaven teplotě mezi **180 °C až 220 °C** a tlaku, čímž se plastový prášek roztaví a vlisuje do struktury papíru.
- **Transferový pás:** U barevných tiskáren slouží k postupnému nanášení všech čtyř barev toneru (CMYK) předtím, než je kompletní obraz přenesen na papír, což zajišťuje dokonalý soutisk.
- **Sběr odpadního toneru:** Mechanická stěrka odstraňuje zbytky prášku z válce po přenosu na papír a ukládá je do vyhrazené odpadní nádobky, která musí být po zaplnění vyměněna.

### Provozní vlastnosti
- **Výhody:** Extrémní rychlost tisku, stálost výstupu (nerozmazává se vodou), toner nepodléhá zasychání ani po letech nečinnosti.
- **Nevýhody:** Vyšší počáteční investice, nevhodnost pro tisk kvalitních fotografií (rastrování), vyšší spotřeba elektrické energie během nahřívání fuseru.
- **Ekologické aspekty:** Moderní tonerové kazety jsou často recyklovatelné a tiskárny jsou vybaveny filtry pevných částic pro minimalizaci emisí prachu z toneru do ovzduší kanceláře.
- **Životnost komponent:** Kromě toneru vyžaduje laserová tiskárna pravidelnou výměnu válce (Drum unit) a přenosového pásu po vytištění určitého počtu tisíc stran.

## 4. Termální technologie tisku

Termální tiskárny využívají teplo jako primární prostředek k vytvoření obrazu na médiu. Tato kategorie zařízení se vyznačuje vysokou spolehlivostí díky minimu pohyblivých částí a absenci klasických inkoustů nebo tonerů, což výrazně zjednodušuje údržbu v náročných provozních podmínkách. Technologie se dělí na systémy využívající chemickou reakci v papíru a systémy založené na fyzikálním přenosu barviva z nosné fólie.

### Subkategorie termotisku
- **Přímý termální tisk:** Využívá speciální teplocitlivý papír, který v místě zahřátí tiskovou hlavou zčerná. Nepotřebuje žádné další barvicí médium, což minimalizuje provozní náklady. Typické nasazení zahrnuje pokladní systémy v supermarketech, parkovací automaty, jízdenkové terminály a mobilní tiskárny štítků pro logistiku. Hlavní nevýhodou je citlivost výstupu na okolní teplo a UV záření, které způsobuje postupné blednutí obrazu.
- **Termosublimační tisk:** Zahřívací hlava s vysokým rozlišením odpařuje pevné barvivo z vícebarevné nosné fólie (pásky), které v plynné formě proniká hluboko do struktury speciálního porézního papíru. Na rozdíl od inkoustu nevytváří kapky, ale spojité barevné plochy, čímž dosahuje fotorealistické kvality srovnatelné s klasickou fotografií. Výsledný tisk je v posledním kroku automaticky laminován čirou ochrannou vrstvou, která zajišťuje odolnost proti vlhkosti a otisku prstů.
- **Termotransferový tisk (TTR):** Podobný přímému termálnímu tisku, ale využívá barvicí pásku (resin nebo vosk), která se teplem přenese na běžný papír nebo syntetickou fólii. Tento typ tisku je extrémně odolný a používá se pro trvanlivé značení součástek v průmyslu nebo tisk čárových kódů v náročných skladech.
- **Zink (Zero Ink):** Speciální technologie využívaná v kapesních tiskárnách, kde papír obsahuje krystaly barviv aktivované různými úrovněmi a délkami tepelných impulsů, čímž vzniká plnobarevný obraz bez potřeby pásky nebo inkoustu.

## 5. Velkoformátový výstup a Plotry

Plotr je specializované zařízení navržené pro přesný výstup ve velkých formátech (typicky A2 až A0+), které nachází uplatnění v inženýrském projektování, architektuře, kartografii a reklamním průmyslu. Moderní plotry se od běžných tiskáren liší nejen rozměry, ale i schopností pracovat s nekonečnými rolemi papíru, textilními bannery nebo samolepicími fóliemi. Vektory jsou v těchto zařízeních interpretovány s vysokou přesností, což je klíčové pro zachování měřítek u stavební dokumentace.

### Typy a principy velkoformátových zařízení
- **Perový plotr:** Historicky první typ, kde mechanické rameno fyzicky drží technické pero nebo tuš a kreslí čáry přímo na papír podle matematických vektorů. Je ideální pro precizní perokresbu, ale neumožňuje tisk barevných ploch. Dnes je v technické praxi prakticky zcela nahrazen inkoustovými technologiemi.
- **Inkoustový plotr (Velkoformátová tiskárna):** Funguje na stejném principu jako stolní inkoustová tiskárna, ale tisková hlava pojíždí nad širokým formátem média. Využívá se pro tisk barevných vizualizací, GIS map a reklamních posterů. Špičkové modely používají až 12 barev pro dokonalé podání barevných prostorů.
- **Řezací plotr (Vinyl Cutter):** Namísto psacího nástroje je osazen mikroskopickým, velmi ostrým a otočným nožem (skalpelem). Ten s vysokou přesností vyřezává tvary a texty do samolepicích vinylových fólií. Je to základní stroj pro výrobu polepů vozidel, výloh a sportovních dresů.
- **Laserový plotr:** Využívá výkonný laserový paprsek k vypalování nebo řezání pevných materiálů, jako je dřevo, plexisklo nebo kůže. Umožňuje gravírování detailních log a textů do reklamních předmětů nebo přesné vyřezávání modelářských dílů.
- **Flatbed plotr:** Zařízení s pevnou rovnou plochou, nad kterou se pohybuje tiskový most. Umožňuje tisk přímo na pevné deskové materiály (Kapa desky, sklo, plech), které nelze ohýbat v rolích.

## 6. 3D tisk (Aditivní výroba)

3D tisk představuje proces vytváření fyzických objektů z digitálního 3D modelu postupným nanášením nebo vytvrzováním materiálu po vrstvách (aditivní proces). Tato technologie transformovala prototypování a malosériovou výrobu tím, že umožňuje realizaci geometrií, které jsou tradičními metodami obrábění (subtraktivní procesy) nevyrobitelné. Základem úspěšného tisku je kvalitní příprava dat, volba vhodného materiálu vzhledem k mechanickému namáhání výsledného dílu a správná orientace objektu na tiskové podložce pro minimalizaci nutnosti podpůrných struktur.

### Hlavní technologie 3D tisku
- **FDM (Fused Deposition Modeling):** Tavení plastové struny (filamentu) a její vytlačování přes trysku na podložku. Je to nejrozšířenější metoda díky své jednoduchosti, bezpečnosti a nízkým nákladům na provoz.
    - **Materiály:** PLA (kukuřičný škrob, biologicky rozložitelný, tuhý ale křehký), PETG (odolný proti nárazu a chemikáliím, ideální pro funkční díly), ASA (vysoce UV stabilní a teplotně odolný, vhodný pro venkovní použití), ABS (pevný, vyžaduje uzavřený box kvůli tepelné roztažnosti a výparům).
- **SLA (Stereolitografie):** Vytvrzování tekuté fotocitlivé pryskyřice (resinu) pomocí UV laseru nebo LCD displeje s vysokým rozlišením. Tato metoda dosahuje mikroskopické přesnosti a naprosté hladkosti povrchů, což ji činí ideální pro šperkařství, stomatologii a miniatury.
    - **Post-processing:** Hotový výtisk z SLA tiskárny je nutné omýt v isopropylalkoholu (IPA) pro odstranění zbytkového resinu a následně nechat vytvrdit v UV komoře pro dosažení finálních mechanických vlastností.
- **SLS (Selective Laser Sintering):** Průmyslová metoda, kde výkonný laser spéká jemný plastový (nylonový) prášek. Nevyžaduje žádné podpory, protože nespečený prášek v komoře slouží jako přirozená opora pro tisk.
- **DLP (Digital Light Processing):** Obdobná technologie jako SLA, ale využívá projektor k vytvrzení celé vrstvy resinu najednou, což výrazně zrychluje tiskový proces u velkých objektů.

### Technické aspekty a parametry
- **Výška vrstvy:** Udává se v mikronech (obvykle 100–300 µm u FDM, 25–50 µm u SLA). Menší výška znamená hladší povrch, ale exponenciálně delší dobu tisku.
- **Infill (Výplň):** Vnitřní struktura 3D objektu, která může být mřížková, gyroidní nebo medová. Umožňuje šetřit materiál a čas při zachování vysoké pevnosti dílu.
- **Teplotní management:** Přesná regulace teploty trysky (200–300 °C) a vyhřívané podložky (60–110 °C) je klíčová pro adhezi první vrstvy a prevenci deformací (warping) vlivem vnitřního pnutí materiálu.
- **Slicing a G-kód:** Specializovaný software (Slicer) rozřeže 3D model (typicky formát .STL nebo .3MF) na horizontální řezy a vygeneruje textový soubor s instrukcemi (G-kód) pro motory a topná tělesa tiskárny.
- **Podpory (Supports):** Dočasné struktury tištěné společně s modelem, které podpírají převisy (overhangy) větší než 45 stupňů. Po dokončení tisku se mechanicky nebo chemicky (rozpustné materiály) odstraňují.
- **Kalibrace:** Proces nastavení roviny podložky a vzdálenosti trysky od povrchu (Z-offset), který je kritický pro správné přilnutí první vrstvy materiálu.
