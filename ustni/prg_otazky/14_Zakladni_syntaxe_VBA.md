# 14. Základní syntaxe jazyka VBA

**VBA (Visual Basic for Applications)** je interpretovaný programovací jazyk integrovaný do aplikací sady Microsoft Office (Excel, Word, Access). Slouží k automatizaci opakujících se úloh, tvorbě vlastních funkcí pro výpočty v buňkách a rozšíření funkcionality aplikace nad rámec standardních nástrojů. VBA je dialekt jazyka Visual Basic — jeho syntaxe je anglická, case-insensitive a konstrukty se ukončují klíčovými slovy (ne složenými závorkami).

## Objektový model Excelu

VBA pracuje přes hierarchický **objektový model** — každý prvek Excelu je reprezentován objektem s vlastnostmi (Properties) a metodami (Methods). Hierarchie od nejvyšší úrovně:

- **`Application`** — samotná běžící instance Excelu. Nejvyšší objekt.
- **`Workbook`** — sešit (jeden `.xlsx` soubor). Kolekce: `Workbooks`.
- **`Worksheet`** — list v sešitu. Kolekce: `Worksheets`.
- **`Range`** — buňka nebo oblast buněk. Příklady: `Range("A1")`, `Cells(1, 1)`, `Range("A1:C10")`.

```vba
' Přístup přes celou hierarchii
Application.Workbooks("Sešit1.xlsx").Worksheets("List1").Range("A1").Value = "Ahoj"

' Zkrácený přístup — pokud pracujeme v aktivním sešitu a listu
Range("A1").Value = "Ahoj"
Cells(1, 1).Value = "Ahoj"   ' Cells(řádek, sloupec) — obojí 1-based
```

## Vývojové prostředí (VBE)

VBA editor se spouští klávesovou zkratkou **Alt + F11**. Skládá se z:

- **Project Explorer:** Strom projektů a modulů (vlevo).
- **Code Window:** Oblast pro zápis kódu.
- **Immediate Window:** Okno pro okamžité spouštění příkazů (Ctrl+G) — slouží k ladění.
- **Module:** Standardní modul pro `Sub` a `Function` procedury.

## Proměnné a datové typy

Proměnné se deklarují klíčovým slovem **`Dim`** a typem za **`As`**. Bez deklarace je proměnná automaticky typu `Variant` (univerzální, ale paměťově neefektivní).

**`Option Explicit`** umístěné na začátek modulu vynutí povinnou deklaraci všech proměnných — chrání před překlepty v názvech (bez něj typo vytvoří novou proměnnou `Variant` s hodnotou 0).

```vba
Option Explicit   ' nutí deklarovat vše — doporučená praxe

Dim jmeno    As String    ' textový řetězec
Dim vek      As Integer   ' celé číslo  2 B (-32 768 až 32 767)
Dim plat     As Long      ' celé číslo  4 B (-2 147 483 648 až 2 147 483 647)
Dim cisloID  As LongLong  ' celé číslo  8 B (pouze 64bit Office)
Dim teplota  As Double    ' reálné číslo 8 B (IEEE 754 double precision)
Dim aktivni  As Boolean   ' logická hodnota True/False
Dim datum    As Date      ' datum a čas

' Inicializace
jmeno = "Jan Novak"
vek   = 25
plat  = 48000
```

**`Integer`** (2 B) je historicky zastaralý — pro celá čísla se doporučuje **`Long`** (4 B), který pokryje veškeré běžné hodnoty a je na moderních 32/64bit procesorech výkonnější. **`LongLong`** (8 B) je dostupný výhradně v 64bitové verzi Office.

## Podmínky

VBA ukončuje každý blok klíčovými slovy — `End If`, `End Select`, `End Sub` atd.

```vba
Dim x As Integer
x = 15

' Jednořádkový If bez End If (pouze jedna instrukce)
If x > 0 Then MsgBox "Kladne"

' Víceřádkový If...ElseIf...Else...End If
If x > 10 Then
    MsgBox "Vetsi nez 10"
ElseIf x = 10 Then
    MsgBox "Rovno 10"
Else
    MsgBox "Mensi nez 10"
End If

' Select Case (ekvivalent switch)
Select Case x
    Case 1
        MsgBox "Jedna"
    Case 2, 3          ' více hodnot
        MsgBox "Dva nebo tri"
    Case 10 To 20      ' rozsah hodnot
        MsgBox "Deset az dvacet"
    Case Else
        MsgBox "Neco jineho"
End Select
```

## Cykly

```vba
' For...Next — počítaný cyklus
Dim i As Integer
For i = 1 To 10
    Debug.Print i
Next i

' For...Next se záporným krokem
For i = 10 To 1 Step -2
    Debug.Print i      ' 10, 8, 6, 4, 2
Next i

' For Each...Next — průchod kolekcí
Dim ws As Worksheet
For Each ws In ThisWorkbook.Worksheets
    Debug.Print ws.Name
Next ws

' Do While...Loop — podmínka na začátku
Dim radek As Integer
radek = 1
Do While Cells(radek, 1).Value <> ""
    Debug.Print Cells(radek, 1).Value
    radek = radek + 1
Loop

' Do...Loop While — podmínka na konci (vždy alespoň jeden průchod)
Do
    radek = radek + 1
Loop While Cells(radek, 1).Value <> ""
```

## Sub a Function

```vba
' Sub — nepřijímá návratovou hodnotu (void procedura)
Sub PozdravUzivatele()
    MsgBox "Dobry den, " & Application.UserName
End Sub

' Sub s parametry
Sub ZapisDoBunky(radek As Integer, sloupec As Integer, hodnota As String)
    Cells(radek, sloupce).Value = hodnota
End Sub

' Function — vrací hodnotu (použitelná i jako vzorec v buňce)
Function SoucetCtverce(a As Double, b As Double) As Double
    SoucetCtverce = a ^ 2 + b ^ 2   ' návratová hodnota = přiřazení jménu funkce
End Function

' Volání
Call PozdravUzivatele()                   ' nebo jen PozdravUzivatele
ZapisDoBunky 1, 1, "Test"
Dim vysledek As Double
vysledek = SoucetCtverce(3, 4)            ' = 25
```

## Konstrukt `With`

`With` zkrátí opakovaný přístup k vlastnostem jednoho objektu:

```vba
With Range("A1")
    .Value = "Nadpis"
    .Font.Bold = True
    .Font.Size = 14
    .Interior.Color = RGB(255, 255, 0)
End With
```

## Logické operátory ve VBA

| Operátor | Popis |
|---|---|
| `And` | Logický součin — obě podmínky pravdivé. |
| `Or` | Logický součet — alespoň jedna pravdivá. |
| `Not` | Negace. |
| `=` | Porovnání rovnosti (ne přiřazení, záleží na kontextu). |
| `<>` | Nerovnost. |
