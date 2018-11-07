---
title: SQL-frågor för Azure Cosmos DB | Microsoft Docs
description: Läs mer om SQL-syntax, databasbegrepp och SQL-frågor för Azure Cosmos DB. SQL kan användas som en JSON-frågespråket i Azure Cosmos DB.
keywords: SQL-syntax, sql-fråga, sql-frågor, frågespråk för json, databasbegrepp och sql-frågor, mängdfunktioner
services: cosmos-db
author: LalithaMV
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: laviswa
ms.openlocfilehash: 22b31e7df4e11f8f98877a8497b533203dcc26b3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233311"
---
# <a name="query-azure-cosmos-db-data-with-sql-queries"></a>Fråga Azure Cosmos DB-data med SQL-frågor

Microsoft Azure Cosmos DB stöder förfrågningar till dokument med hjälp av SQL (Structured Query Language) som en JSON-frågespråket på SQL-API-konton. När du utformar frågespråket för Azure Cosmos DB, anses följande två mål:

* Vi har gjort Azure Cosmos DB för SQL, ett av de välbekanta och mest populära frågespråk i stället för inventing ett nytt frågespråk. Azure Cosmos DB SQL är en formell programmeringsmodell för komplexa frågor via JSON-dokument.  

* Azure Cosmos DB använder JavaScript-programmeringsmodell som grund för frågespråket. SQL API grundas på typsystemet i JavaScript-typsystemet, uttrycksutvärdering och funktionsanrop. Den här i sin tur är en naturlig programmeringsmodell för relationella projektioner, hierarkisk navigering i JSON-dokument, självsignerat kopplingar, rumsliga förfrågningar och anrop av användardefinierade funktioner (UDF) helt skrivna i JavaScript, bland annat. 

Den här artikeln vägleder dig igenom några exempel SQL-frågor med hjälp av enkla JSON-dokument. Mer information om syntaxen för Azure Cosmos DB SQL, se [referens för SQL-syntax](sql-api-sql-query-reference.md) artikeln. 

## <a id="GettingStarted"></a>Kom igång med SQL-kommandon
Nu ska vi skapa två enkla JSON-dokument och fråga mot dessa data. Överväg två JSON-dokument om familjer, infoga dessa JSON-dokument i en samling och sedan fråga data. Här har vi ett enkelt JSON dokumentera för familjen Andersen och Wakefield familjer, överordnade, barn (och deras husdjur), adress och registreringsinformation. Dokumentet har strängar, tal, booleska värden, matriser och kapslade egenskaper. 

**Dokument1**  

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow", 
         "gender": "female", 
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Här är ytterligare ett dokument med en skillnad – `givenName` och `familyName` används i stället för `firstName` och `lastName`.

**Dokument2**  

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

Nu ska vi prova några frågor mot dessa data för att förstå några av de viktigaste aspekterna i Azure Cosmos DB SQL-frågespråket. 

**Query1**: till exempel returnerar följande fråga dokument där id-fältet matchar `AndersenFamily`. Eftersom det är en `SELECT *`, resultatet av frågan är komplett JSON-dokument finns mer information om syntaxen finns i [SELECT-instruktion](sql-api-sql-query-reference.md#select-query):

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

**Fråga2** : Tänk nu när vi behöver formatera om JSON-utdata i en annan form. Den här frågan genererar ett nytt JSON-objekt med två valda fält, namn och Stad, när den adressen stad har samma namn som tillståndet. I det här fallet matchar ”USA, USA”.   

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state
```

**Results**

```json
    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]
```

**Query3**: den här frågan returnerar alla angivna namn på barnen i familjen vars id matchar `WakefieldFamily` sorterade efter stad där.

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

**Results**

```json
    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]
```

Nedan följer några aspekter av frågespråket igenom de exempel som du har sett i Cosmos DB:  

* Eftersom SQL-API fungerar på JSON-värden, behandlar den trädet formade entiteter i stället för rader och kolumner. Därför språk kan du referera till noder i trädet på varje godtyckliga djup som `Node1.Node2.Node3…..Nodem`, vilket liknar relationell SQL som refererar till de två referensen för `<table>.<column>`.   

* Strukturerade frågespråket fungerar med schemalösa-data. Typsystemet måste bindas dynamiskt. I samma uttryck kan ge olika typer på olika dokument. Resultatet av en fråga är ett giltigt JSON-värde, men är inte säkert att ha ett fast schema.  

* Azure Cosmos DB stöder endast strikt JSON-dokument. Det innebär att den och uttryck är begränsade till bara handlar om JSON-typer. Referera till den [JSON-specifikationen](http://www.json.org/) för mer information.  

* En Cosmos DB-samling är en schemafri behållare för JSON-dokument. Relationer mellan dataentiteter inom och mellan dokument i en samling som implicit avbildas av inneslutning, inte av primärnyckel och sekundärnyckel viktiga relationer. Det här är en viktig aspekt vara värd att påpeka hänsyn till intra-dokumentet-kopplingar som beskrivs senare i den här artikeln.

## <a id="SelectClause"></a>SELECT-satsen

Varje fråga består av en SELECT-satsen och valfria FROM och WHERE-satserna per ANSI SQL-standarder. Vanligtvis för varje fråga räknas källan i FROM-satsen. Sedan tillämpas filtret i WHERE-satsen på källan för att hämta en delmängd av JSON-dokument. Slutligen används SELECT-satsen för att beräkna de begärda JSON-värden i select-listan. Läs om vilken syntax i [väljer syntax](sql-api-sql-query-reference.md#bk_select_query).

I följande exempel visas en typisk urvalsfråga. 

**Fråga**

```sql
    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]
```

### <a name="nested-properties"></a>Kapslade egenskaper
I exemplet nedan vi projicerar två kapslade egenskaper `f.address.state` och `f.address.city`.

**Fråga**

```sql
    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "state": "WA", 
      "city": "seattle"
    }]
```

Projektion stöder också JSON-uttryck som du ser i följande exempel:

**Fråga**

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]
```

Låt oss titta på rollen `$1` här. Den `SELECT` satsen som behövs för att skapa en JSON-objekt och eftersom ingen nyckel har angetts och vi använder implicit argumentet variabelnamn som börjar med `$1`. Den här frågan returnerar exempelvis två argumentvariabler som implicit, märkta `$1` och `$2`.

**Fråga**

```sql
    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM-satsen

Den från < from_specification >-satsen är valfri såvida inte källan filtreras eller projected senare i frågan. Läs om vilken syntax i [från syntax](sql-api-sql-query-reference.md#bk_from_clause). En fråga som `SELECT * FROM Families` anger att hela familjer samlingen är källan som att räkna upp. En särskild identifierare som rot kan användas för att representera samlingen istället för att använda namnet på samlingen. I följande lista innehåller de regler som tillämpas per fråga:

* Samlingen kan vara ett alias, till exempel `SELECT f.id FROM Families AS f` eller helt enkelt `SELECT f.id FROM Families f`. Här `f` motsvarar `Families`. `AS` är ett valfritt nyckelord för alias identifierare.  

* När ett alias, den ursprungliga källan kan inte bindas. Till exempel `SELECT Families.id FROM Families f` är syntaktiskt felaktig eftersom identifieraren ”familjer” inte kan matchas längre.  

* Alla egenskaper som måste referera till måste vara fullständigt kvalificerade. Om efterlevnad av strikt schema gäller detta för att undvika eventuella tvetydig bindningar. Därför `SELECT id FROM Families f` är syntaktiskt felaktig eftersom egenskapen `id` är inte bunden.

### <a name="get-subdocuments-using-from-clause"></a>Få underdokument med FROM-satsen

Källan kan också reduceras till en mindre deluppsättning. Till exempel att räkna upp bara ett underträd i varje dokument kan i subroot sedan bli källan, som visas i följande exempel:

**Fråga**

```sql
    SELECT * 
    FROM Families.children
