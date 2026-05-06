# 4. Historie operačních systémů, jejich architektura a dělení

Operační systém (OS) je základní vrstva systémového softwaru, která tvoří rozhraní mezi hardwarem a aplikačními programy. Jeho účelem je efektivně spravovat a abstrahovat fyzické zdroje (CPU, RAM, disky, I/O periferie) a poskytovat standardizované API pro aplikace.

## Historie OS Unix a Linux
Vývoj unixových systémů položil základy pro moderní správu procesů a zabezpečení.
- **Předchůdce a vznik UNIXu**: V 60. letech se v laboratořích Bell Labs, MIT a GE vyvíjel rozsáhlý systém *Multics*. Pro svou přebujelost byl opuštěn. Inženýři Ken Thompson a Dennis Ritchie (Bell Labs) z něj v roce 1969 abstrahovali jednodušší a elegantnější systém – **UNIX**. Byl přepsán do nízkoúrovňového jazyka C, což znamenalo historický zlom: systém se stal přenositelným (překompilovatelným) na různé hardwarové architektury.
- **Filozofie UNIXu**: "Všechno je soubor" (včetně hardwaru mapovaného do adresáře `/dev`). Úzce zaměřené nástroje, které dělají jednu věc a dělají ji dobře, přičemž jsou spojovány přes roury (Pipes).
- **Zrod Linuxu**: Počátkem 90. let existoval projekt GNU (vytvořený Richardem Stallmanem), který dodal svobodné unixové nástroje a kompilátory, avšak chybělo mu funkční jádro (kernel). V roce 1991 student **Linus Torvalds** napsal a pod svobodnou licencí GNU GPL zveřejnil jádro **Linux**. Spojením GNU nástrojů a Linuxového jádra vznikl plnohodnotný svobodný operační systém (GNU/Linux). Dnes je dominantní platformou drtivé většiny internetových serverů, superpočítačů a smartphonů (Android je postaven na modifikovaném jádře Linuxu).

## Historie OS Windows
Zatímco Unix cílil na velké akademické sálové počítače, Microsoft ovládl trh osobních mikropočítačů (PC).
- **Éra MS-DOS (1981)**: Microsoft odkoupil systém QDOS, upravil jej a licencoval IBM pod názvem MS-DOS. Šlo o 16bitový, jednouživatelský (single-user) a jednonitný (single-tasking) systém s výhradně textovým rozhraním (CLI).
- **Nadstavby nad DOSem (Windows 1.0 až Windows 98/ME)**: Rané verze Windows (začínající rokem 1985) nebyly skutečnými operačními systémy, ale pouze 16bitovými a později 32bitovými grafickými nadstavbami (GUI) běžícími nad vrstvou MS-DOS. Vyznačovaly se tristní stabilitou, jelikož aplikace měly přímý přístup k hardwaru a využívaly nespolehlivý kooperativní multitasking.
- **Rodina Windows NT (New Technology)**: Zcela nová, paralelně vyvíjená větev systému od roku 1993, napsaná pro byznys a servery s důrazem na absolutní stabilitu a bezpečnost. Jádro bylo zcela přepsáno na preemptivní multitasking a plnou izolaci paměti procesů od hardwaru. Počínaje systémem **Windows XP** (a u moderních Windows 10/11) Microsoft definitivně odřízl starý MS-DOS a veškeré dnešní systémy Windows fungují exkluzivně na vyspělém jádře architektury NT.

## Obvyklá struktura OS (Architektura)
Moderní operační systémy využívají k ochraně před zhroucením striktní vrstvený model paměti a oprávnění, hardwarově podporovaný procesory (tzv. Protection Rings). Celý výpočetní prostor se dělí na dvě izolované sféry: **Kernel Space** (prostor jádra) a **User Space** (uživatelský prostor).

1. **Jádro (Kernel)**: Centrální výpočetní a řídící komponenta operačního systému běžící v nejvyšším stupni oprávnění (Ring 0). Má přímý, nelimitovaný přístup ke všem fyzickým zdrojům (RAM, CPU instrukce). Stará se o kritické operace:
   - **Plánovač procesů (Scheduler)**: Přiděluje časová okna CPU procesům (např. algoritmus CFS v Linuxu).
   - **Správa paměti (Memory Management / Paging)**: Přiděluje virtuální paměť a řeší stránkování do SWAPu.
   - **Správa souborového systému (VFS)**: Zajišťuje I/O operace na pevných discích.
