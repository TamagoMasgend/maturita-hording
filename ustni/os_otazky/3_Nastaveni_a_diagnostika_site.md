---
created: 2026-05-06T11:09
updated: 2026-05-20T12:58
---
# 3. Nastavení a diagnostika síťového prostředí v operačních systémech

Pro začlenění počítače do jakékoli moderní sítě založené na rodině protokolů TCP/IP je naprostou nutností správná konfigurace jeho síťového rozhraní (Network Interface – typicky Ethernet NIC nebo Wi-Fi adaptér). Operační systém musí hardwarovému rozhraní přidělit platnou logickou IP adresu, masku podsítě, adresu výchozí brány (Default Gateway) a adresy překladových DNS serverů. Bez těchto parametrů je komunikace mimo lokální L2 segment principiálně i matematicky nemožná.

## Konfigurace síťového interface
Oba dominantní operační systémy přistupují k aplikaci síťových parametrů odlišně, s rozdílnou filozofií administrace.

### Konfigurace v OS Windows
Windows se tradičně opírá o vizuální klikací rozhraní spojené se systémovým registrem, avšak pro inženýrskou správu a rozsáhlé skriptování poskytuje robustní terminálové nástroje.
- **GUI rozhraní**: Nastavení probíhá přes aplet `ncpa.cpl` (Síťová připojení), kde se ve vlastnostech konkrétního adaptéru přistupuje k protokolu "Protokol IP verze 4 (TCP/IPv4)".
- **Terminál (CMD / PowerShell)**:
  - Zjištění stavu a aktuální adresy z CMD: `ipconfig /all`
  - Statické nastavení přes PowerShell:
    `New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.1.10 -PrefixLength 24 -DefaultGateway 192.168.1.1`
  - Statické nastavení přes legacy CMD (`netsh`):
    - Nastavení IP, masky a brány: `netsh interface ipv4 set address name="Ethernet" static 192.168.1.10 255.255.255.0 192.168.1.1`
    - Nastavení primárního DNS serveru: `netsh interface ipv4 set dns name="Ethernet" static 8.8.8.8`

### Konfigurace v OS Linux
Linux ztělesňuje unixovou filozofii, kdy je veškerá konfigurace uložena primárně v textových souborech. Historické příkazy jako `ifconfig` byly dnes nahrazeny moderním balíkem `iproute2` (příkaz `ip`). V serverových prostředích závisí uložení perzistentní konfigurace na použitém správci sítě:
- **Dočasné zjištění a nastavení přes CLI**:
  - Vypsání všech rozhraní a jejich IP adres: `ip a` (alias pro `ip addr show`)
  - Dočasné přiřazení IP adresy adaptéru: `sudo ip addr add 192.168.1.10/24 dev eth0`
  - Nastavení výchozí brány (Default Gateway): `sudo ip route add default via 192.168.1.1 dev eth0`
  - Nastavení DNS překladače (dočasně zápisem do souboru): `echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf`
  - Nahození rozhraní do stavu UP: `sudo ip link set eth0 up`
- **Trvalé (Perzistentní) nastavení**:
  - Na serverových distribucích (jako Ubuntu) dominuje nástroj **Netplan**. Parametry se zapisují do deklarativního souboru ve formátu YAML (např. v `/etc/netplan/01-netcfg.yaml`) s důrazem na přesné odsazení mezerami.
    
    *Příklad konfigurace `/etc/netplan/01-netcfg.yaml` pro statickou IP*:
    ```yaml
    network:
      version: 2
      renderer: networkd
      ethernets:
        eth0:
          dhcp4: no
          addresses:
            - 192.168.1.10/24
          routes:
            - to: default
              via: 192.168.1.1
          nameservers:
            addresses: [8.8.8.8, 1.1.1.1]
    ```
    Konfigurace se do OS aplikuje příkazem `sudo netplan apply`.
  - V desktopových instalacích (nebo distribucích RHEL) se používá démon **NetworkManager** s nástrojem `nmcli`.

## Statické versus Dynamické nastavení sítě
Inženýrský přístup se rozchází u způsobu alokace IP parametrů. Správce sítě musí zvážit kompromis mezi administrativní zátěží a garancí fixních hodnot.

- **Statické nastavení (Manual Allocation)**: Administrátor natvrdo a ručně vepíše IP adresu, masku a bránu do operačního systému každého počítače. Výhodou je, že server nebo tiskárna bude mít vždy neměnnou, předvídatelnou IP adresu nutnou pro port-forwarding a firewallová pravidla. Zásadní nevýhodou je, že při síti s tisícem počítačů jde o administrativní zátěž náchylné na lidskou chybu, kdy hrozí zapsání duplicitní IP adresy a vznik IP konfliktu na síti.
- **Dynamické nastavení**: Konfigurace klientů je na straně operačního systému nastavena na pouhé zjišťování (Obtain automatically). Při zapnutí sítě vyšle OS speciální broadcast dotaz a konfiguraci si z centrálního serveru na určený časový úsek (Lease time) pouze propůjčí. Eliminuje se lidská chyba, masky i brány jsou doručeny centrálně a IP konflikty nevznikají. Vyžaduje to ale nasazení a dostupnost DHCP serveru.

