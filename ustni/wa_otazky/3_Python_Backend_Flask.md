# 3. Programování backendu webových aplikací v Pythonu

## Kompilované versus Skriptovací a Interpretované jazyky

Při zpracování zdrojového kódu (lidsky čitelného zápisu z ASCII/UTF-8 znaků) a jeho finální transformaci na strojové instrukce procesoru (jedničky a nuly) se softwarové inženýrství rozděluje do dvou naprosto odlišných architektonických táborů. Tím je určen jak výkon, tak způsob distribuce aplikací.

1. **Kompilované jazyky (C, C++, Rust, Go, Pascal)**:
   V tomto konceptu se zdrojový kód neprovádí okamžitě. Vývojář použije komplexní softwarový nástroj zvaný **Kompilátor** (Compiler – např. gcc nebo clang). Kompilátor projde celý kód najednou, provede rozsáhlé optimalizace, zkontroluje datové typy a staticky jej zkompiluje do platformově závislé, pevně dané spustitelné binárky (např. soubor `.exe` ve Windows PE formátu, nebo `.elf` v Linuxu).
   - **Výhody**: Jakmile je binárka vytvořena, k jejímu běhu na cílovém serveru už kompilátor ani zdrojový kód absolutně nejsou potřeba. Kód je tím chráněn před kopírováním a především nabízí extrémní, surovou rychlost běhu přímo v křemíku CPU, bez jakýchkoliv překladových prostředníků.
   - **Nevýhody**: Dlouhá doba nutná ke kompilaci větších projektů před každým spuštěním (často desítky minut). Výsledný program je vázán na konkrétní architekturu a operační systém (binárku zkompilovanou pro Windows x86 procesor nespustíte na Linuxu s ARM procesorem, musíte provést tzv. Křížovou kompilaci).

2. **Skriptovací / Interpretované jazyky (Python, JavaScript, PHP, Ruby)**:
   Zdrojový kód se zde nekompiluje předem do samostatného binárního souboru cílové platformy. Zákazníkovi či serveru se distribuuje přímo originální, textový zdrojový kód. K jeho běhu je bezpodmínečně nutné nainstalovat překladový program – **Interpret** (např. CPython, Node.js V8, PHP Zend Engine). Kód se vyhodnocuje a převádí do strojových instrukcí sekvenčně, řádek po řádku, až v reálném čase za běhu.
   - U jazyka Python je architektura mírně složitější. Kód není interpretován jako čistý text, ale CPython jej nejprve bleskově (a nepozorovaně) transformuje do nízkoúrovňového abstraktního formátu zvaného **Bytecode** (skryté `.pyc` soubory ve složce `__pycache__`). Tento Bytecode následně zpracovává izolační vrstva zvaná PVM (Python Virtual Machine).
   - **Výhody**: Absolutní přenositelnost napříč operačními systémy. Stačí stejný textový skript překopírovat z Windows na Linux či Mac, a pokud je tam instalován interpret, program okamžitě běží bez úprav (Write once, run anywhere). Zajišťuje enormně rychlé vývojové iterace (okamžité testování bez čekání na kompilátor).
   - **Nevýhody**: Z principu věci a neustálé režie překladového enginu je výpočetní čas rozsáhlý pomalejší. U matematicky intenzivních operací je Python oproti C++ i stonásobně pomalejší. U Pythonu do výkonu zasahuje i nechvalně známý koncept **GIL (Global Interpreter Lock)**, což je mutex v jádře CPythonu, který zamezuje paralelnímu provádění instrukcí z více vláken současně (z důvodu nebezpečí poškození Garbage Collectoru sčítajícího reference paměti).

## Základní programové konstrukce v jazyce Python

Python je vysokoúrovňový jazyk s **dynamickým a silným typováním** (proměnná automaticky přijme datový typ z přiřazené hodnoty, aniž by se musela deklarovat typem předem, ale nelze sčítat string s integerem). Z architektonického hlediska nevyužívá pro ohraničení bloků kódu tradiční složené závorky `{}` nebo slova `BEGIN/END`, ale využívá tvrdé sémantické pravidlo odsazování (Indentation – typicky 4 mezery nebo 1 tabulátor). Pokud je v Pythonu blok špatně odsazen, nejedná se o estetickou vadu, program zcela spadne s chybou `IndentationError`.

