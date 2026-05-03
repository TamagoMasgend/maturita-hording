# 15. Adresace a směrování v sítích

## Princip a význam směrování (Routing)
Směrování je klíčový proces síťové vrstvy (L3), jehož absolutním cílem je nalezení nejoptimálnější, nejrychlejší nebo nákladově nejefektivnější trasy pro přesun datových paketů od odesílatele k příjemci přes komplexní topologii pospojovaných sítí. Zatímco adresace zajišťuje unikátní logickou identifikaci každého uzlu, směrování se stará o samotnou navigaci těmito uzly.

Na linkové vrstvě (L2) dochází k **přímému směrování (přepínání)**, které zprostředkovává switch pouze v rámci jedné lokální sítě pomocí MAC adres a protokolu ARP. Pokud však odesílatel logickou operací AND (aplikací masky podsítě na cílovou IP adresu) zjistí, že cíl se nachází v cizí síti, předá rámec své *výchozí bráně* (Default Gateway). Tím začíná **nepřímé směrování**, jehož režii přebírá router (směrovač). Ten pracuje primárně s IP adresami, čte z paketu cílovou IP adresu a nahlíží do své směrovací tabulky, kudy paket poslat dál.

## Mechanismy adresace a maskování podsítí (Subnetting)
Efektivní směrování vyžaduje precizní matematické rozdělení IP adres na síťovou část (Network ID) a hostitelskou část (Host ID). K tomu slouží maska podsítě.

- **VLSM (Variable Length Subnet Mask)**: Inženýrská technika, která umožňuje v jedné velké síti aplikovat různě dlouhé masky (např. `/26`, `/30`). Zabraňuje se tím plýtvání adresami – zatímco pro LAN se 60 uživateli se použije maska `/26` (poskytující 62 adres), pro point-to-point spoj mezi dvěma routery stačí maska `/30` (pouhé 2 využitelné adresy).
- **CIDR (Classless Inter-Domain Routing)**: Moderní beztřídní způsob adresování, který nahradil staré rigidní třídy (A, B, C). CIDR umožňuje tzv. agregaci cest (Route Summarization / Supernetting), kdy lze několik menších spojitých podsítí (např. čtyři `/24` sítě) shrnout do jednoho většího záznamu ve směrovací tabulce (např. `/22`). Tím se drasticky zmenšuje velikost směrovacích tabulek na páteřních internetových routerech.
- U každé vyčleněné IPv4 sítě je absolutní nutností vyhradit dvě adresy, které nelze přiřadit koncovému zařízení: **Adresa sítě** (všechny bity hostitelské části jsou 0) sloužící pro identifikaci bloku v routovací tabulce a **Broadcast adresa** (všechny bity hostitelské části jsou 1), určená pro plošné oslovení všech uzlů v dané podsíti.

## Směrovací tabulka a výpočet Metriky
Jádrem rozhodovacího procesu každého routeru je směrovací tabulka (Routing Table). Ta pro každou cílovou síť eviduje masku, výstupní rozhraní routeru, IP adresu dalšího skoku (Next-hop) a číselné ohodnocení kvality dané cesty, zvané **metrika**.

Pokud se do stejné cílové sítě nabízí více nezávislých cest, router si vždy vybere tu s **nejnižší (nejlepší) metrikou**. Různé směrovací protokoly však metriku počítají odlišně:
- Může to být prostý **Hop Count** (počet překonaných routerů na trase).
- Komplexní algoritmy zohledňují **Šířku pásma (Bandwidth)**, průměrné **Zpoždění (Delay)**, aktuální **Zatížení (Load)** nebo **Spolehlivost linky**.
- Pokud router získá o jedné síti informaci z vícero různých směrovacích protokolů (např. z OSPF i z RIP), použije hodnotu tzv. **Administrativní vzdálenosti (Administrative Distance)**, což je předem dané číslo určující "důvěryhodnost" daného protokolu (např. přímo připojená síť = 0, statická cesta = 1, OSPF = 110). Router upřednostní protokol s nejnižší administrativní vzdáleností.

## Evoluce přístupů k nepřímému směrování
Historicky prošly algoritmy k doručování paketů bouřlivým vývojem od neefektivních po současné optimalizované modely.

