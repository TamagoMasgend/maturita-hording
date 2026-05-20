---
created: 2026-05-06T23:20
updated: 2026-05-20T13:02
---
# 1. Hyper Text Markup Language (HTML)

## Koncept, účel a fungování pod kapotou

HTML (Hyper Text Markup Language) není programovací jazyk, nýbrž striktně strukturální **značkovací jazyk**. Jeho exkluzivním účelem je definovat sémantiku a logickou strukturu textových dokumentů přenášených přes síť.

Z inženýrského hlediska nefunguje HTML jako kód, který by se spouštěl v procesoru. Webový prohlížeč (klient) přijme zdrojový kód jako surový textový řetězec a spustí vnitřní parser.

1. **Tokenizace a Lexikální analýza**: Prohlížeč rozseká surový textový řetězec na identifikovatelné kousky (tokeny), jako jsou otevírací tagy, uzavírací tagy a textové uzly.
2. **Sestavení stromu**: Z tokenů prohlížeč sestaví vnitřní objektovou reprezentaci dokumentu v paměti RAM, zvanou **DOM (Document Object Model)**.
3. Teprve tento logický stromový model (spojený s formátovacími pravidly z CSS) tvoří základ pro finální vykreslení pixelů na obrazovku (proces zvaný _Painting_). Manipulace s HTML v reálném čase se neprovádí přepisováním textu, ale výhradně skriptováním (pomocí JavaScriptu) přímo nad uzly stromu DOM v paměti.

## Historie a standardizace

Původní koncept HTML navrhl inženýr **Tim Berners-Lee** v laboratořích CERN v roce 1989 s cílem usnadnit sdílení vědeckých dokumentů pomocí křížových odkazů (hypertextu), což položilo základy World Wide Webu.

- O standardizaci se historicky staralo konsorcium **W3C (World Wide Web Consortium)**. V 90. letech a na začátku milénia panovala éra tzv. "Války prohlížečů", kdy si každý výrobce (Microsoft IE, Netscape) přidával do HTML vlastní nestandardizované tagy, což vedlo k rozbití kompatibility. W3C definovalo přísné verze jako HTML 4.01 a následně XHTML (což byla kombinace HTML se striktními chybovými pravidly vycházejícími z XML, kde chybějící lomítko znamenalo pád celé stránky).
- Vývoj moderní specifikace dnes řídí flexibilnější organizace **WHATWG (Web Hypertext Application Technology Working Group)**, kterou založili zástupci firem Apple, Mozilla a Opera jako reakci na pomalost W3C.
- V roce 2014 byl vydán zlomový standard **HTML5**, který nativně přinesl podporu pro multimédia (video, audio), prvek `<canvas>` pro 2D/3D vykreslování v hardwaru a API pro lokální ukládání dat (Local Storage). To odstranilo nutnost instalace nespolehlivých zásuvných modulů třetích stran (např. Adobe Flash nebo Microsoft Silverlight). Současná specifikace HTML5 funguje jako takzvaný "Living Standard", tedy průběžně aktualizovaná specifikace bez vydávání nových velkých verzí.

## Syntaxe a struktura validního dokumentu

Každý validní HTML5 dokument musí obsahovat přesně definovanou kostru. Skládá se z deklarace dokumentu a kořenového elementu rozděleného na hlavičku (skrytá metadata pro parser a vyhledávače) a tělo (viditelný obsah pro uživatele).

```html
<!DOCTYPE html>
<html lang="cs">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content="Inženýrská ukázka stavby dokumentu." />
    <title>Inženýrská ukázka HTML5</title>
    <!-- Napojení externího kaskádového stylu -->
    <link rel="stylesheet" href="style.css" />
    <!-- Asynchronní načtení skriptu bez blokování vykreslování DOMu -->
    <script src="app.js" async></script>
  </head>
  <body>
    <!-- Vizuální část vykreslovaná renderovacím jádrem -->
    <h1>Struktura dokumentu</h1>
    <p>Toto je ukázkový textový odstavec.</p>
  </body>
</html>
```

