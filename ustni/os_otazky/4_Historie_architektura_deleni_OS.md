---
created: 2026-05-06T11:11
updated: 2026-05-20T13:00
---
# 4. Historie operačních systémů, jejich architektura a dělení

Operační systém (OS) je základní vrstva systémového softwaru, která tvoří rozhraní mezi hardwarem a aplikačními programy. Jeho účelem je efektivně spravovat a abstrahovat fyzické zdroje (CPU, RAM, disky, I/O periferie) a poskytovat standardizované API pro aplikace.

## Historie OS Unix a Linux
Vývoj unixových systémů položil základy pro moderní správu procesů, víceuživatelské systémy a síťovou komunikaci.
- **Vznik UNIXu (1969)**: V 60. letech se v laboratořích Bell Labs, MIT a General Electric vyvíjel ambiciózní, ale přebujelý systém *Multics*. Pro svou neefektivitu byl opuštěn, což motivovalo inženýry **Kena Thompsona** a **Dennise Ritchieho** (Bell Labs), aby vytvořili jednodušší a elegantnější systém – **UNIX**. Zásadním zlomem bylo přepsání UNIXu do nově vyvinutého programovacího jazyka **C** (1973). Tím se systém odpoutal od konkrétního hardwaru a stal se přenositelným na jiné procesorové architektury.
- **Větvení UNIXu**: Postupně vznikly dvě hlavní vývojové větve: komerční **System V** od AT&T a akademická **BSD** (Berkeley Software Distribution). Kvůli licenčním sporům o značku UNIX vznikla snaha o sjednocení a standardizaci rozhraní, což vyústilo v normu **POSIX** (Portable Operating System Interface), kterou dnes splňují všechny unixové systémy.
- **GNU Projekt (1983)**: **Richard Stallman** založil projekt GNU s cílem vytvořit kompletně svobodný unixový operační systém. Vyvinul klíčové nástroje (kompilátor GCC, knihovnu glibc, textové editory), ale chybělo jim funkční jádro (GNU Hurd).
- **Zrod Linuxu (1991)**: Finský student **Linus Torvalds** začal na architektuře Intel 386 psát vlastní jádro inspirované systémem MINIX. V roce 1991 zveřejnil zdrojový kód jádra **Linux** pod svobodnou licencí GNU GPL. Spojením GNU uživatelských nástrojů a Linuxového jádra vznikl plnohodnotný operační systém **GNU/Linux**. Ten se díky své stabilitě, bezpečnosti a otevřenosti stal dominantní platformou pro servery, cloudovou infrastrukturu, superpočítače a mobilní zařízení (Android využívá upravené jádro Linuxu).

## Historie OS Windows
Microsoft cílil primárně na trh osobních mikropočítačů (PC), kde postupně vystřídal několik architektur.
- **Éra MS-DOS (1981)**: Microsoft odkoupil systém QDOS (Quick and Dirty OS), upravil jej a pod názvem MS-DOS (Microsoft Disk Operating System) licencoval firmě IBM pro její první osobní počítače IBM PC. MS-DOS byl 16bitový, jednouživatelský (single-user) a jednonitný (single-tasking) systém ovládán výhradně z příkazové řádky. Neměl žádnou ochranu paměti – pád programu znamenal pád celého počítače.
- **Grafické nadstavby Windows 1.0 až 98/ME**: V roce 1985 vydal Microsoft první grafické rozhraní **Windows 1.0**. Verze Windows 1.x, 2.x, 3.x a následná řada Windows 9x (Windows 95, 98, ME) nebyly samostatnými operačními systémy v moderním slova smyslu, ale pouze 16/32bitovými grafickými nadstavbami spouštěnými nad vrstvou MS-DOS. Trpěly nízkou stabilitou kvůli kooperativnímu multitaskingu a chybějící izolaci paměti aplikací.
- **Architektura Windows NT (New Technology)**: Paralelně s řadou 9x vyvíjel Microsoft od roku 1993 zcela nový systém určený pro podnikové nasazení a servery. Tým vedený **Davem Cutlerem** navrhl moderní 32bitové (později 64bitové) preemptivní jádro s hardwarovou abstrakcí (HAL) a plnou izolací paměti. Prvním systémem pro běžné spotřebitele postaveným na tomto jádře byly **Windows XP** (2001). Všechny současné verze (Windows 10, 11, Windows Server) fungují výhradně na jádře řady Windows NT.