## Protokol DHCP a proces DORA
**DHCP (Dynamic Host Configuration Protocol)** je služba aplikační vrstvy pracující v architektuře klient-server přes nespojovaný protokol UDP na portech 67 a 68. Proces přidělení IP adresy zapnutému stroji, který zatím nemá žádnou IP adresu, je tvořen čtyřmi kroky, tzv. **DORA**:

1. **D - Discover (Objev)**: Klientský počítač nemá IP adresu, a protože nezná topologii sítě, neví ani kde je DHCP server. Vygeneruje proto L2 a L3 Broadcast paket (`255.255.255.255`), do kterého zapíše svou vlastní hardwarovou MAC adresu. Paket se rozešle do celé lokální sítě: *"Jsem nový klient s touto MAC adresou, je tu nějaký DHCP server?"*
2. **O - Offer (Nabídka)**: Jakýkoliv aktivní DHCP server na tomto segmentu broadcast uslyší, zkontroluje svou databázi (Pool) a rezervuje pro onu MAC adresu jednu volnou IP. Následně odpoví zprávou Offer (často opět broadcastem z důvodu absence klientovy IP), která říká: *"Tady DHCP server, mohu ti pronajmout IP adresu 192.168.1.50."*
3. **R - Request (Požadavek)**: Klient obdrží nabídku (případně i více nabídek od různých DHCP serverů, z nichž si bere vždy tu první, která dorazí). Klient znovu odešle plošný Broadcast, v němž oficiálně žádá o pronájem té jedné konkrétní vybrané IP. Odesílá to broadcastem proto, aby ostatní případné DHCP servery věděly, že jejich nabídky byly klientem odmítnuty a mohou je vrátit zpět do svého poolu.
4. **A - Acknowledge (Potvrzení)**: Finální krok. Zvolený DHCP server odešle klientovi zprávu ACK. Zpráva obsahuje finální propůjčenou IP adresu, masku, adresu routeru (Gateway), servery DNS a tzv. Lease Time (dobu pronájmu). Klient si tyto hodnoty zapíše do OS a začne je používat. V polovině doby pronájmu pak žádá o prodloužení (Renew) už přes přímý Unicast na DHCP server.

- **Uvolnění a obnovení DHCP parametrů v CLI**:
  - **OS Windows**:
    - Uvolnění IP adresy: `ipconfig /release`
    - Vyžádání nové konfigurace od DHCP: `ipconfig /renew`
  - **OS Linux**:
    - Uvolnění IP adresy: `sudo dhclient -r`
    - Vyžádání nové konfigurace od DHCP: `sudo dhclient`

## Diagnostické síťové nástroje
Pro řešení problémů (Troubleshooting) integrují oba operační systémy fundamentální utility operující přes přímé rozhraní CLI. 

1. **ping** *(Oba systémy)*
   Základní utilita pro ověření end-to-end dosažitelnosti (reachability) cílového stroje v síti. Funguje zcela na bázi síťové L3 vrstvy (protokolu ICMP), kdy cíli posílá `Echo Request` a očekává odpověď `Echo Reply`. Poskytuje informaci o tom, zda server komunikuje, a měří hodnotu RTT (Round Trip Time - latenci v milisekundách) a procentuální ztrátovost paketů. V Linuxu běží ping donekonečna, ve Windows pošle standardně jen 4 pakety.
2. **tracert** *(Windows)* / **traceroute** *(Linux)*
   Užitečný nástroj k mapování celé geografické cesty routerů (hopů) z bodu A do bodu B. Využívá vlastnosti pole TTL (Time-to-Live) v IP hlavičce. Pošle první paket s TTL=1. První router po cestě (brána) sníží TTL na 0, paket zahodí a klientovi pošle chybovou ICMP zprávu `Time Exceeded`. Klient si z této odpovědi přečte IP adresu routeru, vypíše ji a pošle druhý paket s TTL=2. Tímto postupným zvyšováním TTL vytyčí kompletní cestu a určí, na kterém routeru konektivita selhává.
3. **ipconfig** *(Windows)* / **ip a** *(Linux)*
   Zjištění stavu síťových adaptérů v samotném počítači. Vrací přehled zapojených síťových karet, jejich MAC adresy (`ipconfig /all`), přidělené IP adresy, masky sítě, aktivní výchozí brány a servery DNS.
4. **nslookup** *(Windows/Linux)* / **dig** *(moderní Linux)*
   Nástroj pro přímé dotazování na DNS servery. Prověřuje, zda systém umí překládat doménová jména na logické IP adresy (záznamy A, AAAA, MX apod.) a zda je překladač správně nastaven.
5. **netstat** *(Windows)* / **ss** *(moderní Linux)*
   Hloubkový monitoring transportní a aplikační vrstvy. Zobrazuje veškerá momentálně otevřená a naslouchající síťová spojení (sokety) napříč procesy na lokálním stroji, včetně příslušných TCP a UDP portů (stavy `ESTABLISHED` nebo `LISTEN`).
6. **arp -a** *(Oba systémy)*
   Zobrazení vyrovnávací paměti (cache) protokolu ARP (Address Resolution Protocol). Zobrazuje aktuální tabulku mapování IP adres (L3) na fyzické MAC adresy (L2) síťových karet v lokální síti.
7. **route print** *(Windows)* / **ip route** *(Linux)*
   Zobrazení a správa směrovací (routing) tabulky v operačním systému. Určuje, přes které síťové rozhraní a brány budou odesílány pakety pro jednotlivé podsítě a internet.