```

**Results**  

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Medan exemplet ovan används en matris som källa, ett objekt också skulle kunna användas som källa, vilket är vad som anges i följande exempel: någon giltig JSON-värde (inte odefinierat) som finns i källan betraktas som ska ingå i resultatet av frågan. Om några familjer som inte har en `address.state` värde, de ingår inte i frågeresultatet.

**Fråga**

```sql
    SELECT * 
    FROM Families.address.state
```

**Results**

```json
    [
      "WA", 
      "NY"
    ]
```

## <a id="WhereClause"></a>WHERE-satsen
WHERE-satsen (**`WHERE <filter_condition>`**) är valfritt. Den anger de villkor som JSON-dokument som tillhandahålls av källan måste uppfylla för att vara med i resultatet. Valfritt JSON-dokument måste utvärderas de angivna villkoren ”true” om man ta hänsyn till resultatet. WHERE-satsen används av indexet lagret för att avgöra den absoluta minsta delmängden av källdokument som kan ingå i resultatet. Läs om vilken syntax i [var syntax](sql-api-sql-query-reference.md#bk_where_clause).

Följande fråga begär dokument som innehåller en namnegenskapen vars värde är `AndersenFamily`. Andra dokument som inte har en namnegenskap, eller där värdet inte motsvarar `AndersenFamily` utesluts. 

**Fråga**

```sql
    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]
```

Föregående exempel visade en enkel likhet-fråga. SQL-API: et stöder också en mängd olika skalära uttryck. Den mest använda är binär- och unära uttryck. Egenskapen referenser från käll-JSON-objekt är också giltigt uttryck. 

De följande binära operatorerna som stöds för närvarande och kan användas i frågor som visas i följande exempel:  

|**Typ av frågeoperator**  |**Värden**  |
|---------|---------|
|Aritmetiska    |   +,-,*,/,%   |
|Bitvis  |   |, &, ^, <<>>,, >>> (noll fyllning högerskift)      |
|Logiska   |   OCH, ELLER INTE      |
|Jämförelse   |    =, !=, &lt;, &gt;, &lt;=, &gt;=, <>     |
|Sträng  |  || (sammanfoga)       |

Låt oss ta en titt på några frågor med de binära operatorerna.

```sql
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5
```

Unära operatorer +,-, ~, och inte stöds också och kan användas i frågor som visas i följande exempel:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Förutom binära och unära operatorer men, tillåts egenskapen referenser. Till exempel `SELECT * FROM Families f WHERE f.isRegistered` returnerar JSON-dokument som innehåller egenskapen `isRegistered` där egenskapens värde är lika JSON `true` värde. Alla övriga värden (FALSKT, null, Odefinierad, `<number>`, `<string>`, `<object>`, `<array>`och så vidare) leder till källdokumentet som undantas från resultatet. 

### <a name="equality-and-comparison-operators"></a>Likhet och jämförelse operatorer
I följande tabell visar resultatet av likhet jämförelser i SQL-API: et mellan alla två JSON-typer.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Odefinierad</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Booleskt värde</strong>
         </td>
         <td valign="top">
            <strong>Antal</strong>
         </td>
         <td valign="top">
            <strong>sträng</strong>
         </td>
         <td valign="top">
            <strong>Objekt</strong>
         </td>
         <td valign="top">
            <strong>Matris</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Odefinierad<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Booleskt värde<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Antal<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>sträng<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objekt<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Matris<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
      </tr>
   </tbody>
</table>

För andra jämförelseoperatorer som >, > =,! =, <, och < =, följande regler gäller:   

* Jämförelse mellan typer resulterar i odefinierad.  
* Jämförelse mellan två objekt eller två matriser resulterar i odefinierad.   

Om resultatet av det skalära uttrycket i filtret är odefinierad motsvarande dokumentet inte tas med i resultatet, eftersom Undefined logiskt inte motsvarar en ”true”.

## <a name="between-keyword"></a>MELLAN nyckelord
Du kan också använda nyckelordet BETWEEN för att uttrycka frågor mot intervall med värden som i ANSI SQL. MELLAN kan användas mot strängar eller siffror.

Den här frågan returnerar till exempel alla family dokument där den första underordnade i företagsklass som är mellan 1-5 (båda inkluderande). 

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Till skillnad från i ANSI-SQL, kan du också använda BETWEEN-satsen i FROM-satsen som i följande exempel.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Den största skillnaden mellan att använda BETWEEN i SQL-API och ANSI SQL är att du kan uttrycka intervallfrågor mot egenskaper av olika typer – du kan till exempel ha ”klass” vara ett tal (5) i vissa dokument och strängar i andra (”grade4”). I dessa fall, som i JavaScript, en jämförelse mellan två olika typer resulterar i ”Odefinierad” och dokumentet kommer att hoppas över.

> [!NOTE]
> Glöm inte att skapa en indexprincip som använder en intervallet Indextyp mot alla numeriska egenskaper/sökvägar som filtreras i BETWEEN-satsen för snabbare fråga körningstider. 

### <a name="logical-and-or-and-not-operators"></a>Logiska (AND, OR och NOT) operatorer
Logiska operatorer fungerar på booleska värden. De logiska tabellerna sanningen för de här operatorerna visas i följande tabeller.

**ELLER har frågor**

| ELLER | True | False | Odefinierat |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Odefinierat |
| Odefinierat |True |Odefinierat |Odefinierat |

**OCH operatör**

| OCH | True | False | Odefinierat |
| --- | --- | --- | --- |
| True |True |False |Odefinierat |
| False |False |False |False |
| Odefinierat |Odefinierat |False |Odefinierat |

**NOT-operator**

| INTE |  |
| --- | --- |
| True |False |
| False |True |
| Odefinierat |Odefinierat |

## <a name="in-keyword"></a>I nyckelord

IN-nyckelordet kan användas för att kontrollera om ett angivet värde matchar något värde i en lista. Den här frågan returnerar till exempel alla family dokument där id är ”WakefieldFamily” eller ”AndersenFamily”. 

```sql
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Det här exemplet returnerar alla dokument där tillståndet är någon av de angivna värdena.

```sql
    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="ternary--and-coalesce--operators"></a>Ternär (?) och operatörer av Coalesce (?)

Ternär och Coalesce operatörer kan användas för att skapa villkorsuttryck, liknar vanliga programmeringsspråk som C# och JavaScript. Operatorn Ternär (?) kan vara väldigt användbar när nya JSON-egenskaper i farten. Exempel: nu kan du skriva frågor för att klassificera klass-nivåer till mänskliga läsbart format som nybörjare/mellanliggande/Avancerat enligt nedan.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c
```

