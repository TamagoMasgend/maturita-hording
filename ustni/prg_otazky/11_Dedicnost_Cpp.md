# 11. Dědičnost C++

**Dědičnost (inheritance)** je mechanismus OOP, který umožňuje nové třídě (**odvozené / child / derived**) přebrat atributy a metody existující třídy (**základní / parent / base**). Odvozená třída rozšiřuje nebo specializuje chování základní třídy bez duplikace kódu. Dědičnost modeluje vztah **„je to"** (is-a): `Zamestnanec` je `Osoba`, `Automobil` je `Vozidlo`.

## Syntaxe dědičnosti v C++

```cpp
class ZakladniTrida {
    // ...
};

class OdvozenaTrida : public ZakladniTrida {
    // ...
};
```

Přístupový modifikátor za dvojtečkou (`public`, `protected`, `private`) určuje, jak se změní viditelnost zděděných členů:

- **`: public`** — nejběžnější; `public` a `protected` členy zůstávají stejné.
- **`: protected`** — `public` členové základní třídy se stanou `protected` v odvozené.
- **`: private`** — všechny zděděné členy se stanou `private`.

```cpp
#include <iostream>
#include <string>
using namespace std;

// Základní (rodičovská) třída
class Osoba {
protected:
    string jmeno;  // protected — přístupný v odvozených třídách, ne zvenčí
    int vek;

public:
    Osoba(string j, int v) : jmeno(j), vek(v) {}   // konstruktor

    void vypis() const {
        cout << "Jmeno: " << jmeno << ", Vek: " << vek << endl;
    }
};

// Odvozená třída — dědí od Osoba
class Zamestnanec : public Osoba {
private:
    string pozice;  // vlastní atribut navíc
    double plat;

public:
    // Konstruktor odvozené třídy — řetězení na konstruktor základní třídy
    Zamestnanec(string j, int v, string poz, double pl)
        : Osoba(j, v), pozice(poz), plat(pl) {}

    void vypis() const {                         // překrytí (override) metody
        Osoba::vypis();                          // volání metody základní třídy
        cout << "Pozice: " << pozice
             << ", Plat: " << plat << " Kc" << endl;
    }

    string getPozice() const { return pozice; }
};

int main() {
    Zamestnanec z("Jan Novak", 35, "Technik", 55000.0);
    z.vypis();

    // Přístup přes ukazatel základní třídy — polymorfismus
    Osoba* ptr = &z;
    ptr->vypis();  // volá Osoba::vypis() — není virtual
    return 0;
}
```

## Konstruktor a inicializační seznam

Odvozená třída nemůže přímo inicializovat `protected` nebo `private` atributy základní třídy — musí zavolat konstruktor základní třídy přes **inicializační seznam** (za dvojtečkou za hlavičkou konstruktoru). Inicializační seznam je výkonnější než přiřazení v těle konstruktoru — atributy jsou inicializovány přímo, ne nejdříve výchozí hodnotou a pak přepsány.

```cpp
Zamestnanec(string j, int v, string poz, double pl)
    : Osoba(j, v),        // volání konstruktoru základní třídy
      pozice(poz),        // inicializace vlastního atributu
      plat(pl)
{}
```

## Virtuální metody a polymorfismus

Klíčové slovo **`virtual`** označí metodu základní třídy jako překryvatelnou. Odvozená třída ji překryje klíčovým slovem **`override`** (od C++11). Při volání přes ukazatel nebo referenci na základní třídu se zavolá správná verze — verze skutečného (dynamického) typu objektu, ne statického typu ukazatele. Toto je **dynamický polymorfismus**.

```cpp
class Tvar {
public:
    virtual double obsah() const {  // virtual — může být překryta
        return 0.0;
    }
    virtual ~Tvar() {}              // virtuální destruktor — povinný, pokud je virtual
};

class Kruh : public Tvar {
    double polomer;
public:
    Kruh(double r) : polomer(r) {}
    double obsah() const override {  // override — překrývá virtuální metodu
        return 3.14159 * polomer * polomer;
    }
};

class Obdelnik : public Tvar {
    double a, b;
public:
    Obdelnik(double a, double b) : a(a), b(b) {}
    double obsah() const override {
        return a * b;
    }
};

int main() {
    Tvar* tvary[2];
    tvary[0] = new Kruh(5.0);
    tvary[1] = new Obdelnik(4.0, 6.0);

    for (int i = 0; i < 2; i++) {
        cout << tvary[i]->obsah() << endl;  // volá správnou verzi dle skutečného typu
        delete tvary[i];
    }
    return 0;
}
```

## `protected` vs. `private` — zapouzdření při dědičnosti

- **`private`** atributy a metody základní třídy jsou **nepřístupné** v odvozené třídě. Odvozená třída je zdědí (zabírají paměť), ale nemůže je přímo číst ani modifikovat — musí použít veřejné nebo `protected` metody základní třídy. Realizuje plné zapouzdření.
- **`protected`** atributy jsou přístupné v odvozené třídě, ale stále skryté před vnějším kódem. Umožňují sdílení implementace při zachování zapouzdření vůči zbytku programu.

```cpp
class Zvire {
private:
    int vek;         // nepřístupné ani v odvozené třídě
protected:
    string jmeno;    // přístupné v odvozené třídě
public:
    Zvire(string j, int v) : jmeno(j), vek(v) {}
    int getVek() const { return vek; }  // přístup k private přes public metodu
};

class Pes : public Zvire {
public:
    Pes(string j, int v) : Zvire(j, v) {}
    void stekej() {
        cout << jmeno << " ste";  // OK — protected přístupný
        // cout << vek;           // CHYBA — private nepřístupný
        cout << getVek();         // OK — přes public metodu základní třídy
    }
};
```

## Volání metody základní třídy — `NazevTridy::metoda()`

V C++ neexistuje klíčové slovo `base` jako v C#. Metodu základní třídy se volá explicitně přes **operátor rozlišení oboru** (`::`) s názvem třídy:

```cpp
void Zamestnanec::vypis() const {
    Osoba::vypis();   // volá vypis() základní třídy Osoba
    cout << pozice << endl;
}
```

V C# se pro totéž použije klíčové slovo `base.Metoda()` — syntaxe je jednodušší a odpadá nutnost psát explicitní název třídy.

## Čistě virtuální metoda (Pure Virtual) a abstraktní třída

Metoda deklarovaná jako `virtual ... = 0` nemá implementaci v základní třídě. Třída obsahující alespoň jednu čistě virtuální metodu je **abstraktní** — nelze z ní vytvořit instanci. Slouží jako rozhraní, které odvozené třídy musí implementovat.

```cpp
class Tvar {
public:
    virtual double obsah() const = 0;  // čistě virtuální — musí být překryta
    virtual ~Tvar() {}
};
// Tvar t;  // CHYBA — abstraktní třída
```

## Vícenásobná dědičnost

C++ (na rozdíl od C#) povoluje vícenásobnou dědičnost — odvozená třída může dědit od více základních tříd. Přináší to komplexitu (problém diamantu), řeší se virtuální dědičností.
