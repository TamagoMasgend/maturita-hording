# 18. Síťové prvky modelu ISO/OSI a strukturovaná kabeláž

V oblastech podnikového i domácího IT tvoří síťová infrastruktura nedílný celek dvou světů: **aktivních síťových prvků** (elektronika řídící tok dat podle vrstev OSI modelu) a **pasivní strukturované kabeláže** (standardizované fyzické médium umožňující transport).

## Aktivní síťové prvky
Každý síťový prvek operuje na určité vrstvě modelu ISO/OSI a v závislosti na této vrstvě disponuje "inteligencí" k analýze hlaviček datových PDU. 

### Prvky fyzické vrstvy (L1)
Tato zařízení nemají ani IP, ani MAC adresu. Nerozumějí struktuře dat, pouze slepě elektricky nebo opticky replikují zachycený signál, aby prodloužily jeho dosah.
- **Opakovač (Repeater)**: Zařízení s jedním vstupem a výstupem. Přijme zesláblý signál, zrekonstruuje jeho přechodové hrany, zesílí jej a přepošle dál na bezdrátový nebo kabelový segment.
- **Rozbočovač (Hub)**: Architektonicky víceportový opakovač plnící roli středu historické hvězdicové topologie. Elektrický puls, který přijde na libovolný port, je bit po bitu okamžitě replikován a odeslán na všechny ostatní aktivní porty. Tím Hub vytváří jedinou, společnou a rozsáhlý **kolizní doménu**. V síti se musí využívat CSMA/CD, propustnost klesá a při komunikaci se zařízení vzájemně přehlušují. V moderních sítích byl zcela nahrazen přepínači.
- **Modem (Modulátor-Demodulátor)**: Koncové zařízení operátora. Převádí digitální data z počítačové sítě na analogový signál vhodný pro transport přes telekomunikační síť (xDSL kabel, optické linky nebo koaxiál CATV) a naopak.

### Prvky linkové vrstvy (L2)
Zařízení na této vrstvě již rozeznávají rámce a směrují výhradně podle fyzických **MAC adres**.
- **Síťová karta (NIC - Network Interface Card)**: Koncový bod počítače nesoucí továrně vypálenou unikátní 48bitovou MAC adresu. Zprostředkovává převod logických dat od procesoru do L2 rámců a fyzických pulzů média (integruje Ethernet/Wi-Fi standardy).
- **Most (Bridge)**: Dvouportový L2 prvek, který kdysi spojoval dvě odlišné segmenty (např. odděloval Ethernet a Token Ring), čímž lokálně izoloval kolizní domény.
- **Přepínač (Switch)**: Ultimátní náhrada za hub a jádro dnešních lokálních sítí (LAN). Obsahuje specializované ASIC čipy, které čtou zdrojové MAC adresy z příchozích rámců a dynamicky se učí "MAC tabulku" (CAM tabulka), kde mapují stroje na fyzické porty (Fast Ethernet porty `FA0/1` nebo Gigabit `Gi0/1`). Pokud cíl rámce znají, odešlou jej striktně pouze na tento cílový port. Pokud cíl neznají nebo jde o zprávu zacílenou na Broadcast (`FF:FF:FF:FF:FF:FF`), rozešlou rámec všude. **Switch na každém svém portu odděluje kolizní domény**.
  - **Režimy přeposílání rámců ve switchi**:
    1. *Cut-through*: Switch načte jen prvních 6 bajtů s cílovou MAC a okamžitě přeposílá. Minimální zpoždění, ale pošle dál i poškozené rámce (nemá prostor ověřit chybový kontrolní součet na konci).
    2. *Fragment-Free*: Switch čeká na přečtení 64 bajtů, čímž eliminuje rozsáhlý přeposílání úlomků z raných kolizí.
    3. *Store-and-Forward*: Průmyslový standard. Switch načte kompletní rámec do paměti, pečlivě přepočítá CRC součet a pokud nesedí, rámec na místě zlikviduje. Pokud je bez chyby, odešle ho na cíl.

### Prvky síťové vrstvy (L3) a vyšších vrstev
Tato zařízení otevírají IP hlavičky a řídí celosvětovou logiku transportu.
- **Směrovač (Router)**: Propojuje odlišné IP podsítě (např. lokální firemní síť s Internetem). Nedívá se na MAC adresy, zajímá ho cílová IPv4/IPv6 adresa v paketu, kterou porovnává se svou směrovací tabulkou, aby našel optimální a nejkratší cestu ven. Zásadní inženýrská pravda zní: **Router izoluje (odděluje) broadcastové domény**. Pokud nějaký uzel zakřičí L2 broadcast (např. ARP dotaz), uslyší to všichni na switchi, ale router tento křik na svém rozhraní ukončí a do sousední sítě ho nepustí, čímž brání zahlcení internetu.
- **L3 Switch (Multilayer Switch)**: Architektura spojující fyzický vysokorychlostní hardware switche se softwarovými L3 moduly (např. Cisco Catalyst). Umí přímo přiřazovat a řešit adresy L3 podsítí v rámci podniku a provádět rozsáhlý rychlý (hardwarový) Inter-VLAN routing, aniž by musel pakety posílat ven na klasický jednoruký router.
- **Firewall**: Bezpečnostní prvek (softwarový či specializovaný hardwarový), zkoumající komunikaci na vrstvách L3 (IP) až L7 (detekce konkrétních aplikací a URL). Analyzuje packety podle striktních přístupových politik (ACL), blokuje nebezpečné porty a zajišťuje NAT/PAT překlad z vnitřních privátních adres na veřejný internet.