### 1. Primitivní a složené datové typy

Data musí mít uložena v RAM svůj exaktní typový formát pro procesor.

- **Skalární typy**:
  - `int` (Celá čísla bez paměťového limitu 64bitu: `pocet_kroku = 105`)
  - `float` (Desetinná čísla podle standardu IEEE 754: `teplota = -15.6`)
  - `str` (Nezměnitelné textové řetězce v kódování Unicode: `jmeno = "Jan"`)
  - `bool` (Logické booleovské stavy: `aktivni = True` nebo `False`)
- **Kolekce a Datové struktury**:
  - `list` (Seznam): Dynamické, heterogenní a měnitelné (mutable) pole hodnot. Systém za něj alokuje pole ukazatelů. Indexuje se matematicky od nuly. `servery = ["web-01", "db-master", "proxy"]`
  - `tuple` (N-tice): Rozšířený seznam, který je však uzamčen a po vytvoření naprosto nezměnitelný (immutable). Nabízí se jako ochrana kritických dat. `sql_port = (192, 168, 1, 1, 3306)`
  - `dict` (Slovník): Extrémně výkonná struktura pracující s neindexovanými páry _Klíč: Hodnota_. Pod povrchem v paměti RAM implementována jako hašovací tabulka zajišťující asymptotickou časovou složitost prohledávání v čase O(1). `uzivatel = {"id": 404, "role": "admin", "login": "root"}`

### 2. Větvení kódu (Podmínečné skoky)

Slouží k deterministickému větvení logického algoritmu na základě booleovské pravdy (vyhodnocení výrazu na True/False). V CPU se po kompilaci překládají jako logické skoky.

```python
# Nasimulování hodnoty síťového pingu
ping_latence = 150

# Logické větvení IF - ELIF - ELSE se striktním odsazováním
if ping_latence < 20:
    print("Excelentní odezva sítě.")
elif ping_latence <= 100:
    print("Stabilní spojení, možný drobný lag.")
else:
    # Pokud nevyhoví ani jedna podmínka nad tímto řádkem, spadne program vždy sem
    print("Kritické zpoždění, síť je přetížená!")
```

### 3. Iterace a Cykly

Strojově opakují zadaný blok instrukcí. Python podporuje dva fundamentální typy cyklů a klíčová slova `break` pro nouzové opuštění smyčky a `continue` pro okamžitý přeskok aktuálního zpracování a posun na další prvek v iteraci.

```python
# Cyklus FOR (Definitní iterace): Určený primárně pro postupné procházení přes kolekce.
databaze = ["MySQL", "PostgreSQL", "Redis", "MongoDB"]
for db_engine in databaze:
    if db_engine == "Redis":
        continue  # Přeskočí in-memory slovník a nevypíše jej, skočí zpět nahoru
    print(f"Spouštím tradiční databázi na disk: {db_engine}")

# Cyklus WHILE (Indefinitní iterace): Běží v kruhu tak dlouho, dokud je logická podmínka True.
pokusy_o_pripojeni = 0
while pokusy_o_pripojeni < 3:
    print("Zkouším navázat TCP soket...")
    pokusy_o_pripojeni += 1  # Inkrementace pojistky, jinak vznikne nekonečný zacyklený pád programu
```

### 4. Definice funkce a návratové hodnoty

