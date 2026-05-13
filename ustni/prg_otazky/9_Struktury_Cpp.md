# 9. Struktury C++

**Struktura (`struct`)** je uživatelsky definovaný složený datový typ, který sdružuje libovolný počet proměnných různých datových typů pod jedním pojmenovaným celkem. Umožňuje modelovat reálné entity s více atributy — například osobu (jméno + věk + výška) jako jedinou proměnnou namísto tří separátních proměnných.

## Definice struktury

Struktury se definují v **globálním prostoru** (mimo jakoukoliv funkci nebo třídu), aby byly přístupné z celého souboru nebo programu. Definice popisuje šablonu — samu o sobě nealokuje žádnou paměť. Paměť se alokuje až při deklaraci proměnné daného strukturního typu.

```cpp
#include <iostream>
#include <string>
using namespace std;

// Definice v globálním prostoru — šablona datového typu
struct Osoba {
    string jmeno;
    int vek;
    double vyska;      // v metrech
};  // středník na konci definice je povinný

int main() {
    // Deklarace proměnné typu Osoba — teprve zde se alokuje paměť
    Osoba student;

    // Přístup k členům přes operátor tečky (.)
    student.jmeno = "Jan Novak";
    student.vek   = 20;
    student.vyska = 1.82;

    cout << student.jmeno << ", " << student.vek << " let" << endl;
    return 0;
}
```

## Inicializace struktury

Strukturu lze inicializovat agregátní inicializací pomocí složených závorek `{}` — hodnoty se přiřadí členům v pořadí deklarace:

```cpp
Osoba ucitel = {"Marie Svobodova", 45, 1.68};

// Nebo konkrétní pojmenování (C++20 designated initializers)
Osoba reditel = {.jmeno = "Petr Kolar", .vek = 52, .vyska = 1.75};
```

## Konstruktor struktury

I přestože je `struct` primárně určena pro data, může v C++ obsahovat **konstruktor** — speciální funkci bez návratového typu, jejíž jméno je shodné se jménem struktury. Konstruktor se vykoná automaticky při vytvoření instance a zajistí inicializaci členů na definované výchozí hodnoty.

```cpp
struct Bod {
    double x;
    double y;

    // Konstruktor
    Bod(double px, double py) {
        x = px;
        y = py;
    }

    // Výchozí konstruktor — bez parametrů
    Bod() {
        x = 0.0;
        y = 0.0;
    }
};

int main() {
    Bod b1(3.5, 7.2);   // volá parametrický konstruktor
    Bod b2;             // volá výchozí konstruktor — x=0, y=0
    cout << b1.x << ", " << b1.y << endl;
    return 0;
}
```

## Struktura vs. třída (`class`)

V C++ je rozdíl mezi `struct` a `class` jediný — výchozí přístupová úroveň členů:

| | `struct` | `class` |
|---|---|---|
| Výchozí přístup | **`public`** | `private` |
| Může mít konstruktor | Ano | Ano |
| Může mít metody | Ano | Ano |
| Může dědit | Ano | Ano |

Z konvence se `struct` používá výhradně pro jednoduché datové kontejnery bez složité funkcionality — tzv. **POD (Plain Old Data)**. Jakmile entita vyžaduje zapouzdření, metody nebo řízení přístupu, upřednostňuje se `class`.

## Pole struktur

Struktury lze skládat do polí pro reprezentaci více entit stejného typu:

```cpp
const int POCET = 3;
Osoba trida[POCET] = {
    {"Jan",  18, 1.80},
    {"Eva",  17, 1.65},
    {"Tomas",19, 1.75}
};

for (int i = 0; i < POCET; i++) {
    cout << trida[i].jmeno << ": " << trida[i].vek << endl;
}
```

## Předávání struktury funkci

Předání hodnotou zkopíruje celou strukturu — vhodné jen pro malé struktury. Pro velké struktury nebo nutnost modifikace originálu se předává referencí:

```cpp
// Čtení bez kopie — const reference (efektivní a bezpečné)
void vypis(const Osoba& o) {
    cout << o.jmeno << ", " << o.vek << endl;
}

// Modifikace originálu — reference bez const
void narozeniny(Osoba& o) {
    o.vek++;
}

Osoba jan = {"Jan", 20, 1.80};
vypis(jan);
narozeniny(jan);
cout << jan.vek;  // 21
```

## Vnořené struktury

Struktury mohou být vnořeny — jeden člen je sám strukturou:

```cpp
struct Adresa {
    string ulice;
    string mesto;
    int psc;
};

struct Zamestnanec {
    string jmeno;
    Adresa bydliste;   // vnořená struktura
    double plat;
};

Zamestnanec z;
z.jmeno = "Novak";
z.bydliste.mesto = "Praha";
z.bydliste.psc   = 11000;
```
