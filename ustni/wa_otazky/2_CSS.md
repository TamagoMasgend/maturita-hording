---
created: 2026-05-06T23:20
updated: 2026-05-20T13:03
---
# 2. Kaskádové styly (CSS)

## Koncept a účel CSS ve webovém inženýrství

**CSS (Cascading Style Sheets)** je specializovaný, deklarativní programovací jazyk sloužící k definování vizuální prezentace, rozložení a typografie dokumentů napsaných ve značkovacích jazycích (typicky HTML nebo XML).

Hlavním a absolutním inženýrským přínosem CSS je **oddělení struktury (obsahu) od formy (vzhledu)**. Zatímco HTML se soustředí výhradně na to, aby text rozčlenilo do DOM stromu a dalo mu sémantický význam (např. _co_ je nadpis a _co_ je odstavec), CSS přebírá plnou odpovědnost za to, _jak_ se tento prvek fyzicky vykreslí na grafickém zařízení (barva pixelů, rozměry na obrazovce, pozicování, animace, typografie). Toto striktní oddělení umožňuje vývojářům změnit kompletní vzhled tisíců podstránek celého portálu pouhou úpravou jediného CSS souboru, aniž by museli zasahovat do zdrojových HTML dat.

### Vykreslovací pipeline v prohlížeči (Rendering Engine)

Pochopení principu CSS vyžaduje znalost, jak prohlížeč (např. jádro Blink v Chrome nebo WebKit v Safari) transformuje kód na pixely.

1. Prohlížeč stáhne CSS soubor a spustí vlastní lexikální parser, který ze selektorů a pravidel sestaví v operační paměti strukturu zvanou **CSSOM (CSS Object Model)**.
2. V další fázi engin spojí strukturální strom HTML (**DOM**) s pravidly formátování (**CSSOM**) a vytvoří tzv. **Render Tree**. Tento finální strom obsahuje již pouze ty prvky, které budou reálně viditelné na obrazovce (elementy skryté pravidlem `display: none` jsou z Render Tree vynechány).
3. Na Render Tree se aplikuje proces **Layout (nebo Reflow)**, kdy se matematicky vypočítá přesná geometrie – X/Y souřadnice a šířka/výška boxů pro každý prvek na monitoru.
4. V závěrečném kroku **Paint** posílá prohlížeč instrukce grafické kartě (GPU), aby vypočítané geometrické obdélníky vyplnila konkrétními barvami, stíny a textem.

Termín "Kaskádové" označuje přísný deterministický algoritmus, kterým engin prohlížeče vyhodnocuje konflikty (když například dvě různá CSS pravidla požadují obarvit stejný nadpis jinou barvou). Priorita se vypočítává na základě **Specifičnosti selektoru** (ID má vyšší váhu než Třída), umístění v kódu (pozdější pravidlo přepisuje to dřívější) a přítomnosti absolutního přepínače `!important`.

## Standardizace a vývojové moduly

CSS je otevřený standard vyvíjený konsorciem **W3C (World Wide Web Consortium)**.

- Historicky vznikly monolitické standardy CSS 1 (v roce 1996) a CSS 2 (v roce 1998).
- Aktuálním platným standardem je **CSS3**. Inženýři z W3C zcela opustili dřívější koncept "velkých vydání" a CSS3 rozštěpili do desítek nezávisle vyvíjených specifikací (Modulů) – existuje tak například nezávislý _CSS Color Module_, _CSS Animations_, nebo _CSS Grid Layout_. Díky modularizaci mohou tvůrci prohlížečů implementovat nové technologie okamžitě po dokončení a odladění konkrétního modulu, aniž by se muselo roky čekat na schválení rozsáhlého balíku celého jazyka.

## Metody zápisu a napojení do HTML dokumentu

Z inženýrského a architektonického hlediska (Clean Code) se formátovací pravidla dají do HTML dokumentu vpravit třemi naprosto izolovanými způsoby.

