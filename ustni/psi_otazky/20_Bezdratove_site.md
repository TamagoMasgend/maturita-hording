# 20. Bezdrátové síťové technologie

Bezdrátové (Wireless) počítačové sítě upouští od tradičních měděných či optických kabelů a k přenosu logických informací zneužívají volný prostor pomocí elektromagnetického vlnění (rádiových vln, mikrovln nebo zlomku infračerveného spektra). To umožňuje absolutní mobilitu, odstranění instalačních limitů pro kabeláž a rapidní škálovatelnost.

## Fyzikální principy vlnění
Elektromagnetická vlna je šířící se fyzikální jev složený ze dvou vzájemně kolmých složek – elektrické a magnetické. Lze ji exaktně popsat těmito základními vlastnostmi:
- **Frekvence ($f$)**: Počet plných kmitů vlny za jednu sekundu, udávaný v Hertzech (Hz). Megahertz (MHz) představuje milion kmitů, Gigahertz (GHz) miliardu kmitů za vteřinu. Například pásmo 5 GHz znamená, že se vlna otočí pětmiliardkrát za jedinou vteřinu.
- **Perioda ($T$)**: Trvání jednoho plného kmitu v čase (sekundách).
- **Vlnová délka ($\lambda$)**: Přímá vzdálenost, kterou vlna urazí během jedné periody (vzdálenost mezi dvěma vrcholy). Spočítá se jako rychlost světla dělená frekvencí ($c / f$). 

Z fyziky antén vyplývá kruté pravidlo: **Nižší frekvence (delší vlna)** poskytuje lepší prostupnost pevnými překážkami (zdmi) a delší dosah, ale s mnohem menší datovou propustností. Naopak **Vysoká frekvence (např. 60 GHz)** poskytuje astronomické šířky pásma, ale její vlnovou délku zcela pohltí i okenní sklo nebo déšť.

Komerční nasazení podléhá přísné legislativě. **Licencovaná pásma** (GSM operátoři, satelity, DVB-T televize) vyžadují zakoupení absolutně exkluzivního práva u státního telekomunikačního úřadu na vyhrazenou frekvenci (garance nerušeného chodu). **Bezlicenční pásma** (nejčastěji ISM: Industrial, Scientific, and Medical na 2,4 GHz a 5 GHz) může k tvorbě Wi-Fi a Bluetooth sítí zdarma využít naprosto kdokoli, pod podmínkou, že nepřekročí legislativou povolený vysílací výkon antény (např. u Wi-Fi routerů 100 mW v EU a až 300 mW v USA).

## Typologie Wireless sítí

### Wireless LAN: Sítě Wi-Fi (IEEE 802.11)
Wi-Fi (Wireless Fidelity) je nezpochybnitelným tržním králem lokálních bezdrátových sítí na frekvencích 2,4 GHz a 5 GHz. Na 2,4 GHz je propustnost limitována fyzickým překrýváním pouhých 13 úzkých kanálů a rozsáhlý zahlcením od Bluetooth či mikrovlnek. Proto pásmo 5 GHz (a nejnovější 6 GHz u Wi-Fi 6E/7) otevírá desítky nerušených, tlustých kanálů.
- Základní prvky sítě tvoří klienti (síťové bezdrátové karty v laptopech a mobilech) a **Access Point (AP - Přístupový bod)**. Moderní AP, lidově zvané Wi-Fi Router, v sobě kombinuje L2 rozhraní a L3 NAT s DHCP k překlenutí bezdrátových a metalických sítí dohromady.
- Sítě mohou fungovat v primitivní **Ad-hoc (P2P)** architektuře, kdy k sobě telefony na pikniku přistupují zcela napřímo. Pro podniky se využívá **Infrastrukturní** mód řízený centrálním AP odesílacím broadcastovým majákem identifikátor sítě **SSID**.

Evoluce IEEE 802.11 s cílem navyšovat přenosové rychlosti pomocí vícecestného propouštění antén (MIMO) a širších kanálů:
- **802.11b (1999)**: Propustnost do **11 Mb/s** pouze přes staré pásmo 2,4 GHz.
- **802.11a (1999)**: Propustnost do **54 Mb/s** překvapivě pouze na 5 GHz.
- **802.11g (2003)**: Sjednocení na 2,4 GHz a s rychlostí **54 Mb/s**.
- **802.11n (2009 - Wi-Fi 4)**: Příchod MIMO technologií, nasazeno na 2,4 GHz i 5 GHz rychlostí zhruba **600 Mb/s**.
- **802.11ac (2014 - Wi-Fi 5)**: Průlom pouze na 5 GHz v rozsáhlých rychlostech nad **1,3 Gb/s** pro moderní multimedia. K řízení směrovosti začal být využíván *Beam forming*, kdy router sleduje pohybující se mobily v domě a matematicky formuje fázi záření antén čistě jejich směrem.
- **802.11ax (2019 - Wi-Fi 6)**: Rychlosti okolo **12 Gb/s** využívající hustou OFDM modulaci napříč oběma spektry, schopné nezahlcovat router ani na přeplněných stadionech (technologie BSS Coloring).