Du kan också kapsla anrop till operator som i frågan nedan.

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c
```

Som med andra operatorer för fråga ingår om refererade egenskaperna i villkorsuttrycket saknas i alla dokument, eller om de typer som jämförs är olika, sedan dessa dokument inte i frågeresultatet.

Operatorn Coalesce (?) kan användas för att effektivt söka efter förekomsten av en egenskap (alias) definieras) i ett dokument. Detta är användbart när frågor körs mot halvstrukturerade eller data av olika typer. Den här frågan returnerar till exempel ”efternamn” om tillgänglig, eller ”efternamn” om det inte finns.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="EscapingReservedKeywords"></a>Citerade Egenskapsåtkomst
Du kan också komma åt egenskaper med hjälp av operatorn citerade egenskapen `[]`. Till exempel `SELECT c.grade` och `SELECT c["grade"]` är likvärdiga. Den här syntaxen är användbart när du behöver att undvika en egenskap som innehåller blanksteg, specialtecken eller händer att dela samma namn som en SQL-nyckelord eller reserverat ord.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="aliasing"></a>Alias

Nu ska vi utöka i exemplet ovan med explicita alias med värden. Så som de är nyckelord som används för alias. Det är valfritt som visas när du projicerar det andra värdet som `NameInfo`. 

Om en fråga har två egenskaper med samma namn, måste alias användas för att byta namn på en eller båda egenskaperna så att de är skiljas åt i det beräknade resultatet.

**Fråga**
```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="scalar-expressions"></a>Skaläruttryck
Förutom egenskapen referenser stöder SELECT-satsen också skaläruttryck konstanter, aritmetiska uttryck, logiska uttryck, t.ex. Här är till exempel en enkel ”Hello World”-fråga.

**Fråga**

```sql
    SELECT "Hello World"
```

**Results**

```json
    [{
      "$1": "Hello World"
    }]
```

Här är ett mer avancerat exempel som använder ett skalärt uttryck.

**Fråga**

```sql
    SELECT ((2 + 11 % 7)-2)/3    
```

**Results**

```json
    [{
      "$1": 1.33333
    }]
```

I följande exempel visas är resultatet av det skalära uttrycket ett booleskt värde.

**Fråga**

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    
```

**Results**

```json
    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>Skapa en objekt och matriser
En annan viktig funktion i SQL-API är matris/objektskapande. Observera att vi har skapat ett nytt JSON-objekt i exemplet ovan. På samma sätt kan kan en också skapa matriser som visas i följande exempel:

**Fråga**

```sql
    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    
```

**Results**  

```json
    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

## <a id="ValueKeyword"></a>VÄRDET nyckelord
Den **värdet** nyckelord är ett sätt att returnera värdet på JSON. Exempelvis kan den fråga som visas nedan returnerar skalären `"Hello World"` i stället för `{$1: "Hello World"}`.

**Fråga**

```sql
    SELECT VALUE "Hello World"
```

**Results**

```json
    [
      "Hello World"
    ]
```

Följande fråga returnerar JSON-värde utan den `"address"` etikett i resultaten.

**Fråga**

```sql
    SELECT VALUE f.address
    FROM Families f    
```

**Results**  

```json
    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]
```

I följande exempel utökar det för att visa hur du returnerar JSON primitiva värden (lövnivån av JSON-träd). 

**Fråga**

```sql
    SELECT VALUE f.address.state
    FROM Families f    
```

**Results**

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="-operator"></a>* Operator
Särskilda operatorn (*) stöds projicera dokumentet som – är. När den används, måste det vara det enda beräknade fältet. När en fråga av typen `SELECT * FROM Families f` är giltig, `SELECT VALUE * FROM Families f ` och `SELECT *, f.id FROM Families f ` är inte giltiga.

**Fråga**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"
```

**Results**

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="TopKeyword"></a>Operatorn TOP
Nyckelordet ÖVERSTA kan användas för att begränsa antalet värden från en fråga. När upp används tillsammans med ORDER BY-satsen, är resultatmängden begränsad till sorterad värden; N första Annars returneras första N antalet resultat i en odefinierad order. Som bästa praxis, i en SELECT-instruktion, Använd alltid en ORDER BY-sats med TOP-satsen. Det här är det enda sättet att förutsägbart indikerar vilka rader som påverkas av längst upp. 

**Fråga**

```sql
    SELECT TOP 1 * 
    FROM Families f 
```

**Results**

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

TOP kan användas med ett konstant värde (som visas ovan) eller med ett variabelvärde använda parameteriserade frågor. Mer information finns i parameterfrågor nedan.

## <a id="Aggregates"></a>Mängdfunktioner
Du kan också utföra aggregeringar i den `SELECT` satsen. Mängdfunktioner utföra beräkningar på en uppsättning värden och returnera ett enstaka värde. Följande fråga returnerar till exempel antalet family dokument i samlingen.

**Fråga**

```sql
    SELECT COUNT(1) 
    FROM Families f 
```

**Results**

```json
    [{
        "$1": 2
    }]
```

Du kan också returnera skalärvärde för samlingen med hjälp av den `VALUE` nyckelord. Följande fråga returnerar till exempel antalet värden som ett enda tal:

**Fråga**

```sql
    SELECT VALUE COUNT(1) 
    FROM Families f 
```

**Results**

```json
    [ 2 ]
```

Du kan också utföra aggregeringar i kombination med filter. Följande fråga returnerar till exempel antalet dokument med adressen i delstaten Washington.

**Fråga**

```sql
    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 
```

**Results**

```json
    [ 1 ]
```

I följande tabell listas stöds mängdfunktioner i SQL-API: et. `SUM` och `AVG` utförs över numeriska värden, medan `COUNT`, `MIN`, och `MAX` kan utföras via tal, strängar, booleska värden och null-värden. 

| Användning | Beskrivning |
|-------|-------------|
| ANTAL | Returnerar antalet objekt i uttrycket. |
| SUMMA   | Returnerar summan av alla värden i uttrycket. |
| MIN   | Returnerar det minsta värdet i uttrycket. |
| MAX   | Returnerar det största värdet i uttrycket. |
| MEDEL   | Returnerar medelvärdet av värdena i uttrycket. |

Aggregeringar kan också utföras via resultatet av en matris iteration. Mer information finns i [matris Iteration i frågor](#Iteration).

> [!NOTE]
> När du använder Datautforskaren i Azure portal, Observera att mängdfrågor kan returnera de delvis sammansatta resultaten från en sida i fråga. SDK: erna producerar en enda ackumulerade värdet på alla sidor. 
> 
> För att kunna utföra mängdfrågor med hjälp av kod du behöver .NET SDK 1.12.0, .NET Core SDK 1.1.0 eller Java SDK 1.9.5 eller senare.    
>

## <a id="OrderByClause"></a>ORDER BY-sats
Som du kan inkludera ett valfritt Order By-sats vid fråga till i ANSI-SQL. Instruktionen kan inkludera ett valfritt argument Stig/fall för att ange vilken ordning där resultaten måste hämtas.

Här är till exempel en fråga som hämtar familjer i den ordning de fasta stadens namn.

**Fråga**

```sql
    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
```

**Results**

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"    
      }
    ]
```

Och här är en fråga som hämtar familjer efter skapandedatum, som lagras som ett tal som representerar epok tid, dvs, förfluten tid sedan den 1 januari 1970 i sekunder.

**Fråga**

```sql
    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
```

**Results**

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472    
      }
    ]
```

## <a id="Advanced"></a>Avancerade databasbegrepp och SQL-frågor

### <a id="Iteration"></a>Iteration
En ny konstruktion har lagts till den **IN** nyckelord i SQL-API för att tillhandahålla stöd för att iterera över JSON-matriser. FROM-källan har stöd för iteration. Låt oss börja med följande exempel:

**Fråga**