1. **Inline styl (Přímý zápis do značky - Antipattern)**:
   Pravidla se píší přímo do hodnoty HTML atributu `style` uvnitř otevíracího tagu. Tento přístup má ze všech nejvyšší výpočetní prioritu (přebije většinu ostatních zápisů), ale zcela porušuje fundamentální princip oddělení obsahu od vzhledu. Zcela znemožňuje jakoukoliv hromadnou správu, protože při změně barvy musíte najít a přepsat kód na tisíci místech ručně.

   ```html
   <!-- Tvrdé definování vzhledu bez možnosti snadné změny napříč webem -->
   <p style="color: red; font-size: 14px; margin-top: 10px;">
     Červený text s horním okrajem
   </p>
   ```

2. **Internal / Embedded styl (V hlavičce dokumentu)**:
   CSS kód je soustředěn do párového tagu `<style>`, který se vkládá výhradně dovnitř bloku `<head>`. Aplikuje se pouze a výhradně na prvky nacházející se na té jedné konkrétní fyzické stránce. Je to lepší než inline, ale stále neumožňuje sdílení kódu napříč celým projektem. Zůstává však jedinou spolehlivou volbou pro vývojáře HTML e-mailových šablon (newsletterů), kde e-mailoví klienti externí soubory z bezpečnostních důvodů blokují.

   ```html
   <head>
     <style>
       /* Třída aplikovaná jen na elementy této jediné stránky */
       .upozorneni {
         background-color: yellow;
         padding: 5px;
       }
     </style>
   </head>
   ```

3. **External styl (Externí souborový odkaz - Průmyslový standard)**:
   Jediné správné, vysoce škálovatelné řešení. Všechna CSS pravidla leží ve zcela nezávislém textovém souboru s příponou `.css`. HTML dokument tento soubor pouze načítá z hlavičky pomocí nepárového tagu `<link>`. Výhoda neleží jen ve snadné správě kódu (upravím soubor na serveru a změní se vizuál milionu podstránek), ale hlavně v infrastruktuře sítě. Prohlížeč si externí `.css` soubor stáhne jen při prvním načtení a uloží si ho do lokální mezipaměti (Cache). U každého dalšího kliknutí napříč webem se formátování načítá přímo z lokálního disku klienta. Díky tomu se drasticky snižuje síťová zátěž (Bandwidth) i zatížení procesoru serveru.

   ```html
   <head>
     <link rel="stylesheet" href="assets/css/main.css" />
   </head>
   ```

## Architektura Selektorů

Aby jazyk CSS věděl, na který konkrétní HTML uzel v DOM stromu má pravidla aplikovat, využívá takzvané **Selektory**. Syntaxe struktury je vždy tvořena blokem: `selektor { vlastnost: hodnota; }`. Kód uvnitř složených závorek tvoří tzv. deklaraci.

1. **Univerzální selektor (`*`)**:
   Aplikuje formátování plošně a nekompromisně na naprosto každý element v DOM stromu. Z výkonnostního hlediska velmi náročný. Typicky se užívá hned na prvním řádku souboru k provedení tzv. "CSS Resetu" – zrušení výchozích prohlížečových okrajů, které by jinak rozhodily matematický výpočet šířky.
2. **Typový / Elementový selektor**:
   Cílí plošně na všechny uzly podle jejich systémového názvu HTML tagu.
   - `h1 { color: #333333; font-weight: bold; }` (Obarví všechny nadpisy první úrovně tmavě šedou).
3. **Selektor Třídy (Class selector začínající znakem `.` )**:
   Třída je opakovatelně použitelný identifikátor a základní stavební blok pro tvorbu design systémů (např. Bootstrap). Jeden element může v HTML nést více tříd (oddělených mezerou) a jedna třída může být napříč projektem definována pro tisíce různých elementů.
   - `.tlacitko-error { background-color: red; color: white; }` (Aplikováno přes HTML parametr `<button class="tlacitko-error">`).
