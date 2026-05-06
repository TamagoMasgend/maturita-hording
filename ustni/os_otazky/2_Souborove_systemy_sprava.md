# 2. Souborové systémy a správa oprávnění v OS

## Koncept a účel souborového systému (File System)
Z hardwarového hlediska je paměťové médium (HDD, SSD) pouze rozsáhlý plochým prostorem izolovaných sektorů a datových bloků. Firmware disku vůbec netuší, co je to složka nebo fotka, zná pouze adresování bloků logickými čísly (LBA - Logical Block Addressing).

**Souborový systém** je proto kritická datová struktura a softwarová vrstva operačního systému, která těmto surovým blokům dává logický význam. Poskytuje abstrakci k ukládání a získávání dat. Souborový systém sdružuje sektory do takzvaných **clusterů** (alokačních jednotek), organizuje data do hierarchické stromové struktury, vede evidenci o volném i obsazeném místě (alokační tabulka) a k samotným uživatelským datům připojuje zásadní **metadata** (čas vytvoření, přístupová práva, velikost a vlastník).

Většina dnešních systémů obsahuje funkci **žurnálování (Journaling)**, kdy si OS ještě před samotným fyzickým zápisem souboru zapíše záměr o transakci do odděleného logu (žurnálu). Pokud během zápisu dojde k výpadku proudu, po restartu systém pouze přečte žurnál a ví přesně, který soubor zůstal nekompletní a jak obnovit konzistenci disku.

## Architektonická diverzita: Proč existují různé souborové systémy?
V inženýrské praxi neexistuje univerzální "dokonalý" souborový systém, protože různé aplikace a typy nosičů vyžadují radikálně odlišné přístupy. Systémy se historicky dělí podle jejich účelu:

1. **Podle vlastností hardwarového média**:
   - Paměťové karty (SD) a USB Flash disky nepotřebují složitá přístupová práva ani rozsáhlý žurnál, který by jen zbytečně opotřebovával paměťové buňky NAND. Proto zde dominuje lehký systém **exFAT**.
   - Společnost Apple vyvinula systém **APFS** s nativní optimalizací na to, jak funguje Flash paměť u NVMe disků v iPhonech a Macích.
2. **Historické a technologické limity**:
   - Prastarý systém **FAT32** obsahuje nepřekonatelný 32bitový limit – soubor na něm z principu matematiky nesmí být větší než 4 GB, což jej pro dnešní filmy v 4K činí nepoužitelným.
3. **Extrémní podnikové nasazení**:
   - Servery pracující s Big Daty vyžadují pokročilé funkce, které domácí PC nevyužijí: snapshotování v reálném čase, nativní softwarový RAID, deduplikaci a copy-on-write. V Linuxu tuto roli zastávají systémy **ZFS** a **Btrfs**.
4. **Standardy OS**:
   - Každé jádro (Kernel) se historicky vyvíjelo vlastním směrem. Windows spoléhá na robustní a žurnálovací systém **NTFS**, zatímco drtivá většina linuxových distribucí dnes instaluje rodinu souborových systémů **ext4** nebo výkonnostní **XFS**.

## Datové entity: Soubor versus Adresář
Navzdory vizuálnímu zpracování v GUI (kde adresář vypadá jako složka), je z hlediska inženýrství jádra operačního systému (zvláště patrné na architektuře UNIX/Linux) rozdíl zcela jiný.

- **Soubor (File)** je definován jako pojmenovaná kolekce souvisejících bajtů o konečné délce, uložená na paměťovém médiu. Nese v sobě samotná uživatelská data (text, binární kód, video) nebo data systémová.
- **Adresář (Directory)** ve skutečnosti není "místo, do kterého padají soubory". Z pohledu OS je adresář **speciální typ souboru**. Zatímco běžný soubor obsahuje text nebo video, soubor typu adresář neobsahuje uživatelská data, nýbrž pouze systémovou **asociativní tabulku (seznam)**. Tato tabulka funguje jako překladač: mapuje lidsky čitelná jména (např. "dokument.txt") na hardwarové datové struktury disku (v Linuxu nazývané **Inody** – Index Nodes), kde soubory fyzicky leží. 