Řešení kolizí na Wi-Fi je z důvodu "neviditelnosti" pro rádiové obvody problematické. Nelze implementovat drátové CSMA/CD, proto Wi-Fi nasazuje **CSMA/CA (Collision Avoidance)**, které fyzicky předchází srážkám čekáním nebo předposíláním logických varovných rámců (RTS/CTS), jimiž umlčí všechny okolo před spuštěním vlastního přenosu.

### Wireless PAN a sítě pro IoT (Internet of Things)
Sítě kategorie PAN pro malou plochu okolo člověka a nízkoenergetické sítě pro internet věcí (IoT).
- **Bluetooth (IEEE 802.15.1)**: Stojí na komunikaci Master/Slave v topologii point-to-multipoint v extrémně zaplněném pásmu 2,4 GHz. Pásmo je rozsekáno na 79 kanálů o šířce 1 MHz, mezi kterými obvody rychlostí blesku neustále přeskakují (Frequency Hopping), aby se vyhnuly rušení od Wi-Fi.
  - Verze Bluetooth prošly vývojem od pomalého 1.0 přes Bluetooth 3.0 HS s rychlostí 24 Mbps. Nejnovější energeticky nenáročný protokol **Bluetooth 5.3** dosahuje až 2 Mbps bez vybíjení baterií hodinek a fitness náramků.
  - Dosah definuje výkon antény: Class 1 má 100 mW s dosahem do 100 m, **Class 2** (mobily a notebooky) má 2,5 mW s limitem na **10 metrů** a Class 3 (1 mW) zvládne pouhé 1-2 metry u očních headsetů.
- **NFC (Near Field Communication)**: Technologie využívající magnetickou indukci (na frekvenci 13,56 MHz). Umožňuje bezdrátovou a silně zabezpečenou komunikaci na extrémně krátkou vzdálenost (do 10 cm). Nasazuje se výhradně pro bezkontaktní platby terminálem nebo okamžité párování hardwaru pouhým dotykem.
- **IrDA (Infrared Data Association)**: neaktivní předchůdce Bluetooth snesený do infračerveného spektra optiky (vlnová délka 875 nm). Extrémně nespolehlivý, protože vysílací a přijímací diody musely mít striktní optickou přímou viditelnost (Line-of-Sight) na pár centimetrů bez jediné překážky a limitem pouhých 4 Mbps.
- **ZigBee a Z-Wave**: Specifické průmyslové IoT standardy operující v pásmech od 868 MHz po 2,4 GHz. Nejsou určeny pro rychlý přenos dat, ale zato vynikají enormní energetickou stabilitou, nulovou odezvou a robustní **Mesh sítí** s automatickým směrováním (kde každá chytrá žárovka posílá data dalším zařízením v okolí). Dominantně nasazovány v automatizaci Smart Home budov.
- **LoRa (Long Range)**: Inženýrský protipól Bluetoothu a Wi-Fi. Bezdrátová technologie určená pro doručování malých objemů dat na velmi dlouhé vzdálenosti (až desítky kilometrů) za cenu drasticky nízké spotřeby energie. Ideální architektura pro sítě LPWAN obsluhující IoT senzory a monitorovací zařízení rozprostřená na rozsáhlých územích.

### Wireless MAN a WAN
rozsáhlý topologie postavené pro pokrytí celých měst nebo kontinentů poskytovateli Internetu.
- **WiMAX (IEEE 802.16)**: Metropolitní síť pro nasazení do vzdálenosti desítek kilometrů. Dokáže poskytnout symetrický výkon (Point-to-Multipoint) pro sídliště v "poslední míli" (tam, kam pro ISP už finančně nedává smysl natahovat pozemní optiku nebo rozkopávat ulice, ale domy přitom potřebují vysokorychlostní 100Mbit internet z jediného vysílače na věži). Využívá licencovaná i nelicencovaná spektra do astronomických 60 GHz.
- **Mobilní GSM / LTE / 5G**: Kontinentální WAN nasazení s rozsáhlý penetrací a frekvencemi přesunutými z rušených TV pásem (0,9 GHz po 2,4 GHz). Mobilní síť spojuje uživatele k buňkám (věžím) obrovských rozměrů a plní požadavky na datový internet v pohybu skrze standardizované asymetrické sítě poskytovatelů. Do WAN sítí spadají rovněž satelitní technologie zemské dráhy (např. systém Starlink s fázovanými anténami).