1. **Izolované a náhodné přístupy**: Zastaralé, primitivní metody, u nichž router nijak nekomunikoval s okolím. Patřilo sem *Záplavové směrování* (paket byl okopírován na všechny porty kromě příchozího – vysoce redundantní, ale způsobující zahlcení), *Náhodné směrování* a *Metoda horké brambory*, kdy se paket okamžitě odeslal nejméně vytíženou frontou pryč, jen aby se router zbavil zátěže.
2. **Statické směrování**: Cesty do cílových sítí vkládá do routeru administrátor zcela manuálně (příkazem). Je extrémně bezpečné, nespotřebovává výkon CPU ani šířku pásma pro provoz protokolu, ale postrádá schopnost jakékoliv konvergence – pokud fyzická linka vypadne, router trasu nepřepočítá a data se začnou zahazovat. Typicky se omezuje pouze na statické nastavení *Default Gateway* na okrajích sítí.
3. **Dynamické směrování**: Distribuovaný a inteligentní přístup. Routery v síti běží pod hlavičkou společného směrovacího protokolu. Neustále si automaticky vyměňují topologické informace a samy přepočítávají matematicky nejkratší cesty k cílům.

## Dynamické směrovací algoritmy a protokoly
Dynamické protokoly se dělí do tří inženýrských skupin (rodin) podle matematického algoritmu, kterým zpracovávají informace o topologii sítě. Od toho se odvíjí jejich rychlost **konvergence** (doba, za kterou všechny routery v síti zaregistrují změnu a aktualizují své tabulky).

### 1. Distance Vector (Metoda vektoru vzdáleností)
Routery si nevytvářejí mapu celé sítě. Své znalosti zakládají pouze na tom, co jim sdělí jejich bezprostřední sousedé. Pravidelně sousedům v periodických intervalech posílají obsah celé své vlastní směrovací tabulky (vektorů). Algoritmem je typicky **Bellman-Ford**.
- **RIP (Routing Information Protocol)**: Jeden z nejstarších protokolů. Jako jedinou metriku používá prostý počet skoků (Hop Count) s tvrdým limitem max. 15 skoků (16. skok je považován za nedostupnou síť). Větší sítě tedy neobslouží. Jeho konvergence je extrémně pomalá a trpí vznikem tzv. směrovacích smyček. K potlačení smyček musí využívat techniky jako *Split Horizon* (router nikdy nepošle aktualizaci o síti zpět na rozhraní, ze kterého se o ní předtím dozvěděl). Novější RIPv2 a RIPng přidaly podporu pro VLSM, masky a IPv6, avšak protokol jako takový je technologicky obsolentní.
- **EIGRP (Enhanced Interior Gateway Routing Protocol)**: Vysoce pokročilý protokol firmy Cisco. Na rozdíl od RIPu má rychlou konvergenci, odesílá pouze inkrementální (částečné) updaty a jeho komplexní kompozitní metrika se počítá nejen ze zpoždění, ale především z minimální šířky pásma (Bandwidth) po celé trase.

### 2. Link-State (Metoda stavu linek)
Protiklad k Distance Vectoru. Každý router v síti aktivně mapuje stav svých vlastních linek a rozesílá tuto malou informaci pomocí LSA paketů všem ostatním routerům. Každý router si tak z těchto kousků sám v paměti složí kompletní a identickou mapu celé topologie sítě a následně na ni aplikuje **Dijkstrův algoritmus** pro nalezení nejkratší cesty ke každému bodu.
- **OSPF (Open Shortest Path First)**: Nejpoužívanější průmyslový standard pro vnitropodnikové sítě (Interior Gateway Protocol). Jeho fungování probíhá ve třech krocích: navázání stabilního sousedství pomocí Hello paketů, zaplavení sítě změnami stavu (LSA) a konečný výpočet nejkratší cesty podle metriky, která vychází z nominální propustnosti dané linky (Cost – typicky `100 000 000 / Bandwidth`). Podporuje rozdělení sítě do hierarchických zón (Area 0), což drasticky snižuje paměťovou zátěž routerů. Konvergence je v řádech milisekund.

### 3. Path-Vector (Protokol vektoru cesty)
Kombinuje a překonává obě předchozí skupiny. Není určen k hledání nejkratší trasy v lokální firmě, ale k prosazování administrativních politik (např. "přes síť tohoto operátora data posílat nechceme, je drahý").
- **BGP (Border Gateway Protocol)**: Jediný zástupce vnějších směrovacích protokolů (Exterior Gateway Protocol). Je to "protokol, který pohání samotný Internet". Internet nedělí na lokální sítě, ale na gigantické **Autonomní systémy (AS)** přidělené korporacím a ISP (mají unikátní AS Number). Směrovací tabulky BGP na páteřních kořenových routerech dnes obsahují statisíce záznamů. Metrika zde není primárním určovatelem trasy; směrování závisí na sérii atributů definujících komerční, geografické a bezpečnostní politiky dohodnuté mezi telekomunikačními operátory.