- `<!DOCTYPE html>`: Instrukce (Document Type Declaration) pro parser prohlížeče, jež vynutí zpracování vykreslování podle moderního standardu HTML5 (tzv. Standards Mode) a zamezí přepnutí do režimu zpětné kompatibility (Quirks Mode), který by záměrně emuloval staré a chybné chování prohlížečů z 90. let.
- `<html lang="cs">`: Kořenový uzel definující jazyk celého dokumentu, což je kritický parametr pro hlasové syntetizátory a SEO optimalizaci.
- `<head>`: Zahrnuje neviditelná metadata. Kritický je tag `<meta charset="UTF-8">` k vynucení alokace znaků podle Unicode, bez něhož by diakritika u asijských či slovanských jazyků způsobovala deformační znaky na obrazovce. Dále obsahuje tag `viewport` instruující mobilní zařízení, aby stránku vizuálně nezmenšovala, ale přizpůsobila ji fyzické šířce displeje.

## Teorie tagů, atributy a jejich klasifikace

Základním stavebním kamenem HTML je **Tag (značka)**, ohraničený lomenými závorkami `< >`. Tag obaluje obsah a propůjčuje mu logický význam. Modifikaci chování, identifikaci nebo napojení dat zajišťují **Atributy**, které se vkládají výhradně do otevírací značky ve formátu `klic="hodnota"` (např. identifikátor `id`, přiřazení do skupiny `class`, nebo cílová adresa odkazu `href`).

### 1. Rozdělení podle párovosti (Syntaxe)

- **Párové tagy**: Obalují cílový obsah, na který mají vliv. Striktně vyžadují otevírací i uzavírací značku (obsahující lomítko). Pokud se párový tag neuzavře, parser prohlížeče se pokusí chybu "uhodnout", což může vést k rozbití celého zbytku DOM stromu. Příklad: `<div>Obsah kontejneru</div>`.
- **Nepárové tagy (Empty elements)**: Nemají žádný vnitřní textový obsah. Slouží k vložení prvku přímo na definované místo v DOM stromu. V éře XHTML se musely ukončovat lomítkem na konci (např. `<br />`), avšak v HTML5 to není vyžadováno. Příklad: `<img>` (vykreslení obrázku), `<br>` (vynucené zalomení řádku), `<hr>` (horizontální čára), `<input>` (vstupní pole formuláře).

### 2. Rozdělení podle vizuálního zobrazení (Display model)

Přestože fyzické vykreslení lze zcela změnit pomocí CSS, HTML definuje výchozí chování tagů ve dvou rovinách.

- **Blokové elementy (Block-level)**: Chovají se jako boxy poskládané pod sebou. Zabírají celou dostupnou šířku (100 %) svého rodičovského kontejneru a vynucují zalomení řádku před sebou i za sebou. Lze jim v CSS nastavit exaktní výšku i šířku (height/width). Příklady: `<div>`, `<p>`, `<h1>` až `<h6>`, `<ul>`, `<form>`, `<table>`. Blokové elementy mohou běžně obsahovat jiné blokové i řádkové elementy.
- **Řádkové elementy (Inline)**: Chovají se jako text. Obalují pouze přesnou šířku svého obsahu a plynule pokračují vedle sebe na jednom řádku, dokud nenarazí na okraj okna, kde se zalomí. Typograficky a inženýrsky jim nelze aplikovat horní či dolní rozměry v pixelech. Příklady: `<span>` (obecný řádkový obalovač), `<a>` (odkaz), `<strong>` (tučné zvýraznění), `<em>` (kurzíva). Řádkové elementy by z logiky věci nikdy neměly obsahovat elementy blokové.

### 3. Sémantické elementy

