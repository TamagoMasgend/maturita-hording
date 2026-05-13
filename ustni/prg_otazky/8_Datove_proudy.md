# 8. Datové proudy

**Datový proud (stream)** je abstrakce sekvenčního toku dat mezi zdrojem (nebo cílem) a programem. Data proudí jako sekvence bajtů — program nemusí znát fyzický zdroj dat (soubor, síť, klávesnice, paměť), rozhraní je vždy identické. Standardní knihovna C++ (`<iostream>`, `<fstream>`, `<sstream>`) a .NET v C# poskytují hierarchii tříd pro práci se všemi typy proudů.

## Standardní vstup a výstup — `iostream`

Hlavička **`<iostream>`** zpřístupňuje dva globální objekty:

- **`std::cout`** (Character Output) — výstupní proud napojený na standardní výstup (terminál/konzoli). Data se do něj vkládají operátorem `<<`.
- **`std::cin`** (Character Input) — vstupní proud napojený na standardní vstup (klávesnici). Data se z něj čtou operátorem `>>`.

```cpp
#include <iostream>
using namespace std;

int main() {
    int vek;
    cout << "Zadej vek: ";    // výstup na terminál
    cin >> vek;               // čtení jedné hodnoty (oddělení mezerou/Enter)
    cout << "Tvuj vek: " << vek << endl;

    string jmeno;
    cout << "Zadej jmeno: ";
    cin >> jmeno;             // čte pouze do první mezery

    // getline čte celý řádek včetně mezer
    cin.ignore();             // odstraní zbývající '\n' ze vstupního bufferu
    string veta;
    getline(cin, veta);
    return 0;
}
```

### `cin.ignore()`

Po čtení čísla operátorem `>>` zůstane v bufferu nevytažený znak nového řádku `\n`. Pokud by následovalo volání `getline`, okamžitě by přečetlo tento `\n` jako prázdný řádek. Metoda `cin.ignore()` (nebo `cin.ignore(1000, '\n')`) tento zbytek vymaže.

## Řetězcový proud — `sstream`

**`std::stringstream`** umožňuje používat řetězec jako zdroj nebo cíl datového proudu — operátory `<<` a `>>` fungují stejně jako u `cout`/`cin`, ale pracují s `std::string` v paměti. Typické použití: převod čísla na řetězec nebo parsování řetězce na číslo.

```cpp
#include <sstream>

// Číslo → řetězec
int cislo = 42;
stringstream ss;
ss << cislo;
string text = ss.str();  // "42"

// Řetězec → číslo (parsování)
stringstream ss2("123 456");
int a, b;
ss2 >> a >> b;           // a = 123, b = 456
```

## Práce se soubory — `fstream`

Hlavička **`<fstream>`** poskytuje třídy pro čtení a zápis souborů:

- **`std::ifstream`** (input file stream) — čtení ze souboru.
- **`std::ofstream`** (output file stream) — zápis do souboru.
- **`std::fstream`** — obousměrný přístup.

### Cesty k souborům

- **Absolutní cesta:** úplná cesta od kořenového adresáře. Windows: `C:\\data\\soubor.txt`; Linux/macOS: `/home/user/data/soubor.txt`.
- **Relativní cesta:** cesta vztažená k aktuálnímu pracovnímu adresáři procesu. `data/soubor.txt` nebo `../config.txt`.

### Zápis do souboru

```cpp
#include <fstream>

ofstream soubor("vystup.txt");         // otevření pro zápis (přepíše obsah)
if (soubor.is_open()) {
    soubor << "Prvni radek" << endl;
    soubor << "Druhy radek" << endl;
    soubor.close();                    // uvolnění systémového prostředku
} else {
    cerr << "Nelze otevrit soubor!" << endl;
}

// Otevření v režimu připojení na konec (append)
ofstream soubor2("vystup.txt", ios::app);
soubor2 << "Pridany radek" << endl;
soubor2.close();
```

### Čtení ze souboru

```cpp
ifstream soubor("vstup.txt");
if (soubor.is_open()) {
    string radek;
    while (getline(soubor, radek)) {   // čte řádek po řádku dokud nenarazí na EOF
        cout << radek << endl;
    }
    soubor.close();
}

// Čtení slovo po slovu
ifstream soubor2("vstup.txt");
string slovo;
while (soubor2 >> slovo) {
    cout << slovo << " ";
}
```

### Otevírací režimy (`ios` příznaky)

| Příznak | Popis |
|---|---|
| `ios::in` | Otevření pro čtení (výchozí pro `ifstream`). |
| `ios::out` | Otevření pro zápis, obsah se smaže (výchozí pro `ofstream`). |
| `ios::app` | Zápis se připojuje na konec souboru. |
| `ios::binary` | Binární režim (žádná konverze řádkových zakončení). |
| `ios::trunc` | Obsah souboru se zkrátí na nulu při otevření. |

Příznaky lze kombinovat operátorem `|`:

```cpp
fstream f("data.bin", ios::in | ios::out | ios::binary);
```

## Datové proudy v C#

C# pracuje se soubory přes třídy v jmenném prostoru `System.IO`:

```csharp
using System.IO;

// Zápis
StreamWriter sw = new StreamWriter("vystup.txt");
sw.WriteLine("Prvni radek");
sw.Close();

// Připojení na konec
StreamWriter sw2 = new StreamWriter("vystup.txt", append: true);
sw2.WriteLine("Pridany radek");
sw2.Close();

// Čtení — řádek po řádku
StreamReader sr = new StreamReader("vstup.txt");
string radek;
while ((radek = sr.ReadLine()) != null) {
    Console.WriteLine(radek);
}
sr.Close();

// Zkrácený zápis přes statické metody File
string[] radky = File.ReadAllLines("vstup.txt");
File.WriteAllText("vystup.txt", "obsah");
```

Klíčový vzor je **`using` blok** — zajistí automatické volání `Close()` (a `Dispose()`) i při výjimce:

```csharp
using (StreamWriter sw = new StreamWriter("vystup.txt")) {
    sw.WriteLine("Automaticky uzavreno po opusteni bloku");
}
```
