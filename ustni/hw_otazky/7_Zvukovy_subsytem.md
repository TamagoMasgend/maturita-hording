# 7. Zvukový subsystém PC

## Účel a princip digitalizace zvuku
Zvukový subsystém počítače představuje komplexní soustavu hardwarových obvodů zodpovědných za vstup, výstup a zpracování audio signálu. Zatímco lidské ucho a fyzikální svět pracují se spojitým (analogovým) akustickým vlněním, počítač vyžaduje data striktně v diskrétní (digitální) binární podobě. Zvuková karta proto plní roli hardwarového překladatele pomocí dvou kritických obvodů: **ADC** (Analog-to-Digital Converter) pro záznam z mikrofonu a **DAC** (Digital-to-Analog Converter) pro přehrávání do reproduktorů.

Proces převodu analogové křivky do digitální podoby se matematicky skládá ze dvou kroků:
1. **Vzorkování (Sampling)**: Obvod v přesných časových intervalech odebere aktuální hodnotu napětí analogového signálu. Čím kratší je interval, tím vyšší je frekvence a přesnější zvuk. Kvalitativní stupně frekvencí jsou historicky definovány na: **11,025 kHz** (telefonní kvalita), **22,05 kHz** (rádiová kvalita), **44,1 kHz** (standard pro Audio CD), **48 kHz** (standard pro DVD) a **96 kHz** (studiová kvalita).
2. **Kvantování (Quantization)**: Naměřenému vzorku je přiřazena konkrétní digitální hodnota, neboli dojde k jeho zaokrouhlení na nejbližší napěťovou úroveň v rámci definovaného rozsahu. Bitová hloubka (rozlišení) určuje celkový počet těchto úrovní. Standardní karty vzorkují s rozlišením **8, 16 nebo 24 bitů**. Například kvalita Audio CD (44,1 kHz / 16 bitů) matematicky znamená, že převodník zanese napětí do paměti 44 100krát za vteřinu, přičemž každý vzorek nabývá jedné z 65 536 (2^16) hodnot.

## Architektura zvukových karet a konektory
Zvukové obvody se v počítači dělí primárně podle fyzického umístění, jež silně ovlivňuje izolaci od elektromagnetického rušení (EMI). **Integrované karty** (On-board) jsou zapájené přímo na základní desce přes sběrnici PCI-e. **Dedikované externí karty** se nejčastěji připojují přes USB; nezatěžují tolik centrální procesor (obsahují vlastní DSP procesory) a poskytují mnohem čistší signál, což z nich dělá nutnost pro profesionální tvorbu hudby. K nim lze připojit sluchátka, reproduktory, zesilovače a také MIDI rozhraní pro elektronické hudební nástroje.

Pro komunikaci s periferiemi vládne průmyslový standard **2,5mm / 3,5mm Jack**, osazený následujícím barevným značením:
- **Zelená**: Hlavní analogový stereo výstup (pro sluchátka a přední reproduktory).
- **Růžová**: Analogový vstup určený výhradně pro mikrofony s dodávaným pracovním napětím.
- **Modrá**: Line-In vstup (pro nahrávání z externích zařízení, např. z rádia či kytary).
- **Černá / Stříbrná**: Výstupy pro zadní a boční kanály prostorového zvuku.
- **Oranžová**: Výstup pro centrální reproduktor/subwoofer nebo zastupující digitální S/PDIF (Sony/Philips Digital Interface).

Pro bezeztrátový přenos se využívá digitální **S/PDIF** přes optický kabel u konektorů **TOSLINK** nebo přes koaxiální elektrický kabel končící konektorem **RCA Cinch**. Pro studiové mikrofony slouží plně symetrický konektor **XLR**.

## Konstrukce a fyzikální parametry reproduktorů
Reproduktory coby elektroakustické měniče transformují střídavý elektrický proud na mechanické vlnění vzduchu. Základem nejběžnějšího **elektrodynamického** pohonu je kmitací cívka z navinutého drátu, umístěná ve válcové vzduchové mezeře permanentního magnetu. Průchodem signálu na cívku působí síla, jež pohybuje pevně připojenou membránou.

Materiál membrány zásadně definuje akustické parametry: od **papíru** (levný, snadno zpracovatelný), přes **plasty** a tužší **kevlar**, až po **hliník** a **sklovlákno** u prémiových soustav.

Z inženýrského hlediska dělíme reproduktory podle principu pohonu:
- **Elektrodynamické**: Nejčastější. Dělí se dále na běžné kónické, vysoce citlivé *páskové reproduktory*, *plošné měniče* s vodičem po celé ploše, a reproduktory *s ohybovou vlnou*.
- **Elektrostatické**: Pracují na vzájemném přitahování elektricky nabitých desek.
- **Elektromagnetické**: Membrána ze železného plechu přitahována cívkou s jádrem (dnes již zastaralé).
- **Piezoelektrické**: Využívají deformace krystalů pod napětím.
- **Plazmové**: Využívají přímo modulace ionizovaného plynu ke změně tlaku vzduchu, tvořící absolutně nehmotnou a nezpožděnou membránu.

