# 7. Funkce a metody

**Funkce** je pojmenovaný, znovupoužitelný blok kódu, který vykonává konkrétní, ohraničenou úlohu. Přijímá vstupní data (**parametry**), zpracuje je a volitelně vrací výslednou hodnotu. Funkce jsou základním nástrojem pro **dekompozici** programu — rozdělení složitého problému na menší, přehledné a testovatelné části. Eliminují duplicitu: kód napsaný jednou lze volat z libovolného místa programu.

**Metoda** je funkce definovaná uvnitř třídy nebo struktury. Je funkčně identická s funkcí, ale je svázána s konkrétním objektem nebo třídou a má přístup k jeho datovým atributům. V C++ existují jak funkce (mimo třídu), tak metody (uvnitř třídy). V C# existují výhradně metody — veškerý kód musí být uvnitř třídy.

## Funkce `main`

`main` je povinná vstupní funkce každého C/C++ programu. Operační systém ji volá jako první po spuštění procesu. Vrací typ `int` — návratová hodnota 0 indikuje úspěšné ukončení, nenulová hodnota signalizuje chybu.

```cpp
int main() {
    // kód programu
    return 0;
}

// Varianta s argumenty příkazové řádky
int main(int argc, char* argv[]) {
    // argc = počet argumentů, argv = pole řetězců argumentů
    return 0;
}
```

V C# je vstupní metoda `Main` (s velkým M) statická metoda třídy:

```csharp
static void Main(string[] args) {
    // kód programu
}
```

## Typy funkcí a jejich deklarace

### Funkce bez návratové hodnoty a bez parametrů

```cpp
// C++ — typ void značí, že funkce nevrací žádnou hodnotu
void pozdrav() {
    cout << "Dobry den!" << endl;
}
// Volání
pozdrav();
```

```csharp
// C#
void Pozdrav() {
    Console.WriteLine("Dobry den!");
}
Pozdrav();
```

### Funkce s parametry bez návratové hodnoty

```cpp
// C++ — parametry deklarovány jako (typ jmeno, typ jmeno, ...)
void vypisSoucet(int a, int b) {
    cout << "Soucet: " << (a + b) << endl;
}
vypisSoucet(3, 7);  // Soucet: 10
```

### Funkce s návratovou hodnotou

```cpp
// C++ — typ před jménem funkce určuje typ vrácené hodnoty
int soucet(int a, int b) {
    return a + b;  // return ukončí funkci a předá hodnotu volajícímu
}
int vysledek = soucet(5, 3);  // vysledek = 8
```

```csharp
int Soucet(int a, int b) {
    return a + b;
}
int vysledek = Soucet(5, 3);
```

### VBA — Sub vs Function

```vba
' Sub — nepřijímá návratovou hodnotu (ekvivalent void)
Sub Pozdrav()
    MsgBox "Dobry den!"
End Sub

' Function — vrací hodnotu
Function Soucet(a As Integer, b As Integer) As Integer
    Soucet = a + b   ' návratová hodnota se přiřadí jménu funkce
End Function

' Volání
Call Pozdrav()
Dim vysledek As Integer
vysledek = Soucet(3, 7)
```

## Předávání parametrů

### Hodnotou (by value)

Funkce obdrží **kopii** hodnoty. Změna parametru uvnitř funkce neovlivní původní proměnnou.

```cpp
void zdvojnasob(int x) {
    x = x * 2;   // modifikuje pouze lokální kopii
}
int a = 5;
zdvojnasob(a);
cout << a;  // stále 5
```

### Referencí (by reference) — C++

Funkce obdrží **aliaz** na původní proměnnou. Změna parametru se projeví i mimo funkci.

```cpp
void zdvojnasob(int& x) {   // & značí referenci
    x = x * 2;
}
int a = 5;
zdvojnasob(a);
cout << a;  // nyní 10
```

```csharp
// C# — klíčové slovo ref nebo out
void Zdvojnasob(ref int x) {
    x = x * 2;
}
int a = 5;
Zdvojnasob(ref a);  // ref musí být uvedeno i při volání
Console.WriteLine(a);  // 10
```

Klíčové slovo **`out`** v C# je podobné `ref`, ale výstupní parametr nemusí být inicializován před voláním — funkce ho musí přiřadit. Typicky se využívá pro vracení více hodnot najednou.

## Deklarace (prototyp) a definice v C++

V C++ musí být funkce deklarována před prvním použitím. Pokud je definice funkce umístěna za místem volání, je nutný **prototyp** (deklarace bez těla):

```cpp
// Prototyp — informuje kompilátor o signatuře funkce
int soucet(int a, int b);

int main() {
    int vysledek = soucet(3, 4);  // kompilátor ví o signatuře
    return 0;
}

// Definice — může být kdekoliv níže nebo v jiném souboru
int soucet(int a, int b) {
    return a + b;
}
```

## Výhody dekompozice kódu

Rozdělení programu na funkce přináší: lepší čitelnost, jednodušší ladění (izolace chyby na konkrétní funkci), znovupoužitelnost (volání ze více míst), testovatelnost (unit testy pro jednotlivé funkce) a snadnější týmovou spolupráci (každý vývojář pracuje na separátních funkcích).
