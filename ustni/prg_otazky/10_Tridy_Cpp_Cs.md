# 10. Třídy C++ a C#

**Třída (`class`)** je základní stavební kámen objektově orientovaného programování (OOP). Definuje šablonu — nový složený datový typ — popisující data (**atributy**) a operace nad nimi (**metody**), které spolu logicky tvoří celek. **Objekt** je konkrétní instance třídy — proměnná vytvořená podle šablony třídy s vlastní kopií dat v paměti.

Třídy realizují tři klíčové principy OOP: **zapouzdření** (skrytí vnitřní implementace), **dědičnost** (přenos vlastností do odvozených tříd) a **polymorfismus** (různé chování stejně pojmenované metody v různých třídách).

## Přístupové modifikátory

Přístupové modifikátory řídí viditelnost členů třídy — které části programu smějí číst nebo modifikovat atributy a volat metody.

- **`private`** — člen je přístupný výhradně uvnitř těla třídy, kde je definován. Vnější kód ani odvozené třídy k němu nemají přístup. Výchozí úroveň pro členy třídy (`class`) v C++. Realizuje zapouzdření — chrání vnitřní stav objektu.
- **`public`** — člen je přístupný odkudkoli v programu. Tvoří **veřejné rozhraní (API)** třídy — sadu operací, které třída nabízí navenek.
- **`protected`** — člen je přístupný uvnitř třídy a v **odvozených (child) třídách**, ale ne z vnějšího kódu. Při práci s `protected` platí: odvozená třída může `protected` atribut číst a modifikovat přímo, jako by byl vlastní. Vnější kód (mimo třídu nebo její potomky) přístup nemá.

```cpp
class Banka {
protected:
    double zustatek;  // přístupný v odvozených třídách, ne venku
public:
    Banka(double z) : zustatek(z) {}
};
class SporiciUcet : public Banka {
public:
    SporiciUcet(double z) : Banka(z) {}
    void pridejUrok(double sazba) {
        zustatek *= (1 + sazba);  // OK — protected je přístupný
    }
    double getZustatek() { return zustatek; }
};
// SporiciUcet ucet(1000); ucet.zustatek = 500; // CHYBA — protected není public
```

## Definice třídy v C++

```cpp
#include <iostream>
#include <string>
using namespace std;

class Osoba {
private:
    string jmeno;   // atribut — data skrytá před vnějším kódem
    int vek;

public:
    // Konstruktor — stejné jméno jako třída, bez návratového typu
    Osoba(string j, int v) {
        jmeno = j;
        vek   = v;
    }

    // Metody — operace třídy (veřejné rozhraní)
    void vypis() const {             // const zaručuje, že metoda nemění objekt
        cout << jmeno << ", " << vek << " let" << endl;
    }

    // Getter — řízený přístup k privátnímu atributu
    string getJmeno() const { return jmeno; }
    int    getVek()   const { return vek; }

    // Setter — řízená modifikace s možností validace
    void setVek(int v) {
        if (v >= 0 && v <= 150) vek = v;
    }
};

int main() {
    Osoba o1("Jan Novak", 20);  // vytvoření instance — volání konstruktoru
    o1.vypis();                 // Jan Novak, 20 let
    o1.setVek(21);
    cout << o1.getVek();        // 21
    // o1.vek = 99;             // CHYBA — private člen není přístupný
    return 0;
}
```

## Destruktor v C++

Destruktor je speciální metoda s prefixem `~`, volaná automaticky při zániku objektu. Využívá se pro uvolnění ručně alokovaných prostředků (paměť na haldě, soubory, síťová spojení):

```cpp
class Buffer {
private:
    int* data;
public:
    Buffer(int velikost) { data = new int[velikost]; }
    ~Buffer() { delete[] data; }  // automatické uvolnění paměti
};
```

## Třídy v C#

C# používá syntakticky velmi podobnou strukturu. Klíčový rozdíl je, že C# je spravovaný jazyk — Garbage Collector uvolňuje paměť automaticky, destruktor (finalizer `~ClassName`) se volá nedeterministicky a ve většině případů se nepoužívá.

```csharp
using System;

class Osoba {
    private string jmeno;
    private int vek;

    // Konstruktor
    public Osoba(string j, int v) {
        jmeno = j;
        vek   = v;
    }

    // Metoda
    public void Vypis() {
        Console.WriteLine($"{jmeno}, {vek} let");
    }

    // C# Properties — elegantní náhrada getterů/setterů
    public string Jmeno {
        get { return jmeno; }
        set { jmeno = value; }
    }

    public int Vek {
        get { return vek; }
        set {
            if (value >= 0 && value <= 150) vek = value;
        }
    }
}

class Program {
    static void Main(string[] args) {
        Osoba o1 = new Osoba("Jan Novak", 20);
        o1.Vypis();
        o1.Vek = 21;
        Console.WriteLine(o1.Jmeno);
    }
}
```

### Auto-implemented Properties (C#)

Pro jednoduché vlastnosti bez validační logiky C# nabízí zkrácený zápis:

```csharp
class Produkt {
    public string Nazev { get; set; }       // automatický getter a setter
    public double Cena  { get; private set; }// setter přístupný jen uvnitř třídy
    public int    Id    { get; }            // pouze getter — readonly po inicializaci
}
```

## Klíčové slovo `this`

`this` je odkaz na aktuální instanci objektu. Využívá se pro rozlišení mezi parametrem a atributem třídy při shodném jménu:

```cpp
// C++
class Osoba {
    string jmeno;
public:
    void setJmeno(string jmeno) {
        this->jmeno = jmeno;  // this->jmeno = atribut, jmeno = parametr
    }
};
```

```csharp
// C#
class Osoba {
    string jmeno;
    public void SetJmeno(string jmeno) {
        this.jmeno = jmeno;
    }
}
```

## Statické členy (`static`)

Statický atribut nebo metoda patří třídě, nikoli konkrétní instanci — je sdílená napříč všemi objekty. Přistupuje se k nim přes jméno třídy, ne přes instanci.

```csharp
class Pocitadlo {
    private static int pocet = 0;
    public Pocitadlo() { pocet++; }
    public static int GetPocet() { return pocet; }
}
Console.WriteLine(Pocitadlo.GetPocet());  // přístup přes jméno třídy
```