Dělení reproduktorů podle reprodukovaného frekvenčního pásma:
- **Basové (Nízkopásmové)**: Pracují v rozsahu **20 až 1 500 Hz** pro speciální subwoofery a **35 až 5 000 Hz** u klasických středobasů.
- **Středopásmové**: Pracují v kritickém pásmu pro lidský hlas od **80 Hz do 1 200 Hz**.
- **Vysokotónové (Výškové)**: Reprodukují zbytek slyšitelného spektra od **2 000 do 20 000 Hz**.

Elektroakustickým limitem pro vybuzení je jmenovitá **Impedance** cívky (typicky 4 nebo 8 Ω). Mechanickou zatížitelnost definuje výkon v **RMS** (trvalý sinusový výkon ve Wattech, který reproduktor nezničí), zatímco udávaný **PMPO** výkon je pouze marketingový výpočet absolutního zlomkového maxima těsně před destrukcí měniče. Běžné PC reprosoustavy jsou obvykle **Aktivní** (2.1 nebo 5.1 s vlastním interním zesilovačem v těle subwooferu), zatímco pro Hi-Fi slouží **Pasivní** soustavy, vyžadující samostatný kvalitní AV receiver.

## Sluchátka a záznam zvuku (Mikrofony)
Sluchátka představují speciální zmenšené reproduktory (často označované jako In-Ear Monitory v profi segmentu). Z ergonomického hlediska rozeznáváme mušle obepínající ucho **Full size (Cirkumaurální)**, ležící na boltci **Supraaurální**, dále populární špunty **Ear in** pro koncerty a plastové pecky **Earbuds**. Z hlediska akustické izolace se dělí na *Otevřená*, *Polootevřená* a izolující *Uzavřená*. Pro zvukovou kvalitu hraje rozsáhlý roli impedance: **nízkoimpedanční** snadno rozhýbe mobil, ale **vysokoimpedanční** cívky (nad 100 Ω, někdy i 300 Ω) u studiových sluchátek dokonale zpracovávají pásma nad 16 000 Hz a bezprostředně vyžadují externí sluchátkový zesilovač.

**Mikrofony** provádějí inverzní proces – mění akustický tlak na elektrický signál. 

Z hlediska konstrukce a cílového nasazení rozeznáváme mikrofony **klopové** (nenápadně připevněné sponou na hrudník pro televizní produkci), **ruční** (odolná pódiová klasika), **stolní / konferenční** a **puškové** (extrémně dlouhé, úzce směrové shotgun mikrofony montované přímo na filmové kamery). Získaný signál je nutné odvést do zvukového subsystému. Využívá se k tomu spotřebitelský **3,5mm Jack**, počítačový standard **USB** (čímž se obejde interní zvuková karta, protože mikrofon sám obsahuje ADC obvod a zesilovač) nebo profesionální trojpinový **XLR** konektor, garantující dokonale symetrický a mechanicky stabilní přenos bez rušení.

Z hlediska vnitřní fyziky převodníku mikrofony dělíme na:
- **Dynamické**: Membrána hýbe cívkou v magnetu. Snášejí rozsáhlý tlak (živý zpěv, bicí), nevyžadují externí napájení. Jejich zvláštní a velmi křehkou odnoží je **páskový mikrofon**, obsahující hliníkovou fólii a převodní transformátor.
- **Kondenzátorové**: Membrána tvoří desku kondenzátoru. Extrémně citlivé, špičkové mikrofony. Absolutně vyžadují napájení (Phantom 48V).
- **Elektretové**: Zlevněná verze kondenzátorů, kde je elektroda trvale nabitá (tzv. elektret). Vyžadují pouze drobné napětí pro vnitřní FET předzesilovač. rozsáhlý se používají do mobilů, PC headsetů a diktafonů.
- **Uhlíkové**: Zastaralý formát pro pre-historické telefony, kde membrána stlačovala uhlíková zrnka a měnila jejich odpor. Trpěly rozsáhlý a silným chrastěním, avšak generovaly natolik silný signál, že mohly modulovat přímo výkonové vysílače.
- **Piezoelektrické**: Využívané v 50. letech nebo jako snímače chvění na kytarách.

Inženýrským parametrem je **směrová charakteristika** (polární vzor): Všesměrová pro zasedačky, **Kardioidní / Superkardioidní** pro izolaci jednoho zpěváka potlačením hluku z obklopujícího zadního pódiového ruchu, nebo **Osmičková** pro rozhovory tváří v tvář. Zcela úzce směrová charakteristika se používá u výše zmíněných puškových mikrofonů. Měřítkem kvality je pak **Odstup signálu od šumu** a produkce vlastního elektronického šumu pod `<20 dB`. Udávaná **Citlivost (SPL)** měří vyprodukované milivolty (mV) při dopadu akustického tlaku o síle 1 Pascal.