```sql
    SELECT * 
    FROM Families.children
```

**Results**  

```json
    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]
```

Nu ska vi titta på en annan fråga som utför iteration underordnade objekt i samlingen. Observera skillnaden i utdata-matris. Det här exemplet delar upp `children` och plattar ut resultatet till en enskild matris.  

**Fråga**

```sql
    SELECT * 
    FROM c IN Families.children
```

**Results**  

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Detta kan ytterligare användas för att filtrera på varje enskild post i listan som visas i följande exempel:

**Fråga**

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

**Results**  

```json
    [{
      "givenName": "Lisa"
    }]
```

Du kan också utföra aggregering över resultatet av matrisen iteration. Till exempel räknar följande fråga antalet underordnade bland alla serier.

**Fråga**

```sql
    SELECT COUNT(child) 
    FROM child IN Families.children
```

**Results**  

```json
    [
      { 
        "$1": 3
      }
    ]
```

### <a id="Joins"></a>Kopplingar
I en relationsdatabas är behovet av att ansluta över tabeller viktigt. Det är den logiska naturlig följd att utforma normaliserade scheman. Sätt som strider mot detta innehåller SQL API Avnormaliserade datamodellen schemafria dokument. Det här är den logiska motsvarigheten till en ”självkoppling”.

Syntax som språket stöder är < from_source1 > JOIN < from_source2 > koppling... Anslut < from_sourceN >. Övergripande detta returnerar en uppsättning **N**- tupplar (tuppel med **N** värden). Varje tuppel har värden som produceras av iterera alla alias för samlingen över sina respektive uppsättningar. Det här är alltså en fullständig kryssprodukten av de mängder som deltar i kopplingen.

I följande exempel visas hur kopplingssatsen fungerar. I följande exempel, resultatet är tom eftersom kryssprodukten av varje dokument från källa och en tom uppsättning är tom.

**Fråga**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

**Results**  

```json
    [{
    }]
```

I följande exempel är kopplingen mellan dokumentroten och `children` subroot. Det är en kryssprodukten mellan två JSON-objekt. Det faktum att underordnade är en matris är inte effektiva i KOPPLINGEN eftersom vi arbetar med en enda rot som är underordnade matrisens. Resultatet innehåller därför bara två resultat, eftersom kryssprodukten av varje dokument med matrisen ger exakt bara ett dokument.

**Fråga**

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

**Results**

```json
    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]
```

I följande exempel visas en mer konventionella koppling:

**Fråga**

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children 
```

**Results**

```json
    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]
```

Det första att notera är att den `from_source` av den **ansluta** -satsen är en Iteratorn. Därför är flödet i det här fallet följande:  

* Expandera varje underordnat element **c** i matrisen.
* Tillämpa en kryssprodukten med rot dokumentets **f** med varje underordnat element **c** som var tillplattad i det första steget.
* Slutligen projektet rotobjektet **f** namnge egenskapen enbart. 

Det första dokumentet (`AndersenFamily`) innehåller endast ett underordnat element, så resultatet innehåller bara ett enda objekt som motsvarar det här dokumentet. Det andra dokumentet (`WakefieldFamily`) innehåller två underordnade. Därför producerar kryssprodukten ett separat objekt för varje underordnad, vilket lett till två objekt, en för varje underordnad som motsvarar det här dokumentet. Fälten rot i båda dessa dokument är likadana, precis som du förväntar dig i en kryssprodukten.

Verktyget verkliga i kopplingen är att formuläret tupplar från kryssprodukten i en form som annars är svåra att projektet. Du kan dessutom som vi ser i exemplet nedan, filtrera på en kombination av en tuppel att kan användaren har valt ett villkor som uppfyller tuppeln övergripande.

**Fråga**

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
```

**Results**

```json
    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]
```

Det här exemplet är en naturlig förlängning av föregående exempel och skapar en dubbla koppling. Därför kan kryssprodukten visas som följande pseudo kod:

```
    for-each(Family f in Families)
    {    
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` har en underordnad som har en husdjur. Därför kryssprodukten ger en rad (1\*1\*1) från den här serien. WakefieldFamily men har två barn, men endast en underordnad ”Jesse” har husdjur. Jesse har två husdjur dock. Därför kryssprodukten ger 1\*1\*2 = 2 rader från den här serien.

I nästa exempel finns ett ytterligare filter på `pet`. Detta omfattar inte alla tupplar där husdjur namnet inte är ”skuggkopia”. Observera att vi kan skapa tupplar från matriser, filter på någon av elementen i tuppeln och projicera olika kombinationer av elementen. 

**Fråga**

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

**Results**

```json
    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]
```

## <a id="JavaScriptIntegration"></a>JavaScript-integrering
Azure Cosmos DB är en programmeringsmodell för att köra JavaScript-baserade programlogik direkt på samlingarna som lagrade procedurer och utlösare. Detta möjliggör både:

* Möjlighet att göra högpresterande transaktionella CRUD-åtgärder och frågor mot dokument i en samling tack vare den djupgående integrationen av JavaScript-körning direkt i databasmotorn. 
* En naturlig modellering av Kontrollflöde, variabel omfång och tilldelning och integrering av undantagshantering primitiver med databastransaktioner. Mer information om Azure Cosmos DB-stöd för JavaScript-integrering finns i dokumentationen för JavaScript-programmering på serversidan.

### <a id="UserDefinedFunctions"></a>Användardefinierade funktioner (UDF)
SQL-API har stöd för användardefinierade funktioner (UDF, User) tillsammans med de typer som redan har definierats i den här artikeln. I synnerhet stöds skalära UDF: er där utvecklare kan skicka in noll eller flera argument och returnerar ett enda argument resultat tillbaka. Var och en av de här argumenten kontrolleras för att du är juridiska JSON-värden.  

SQL-syntax utökas så att den stöder anpassad programlogik med hjälp av dessa användardefinierade funktioner. UDF: er kan registreras med SQL-API och sedan referera till som en del av en SQL-fråga. De UDF: er är i själva verket exquisitely utformade anropas av frågor. Följd att det här alternativet har UDF: er inte åtkomst till context-objektet som har andra JavaScript typer (lagrade procedurer och utlösare). Eftersom frågorna i skrivskyddat läge, kan de köras på primära eller på sekundära repliker. Därför är UDF: er avsedda att köras på sekundära repliker till skillnad från andra typer av JavaScript.

Nedan visas ett exempel på hur en UDF kan registreras för Cosmos DB-databasen, särskilt under en dokumentsamling.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
```

I föregående exempel skapas en UDF vars namn är `REGEX_MATCH`. Godtas två JSON-strängvärden `input` och `pattern` och kontrollerar om de första matchar mönstret som anges i andra med hjälp av JavaScript-string.match() funktion.

Vi kan nu använda den här UDF i en fråga i en projektion. UDF: er måste vara kvalificerat med skiftlägeskänsliga prefixet ”udf”. När anropas från frågor. 

> [!NOTE]
> Innan du 3/17/2015 stöds Cosmos DB UDF anrop utan ”udf”. prefix som väljer REGEX_MATCH(). Det här anropande mönstret är inaktuell.  
> 
> 

**Fråga**

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

**Results**

```json
    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]
```

En användardefinierad funktion kan också användas i ett filter som visas i exemplet nedan, även kvalificerad med ”udf”. prefix för:

**Fråga**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

**Results**

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

I princip UDF: er är giltig skalära uttryck och kan användas i både projektioner och filter. 

Om du vill expandera på kraften i UDF: er, låt oss titta på ett annat exempel med villkorslogik:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
```