## Obvyklá struktura OS (Architektura)
Moderní operační systémy využívají k ochraně před destabilizací a neautorizovaným přístupem k hardwaru hardwarově podporovaný model privilegií procesoru, známý jako **Protection Rings** (ochranné prstence). Většina architektur implementuje dva základní režimy:
- **Kernel Mode (Privilegovaný režim / Ring 0)**: Režim s neomezeným přístupem k instrukcím CPU a fyzické paměti RAM. Běží v něm samotné jádro (kernel) a kritické ovladače.
- **User Mode (Neprivilegovaný režim / Ring 3)**: Režim s omezenými instrukcemi, ve kterém běží běžné uživatelské aplikace, grafické prostředí a uživatelské knihovny. Aplikace mají zakázáno přistupovat přímo k hardwaru nebo paměti jiných procesů.

### Architektonické vrstvy operačního systému:
1. **Hardware**: Fyzické komponenty počítače (CPU, RAM, disky, síťové karty).
2. **Jádro (Kernel)**: Centrální komponenta OS běžící v Ring 0. Zajišťuje:
   - **Plánovač procesů (Scheduler)**: Rozděluje čas procesoru (CPU) mezi jednotlivá vlákna a procesy.
   - **Správa paměti**: Mapuje virtuální paměť procesů na fyzickou paměť RAM a spravuje stránkování (Paging).
   - **Správa souborového systému**: Poskytuje abstrakci souborů a adresářů nad fyzickými bloky disku.
3. **Ovladače zařízení (Device Drivers)**: Moduly, které překládají standardizované požadavky jádra na nízkoúrovňové signály konkrétního hardwaru. Často běží na rozhraní **HAL** (Hardware Abstraction Layer), které maskuje rozdíly mezi různými základními deskami a procesory.
4. **Systémová API a Knihovny**: Rozhraní pro vývojáře aplikací (např. knihovna `glibc` v Linuxu, moduly `Win32 API` ve Windows). Tvoří most mezi uživatelským prostorem a jádrem.
5. **System Call Interface (Rozhraní systémových volání)**: Kdykoliv aplikace v User Mode potřebuje provést privilegovanou operaci (např. zapsat soubor na disk nebo odeslat síťový paket), musí provést tzv. **System Call** (systémové volání). Tím vyvolá softwarové přerušení (interrupt), procesor bezpečně přepne z Ring 3 do Ring 0, předá řízení jádru, to operaci provede, přepne se zpět do Ring 3 a vrátí výsledek aplikaci.
6. **Uživatelské rozhraní (Shell / GUI)**: Nejsvrchnější vrstva v User Mode (např. Bash v Linuxu, Explorer.exe ve Windows), která umožňuje uživateli zadávat příkazy a spouštět programy. Pád shellu neohrozí běžící jádro ani ostatní procesy.

## Dělení OS podle různých kritérií
Operační systémy lze klasifikovat podle několika základních inženýrských a uživatelských charakteristik.

