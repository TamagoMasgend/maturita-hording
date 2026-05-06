# 19. Ethernet

## Podstata a vývoj standardu
Ethernet, nesoucí standardizační označení **IEEE 802.3**, je absolutně dominantní technologií používanou pro budování drátových lokálních sítí (LAN) na úrovni fyzické (L1) a linkové vrstvy (L2). Původně byl vyvinut v roce 1973 laboratořemi Xerox PARC týmem Roberta Metcalfea, aby propojil sálové počítače se sdílenými tiskárnami. Cílem Ethernetu bylo vždy poskytnout jednoduchou, levnou, a přitom snadno spravovatelnou a udržovatelnou komunikační technologii.

Původní "Ethernet 1.0" běžel na rychlosti 2,94 Mbps, nicméně s oficiální standardizací v roce 1980 došlo k rozsáhlému boomu:
- **Standardní Ethernet (10 Mbps)**: Definován jako rodina 10Base.
- **Fast Ethernet (100 Mbps)**: S označením 100Base překonal rychlostní překážky při nástupu internetu v roce 1995.
- **Gigabit Ethernet (1 Gbps)**: Technologie 1000Base se stala standardem pro datacentra a moderní kanceláře.
- **10 Gigabit Ethernet (10 Gbps)** a více (40G, 100G) se dnes používají na páteřních spojích (Backbone).

Názvosloví Ethernetu inženýrsky definuje přesné vlastnosti linky – například u **100Base-TX** číslo 100 znamená rychlost v Mbps, *Base* značí přenos v základním pásmu (Baseband - digitální kódování, nikoliv modulace nosné) a *TX* definuje fyzické médium, v tomto případě kroucenou dvojlinku typu Twisted Pair (na rozdíl např. od optického vlákna *FX*).

## Topologie a historické zapojení
Evoluce Ethernetu prošla z hlediska topologie zásadními fázemi úzce vázanými na pokrok v použité kabeláži:
- **Sběrnicová topologie (Bus)**: Historický základ na bázi koaxiálního kabelu. Veškeré PC sdílely jedno průběžné drátové médium. U tlustého koaxiálu (Thicknet, **10Base-5** s dosahem 500 m) a u tenkého koaxiálu (Thinnet, **10Base-2** s dosahem 200 m) se kabel připojoval přes T-konektory a na koncích sběrnice musely být nainstalovány terminátory, pohlcující odraz signálu. Pokud se kabel na jednom místě přetrhl, zřítila se komunikace pro celou síť.
- **Hvězdicová topologie (Star)**: Dnešní nekompromisní standard. Po zavedení levné kroucené dvojlinky (Cat5e/Cat6) zakončené konektory RJ-45 (**10Base-T, 100Base-TX**) a optických vláken došlo ke změně struktury. Všechny kabely se sbíhají z koncových PC přímo do centrálního přepínače (Switch). Přerušení jednoho kabelu paralyzuje pouze daný počítač, nikoli zbytek budovy. Kombinováním switchů pak vzniká topologie Stromu.

## Kódování dat na fyzické vrstvě
Signál u Ethernetu využívá neustálý přechod z analogových do digitálních modulací. Jak rychlost rostla, museli inženýři opustit primitivní kódy a aplikovat efektivnější linkové kódování.
- Změny napětí rozdělujeme na Unipolární (jen jedna polarita), Polární (kladná a záporná úroveň, např. -5 V a +5 V) a Bipolární (kombinace, např. návrat k nule).
- **Manchester kódování**: Bylo základním pilířem historického 10 Mbps Ethernetu. Nepoužívalo změnu prahu napětí pro reprezentaci 0 a 1, ale orientovalo se na *hranu*. Každý přenášený bit obsahoval uprostřed svého časového intervalu napěťový skok (tzv. přechodovou hranu). Tím odesílatel i přijímač distribuovali logická data a zároveň přesně synchronizovali své hodiny přímo v signálu.
- **MLT-3 a 4B/5B**: Využívané u Fast Ethernetu (100 Mbps). Kódování využívající 3 úrovně napětí, čímž zásadně omezilo nutnou frekvenční šířku pásma v drátu a vyřešilo modulační režii u 100 MHz frekvencí.
- **PAM-5 (Pulse Amplitude Modulation)**: Absolutní špička pro Gigabit Ethernet (1000Base-T) na Cat5e kabeláži. Pracuje napříč všemi 4 kroucenými páry současně (čtyři dimenze 4D), ve kterých kóduje bity do 5 napěťových úrovní. Přenos probíhá s plně duplexním zrušením echa, díky čemuž je možné tlačit a přijímat celou gigabitovou porci skrz staré měděné dráty.

## L2 rámce Ethernetu
Linková vrstva Ethernetu nesená formátem Ethernet II ohraničuje přenášený payload IP paketů do pevných rámců o délce těla maximálně 1 500 Bajtů. Struktura rámce musí zahrnovat:
- **Preambule a SFD (7 + 1 bajtů)**: Střídání nul a jedniček umožňující bitovou synchronizaci hardwaru přijímače před čtením samotných dat.
- **MAC Adresy odesílatele a příjemce (2× 6 bajtů)**: Hardware využívá tyto 48bitové adresy pro filtrování a řízení toku přes Switche uvnitř LAN sítě.
- **EtherType / Length (2 bajty)**: Identifikuje, jaký logický protokol se veze uvnitř datového pole (např. IPv4).
- **Tělo (Payload)**: Samotná užitečná data.
- **FCS (Frame Check Sequence, 4 bajty)**: Na chvostu rámce se nachází kontrolní součet algoritmu CRC generovaný odesílatelem, čímž hardware příjemce dokáže detekovat zkorumpovaný či šumem zničený rámec a na místě ho zahodit.

## Kolize a přístupové metody: CSMA/CD
V dřevních dobách, kdy Ethernet využíval sdílený koaxiální kabel nebo tupé rozbočovače (Huby), tvořila celá sít **jednu jedinou kolizní doménu**. Počítače nevěděly, kdo a kdy má vysílat (nešlo o statickou frekvenci ani časové okno TDMA). Místo toho se uplatnila distribuovaná stochastická přístupová metoda **CSMA/CD** (Carrier Sense Multiple Access with Collision Detection).

Fungovala na třech logických krocích:
1. **Carrier Sense**: PC nejprve elektricky "poslouchá", zda je médium volné a nevysílá zrovna někdo jiný.
2. **Multiple Access**: Médium nemá centrálního pána (žádný token), přístup k němu má kdo chce.
3. **Collision Detection**: Pokud PC "slyší" ticho, začne vysílat, ale *zároveň dál poslouchá drát*. Zvýší-li se náhle napětí nad určitou mez (tzv. kolize, obě PC odvysílala jedničku přes sebe), hardware detekuje destrukci signálu. Obě PC okamžitě vysílání zastaví, do celého kabelu odvysílají krátký zarušovací *JAM signál* (aby varovaly i vzdálené stanice), následně si dle exponenciálního *Backoff* algoritmu matematicky vygenerují náhodný čas k čekání a odeslání zopakují.

S příchodem moderních hardwarových **přepínačů (Switchů)**, které s každým počítačem tvoří izolovaný full-duplexní mikro-okruh přes kroucenou dvojlinku (PC a Switch přijímají i odesílají po separátních párech bez elektrického setkání na drátu), **riziko kolizí zcela zaniklo**. Architektura dnešních Ethernetových sítí již CSMA/CD v praxi aplikovat nemusí a komunikace probíhá gigabitovou rychlostí ve 100% čistém Full-Duplexu.