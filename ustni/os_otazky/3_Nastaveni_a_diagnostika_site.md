# 3. Nastavení a diagnostika síťového prostředí v operačních systémech

Pro začlenění počítače do jakékoli moderní sítě založené na rodině protokolů TCP/IP je naprostou nutností správná konfigurace jeho síťového rozhraní (Network Interface – typicky Ethernet NIC nebo Wi-Fi adaptér). Operační systém musí hardwarovému rozhraní přidělit platnou logickou IP adresu, masku podsítě, adresu výchozí brány (Default Gateway) a adresy překladových DNS serverů. Bez těchto parametrů je komunikace mimo lokální L2 segment principiálně i matematicky nemožná.

## Konfigurace síťového interface
Oba dominantní operační systémy přistupují k aplikaci síťových parametrů odlišně, s rozdílnou filozofií administrace.

### Konfigurace v OS Windows
Windows se tradičně opírá o vizuální klikací rozhraní spojené se systémovým registrem, avšak pro inženýrskou správu a rozsáhlý skriptování poskytuje robustní terminálové nástroje.
- **GUI rozhraní**: Nastavení probíhá přes aplet `ncpa.cpl` (Síťová připojení), kde se ve vlastnostech konkrétního adaptéru přistupuje k protokolu "Protokol IP verze 4 (TCP/IPv4)".
- **Terminál (CMD / PowerShell)**:
  - Zjištění stavu a aktuální adresy z CMD: `ipconfig /all`
  - V PowerShellu lze statickou IP aplikovat moderními commandlety: 
    `New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.10 -PrefixLength 24 -DefaultGateway 192.168.1.1`

### Konfigurace v OS Linux
Linux ztělesňuje unixovou filozofii, kdy je veškerá konfigurace uložena primárně v textových souborech. Historické příkazy jako `ifconfig` byly dnes naprosto odstraněny a nahrazeny balíkem `iproute2` (příkaz `ip`). V serverových prostředích závisí uložení perzistentní konfigurace na použitém správci sítě:
- **Dočasné zjištění a nastavení přes CLI**:
  - Vypsání všech rozhraní a jejich IP adres: `ip a` (alias pro `ip addr show`)
  - Dočasné přiřazení IP adresy adaptéru (do restartu): `sudo ip addr add 192.168.1.10/24 dev eth0`
  - Nahození rozhraní do stavu UP: `sudo ip link set eth0 up`
- **Trvalé (Perzistentní) nastavení**:
  - Na serverových distribucích (jako Ubuntu) dominuje nástroj **Netplan**. Parametry se zapisují do deklarativního souboru ve formátu YAML (např. v `/etc/netplan/01-netcfg.yaml`) s důrazem na přesné odsazení mezerami. Konfigurace se do OS aplikuje příkazem `sudo netplan apply`.
  - V desktopových instalacích (nebo distribucích RHEL) se hojně uplatňuje démon **NetworkManager** s nástrojem `nmcli`.

## Statické versus Dynamické nastavení sítě
Inženýrský přístup se rozchází u způsobu alokace IP parametrů. Správce sítě musí zvážit kompromis mezi administrativní zátěží a garancí fixních hodnot.

- **Statické nastavení (Manual Allocation)**: Administrátor natvrdo a ručně vepíše IP adresu, masku a bránu do operačního systému každého počítače. Výhodou je, že server nebo tiskárna bude mít vždy neměnnou, předvídatelnou IP adresu nutnou pro port-forwarding a firewallová pravidla. Zásadní nevýhodou je, že při síti s tisícem počítačů jde o administrativní zátěž náchylné na lidskou chybu, kdy hrozí zapsání duplicitní IP adresy a vznik IP konfliktu na síti.
- **Dynamické nastavení**: Konfigurace klientů je na straně operačního systému nastavena na pouhé zjišťování (Obtain automatically). Při zapnutí sítě vyšle OS speciální broadcast dotaz a konfiguraci si z centrálního serveru na určený časový úsek (Lease time) pouze propůjčí. Eliminuje se lidská chyba, masky i brány jsou doručeny centrálně a IP konflikty nevznikají. Vyžaduje to ale nasazení a dostupnost DHCP serveru.

## Protokol DHCP a proces DORA
**DHCP (Dynamic Host Configuration Protocol)** je služba aplikační vrstvy pracující v architektuře klient-server přes nespojovaný protokol UDP na portech 67 a 68. Proces přidělení IP adresy zapnutému stroji, který zatím nemá žádnou IP adresu, je z inženýrského pohledu užitečný proces tvořený čtyřmi přesnými kroky, tzv. **DORA**:

