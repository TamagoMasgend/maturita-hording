# 2. Proměnné a datové typy

**Proměnná** je pojmenovaná oblast operační paměti (RAM), do níž program ukládá a ze které čte data za běhu. Každá proměnná má: **jméno** (identifikátor), **datový typ** (určuje velikost v paměti a povolené operace) a **hodnotu** (aktuálně uložená data). Bez proměnných by nebylo možné ukládat mezivýsledky, vstupní data ani stav programu.

## Deklarace a inicializace

**Deklarace** proměnné informuje kompilátor o jejím jméně a datovém typu — vyhradí se místo v paměti. **Inicializace** je přiřazení první hodnoty. Lze je provést odděleně nebo najednou:

```cpp
// C++ — deklarace bez inicializace (hodnota je nedefinovaná/garbage)
int pocet;
// C++ — deklarace s inicializací
int pocet = 10;
double teplota = 36.6;
bool aktivni = true;
```

```csharp
// C# — proměnná musí být inicializována před prvním použitím (kompilátor hlídá)
int pocet = 10;
string jmeno = "Novak";
```

V C++ proměnná bez inicializace obsahuje tzv. **garbage value** — náhodný obsah z předchozího obsazení dané paměťové adresy. Přístup k neinicializované proměnné je nedefinované chování. V C# kompilátor použití neinicializované lokální proměnné odmítne přeložit.

## Základní datové typy a jejich velikosti

Velikost datového typu určuje, kolik bajtů (B) zaujme v paměti a jaký rozsah hodnot může reprezentovat. **1 bajt = 8 bitů.** Na n bitech lze uložit 2ⁿ různých hodnot.

### Celá čísla (Integer types)

| Typ | Velikost | Rozsah (se znaménkem) |
|---|---|---|
| `char` | 1 B (8 b) | -128 až 127 |
| `short` | 2 B (16 b) | -32 768 až 32 767 |
| `int` | 4 B (32 b) | -2 147 483 648 až 2 147 483 647 |
| `long long` | 8 B (64 b) | ±9,2 × 10¹⁸ |

Varianty s prefixem `unsigned` posunou rozsah na kladná čísla (dvojnásobný maximální rozsah, nula jako minimum). Například `unsigned int`: 0 až 4 294 967 295.

### Čísla s plovoucí desetinnou čárkou (Floating-point)

- **`float`** — 4 B (32 b), přesnost přibližně 7 platných číslic (IEEE 754 single precision).
- **`double`** — 8 B (64 b), přesnost přibližně 15–16 platných číslic (IEEE 754 double precision). Výchozí typ pro reálná čísla.
- **`long double`** — 8 až 16 B dle platformy.

### Logický typ

- **`bool`** — 1 B; nabývá hodnot `true` (1) nebo `false` (0). V C++ je `bool` samostatný typ, v C# rovněž (`bool`, ne `int`).

### Znakový typ a textový řetězec

- **`char`** — 1 B; uloží jeden ASCII znak (0–127). Literál se zapisuje v apostrofech: `'A'`.
- **`string`** — v C++ je to objekt ze standardní knihovny (`std::string`), v C# vestavěný referenční typ. Řetězcový literál se zapisuje v uvozovkách: `"text"`.

## Zjištění velikosti proměnné v paměti

V C++ slouží operátor **`sizeof`**, který vrátí počet bajtů, jež typ nebo proměnná zaujímají v paměti:

```cpp
#include <iostream>
using namespace std;
int main() {
    cout << "int: "    << sizeof(int)    << " B" << endl; // 4
    cout << "double: " << sizeof(double) << " B" << endl; // 8
    cout << "char: "   << sizeof(char)   << " B" << endl; // 1
    int x = 5;
    cout << "x: " << sizeof(x) << " B" << endl; // 4
}
```

V C# lze použít `sizeof` pro primitivní typy ve `unsafe` kontextu, nebo metodu `Marshal.SizeOf<T>()`.

## 32bitové vs. 64bitové systémy

Šířka datové sběrnice procesoru a adresního prostoru určuje, kolik paměti může program adresovat a jak velké jsou nativní ukazatele:

- **32bitový systém:** ukazatel (`pointer`) má velikost **4 B**, maximální adresovatelná RAM je 2³² = 4 GB.
- **64bitový systém:** ukazatel má velikost **8 B**, maximální adresovatelný prostor je 2⁶⁴ ≈ 1,8 × 10¹⁹ B (prakticky neomezený pro běžné aplikace).

Typ `int` zůstává na obou systémech 4 B (standard jazyka C/C++). Typ `long` má na 64bitovém Linuxu 8 B, ale na 64bitovém Windows stále jen 4 B — závisí na platformě a kompilátoru. Pro přesnost je vhodné použít typy z hlavičky `<cstdint>`: `int32_t`, `int64_t`, `uint8_t` apod.

## Konstanty

**Konstanta** je pojmenovaná hodnota, která se po inicializaci nesmí měnit. Zajišťuje, že kritické hodnoty (matematické konstanty, limity) nebudou omylem přepsány.

```cpp
// C++ — klíčové slovo const
const double PI = 3.14159265358979;
const int MAX_VELIKOST = 100;
// Pokus o přiřazení způsobí chybu kompilátoru:
// PI = 3.0;  // ERROR: assignment of read-only variable
```

```csharp
// C# — klíčové slovo const (vyhodnoceno v čase kompilace)
const double PI = 3.14159265358979;
// C# — readonly (vyhodnoceno za běhu, lze inicializovat v konstruktoru)
readonly int maxVelikost;
```

Rozdíl mezi `const` a `readonly` v C#: `const` musí být inicializována hodnotou známou v době kompilace, `readonly` lze inicializovat v konstruktoru třídy za běhu.

## Proč používat různé datové typy?

Použití jednoho univerzálního datového typu (např. `double` pro vše) je nevhodné ze dvou důvodů. Za prvé, zbytečně plýtvá pamětí — uložit příznak `true/false` do 8bajtového `double` je nehospodárné. Za druhé, různé typy umožňují různé operace a zabraňují logickým chybám: `bool` nelze přímo sčítat jako číslo, `char` nelze použít jako celé číslo bez explicitní konverze. Kompilátor díky typovému systému odhalí řadu chyb ještě před spuštěním programu.

## Konvence pojmenování

- **C++:** `camelCase` nebo `snake_case` — `celkovaSuma`, `celkova_suma`.
- **C#:** lokální proměnné `camelCase`, veřejné členy `PascalCase` — `celkovaSuma`, `CelkovaSuma`.
- Identifikátor musí začínat písmenem nebo podtržítkem, nesmí být klíčové slovo jazyka.