2. **Ovladače zařízení (Device Drivers)**: Často tvoří součást jádra nebo vrstvu zvanou **HAL** (Hardware Abstraction Layer). Překládají obecné příkazy jádra (např. "přečti sektor") na specifické elektrické instrukce pro konkrétní hardware konkrétního výrobce.
3. **Systémová API a Knihovny**: Rozhraní (v Linuxu `glibc`, ve Windows moduly `Win32 API`), které tvoří most. Bezpečnostní izolace totiž neumožňuje, aby si běžná aplikace z User Space sáhla přímo do paměti hardwaru. Musí provést tzv. **System Call** (systémové volání) a požádat jádro, aby danou akci provedlo za ni.
4. **Shell a Uživatelské rozhraní**: Nejsvrchnější vrstva. Představuje tlumočníka mezi uživatelem a systémovými knihovnami. Může mít grafickou podobu (GUI - např. Windows Explorer, KDE/GNOME v Linuxu) nebo textovou podobu (CLI - např. Bash, PowerShell). Běží ve striktně omezeném User Space (Ring 3) – pokud zde aplikace zkolabuje, stáhne sebou jen sama sebe, nikoliv celé jádro.

## Dělení OS podle inženýrských charakteristik
Operační systémy lze striktně klasifikovat podle způsobu, jakým rozdělují výpočetní čas a strukturují své jádro.

### 1. Podle architektury jádra (Kernel Design)
- **Monolitické jádro**: Celý operační systém (správa procesů, paměti, souborového systému i ovladače grafiky a síťovek) je zkompilován do jediné obrovské binárky a běží společně v nejvyšším stupni oprávnění (Kernel Space). Výhodou je extrémní rychlost, jelikož podsystémy komunikují přímo v jedné paměti bez režie. Zásadní nevýhodou je bezpečnost – pád jednoho ovladače do nekonečné smyčky zhroutí celý systém (tzv. Kernel Panic / BSOD). Typický představitel: **Linux**.
- **Mikrojádro (Microkernel)**: Osekává kód v Kernel Space na absolutní fyzikální minimum (pouze nejnižší správa paměti, CPU a zajištění meziprocesové komunikace - IPC). Všechno ostatní (souborový systém, ovladače zařízení, síťový zásobník) běží jako izolované uživatelské procesy (v User Space). Pokud spadne ovladač síťové karty, jádro to neovlivní, systém nespadne a démon se pouze tiše restartuje. Nevýhodou je rozsáhlý režie na zasílání zpráv (IPC) mezi prostory, což systém zpomaluje. Představitelé: QNX, moderní vývojové větve macOS/iOS.

### 2. Podle schopnosti multitaskingu
- **Single-tasking**: Systém dokáže v jednu chvíli zpracovávat pouze jediný proces. Dokud se program neukončí, nepustí k CPU nic jiného. Typicky historický **MS-DOS**.
- **Multitasking (Kooperativní)**: Systém spustí více aplikací, ale ty se musí samy programově a dobrovolně vzdávat procesorového času, aby pustily ke slovu ostatní. Stačí chyba programátora jedné aplikace a celý OS "zamrzne". Známé z raných Windows 3.1 / 95.
- **Multitasking (Preemptivní)**: Absolutní vládu nad hardwarem přebírá procesorový plánovač v jádře OS. Sám a nemilosrdně přiděluje i odebírá procesům časová okna (řádově v milisekundách) pomocí hardwarových přerušení, čímž vytváří iluzi plynulého paralelního běhu. Pád jakkoliv špatně napsané aplikace nedokáže systém zablokovat. Průmyslový standard pro Windows NT a Linux.

### 3. Podle reakční doby (Determinismu)
- **Time-Sharing OS (OS s dělením času)**: Běžné systémy (Windows, Linux, macOS). Cílem je poskytnout zhruba stejný a férový výpočetní čas všem aplikacím uživatele pro maximalizaci agregované propustnosti. Pokud dojde k lokální zátěži, systém se na sekundu zadrhne (zpoždění/latence), což u myši či webu nevadí.
- **RTOS (Real-Time Operating System)**: Systémy nasazované v medicíně (kardiostimulátory), průmyslové robotice, letectví nebo armádě. Zde nezáleží na celkové propustnosti, ale na **tvrdém determinismu**. Systém matematicky garantuje, že na konkrétní vstupní signál odpoví v předem garantovaném okně (např. do 2 milisekund) a nikdy toto zpoždění nepřekročí, jinak by u kardiostimulátoru či brzd automobilu došlo k fatálnímu ohrožení života. Typický představitel: QNX, VxWorks, FreeRTOS.