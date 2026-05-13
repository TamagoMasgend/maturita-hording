# 4. Cykly

**Cyklus (loop)** je řídicí struktura, která umožňuje opakované vykonání bloku kódu tak dlouho, dokud platí zadaná podmínka, nebo pro předem stanovený počet opakování. Bez cyklů by opakující se operace (zpracování prvků pole, čtení souboru, výpočetní iterace) vyžadovaly ruční duplikaci kódu.

## Cyklus `for`

`for` je určen pro situace, kdy je počet opakování znám předem. Skládá se ze tří částí oddělených středníkem v záhlaví:

1. **Inicializace** — vykoná se jednou před prvním průchodem (typicky deklarace čítače).
2. **Podmínka** — vyhodnotí se před každým průchodem; při `false` cyklus skončí.
3. **Inkrementace** — vykoná se po každém průchodu (typicky zvýšení čítače).

```cpp
// C++ — výpis čísel 0 až 4
for (int i = 0; i < 5; i++) {
    cout << i << " ";
}
// Výstup: 0 1 2 3 4
```

```csharp
// C# — syntaxe identická s C++
for (int i = 0; i < 5; i++) {
    Console.Write(i + " ");
}
```

```vba
' VBA — For...Next, krok je volitelný (default = 1)
Dim i As Integer
For i = 0 To 4
    Debug.Print i
Next i

' Záporný krok — průchod pozpátku
For i = 10 To 1 Step -1
    Debug.Print i
Next i
```

### Přetečení čítače (Integer Overflow)

Pokud čítač `int` překročí maximální hodnotu (2 147 483 647), přeteče na minimální hodnotu (-2 147 483 648) — program se může zacyklit donekonečna. Přetečení je nedefinované chování v C++. Řešením je použití `unsigned` nebo `long long` pro velké čítače, nebo kontrola podmínky.

### `break` a `continue`

- **`break`** — okamžitě ukončí cyklus a přejde na první instrukci za ním.
- **`continue`** — přeskočí zbytek aktuálního průchodu a přejde na inkrementaci (u `for`) nebo znovu vyhodnotí podmínku (u `while`).

```cpp
for (int i = 0; i < 10; i++) {
    if (i == 3) continue;  // přeskočí i==3
    if (i == 7) break;     // ukončí cyklus při i==7
    cout << i << " ";
}
// Výstup: 0 1 2 4 5 6
```

## Cyklus `while`

`while` opakuje tělo cyklu tak dlouho, dokud je podmínka `true`. Podmínka je vyhodnocena **před každým** průchodem — pokud je podmínka od začátku `false`, tělo se nevykoná ani jednou.

```cpp
// C++ — čtení čísel do dosažení nuly
int vstup;
cout << "Zadej cislo (0 = konec): ";
cin >> vstup;
while (vstup != 0) {
    cout << "Zadano: " << vstup << endl;
    cin >> vstup;
}
```

```csharp
int vstup = int.Parse(Console.ReadLine());
while (vstup != 0) {
    Console.WriteLine("Zadano: " + vstup);
    vstup = int.Parse(Console.ReadLine());
}
```

```vba
Dim vstup As Integer
vstup = InputBox("Zadej cislo")
Do While vstup <> 0
    MsgBox "Zadano: " & vstup
    vstup = InputBox("Zadej cislo")
Loop
```

## Cyklus `do-while`

`do-while` zaručuje, že tělo cyklu se vykoná **minimálně jednou** — podmínka se vyhodnotí **až po** prvním průchodu. Typické použití: validace uživatelského vstupu (zobraz výzvu, přečti vstup, opakuj pokud neplatný).

```cpp
// C++ — zajistí alespoň jedno provedení
int vstup;
do {
    cout << "Zadej cislo 1-10: ";
    cin >> vstup;
} while (vstup < 1 || vstup > 10);
```

```csharp
int vstup;
do {
    Console.Write("Zadej cislo 1-10: ");
    vstup = int.Parse(Console.ReadLine());
} while (vstup < 1 || vstup > 10);
```

```vba
' VBA ekvivalent — Do...Loop While (podmínka na konci)
Dim vstup As Integer
Do
    vstup = InputBox("Zadej cislo 1-10")
Loop While vstup < 1 Or vstup > 10
```

## Cyklus `foreach` (range-based for)

`foreach` iteruje přes každý prvek kolekce nebo pole bez nutnosti spravovat čítač nebo index. Je bezpečnější než klasický `for` — eliminuje riziko indexování mimo rozsah.

```cpp
// C++ — range-based for (od C++11)
#include <vector>
vector<int> cisla = {10, 20, 30, 40};
for (int cislo : cisla) {
    cout << cislo << " ";
}
// Modifikace prvků vyžaduje referenci:
for (int& cislo : cisla) {
    cislo *= 2;
}
```

```csharp
// C# — foreach přes pole i kolekce
int[] cisla = {10, 20, 30, 40};
foreach (int cislo in cisla) {
    Console.Write(cislo + " ");
}

// Foreach je určen pouze pro čtení — nelze modifikovat prvky přímo
```

```vba
' VBA — For Each...Next pro kolekce objektů
Dim ws As Worksheet
For Each ws In ThisWorkbook.Worksheets
    Debug.Print ws.Name
Next ws
```

## Přehled a volba správného cyklu

| Cyklus | Kdy použít |
|---|---|
| `for` | Počet opakování znám předem (iterace přes index). |
| `while` | Počet opakování není znám; podmínka se může být nesplněna hned od začátku. |
| `do-while` | Tělo musí proběhnout alespoň jednou (validace vstupu). |
| `foreach` | Průchod každým prvkem kolekce nebo pole bez správy indexu. |

Vnořené cykly (cyklus uvnitř cyklu) se využívají pro práci s vícerozměrnými poli nebo maticemi. Časová složitost vnořených cyklů je O(n²) pro dvojité zanoření — nutno zohlednit pro velké datové sady.
