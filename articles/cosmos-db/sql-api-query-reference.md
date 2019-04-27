---
title: Syntaxen för SQL i Azure Cosmos DB
description: Den här artikeln förklarar SQL language frågesyntaxen används i Azure Cosmos DB, olika operatorer och nyckelord som är tillgängliga i det här språket.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 22b03417495625ef70650a015530d6f56b32fd4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626899"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>SQL-Språkreferens för Azure Cosmos DB 

Azure Cosmos DB stöder Frågedokument med hjälp av en välbekant SQL (Structured Query Language) som grammatik över hierarkisk JSON-dokument utan uttryckliga scheman eller att sekundära index. Den här artikeln innehåller dokumentation för SQL-fråga språksyntax används i SQL API-konton. En genomgång av exempel på SQL-frågor finns i [SQL-fråga exemplen i Cosmos DB](how-to-sql-query.md).  
  
Gå till den [Frågespelplan](https://www.documentdb.com/sql/demo), där du kan testa Cosmos DB och köra SQL-frågor mot en exempeldatauppsättning.  
  
## <a name="select-query"></a>SELECT-fråga  
Varje fråga består av en SELECT-sats och valfria FROM- och WHERE-satser enligt ANSI-SQL-standarderna. Vanligtvis för varje fråga källan i FROM-satsen räknas sedan filtret i WHERE-satsen har tillämpats på källan för att hämta en delmängd av JSON-dokument. Slutligen används SELECT-satsen för att beräkna de begärda JSON-värdena i select-listan. Exempel finns i [urvalsfråga exempel](how-to-sql-query.md#SelectClause)
  
**Syntax**  
  
```sql
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Kommentarer**  
  
 Se följande avsnitt för information på varje sats:  
  
-   [SELECT-satsen](#bk_select_query)    
-   [FROM-satsen](#bk_from_clause)    
-   [WHERE-satsen](#bk_where_clause)    
-   [ORDER BY-sats](#bk_orderby_clause)  
  
Satser i SELECT-instruktionen måste sorteras enligt ovan. En av de valfria satserna kan utelämnas. Men när valfria satser används, måste de visas i rätt ordning.  
  
### <a name="logical-processing-order-of-the-select-statement"></a>Logiska behandlingsordning av SELECT-satsen  
  
Ordningen som satser bearbetas är:  

1.  [FROM-satsen](#bk_from_clause)  
2.  [WHERE-satsen](#bk_where_clause)  
3.  [ORDER BY-sats](#bk_orderby_clause)  
4.  [SELECT-satsen](#bk_select_query)  

Observera att detta skiljer sig från den ordning som de visas i syntax. Sorteringen är så att alla nya symboler som introducerades av en bearbetade-sats är synliga och kan användas i satser som bearbetas senare. Exempelvis alias som deklarerats i en FROM-sats är tillgängliga i var och SELECT-satser.  

### <a name="whitespace-characters-and-comments"></a>Tecken som blanksteg och kommentarer  

Alla tecken som blanksteg som inte är en del av en sträng inom citattecken eller citattecken identifierare ingår inte i grammatik språk och ignoreras under parsning.  

T-SQL style kommentarer som har stöd för frågespråket  

-   SQL-uttryck `-- comment text [newline]`  

Medan tecken som blanksteg och kommentarer som du inte har någon betydelse i grammatik, måste de användas för att avgränsa token. Exempel: `-1e5` är ett enda tal token, tag`: – 1 e5` är en minus token följt av nummer 1 och identifierare e5.  

##  <a name="bk_select_query"></a> SELECT-satsen  
Satser i SELECT-instruktionen måste sorteras enligt ovan. En av de valfria satserna kan utelämnas. Men när valfria satser används, måste de visas i rätt ordning. Exempel finns i [urvalsfråga exempel](how-to-sql-query.md#SelectClause).

**Syntax**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argument**  
  
- `<select_specification>`  

  Egenskaper för eller -värde som ska väljas för resultatuppsättningen.  
  
- `'*'`  

  Anger att värdet ska hämtas utan några ändringar. Mer specifikt om bearbetade värdet är ett objekt, hämtas alla egenskaper.  
  
- `<object_property_list>`  
  
  Anger listan över egenskaper som ska hämtas. Varje returnerade värdet ska vara ett objekt med egenskaper som anges.  
  
- `VALUE`  

  Anger att JSON-värde ska hämtas i stället för det fullständiga JSON-objektet. Detta, till skillnad från `<property_list>` radbryts inte det beräknade värdet i ett objekt.  
  
- `<scalar_expression>`  

  Uttryck som representerar värdet som ska beräknas. Se [skaläruttryck](#bk_scalar_expressions) information.  
  
**Kommentarer**  
  
Den `SELECT *` syntax är bara giltigt om FROM-satsen har deklarerats exakt ett alias. `SELECT *` innehåller en identity-projektion, som kan vara användbart om inga projektion krävs. Välj * är bara giltigt om FROM-satsen har angetts och införs bara en enda Indatakällan.  
  
Båda `SELECT <select_list>` och `SELECT *` är ”syntaktiska socker” och du kan också kan uttryckas med hjälp av enkla SELECT-instruktioner som visas nedan.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   motsvarar att:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   motsvarar att:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Se även**  
  
[Skaläruttryck](#bk_scalar_expressions)  
[SELECT-satsen](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM-satsen  
Anger källan eller anslutna källor. FROM-satsen är valfri såvida inte källan filtreras eller projected senare i frågan. Syftet med den här satsen är att ange den datakälla som frågan måste fungera. Hela behållaren är ofta källan, men en kan ange en delmängd av behållaren i stället. Om den här satsen inte anges körs andra satser fortfarande som om FROM-satsen tillhandahålls ett enskilt dokument. Exempel finns i [från satsen exempel](how-to-sql-query.md#FromClause)
  
**Syntax**  
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
**Argument**  
  
- `<from_source>`  
  
  Anger en datakälla, med eller utan ett alias. Om alias inte har angetts, kommer den härledas från den `<container_expression>` med hjälp av följande regler:  
  
  -  Om uttrycket är en container_name, kommer container_name att användas som ett alias.  
  
  -  Om uttrycket är `<container_expression>`, %{Property_Name/ och %{Property_Name/ används som ett alias. Om uttrycket är en container_name, kommer container_name att användas som ett alias.  
  
- PRECIS SOM `input_alias`  
  
  Anger att den `input_alias` är en uppsättning värden som returneras av det underliggande container-uttrycket.  
 
- `input_alias` INDIEN  
  
  Anger att den `input_alias` bör representerar uppsättningen med värden som hämtas av iterera över alla matriselement av varje matrisen som returneras av det underliggande container-uttrycket. Ett värde som returneras av underliggande container-uttryck som inte är en matris ignoreras.  
  
- `<container_expression>`  
  
  Anger container-uttrycket som används för att hämta dokument.  
  
- `ROOT`  
  
  Anger det dokumentet ska hämtas från standardbehållaren, just nu anslutna.  
  
- `container_name`  
  
  Anger det dokumentet ska hämtas från den angivna behållaren. Namnet på behållaren måste matcha namnet på den behållare som är anslutna till.  
  
- `input_alias`  
  
  Anger det dokumentet ska hämtas från den källa som definieras av det angivna aliaset.  
  
- `<container_expression> '.' property_`  
  
  Anger det dokumentet ska hämtas genom att öppna den `property_name` egenskap eller array_index matriselement för alla dokument som hämtas av angivna behållaren uttryck.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Anger det dokumentet ska hämtas genom att öppna den `property_name` egenskap eller array_index matriselement för alla dokument som hämtas av angivna behållaren uttryck.  
  
**Kommentarer**  
  
Alla alias har angetts eller härledd i den `<from_source>(`s) måste vara unikt. Syntaxen `<container_expression>.`%{Property_Name/ är samma som `<container_expression>' ['"property_name"']'`. Denna syntax kan dock användas om ett egenskapsnamn innehåller ett icke-ID-tecken.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>hantera saknade egenskaper, matriselement och odefinierad värden som saknas
  
Om ett uttryck för behållaren har åtkomst till egenskaper eller matriselement och att värdet inte finns, ignoreras värdet och inte fortsätta bearbetningen.  
  
### <a name="container-expression-context-scoping"></a>Behållaren uttryck kontext omfång  
  
Ett uttryck för behållare kan vara behållare omfattar eller dokumentet omfattar:  
  
-   Ett uttryck är container-omfattande, om den underliggande källan för container-uttryck är antingen ROTEN eller `container_name`. Sådana ett uttryck representerar en uppsättning dokument som hämtas från behållaren direkt och är inte beroende av bearbetning av andra behållare-uttryck.  
  
-   Ett uttryck är dokumentet-omfattande, om den underliggande källan för container-uttryck är `input_alias` introducerade tidigare i frågan. Sådana ett uttryck representerar en uppsättning dokument som hämtas av utvärderingen av behållaren uttryck i omfånget för varje dokument som hör till den uppsättning som är associerade med ett alias-behållaren.  Den resulterande uppsättningen blir en union av uppsättningar som erhålls av utvärderingen av container-uttryck för varje dokument i den underliggande uppsättningen.  
  
### <a name="joins"></a>Kopplingar 
  
I den aktuella versionen stöder Cosmos DB inre kopplingar. Ytterligare join-funktioner är kommande. 

Inre kopplingar resultera i en fullständig kryssprodukten av de mängder som deltar i kopplingen. Resultatet av en N-vägs-koppling är en uppsättning element N tupplar, där varje värde i tuppeln är associerad med alias som deltar i kopplingen och kan nås genom att referera till som alias i andra-satser. Exempel finns i [nyckelordet exempel](how-to-sql-query.md#Joins)
  
Utvärderingen av kopplingen beror på kontexten omfånget för deltagande uppsättningar:  
  
- En koppling mellan behållare-uppsättning A och container-omfattande ange B, resulterar i en kryssprodukten av alla element i sets A och B.
  
- En koppling mellan uppsättning A och dokument-omfattande uppsättning B, resulterar i en union av alla uppsättningar som erhålls genom att utvärdera dokument-omfattande uppsättning B för varje dokument från ange A.  
  
  Högst ett container-omfattande uttryck stöds i den aktuella versionen av frågeprocessorn.  
  
### <a name="examples-of-joins"></a>Exempel på kopplingar  
  
Låt oss titta på följande FROM-satsen: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Låt varje källa som definierar `input_alias1, input_alias2, …, input_aliasN`. Den här FROM-satsen returnerar en mängd av N-tupplar (tuppel med N värden). Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar.  
  
**Exempel 1** -2 källor  
  
- Låt `<from_source1>` container-begränsas och som representerar uppsättningen {A, B, C}.  
  
- Låt `<from_source2>` vara dokument-omfattande refererar till input_alias1 och representerar uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} för `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2>` resulterar i följande tupplar:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exempel 2** -3 källor  
  
- Låt `<from_source1>` container-begränsas och som representerar uppsättningen {A, B, C}.  
  
- Låt `<from_source2>` vara begränsade av dokumentet refererar till `input_alias1` och representerar uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} för `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- Låt `<from_source3>` vara begränsade av dokumentet refererar till `input_alias2` och representerar uppsättningar:  
  
    {100, 200} för `input_alias2 = 1,`  
  
    {300} för `input_alias2 = 3,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2> JOIN <from_source3>` resulterar i följande tupplar:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Brist på tupplar för andra värden på `input_alias1`, `input_alias2`, som den `<from_source3>` returnerade inte några värden.  
  
**Exempel 3** -3 källor  
  
- Låt < from_source1 > vara container-omfattande och som representerar uppsättningen {A, B, C}.  
  
- Låt `<from_source1>` container-begränsas och som representerar uppsättningen {A, B, C}.  
  
- Låt < from_source2 > vara dokument-omfattande refererande input_alias1 och representerar uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} för `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- Låt `<from_source3>` begränsas till `input_alias1` och representerar uppsättningar:  
  
    {100, 200} för `input_alias2 = A,`  
  
    {300} för `input_alias2 = C,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2> JOIN <from_source3>` resulterar i följande tupplar:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), C, 4, 300, (C, 5, 300)  
  
  > [!NOTE]
  > Detta resulterade i kryssprodukten mellan `<from_source2>` och `<from_source3>` eftersom båda är begränsade till samma `<from_source1>`.  Detta resulterade i 4 (2 × 2) tupplar som har värdet A, 0 tupplar som har värdet B (1 x 0) och 2 (2 x 1) tupplar som har värdet C.  
  
**Se även**  
  
 [SELECT-satsen](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> WHERE-satsen  
 Anger sökvillkor för dokument som returneras av frågan. Exempel finns i [WHERE-satsen exempel](how-to-sql-query.md#WhereClause)
  
 **Syntax**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argument**  
  
- `<filter_condition>`  
  
   Anger villkoret vara uppfyllda för dokument som ska returneras.  
  
- `<scalar_expression>`  
  
   Uttryck som representerar värdet som ska beräknas. Se den [skaläruttryck](#bk_scalar_expressions) information.  
  
  **Kommentarer**  
  
  Villkoret måste utvärderas till SANT för dokumentet som ska returneras ett uttryck har angetts som filter. Endast booleska värdet true kommer uppfyller villkoren, ett annat värde: Odefinierad, null, false, tal, matris, eller ett objekt kommer inte uppfyller villkoret.  
  
##  <a name="bk_orderby_clause"></a> ORDER BY-sats  
 Anger sorteringsordning för resultaten som returnerades av frågan. Exempel finns i [ORDER BY-sats-exempel](how-to-sql-query.md#OrderByClause)
  
 **Syntax**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argument**  
  
- `<sort_specification>`  
  
   Anger en egenskap eller ett uttryck som du vill sortera frågeresultatet. En sorteringskolumn kan anges som ett alias för namn eller en kolumn.  
  
   Du kan ange flera sorteringskolumner. Kolumnnamnen måste vara unika. Vilken ordning kolumnerna sortera i ORDER BY-satsen definierar hur sorterade resultatuppsättningen. Det vill säga resultatet sorteras efter den första egenskapen och sedan den beställda listan sorteras efter den andra egenskapen och så vidare.  
  
   Namn på kolumnerna som refereras i ORDER BY-satsen måste motsvara till antingen en kolumn i select-listan eller en kolumn som definierats i en tabell som angetts i FROM-sats utan någon tvetydigheter.  
  
- `<sort_expression>`  
  
   Anger en enskild egenskap eller ett uttryck som du vill sortera frågeresultatet.  
  
- `<scalar_expression>`  
  
   Se den [skaläruttryck](#bk_scalar_expressions) information.  
  
- `ASC | DESC`  
  
   Anger att värdena i den angivna kolumnen ska sorteras i stigande eller fallande ordning. ASC sorterar från det lägsta värdet för högsta värden. DESC sorterar från högsta värdet till lägsta värdet. ASC är standardsorteringsordning. Null-värden behandlas som de lägsta möjliga värdena.  
  
  **Kommentarer**  
  
  Medan frågegrammatik har stöd för flera ordning av egenskaper, Cosmos DB-fråga runtime stöder sortering endast mot en enskild egenskap och endast mot egenskapsnamn (inte mot beräknade egenskaper). Sortera kräver också att indexprincip innehåller ett intervallsindex för egenskapen och den angivna typen, med den maximala precisionen. I indexering princip-dokumentationen för mer information.  
  
##  <a name="bk_scalar_expressions"></a> Skaläruttryck  
 Ett skalärt uttryck som är en kombination av symboler och operatörer som kan utvärderas för att få ett enskilt värde. Enkla uttryck kan vara konstanter, egenskapen referenser, matris referenser, alias referenser eller funktionsanrop. Enkla uttryck kan kombineras till komplexa uttryck med hjälp av operatörer. Exempel finns i [skaläruttryck exempel](how-to-sql-query.md#scalar-expressions)
  
 Mer information om värden som skalärt uttryck som kan ha finns [konstanter](#bk_constants) avsnittet.  
  
 **Syntax**  
  
```sql  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argument**  
  
- `<constant>`  
  
   Representerar ett konstant värde. Se [konstanter](#bk_constants) information.  
  
- `input_alias`  
  
   Representerar ett värde som definieras av den `input_alias` introducerades i den `FROM` satsen.  
  Det här värdet är säkert att den inte **odefinierat** –**odefinierat** värden i inkommande hoppas över.  
  
- `<scalar_expression>.property_name`  
  
   Representerar ett värde för egenskapen för ett objekt. Om egenskapen inte finns eller egenskapen refererar till ett värde som inte är ett objekt och sedan uttrycket utvärderas till **odefinierat** värde.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representerar ett värde för egenskapen med namnet `property_name` eller matriselementet med index `array_index` av en objektmatris. Om egenskapen/matrisindex finns inte eller egenskapen/matrisindex refereras på ett värde som är inte en objektmatris sedan uttrycket utvärderas till ett odefinierat värde.  
  
- `unary_operator <scalar_expression>`  
  
   Representerar en operatör som tillämpas på ett enda värde. Se [operatörer](#bk_operators) information.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representerar en operatör som tillämpas på två värden. Se [operatörer](#bk_operators) information.  
  
- `<scalar_function_expression>`  
  
   Representerar ett värde som definieras av ett resultat av ett funktionsanrop.  
  
- `udf_scalar_function`  
  
   Namnet på den användardefinierade skalära funktionen.  
  
- `builtin_scalar_function`  
  
   Namnet på inbyggd skalärfunktion.  
  
- `<create_object_expression>`  
  
   Representerar ett värde som fås genom att skapa ett nytt objekt med angivna egenskaperna och deras värden.  
  
- `<create_array_expression>`  
  
   Representerar ett värde som fås genom att skapa en ny matris med angivna värden som element  
  
- `parameter_name`  
  
   Representerar ett värde för det angivna parameternamnet. Parameternamn måste ha en enda \@ som det första tecknet.  
  
  **Kommentarer**  
  
  När du anropar en inbyggda eller användardefinierade skalärfunktion måste alla argument definieras. Om något av argumenten är odefinierad kommer inte att anropa funktionen och resultatet blir odefinierad.  
  
  När du skapar ett objekt ska hoppas över egenskaper som är tilldelad odefinierat värde och inte ingår i det skapade objektet.  
  
  När skapar en matris, ett elementvärde som tilldelas **odefinierat** värde ska hoppas över och inte ingår i det skapade objektet. Detta innebär att nästa definierade element ska utföras så att den Skapa matrisen inte kommer ha hoppas över index.  
  
##  <a name="bk_operators"></a> Operatörer  
 Det här avsnittet beskrivs operatorer som stöds. Varje operatör kan tilldelas till exakt en kategori.  
  
 Finns i **operatorn kategorier** tabellen nedan, för information om hantering av **odefinierat** värden, krav för indatavärden och hantering av värden med inte matchande typer.  
  
 **Operatorn kategorier:**  
  
|**Kategori**|**Detaljer**|  
|-|-|  
|**Aritmetiska**|Operatorn förväntar sig indata ska vara sin. Utdata är också ett tal. Om någon av indata är **odefinierat** eller annan typ än antalet sedan resultatet är **odefinierat**.|  
|**Bitvis**|Operatorn förväntar sig indata ska vara 32-bitars heltal sin. Utdata är också 32-bitars heltal tal.<br /><br /> Alla heltalsvärde ska avrundas. Positiv avrundas nedåt, negativa värden avrundas uppåt.<br /><br /> Ett värde som är utanför intervallet för 32-bitars heltal omvandlas med sista 32-bitar i dess två visas.<br /><br /> Om någon av indata är **odefinierat** eller annan typ än tal, och sedan resultatet är **odefinierat**.<br /><br /> **Obs!** Beteendet ovan är kompatibel med JavaScript bitvis operator-beteende.|  
|**Logiska**|Operatorn förväntar sig indata ska vara Boolean(s). Utdata är också ett booleskt värde.<br />Om någon av indata är **odefinierat** eller annan typ än Boolean, så resultatet kommer att vara **odefinierat**.|  
|**Jämförelse**|Operatorn förväntar sig indata ska ha samma typ och inte är odefinierad. Resultatet är ett booleskt värde.<br /><br /> Om någon av indata är **odefinierat** eller indata har olika typer och resultatet är **odefinierat**.<br /><br /> Se **gruppering av värden för jämförelse** tabellen för värdet ordning information.|  
|**sträng**|Operatorn förväntar sig indata ska vara strängarna. Utdata är också en sträng.<br />Om någon av indata är **odefinierat** eller annan typ än sträng och sedan resultatet är **odefinierat**.|  
  
 **Unära operatorer:**  
  
|**Namn**|**Operator**|**Detaljer**|  
|-|-|-|  
|**Aritmetiska**|+<br /><br /> -|Returnerar det numeriska värdet.<br /><br /> Binär negation. Returnerar negated numeriskt värde.|  
|**Bitvis**|~|Sådana komplementet. Returnerar en uppsättning ett numeriskt värde.|  
|**Logiska**|**INTE**|Negation. Returnerar negated booleskt värde.|  
  
 **De binära operatorerna som:**  
  
|**Namn**|**Operator**|**Detaljer**|  
|-|-|-|  
|**Aritmetiska**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Tillägg.<br /><br /> Subtraktion.<br /><br /> Multiplikation.<br /><br /> Division.<br /><br /> Modulering.|  
|**Bitvis**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Logiskt eller.<br /><br /> Binärt AND.<br /><br /> Bitvis XOR.<br /><br /> Vänsterskift.<br /><br /> Högerskift.<br /><br /> Noll fyllning högerskift.|  
|**Logiska**|**OCH**<br /><br /> **OR**|Logisk konjunktion. Returnerar **SANT** om båda argumenten är **SANT**, returnerar **FALSKT** annars.<br /><br /> Logisk disjunktion. Returnerar **SANT** om alla argument är **SANT**, returnerar **FALSKT** annars.|  
|**Jämförelse**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Är lika med. Returnerar **SANT** om argument är lika med, returnerar **FALSKT** annars.<br /><br /> Inte lika med. Returnerar **SANT** om argumenten inte är lika med, returnerar **FALSKT** annars.<br /><br /> Större än. Returnerar **SANT** om det första argumentet är större än det andra returnerar **FALSKT** annars.<br /><br /> Större än eller lika med. Returnerar **SANT** om det första argumentet är större än eller lika med den andra mallen, returnerar **FALSKT** annars.<br /><br /> Mindre än. Returnerar **SANT** om det första argumentet är mindre än en sekund, avkastningen **FALSKT** annars.<br /><br /> Mindre än eller lika med. Returnerar **SANT** om det första argumentet är mindre än eller lika med den andra mallen, returnerar **FALSKT** annars.<br /><br /> Slå samman. Returnerar det andra argumentet om det första argumentet är en **odefinierat** värde.|  
|**Sträng**|**&#124;&#124;**|Sammanfogning. Returnerar en sammansättning av båda argumenten.|  
  
 **Diagram-Ternär operatorer:**  

|**Namn**|**Operator**|**Detaljer**| 
|-|-|-|  
|Ternär operator|?|Returnerar det andra argumentet om det första argumentet utvärderas till **SANT**; annars returneras det tredje argumentet.|  

  
 **Sorteringen av värden för jämförelse**  
  
|**Typ**|**Värden ordning**|  
|-|-|  
|**Odefinierad**|Inte jämförbar.|  
|**Null**|Enstaka värde: **null**|  
|**Nummer**|Naturliga tal.<br /><br /> Negativt oändligt värde är mindre än andra numeriskt värde.<br /><br /> Positivt oändligt värde är större än andra numeriskt värde. **NaN** värdet är inte jämförbar. Jämföra med **NaN** leder **odefinierat** värde.|  
|**Sträng**|Lexicographical ordning.|  
|**Matris**|Ingen ordning, men rättvis.|  
|**Objekt**|Ingen ordning, men rättvis.|  
  
 **Kommentarer**  
  
 I Cosmos DB kallas ofta inte typerna av värden förrän de har hämtats från databasen. De flesta av operatorer har strikta krav för att stödja effektiv körning av frågor. Operatörer av själva utför även inte implicita konverteringar.  
  
 Detta innebär att en fråga av typen: Välj * från ROOT r var r.Age = 21 returnerar endast dokument med egenskapen ålder lika med antalet 21. Dokument med egenskapen ålder som är lika med strängen ”21” eller ”0021” strängen matchar inte, som uttrycket ”21” = 21 utvärderar till odefinierad. Det möjliggör en optimal användning av index, eftersom sökning efter ett specifikt värde (till exempel hur många 21) är snabbare än Sök efter ett obestämt antal potential matchar (talet 21 eller strängar ”21”, ”021”, ”21.0”...). Detta skiljer sig från hur JavaScript utvärderar operatörer på värden av olika typer.  
  
 **Matriser och -objekt likhet och jämförelse**  
  
 Jämföra hodnot matris eller ett objekt med hjälp av range-operatorer (>, > =, <, < =) resulterar i en odefinierad eftersom det inte finns inte ordningen som definierats för objektet eller matrisen värden. Men använda likhet/olikhet-operatorer (=,! =, <>) stöds och värden som ska jämföras strukturellt.  
  
 Matriser är lika om dessa matriser har samma antal element och elementen på matchar positioner också är lika. Om jämföra ett par av element resulterar i en odefinierad, är resultatet av matrisen jämförelse odefinierad.  
  
 Objekt är lika med om båda objekten har samma egenskaper som definierats, och om värdena för matchande egenskaper också är lika med. Om jämföra ett par av egenskapen värden resulterar i en odefinierad, är resultatet av objektet jämförelse odefinierad.  
  
##  <a name="bk_constants"></a> Konstanter  
 En konstant, även kallat en literal eller ett skalärt värde är en symbol som representerar ett värde för specifika data. Formatet för en konstant beror på datatypen för det värde som representerar.  
  
 **Skalära datatyper som stöds:**  
  
|**Typ**|**Värden ordning**|  
|-|-|  
|**Odefinierad**|Enstaka värde: **Odefinierad**|  
|**Null**|Enstaka värde: **null**|  
|**Boolesk**|Värden: **FALSKT**, **SANT**.|  
|**Nummer**|Ett dubbel precision Flyttalsnummer, IEEE-754 som standard.|  
|**Sträng**|En sekvens med noll eller flera Unicode-tecken. Strängar måste stå inom enkla eller dubbla citattecken.|  
|**Matris**|En sekvens med noll eller flera element. Varje element kan vara ett värde av alla skalära datatypen, utom odefinierad.|  
|**Objekt**|En osorterad uppsättning noll eller flera namn/värde-par. Namnet är en Unicode-sträng, värdet kan vara av olika skalära datatyper, utom **Undefined**.|  
  
 **Syntax**  
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argument**  
  
* `<undefined_constant>; undefined`  
  
  Representerar en odefinierad värde av typen odefinierad.  
  
* `<null_constant>; null`  
  
  Representerar **null** värde av typen **Null**.  
  
* `<boolean_constant>`  
  
  Representerar konstant av typen Boolean.  
  
* `false`  
  
  Representerar **FALSKT** värde av typen Boolean.  
  
* `true`  
  
  Representerar **SANT** värde av typen Boolean.  
  
* `<number_constant>`  
  
  Representerar en konstant.  
  
* `decimal_literal`  
  
  Decimal litteraler är värden som representeras med hjälp av decimalform eller matematisk notation.  
  
* `hexadecimal_literal`  
  
  Hexadecimala strängar är värden som representeras med prefixet ”0 x” följt av en eller flera hexadecimala siffror.  
  
* `<string_constant>`  
  
  Representerar en konstant av typen String.  
  
* `string _literal`  
  
  Stränglitteraler är Unicode-strängar som representeras av en sekvens med noll eller flera Unicode-tecken eller escape-sekvenser. Stränglitteraler är inom enkla citattecken (apostrof ”:) eller dubbla citattecken (citattecken”:).  
  
  Följande escape-sekvenser tillåts:  
  
|**Escape-sekvensen**|**Beskrivning**|**Unicode-tecken**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|citattecken (”)|U+0022|  
|\\\ |omvänd solidus (\\)|U + 005C|  
|\\/|solidus (/)|U + 002F|  
|\b|BACKSTEG|U + 0008|  
|\f|formuläret feed|U + 000C|  
|\n|radmatning|U + 000A|  
|\r|vagnretur|U + 000D|  
|\t|tabb|U + 0009|  
|\uXXXX|En Unicode-tecken som definieras av 4 hexadecimala siffror.|U + XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Prestandavägledning för fråga  
 Det bör använda filter som kan hanteras via ett eller flera index för en fråga som ska köras effektivt för en stor behållare.  
  
 Följande filter beaktas för index sökning:  
  
- Använd Likhetsoperatorn (=) med ett dokument sökvägsuttryck och en konstant.  
  
- Använda range-operatorer (<, \<=, >, > =) med ett dokument sökvägsuttryck och antalet konstanter.  
  
- Dokumentet sökvägsuttryck står för ett uttryck som identifierar en konstant sökväg i dokument från behållaren refererad databas.  
  
  **Dokumentet sökvägsuttryck**  
  
  Dokumentet sökvägsuttryck är uttryck som en sökväg till egenskapen eller matris indexeraren bedömare över ett dokument som kommer från databasen behållardokument. Den här sökvägen kan användas för att identifiera platsen för värden som refereras i ett filter direkt i dokumenten i behållaren för databasen.  
  
  För ett uttryck för att anses vara ett dokument sökvägsuttryck, bör det:  
  
1.  Referera till roten behållare direkt.  
  
2.  Indexerare för referens-egenskapen eller konstant matris för vissa dokument sökvägsuttryck  
  
3.  Referera till ett alias som representerar vissa sökvägsuttryck för dokumentet.  
  
     **Konventioner för syntax**  
  
     I följande tabell beskrivs konventioner som används för att beskriva syntax i följande SQL-referens.  
  
    |**Konvention**|**Används för**|  
    |-|-|    
    |VERSALER|Skiftlägeskänsliga nyckelord.|  
    |Gemener|Skiftlägeskänsliga nyckelord.|  
    |\<nonterminal >|Öppen har definierats separat.|  
    |\<nonterminal >:: =|Syntax definitionen av nonterminal.|  
    |other_terminal|Terminal (token), som beskrivs i detalj i orden.|  
    |identifierare|Identifierare. Gör följande endast tecken: a-z A-Z 0-9 _First tecknet får inte vara en siffra.|  
    |”string”|Sträng inom citattecken. Gör att en giltig sträng. Se beskrivning av en string_literal.|  
    |”symbol”|Literal symbol som är en del av syntaxen.|  
    |&#124;(lodrätt streck)|Alternativ för syntax objekt. Du kan använda endast en av de objekt som angetts.|  
    |[] /(brackets)|Hakparenteser omsluter en eller flera valfria objekt.|  
    |[ ,...n ]|Anger föregående objekt kan vara upprepade n flera gånger. Förekomster avgränsas med kommatecken.|  
    |[ ...n ]|Anger föregående objekt kan vara upprepade n flera gånger. Förekomster avgränsas med tomma värden.|  
  
##  <a name="bk_built_in_functions"></a> Inbyggda funktioner  
 Cosmos DB innehåller många inbyggda SQL-funktioner. Kategorier av inbyggda funktionerna finns nedan.  
  
|Funktion|Beskrivning|  
|--------------|-----------------|  
|[Matematiska funktioner](#bk_mathematical_functions)|Matematiska funktioner utför en beräkning, vanligtvis baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde.|  
|[Funktioner för typkontroll](#bk_type_checking_functions)|Med funktionerna för typkontroll kan du kontrollera typen av ett uttryck i SQL-frågor.|  
|[Strängfunktioner](#bk_string_functions)|Strängfunktioner utföra en åtgärd på ett strängvärde för indata och returnerar en sträng, numeriskt eller booleskt värde.|  
|[Matrisfunktioner](#bk_array_functions)|Matrisfunktioner kan du utföra en åtgärd på en matris indatavärdet och returnera numeriska, booleskt värde eller Matrisvärde.|  
|[Spatial funktioner](#bk_spatial_functions)|Funktionerna spatial utföra en åtgärd på en spatialobjektet indatavärdet och returnerar ett numeriskt eller booleskt värde.|  
  
###  <a name="bk_mathematical_functions"></a> Matematiska funktioner  
 Följande funktioner utför en beräkning, vanligtvis baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[TAK](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[GRADER](#bk_degrees)|  
|[EXP](#bk_exp)|[VÅNING](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANER](#bk_radians)|[AVRUNDA](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[RUTA](#bk_square)|[INLOGGNING](#bk_sign)|  
|[TAN](#bk_tan)|[AVKORTA](#bk_trunc)||  
  
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
  
  Exponenten för den naturliga logaritmen för ett tal är antalet själva: EXP (loggning (n)) = n. Och den naturliga logaritmen för e upphöjt till ett tal är antalet själva: LOG (EXP (n)) = n.  
  
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
  
  Den naturliga logaritmen för e upphöjt till ett tal är antalet själva: LOGG (EXP (n)) = n. Och exponentiell för den naturliga logaritmen för ett tal är antalet själva: EXP (loggning (n)) = n.  
  
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
  
 **Argument**  
  
- `numeric_expression`  
  
   Är ett numeriskt uttryck.  
  
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
  
  **Exempel**  
  
  I följande exempel Avrundar följande positiva och negativa tal till närmaste heltal.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
 Här är resultatuppsättningen.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
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
  
###  <a name="bk_type_checking_functions"></a> Funktioner för typkontroll  
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
  
###  <a name="bk_string_functions"></a> Strängfunktioner  
 Följande skalärfunktioner utför en åtgärd på ett strängindatavärde och returnerar en sträng, ett numeriskt värde eller ett booleskt värde.  
  
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
  
   Är ett numeriskt uttryck. Om num_expr är negativt eller icke-bestämt antal, är resultatet odefinierad.

  > [!NOTE]
  > Den maximala längden på resultatet är 10 000 tecken d.v.s. (length(str_expr) * num_expr) < = 10000.
  
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

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Returnerar uttryck översättas till en matris. Om uttrycket inte kan översättas, returnerar odefinierad.  
  
 **Syntax**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett skalärt uttryck som ska utvärderas som ett JSON-matris-uttryck. Observera att kapslade strängvärden måste skrivas med dubbla citattecken ska vara giltigt. Mer information om JSON-format finns [json.org](https://json.org/)
  
  **Returnera typer**  
  
  Returnerar ett matrisuttryck eller odefinierad.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToArray beter sig över olika typer. 
  
 Följande är exempel med giltiga indata.

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

 Följande är ett exempel på ogiltiga indata. 
   
 Enkla citattecken i matrisen är inte giltig JSON.
Även om de är giltiga i en fråga, kommer de inte att parsa till giltiga matriser. Strängar i matrisen strängen måste antingen undantas ”[\\”\\”]” eller omgivande citattecken måste vara enkel ”[” ”]”.

```
SELECT
    StringToArray("['5','6','7']")
```

 Här är resultatuppsättningen.

```
[{}]
```

 Här följer några exempel på ogiltiga indata.
   
 Det uttryck som skickas kommer att tolkas som en JSON-matris. följande utvärderar inte för att ange matris och därför returnera odefinierad.
   
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

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Returnerar uttryck översättas till ett booleskt värde. Om uttrycket inte kan översättas, returnerar odefinierad.  
  
 **Syntax**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett skalärt uttryck som ska utvärderas som ett booleskt uttryck.  
  
  **Returnera typer**  
  
  Returnerar ett booleskt uttryck eller odefinierad.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToBoolean beter sig över olika typer. 
 
 Följande är exempel med giltiga indata.

 Blanksteg tillåts endast innan eller efter ”true” / ”false”.

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

 Följande är exempel med ogiltiga indata.
 
 Booleska värden är skiftlägeskänsliga och måste skrivas med små bokstäver för d.v.s. ”true” och ”false”.

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

 Här är resultatuppsättningen.  
  
```  
[{}]
``` 

 Det uttryck som skickas ska parsas som ett booleskt uttryck; dessa indata utvärderas inte för att ange booleskt värde och därför returnera odefinierad.

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

####  <a name="bk_stringtonull"></a> StringToNull  
 Returnerar uttryck översätts till null. Om uttrycket inte kan översättas, returnerar odefinierad.  
  
 **Syntax**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett skalärt uttryck som ska utvärderas som ett null-uttryck.
  
  **Returnera typer**  
  
  Returnerar ett uttryck som är null eller odefinierad.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToNull beter sig över olika typer. 

 Följande är exempel med giltiga indata.
 
 Blanksteg tillåts endast före eller efter ”null”.

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

 Följande är exempel med ogiltiga indata.

 Null är skiftlägeskänsligt och måste skrivas med alla gemener d.v.s. ”null”.

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Här är resultatuppsättningen.  
  
```  
[{}]
```  

 Det uttryck som skickas kommer att tolkas som ett null-uttryck; dessa indata utvärderas inte om du vill ange null och därför returnera odefinierad.

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

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Returnerar uttryck översättas till ett tal. Om uttrycket inte kan översättas, returnerar odefinierad.  
  
 **Syntax**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett skalärt uttryck som ska utvärderas som ett JSON-Number-uttryck. Siffror i JSON måste vara ett heltal eller ett flyttal. Mer information om JSON-format finns [json.org](https://json.org/)  
  
  **Returnera typer**  
  
  Returnerar ett antal uttryck eller odefinierad.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToNumber beter sig över olika typer. 

 Blanksteg tillåts endast före eller efter hur många.
 
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

 I JSON som ett giltigt nummer måste vara antingen vara ett heltal eller ett flyttal peka tal.
 
```  
SELECT   
    StringToNumber("0xF")
```  
  
 Här är resultatuppsättningen.  
  
```  
{{}}
```  

 Det uttryck som skickas kommer att tolkas som ett antal uttryck; dessa indata utvärderas inte för att ange antal och därför returnera odefinierad. 

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

####  <a name="bk_stringtoobject"></a> StringToObject  
 Returnerar uttryck översättas till ett objekt. Om uttrycket inte kan översättas, returnerar odefinierad.  
  
 **Syntax**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argument**  
  
- `expr`  
  
   Är ett skalärt uttryck som ska utvärderas som ett uttryck för JSON-objekt. Observera att kapslade strängvärden måste skrivas med dubbla citattecken ska vara giltigt. Mer information om JSON-format finns [json.org](https://json.org/)  
  
  **Returnera typer**  
  
  Returnerar ett objektuttryck eller odefinierad.  
  
  **Exempel**  
  
  I följande exempel visas hur StringToObject beter sig över olika typer. 
  
 Följande är exempel med giltiga indata.
 
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
 
 Följande är exempel med ogiltiga indata.
Även om de är giltiga i en fråga, kommer de inte att parsa giltiga objekt. Strängar i strängen i objektet måste antingen undantas ”{\\” en\\”:\\” str\\”}” eller omgivande citattecken måste vara enkel ' {”a”: ”str”} ”.

 Enkla citattecken som omger egenskapsnamn är inte giltig JSON.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

 Här är resultatuppsättningen.

```  
[{}]
```  

 Egenskapsnamn utan omgivande citattecken är inte giltig JSON.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

 Här är resultatuppsättningen.

```  
[{}]
``` 

 Följande är exempel med ogiltiga indata.
 
 Det uttryck som skickas kommer att tolkas som ett JSON-objekt. dessa indata utvärderas inte för att ange objekt och därför returnera odefinierad.
 
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
  
   Är ett numeriskt uttryck för att ange start- och tecken.    
  
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
  
###  <a name="bk_array_functions"></a> Matrisfunktioner  
 Följande skalärfunktioner utföra en åtgärd på en matris indatavärdet och returnera numeriska, matrisen eller booleskt värde  
  
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
Returnerar ett booleskt värde som anger huruvida matrisen innehåller det angivna värdet. Du kan söka efter en partiell eller fullständig matchning av ett objekt med hjälp av ett booleskt uttryck i kommandot. 

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
  
   Är ett booleskt uttryck. Om den är inställd på ' true'and om det angivna värdet är ett objekt, kommandot söker efter en delvis matchning (objektet search är en delmängd av ett av objekten). Om den är inställd på 'false', söker kommandot efter en fullständig matchning av alla objekt i matrisen. Standardvärdet om inget anges är false. 
  
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
 
###  <a name="bk_spatial_functions"></a> Spatial funktioner  
 Följande skalärfunktioner utföra en åtgärd på en spatialobjektet indatavärdet och returnerar ett numeriskt eller booleskt värde.  
  
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

- [SQL-syntax och SQL-fråga för Cosmos DB](how-to-sql-query.md)

- [Dokumentation om cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
