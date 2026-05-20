---
created: 2026-05-06T10:17
updated: 2026-05-20T12:59
---
# 2. Souborové systémy a správa oprávnění v OS

## Koncept a účel souborového systému (File System)
Z hardwarového hlediska je paměťové médium (HDD, SSD) pouze rozsáhlým plochým prostorem izolovaných sektorů a datových bloků. Firmware disku vůbec netuší, co je to složka nebo fotka, zná pouze adresování bloků logickými čísly (LBA - Logical Block Addressing).

**Souborový systém** je proto kritická datová struktura a softwarová vrstva operačního systému, která těmto surovým blokům dává logický význam. Poskytuje abstrakci k ukládání a získávání dat. Souborový systém sdružuje sektory do takzvaných **clusterů** (alokačních jednotek), organizuje data do hierarchické stromové struktury, vede evidenci o volném i obsazeném místě (alokační tabulka) a k samotným uživatelským datům připojuje zásadní **metadata** (čas vytvoření, přístupová práva, velikost a vlastník).

- **Rozdělení disku (Partitioning) vs. Formátování (Formatting)**:
  - **Partitioning (Dělení disku)**: Před vytvořením souborového systému musí být disk rozdělen na jeden či více logických oddílů (partitions). K tomu se používají tabulky oddílů:
    - **MBR (Master Boot Record)**: Starší standard (1983). Podporuje max. 4 primární oddíly a velikost disku do 2 TB (kvůli 32bitovému adresování sektorů). Bootovací kód je uložen v prvním sektoru disku.
    - **GPT (GUID Partition Table)**: Moderní standard (součást UEFI). Podporuje prakticky neomezené množství oddílů (ve Windows typicky 128) a disky o velikostech v řádech ZB (Zettabajtů). Pro spolehlivost ukládá záložní kopii tabulky oddílů na konec disku a využívá CRC32 pro kontrolu integrity.
  - **Formatting (Formátování)**: Proces, při kterém se na konkrétním diskovém oddílu vytvoří prázdná struktura vybraného souborového systému (např. NTFS, ext4), čímž se disk připraví pro zápis dat.

Většina dnešních systémů obsahuje funkci **žurnálování (Journaling)**, kdy si OS ještě před samotným fyzickým zápisem souboru zapíše záměr o transakci do odděleného logu (žurnálu). Pokud během zápisu dojde k výpadku proudu, po restartu systém pouze přečte žurnál a ví přesně, který soubor zůstal nekompletní a jak obnovit konzistenci disku bez nutnosti kontrolovat celý disk.

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
- **Adresář (Directory)** ve skutečnosti není "místo, do kterého padají soubory". Z pohledu OS je adresář **speciální typ souboru**. Zatímco běžný soubor obsahuje samotná uživatelská data, adresář je seznamem odkazů mapujících uživatelské názvy souborů na příslušná ID diskových uzlů (např. inode čísla v Linuxu) či záznamy v alokační tabulce.

## Vlastnictví a řízení přístupu
U víceuživatelských systémů by byla volná dostupnost všech souborů rozsáhlou bezpečnostní trhlinou. Operační systém proto váže každou entitu (soubor/adresář) na konkrétní bezpečnostní identitu – nejčastěji na konkrétního **Vlastníka (Owner)** a příslušnou nadřazenou **Skupinu (Group)**. Řízení přístupu (Access Control) se mezi dvěma hlavními OS propastně liší.

### Oprávnění v OS Windows (NTFS ACL)
Souborový systém NTFS přistupuje k bezpečnosti granulárně prostřednictvím seznamu **ACL (Access Control List)**. Každý soubor či složka obsahuje tabulku složenou z jednotlivých záznamů **ACE (Access Control Entry)**, kam se zapisují přístupová pravidla spojená se SID uživatele či skupiny.
- Dovolují povolovat (`Allow`) nebo absolutně zakazovat (`Deny` - toto pravidlo má vždy vyšší prioritu) práva ke čtení, zápisu, spouštění aplikací, měnění oprávnění nebo rovnou přidělit Úplné řízení (`Full Control`).
- Práva v NTFS jsou standardně **dědičná** (Inheritance) z nadřazených složek, ale dědičnost lze na libovolné úrovni přerušit a definovat práva explicitně.
- **Terminálová správa (Příklady)**:
  - Zobrazení přístupových práv souboru: `icacls soubor.txt`
  - Udělení plného přístupu uživateli: `icacls soubor.txt /grant Jan:F` (F = Full Control)
  - Odebrání oprávnění konkrétnímu uživateli: `icacls soubor.txt /remove Jan`

