# 13. Dynamická alokace paměti – ukazatele

## Paměťové segmenty spuštěného programu

Každý spuštěný proces obdrží od operačního systému virtuální adresní prostor rozdělený do několika segmentů:

- **Segment kódu (Text Segment):** Oblast jen pro čtení obsahující zkompilované instrukce (strojový kód) programu.
- **Datový segment:** Ukládá globální a statické proměnné inicializované před spuštěním `main`.
- **Zásobník (Stack) — statická část:** Oblast s pevnou velikostí (typicky 1–8 MB, konfigurovatelné). Lokální proměnné, parametry funkcí a návratové adresy se alokují automaticky při vstupu do funkce a uvolňují při výstupu. Přidělení a uvolnění je bleskové (posun ukazatele zásobníku), ale kapacita je omezená. Překročení způsobí **stack overflow**.
- **Halda (Heap) — dynamická část:** Rozsáhlá oblast paměti (limitovaná dostupnou fyzickou a virtuální pamětí). Alokace probíhá za běhu na žádost programátora. V C++ vyžaduje ruční správu, v C# ji zajišťuje Garbage Collector.

## Ukazatel (Pointer)

**Ukazatel** je proměnná, jejíž hodnota je **adresa** jiné proměnné v paměti — neuchovává data, ale umístění dat. Deklaruje se hvězdičkou `*` za datovým typem, na který ukazuje:

```cpp
int  hodnota = 42;
int* ptr;          // ukazatel na int — zatím neinicializovaný (nebezpečný)
ptr = nullptr;     // bezpečná inicializace — ukazatel na nic
```

## Operátor `&` — získání adresy

Unární operátor **`&`** (address-of) aplikovaný na proměnnou vrátí její paměťovou adresu — hodnotu, která se přiřadí ukazateli:

```cpp
int x = 10;
int* ptr = &x;     // ptr nyní obsahuje adresu proměnné x
cout << ptr;       // vypíše adresu (hexadecimálně, např. 0x7ffee4b3c1a4)
cout << &x;        // totéž
```

## Dereference — přístup k hodnotě přes ukazatel

Operátor **`*`** aplikovaný na ukazatel (dereference) přistoupí k hodnotě uložené na adrese, na kterou ukazatel ukazuje:

```cpp
int x = 10;
int* ptr = &x;

cout << *ptr;      // dereference — výstup: 10
*ptr = 99;         // změna hodnoty přes ukazatel
cout << x;         // výstup: 99 — x se skutečně změnilo
```

## Reference vs. ukazatel

**Reference (`&` v deklaraci parametru)** je alias — alternativní jméno pro existující proměnnou. Musí být inicializována při deklaraci, nelze ji přesměrovat na jinou proměnnou. Reference se chová jako proměnná sama — není třeba dereference.

```cpp
int x = 5;
int& ref = x;    // ref je alias pro x
ref = 99;        // totéž jako x = 99
cout << x;       // 99
```

Ukazatel lze přesměrovat (změnit, na co ukazuje), reference nelze. Ukazatel může být `nullptr`, reference nikdy.

## Dynamická alokace — operátor `new`

Operátor **`new`** alokuje paměť na haldě za běhu programu a vrátí ukazatel na nově alokovaný blok. Velikost alokace závisí na datovém typu.

```cpp
// Dynamická proměnná
int* p = new int;      // alokuje 4 B na haldě
*p = 42;               // přiřazení hodnoty přes dereference
cout << *p;            // 42

// Dynamická proměnná s inicializací
double* d = new double(3.14);

// Dynamické pole — velikost určena za běhu
int n;
cin >> n;
int* pole = new int[n];   // alokuje n × 4 B na haldě
pole[0] = 10;
pole[1] = 20;
```

## Uvolnění paměti — `delete`

Každá paměť alokovaná přes `new` musí být manuálně uvolněna operátorem **`delete`** (pro jednotlivou proměnnou) nebo **`delete[]`** (pro dynamické pole). Neuvolněná paměť zůstane zabrána po celou dobu běhu programu — tato situace se nazývá **memory leak (únik paměti)**. Při dlouhodobém běhu (server, daemon) způsobuje vyčerpání RAM.

```cpp
int* p = new int(42);
delete p;           // uvolní paměť jedné proměnné
p = nullptr;        // bezpečné vynulování — zabrání dangling pointeru

int* pole = new int[100];
delete[] pole;      // VŽDY delete[] pro dynamické pole
pole = nullptr;
```

## Garbage Collector (C#) vs. ruční správa (C++)

- **C++ (ruční správa):** Vývojář je zodpovědný za každé `delete`. Nevyvolání `delete` způsobí memory leak. Dvojité `delete` způsobí nedefinované chování (korupci paměti). Nástrojem prevence jsou chytré ukazatele: `std::unique_ptr`, `std::shared_ptr` (automatické uvolnění při zániku objektu — RAII princip).

```cpp
#include <memory>
unique_ptr<int> p = make_unique<int>(42);
// paměť uvolněna automaticky při zániku p — není třeba delete
```

- **C# (automatická správa — GC):** .NET Garbage Collector periodicky prochází heap a uvolňuje objekty, na které neexistuje žádná živá reference. Vývojář nepoužívá `delete` — správu zajistí runtime. GC je nedeterministický (uvolnění neprobíhá okamžitě) a má určitou výpočetní a paměťovou režii.

## Binární odpad (Dangling Pointer)

Po zavolání `delete` ukazatel stále obsahuje starou adresu — ta je ale již neplatná. Přístup k takové adrese způsobí nedefinované chování (čtení „binárního odpadu", korupce paměti, pád programu). Prevencí je vždy nastavit ukazatel na `nullptr` po uvolnění.

```cpp
int* p = new int(10);
delete p;
// *p = 5;     // NEBEZPEČNÉ — dangling pointer
p = nullptr;   // bezpečný stav
```

## Příklad — dynamické 2D pole

```cpp
int radky = 3, sloupce = 4;
int** matice = new int*[radky];        // pole ukazatelů
for (int i = 0; i < radky; i++) {
    matice[i] = new int[sloupce];     // každý řádek alokován zvlášť
}

matice[1][2] = 99;

// Uvolnění musí proběhnout v opačném pořadí
for (int i = 0; i < radky; i++) {
    delete[] matice[i];
}
delete[] matice;
```
