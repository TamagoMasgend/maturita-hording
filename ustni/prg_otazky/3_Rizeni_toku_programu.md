# 3. Řízení toku programu

Bez mechanismů řízení toku by program vykonával instrukce striktně lineárně shora dolů. **Řízení toku (control flow)** umožňuje větvení — výběr různých větví kódu v závislosti na podmínce — a opakování sekvencí (cykly). Podmínkové konstrukce jsou základem rozhodovací logiky každého programu.

## Podmínkový příkaz `if`

Konstrukce `if` vyhodnotí logický výraz (podmínku). Pokud je výsledek `true`, vykoná se blok kódu v těle příkazu. Volitelná větev `else` se vykoná, je-li podmínka `false`. Větev `else if` umožňuje řetězení více podmínek.

```cpp
// C++ — standardní zápis s bloky {}
int x = 15;
if (x > 10) {
    cout << "vetsi nez 10";
} else if (x == 10) {
    cout << "rovno 10";
} else {
    cout << "mensi nez 10";
}

// C++ — bez {} je součástí podmínky pouze JEDNA následující instrukce
if (x > 10)
    cout << "vetsi";   // toto patří do if
    cout << "vzdy";    // toto se vykoná vždy — záludná chyba!
```

```csharp
// C# — syntaxe identická s C++
int x = 15;
if (x > 10) {
    Console.WriteLine("vetsi nez 10");
} else if (x == 10) {
    Console.WriteLine("rovno 10");
} else {
    Console.WriteLine("mensi nez 10");
}
```

```vba
' VBA — odlišná syntaxe, ukončuje se klíčovým slovem End If
Dim x As Integer
x = 15
If x > 10 Then
    MsgBox "vetsi nez 10"
ElseIf x = 10 Then
    MsgBox "rovno 10"
Else
    MsgBox "mensi nez 10"
End If
```

## Relační a logické operátory

**Relační operátory** porovnávají dvě hodnoty a vrací `bool`:

| Operátor | Význam |
|---|---|
| `==` | rovnost |
| `!=` | nerovnost |
| `<`, `>` | menší, větší |
| `<=`, `>=` | menší nebo rovno, větší nebo rovno |

**Logické operátory** kombinují více podmínek:

| C++ / C# | VBA | Popis |
|---|---|---|
| `&&` | `And` | logický součin — obě podmínky musí být `true` |
| `\|\|` | `Or` | logický součet — alespoň jedna podmínka musí být `true` |
| `!` | `Not` | negace — invertuje hodnotu podmínky |

### Priorita operátorů

Při vyhodnocování výrazu platí standardní pořadí: nejprve `!` (NOT), pak relační operátory (`<`, `>`, `==`, …), poté `&&` (AND) a nakonec `||` (OR). Pro explicitní řízení priority je vhodné použít závorky:

```cpp
// Závorky jednoznačně určí pořadí vyhodnocení
if ((vek >= 18 && vek < 65) || zamestnany) { ... }
```

## Příkaz `switch`

`switch` je efektivní alternativa k řetězci `else if` pro větvení na základě přesné hodnoty celočíselného nebo výčtového výrazu. Kompilátor ho optimalizuje na tzv. **jump table** — přímý skok na adresu větve bez sekvenčního porovnávání.

### C++

V C++ lze ve `switch` použít typy: `int`, `char`, `short`, `long`, `enum` a jejich varianty `unsigned`. **Nelze** použít `float`, `double` ani `string` (v základním C++).

```cpp
int den = 3;
switch (den) {
    case 1:
        cout << "Pondeli";
        break;   // ukončí switch, zabrání "propadnutí" do dalšího case
    case 2:
        cout << "Utery";
        break;
    case 3:
    case 4:      // více hodnot → stejná větev (fallthrough záměrně)
        cout << "Stred nebo Ctvrtek";
        break;
    default:     // vykoná se, pokud žádný case neodpovídá
        cout << "Jiny den";
        break;
}
```

Klíčové slovo **`break`** je povinné na konci každé větve. Bez něj program „propadne" (`fallthrough`) do následujícího `case` a vykoná i jeho kód — typická chyba.

### C#

C# rozšiřuje `switch` o možnost použití **`string`** jako výrazu:

```csharp
string barva = "cervena";
switch (barva) {
    case "cervena":
        Console.WriteLine("Stop");
        break;
    case "zelena":
        Console.WriteLine("Jdi");
        break;
    default:
        Console.WriteLine("Pozor");
        break;
}
```

C# rovněž nabízí modernější **`switch` výraz** (C# 8.0+):

```csharp
string popis = barva switch {
    "cervena" => "Stop",
    "zelena"  => "Jdi",
    _         => "Pozor"   // _ je wildcard, ekvivalent default
};
```

### VBA

```vba
Dim den As Integer
den = 3
Select Case den
    Case 1
        MsgBox "Pondeli"
    Case 2
        MsgBox "Utery"
    Case 3, 4            ' více hodnot odděleno čárkou
        MsgBox "Stred nebo Ctvrtek"
    Case Else
        MsgBox "Jiny den"
End Select
```

VBA nepoužívá `break` — každý `Case` blok je automaticky ukončen dalším `Case` nebo `End Select`. Funkční ekvivalent `switch` je `Select Case`.

## Ternární operátor (C++ a C#)

Pro jednoduché přiřazení dle podmínky lze použít zkrácený zápis **ternárního operátoru** `? :`:

```cpp
// výsledek = (podmínka) ? hodnota_pokud_true : hodnota_pokud_false
int max = (a > b) ? a : b;
```

Ternární operátor nenahrazuje celý blok `if`/`else`, je vhodný výhradně pro jednoduchá přiřazení — složité výrazy snižují čitelnost kódu.