## Vlastnictví a řízení přístupu
U víceuživatelských systémů by byla volná dostupnost všech souborů rozsáhlý bezpečnostní trhlinou. Operační systém proto váže každou entitu (soubor/adresář) na konkrétní bezpečnostní identitu – nejčastěji na konkrétního **Vlastníka (Owner)** a příslušnou nadřazenou **Skupinu (Group)**. Řízení přístupu (Access Control) se mezi dvěma hlavními OS propastně liší.

### Oprávnění v OS Windows (NTFS ACL)
Souborový systém NTFS přistupuje k bezpečnosti granulárně prostřednictvím seznamu **ACL (Access Control List)**. Každý soubor obsahuje rozsáhlý tabulku, kam může administrátor zapsat desítky přesných pravidel. Tyto položky, zvané ACE, umožňují detailní konfiguraci oprávnění:
- Dovolují povolovat (`Allow`) nebo absolutně zakazovat (`Deny`) práva ke čtení, zápisu, spouštění aplikací, měnění oprávnění nebo rovnou přidělit Úplné řízení (`Full Control`). 
- Práva v NTFS jsou silně **dědičná** (Inheritance). Složka zkopírovaná do jiného nadřazeného adresáře okamžitě přebírá ACL oprávnění této nadřazené složky. Identifikace uživatelů probíhá přes unikátní hash SID.

### Oprávnění v OS Linux (POSIX)
Linux vyznává historicky prastarý a striktnější matematický model POSIX oprávnění. Pro každý soubor a inod jsou v systému definovány pevně stanovené třídy uživatelů ve formátu `U G O` (User = vlastník, Group = přiřazená skupina, Others = úplně všichni ostatní uživatelé v systému). Pro každou tuto třídu lze vztyčit tři základní bity oprávnění:
- **r (read)** = právo číst data. Hodnota = 4.
- **w (write)** = právo soubor smazat či měnit. Hodnota = 2.
- **x (execute)** = právo binárku (nebo skript) reálně spustit do CPU. V případě adresáře toto právo dovoluje do něj "vstoupit" (příkazem cd). Hodnota = 1.
Bezpečnostní maska se udává v osmičkové soustavě. Příkladem je populární oprávnění **755** (7 pro U, 5 pro G, 5 pro O). V matematickém rozpadu to znamená `4+2+1 / 4+1 / 4+1`, tedy vlastník smí se souborem naprosto vše (číst, psát, spouštět), kdežto přidružená skupina a zbytek internetu mohou soubor pouze číst a spustit (ale už nezmění kód). 

## Terminálová správa OS
Všechny moderní operační systémy umí pro správu souborů a adresářů komunikovat s administrátorem přes přímé rozhraní příkazové řádky (CLI - shell).

### Příklady v OS Windows (CMD / PowerShell)
- Zobrazení obsahu aktuálního adresáře: `dir` (v moderním PowerShellu zástupný alias za `Get-ChildItem`)
- Změna cesty (vstup do jiné složky): `cd C:\Users\Admin\Dokumenty`
- Vytvoření nové složky: `mkdir Skolni_projekty`
- Vytvoření prázdného souboru a odeslání textu do něj: `echo "Hello World" > soubor.txt`
- Zkopírování souboru: `copy soubor.txt zaloha.txt`
- Smazání samotného souboru: `del soubor.txt`
- Smazání neprázdné složky (včetně podadresářů a bez ptaní): `rmdir /S /Q Skolni_projekty`

### Příklady v OS Linux (Bash)
- Výpis obsahu adresáře: `ls -la` (přepínače způsobí zobrazení seznamu do řádků `-l` včetně skrytých souborů `-a` a s výpisem POSIX práv u inodů).
- Změna pracovního adresáře: `cd /home/jan/Dokumenty` (příkaz `cd ..` vás hodí vždy o jednu složku zpět).
- Vytvoření struktury složek najednou: `mkdir -p /Skolni_projekty/Maturita` (přepínač `-p` vytvoří i neexistující rodiče v cestě).
- Vytvoření čistého prázdného souboru, či dotknutí se jeho časové značky: `touch soubor.txt`
- Zkopírování souboru: `cp soubor.txt zaloha.txt`
- Vymazání složky a jejího obsahu pomocí extrémní síly: `rm -rf /Skolni_projekty` (odstraní Recursive a s použitím Force).
- Změna oprávnění souboru tak, aby jej vlastník mohl spustit, a ostatní s ním nesměli nic (700): `chmod 700 skript.sh`
- Změna vlastníka a skupiny přes administrátorský účet: `sudo chown karel:vyvojari tajny_dokument.txt`