1. **D - Discover (Objev)**: Klientský počítač nemá IP adresu, a protože nezná topologii sítě, neví ani kde je DHCP server. Vygeneruje proto L2 a L3 Broadcast paket (`255.255.255.255`), do kterého zapíše svou vlastní hardwarovou MAC adresu. Paket se rozeřve do celé lokální sítě: *"Jsem nový klient s touto MAC adresou, je tu nějaký DHCP server?"*
2. **O - Offer (Nabídka)**: Jakýkoliv aktivní DHCP server na tomto segmentu broadcast uslyší, zkontroluje svou databázi (Pool) a rezervuje pro onu MAC adresu jednu volnou IP. Následně odpoví zprávou Offer (často opět broadcastem z důvodu absence klientovy IP), která říká: *"Tady DHCP server, mohu ti na 24 hodin pronajmout IP adresu 192.168.1.50."*
3. **R - Request (Požadavek)**: Klient obdrží nabídku (případně i více nabídek od různých DHCP serverů, z nichž si bere vždy tu první, která dorazí). Klient znovu odešle plošný Broadcast, v němž oficiálně žádá o pronájem té jedné konkrétní vybrané IP. Odesílá to broadcastem proto, aby ostatní případné DHCP servery věděly, že jejich nabídky byly klientem odmítnuty a mohou si adresy vrátit do svého volného Poolu.
4. **A - Acknowledge (Potvrzení)**: Finální krok. Zvolený DHCP server odešle klientovi zprávu ACK. Zpráva obsahuje finální propůjčenou IP adresu, masku, adresu routeru (Gateway), servery DNS a tzv. Lease Time (dobu pronájmu). Klient si tyto hodnoty zapíše do operačního systému a může začít komunikovat. V polovině doby pronájmu pak žádá o prodloužení už jen přes přímý Unicast na DHCP server.

## Diagnostické síťové nástroje
Pro řešení problémů (Troubleshooting) integrují oba operační systémy fundamentální inženýrské utility operující přes přímé rozhraní CLI. 

1. **ping** *(Oba systémy)*
   Základní utilita pro ověření end-to-end dosažitelnosti (reachability) cílového stroje v síti. Funguje zcela na bázi síťové L3 vrstvy (protokolu ICMP), kdy cíli posílá `Echo Request` a očekává odpověď `Echo Reply`. Poskytuje inženýrovi informaci nejen o tom, zda server žije, ale měří kritickou hodnotu RTT (Round Trip Time - latenci v milisekundách) a procentuální ztrátovost paketů po cestě. V Linuxu běží ping donekonečna, ve Windows pošle standardně jen 4 pakety.
2. **tracert** *(Windows)* / **traceroute** *(Linux)*
   užitečný nástroj k mapování celé geografické cesty routerů (hopů) z bodu A do bodu B. Využívá vlastnosti pole TTL (Time-to-Live) v IP hlavičce. Pošle první paket s TTL=1. První router po cestě (výchozí brána) sníží TTL na 0, paket zničí a klientovi pošle chybovou ICMP zprávu `Time Exceeded`. Klient si z této odpovědi přečte IP adresu tohoto routeru, vypíše ji na obrazovku a hned pošle druhý paket s TTL=2. Tímto postupným zahazováním paketů a zvyšováním TTL vytyčí kompletní cestu a pomůže administrátorovi určit, na kterém routeru v internetu po cestě konektivita fyzicky selhává.
3. **ipconfig** *(Windows)* / **ip a** *(Linux)*
   Zjištění aktuálního stavu síťových adaptérů v samotném počítači. Vrací přehled zapojených síťových karet, jejich MAC adresy (`ipconfig /all`), přidělené IPv4 a IPv6 adresy, masky sítě, aktivní výchozí brány a servery DNS. Ve Windows slouží `ipconfig /release` a `/renew` k vyžádání nového DORA procesu s DHCP serverem.
4. **nslookup** *(Windows/Linux)* / **dig** *(moderní Linux)*
   Nástroj pro přímé dotazování na jmenné DNS servery. Prověřuje, zda systém umí korektně přeložit doménové jméno `www.seznam.cz` na logickou IP adresu, odhaluje A/AAAA nebo MX (Mail Exchange) záznamy domény a případnou nefunkčnost překladače při konfiguraci klienta.
5. **netstat** *(Windows)* / **ss** *(moderní Linux)*
   Hloubkový monitoring z transportní a aplikační vrstvy. Zobrazuje veškerá momentálně otevřená a naslouchající síťová spojení (sokety) napříč procesy na lokálním stroji, včetně příslušných TCP a UDP portů (např. porty ve stavu `ESTABLISHED` nebo `LISTEN`). Ideální nástroj k odhalení kompromitovaných procesů nebo skrytých backdoorů otevřených do sítě.