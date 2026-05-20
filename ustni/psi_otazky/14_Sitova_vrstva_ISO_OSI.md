---
created: 2026-05-02T19:01
updated: 2026-05-13T18:13
---

# 14. Síťová vrstva modelu ISO/OSI

## Účel a pozice v referenčním modelu

Síťová vrstva (Network Layer) tvoří třetí vrstvu modelu ISO/OSI. Její absolutní prioritou je logická adresace a zajištění end-to-end doručování datových bloků (paketů) mezi libovolnými uzly napříč různorodými propojenými sítěmi v celosvětovém měřítku. Na rozdíl od linkové vrstvy, která řeší pouze transport mezi fyzicky bezprostředně sousedícími zařízeními na jedné lokální síti, má síťová vrstva takzvaný globální přesah.

Z inženýrského hlediska poskytuje rodina protokolů IP (Internet Protocol) nespolehlivou (best-effort) a nespojovanou službu. To znamená, že síťová vrstva paket jednoduše odešle bez toho, aniž by předem navazovala s cílem relaci. Sama o sobě negarantuje doručení, nezachovává pořadí paketů a neposílá potvrzení o přijetí. Případnou ztrátu dat, jejich duplikaci nebo chyby ve flow-control přenechává k vyřešení až vyšší, transportní vrstvě (např. protokolu TCP).

## Logická adresace: IPv4 a IPv6

Zatímco MAC adresa identifikuje hardware, IP adresa na síťové vrstvě funguje jako logický lokátor, který přesně určuje geografickou i topologickou polohu zařízení v internetové síti. K doručení se momentálně využívají dvě historicky oddělené, ale koexistující verze protokolů (Dual-Stack provoz).

### Protokol IPv4

Dominantní, avšak kapacitně vyčerpaný standard. Využívá **32bitový adresní prostor**, což poskytuje přibližně 4,3 miliardy unikátních adres. Pro lidskou čitelnost se zapisuje dekadicky do čtyř oktetů oddělených tečkou (např. `192.168.1.10`).