Příchod HTML5 radikálně změnil způsob návrhu struktury (Markup). Dříve vývojáři stavěli weby pomocí desítek do sebe zanořených obecných tagů `<div>` s různými třídami (tzv. "Div-soup" nebo "Divitida"). To poskytovalo nulovou informaci pro strojové zpracování.
HTML5 definovalo **sémantické tagy**, které nesou exaktní význam nejen pro lidské oko, ale hlavně pro vyhledávací roboty (Googlebot) a čtečky obrazovky pro nevidomé (Accessibility/A11y).

- `<header>`: Hlavička stránky nebo článku (typicky obsahuje logo a nadpis).
- `<nav>`: Blok obsahující primární navigační odkazy webu.
- `<main>`: Obaluje absolutně unikátní, stěžejní obsah dané podstránky (na stránce smí být pouze jeden).
- `<article>`: Nezávislý a samostatně šiřitelný obsah (např. příspěvek na blogu, produktová karta).
- `<section>`: Logické rozdělení obsahu (např. kapitola), často uvedeno vlastním nadpisem.
- `<aside>`: Postranní panel, jehož obsah okrajově souvisí s hlavním textem (reklamy, tagy, související články).
- `<footer>`: Patička stránky obsahující autorská práva, kontakty nebo obchodní podmínky.

## Praktický inženýrský příklad nasazení tagů

Následující kód ukazuje integraci formuláře a tabulky s důrazem na přístupnost (Accessibility) a korektní strukturu dat zasílaných na server.

```html
<!-- Sémantický kontejner pro hlavní obsah, usnadňující procházení pro čtečky nevidomých -->
<main>
  <section id="registrace">
    <h2>Registrace nového uživatele</h2>

    <!-- Formulář odesílající data přes HTTP metodu POST na definovaný endpoint -->
    <!-- Parametr enctype umožňuje odesílání binárních souborů (např. profilové fotky) -->
    <form
      action="/api/v1/users/submit"
      method="POST"
      enctype="multipart/form-data"
    >
      <!-- Tag label je svázán s inputem přes atribut 'for' a 'id'. 
                 Kliknutí na text labelu fokusuje samotné vstupní pole. -->
      <label for="username">Uživatelské jméno:</label>
      <!-- Vstupní pole s atributem required blokuje odeslání prázdných dat rovnou v prohlížeči -->
      <input
        type="text"
        id="username"
        name="user_login"
        placeholder="Zadejte login"
        required
      />

      <label for="password">Bezpečné heslo:</label>
      <!-- Typ password maskuje zadávané znaky zobrazením hvězdiček -->
      <input type="password" id="password" name="user_pass" required />

      <!-- Tlačítko typu submit iniciuje odeslání všech inputů uvnitř form tagu -->
      <button type="submit">Vytvořit účet</button>
    </form>
  </section>

  <section id="infrastruktura">
    <h2>Přehled dostupných serverů</h2>
    <!-- Definice strukturované datové tabulky -->
    <table border="1">
      <!-- Hlavička tabulky s tučným formátováním nadpisů sloupců (th) -->
      <thead>
        <tr>
          <th>Hostname</th>
          <th>IPv4 Adresa</th>
          <th>Status OS</th>
        </tr>
      </thead>
      <!-- Samotné tělo tabulky s datovými buňkami (td) v řádcích (tr) -->
      <tbody>
        <tr>
          <td>web-prod-01.local</td>
          <td>192.168.1.10</td>
          <td><strong>Running</strong></td>
        </tr>
        <tr>
          <td>db-master.local</td>
          <td>10.0.0.50</td>
          <td><em>Maintenance</em></td>
        </tr>
      </tbody>
    </table>
  </section>

  <!-- Nepárový tag obrázku. Atribut alt je kritický pro SEO a nevidomé uživatele v případě,
         že se obrázek z důvodu chyby na síti nepodaří stáhnout a vykreslit. -->
  <img
    src="assets/images/server-rack-diagram.jpg"
    alt="Fotografie architektury datového centra v Praze"
  />
</main>
```
