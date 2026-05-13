# 1. Rozdělení programovacích jazyků a základní pojmy

Programovací jazyk je formální systém symbolů a syntaktických pravidel, pomocí nichž vývojář specifikuje algoritmus — přesnou sekvenci instrukcí, které procesor vykoná. **Algoritmus** je konečná, jednoznačná posloupnost kroků vedoucí k řešení problému. **Program** je konkrétní implementace algoritmu v daném jazyce. Jazyky se třídí podle míry abstrakce nad hardwarem do dvou základních kategorií: **nízkoúrovňové** a **vysokoúrovňové**.

## Nízkoúrovňové jazyky (Low-Level Languages)

Nízkoúrovňové jazyky vznikly s prvními digitálními počítači ve 40. a 50. letech 20. století. Pracují na minimální úrovni abstrakce nad fyzickým hardwarem — instrukce jazyka přímo odpovídají nebo těsně mapují instrukce procesoru.

**Strojový kód (Machine Code)** je nejnižší forma zápisu programu. Procesor čte sekvenci binárních hodnot (0 a 1) a přímo je vykonává. Každá instrukce — přesun hodnoty do registru, aritmetická operace, podmíněný skok na adresu — má přiřazený binární operační kód (**opcode**). Přímé programování ve strojovém kódu je pro vývojáře prakticky nerealizovatelné: kód je nečitelný, nenese žádnou strukturu a je extrémně náchylný na chyby.

**Assembler (Assembly Language)** je symbolická reprezentace strojového kódu. Binární instrukce jsou nahrazeny krátkými mnemonikami: `MOV`, `ADD`, `SUB`, `JMP`, `CMP`, `PUSH`, `POP`. Speciální program — rovněž zvaný assembler — přeloží tento zápis do strojového kódu v poměru 1:1. Každá procesorová architektura (x86, ARM, RISC-V) má vlastní instrukční sadu a vlastní dialekt assembleru. Kód napsaný pro x86 nelze zkompilovat a spustit na ARMu — přenositelnost neexistuje.

### Vlastnosti nízkoúrovňových jazyků

- **Výhody:** přímá kontrola nad registry, pamětí a hardwarem; maximální výkon; minimální paměťová a výpočetní režie.
- **Nevýhody:** nulová přenositelnost mezi architekturami; extrémně nízká produktivita; stovky instrukcí pro triviální operaci; vysoká chybovost.

Assembler se dnes využívá výhradně v oblastech, kde je absolutní kontrola nad hardwarem nezbytná: firmware mikrokontrolérů, bootloadery, jádra operačních systémů a výkonnostně kritické kryptografické rutiny.

## Vysokoúrovňové jazyky (High-Level Languages)

Vysokoúrovňové jazyky zavádějí výrazně vyšší úroveň abstrakce. Vývojář pracuje s proměnnými, funkcemi, třídami a datovými strukturami místo s registry a přímými adresami paměti. Syntaxe je blíže matematickému nebo přirozenému jazyku. Překlad do strojového kódu zajišťuje specializovaný program — **kompilátor** nebo **interpret**.

### Kompilátor (Compiler)

Kompilátor přečte celý zdrojový kód a přeloží ho do nativního binárního spustitelného souboru (`.exe`, `.out`) **před** samotným spuštěním programu. Výsledný soubor obsahuje pouze strojový kód — zdrojový kód je skrytý a chráněný před čtením třetích stran. Přeložený program běží rychle, protože překlad proběhl jediný čas.

Klíčovým omezením je, že zkompilovaný soubor je vázán na konkrétní platformu a operační systém. Program zkompilovaný pro Windows (formát `PE/COFF`, architektura x86-64) nelze přímo spustit na Linuxu (formát `ELF`) ani na macOS (formát `Mach-O`). Každá cílová platforma vyžaduje samostatnou kompilaci specifickým kompilátorem. Typické kompilované jazyky: **C, C++, Rust, Go**.

### Interpret (Interpreter)

Interpret čte a vykonává zdrojový kód **instrukci po instrukci** za běhu programu bez předchozího překladu celku. Zdrojový kód zůstává ve čitelné textové podobě — nelze ho skrýt. To usnadňuje ladění, sdílení a multiplatformní nasazení, ale komplikuje ochranu duševního vlastnictví.

Interpretované programy jsou zpravidla pomalejší, protože překlad každé instrukce se opakuje při každém spuštění. Moderní interprety tuto nevýhodu částečně eliminují **JIT kompilací (Just-In-Time)**, kdy se frekventované části kódu zkompilují do nativního strojového kódu přímo za běhu. Typické interpretované jazyky: **Python, JavaScript, Ruby**.

Hybridní přístup kombinuje obojí — kompilaci do přenosného mezikódu s JIT za běhu: **Java** (zdrojový kód → bytecode → JVM) a **C#** (zdrojový kód → CIL/MSIL → CLR .NET runtime). Mezikód je přenositelný mezi platformami, ale ke spuštění vyžaduje nainstalovaný runtime.

## Programovací paradigmata

Jazyky se třídí rovněž podle paradigmatu — způsobu, jakým vývojář strukturuje řešení problému.

### Procedurální a strukturované programování

Program je organizován jako sekvence příkazů a pojmenovaných podprogramů (**procedur** nebo **funkcí**). Strukturované programování je podmnožinou zakazující nekontrolovaný skok `GOTO` — řízení toku zajišťují výhradně podmínky (`if`) a cykly (`for`, `while`). Kód je lineárně sledovatelný a přehledný. Zástupci: **C, Pascal, BASIC**.

### Objektově orientované programování (OOP)

OOP organizuje kód kolem **objektů** — instancí tříd, které sdružují data (**atributy**) a operace nad nimi (**metody**) do jednoho zapouzdřeného celku. Klíčové principy jsou:

- **Zapouzdření (Encapsulation):** data objektu jsou skrytá před vnějším přístupem; přístup je řízen přes metody.
- **Dědičnost (Inheritance):** nová třída přebírá atributy a metody třídy nadřazené — eliminuje duplicitu kódu.
- **Polymorfismus (Polymorphism):** různé třídy mohou sdílet stejné rozhraní (název metody) a každá ho implementuje vlastním způsobem.

**C++** je multiparadigmatický jazyk. Plně podporuje procedurální přístup (zpětná kompatibilita s C) i kompletní OOP. Vývojář volí paradigma dle potřeby a obě lze kombinovat v jednom projektu.

**C#** je čistě OOP jazyk spravovaný Microsoftem, běžící na platformě **.NET**. Veškerý kód musí být organizován uvnitř tříd — samostatné funkce mimo třídu neexistují. Kompilátor jazyka C# generuje přenosný mezikód CIL, který .NET CLR runtime přeloží pomocí JIT do nativního strojového kódu.

Volba paradigmatu závisí na doméně: systémové nástroje, ovladače a výkonnostně kritické aplikace preferují procedurální C nebo multiparadigmatické C++. Rozsáhlé podnikové aplikace, GUI frameworky a webové backendy využívají OOP C# nebo Javu pro jejich organizační a údržbové výhody.