### 1. Podle architektury jádra (Kernel Design)
- **Monolitické jádro**: Celý operační systém (správa procesů, paměti, souborového systému i ovladače) je zkompilován do jediné obrovské binárky a běží společně v Kernel Space (Ring 0). Výhodou je extrémní rychlost díky přímému volání funkcí bez režie. Nevýhodou je, že chyba v jedné části (např. pád ovladače) způsobí kolaps celého systému (BSOD, Kernel Panic). Zástupce: **Linux**.
- **Mikrojádro (Microkernel)**: Osekává kód v Kernel Space na absolutní minimum (pouze IPC - meziprocesová komunikace, správa paměti a plánování CPU). Vše ostatní (ovladače, souborový systém) běží jako běžné procesy v User Space (Ring 3). Pád ovladače neohrozí jádro (pouze se restartuje příslušná služba), nevýhodou je však režie při komunikaci (IPC) a tím nižší rychlost. Zástupci: **QNX**, **MINIX**.
- **Hybridní jádro**: Kombinace monolitického a mikrojádra. Jádro je modulární a strukturované, ale z výkonnostních důvodů běží klíčové podsystémy (včetně grafiky a ovladačů) v Kernel Space. Zástupci: **Windows NT** (jádro Windows 10/11), jádro **XNU** (použité v macOS a iOS, kombinuje mikrojádro Mach a součásti BSD UNIXu).

### 2. Podle počtu uživatelů
- **Jednouživatelské (Single-user)**: V jednom okamžiku smí se systémem pracovat pouze jeden uživatel a existuje pouze jeden uživatelský profil. Příklad: **MS-DOS**, rané verze Windows (95, 98).
- **Víceuživatelské (Multi-user)**: Systém podporuje současnou práci více uživatelů (např. přes vzdálené terminály SSH, RDP) a zajišťuje striktní izolaci jejich dat a oprávnění. Příklad: **Linux**, **Windows NT** a moderní Windows (10, 11).

### 3. Podle schopnosti multitaskingu
- **Single-tasking**: Systém dokáže v jeden moment zpracovávat pouze jediný běžící proces. Dokud se program neukončí, nepustí k CPU nic jiného. Typicky **MS-DOS**.
- **Kooperativní multitasking**: Systém umožňuje spuštění více aplikací současně, ale ty se musí samy a dobrovolně vzdávat procesorového času a předávat jej dalším. Chyba v jedné aplikaci (zacyklení) způsobí zamrznutí celého OS. Příklad: **Windows 3.1 / 95**, starý Mac OS.
- **Preemptivní multitasking**: Jádro operačního systému (plánovač) plně ovládá přidělování CPU. Sám odebírá výpočetní čas procesům na základě priorit a časových kvant pomocí hardwarových přerušení. Pád aplikace neohrozí chod ostatních programů ani systému. Standard u **Windows NT**, **Linuxu** a macOS.

### 4. Podle reakční doby (Determinismu)
- **Time-Sharing OS (Dělení času)**: Běžné operační systémy (Windows, Linux, macOS). Cílem plánovače je rozdělit výkon spravedlivě mezi všechny aplikace tak, aby byla zajištěna plynulá odezva pro uživatele. Občasné zpoždění (latence) při zátěži nevadí.
- **RTOS (Real-Time OS / Operační systémy reálného času)**: Nasazované v kritických systémech (medicína, letectví, automobilový průmysl). Klíčový je **determinismus** – systém matematicky garantuje, že na konkrétní podnět odpoví v přesně stanoveném časovém limitu (např. v řádu mikrosekund), jinak hrozí havárie či ohrožení života. Zástupci: **QNX**, **VxWorks**, **FreeRTOS**.

### 5. Podle licenčního modelu a otevřenosti kódu
- **Proprietární (Uzavřené)**: Kód je chráněn autorským právem, binární kód je distribuován komerčně a uživatelé nemají přístup ke zdrojovému kódu. Příklad: **MS Windows**, **macOS**.
- **Open-source (Otevřené)**: Zdrojový kód je volně dostupný pod licencemi (např. GNU GPL, MIT, BSD), které umožňují kód prohlížet, upravovat, šířit a využívat zdarma. Příklad: **Linux (jádro a distribuce)**, **FreeBSD**.