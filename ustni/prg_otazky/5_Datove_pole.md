# 5. Datové pole C++ a C#

**Pole (array)** je homogenní datová struktura ukládající pevný počet prvků stejného datového typu v **kontinuálním bloku paměti**. Každý prvek je přístupný přes **index** — celé číslo určující jeho pozici. Indexování začíná od nuly: první prvek má index 0, poslední prvek pole o délce n má index n−1.

Pole je základní a výkonnostně nejefektivnější datová struktura. Díky kontinuální paměti a pevnému indexování je přístup k libovolnému prvku v konstantním čase O(1).

## Statická a dynamická část paměti

Každý spuštěný program má operační systém přidělenu paměť rozdělenou na několik segmentů:

- **Zásobník (Stack) — statická část:** Oblast paměti s pevně omezenou velikostí (typicky 1–8 MB). Pole deklarovaná uvnitř funkce bez klíčového slova `new` jsou alokována na zásobníku. Alokace i uvolnění jsou automatické (při vstupu a výstupu z funkce). Zásobník je rychlý, ale omezený.
- **Halda (Heap) — dynamická část:** Rozsáhlá oblast paměti spravovaná programem za běhu. Pole alokovaná přes `new` jsou umístěna na haldě. Jejich životnost nekončí automaticky s funkcí — vývojář (v C++) musí paměť explicitně uvolnit operátorem `delete[]`. Halda dovoluje rozsáhlé struktury a dynamicky určenou velikost.

## Pole v C++

### Statické pole (na zásobníku)

Velikost musí být **konstantní** výraz známý v době kompilace.

```cpp
// Deklarace a inicializace statického pole
int cisla[5] = {10, 20, 30, 40, 50};

// Přístup k prvkům přes index
cout << cisla[0];  // 10
cout << cisla[4];  // 50

// Přístup mimo rozsah (index < 0 nebo >= 5) je nedefinované chování
// cisla[5] = 99;  // CHYBA — přepíše cizí paměť

// Procházení pole cyklem
for (int i = 0; i < 5; i++) {
    cout << cisla[i] << " ";
}

// Zjištění počtu prvků
int pocet = sizeof(cisla) / sizeof(cisla[0]);  // = 5
```

### Dynamické pole (na haldě)

Velikost může být určena za běhu (proměnná, vstup uživatele).

```cpp
int n;
cin >> n;
int* pole = new int[n];   // alokace n prvků na haldě

// Naplnění
for (int i = 0; i < n; i++) {
    pole[i] = i * 2;
}

// Povinné uvolnění paměti po skončení práce
delete[] pole;
pole = nullptr;  // bezpečné vynulování ukazatele
```

### Dvourozměrné pole (matice) v C++

```cpp
int matice[3][4];  // 3 řádky, 4 sloupce

// Naplnění vnořeným cyklem
for (int r = 0; r < 3; r++) {
    for (int s = 0; s < 4; s++) {
        matice[r][s] = r * 4 + s;
    }
}
```

## Pole v C#

C# spravuje paměť automaticky přes **Garbage Collector (GC)** — vývojář nemusí paměť uvolňovat ručně. Všechna pole jsou v C# alokována na haldě (jsou to referenční typy).

```csharp
// Deklarace a inicializace
int[] cisla = {10, 20, 30, 40, 50};
// nebo explicitně
int[] cisla2 = new int[5];  // inicializováno na 0

// Přístup k prvkům
Console.WriteLine(cisla[0]);  // 10
Console.WriteLine(cisla[4]);  // 50

// Přístup mimo rozsah vyvolá výjimku IndexOutOfRangeException — program nespadne tiše
// cisla[5] = 99;  // Runtime výjimka

// Délka pole
Console.WriteLine(cisla.Length);  // 5

// Procházení range-based
foreach (int c in cisla) {
    Console.Write(c + " ");
}
```

### Dvourozměrné pole v C#

```csharp
// Obdélníkové pole
int[,] matice = new int[3, 4];
matice[0, 0] = 1;

// Zubaté pole (jagged array) — každý řádek může mít jinou délku
int[][] zubate = new int[3][];
zubate[0] = new int[]{1, 2, 3};
zubate[1] = new int[]{4, 5};
```

### Užitečné metody třídy `Array` v C#

- **`Array.Sort(cisla)`** — seřadí pole in-place.
- **`Array.Reverse(cisla)`** — obrátí pořadí prvků.
- **`Array.IndexOf(cisla, hodnota)`** — vrátí index prvního výskytu hodnoty (nebo -1).
- **`cisla.Length`** — počet prvků.
- **`cisla.Clone()`** — mělká kopie pole.

## Srovnání C++ a C#

| Vlastnost | C++ statické pole | C++ dynamické pole | C# pole |
|---|---|---|---|
| Umístění | Stack | Heap | Heap |
| Správa paměti | Automatická | Ruční (`delete[]`) | GC automaticky |
| Kontrola rozsahu | Žádná (UB) | Žádná (UB) | Výjimka za běhu |
| Velikost za běhu | Ne | Ano | Ano |

Pole je vhodné tehdy, kdy je počet prvků znám předem a nemění se. Pro dynamicky rostoucí kolekce je vhodný kontejner `vector` (C++) nebo `List<T>` (C#).