### Oprávnění v OS Linux (POSIX)
Linux vyznává historický a striktnější matematický model POSIX oprávnění. Pro každý soubor a adresář jsou definovány pevně stanovené třídy ve formátu **UGO** (User = vlastník, Group = přiřazená skupina, Others = ostatní). Pro každou třídu lze nastavit tři základní bity:
- **r (read)** = právo číst data (u adresáře dovoluje vypsat obsah pomocí `ls`). Hodnota = 4.
- **w (write)** = právo soubor měnit či smazat (u adresáře dovoluje vytvářet a mazat soubory). Hodnota = 2.
- **x (execute)** = právo binárku či skript spustit (u adresáře dovoluje do něj vstoupit pomocí `cd`). Hodnota = 1.

Bezpečnostní maska se udává v osmičkové soustavě (např. **755** $\rightarrow$ vlastník `4+2+1=7` (rwx), skupina `4+1=5` (r-x), ostatní `4+1=5` (r-x)).

- **Jak číst výpis `ls -l`**:
  Výpis začíná deseti znaky, např. `-rwxr-xr--`:
  - **1. znak**: Typ souboru (`-` běžný soubor, `d` adresář, `l` symbolický odkaz/symlink).
  - **2.-4. znak (User)**: Oprávnění vlastníka – zde `rwx` (čtení, zápis, spuštění).
  - **5.-7. znak (Group)**: Oprávnění skupiny – zde `r-x` (čtení, spuštění).
  - **8.-10. znak (Others)**: Oprávnění pro ostatní – zde `r--` (pouze čtení).

- **Speciální bity oprávnění (Special Bits)**:
  - **SUID (Set User ID)**: (hodnota 4, symbolicky `u+s`) Spustí-li běžný uživatel program s tímto bitem, běží pod právy vlastníka souboru (často root). Typicky příkaz `passwd`.
  - **SGID (Set Group ID)**: (hodnota 2, symbolicky `g+s`) U adresářů zajišťuje, že všechny nově vytvořené soubory v něm automaticky zdědí skupinu tohoto adresáře (využívá se u sdílených složek).
  - **Sticky Bit**: (hodnota 1, symbolicky `+t`) Používá se na sdílené složky (např. `/tmp`). Zaručuje, že soubor může smazat pouze jeho vlastník, vlastník adresáře nebo root. Zabraňuje uživatelům mazat cizí soubory.

## Terminálová správa OS
Všechny moderní operační systémy umí pro správu souborů a adresářů komunikovat přes přímé rozhraní příkazové řádky (CLI - shell).

### Příklady v OS Windows (CMD / PowerShell)
- Zobrazení obsahu aktuálního adresáře: `dir` (v moderním PowerShellu zástupný alias za `Get-ChildItem`)
- Změna cesty (vstup do jiné složky): `cd C:\Users\Admin\Dokumenty`
- Vytvoření nové složky: `mkdir Skolni_projekty`
- Vytvoření prázdného souboru a odeslání textu do něj: `echo "Hello World" > soubor.txt`
- Zobrazení obsahu souboru na obrazovku: `type soubor.txt` (v PowerShellu `Get-Content`)
- Zkopírování souboru: `copy soubor.txt zaloha.txt`
- Přesunutí nebo přejmenování souboru: `move soubor.txt novy_nazev.txt`
- Vyhledání řetězce v souboru: `findstr "hledany_text" soubor.txt`
- Smazání samotného souboru: `del soubor.txt`
- Smazání neprázdné složky (včetně podadresářů a bez ptaní): `rmdir /S /Q Skolni_projekty`

### Příklady v OS Linux (Bash)
- Výpis obsahu adresáře: `ls -la` (zobrazí seznam jako řádky `-l` včetně skrytých souborů `-a` a POSIX práv).
- Změna pracovního adresáře: `cd /home/jan/Dokumenty` (příkaz `cd ..` skáče o složku výše).
- Vytvoření struktury složek najednou: `mkdir -p /Skolni_projekty/Maturita` (přepínač `-p` vytvoří chybějící rodičovské adresáře).
- Vytvoření čistého prázdného souboru: `touch soubor.txt`
- Zobrazení obsahu souboru na obrazovku: `cat soubor.txt` (pro stránkování velkých souborů `less` nebo `more`)
- Zkopírování souboru: `cp soubor.txt zaloha.txt`
- Přesunutí nebo přejmenování souboru: `mv soubor.txt novy_nazev.txt`
- Vyhledání řetězce v souboru: `grep "hledany_text" soubor.txt`
- Vymazání složky a jejího obsahu rekurzivně a silou: `rm -rf /Skolni_projekty` (Recursive a Force).
- Změna oprávnění souboru tak, aby jej vlastník mohl spustit, a ostatní s ním nesměli nic (700): `chmod 700 skript.sh`
- Změna vlastníka a skupiny přes administrátorský účet: `sudo chown karel:vyvojari tajny_dokument.txt`