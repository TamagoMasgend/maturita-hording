# 13. Linková vrstva modelu ISO/OSI

## Účel a pozice v referenčním modelu
Linková vrstva (Data Link Layer) je v pořadí druhou vrstvou referenčního modelu ISO/OSI. Nachází se přesně na rozhraní mezi ryze hardwarovou fyzickou vrstvou (zpracovávající bity a volty) a softwarovou síťovou vrstvou (zpracovávající IP pakety a globální směrování). Zatímco IP adresa zaručuje doručení dat napříč celosvětovým Internetem, linková vrstva má za absolutní cíl bezpečný a řízený transport dat výhradně mezi dvěma zařízeními v rámci jedné lokální sítě (na stejném broadcastovém segmentu), případně mezi dvěma routery na jednom point-to-point spoji.

Úkolem L2 je organizovat syrový proud nul a jedniček z fyzické vrstvy do logických ohraničených celků zvaných **rámce (frames)**. Linková vrstva jako první zavádí mechanismy adresování odesílatele a příjemce, řeší arbitráž (kdo smí na sdíleném médiu právě teď vysílat) a implementuje základní detekci chyb, aby se předešlo zpracování poškozených dat.

## Rozdělení na podvrstvy LLC a MAC
Aby bylo možné používat stejné protokoly síťové vrstvy (např. IPv4 nebo IPv6) nad zcela odlišnými fyzickými médii (metalický Ethernet, bezdrátová Wi-Fi, optický GPON), inženýři IEEE 802 rozdělili linkovou vrstvu na dvě nezávislé podvrstvy.

1. **LLC (Logical Link Control - 802.2)**: Horní podvrstva, která přímo komunikuje se síťovou vrstvou. Funguje jako softwarový převodník – multiplexer. LLC hlavička obsahuje identifikaci toho, jaký protokol třetí vrstvy (IPv4, IPv6, ARP atd.) je v datové části rámce zapouzdřen. Díky LLC může operační systém obsluhovat různé hardwarové síťové karty jednotným způsobem pomocí ovladačů (drivers).
2. **MAC (Media Access Control)**: Spodní podvrstva úzce spjatá s konkrétním hardwarem a fyzickým médiem. Její definice je pevně vyleptána do křemíku síťových karet (NIC) a bezdrátových adaptérů. Úkolem MAC podvrstvy je přidat zdrojovou a cílovou fyzickou adresu, sestavit tělo rámce a řídit **přístupové metody**, tedy rozhodovat o tom, v jaký mikrosekundový okamžik smí hardware vyslat elektrický či optický signál na sdílené kabelové nebo rádiové médium, aby nedošlo ke kolizi.

## Hardwarová adresace (MAC adresy)
Komunikace na linkové vrstvě striktně spoléhá na plochý adresní model bez jakékoli geografické hierarchie (na rozdíl od IP adres). Identifikátorem uzlu je **MAC adresa** (Media Access Control Address) – globálně unikátní 48bitové (6bajtové) číslo vypálené do ROM paměti síťového rozhraní při výrobě.

MAC adresa se zapisuje hexadecimálně (např. `00:1A:2B:3C:4D:5E`). Inženýrsky se dělí na dvě přesné poloviny:
- Prvních 24 bitů představuje **OUI (Organizationally Unique Identifier)**, což je kód jednoznačně přidělený konkrétnímu výrobci hardwaru (např. Cisco, Intel, Apple) organizací IEEE.
- Zbylých 24 bitů (tzv. NIC Specific) představuje sériové číslo přidělené samotným výrobcem, čímž je zaručena absolutní unikátnost každé vyrobené karty na světě.

Linková vrstva definuje tři typy L2 adres pro odlišný rozptyl komunikace:
- **Unicast**: Adresa konkrétního jednoho zařízení (komunikace jedna ku jedné).
- **Multicast**: Speciální adresa vyhrazená pro logickou skupinu přijímačů. Rámec zpracují pouze ty síťové karty, které k této skupině naslouchají.
- **Broadcast**: Všesměrová adresa (všechny bity nastaveny na jedničky: `FF:FF:FF:FF:FF:FF`). Rámec je hardwarově doručen a přerušením procesoru zpracován každým jedním uzlem v dané lokální síti. Používá se například pro objevovací protokoly jako ARP.

## Metody přístupu ke sdílenému médiu (Arbitráž)
Způsob, jakým MAC podvrstva rozhoduje, kdo smí aktuálně vysílat, je nejkritičtějším faktorem výkonnosti sdílených sítí. Algoritmy dělíme na deterministické a stochastické.