### Schematické značky Cisco
Vzhledem k dominantnímu postavení společnosti Cisco na trhu se pro vizualizaci síťových topologií celosvětově vžila standardizovaná sada inženýrských symbolů (tzv. Cisco Network Topology Icons):
- **Směrovač (Router)**: Reprezentován ikonou **kruhu se čtyřmi šipkami** (směřujícími dovnitř a ven), symbolizující rozřazování provozu do různých cest.
- **Přepínač (Switch)**: Vykreslován jako **čtvercová ikona (nebo kvádr) se čtyřmi šipkami**, jež jsou uspořádány paralelně (často dvěma směry), čímž značí přímé přepínání portů uvnitř jedné sítě. L3 Switch je pak doplněn o hvězdici uvnitř.
- **Rozbočovač (Hub)**: Kreslen jako jednoduchý obdélník obsahující **pouze jedinou obousměrnou šipku**, indikující jednu společnou kolizní doménu.
- **Firewall**: Tradičně se zakresluje ikonou **cihlové zdi**, do které naráží oheň, případně jako zeď oddělující lokální síť od mraku internetu.

## Pasivní síťová infrastruktura a strukturovaná kabeláž
Kvalitní elektronika nemůže fungovat na poškozeném kabelu. Proto v komerčních budovách existuje strukturovaná kabeláž – předem definovaný, standardizovaný a flexibilní univerzální systém rozvodů (podle norem jako ISO/IEC 11801), který skryje vedení do stěn, podhledů a podlah. Umožňuje bez stavebních zásahů libovolně migrovat uživatele, měnit síťovou topologii nebo snadno diagnostikovat chyby.

### Komponenty strukturované kabeláže
- **Média (Instalační kabely)**: Měděné kroucené dvojlinky (Twisted Pair) s plným drátem instalované napevno do zdí, nebo mnohovidová a jednovidová optická vlákna pokládaná do páteřních spojů. Měď je rozdělena na výkonové kategorie – v podnicích dominuje Cat5e/Cat6 (limit 1 Gbps na 100m) a Cat6A s podporou 10 Gbps na 100m bez přeslechů v pásmu 500 MHz.
- **Datové zásuvky**: Koncové účastnické nástěnné panely (zásuvky na RJ-45).
- **Propojovací panely (Patch panely)**: Mechanická křižovatka všech zdí-vedených kabelů ústící v serverovně. Zezadu se pevný kabel zařízne přes zarezávací konektory (Krone/110) a zepředu vystupuje klasickou RJ-45 zdířkou, která je jednoznačně očíslována podle kanceláře.
- **Propojovací kabely (Patch kabely)**: Měkké (lanko), krátké měděné či optické šňůry určené k propojení portu na Switchi s portem na Patch panelu, případně od uživatele ze zdi do síťové karty PC.

### Telekominikační rozvaděče (Racky)
Infrastruktura se pro ochranu a chlazení neskládá na stoly, nýbrž montuje do průmyslových skříní. Pevným celosvětovým standardem je **19" Rack** (šířka upínacích lišt pro šrouby činí přesně 19 palců, cca 50 cm) sestávající z hliníkových či ocelových rámů (někdy s prosklenými dveřmi, nebo otevřený). Menší variantou do malých firem je Half-rack (šířka 10").

Výška racků a síťových komponent (switchů, serverů, UPS) se z důvodu kompatibility udává exaktně v Rackových jednotkách označovaných jako **U (Unit)**.
- **1 U** = 1,75 palce (přesně **44,45 mm**).
- Klasický podlahový stojanový rack mívá standardní výšku 42U. 
- Menší nástěnné racky pro lokální patrové rozvody mívají 6U, 9U nebo 12U.

### Topologie páteřních spojů
Kabeláž rozsáhlý firmy nelze zapojit "jak to vyjde", ale řídí se striktní logikou, typicky postavenou na návrhu Campus sítí s využitím zborcených hvězd.

- **Zborcená páteř (Collapsed Backbone)**: Nejpoužívanější moderní topologie v LAN sítích. Všechny periferní (patrové) switche se sbíhají napřímo svými křídly centrálně do jediného extrémně výkonného, zálohovaného "Core" serveru/routeru uprostřed sítě (typicky spojeno optikou 10G/40G). Datová komunikace např. mezi třetím a druhým patrem neskáče horizontálně, ale letí do jádra a hned zase z jádra ven. Výpadek lokální patrové větve nijak neovlivní zbytek podniku, ale centrální jádro vyžaduje rozsáhlý finanční prostředky a plnou redundanci.
- **Distribuovaná páteř**: V podstatě rozsáhlý kruhová nebo sběrnicová dálková linka (např. technologie FDDI, dnes již zastaralá), do které se jednotlivé budovy napichují na trase. Je levnější na délku kabeláže, ale neúměrně se zvyšuje riziko rozsáhlý odpojení při přerušení kabelu. V moderním IT nahrazena prstencovými metropolitními Ethernet spojemi s protokolem STP pro zálohu trasy.