- **Třídní adresování (Classful)**: Historické dělení adresního prostoru do tříd (A, B, C, D, E) podle hodnot prvních bitů. Třída A (maska /8) poskytovala sítě pro 16 milionů hostitelů, **Třída B** (maska /16) sítě pro 65 534 hostitelů (rozsah `128.0.0.0–191.255.255.255`), Třída C (maska /24) sítě pro 254 hostitelů. Třída D je vyhrazena pro Multicast (IP televize, směrovací protokoly) a Třída E pro experimenty. Toto hrubé dělení vedlo k rozsáhlému plýtvání adresami a dnes se již nevyužívá.
- **CIDR (Classless Inter-Domain Routing)**: Moderní beztřídní způsob alokace. K IP adrese se připojuje maska sítě ve formě prefixu (např. `/26`). Maska (zapsaná bitově pomocí jedniček a nul) matematicky přesně odděluje tu část IP adresy, která představuje Network ID (identifikátor sítě), od části, která tvoří Host ID (identifikátor konkrétního zařízení).
- **Veřejné a Privátní adresy**: Pro oddálení vyčerpání IPv4 byly určité bloky (např. `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) vyhrazeny jako privátní. Tyto adresy se nesmí vyskytnout ve veřejném internetu a volně se opakují uvnitř lokálních firemních a domácích LAN sítí. Výstup z LAN do Internetu se proto musí realizovat překladem přes **NAT**. Samostatnou kapitolou je **Loopback adresa** (`127.0.0.1`), která slouží k testování funkčnosti lokálního síťového rozhraní a navrácení paketů zpět sobě samému.

### Protokol IPv6

Standard navržený k řešení všech neduhů IPv4. Poskytuje rozsáhlý **128bitový adresní prostor(2001:0db8:85a3:0000:0000:8a2e:0370:7334)**, zapisovaný pro přehlednost hexadecimálně v osmi blocích (tzv. hextetech) oddělených dvojtečkou (např. `2001:0db8::8a2e:0370:7334`, kde blok `::` nahrazuje dlouhou řadu nul). IPv6 zcela odstraňuje nutnost překladu NAT (z důvodu ohromného počtu veřejných adres), nativně podporuje šifrování IPsec a odstraňuje adresování Broadcast, které neúměrně zatěžovalo uzly v LAN.

- **Global Unicast (2000::/3)**: Veřejně směrovatelné adresy globálního internetu.
- **Link-Local (FE80::/10)**: Adresy automaticky generované sítovými rozhraními pro lokální komunikaci v rámci jednoho L2 segmentu (bez nich by nefungoval např. zjišťovací protokol NDP). Router tyto pakety nikdy nepřepošle do jiné sítě.
- **Anycast a Multicast**: Adresy pro doručení dat nejbližšímu dostupnému serveru ve skupině (Anycast – využívané pro CDN sítě) nebo celé skupině naslouchajících (Multicast).

## Struktura paketu a fragmentace

Když síťová vrstva obdrží z transportní vrstvy datový segment (např. TCP segment), obalí ho vlastní IP hlavičkou, čímž vznikne **paket**. IPv4 i IPv6 hlavička musí obsahovat dvě nejdůležitější informace: zdrojovou IP adresu odesílatele a cílovou IP adresu příjemce.

Kromě toho hlavička obsahuje kritický mechanismus proti zacyklení paketů – **TTL (Time to Live)** u IPv4, potažmo **Hop Limit** u IPv6. Jde o 8bitové počítadlo, které každý router po cestě sníží o hodnotu 1. Pokud počítadlo klesne na 0, router paket okamžitě zahodí a odešle odesílateli chybové hlášení ICMP, čímž se zabrání tomu, aby pakety nekonečně rotovaly ve špatně nakonfigurovaných směrovacích smyčkách.

**Fragmentace**: Linková vrstva (typicky Ethernet) má fyzicky omezenou velikost rámečku, který dokáže přenést – tzv. **MTU (Maximum Transmission Unit)**, standardně 1500 bajtů. Pokud router obdrží z L3 paket, který je větší než MTU odchozího fyzického portu, musí síťová vrstva tento rozsáhlý paket rozsekat na menší kousky (fragmenty), odeslat je samostatně a cílové zařízení je z RAM paměti poskládá zpět. Protože fragmentace nesmírně zatěžuje procesory routerů a prodlužuje zpoždění, protokol IPv6 ji na routerech zakazuje úplně – rozdělení dat si musí ještě před odesláním vyřešit výhradně koncové zařízení (např. PC nebo server).

## Směrování (Routing) a aktivní prvky L3

Přesun dat mezi oddělenými sítěmi provádí **Směrovač (Router)**. Jde o L3 aktivní prvek, který analyzuje cílovou IP adresu v hlavičce příchozího paketu, podívá se do své paměti do tzv. **Směrovací tabulky (Routing Table)** a určí port, přes který paket nejrychleji dorazí do cíle.

- **Statické směrování**: Cesty do cílových sítí jsou do tabulky vloženy manuálně administrátorem. Neškáluje pro velké sítě, jelikož neumí automaticky reagovat na výpadek linky (failover). Součástí statického směrování je nastavení "Výchozí brány" (Default Gateway / 0.0.0.0), kam router odesílá všechny pakety, pro které nezná specifickou cestu v tabulce.
- **Dynamické směrování (IGP / EGP)**: Routery mezi sebou neustále komunikují a matematicky si vyměňují informace o topologii sítě. Pokud jeden kabel přestřižen, protokoly jako **OSPF** (vypočítává nejkratší cestu uvnitř jednoho podniku na základě rychlosti/ceny linky) nebo rozsáhlý internetový protokol **BGP** (vypočítává cestu přes různé autonomní systémy celého světa) během milisekund tabulku přemažou a tok dat přesměrují záložní trasou.

K moderním L3 zařízením patří také **Layer 3 Switch** (Multilayer Switch). Fyzicky vypadá jako gigabitový přepínač, avšak jeho ASIC čipy dokážou nativně přečíst IP hlavičku a směrovat rychlostí samotného hardwaru mezi mnoha firemními VLAN podsítěmi (tzv. Inter-VLAN routing), aniž by data musela opustit switch do pomalejšího externího routeru.

## Pomocné a diagnostické protokoly

Pro samotné doručování uživatelských dat využívá vrstva protokol IP. K jejímu funkčnímu provozu jsou však nezbytné doprovodné, řídicí a mapovací protokoly.

- **ARP (Address Resolution Protocol)**: Exaktní přemosťovač mezi L3 (IP) a L2 (MAC). Jakmile chce PC odeslat IP paket na konkrétní IP adresu ve své lokální síti, musí zjistit MAC adresu cílového hardwaru, aby vůbec mohlo sestavit ethernetový rámec. Z PC vyletí na všechny strany L2 Broadcast (Dotaz: "Kdo z vás má tuto IP adresu?"). Cílový stroj přijme dotaz a odpoví zpět svou MAC adresou unicastem. ARP dotazy omezuje IPv6 nahrazením za bezpečnější rodinu protokolů NDP (Neighbor Discovery Protocol).
- **ICMP (Internet Control Message Protocol)**: Služební protokol síťové vrstvy využívaný k diagnostice, zjišťování dostupnosti uzlů a chybovému reportingu. ICMP hlásí odesílateli například chyby "Cílová síť je nedostupná" (Destination Unreachable) nebo "Čas TTL vypršel" (Time Exceeded). Na ICMP zprávách Echo Request a Echo Reply (odpověď typu PING) je založena základní diagnostika spojení a oblíbený nástroj _Traceroute_ mapující cestu routerů.
- **NAT (Network Address Translation)**: Hardwarový proces operující typicky na hranici WAN a LAN na routerech nebo Firewallech. Přepisuje hlavičky IP paketů za letu a mění interní privátní IPv4 adresy počítačů (např. 192.168.x.x) na jednu jedinou veřejnou IPv4 adresu pronajatou od poskytovatele. Skrývá tak celou vnitřní síť a umožňuje překonat omezený počet volných IPv4 adres v Internetu. V kombinaci s TCP/UDP porty hovoříme o PAT (Port Address Translation).