Funkce jsou architektonickým blokem k podpoře modifikace kódu (princip DRY - Don't Repeat Yourself). Izolují konkrétní, často volanou rutinu do jednoho jmenného obalu. Definují se rezervovaným slovem `def`. Vstupní parametry lze specifikovat volitelně včetně typových nápověd (Type Hints), výsledek proces vrací operační paměti do místa volání klauzulí `return`.

```python
# Funkce pro výpočet daně s typovými nápovědami a přednastavenou výchozí hodnotou parametru (sazba 21 %)
def vypocitej_dan(cena_bez_dph: float, sazba: float = 0.21) -> float:
    vypocet = cena_bez_dph + (cena_bez_dph * sazba)
    return vypocet  # Předá výsledek zpracování zpět a funkci bezpečně ukončí

# Volání funkce (argument 'sazba' se automaticky doplní na výchozích 0.21)
konecna_faktura = vypocitej_dan(1000.0)
print(f"Cena po zdanění činí: {konecna_faktura} Kč")
```

## Architektura projektu v mikro-frameworku Flask

Pro běh backendové aplikace v jazyce Python není nutné složitě konfigurovat systémové sokety nad TCP/IP pro příjem dat (HTTP Requestů). Na to slouží obalové aplikační platformy (Frameworky). **Flask** patří do kategorie _Mikro-frameworků_. Na rozdíl od rozsáhlý monolitického frameworku Django, Flask vývojáři nediktuje žádnou předdefinovanou databázi (ORM) ani složitou strukturu desítek složek. Poskytuje pouhé minimalistické jádro k obsluze sítě.

Architektonicky se staví na průmyslovém rozhraní **WSGI (Web Server Gateway Interface)**, což je komunikační předpis v jazyce Python (konkrétně v knihovně Werkzeug zprostředkovávající toky), který umožňuje ostrému internetovému serveru (např. Nginx ve spolupráci s Gunicorn) vzbudit v RAM paměti Python kód, přesunout do něj síťový dotaz a převzít od něj textovou odpověď pro klienta. Pro generování dynamických HTML stránek Flask integruje šablonovací engine **Jinja2**.

Konvenční a logická struktura adresářů produkčního mikro-projektu vypadá následovně:

1. `app.py` (případně `main.py`): Hlavní vstupní bod programu (Entrypoint). V tomto souboru instanciujeme Flask aplikaci a nacházejí se zde tzv. _Routy_ – funkce namapované pomocí dekorátorů (např. `@app.route`) na konkrétní URL adresy (end-pointy). Zde se nachází veškerá byznys logika, dotazy do databáze a podmínky předávající data do zobrazení.
2. `templates/`: Složka vyhrazená exkluzivně pro `.html` soubory šablon. Vykreslovač Jinja2, integrovaný do příkazu `render_template()`, do těchto HTML šablon dokáže promítnout Python proměnné předané z backendu pomocí dvojitých složených závorek (např. `<h1>Vítejte, {{ jmeno_uzivatele }}</h1>`). Umožňuje také cykly přímo v HTML pro vykreslení tabulky řádků z databáze bez psaní složitého JavaScriptu.
3. `static/`: Bezpečnostní izolovaná složka vyhrazena pro statické asety. Všechny soubory umístěné zde se nijak neprovádějí v procesoru serveru, ale jsou odeslány surově přímo klientovi (do jeho prohlížeče): CSS soubory stylů, klientský JavaScript, fonty a obrázky.

**Základní, minimalistická praktická ukázka backendového kódu (`app.py`):**

```python
from flask import Flask, render_template

# 1. Vytvoření instance globální WSGI aplikace
app = Flask(__name__)

# 2. Definice statické Routy.
# Dekorátor mapuje rootovou adresu domény (lomítko) na následující funkci.
@app.route('/')
def homepage():
    # Vrací prostý textový string, prohlížeč mu tiše vnutí hlavičku HTTP 200 OK.
    return "<h1>Jádro backendové infrastruktury aktivní.</h1>"

# 3. Definice dynamické Routy.
# Adresa chytá proměnnou část (user_id) poslanou v řádku prohlížeče a vkládá ji do parametru funkce.
@app.route('/uzivatel/<int:user_id>')
def user_profile(user_id):
    # Provede imaginární dotaz do DB a následně renderuje HTML soubor 'profile.html' ze složky templates/
    # Přidává proměnnou, kterou Jinja2 propálí přímo do HTML těla dokumentu.
    return render_template("profile.html", hledane_id=user_id)

# 4. Spouštěcí klauzule
if __name__ == '__main__':
    # Spuštění interního developerského Werkzeug serveru na všech interfasech (0.0.0.0) na portu 5000.
    # V produkčním cloudu tento kód nespouští přímo Python, ale WSGI server typu Gunicorn/uWSGI.
    app.run(host='0.0.0.0', port=5000, debug=True)
```
