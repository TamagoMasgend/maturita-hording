# 16. Transportní, relační a prezentační vrstva modelu ISO/OSI

Referenční model ISO/OSI dělí komunikaci do sedmi vrstev. Zatímco spodní tři vrstvy (fyzická, linková, síťová) řeší hardwarový přesun paketů přes infrastrukturu k cílovému uzlu, horní vrstvy (transportní, relační, prezentační a aplikační) se zabývají sémantikou, formátováním a koncovým doručením dat přímo do paměti konkrétního softwarového procesu. V prakticky využívaném modelu rodiny TCP/IP jsou funkce vrstev 5, 6 a 7 agregovány do jediné mohutné aplikační vrstvy.

## Transportní vrstva (4. vrstva)
Transportní vrstva (Transport Layer) zajišťuje absolutní odstínění horních softwarových vrstev od fyzické infrastruktury sítě. Její zodpovědností je výhradně **end-to-end komunikace** mezi procesy běžícími na koncových zařízeních. Nezajímá ji topologie ani počet routerů na trase.

Data plynoucí od aplikace transportní vrstva rozseká na menší bloky zvané **segmenty** (případně datagramy). Ke každému segmentu připojí transportní hlavičku a předá je síťové vrstvě (L3) k doručení. 

### Adresace procesů: Síťové porty
Aby transportní vrstva rozlišila, zda data putují do webového prohlížeče, poštovního klienta nebo hry, využívá logickou identifikaci pomocí **Portů** (16bitové číslo, rozsah 0 až 65 535). Kombinace IP adresy a čísla portu tvoří síťový Socket.
- **Dobře známé porty (0 – 1 023)**: Fixně rezervované pro privilegované systémové služby a protokoly (např. port 80 pro HTTP, 443 pro HTTPS, 21 pro FTP).
- **Registrované porty (1 024 – 49 151)**: Komerčně registrované síťové aplikace (např. 3389 RDP).
- **Soukromé a dynamické porty (49 152 – 65 535)**: Dočasné porty (ephemeral), které si náhodně přiřadí operační systém počítače jako zdrojové, když otevírá spojení k serveru.

### Protokol TCP (Transmission Control Protocol)
Protokol poskytující **spolehlivou a spojovanou službu**. Před odesláním dat musí nejprve ustavit logické spojení pomocí tzv. třícestného handshaku (výměna vlajek SYN, SYN-ACK, ACK).
- **Sekvenční číslování a potvrzování**: Každý odeslaný bajt nese své pořadové číslo. Příjemce musí přijatá data potvrdit odesláním zprávy ACK. Pokud odesílateli potvrzení v časovém limitu nedorazí, segment bez vyzvání automaticky odešle znovu. Tím je absolutně garantováno doručení bez chyb a záchrana ztracených paketů.
- **Řízení toku (Flow Control)**: Mechanismus plovoucího okénka (Sliding Window) brání rychlému serveru zahltit pomalého příjemce daty, která nestíhá zpracovat. Příjemce dynamicky v TCP hlavičce diktuje, kolik bajtů může aktuálně přijmout.
- Použití: Web, e-maily, přenosy souborů, kde nesmí chybět jediný bit.

### Protokol UDP (User Datagram Protocol)
Protokol poskytující **nespolehlivou a nespojovanou službu**. Neprovádí handshake, nečísluje segmenty a nevyžaduje potvrzení o přijetí (ACK). PDU u UDP se korektně nazývá **datagram**.
- Absence řídicí logiky znamená drasticky nižší hlavičku (pouze zdrojový a cílový port, délka a kontrolní součet) a nulovou latenci. Ztráta paketu na lince se neřeší a neopravuje.
- Použití: Služby citlivé na zpoždění – streamování videa, VoIP telefonie, online videohry a rychlé dotazovací služby jako DNS.

## Relační vrstva (5. vrstva)
Relační vrstva (Session Layer) formálně řídí logický dialog mezi aplikacemi. Zabezpečuje korektní zahájení relace (session), průběžnou synchronizaci probíhající komunikace a ladné ukončování, aby nedošlo k poškození dat při náhlém výpadku.
- **Řízení dialogu**: Určuje režim toku (kdo má momentálně vysílat u half-duplex komunikace).
- **Synchronizační body**: Při přenosu obřích souborů může vrstva do proudu dat vkládat značky. Pokud dojde k výpadku systému nebo konektivity, relace se po obnovení naváže přesně od posledního bodu a nemusí se celý soubor posílat od nuly.
- V praxi historických sítí se zde objevoval protokol **NetBIOS**, který k navázání relace na LAN (lokální síti) nepoužíval IP adresy, ale unikátní 15znaková logická jména počítačů, a k hledání odesílal L2 broadcasty.

## Prezentační vrstva (6. vrstva)
Prezentační vrstva (Presentation Layer) se nezabývá směřováním, ale významem (sémantikou) a strukturou (syntaxí) přenášených dat. Heterogenní výpočetní architektury (např. procesory s řazením bajtů Little Endian oproti Big Endian) nebo různé operační systémy interpretují paměť odlišně. 

### Konverze formátů a kódování
Vrstva překládá data z interní reprezentace operačního systému do univerzálního, síťově nezávislého standardu.
- **Kódování znaků**: Převádí textové řetězce mezi formáty, například do standardu ASCII, historického EBCDIC, nebo moderního kódování Unicode (UTF-8). Zajišťuje standardizaci obrazových (JPEG, GIF) a audio (MP3) proudů pro správné zobrazení na cílovém zařízení.
- **Rozšíření MIME (Multipurpose Internet Mail Extensions)**: Zásadní konverzní předpis v e-mailové komunikaci, který vyřešil historické omezení protokolu SMTP pouze na odesílání prostého neakcentovaného textu (US-ASCII). MIME definuje hlavičky umožňující začlenit přílohy, multimédia a české znaky tím, že před odesláním celá binární data překóduje na bezpečné ASCII znaky algoritmem **Base64** nebo Quoted-Printable.

### Zabezpečení a šifrování
Dnešní nejvýznamnější role 6. vrstvy (přestože se de-facto prolíná do transportních soketů). Nezabezpečená data zachytitelná odposlechem vrstva před odesláním zašifruje a u příjemce dešifruje.
- **Symetrické šifrování**: Odesílatel i příjemce musí znát stejný, jediný tajný klíč k zašifrování i dešifrování datových bloků (např. protokoly AES, dříve RC4). Extrémně rychlé, ale problémem je bezpečná distribuce onoho klíče na dálku.
- **Asymetrické šifrování**: Model dvou klíčů (veřejného a soukromého). Co se zašifruje veřejným klíčem, lze odemknout striktně pouze soukromým klíčem příjemce (algoritmy RSA, DSA). Je pomalejší, ale absolutně řeší problém výměny hesel.
- **Zajištění integrity a otisky (Hashování)**: Matematické funkce generující z libovolně velkého objemu dat hexadecimální "otisk" (fingerprint) o neměnné délce. Jakákoliv nepatrná změna v původních datech vygeneruje zcela jiný hash. K detekci změny balíku se využívají rodiny algoritmů SHA (či zastaralý, na kolize náchylný MD5).
- **Protokol TLS (Transport Layer Security)**: Moderní standard nahrazující prolomené SSL. Je nasazován před přenosem běžného HTTP (vzniká HTTPS) a skládá se ze tří logických fází: dohoda komunikujících stanic na síle šifrování (algoritmech), navázání bezpečné asymetrické relace pro předání klíčů a samotné plynulé zašifrování datového provozu rychlou symetrickou šifrou.