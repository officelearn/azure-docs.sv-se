---
title: Systemfunktioner
description: Lär dig mer om SQL system-funktioner i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b0e9c751d46f805af75196da464a39783c95ae6a
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619995"
---
# <a name="system-functions"></a>Systemfunktioner

 Cosmos DB innehåller många inbyggda SQL-funktioner. Kategorier av inbyggda funktionerna finns nedan.  
  
|Funktion|Beskrivning|  
|--------------|-----------------|  
|[Matematiska funktioner](#mathematical-functions)|Matematiska funktioner utför en beräkning, vanligtvis baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde.|  
|[Funktioner för typkontroll](#type-checking-functions)|Med funktionerna för typkontroll kan du kontrollera typen av ett uttryck i SQL-frågor.|  
|[Strängfunktioner](#string-functions)|Strängfunktioner utföra en åtgärd på ett strängvärde för indata och returnerar en sträng, numeriskt eller booleskt värde.|  
|[Matrisfunktioner](#array-functions)|Matrisfunktioner kan du utföra en åtgärd på en matris indatavärdet och returnera numeriska, booleskt värde eller Matrisvärde.|
|[Datum-och tids funktioner](#date-time-functions)|Med funktionerna för datum och tid kan du hämta aktuellt UTC-datum och tid i två formulär; en numerisk tidstämpel vars värde är UNIX-epoken i millisekunder eller som en sträng som följer ISO 8601-formatet.|
|[Spatial funktioner](#spatial-functions)|Funktionerna spatial utföra en åtgärd på en spatialobjektet indatavärdet och returnerar ett numeriskt eller booleskt värde.|  

Nedan visas en lista över funktioner i varje kategori:

| Funktionsgrupp | Åtgärder |
|---------|----------|
| Matematiska funktioner | ABS, TAK, EXP, VÅNING, LOG, LOG10, POTENS, ROUND, SIGN, SQRT, FYRKANT, TRUNC, ARCCOS, ARCSIN, ARCTAN, ATN2, COS, COT, GRADER, PI, RADIANER, RAND, SIN, TAN |
| Typ kontroll funktioner | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Strängfunktioner | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Matrisfunktioner | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH och ARRAY_SLICE |
| Datum-och tids funktioner | GETCURRENTDATETIME, GETCURRENTTIMESTAMP,  |
| Spatiella funktioner | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Om du för närvarande använder en användardefinierad funktion (UDF) för vilken en inbyggd funktion nu är tillgänglig, kan motsvarande inbyggda funktion gå snabbare att köra och effektivare.

Den största skillnaden mellan Cosmos DB Functions och ANSI SQL Functions är att Cosmos DB funktioner är utformade för att fungera bra med schema lösa och blandade schema data. Om t. ex. en egenskap saknas eller har ett icke-numeriskt värde `unknown`, hoppas objektet över i stället för att returnera ett fel.

##  <a name="mathematical-functions"></a> Matematiska funktioner  

De matematiska funktionerna utför en beräkning baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde.

Du kan köra frågor som i följande exempel:

```sql
    SELECT VALUE ABS(-4)
```

Resultatet är:

```json
    [4]
```

Här är en tabell med inbyggda matematiska funktioner som stöds.
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[TAK](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[GRADER](#bk_degrees)|  
|[EXP](#bk_exp)|[VÅNING](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANER](#bk_radians)|[AVRUNDA](#bk_round)|[SLUMP](#bk_rand)|
|[SIN](#bk_sin)|[SQRT](#bk_sqrt)|[RUTA](#bk_square)|
|[INLOGGNING](#bk_sign)|[TAN](#bk_tan)|[AVKORTA](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Returnerar det absoluta (positiva) värdet för det angivna numeriska uttrycket.  
  
 **Syntax**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel visar resultatet av att använda funktionen ABS på tre olika antal.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> FUNKTIONERNA ACOS  
 Returnerar vinkeln i radianer vars cosinus är det angivna numeriska uttrycket. Kallas även arccosinus.  
  
 **Syntax**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras ACOS-1.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Returnerar vinkeln i radianer vars sinus är det angivna numeriska uttrycket. Detta kallas också arcsinus.  
  
 **Syntax**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras ASIN-1.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Returnerar vinkeln i radianer vars tangent är det angivna numeriska uttrycket. Detta kallas även arctangens.  
  
 **Syntax**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras ATAN av det angivna värdet.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Returnerar huvudvärdet arctangens av y / x, uttryckt i radianer.  
  
 **Syntax**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel beräknar ATN2 för den angivna x- och y komponenter.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> TAK  
 Returnerar det minsta heltalsvärdet som är större än eller lika med det angivna numeriska uttrycket.  
  
 **Syntax**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel visas positivt numeriskt och negativa noll värden med funktionen tak.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Returnerar trigonometrisk cosinus för den angivna vinkeln i radianer i det angivna uttrycket.  
  
 **Syntax**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel beräknar COS för den angivna vinkeln.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Returnerar trigonometrisk cotangens för den angivna vinkeln i radianer i det angivna numeriska uttrycket.  
  
 **Syntax**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel beräknar COT för den angivna vinkeln.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> GRADER  
 Returnerar motsvarande vinkel i grader för en vinkel som anges i radianer.  
  
 **Syntax**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras grad i en vinkeln för PI/2 radianer.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> VÅNING  
 Returnerar det största heltalsvärdet som är mindre än eller lika med det angivna numeriska uttrycket.  
  
 **Syntax**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel visas positivt numeriskt och negativa noll värden med funktionen VÅNING.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Returnerar exponentiell värdet för det angivna numeriska uttrycket.  
  
 **Syntax**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Kommentarer**  
  
  Ständiga **e** (2.718281...) är basen för logaritmen.  
  
  E upphöjt till ett tal är konstanten **e** upphöjt till för talet. Till exempel EXP(1.0) = e ^ 1.0 = 2.71828182845905 och EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Exponenten för den naturliga logaritmen för ett tal är själva siffran: EXP (LOG (n)) = n. Och den naturliga logaritmen av exponenten för ett tal är själva siffran: LOG (EXP (n)) = n.  
  
  **Exempel**  
  
  Exemplet nedan deklarerar en variabel och returnerar exponentiell värdet för den angivna variabeln (10).  
  
```  
SELECT EXP(10) AS exp  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 I följande exempel returneras exponentiell värdet för natural logarithm 20 och den naturliga logaritmen för e upphöjt till 20. Eftersom dessa funktioner är inverterade funktioner från varandra, är det returnera värdet med avrundning för flytande punkt matematiska i båda fallen 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Returnerar den naturliga logaritmen för det angivna numeriska uttrycket.  
  
 **Syntax**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
- `base`  
  
   Valfritt numeriska argument som anger basen för logaritmen.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Kommentarer**  
  
  Som standard returnerar LOG() den naturliga logaritmen. Du kan ändra logaritmens bas till ett annat värde med hjälp av den grundläggande icke obligatoriska parametern.  
  
  Den naturliga logaritmen är logaritmen för talet **e**, där **e** är en onormal konstant ungefär lika 2.718281828.  
  
  Den naturliga logaritmen av exponenten för ett tal är själva siffran: LOG (EXP (n)) = n. Och exponenten för den naturliga logaritmen för ett tal är själva siffran: EXP (LOG (n)) = n.  
  
  **Exempel**  
  
  Exemplet nedan deklarerar en variabel och Returnerar logaritmen värdet för den angivna variabeln (10).  
  
```  
SELECT LOG(10) AS log  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 I följande exempel beräknar LOGGEN för e upphöjt till ett tal.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Returnerar 10-logaritmen av uttryck.  
  
 **Syntax**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Kommentarer**  
  
  Funktionerna LOG10 och POWER är omvänt relaterade till varandra. Till exempel 10 ^ LOG10(n) = n.  
  
  **Exempel**  
  
  Exemplet nedan deklarerar en variabel och returnerar LOG10 värdet för den angivna variabeln (100).  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Returnerar konstantvärdet för PI.  
  
 **Syntax**  
  
```  
PI ()  
```  
   
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras värdet för PI.  
  
```  
SELECT PI() AS pi 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Returnerar värdet för det angivna uttrycket till angiven potens.  
  
 **Syntax**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
- `y`  
  
   Strömbrytare som att höja `numeric_expression`.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel visar att ett tal upphöjt till 3 (kuben talets).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANER  
 Returnerar radianer när ett numeriskt uttryck i grader anges.  
  
 **Syntax**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel tar några vinklar som indata och returnerar motsvarande radian värden.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  Här är resultatuppsättningen.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> AVRUNDA  
 Returnerar ett numeriskt värde avrundat till närmaste heltal.  
  
 **Syntax**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Kommentarer**
  
  Avrundnings åtgärden som utförs följer följande mitt punkts avrundning bort från noll. Om indatatypen är ett numeriskt uttryck som är exakt mellan två heltal blir resultatet det närmaste heltal svärdet bort från noll.  
  
  |< Numeric_Expression >|Rektangel|
  |-|-|
  |– 6,5000|-7|
  |– 0,5|-1|
  |0,5|1|
  |6,5000|7||
  
  **Exempel**  
  
  I följande exempel Avrundar följande positiva och negativa tal till närmaste heltal.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Här är resultatuppsättningen.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

#### <a name="bk_rand"></a>SLUMP
 Returnerar ett slumpmässigt genererat numeriskt värde från [0, 1).
 
 **Syntax**  
  
```  
RAND ()  
```  

  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras ett slumpmässigt genererat numeriskt värde.  
  
```  
SELECT RAND() AS rand 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"rand": 0.87860053195618093}]  
``` 

####  <a name="bk_sign"></a> INLOGGNING  
 Returnerar positiv (+ 1), noll (0) eller minustecken (-1) i det angivna numeriska uttrycket.  
  
 **Syntax**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras LOGGA värden av tal från -2 till 2.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Returnerar trigonometrisk sinus för den angivna vinkeln i radianer i det angivna uttrycket.  
  
 **Syntax**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel beräknar SIN för den angivna vinkeln.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Returnerar kvadratroten för det angivna numeriska värdet.  
  
 **Syntax**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returnerar roten av talen 1-3.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> RUTA  
 Returnerar för det angivna numeriska värdet.  
  
 **Syntax**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras rutor för tal 1-3.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Returnerar tangens för den angivna vinkeln i radianer i det angivna uttrycket.  
  
 **Syntax**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel beräknar tangens för PI () / 2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> AVKORTA  
 Returnerar ett numeriskt värde trunkerat till närmaste heltal.  
  
 **Syntax**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel trunkerar följande positiva och negativa tal till närmaste heltal.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>Typ kontroll funktioner

Med typ kontroll funktionerna kan du kontrol lera typen av uttryck i en SQL-fråga. Du kan använda typ kontroll funktioner för att avgöra vilka typer av egenskaper som finns i objekt i farten, när de är variabla eller okända. Här är en tabell över inbyggda typ kontroll funktioner som stöds:

Typkontroll mot indatavärden stöd för följande funktioner och var och en returnerar ett booleskt värde.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Returnerar ett booleskt värde som anger om vilken typ av det angivna uttrycket är en matris.  
  
 **Syntax**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argument**  
  
- `expression`  
  
   Är ett giltigt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar objekt i JSON-boolesk, tal, sträng, null, objektet, matrisen och odefinierad typer med hjälp av funktionen IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Returnerar ett booleskt värde som anger om det angivna uttrycket är ett booleskt värde.  
  
 **Syntax**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argument**  
  
- `expression`  
  
   Är ett giltigt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar objekt i JSON-boolesk, tal, sträng, null, objektet, matrisen och odefinierad typer med hjälp av funktionen IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde.  
  
 **Syntax**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argument**  
  
- `expression`  
  
   Är ett giltigt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel söker efter förekomsten av en egenskap i det angivna JSON-dokumentet. Först returnerar true eftersom ”a” finns, men andra returnerar FALSKT eftersom ”b” saknas.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Returnerar ett booleskt värde som anger om vilken typ av det angivna uttrycket är null.  
  
 **Syntax**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argument**  
  
- `expression`  
  
   Är ett giltigt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar objekt i JSON-boolesk, tal, sträng, null, objektet, matrisen och odefinierad typer med hjälp av funktionen IS_NULL.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Returnerar ett booleskt värde som anger om det angivna uttrycket är ett tal.  
  
 **Syntax**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argument**  
  
- `expression`  
  
   Är ett giltigt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar objekt i JSON-boolesk, tal, sträng, null, objektet, matrisen och odefinierad typer med hjälp av funktionen IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Returnerar ett booleskt värde som anger om det angivna uttrycket är ett JSON-objekt.  
  
 **Syntax**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argument**  
  
- `expression`  
  
   Är ett giltigt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar objekt i JSON-boolesk, tal, sträng, null, objektet, matrisen och odefinierad typer med hjälp av funktionen IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Returnerar ett booleskt värde som anger om det angivna uttrycket är en primitiv hämtas (string, booleskt, numeriska eller null).  
  
 **Syntax**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argument**  
  
- `expression`  
  
   Är ett giltigt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar objekt i JSON-boolesk, tal, sträng, null, objektet, matrisen och odefinierad typer med hjälp av funktionen IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Returnerar ett booleskt värde som anger om det angivna uttrycket är en sträng.  
  
 **Syntax**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argument**  
  
- `expression`  
  
   Är ett giltigt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar objekt i JSON-boolesk, tal, sträng, null, objektet, matrisen och odefinierad typer med hjälp av funktionen IS_STRING.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>Sträng funktioner

Följande skalära funktioner utför en åtgärd på ett sträng indatavärde och returnerar ett sträng värde, ett numeriskt värde eller ett booleskt värde:
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[INNEHÅLLER](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[VÄNSTER](#bk_left)|[LÄNGD](#bk_length)|  
|[LÄGRE](#bk_lower)|[LTRIM](#bk_ltrim)|[ERSÄTT](#bk_replace)|  
|[Replikera](#bk_replicate)|[OMVÄND](#bk_reverse)|[HÖGER](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[DELSTRÄNGEN](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[ÖVRE](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Returnerar en sträng som är resultatet av en sammanfogning av två eller fler strängvärden.  
  
 **Syntax**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel returneras sammanfogad sträng med de angivna värdena.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> INNEHÅLLER  
 Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra.  
  
 **Syntax**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar om ”abc” innehåller ”ab” och ”d”.  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Returnerar ett booleskt värde som anger om först stränguttryck slutar med andra.  
  
 **Syntax**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returneras ”abc” slutar med ”b” och ”bc”.  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Returnerar startpositionen för den första förekomsten av det andra stränguttrycket i det första angivna stränguttrycket eller -1 om strängen inte hittas.  
  
 **Syntax**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel returnerar index för olika delsträngar i ”abc”.  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> VÄNSTER  
 Returnerar den vänstra delen av en sträng med det angivna antalet tecken.  
  
 **Syntax**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
- `num_expr`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel returneras den vänstra delen av ”abc” för värden för olika längd.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LÄNGD  
 Returnerar antalet tecken i angivet stränguttryck.  
  
 **Syntax**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel returnerar längden på en sträng.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LÄGRE  
 Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener.  
  
 **Syntax**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visar hur du använder lägre i en fråga.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Returnerar ett stränguttryck efter att inledande blanksteg har tagits bort.  
  
 **Syntax**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visar hur du använder LTRIM inuti en fråga.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> ERSÄTT  
 Ersätter alla förekomster av ett angivet strängvärde med ett annat strängvärde.  
  
 **Syntax**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visar hur du använder Ersätt i en fråga.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLIKERA  
 Upprepar ett strängvärde ett angivet antal gånger.  
  
 **Syntax**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
- `num_expr`  
  
   Är ett numeriskt uttryck. Om num_expr är negativt eller ej ändligt är resultatet odefinierat.

  > [!NOTE]
  > Den maximala längden på resultatet är 10 000 tecken, t. ex. (längd (str_expr) * num_expr) < = 10 000.
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visar hur du använder replikering i en fråga.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> OMVÄND  
 Returnerar den omvända ordningen av ett strängvärde.  
  
 **Syntax**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visar hur du använder OMVÄND i en fråga.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> HÖGER  
 Returnerar den högra delen av en sträng med det angivna antalet tecken.  
  
 **Syntax**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
- `num_expr`  
  
   Är ett numeriskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel returneras den högra delen av ”abc” för värden för olika längd.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Returnerar ett stränguttryck efter att det tar bort avslutande blanksteg.  
  
 **Syntax**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visar hur du använder RTRIM inuti en fråga.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Returnerar ett booleskt värde som anger om först stränguttryck börjar med andra.  
  
 **Syntax**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel kontrollerar om strängen ”abc” börjar med ”b” och ”a”.  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a>StringToArray  
 Returnerar uttryck som har översatts till en matris. Om uttrycket inte kan översättas returneras undefined.  
  
 **Syntax**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett giltigt skalärt uttryck som ska utvärderas som ett JSON-mat ris uttryck. Observera att kapslade sträng värden måste skrivas med dubbla citat tecken för att vara giltiga. Mer information om JSON-formatet finns i [JSON.org](https://json.org/)
  
  **Returnera typer**  
  
  Returnerar ett mat ris uttryck eller odefinierat.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToArray beter sig mellan olika typer. 
  
 Följande är exempel på giltiga indatatyper.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Här är resultatuppsättningen.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Följande är ett exempel på ogiltiga indatatyper. 
   
 Enkla citat tecken i matrisen är inte giltiga JSON.
Även om de är giltiga i en fråga går de inte att parsa till giltiga matriser. Strängar i mat ris strängen måste antingen föregås av [\\\\"]" eller det omgivande citatet måste vara Single ["]".

```
SELECT
    StringToArray("['5','6','7']")
```

Här är resultatuppsättningen.

```
[{}]
```

Följande är exempel på ogiltiga indatatyper.
   
 Det överförda uttrycket kommer att parsas som en JSON-matris. följande utvärderar inte till typ mat ris och returnerar således odefinierad.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Här är resultatuppsättningen.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a>StringToBoolean  
 Returnerar uttryck som har översatts till ett booleskt värde. Om uttrycket inte kan översättas returneras undefined.  
  
 **Syntax**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett giltigt skalärt uttryck som ska utvärderas som ett booleskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck eller odefinierat.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToBoolean beter sig mellan olika typer. 
 
 Följande är exempel på giltiga indatatyper.

Blank steg tillåts bara före eller efter "true"/"false".

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

Följande är exempel på ogiltiga indatatyper.

 Booleska värden är Skift läges känsliga och måste skrivas med små bokstäver, d.v.s. "sant" och "falskt".

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Här är resultatuppsättningen.  
  
```  
[{}]
``` 

Det överförda uttrycket kommer att parsas som ett booleskt uttryck. de här inmatningarna utvärderas inte till typen Boolean och returneras därför inte.

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Här är resultatuppsättningen.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a>StringToNull  
 Returnerar uttryck översatt till null. Om uttrycket inte kan översättas returneras undefined.  
  
 **Syntax**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett giltigt skalärt uttryck som ska utvärderas som ett null-uttryck.
  
  **Returnera typer**  
  
  Returnerar ett null-uttryck eller ett odefinierat värde.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToNull beter sig mellan olika typer. 

Följande är exempel på giltiga indatatyper.

 Blank steg tillåts bara före eller efter "null".

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

Följande är exempel på ogiltiga indatatyper.

Null är Skift läges känsligt och måste skrivas med alla gemener, t. ex. "null".

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Här är resultatuppsättningen.  
  
```  
[{}]
```  

Det överförda uttrycket kommer att parsas som ett null-uttryck. dessa indata kan inte utvärderas till typen null och returneras därför inte.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a>StringToNumber  
 Returnerar uttryck översatt till ett tal. Om uttrycket inte kan översättas returneras undefined.  
  
 **Syntax**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett giltigt skalärt uttryck som ska utvärderas som ett JSON Number-uttryck. Talen i JSON måste vara ett heltal eller en flyttal. Mer information om JSON-formatet finns i [JSON.org](https://json.org/)  
  
  **Returnera typer**  
  
  Returnerar ett tal uttryck eller ett odefinierat värde.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToNumber beter sig mellan olika typer. 

Blank steg tillåts bara före eller efter talet.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Här är resultatuppsättningen.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

I JSON måste ett giltigt tal vara ett heltal eller ett flytt ALS nummer.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 Här är resultatuppsättningen.  
  
```  
{{}}
```  

Det överförda uttrycket kommer att tolkas som ett tal uttryck. dessa indata utvärderas inte till typ nummer och returneras därför inte. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Här är resultatuppsättningen.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a>StringToObject  
 Returnerar uttryck översatt till ett objekt. Om uttrycket inte kan översättas returneras undefined.  
  
 **Syntax**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett giltigt skalärt uttryck som ska utvärderas som ett JSON-objekt uttryck. Observera att kapslade sträng värden måste skrivas med dubbla citat tecken för att vara giltiga. Mer information om JSON-formatet finns i [JSON.org](https://json.org/)  
  
  **Returnera typer**  
  
  Returnerar ett objekt uttryck eller ett odefinierat objekt.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToObject beter sig mellan olika typer. 
  
 Följande är exempel på giltiga indatatyper.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Här är resultatuppsättningen.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Följande är exempel på ogiltiga indatatyper.
Även om de är giltiga i en fråga, kommer de inte att parsas till giltiga objekt. Strängar inom en objekt sträng måste\\antingen vara undantagna "{" a\\":\\" Str\\"} eller så måste det omgivande citatet vara en enstaka {" a ":" Str "}.

Enkla citat tecken som omger egenskaps namnen är inte giltiga JSON.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Här är resultatuppsättningen.

```  
[{}]
```  

Egenskaps namn utan omgivande citat tecken är inte giltiga JSON.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Här är resultatuppsättningen.

```  
[{}]
``` 

Följande är exempel på ogiltiga indatatyper.

 Det överförda uttrycket kommer att parsas som ett JSON-objekt. dessa indata utvärderas inte till Type-objekt och returneras därför inte.

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Här är resultatuppsättningen.

```
[{}]
```

####  <a name="bk_substring"></a> DELSTRÄNGEN  
 Returnerar en del av ett stränguttryck med början vid den angivna nollbaserade teckenpositionen och fortsätter med den angivna längden eller i slutet av strängen.  
  
 **Syntax**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
- `num_expr`  
  
   Är ett giltigt numeriskt uttryck som anger start-och slut tecken.    
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel Returnerar delsträngen av ”abc” vid 1 och en längd på 1 tecken.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Returnerar en strängrepresentation av skalärt uttryck. 
  
 **Syntax**  
  
```  
ToString(<expr>)
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett skalärt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visas hur ToString beter sig över olika typer.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 Följande framfört:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 I följande exempel visas hur ToString kan användas med andra strängfunktioner som CONCAT.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

Här är resultatuppsättningen.  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
Får följande indata.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
I följande exempel visas hur ToString kan användas med andra strängfunktioner som Ersätt.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
Här är resultatuppsättningen.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 Returnerar ett stränguttryck efter att det tar bort inledande och avslutande blanksteg.  
  
 **Syntax**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visar hur du använder TRIMNING inuti en fråga.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> ÖVRE  
 Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler.  
  
 **Syntax**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argument**  
  
- `str_expr`  
  
   Är ett stränguttryck.  
  
  **Returnera typer**  
  
  Returnerar ett stränguttryck.  
  
  **Exempel**  
  
  I följande exempel visas hur du använder längst upp i en fråga  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>Mat ris funktioner

Följande skalära funktioner utför en åtgärd på ett värde för mat ris värden och returnerar numeriska värden, booleska eller matriskonstanter:
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Returnerar en matris som är resultatet av en sammanfogning av två eller fler matrisvärden.  
  
 **Syntax**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argument**  
  
- `arr_expr`  
  
   Är någon giltig matris-uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett matrisuttryck.  
  
  **Exempel**  
  
  I följande exempel hur du sammanfoga två matriser.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Returnerar ett booleskt värde som anger huruvida matrisen innehåller det angivna värdet. Du kan söka efter en partiell eller fullständig matchning av ett objekt genom att använda ett booleskt uttryck i kommandot. 

**Syntax**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argument**  
  
- `arr_expr`  
  
   Är någon giltig matris-uttryck.  
  
- `expr`  
  
   Är ett giltigt uttryck.  

- `bool_expr`  
  
   Är ett booleskt uttryck. Om det är inställt på "true'and om det angivna sökvärdet är ett objekt, söker kommandot efter en partiell matchning (sökobjektet är en delmängd av ett av objekten). Om den har angetts till false söker kommandot efter en fullständig matchning av alla objekt i matrisen. Standardvärdet om inget värde anges är falskt. 
  
  **Returnera typer**  
  
  Returnerar ett booleskt värde.  
  
  **Exempel**  
  
  I följande exempel hur du kontrollerar om medlemskap i en matris med ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"b1": true, "b2": false}]  
```  

I följande exempel hur du söker efter en partiell matchning av en JSON i en matris med ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Returnerar antalet element i det angivna matrisuttrycket.  
  
 **Syntax**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argument**  
  
- `arr_expr`  
  
   Är någon giltig matris-uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck.  
  
  **Exempel**  
  
  I följande exempel hur du hämtar hur lång en matris med ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Returnerar en del av ett matrisuttryck.
  
 **Syntax**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argument**  
  
- `arr_expr`  
  
   Är någon giltig matris-uttryck.  
  
- `num_expr`  
  
   Nollbaserat numeriskt index som du vill börja matrisen. Negativa värden kan användas för att ange startIndex i förhållande till det sista elementet i matrisen d.v.s. -1-referenser till det sista elementet i matrisen.  

- `num_expr`  

   Maximalt antal element i matrisen resulterande.    

  **Returnera typer**  
  
  Returnerar ett matrisuttryck.  
  
  **Exempel**  
  
  I följande exempel visar hur du hämtar olika segment i en matris med ARRAY_SLICE.  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>Funktionen datum och tid

Med följande skalära funktioner kan du hämta aktuellt UTC-datum och tid i två formulär; en numerisk tidstämpel vars värde är UNIX-epoken i millisekunder eller som en sträng som följer ISO 8601-formatet. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a>GETCURRENTDATETIME
 Returnerar aktuellt UTC-datum och-tid som en ISO 8601-sträng.
  
 **Syntax**
  
```
GETCURRENTDATETIME ()
```
  
  **Returnera typer**
  
  Returnerar aktuellt UTC-datum och klock slags ISO 8601-sträng värde. 

  Detta uttrycks i formatet ÅÅÅÅ-MM-DDThh: mm: SS. sssZ där:
  
  |||
  |-|-|
  |MMMM|fyrsiffrigt år|
  |MM|tvåsiffrig månad (01 = januari osv.)|
  |DD|fyrsiffrigt dag i månad (01 till 31)|
  |t|indikerare för start av tids element|
  |formatet|två siffror per timme (00 – 23)|
  |hög|två siffer minuter (00 till 59)|
  |ss|två siffror i sekunder (00 till 59)|
  |.sss|tre siffror på Decimal bråk i en sekund|
  |Z|UTC-beteckning (Coordinated Universal Time)||
  
  Mer information om ISO 8601-formatet finns i [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Kommentarer**

  GETCURRENTDATETIME är en icke-deterministisk funktion. 
  
  Resultatet som returneras är UTC (Coordinated Universal Time).

  **Exempel**  
  
  I följande exempel visas hur du hämtar den aktuella UTC-datum tiden med den inbyggda funktionen GetCurrentDateTime.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 Här är ett exempel på en resultat uppsättning.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a>GETCURRENTTIMESTAMP
 Returnerar antalet millisekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970. 
  
 **Syntax**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt värde, det aktuella antalet millisekunder som har förflutit sedan UNIX-epoken, dvs. antalet millisekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.

  **Kommentarer**

  GETCURRENTTIMESTAMP är en icke-deterministisk funktion.
  
  Resultatet som returneras är UTC (Coordinated Universal Time).

  **Exempel**  
  
  I följande exempel visas hur du hämtar den aktuella tidsstämpeln med hjälp av den inbyggda funktionen GetCurrentTimestamp.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 Här är ett exempel på en resultat uppsättning.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>Spatiala funktioner

Cosmos DB stöder följande inbyggda OGC-funktioner (Open Geospatial Consortium) för geospatial frågekörning. Följande skalärfunktioner utföra en åtgärd på en spatialobjektet indatavärdet och returnerar ett numeriskt eller booleskt värde.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Returnerar avståndet mellan de två GeoJSON Point-, Polygon- eller LineString-uttrycken.  
  
 **Syntax**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argument**  
  
- `spatial_expr`  
  
   Är ett giltigt uttryck för GeoJSON punkt, Polygon eller LineString-objektet.  
  
  **Returnera typer**  
  
  Returnerar ett numeriskt uttryck som innehåller avståndet. Detta uttrycks i mätare för referenssystem standard.  
  
  **Exempel**  
  
  I följande exempel visas hur du returnerar alla family dokument som ligger inom 30 km för den angivna platsen med hjälp av den inbyggda funktionen ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Returnerar ett booleskt uttryck som anger om GeoJSON (punkt, Polygon eller LineString) det angivna objektet i det första argumentet är inom GeoJSON (punkt, Polygon eller LineString) i det andra argumentet.  
  
 **Syntax**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argument**  
  
- `spatial_expr`  
  
   Är ett giltigt uttryck för GeoJSON punkt, Polygon eller LineString-objektet.  
 
- `spatial_expr`  
  
   Är ett giltigt uttryck för GeoJSON punkt, Polygon eller LineString-objektet.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt värde.  
  
  **Exempel**  
  
  I följande exempel visas hur du hittar alla family dokument i en polygon med ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Returnerar ett booleskt uttryck som anger om GeoJSON (punkt, Polygon eller LineString) det angivna objektet i det första argumentet korsar GeoJSON (punkt, Polygon eller LineString) i det andra argumentet.  
  
 **Syntax**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argument**  
  
- `spatial_expr`  
  
   Är ett giltigt uttryck för GeoJSON punkt, Polygon eller LineString-objektet.  
 
- `spatial_expr`  
  
   Är ett giltigt uttryck för GeoJSON punkt, Polygon eller LineString-objektet.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt värde.  
  
  **Exempel**  
  
  I följande exempel visas hur du hittar alla områden som överlappar angivna polygonen.  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Returnerar ett booleskt värde som anger huruvida det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt.  
  
 **Syntax**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argument**
  
- `spatial_expr`  
  
   Är ett giltigt GeoJSON punkt, Polygon eller LineString-uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck.  
  
  **Exempel**  
  
  I följande exempel visas hur du kontrollerar om en är giltig med hjälp av ST_VALID.  
  
  Nu har exempelvis ett latitud-värde som inte är i det giltiga värdeintervallet [-90, 90], så fråga returnerar FALSKT.  
  
  För polygoner kräver GeoJSON-specifikationen att senaste koordinaten paret som anges ska vara samma som först som skapar en sluten form. Punkter inom en polygon måste anges i motsols ordning. En polygon som angetts i medurs ordning representerar inversen till regionen i den.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Returnerar ett JSON-värde som innehåller ett booleskt värde om det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt, och orsaken som ett strängvärde om det är ogiltigt.  
  
 **Syntax**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **Argument**  
  
- `spatial_expr`  
  
   Är ett giltigt GeoJSON punkt eller polygon uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett JSON-värde som innehåller ett booleskt värde värdet om det angivna GeoJSON punkt eller polygon uttrycket är giltig och om det är ogiltig, dessutom orsak som ett strängvärde.  
  
  **Exempel**  
  
  I följande exempel hur du kontrollerar giltigheten (med information om) med hjälp av ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Här är resultatuppsättningen.  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [UDF: er](sql-query-udfs.md)
- [Agg regeringar](sql-query-aggregates.md)