Nedan visas ett exempel som utövar UDF-filen.

**Fråga**

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    
```

**Results**

```json
     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]
```

Som i föregående exempel showcase, integrera UDF: er kraften i JavaScript-språket med SQL-API för att tillhandahålla ett omfattande programmerbart gränssnitt för att göra komplex procedurmässig, villkorsstyrd logik med hjälp av inbyggda funktionerna för JavaScript-körning.

SQL-API: et tillhandahåller argumenten till de användardefinierade funktioner för varje dokument i källan till det aktuella steget (WHERE-satsen eller SELECT-satsen) i bearbetning av en användardefinierad funktion. Resultatet ingår i den övergripande körning pipelinen sömlöst. Om egenskaperna som anges av en användardefinierad funktion parametrar inte är tillgängliga i JSON-värde, parametern anses inte definierats och kan därför UDF-anrop är helt och hållet hoppas över. På samma sätt om resultatet av en användardefinierad funktion är odefinierad ingår den inte i resultatet. 

Sammanfattningsvis är UDF: er fantastiska verktyg för att göra komplicerad affärslogik som en del av frågan.

### <a name="operator-evaluation"></a>Operatorn utvärdering
Cosmos DB och ritar parallels med JavaScript-operatorer och dess utvärdering semantik miljöpåverkan för en JSON-databas. Även om Cosmos DB försöker bevara JavaScript-semantik i JSON-stöd, avviker åtgärden utvärderingen i vissa fall.

I SQL-API, är till skillnad från traditionella SQL typer av värden ofta inte kända tills värdena hämtas från databasen. För att effektivt köra frågor, har de flesta av operatörerna strikta krav. 

SQL API utföra inte implicit konvertering, till skillnad från JavaScript. Exempelvis kan en fråga som `SELECT * FROM Person p WHERE p.Age = 21` matchar dokument som innehåller en egenskap för ålder vars värde är 21. Andra dokument vars ålder-egenskap stämmer med strängen ”21” eller andra eventuellt oändlig variationer som ”021”, ”21.0”, ”0021”, ”00021”, etc. matchas inte. Detta är däremot att JavaScript där strängvärden är implicit omvandlas till siffror (baserat på operator, t.ex.: ==). Det här alternativet är avgörande för att effektivt index som matchar i SQL-API: et. 

## <a name="parameterized-sql-queries"></a>Parametriserade SQL-frågor
Cosmos DB har stöd för frågor med parametrar som är uttryckt i med vanliga \@ notation. Parametriserad SQL ger stabil hantering av och undantagstecken användarindata, förhindrar oavsiktlig exponering av data via SQL-inmatning. 

Du kan till exempel skriva en fråga som tar efternamn och region som parametrar och sedan arbetsuppgifterna för olika värden för efternamn och adressläge baserat på indata från användaren.

```sql
    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Den här förfrågan kan sedan skickas till Cosmos DB som en fråga som innehåller JSON som visas nedan.

```sql
    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }
```

Argumentet för TOP kan ställas in använda parameteriserade frågor som visas nedan.

```sql
    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }
```

Parametervärden kan vara valfri giltig JSON (strängar, tal, booleska värden, null, och med matriser eller kapslad JSON). Också eftersom Cosmos DB utan schema verifieras parametrar inte mot alla typer.

## <a id="BuiltinFunctions"></a>Inbyggda funktioner
Cosmos DB stöder också ett antal inbyggda funktioner för vanliga åtgärder som kan användas i frågor som användardefinierade funktioner (UDF).

| Funktionen grupp          | Åtgärder                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Matematiska funktioner  | ABS, TAKET, EXP, VÅNING, LOG, LOG10, POWER, RESURSALLOKERING, LOGGA, SQRT, SQUARE, AVKORTA, funktionerna ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN och TAN |
| Funktioner för typkontroll | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED och IS_PRIMITIVE                                                           |
| Strängfunktioner        | CONCAT, innehåller, ENDSWITH, INDEX_OF, vänster, längd, lägre, LTRIM, Ersätt, replikering, OMVÄND, höger, RTRIM, STARTSWITH, DELSTRÄNGEN och övre       |
| Matrisfunktioner         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH och ARRAY_SLICE                                                                                         |
| Spatial funktioner       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID och ST_ISVALIDDETAILED                                                                           | 

Om du använder en användardefinierad funktion (UDF) som en inbyggd funktion är nu tillgänglig, bör du använda motsvarande inbyggd funktion som den ska gå snabbare att köra och mer effektivt. 

### <a name="mathematical-functions"></a>Matematiska funktioner
Matematiska funktioner utför en beräkning, baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde. Här är en tabell med inbyggda matematiska funktioner som stöds.


