# 6. Datové kontejnery C++ a C#

**Kontejner** je dynamická datová struktura ze standardní knihovny, která spravuje kolekci prvků a automaticky řídí alokaci paměti. Na rozdíl od statického pole může kontejner za běhu měnit svou velikost — přidávat a odebírat prvky bez nutnosti ručního přidělování paměti. Kontejnery také nabízejí bohaté rozhraní metod pro vyhledávání, třídění a iteraci.

## `std::vector` (C++)

`vector` je nejpoužívanější sekvenční kontejner v C++. Interně uchovává prvky v dynamicky realokovaném poli na haldě. Zajišťuje **přímý přístup** k libovolnému prvku v čase O(1) — stejně jako pole. Při přidání prvku na konec (`push_back`) kontejner automaticky zdvojnásobí alokovanou kapacitu, pokud je stávající vyčerpána.

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // Vytvoření prázdného vektoru celých čísel
    vector<int> cisla;

    // Přidání prvků na konec
    cisla.push_back(10);
    cisla.push_back(20);
    cisla.push_back(30);

    // Přístup přes index (stejně jako pole)
    cout << cisla[0];        // 10
    cout << cisla.at(1);     // 20 — at() kontroluje rozsah, hází výjimku

    // Počet prvků a kapacita
    cout << cisla.size();    // 3
    cout << cisla.capacity(); // ≥ 3 (interní rezerva)

    // Odebrání posledního prvku
    cisla.pop_back();        // odstraní 30

    // Vymazání všech prvků
    cisla.clear();
    return 0;
}
```

## `std::list` (C++)

`list` implementuje **oboustranně vázaný seznam (doubly linked list)**. Každý prvek uchovává ukazatel na předchozí a následující prvek — prvky nejsou v paměti uloženy kontinuálně. To umožňuje vkládání a mazání prvků uprostřed kolekce v čase O(1), ale neumožňuje přímý přístup přes index — průchod na konkrétní pozici vyžaduje sekvenční procházení O(n).

```cpp
#include <list>
list<int> seznam;
seznam.push_back(10);
seznam.push_back(20);
seznam.push_front(5);   // vložení na začátek — O(1)
seznam.pop_front();     // odebrání ze začátku — O(1)
```

## Iterátory

**Iterátor** je generalizovaný ukazatel na prvek kontejneru. Umožňuje průchod kontejnerem bez ohledu na jeho vnitřní implementaci (vector, list, map…). Každý kontejner poskytuje metody:

- **`begin()`** — vrátí iterátor ukazující na **první** prvek.
- **`end()`** — vrátí iterátor ukazující **za poslední** prvek (sentinel — neplatná pozice).

```cpp
// Vytvoření a použití iterátoru explicitně
vector<int> cisla = {10, 20, 30, 40};
vector<int>::iterator it;

for (it = cisla.begin(); it != cisla.end(); ++it) {
    cout << *it << " ";  // dereference iterátoru
}

// Moderní zápis s auto (C++11)
for (auto it = cisla.begin(); it != cisla.end(); ++it) {
    cout << *it << " ";
}

// Nejčistší zápis — range-based for (interně používá iterátory)
for (int cislo : cisla) {
    cout << cislo << " ";
}
```

Iterátor lze posouvat operátorem `++` (vpřed), `--` (zpět u obousměrných kontejnerů jako `list`), nebo skokem `it + n` u náhodného přístupu (vector).

## Kontejnery v C# — `List<T>`

C# nabízí generický typ **`List<T>`** z jmenného prostoru `System.Collections.Generic`. Je ekvivalentem `std::vector` — interně dynamické pole s automatickou realokací. Paměť spravuje Garbage Collector.

```csharp
using System.Collections.Generic;

List<int> cisla = new List<int>();

// Přidání prvků
cisla.Add(10);
cisla.Add(20);
cisla.Add(30);

// Přístup přes index
Console.WriteLine(cisla[0]);     // 10

// Počet prvků
Console.WriteLine(cisla.Count);  // 3

// Vložení na konkrétní index
cisla.Insert(1, 15);  // {10, 15, 20, 30}

// Odebrání
cisla.Remove(15);        // odstraní první výskyt hodnoty 15
cisla.RemoveAt(0);       // odstraní prvek na indexu 0

// Průchod foreach
foreach (int c in cisla) {
    Console.Write(c + " ");
}

// Třídění
cisla.Sort();

// Hledání
bool obsahuje = cisla.Contains(20);   // true
int idx = cisla.IndexOf(20);          // index prvního výskytu
```

## `LinkedList<T>` v C#

Ekvivalent `std::list` — oboustranně vázaný seznam. Prvky jsou reprezentovány uzly (`LinkedListNode<T>`). Vhodný pro časté vkládání a mazání uprostřed kolekce.

```csharp
LinkedList<int> seznam = new LinkedList<int>();
seznam.AddLast(10);
seznam.AddLast(20);
seznam.AddFirst(5);
seznam.Remove(10);
```

## Srovnání kontejnerů

| Kontejner | Přístup dle indexu | Vložení/smazání uprostřed | Vložení na konec |
|---|---|---|---|
| `vector` / `List<T>` | O(1) | O(n) | O(1) amortizovaně |
| `list` / `LinkedList<T>` | O(n) | O(1) | O(1) |

**Pravidlo volby:** Pro většinu úloh je `vector` / `List<T>` optimální volbou díky výbornému přístupu a cache-friendly paměťovému rozvržení. `list` / `LinkedList<T>` se využívá výhradně tehdy, kdy jsou operace vkládání a mazání uprostřed sekvence dominantní a přístup přes index není potřeba.