- **Deterministické metody (Token Passing)**: Algoritmus garantuje, že se k vysílání dostane každý uzel a že nikdy nenastane kolize. Uzly si předávají logické oprávnění (Token). Vysílat smí pouze ten, kdo právě vlastní Token. Aplikováno ve starých sítích Token Ring nebo moderních redundantních kruhových průmyslových optikách.
- **Stochastické metody (CSMA - Carrier Sense Multiple Access)**: Nedeterministický přístup postavený na principu "naslouchej, a když je volno, tak mluv". Uzly sdílejí společné médium a snaží se komunikovat co nejrychleji, ale riskují ztrátu dat vlivem kolizí.
  - **CSMA/CD (Collision Detection)**: Historický základ metalického half-duplexního Ethernetu (sítě s koaxiálem nebo hubem). Uzel před vysíláním fyzicky "poslouchá" napětí na drátu (Carrier Sense). Pokud je ticho, začne vysílat, ale zároveň dál poslouchá, zda mu do toho nezačne vysílat někdo jiný. Pokud zaznamená nárůst napětí (Collision Detection – detekuje kolizi), okamžitě přeruší přenos, odešle rušivý signál (JAM signal) všem stanicím a vypočítá si náhodný čas čekání (Backoff algorithm), než to zkusí znovu.
  - **CSMA/CA (Collision Avoidance)**: Standard rodiny 802.11 (Wi-Fi). U rádiových vln nelze kolizi spolehlivě detekovat přímo během vysílání (vlastní silný vysílač přehluší přijímač). Proto algoritmus snaze o kolize **předchází**. Pokud je rádiový kanál volný, vysílač čeká ještě malou povinnou prodlevu (DIFS) a náhodný čas k odpočtu. Volitelně si pak může s přijímačem nejprve vyměnit krátké rezervovací zprávy RTS/CTS (Request To Send / Clear To Send), čímž dočasně umlčí všechny ostatní stanice v okolí předtím, než začne vysílat objemná data. Každý přijatý unicastový rámec musí navíc příjemce potvrdit odesláním zprávy ACK (Acknowledgement).

## Struktura rámce (Frame) a ochrana integrity
L2 rámec obaluje užitečná data zapouzdřená ze síťové vrstvy. Ačkoliv se protokoly liší, obecná struktura rámce (např. Ethernet II) se skládá z neměnných bloků:

1. **Preambule a SFD**: Sekvence střídajících se nul a jedniček umožňující bitovou synchronizaci hodin u fyzického přijímače. Následuje SFD (Start Frame Delimiter) indikující přesný začátek hlavičky.
2. **Hlavička (Header)**: Obsahuje cílovou MAC adresu, za kterou následuje zdrojová MAC adresa odesílatele.
3. **EtherType / Length**: Uvádí celkovou délku rámce, nebo častěji (jako EtherType) definuje, jaký protokol 3. vrstvy (např. IPv4 - 0x0800) se nachází v datovém poli. Umožňuje tak LLC de-multiplexování.
4. **Tělo (Payload)**: Samotná užitečná data a hlavičky horních vrstev (typicky 46 až 1500 bajtů – neboli MTU).
5. **Zápatí (FCS - Frame Check Sequence)**: 4bajtový blok na úplném konci rámce, který nese 32bitový kontrolní součet. Vypočítá jej odesílatel matematickým algoritmem CRC (Cyclic Redundancy Check) z celého obsahu rámce. Přijímač na hardwarové úrovni výpočet zopakuje a výsledek porovná. Pokud se součty liší (rámec byl poškozen šumem, indukcí atd.), přijímač rámec okamžitě a bez varování zahodí. Linková vrstva v Ethernetu se neobtěžuje žádostí o opakování – záchranu dat musí vyřešit až vrstva transportní (TCP).

## L2 Síťové prvky a princip Přepínání
Na linkové vrstvě operují inteligentní hardwarová zařízení, která umí přečíst MAC adresy a na jejich základě provádět rozhodnutí (filtrování či směrování komunikace v rámci jedné sítě).

- **Most (Bridge)**: Historický dvouramenný prvek, který odděloval dvě odlišné kolizní domény a na úrovni linkové vrstvy propojoval např. Ethernet síť s Token Ring sítí.
- **Přepínač (Switch)**: Ultimátní L2 zařízení tvořící míchu dnešních LAN. Architektonicky jde o víceportový rychlý hardwarový bridge postavený na dedikovaných ASIC čipech. Switch na každém ze svých portů vytváří separátní, plně duplexní mikro-kolizní doménu bez nutnosti aplikovat CSMA/CD.
  - **Učení MAC adres (MAC Table)**: Switch čte zdrojovou MAC adresu z každého příchozího rámce a dynamicky si buduje "CAM tabulku" – mapování MAC adres na fyzické porty.
  - **Filtrování a Přeposílání**: Jakmile switch zná port, za kterým se nachází cílová MAC adresa přijímaného rámce, neodešle ho (jako tupý hub) na všechny porty, ale dedikovaně jej protlačí **pouze** na cílový port. Tím šetří šířku pásma ostatních a zvyšuje celkovou agregovanou propustnost sítě.
  - Pokud cílová MAC adresa v tabulce není (tzv. Unknown Unicast), nebo jde o Broadcast/Multicast, switch zaplaví rámi všechny aktivní porty kromě portu příchozího (Flooding).

## Pokročilé koncepty Linkové vrstvy
Moderní L2 infrastruktura využívá rozšiřující inženýrské protokoly, které zajišťují bezpečný a škálovatelný design podnikových sítí bez zásahů routerů.
- **VLAN (Virtual LAN - 802.1Q)**: Logické rozdělení jednoho fyzického switche do několika izolovaných virtuálních podsítí (oddělených broadcastových domén). Každému rámci je vložen tzv. VLAN Tag, který říká, komu rámec patří. Propojení mezi dvěma různými VLAN na vrstvě L2 je fyzicky nemožné – komunikaci musí zprostředkovat L3 router.
- **STP (Spanning Tree Protocol)**: V redundantním zapojení přepínačů do kruhu (pro zálohu proti výpadku) hrozí smrtelné vzniknutí broadcastové bouře, kdy by L2 rámce rotovaly v síti donekonečna (L2 hlavička neobsahuje TTL počítadlo, které by paket zabilo). Protokol STP na linkové vrstvě komunikuje přes zprávy BPDU, vyhledá případné smyčky v zapojení a matematicky dočasně logicky zablokuje určité porty, čímž vznikne z pohledu sítě bezpečná stromová struktura, aniž by se porušila mechanická záloha.