| Användning | Beskrivning |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | Returnerar det absoluta (positiva) värdet för det angivna numeriskt uttrycket. |
| [TAKET (num_expr)](#bk_ceiling) | Returnerar det minsta heltalsvärdet större än eller lika med det angivna numeriska uttrycket. |
| [VÅNING (num_expr)](#bk_floor) | Returnerar det största heltalet mindre än eller lika med det angivna numeriska uttrycket. |
| [EXP (num_expr)](#bk_exp) | Returnerar e upphöjt till det angivna numeriska uttrycket. |
| [LOGG (num_expr [, base])](#bk_log) | Returnerar den naturliga logaritmen för det angivna numeriska uttrycket eller med hjälp av den angivna basen för logaritmen |
| [Log10 (num_expr)](#bk_log10) | Returnerar det logaritmiska bas-10-värdet för det angivna numeriska uttrycket. |
| [AVRUNDA (num_expr)](#bk_round) | Returnerar ett numeriskt värde, avrundat till närmaste heltal. |
| [AVKORTA (num_expr)](#bk_trunc) | Returnerar ett numeriskt värde, trunkeras till närmaste heltal. |
| [SQRT (num_expr)](#bk_sqrt) | Returnerar kvadratroten för det angivna numeriska uttrycket. |
| [KVADRAT (num_expr)](#bk_square) | Returnerar i det angivna numeriska uttrycket. |
| [POWER (num_expr, num_expr)](#bk_power) | Returnerar kraften hos det angivna numeriska uttrycket till det angivna värdet. |
| [LOGGA (num_expr)](#bk_sign) | Returnerar värdet logga (-1, 0, 1) för det angivna numeriska uttrycket. |
| [Funktionerna ACOS (num_expr)](#bk_acos) | Returnerar vinkeln i radianer, vars cosinus är det angivna numeriska uttrycket; kallas även arccosinus. |
| [ASIN (num_expr)](#bk_asin) | Returnerar vinkeln i radianer, vars sinus är det angivna numeriska uttrycket. Detta kallas också arcsinus. |
| [ATAN (num_expr)](#bk_atan) | Returnerar vinkeln i radianer, vars tangent motsvarar det angivna numeriska uttrycket. Detta kallas också tangens. |
| [ATN2 (num_expr)](#bk_atn2) | Returnerar vinkeln i radianer mellan positiva x-axeln och ray från ursprunget till tidpunkten (y, x), där x och y är värdena för de två angivna flyttal-uttrycken. |
| [COS (num_expr)](#bk_cos) | Returnerar trigonometriska cosinus för den angivna vinkeln i radianer i det angivna uttrycket. |
| [COT (num_expr)](#bk_cot) | Returnerar trigonometriska cotangens för den angivna vinkeln i radianer i det angivna numeriska uttrycket. |
| [GRADER (num_expr)](#bk_degrees) | Returnerar den motsvarande vinkeln i grader för en vinkel angiven i radianer. |
| [PI)](#bk_pi) | Returnerar det konstanta värdet för PI. |
| [RADIANER (num_expr)](#bk_radians) | Returnerar radianer när ett numeriskt uttryck i grader, anges. |
| [SIN (num_expr)](#bk_sin) | Returnerar trigonometriska sinus för den angivna vinkeln i radianer i det angivna uttrycket. |
| [TAN (num_expr)](#bk_tan) | Returnerar tangens för input-uttryck i det angivna uttrycket. |

Exempel: du kan nu köra frågor som liknar följande:

**Fråga**

```sql
    SELECT VALUE ABS(-4)
```

**Results**

```json
    [4]
```
Den största skillnaden mellan Cosmos DB functions jämfört med ANSI SQL är att de är utformade att fungera väl med schemadata utan schema och blandade. Till exempel om du har ett dokument där egenskapen Size saknas eller har ett icke-numeriska värde som ”okänt” och sedan dokumentet hoppas över, i stället för att returnera ett fel.

### <a name="type-checking-functions"></a>Funktioner för typkontroll
Typ kontrollerar funktioner kan du kontrollera vilken typ av ett uttryck i SQL-frågor. Typ kontrollerar funktioner kan användas för att fastställa typen av egenskaperna i dokument direkt när den är variabel eller okänd. Här är en tabell med typ som stöds inbyggda kontrollerar funktioner.

<table>
<tr>
  <td><strong>Användning</strong></td>
  <td><strong>Beskrivning</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är en matris.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är ett booleskt värde.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är ett tal.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är ett JSON-objekt.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är en sträng.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om egenskapen har tilldelats ett värde.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om typ av värde är en sträng, nummer, booleskt värde eller null.</td>
</tr>

</table>

Med dessa funktioner kan köra du nu frågor som liknar följande:

**Fråga**

```sql
    SELECT VALUE IS_NUMBER(-4)
```

**Results**

```json
    [true]
```

### <a name="string-functions"></a>Strängfunktioner
Följande skalärfunktioner utföra en åtgärd på ett strängvärde för indata och returnerar en sträng, numeriskt eller booleskt värde. Här är en tabell med inbyggda strängfunktioner:

| Användning | Beskrivning |
| --- | --- |
| [LÄNGDEN (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Returnerar antalet tecken i angivet stränguttryck |
| [SAMMANFOGA (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Returnerar en sträng som är resultatet av att sammanfoga två eller flera strängvärden. |
| [DELSTRÄNGEN (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Returnerar en del av ett stränguttryck. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Returnerar ett booleskt värde som anger om först stränguttryck börjar med andra |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Returnerar ett booleskt värde som anger om först stränguttryck slutar med andra |
| [INNEHÅLLER (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Returnerar ett booleskt värde som anger om först stränguttryck innehåller andra. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Returnerar startpositionen för den första förekomsten av andra stränguttryck i första angivet stränguttryck eller -1 om strängen inte hittas. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Returnerar den vänstra delen av en sträng med det angivna antalet tecken. |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Returnerar den högra delen av en sträng med det angivna antalet tecken. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Returnerar ett stränguttryck efter att det tar bort inledande blanksteg. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Returnerar ett stränguttryck efter trunkera alla avslutande blanksteg. |
| [LÄGRE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Returnerar ett stränguttryck när versal data har konverterats till gemener. |
| [ÖVRE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Returnerar ett stränguttryck efter konvertera gemen data till versaler. |
| [Ersätt (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Ersätter alla förekomster av en angiven sträng-värde med ett annat värde. |
| [REPLIKERA (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) |Upprepar ett strängvärde ett angivet antal gånger. |
| [OMVÄND (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Returnerar den omvända ordningen som i ett strängvärde. |

Med dessa funktioner kan köra du nu frågor som liknar följande. Exempel: du kan återgå gruppnamnet i versaler på följande sätt:

**Fråga**

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

**Results**

```json
    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]
```

Eller konkatenera strängar som i det här exemplet:

**Fråga**

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]
```

Strängfunktioner kan också användas i WHERE-satsen för att filtrera resultat, som i följande exempel:

**Fråga**

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Matrisfunktioner
Följande skalärfunktioner utföra en åtgärd på en matris indatavärdet och returnera numeriska, matrisen eller booleskt värde. Här är en tabell med inbyggda matrisfunktioner:

| Användning | Beskrivning |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Returnerar antalet element i matrisen-uttryck. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Returnerar en matris som är resultatet av att sammanfoga två eller flera matrisvärden. |
| [ARRAY_CONTAINS (arr_expr, uttryck [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Returnerar ett booleskt värde som anger om matrisen innehåller det angivna värdet. Ange om matchningen är fullständigt eller partiellt. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Returnerar en del av ett matrisuttryck. |

Matrisfunktioner kan användas för att manipulera matriser i JSON. Här är till exempel en fråga som returnerar alla dokument där en av överordnade är ”Robin Wakefield”. 

**Fråga**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Du kan ange en partiell fragment för att matcha element i matrisen. Följande fråga söker efter alla överordnade med den `givenName` av `Robin`.

**Fråga**

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Här är ett annat exempel där ARRAY_LENGTH används för att hämta antalet underordnade per familj.

**Fråga**

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Spatial funktioner
Cosmos DB stöder följande öppna geospatiala Consortium (OGC) inbyggda funktioner för geospatiala frågor. 

<table>
<tr>
  <td><strong>Användning</strong></td>
  <td><strong>Beskrivning</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Returnerar avståndet mellan de två GeoJSON punkt, Polygon eller LineString-uttrycken.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Returnerar ett booleskt uttryck som anger om det första GeoJSON-objektet (punkt, Polygon eller LineString) är i det andra GeoJSON-objektet (punkt, Polygon eller LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Returnerar ett booleskt uttryck som anger om de två angivna GeoJSON-objekt (punkt, Polygon eller LineString) överlappar varandra.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Returnerar ett booleskt värde som anger om det angivna GeoJSON punkt, Polygon eller LineString-uttrycket är ogiltigt.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Returnerar ett JSON-värde som innehåller ett booleskt värde värdet om det angivna GeoJSON punkt, Polygon eller LineString-uttrycket är giltig och om det är ogiltig, dessutom orsak som ett strängvärde.</td>
</tr>
</table>

Spatial funktioner kan användas för att utföra närhetsförfrågningar mot spatialdata. Här är till exempel en fråga som returnerar alla family dokument som ligger inom 30 km för den angivna platsen med hjälp av den inbyggda funktionen ST_DISTANCE. 

**Fråga**

```sql
    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Mer information om geospatialt stöd i Cosmos DB finns i [arbeta med geospatiala data i Azure Cosmos DB](geospatial.md). Avrundar spatial funktioner och SQL-syntax för Cosmos DB. Nu ska vi ta en titt på hur LINQ-frågor till fungerar och hur det interagerar med syntaxen vi har sett hittills.

## <a id="Linq"></a>LINQ till SQL-API
LINQ är en .NET-programmeringsmodell som uttrycker beräkning som frågor på strömmar av objekt. Cosmos DB innehåller ett bibliotek för klientsidan gränssnittet med LINQ genom att underlätta konvertering mellan JSON och .NET-objekt och mappning från en delmängd av LINQ-frågor till Cosmos DB-frågor. 

Bilden nedan visar arkitekturen för stöd för LINQ-frågor med Cosmos DB.  Med Cosmos DB-klienten kan utvecklare skapa en **IQueryable** objekt som frågor direkt mot Cosmos DB-frågeprovider, som sedan översätter LINQ-frågan till en Cosmos DB-fråga. Frågan skickas sedan till Cosmos DB-servern för att hämta en uppsättning resultat i JSON-format. De returnerade resultaten deserialisera till en dataström med .NET-objekt på klientsidan.

![Arkitektur för stöd för LINQ-frågor med hjälp av SQL-API - SQL-syntax, frågespråk för JSON, databasbegrepp och SQL-frågor][1]

### <a name="net-and-json-mapping"></a>.NET och JSON-mappning
Mappningen mellan .NET-objekt och JSON-dokument är naturlig - varje medlemsfält har mappats till ett JSON-objekt, där fältnamnet är mappad till ”nyckeln” en del av objektet och den ”value”-delen är rekursivt som mappats till värdedelen för objektet. Exempel: The familj objekt som skapas är mappad till JSON-dokumentet som visas nedan. Och omvänt, JSON-dokumentet är mappad till en .NET-objekt.

**C#-klass**

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

**JSON**  

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```


### <a name="linq-to-sql-translation"></a>LINQ till SQL-översättning
Cosmos DB-frågeprovider utför en bästa arbete-mappning från en LINQ-fråga till en Cosmos DB SQL-fråga. I följande beskrivning förutsätter vi att läsaren har en grundläggande kunskaper om av LINQ.

Först för typsystemet stöder vi alla JSON primitiva typer – numeriska typer, booleskt värde, sträng eller null. Endast de här JSON-typer stöds. Följande skalära uttryck stöds.

* Konstanta värden – däribland konstanta värden för de primitiva datatyperna när frågan ska utvärderas.
* Egenskapen/matris indexuttryck – dessa uttryck referera till egenskapen för ett objekt eller ett matriselement.
  
     familj. ID;    Family.children[0].familyName;    Family.children[0].grade;    Family.children[n].grade; n är en int-variabel
* Aritmetiska uttryck – däribland vanliga matematiska uttryck på numeriska och booleska värden. För en fullständig lista finns i SQL-specifikationen.
  
     2 * family.children[0].grade;    x + y;
* Jämförelse av stränguttryck – däribland jämför ett strängvärde till vissa konstant strängvärde.  
  
     mother.familyName == ”Smith”;    child.givenName == s; s är en strängvariabel
* / Objektmatris skapande uttryck - dessa uttryck som returnerar ett objekt av typ sammansatt värde eller anonym typ eller en matris med sådana objekt. Dessa värden kan kapslas.
  
     ny överordnad {familyName = ”Smith”, givenName = ”Johan”}; nytt {först = 1, andra = 2}; en anonym typ med två fält              
     ny int [] {3, child.grade, 5};

### <a id="SupportedLinqOperators"></a>Lista över LINQ-operatorer som stöds
Här är en lista över LINQ-operatorer som stöds i LINQ-provider som ingår i SQL .NET SDK.

* **Välj**: projektioner översätts till SQL SELECT inklusive objektkonstruktion
* **Där**: filter översätter till SQL WHERE och stöd mellan & &, || och! till SQL-operatorer
* **SelectMany**: tillåter återgång av matriser till att ansluta till SQL-satsen. Kan användas till att kedjan/kapslade uttryck för att filtrera på matriselement
* **OrderBy och OrderByDescending**: översätts till ORDER BY stigande/fallande
* **Antal**, **summan**, **Min**, **Max**, och **genomsnittlig** operatörer för aggregering och deras async-motsvarigheter **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, och **AverageAsync**.
* **CompareTo**: översätts till intervallet jämförelser. Ofta används för strängar eftersom de inte är jämförbar i .NET
* **Ta**: översätts till SQL-upp för att begränsa resultaten av en fråga
* **Matematikfunktioner**: har stöd för översättning från. NET'S Abs, funktionerna Acos, Asin, Atan, taket, Cos, Exp, våning, Log, Log10, Pow, resursallokering, logga, Sin, Sqrt, Tan, Truncate till motsvarande SQL inbyggda funktioner.
* **Sträng funktioner**: har stöd för översättning från. NET'S Concat, innehåller, EndsWith, IndexOf, antal, ToLower, TrimStart, Ersätt, omvänd, TrimEnd, StartsWith, delsträngen, ToUpper till motsvarande SQL inbyggda funktioner.
* **Matrisen funktioner**: har stöd för översättning från. NET'S Concat innehåller och antalet till motsvarande SQL inbyggda funktioner.
* **Geospatiala funktioner för tillägget**: har stöd för översättning av stub-metoder avståndet i IsValid och IsValidDetailed till motsvarande SQL inbyggda funktioner.
* **En användardefinierad funktion tilläggsfunktion**: har stöd för översättning av metoden stub-UserDefinedFunctionProvider.Invoke till motsvarande användardefinierad funktion.
* **Diverse**: har stöd för översättning av coalesce och villkorlig operatörer. Kan översätta innehåller strängen innehåller, ARRAY_CONTAINS eller SQL-IN beroende på kontext.

### <a name="sql-query-operators"></a>SQL-frågeoperatorer
Här följer några exempel som illustrerar hur några av standard LINQ-frågeoperatorer översätts till Cosmos DB-frågor.

#### <a name="select-operator"></a>Välj Operator
Syntaxen är `input.Select(x => f(x))`, där `f` är ett skalärt uttryck.

**LINQ lambda-uttrycket**

    input.Select(family => family.parents[0].familyName);

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
```

**LINQ lambda-uttrycket**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

```sql
    SELECT VALUE f.children[0].grade + c
    FROM Families f 
```


**LINQ lambda-uttrycket**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

```sql
    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f
```


#### <a name="selectmany-operator"></a>SelectMany operator
Syntaxen är `input.SelectMany(x => f(x))`, där `f` är ett skalärt uttryck som returnerar en samlingstyp.

**LINQ lambda-uttrycket**

    input.SelectMany(family => family.children);

**SQL** 

```sql
    SELECT VALUE child
    FROM child IN Families.children
```

#### <a name="where-operator"></a>Där operator
Syntaxen är `input.Where(x => f(x))`, där `f` är ett skalärt uttryck som returnerar ett booleskt värde.

**LINQ lambda-uttrycket**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 
```

**LINQ lambda-uttrycket**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3
```

### <a name="composite-sql-queries"></a>Sammansatta SQL-frågor
Ovanstående operatörer kan ha för att skapa mer kraftfulla frågor. Eftersom Cosmos DB stöder kapslade samlingar, kan sammansättningen antingen sammanfogas eller kapslade.

#### <a name="concatenation"></a>Sammanfogning
Syntaxen är `input(.|.SelectMany())(.Select()|.Where())*`. En sammansatt fråga kan börja med en valfri `SelectMany` fråga följt av flera `Select` eller `Where` operatörer.

**LINQ lambda-uttrycket**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**LINQ lambda-uttrycket**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3
```


**LINQ lambda-uttrycket**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

```sql
    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)
```

**LINQ lambda-uttrycket**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

```sql
    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"
```


#### <a name="nesting"></a>Kapsling
Syntaxen är `input.SelectMany(x=>x.Q())` där frågor och är en `Select`, `SelectMany`, eller `Where` operator.

I en kapslad fråga tillämpas den inre frågan på varje element i samlingen yttre. En viktig funktion är att den inre frågan kan referera till fält i element i samlingen yttre som Självkopplingar.

**LINQ lambda-uttrycket**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

```sql
    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents
```

**LINQ lambda-uttrycket**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"
```


**LINQ lambda-uttrycket**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName
```

## <a id="ExecutingSqlQueries"></a>Köra SQL-frågor
Cosmos DB exponerar resurser via ett REST-API som kan anropas av alla språk som stöder HTTP/HTTPS-förfrågningar. Cosmos DB erbjuder också programmeringsbibliotek för flera populära språk som .NET, Node.js, JavaScript och Python. REST API och de olika bibliotek alla stöd för frågor via SQL. .NET SDK stöder LINQ fråga utöver SQL.

I följande exempel visas hur du skapar en fråga och skickar det mot ett Cosmos DB-databaskonto.

### <a id="RestAPI"></a>REST-API
Cosmos DB erbjuder en öppen RESTful-programmeringsmiljö via HTTP. Databaskonton kan etableras med hjälp av en Azure-prenumeration. Cosmos DB-resursmodell består av en uppsättning resurser under ett databaskonto som är adresserbara via en logisk och stabil URI. En uppsättning resurser kallas för ett flöde i det här dokumentet. Ett databaskonto består av en uppsättning databaser, som innehåller flera samlingar, som var och en av vilka i sin tur innehåller dokument, UDF: er och andra typer av resurser.

Grundläggande interaction modellen med dessa resurser är via HTTP-verb som GET, PUT, POST och ta bort med sina standard tolkning. Verbet POST används för att skapa en ny resurs, för att köra en lagrad procedur eller för att utfärda en Cosmos DB-fråga. Frågorna är alltid skrivskyddade åtgärder med inga sidoeffekter.

I följande exempel visas en POST för en SQL API-fråga som görs mot en samling som innehåller två exempeldokument vi har granskat hittills. Frågan har ett enkelt filter på namnegenskapen JSON. Observera användningen av den `x-ms-documentdb-isquery` och Content-Type: `application/query+json` rubriker för att ange att åtgärden en fråga.

**Förfrågan**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**Results**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Det andra exemplet visar en mer komplex fråga som returnerar flera resultat från kopplingen.

**Förfrågan**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Results**

```
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Om en frågas resultat kan inte ryms inom en enda resultatsida REST-API: et och returnerar sedan ett fortsättningstoken via den `x-ms-continuation-token` svarshuvudet. Klienter kan sidbryta resultat genom att inkludera rubriken i efterföljande resultat. Antalet resultat per sida kan också styras via den `x-ms-max-item-count` nummer rubrik. Om den angivna frågan har en Aggregeringsfunktion som `COUNT`, och sedan sidan kan returnera en delvis aggregerat värde resultatsidan. Klienterna måste utföra en andra nivån aggregering över dessa resultat för att skapa de slutliga resultaten, till exempel, summera över antal returneras i de enskilda sidorna att returnera det totala antalet.

Hantera Datapolicy för konsekvens för frågor med den `x-ms-consistency-level` rubrik som alla REST API-begäranden. För sessionskonsekvens, du behöver också skapa ett eko av senast `x-ms-session-token` Cookie-huvud i query-fråga. Den efterfrågade samlingens indexeringsprincip kan också påverka konsekvens i frågeresultaten. Med standardvärdet indexering principinställningar, för samlingar indexet är alltid uppdaterad med innehållet i dokumentet och frågeresultat matcha konsekvens som valts för data. Om indexprincip restriktiva till Lazy, kan frågor returnera inaktuella resultat. Mer information finns i [Azure Cosmos DB-Konsekvensnivåer][consistency-levels].

Om den konfigurera indexprincip på samlingen inte stöder den angivna frågan, returnerar 400 ”Felaktig begäran” i Azure Cosmos DB-servern. Detta returneras för intervallfrågor mot sökvägar som konfigurerats för hash (likhet) sökningar och för sökvägar som uttryckligen är undantagen från indexering. Den `x-ms-documentdb-query-enable-scan` huvudet kan anges för att tillåta frågan för att utföra en genomsökning när ett index inte är tillgänglig.

Du kan få detaljerade mätvärden om frågekörning genom att ange `x-ms-documentdb-populatequerymetrics` sidhuvud till `True`. Mer information finns i [SQL-fråga mått för Azure Cosmos DB](sql-api-sql-query-metrics.md).

### <a id="DotNetSdk"></a>SDK FÖR C# (.NET)
.NET SDK stöder både LINQ och SQL fråga. I följande exempel visas hur du utför enkla filterfrågan introducerade tidigare i det här dokumentet.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Det här exemplet jämför två egenskaper sinsemellan i varje dokument och använder anonym projektioner. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


I nästa exempel visas kopplingar, uttryckt genom LINQ SelectMany.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



.NET-klienten går automatiskt igenom alla sidor i frågeresultaten i foreach-block som ovan. Frågealternativ som presenteras i REST API-avsnitt är också tillgängliga i .NET SDK med hjälp av den `FeedOptions` och `FeedResponse` klasser i metoden CreateDocumentQuery. Antalet sidor kan kontrolleras med hjälp av den `MaxItemCount` inställningen. 

Du kan också uttryckligen styra sidindelning genom att skapa `IDocumentQueryable` med hjälp av den `IQueryable` objekt sedan genom att läsa den` ResponseContinuationToken` värden och skicka dem tillbaka som `RequestContinuationToken` i `FeedOptions`. `EnableScanInQuery` kan ställas in för att aktivera genomsökningar när frågan inte stöds av den konfigurera indexprincip. Partitionerade samlingar kan du använda `PartitionKey` att köra frågan mot en enda partition (även om Cosmos DB automatiskt extrahera detta från frågetexten) och `EnableCrossPartitionQuery` att köra frågor som behöver köras mot flera partitioner. 

Referera till [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-documentdb-net) för fler exempel som innehåller frågor. 

### <a id="JavaScriptServerSideApi"></a>JavaScript API för serversidan
Cosmos DB är en programmeringsmodell för att köra JavaScript-baserade programlogik direkt på samlingar med lagrade procedurer och utlösare. JavaScript-logik som registrerats på en samlingsnivå kan sedan utfärda databasåtgärder i åtgärderna på dokumenten i den givna samlingen. De här åtgärderna är omslutna i omgivande ACID-transaktioner.

I följande exempel visas hur du använder den queryDocuments i JavaScript-server API för att göra förfrågningar från insidan lagrade procedurer och utlösare.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Referenser
1. [Introduktion till Azure Cosmos DB][introduction]
2. [Azure Cosmos DB SQL-specifikation](https://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-documentdb-net)
4. [Azure Cosmos DB-Konsekvensnivåer][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. JavaScript-specifikation [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](https://msdn.microsoft.com/library/bb308959.aspx) 
9. Utvärdering av frågetekniker för stora databaser [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Bearbetning av frågor i parallella relationsdatabassystem, IEEE datorn Society Press, 1994
11. Lu, Ooi, Tan, bearbetning av frågor i parallella relationsdatabassystem, IEEE datorn Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: en inte så främmande språk för databehandling, SIGMOD 2008.
13. G. Graefe. Kaskadspridas ramverk för Frågeoptimeringen. IEEE Data Eng. Bull., 18.3: 1995.

[1]: ./media/sql-api-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
