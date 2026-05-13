# 12. Dědičnost C#

**Dědičnost** v C# je mechanismus OOP umožňující odvozené třídě přebrat atributy a metody třídy základní. C# podporuje výhradně **jednoduchou dědičnost** — třída může mít vždy jen jednu přímou základní třídu. Namísto vícenásobné dědičnosti tříd C# používá **rozhraní (`interface`)**, přičemž třída může implementovat libovolný počet rozhraní.

Syntaxe dědičnosti v C# je oproti C++ zjednodušená — přístupový modifikátor se u dědičnosti nepíše, je vždy implicitně `public`.

## Syntaxe a klíčové slovo `base`

```csharp
class ZakladniTrida {
    // ...
}

class OdvozenaTrida : ZakladniTrida {
    // ...
}
```

Klíčové slovo **`base`** v C# slouží pro explicitní přístup k členům základní třídy z odvozené třídy:

- `base(parametry)` — v konstruktoru odvozené třídy volá konstruktor základní třídy.
- `base.MetodaNazev()` — volá metodu základní třídy (i když je v odvozené překryta).

```csharp
class Osoba {
    protected string jmeno;
    protected int vek;

    public Osoba(string j, int v) {
        jmeno = j;
        vek   = v;
    }

    public virtual void Vypis() {      // virtual — metoda může být v odvozené třídě překryta
        Console.WriteLine($"Jmeno: {jmeno}, Vek: {vek}");
    }
}

class Zamestnanec : Osoba {
    private string pozice;
    private double plat;

    // base(j, v) přesměruje volání na konstruktor základní třídy Osoba
    public Zamestnanec(string j, int v, string poz, double pl)
        : base(j, v) {
        pozice = poz;
        plat   = pl;
    }

    // override — překrývá virtual metodu základní třídy
    public override void Vypis() {
        base.Vypis();              // nejprve zavolá metodu základní třídy
        Console.WriteLine($"Pozice: {pozice}, Plat: {plat} Kc");
    }
}

class Program {
    static void Main() {
        Zamestnanec z = new Zamestnanec("Jan Novak", 35, "Technik", 55000.0);
        z.Vypis();

        // Polymorfismus — ukazatel/reference základní třídy na objekt odvozené
        Osoba o = z;
        o.Vypis();  // volá Zamestnanec.Vypis() — virtual dispatch
    }
}
```

## `virtual` a `override`

- **`virtual`** — označuje metodu v základní třídě jako překryvatelnou. Bez `virtual` překrytí není skutečným polymorfismem (metoda by byla skryta, ne překryta).
- **`override`** — v odvozené třídě deklaruje, že metoda překrývá virtuální metodu základní třídy. Kompilátor ověří, že odpovídající virtuální metoda v základní třídě existuje — chrání před překlepem v názvu.

```csharp
class Tvar {
    public virtual double Obsah() { return 0.0; }
}
class Kruh : Tvar {
    private double polomer;
    public Kruh(double r) { polomer = r; }
    public override double Obsah() {
        return Math.PI * polomer * polomer;
    }
}
```

## Abstraktní třídy (`abstract`)

Abstraktní třída je třída, jejíž instance nelze vytvořit přímo. Definuje společné rozhraní a případně sdílenou implementaci pro skupinu příbuzných tříd. Abstraktní metoda nemá implementaci — odvozená (neabstraktní) třída ji musí povinně překrýt slovem `override`.

```csharp
abstract class Tvar {
    public abstract double Obsah();   // bez těla — musí být override v odvozené
    public abstract double Obvod();

    // Abstraktní třída může mít i neabstraktní metody
    public void Vypis() {
        Console.WriteLine($"Obsah: {Obsah():F2}, Obvod: {Obvod():F2}");
    }
}

class Obdelnik : Tvar {
    private double a, b;
    public Obdelnik(double a, double b) { this.a = a; this.b = b; }
    public override double Obsah()  { return a * b; }
    public override double Obvod()  { return 2 * (a + b); }
}
```

## Zapečetěné třídy (`sealed`)

Klíčové slovo **`sealed`** zabrání dalšímu odvozování od třídy. Použití je vhodné tehdy, kdy třída reprezentuje finální implementaci bez možnosti rozšíření — zvyšuje bezpečnost a může zlepšit výkon (kompilátor/JIT může devirtualizovat volání).

```csharp
sealed class SealedTrida {
    // Od této třídy nelze dál dědit
}
// class Pokus : SealedTrida { }  // CHYBA kompilátoru
```

## Rozhraní (`interface`)

Rozhraní definuje čistou smluvní sadu metod, vlastností nebo událostí bez jakékoli implementace. Třída může implementovat libovolný počet rozhraní — tím C# nahrazuje vícenásobnou dědičnost tříd.

```csharp
interface IVypocitatelny {
    double Obsah();
    double Obvod();
}

interface IVykreslitelny {
    void Vykresli();
}

// Třída implementuje dvě rozhraní a dědí od abstraktní třídy najednou
class Kruh : Tvar, IVypocitatelny, IVykreslitelny {
    private double polomer;
    public Kruh(double r) { polomer = r; }
    public override double Obsah()  { return Math.PI * polomer * polomer; }
    public override double Obvod()  { return 2 * Math.PI * polomer; }
    public void Vykresli()          { Console.WriteLine("Kreslim kruh..."); }
}
```

## Srovnání C++ a C# dědičnosti

| Vlastnost | C++ | C# |
|---|---|---|
| Vícenásobná dědičnost | Ano | Ne (pouze přes interface) |
| Klíčové slovo pro rodič | `Base::metoda()` | `base.Metoda()` |
| Volání rodičovského konstruktoru | `: ZakladniTrida(param)` | `: base(param)` |
| Virtuální metody | `virtual` + implicitní override | `virtual` + povinné `override` |
| Abstraktní třída | `= 0` (pure virtual) | `abstract` |
| Zapečetění | Není přímý ekvivalent (`final` v C++11) | `sealed` |