4. **Selektor ID (začínající znakem `#`)**:
   Extrémně silný identifikátor, který musí být z podstaty W3C normy na jedné konkrétní HTML stránce absolutně unikátní (ID smí mít pouze jeden jediný element). Nese matematicky obrovsky vysokou prioritu (Specifičnost) a s přehledem přepisuje styly definované přes Třídy.
   - `#hlavni-navigace { display: flex; position: sticky; top: 0; }` (Aplikováno na unikátní HTML kontejner `<nav id="hlavni-navigace">`).
5. **Atributový selektor (`[ ]`)**:
   Sofistikované zaměření na elementy, které nesou konkrétní HTML parametr, nezávisle na třídách. Užitečné při stylování komplexních formulářů.
   - `input[type="text"] { border: 1px solid gray; }` (Obarví ohraničení pouze u textových polí, ale ignoruje checkboxy nebo submit tlačítka).
6. **Pseudotřídy (`:`) a Pseudoelementy (`::`)**:
   Nezaměřují se primárně na statický HTML element jako takový, ale zachytávají jeho interaktivní, dynamický **stav** v paměti RAM (např. po interakci s myší), nebo vybírají logické virtuální podčásti elementu.
   - `a:hover { text-decoration: underline; color: blue; }` (Aktivuje podtržení výhradně v čase, kdy nad logickou oblast odkazu najede kurzor myši uživatele).
   - `p::first-line { font-weight: bold; }` (Udělá tučným písmem pouze první vyrenderovaný řádek odstavce, a to i když uživatel velikost okna manuálně zmenšuje a text dynamicky přetéká na další řádky).

## Praktická ukázka moderního uspořádání

Ukázka průmyslového stylování využívajícího definici CSS Box Modelu (aby tloušťka okrajů nerozbíjela zadanou šířku kontejneru) a modulu Flexbox pro dokonalé zarovnání elementů nezávisle na rozlišení monitoru.

**Strukturální HTML kód:**

```html
<div class="aplikacni-kontejner">
  <div class="karta-uzivatele" id="admin-profil">
    <h2>Systémový administrátor</h2>
    <p>Tento box je zarovnán flexboxem do naprostého středu obrazovky.</p>
  </div>
</div>
```

**Zápis formátování v externím CSS souboru (`styles.css`):**

```css
/* Univerzální CSS Reset a bezpečný výpočet okrajů.
   Padding (vnitřní okraj) a Border se nebudou přičítat k celkové šířce elementů. */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Modul Flexbox nasazený na rodičovský kontejner.
   Eliminuje složité matematické plovoucí zarovnávání (float). */
.aplikacni-kontejner {
  display: flex;
  justify-content: center; /* Zarovnání potomků do absolutního středu osy X */
  align-items: center; /* Zarovnání potomků do absolutního středu osy Y */
  height: 100vh; /* Výška nastavena přesně na 100% výšky okna prohlížeče (Viewport Height) */
  background-color: #2c3e50; /* Tmavé hexadecimální pozadí */
}

/* Návrh uživatelské karty (komponenty) přes třídu */
.karta-uzivatele {
  width: 350px;
  padding: 25px;
  background-color: #ffffff;
  border-radius: 12px; /* Zaoblené rohy */
  /* Parametry stínu: posunX, posunY, rozostření, rozprostření, RGBA barva (černá s 15% průhledností) */
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.15);
  transition: transform 0.3s ease; /* Plynulá animace při změně stavu (hover) */
}

/* Selektor ID má nejvyšší prioritu, takže spolehlivě přepíše pozadí z výše definované třídy */
#admin-profil {
  border-top: 5px solid #e74c3c;
}

/* Nasazení Pseudotřídy stavu: Interaktivní reakce prohlížeče bez nutnosti psát JavaScript. */
.karta-uzivatele:hover {
  transform: translateY(
    -8px
  ); /* Matematická transformace po ose Y: posun boxu nahoru při najetí myší */
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.25); /* Zvýraznění stínu simulující 'nadnesení' do prostoru */
}
```
