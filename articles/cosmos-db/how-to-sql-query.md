---
title: SQL-frågor för Azure Cosmos DB
description: Läs mer om SQL-syntax, databasbegrepp och SQL-frågor för Azure Cosmos DB. Använd SQL som ett Azure Cosmos DB JSON-frågespråket.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: a5cc6bfca67f3d90467fa2339bc991c1f0bbeadf
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148939"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>SQL-exempelfrågor för Azure Cosmos DB

Azure Cosmos DB SQL API-konton stöder förfrågningar till objekt som använder SQL Structured Query Language () som ett frågespråk för JSON. Designmålen för Azure Cosmos DB-frågespråket är att:

* Stöd för SQL, ett av de mest välkända och populära fråga språk, i stället för inventing ett nytt frågespråk. SQL är en formell programmeringsmodell för komplexa frågor via JSON-objekt.  

* Använda JavaScript-programmeringsmodell som grund för frågespråket. JavaScript-typsystemet, uttrycksutvärdering och funktionsanrop är roten i SQL-API. Dessa rötter tillhandahåller en naturlig programmeringsmodell för funktioner som relationella projektioner hierarkisk navigering i JSON-objekt, Självkopplingar, rumsliga förfrågningar och anrop av användardefinierade funktioner (UDF) helt skrivna i JavaScript.

Den här artikeln vägleder dig igenom några exempel på SQL-frågor med enkla JSON-objekt. Läs mer om Azure Cosmos DB SQL syntaxen i [referens för SQL-syntax](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Kom igång med SQL-frågor

I ditt SQL API Cosmos DB-konto skapar du en behållare som kallas `Families`. Skapa två enkla JSON-objekt i behållaren och köra några enkla frågor mot dem.

### <a name="create-json-items"></a>Skapa JSON-objekt

Följande kod skapar två enkla JSON-objekt om familjer. Enkla JSON-objekt för familjen Andersen och Wakefield-serier är föräldrar, barn och deras husdjur, adress och registreringsinformation. Det första objektet har strängar, tal, booleska värden, matriser och kapslade egenskaper.


```json
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
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Objektet på andra använder `givenName` och `familyName` i stället för `firstName` och `lastName`.

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
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Frågan JSON-objekt

Prova några frågor mot JSON-data för att förstå några av de viktigaste aspekterna i Azure Cosmos DB SQL-frågespråket.

Följande fråga returnerar objekt där den `id` fältet matchar `AndersenFamily`. Eftersom det är en `SELECT *` frågan, resultatet av frågan är fullständigt JSON-objekt. Mer information om hur du väljer syntax finns i [SELECT-instruktion](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet av frågan är: 

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
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Följande fråga förbereder JSON-utdata till en annan form. Frågan genererar en ny JSON `Family` objekt med två valda fält, `Name` och `City`när adressen stad är samma som tillståndet. ”USA, USA” matchar det här fallet.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Resultatet av frågan är:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Följande fråga returnerar alla angivna namn på barnen i familjen vars `id` matchar `WakefieldFamily`, ordnade efter stad där.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Resultatet är:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

I föregående exempel visas flera aspekter av frågespråket Cosmos DB:  

* Eftersom SQL-API fungerar på JSON-värden, behandlar den trädet formad entiteter i stället för rader och kolumner. Du kan referera till trädnoder på varje godtyckliga djup som `Node1.Node2.Node3…..Nodem`, ungefär som i två delar referens `<table>.<column>` i ANSI SQL.

* Frågespråket fungerar med schemalös data, att typsystemet måste bindas dynamiskt. Samma uttryck kan ge olika typer på olika objekt. Resultatet av en fråga är ett giltigt JSON-värde, men är inte garanterad ska vara av ett fast schema.  

* Azure Cosmos DB stöder endast strikta JSON-objekt. Den och uttryck är begränsade till bara handlar om JSON-typer. Mer information finns i den [JSON-specifikationen](https://www.json.org/).  

* En Cosmos DB-container är en schemafri samling med JSON-objekt. Relationer inom och mellan behållare objekt avbildas implicit av inneslutning, inte av primärnyckel och sekundärnyckel viktiga relationer. Den här funktionen är viktig för intra-item-kopplingar som beskrivs senare i den här artikeln.

## <a id="SelectClause"></a>SELECT-satsen

Varje fråga består av en SELECT-satsen och valfria FROM och WHERE-satserna per ANSI SQL-standarder. Normalt källan i FROM-satsen räknas och WHERE-satsen används ett filter på källan för att hämta en delmängd av JSON-objekt. SELECT-satsen genererar sedan de begärda JSON-värden i select-listan. Mer information om syntaxen finns i [SELECT-instruktion](sql-api-query-reference.md#select-query).

Välj följande fråga exempel returnerar `address` från `Families` vars `id` matchar `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>Accessor med citatomsluten egenskap
Du kan komma åt egenskaper med hjälp av citerade egenskapen operatorn []. Till exempel är `SELECT c.grade` och `SELECT c["grade"]` likvärdiga. Den här syntaxen är användbart för att undvika en egenskap som innehåller blanksteg, specialtecken, eller har samma namn som en SQL-nyckelord eller reserverat ord.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Kapslade egenskaper

I följande exempel projekt två kapslade egenskaper `f.address.state` och `f.address.city`.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>JSON-uttryck

Projektion stöder också JSON-uttryck, som visas i följande exempel:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

SELECT-satsen i föregående exempel, som behövs för att skapa en JSON-objekt och eftersom det innehåller ingen nyckel, i satsen använder implicit argumentet variabelnamnet `$1`. Följande fråga returnerar två argumentvariabler som implicit: `$1` och `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>Nyckelordet VALUE

Nyckelordet värde är ett sätt att returnera värdet på JSON enbart. Exempelvis kan den fråga som visas nedan returnerar det skalära uttrycket `"Hello World"` i stället för `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

Följande fråga returnerar JSON-värden utan den `address` etikett:

```sql
    SELECT VALUE f.address
    FROM Families f
```

Resultatet är:

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

I följande exempel visas hur du returnerar JSON primitiva värden (lövnivån av JSON-träd):


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

Resultatet är:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="DistinctKeyword"></a>DISTINKTA nyckelord

Nyckelordet DISTINCT eliminerar dubbletter i frågans projektion.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

I det här exemplet genererar frågan värden för varje efternamn.

Resultatet är:

```json
[
    "Andersen"
]
```

Du kan också projicera unika objekt. Efternamn finns i det här fallet inte på något av två dokument, så att frågan returnerar ett tomt-objekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Resultatet är:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT kan också användas i projektionen i en underfråga:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Den här frågan genererar en matris som innehåller varje underordnad givenName med borttagna dubbletter. Den här matrisen är ett alias för konsolkommandot ChildNames och planerade i yttre frågan.

Resultatet är:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="aliasing"></a>Alias

Du kan uttryckligen alias värden i frågor. Om en fråga har två egenskaper med samma namn, kan du använda alias för att byta namn på en eller båda egenskaperna så att de är skiljas åt i det beräknade resultatet.

As-nyckelord som används för alias är valfritt, som visas i följande exempel när du projicerar det andra värdet som `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM-sats

FRÅN (`FROM <from_specification>`)-satsen är valfritt, om inte källan filtreras eller projected senare i frågan. Mer information om syntaxen finns i [från syntax](sql-api-query-reference.md#bk_from_clause). En fråga som `SELECT * FROM Families` räknar upp över hela `Families` behållare. Du kan också använda den särskilda identifieraren ROTEN för behållaren istället för att använda behållarens namn.

FROM-satsen tillämpar följande regler per fråga:

* Containern kan vara ett alias, till exempel `SELECT f.id FROM Families AS f` eller bara `SELECT f.id FROM Families f`. Här `f` är alias för `Families`. SOM är ett valfritt nyckelord för alias identifierare.  

* När ett alias, det ursprungliga namnet källa kan inte bindas. Till exempel `SELECT Families.id FROM Families f` är syntaktiskt felaktig eftersom identifieraren `Families` har varit ett alias och går inte att matcha längre.  

* Alla refererade egenskaper måste vara fullständigt kvalificerade att undvika eventuella tvetydig bindningar om efterlevnad av strikt schema. Till exempel `SELECT id FROM Families f` är syntaktiskt felaktig eftersom egenskapen `id` är inte bundet.

### <a name="get-subitems-by-using-the-from-clause"></a>Hämta underobjekt genom att använda FROM-satsen

FROM-satsen kan minska källan till en mindre deluppsättning. Om du vill räkna upp bara ett underträd i varje objekt blir i subroot källan, som visas i följande exempel:

```sql
    SELECT *
    FROM Families.children
```

Resultatet är:

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

Den föregående frågan används en matris som källan, men du kan också använda ett objekt som källa. Frågan tar hänsyn till något giltigt har definierats JSON-värde i källan ska ingå i resultatet. I följande exempel skulle undanta `Families` som inte har en `address.state` värde.

```sql
    SELECT *
    FROM Families.address.state
```

Resultatet är:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>WHERE-sats

Valfria WHERE-satsen (`WHERE <filter_condition>`) anger villkor att käll-JSON-objekt måste uppfylla för frågan och lägga till dem i resultaten. Ett JSON-objekt måste utvärderas de angivna villkoren till `true` man ta hänsyn till resultatet. Index-lagret använder WHERE-satsen för att fastställa den minsta delmängden källobjekt som kan ingå i resultatet. Mer information om syntaxen finns i [var syntax](sql-api-query-reference.md#bk_where_clause).

Följande fråga begäranden-objekt som innehåller en `id` egenskap vars värde är `AndersenFamily`. Den omfattar inte alla objekt som inte har en `id` egenskapen eller vars värde inte matchar `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skalära uttryck i WHERE-satsen

I föregående exempel visades en enkel likhetsfråga. SQL-API: et stöder olika [skaläruttryck](#scalar-expressions). De mest använda är binära och unära uttryck. Egenskapsreferenser från JSON-källobjektet är också giltiga uttryck.

Du kan använda följande operatorer som stöds binär:  

|**Operatortyp**  | **Värden** |
|---------|---------|
|Aritmetiska | +,-,*,/,% |
|Binära    | \|, &, ^, <<, >>, >>> (högerskiftning med nollfyllning) |
|Logiska    | AND, OR, NOT (och, eller, inte)      |
|Jämförelse | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (sammanfoga) |

Följande frågor använder de binära operatorerna som:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Du kan också använda unära operatorer +,-, ~, och inte i frågor som visas i följande exempel:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Du kan också använda egenskapen referenser i frågor. Till exempel `SELECT * FROM Families f WHERE f.isRegistered` returnerar JSON-objektet som innehåller egenskapen `isRegistered` med värdet som är lika med `true`. Någon annan värde, till exempel `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, eller `<array>`, utesluter objektet från resultatet. 

### <a name="equality-and-comparison-operators"></a>Likhets- och jämförelseoperatorer

I följande tabell visas resultatet av likhetsjämförelser i SQL API mellan två godtyckliga JSON-typer.

| **Op** | **Odefinierad** | **Null** | **Boolesk** | **Nummer** | **Sträng** | **Objekt** | **Matris** |
|---|---|---|---|---|---|---|---|
| **Odefinierad** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Null** | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Boolesk** | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Nummer** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Sträng** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Objekt** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) |
| **Matris** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** |

För jämförelseoperatorer som `>`, `>=`, `!=`, `<`, och `<=`, jämförelse över typer eller mellan två objekt eller matriser ger `Undefined`.  

Om resultatet av det skalära uttrycket är `Undefined`, objektet inte ingår i resultatet, eftersom `Undefined` är inte lika med `true`.

### <a name="logical-and-or-and-not-operators"></a>Logiska operatorer (AND, OR och NOT) (och, eller samt inte)

Logiska operatorer arbetar med booleska värden. Följande tabeller visar de logiska tabellerna sanningen för de här operatorerna:

**Operatorn OR** (ELLER)

| ELLER | True | False | Undefined (Odefinierad) |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined (Odefinierad) |
| Undefined (Odefinierad) |True |Undefined (Odefinierad) |Undefined (Odefinierad) |

**Operatorn AND** (OCH)

| AND | True | False | Undefined (Odefinierad) |
| --- | --- | --- | --- |
| True |True |False |Undefined (Odefinierad) |
| False |False |False |False |
| Undefined (Odefinierad) |Undefined (Odefinierad) |False |Undefined (Odefinierad) |

**Operatorn NOT** (INTE)

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined (Odefinierad) |Undefined (Odefinierad) |

## <a name="between-keyword"></a>Nyckelordet BETWEEN (mellan)

Du kan använda nyckelordet BETWEEN för att uttrycka frågor mot cellområden sträng eller numeriska värden som ANSI SQL. Till exempel returnerar följande fråga alla objekt som den första underordnade i företagsklass är 1-5, inklusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Till skillnad från i ANSI SQL, kan du också använda BETWEEN-satsen i FROM-satsen, som i följande exempel.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Du kan ange intervallfrågor mot egenskaper av olika typer i SQL-API, till skillnad från ANSI SQL. Till exempel `grade` kan vara ett nummer som `5` i vissa objekt och liknande `grade4` i andra. I dessa fall, som i JavaScript, en jämförelse mellan de två olika typerna resulterar i `Undefined`, så att objektet har hoppats över.

> [!TIP]
> Skapa en indexprincip som använder en intervallet Indextyp mot alla egenskaper för numeriska eller sökvägar BETWEEN-satsen filtrerar för snabbare fråga körningstider.

## <a name="in-keyword"></a>Nyckelordet IN (I)

Använda nyckelordet IN för att kontrollera om ett angivet värde matchar något värde i en lista. Till exempel följande fråga returnerar alla family objekt där den `id` är `WakefieldFamily` eller `AndersenFamily`.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

I följande exempel returneras alla objekt där tillståndet är någon av de angivna värdena:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* operator

Operatorn särskilda * projekt hela objektet skick. När den används måste den vara det enda projicerade fältet. En fråga som `SELECT * FROM Families f` är giltig, men `SELECT VALUE * FROM Families f` och `SELECT *, f.id FROM Families f` är inte giltiga. Den [först fråga i den här artikeln](#query-the-json-items) används den * operator. 

## <a name="-and--operators"></a>? och rubrikrad? Operatörer

Du kan använda Ternär (?) och slå samman operatorer (?) för att skapa villkorsuttryck som programmeringsspråk som C# och JavaScript. 

Du kan använda den? operator för att skapa nya JSON-egenskaper i farten. Till exempel följande fråga klassificerar i företagsklass nivåer i `elementary` eller `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Du kan även kapsla anrop till den? operatorn, som i följande fråga: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Precis som med andra frågeoperatorer den? operatorn utesluter objekt om de refererade egenskaperna saknas eller de typer som jämförs är olika.

Använd den rubrikrad? operator för att effektivt söka efter en egenskap i ett objekt vid frågor mot halvstrukturerade eller olika typer av data. Till exempel följande fråga returnerar `lastName` om TPM finns, eller `surname` om `lastName` saknas.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>Operatorn TOP

Nyckelordet ÖVERSTA returnerar första `N` antal frågeresultaten i en odefinierad order. Ett bra tips är att använda TOP med ORDER BY-satsen för att begränsa resultaten till först `N` antal sorterad värden. Om du kombinerar dessa två satser är det enda sättet att förutsägbart indikerar vilka rader ÖVERSTA påverkar.

Du kan använda upp med ett konstant värde, som i följande exempel, eller med ett variabelvärde använda parameteriserade frågor. Mer information finns i den [frågor som innehåller parametrar](#parameterized-queries) avsnittet.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Resultatet är:

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
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>ORDER BY-satsen

Du kan inkludera ett valfritt ORDER BY-sats i frågor som i ANSI SQL. Det valfria argumentet ASC eller DESC anger om du vill hämta resultat i stigande eller fallande ordning. ASC är standardinställningen.

Här är till exempel en fråga som hämtar familjer i stigande ordning efter den fasta Ortnamn:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Resultatet är:

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

Följande fråga hämtar familj `id`s efter skapandedatum sina objekt. Objektet `creationDate` är ett tal som representerar den *epoktid*, eller förfluten tid sedan den 1 januari 1970 på några sekunder.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Resultatet är:

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

Dessutom kan du sortera efter flera egenskaper. En fråga som grupperas efter flera egenskaper kräver en [sammansatta index](index-policy.md#composite-indexes). Överväg följande fråga:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Den här frågan hämtar familjen `id` i stigande ordning efter namnet på staden. Om flera objekt har samma stad namn kan frågan ska sortera efter den `creationDate` i fallande ordning.

## <a id="OffsetLimitClause"></a>GRÄNSEN för OFFSET-sats

GRÄNSEN för förskjutning är en valfri sats att hoppa över och sedan vidta vissa antal värden från frågan. Antalet förskjutning och GRÄNSEN för antal måste anges i instruktionen förskjutning GRÄNSEN.

När GRÄNSEN för förskjutning används tillsammans med en ORDER BY-sats, skapas genom att göra hoppa över resultatuppsättningen och utför på sorterad värdena. Om någon ORDER BY-sats används, resulterar det i en deterministisk ordning med värden.

Här är till exempel en fråga som det första värdet och returnerar det andra värdet (i ordningen för fasta stadens namn):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Resultatet är:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Här är en fråga som det första värdet och returnerar det andra värdet (utan att beställa):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Resultatet är:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```




## <a name="scalar-expressions"></a>Skalära uttryck

SELECT-satsen stöder skalärt uttryck som konstanter, matematiska uttryck och logiska uttryck. Följande fråga använder ett skalärt uttryck:


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Resultatet är:

```json
    [{
      "$1": 1.33333
    }]
```

Resultatet av det skalära uttrycket är ett booleskt värde i följande fråga:


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Resultatet är:

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

## <a name="object-and-array-creation"></a>Skapande av objekt och matris

En viktig funktion i SQL-API: T är matris och objekt skapas. I föregående exempel skapas ett nytt JSON-objekt, `AreFromSameCityState`. Du kan också skapa matriser, som visas i följande exempel:


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Resultatet är:

```json
    [
      {
        "CityState": [
          "Seattle",
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


## <a id="Iteration"></a>Iteration

SQL-API har stöd för att iterera över JSON-matriser med en ny konstruktion som lagts till via IN-nyckelordet i FROM-källan. I följande exempel:

```sql
    SELECT *
    FROM Families.children
```

Resultatet är:

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

Nästa fråga utför iteration `children` i den `Families` behållare. Utdata-matrisen skiljer sig från den föregående frågan. Det här exemplet delar upp `children`, och plattar ut resultatet till en enskild matris:  

```sql
    SELECT *
    FROM c IN Families.children
```

Resultatet är:

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

Du kan filtrera ytterligare på varje enskild post i matrisen som du ser i följande exempel:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Resultatet är:

```json
    [{
      "givenName": "Lisa"
    }]
```

Du kan även aggregera över resultatet av en matris iteration. Till exempel räknar följande fråga antalet underordnade bland alla serier:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Resultatet är:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Kopplingar

Kopplingar mellan tabeller finns i en relationsdatabas, den logiska naturlig följd att utforma normaliserade scheman. Däremot SQL API använder Avnormaliserade datamodellen schemafria objekt som är den logiska motsvarar en *självkoppling*.

Språket stöder syntaxen `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Den här frågan returnerar en uppsättning tupplar med `N` värden. Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar. Med andra ord utför den här frågan en fullständig kryssprodukt av de uppsättningar som deltar i kopplingen.

I följande exempel visas hur JOIN-satsen fungerar. Resultatet är tom, sedan kryssprodukten av varje objekt från källa och en tom uppsättning är tom i exemplet nedan:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Resultatet är:

```json
    [{
    }]
```

I följande exempel i kopplingen är en kryssprodukten mellan två JSON-objekt, objekt-roten `id` och `children` subroot. Faktumet som `children` är en matris inte är effektivt i kopplingen, eftersom det handlar om en enda rot som är den `children` matris. Resultatet innehåller bara två resultat, eftersom kryssprodukten av varje objekt med matrisen ger exakt endast ett objekt.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Resultatet är:

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

I följande exempel visas en mer konventionell koppling:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Resultatet är:

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

JOIN-satsen FROM orsaken är ett Iteratorn. Därför är flödet i föregående exempel:  

1. Expandera varje underordnat element `c` i matrisen.
2. Tillämpa en kryssprodukten med rot objektets `f` med varje underordnat element `c` som det första steget tillplattad.
3. Slutligen projektet rotobjektet `f` `id` egenskapen enbart.

Det första objektet `AndersenFamily`, innehåller endast en `children` element, så resultatet innehåller bara ett enda objekt. Det andra objektet `WakefieldFamily`, innehåller två `children`, så kryssprodukten skapar två objekt, en för varje `children` element. Rotfälten i båda dessa objekt är detsamma, vilket är det som förväntas i en kryssprodukt.

Verkliga nyttan av JOIN-satsen är att formuläret tupplar från kryssprodukten i en form som annars är svåra att projektet. Exemplet nedan filter på en kombination av en tuppel som låter användaren välja ett villkor som är nöjd med den övergripande tupplar.

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

Resultatet är:

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

Följande tillägg i föregående exempel skapar en dubbla koppling. Du kan visa kryssprodukten som genererat följande kod:

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

`AndersenFamily` har en underordnad som har en husdjur så kryssprodukten ger en rad (1\*1\*1) från den här serien. `WakefieldFamily` har två barn, endast en av som har husdjur, men den underordnat har två husdjur. Kryssprodukten för den här serien ger 1\*1\*2 = 2 rader.

I nästa exempel finns ett ytterligare filter på `pet`, vilket utesluter alla tupplar där husdjur namnet inte är `Shadow`. Du kan skapa tupplar från matriser, filter på någon av elementen i tuppeln och projicera olika kombinationer av elementen.

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

Resultatet är:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>Användardefinierade funktioner (UDF)

SQL-API har stöd för användardefinierade funktioner (UDF). Du kan skicka in noll eller flera argument och returnera ett enda argument-resultat med skalära UDF: er. API: et kontrollerar varje argument för att du är juridiska JSON-värden.  

API: et utökar den SQL-syntaxen för att stöder anpassad programlogik med UDF: er. Du kan registrera UDF: er med SQL-API: T och referera till dem i SQL-frågor. Faktum är att UDF:er har utmärkt utformning för att anropas från frågor. Följd har UDF: er inte åtkomst till context-objektet som andra JavaScript-typer, till exempel lagrade procedurer och utlösare. Frågor är skrivskyddad och kan köras antingen på primära eller sekundära repliker. Användardefinierade funktioner, till skillnad från andra JavaScript-typer är avsedda att köras på sekundära repliker.

I följande exempel registreras en UDF under en objektbehållaren i Cosmos DB-databasen. I exemplet skapas en UDF vars namn är `REGEX_MATCH`. Godtas två JSON-strängvärden `input` och `pattern`, och kontrollerar om de första matchar mönstret som anges i andra med hjälp av JavaScript- `string.match()` funktion.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Nu kan använda den här UDF i en fråga projektion. Du måste är berättigad UDF: er med skiftlägeskänsliga prefixet `udf.` när du anropar dem från inom frågor.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Resultatet är:

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

Du kan använda UDF kvalificerad med den `udf.` prefix i ett filter, som i följande exempel:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Resultatet är:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

I princip är UDF: er giltig skalärt uttryck som du kan använda i både projektioner och filter.

Titta på ett annat exempel med villkorsstyrd logik för att expandera på kraften i UDF: er:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
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
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

I följande exempel utövar UDF-filen:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Resultatet är:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Om egenskaperna som anges av en användardefinierad funktion parametrar inte är tillgängliga i JSON-värde, parametern betraktas som en odefinierad och hoppas över UDF-anrop. På samma sätt, om resultatet av en användardefinierad funktion är odefinierad den ingår inte i resultatet.

Föregående exempel visas integrera UDF: er kraften i JavaScript-språket med SQL API. UDF: er ger ett omfattande programmerbart gränssnitt för att göra komplex procedurmässig, villkorsstyrd logik med hjälp av inbyggda funktioner för JavaScript-körning. SQL-API: et tillhandahåller argumenten till de UDF: er för varje källa-objekt på den aktuella var eller SELECT-satsen steg i processen. Resultatet är sömlöst integrerat i övergripande körning pipelinen. Sammanfattningsvis är UDF: er fantastiska verktyg för att göra komplicerad affärslogik som en del av frågor.

## <a id="Aggregates"></a>Mängdfunktioner

Mängdfunktioner utföra beräkningar på en uppsättning värden i SELECT-satsen och returnera ett enstaka värde. Till exempel följande fråga returnerar antalet objekt i den `Families` behållare:

```sql
    SELECT COUNT(1)
    FROM Families f
```

Resultatet är:

```json
    [{
        "$1": 2
    }]
```

Du kan också returnera endast skalära värdet för mängden med hjälp av nyckelordet värde. Till exempel returnerar följande fråga antalet värden som ett enskilt tal:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Resultatet är:

```json
    [ 2 ]
```

Du kan också kombinera aggregeringar med filter. Till exempel följande fråga returnerar antalet objekt med adressen tillstånd `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Resultatet är:

```json
    [ 1 ]
```

SQL-API: et stöder mängdfunktionerna. Summa och Genomsnittlig fungerar på numeriska värden och COUNT, MIN och MAX fungerar på tal, strängar, booleska värden och null-värden.

| Funktion | Beskrivning |
|-------|-------------|
| COUNT | Returnerar antalet objekt i uttrycket. |
| SUM   | Returnerar summan av alla värden i uttrycket. |
| MIN   | Returnerar minimivärdet i uttrycket. |
| MAX   | Returnerar maxvärdet i uttrycket. |
| AVG   | Returnerar medelvärdet av värdena i uttrycket. |

Du kan även aggregera över resultatet av en matris iteration. Mer information finns i den [Iteration](#Iteration) avsnittet.

> [!NOTE]
> I Datautforskaren i Azure portal, kan mängdfrågor aggregera ofullständiga resultat via sidan för enbart en fråga. SDK: N producerar en enda ackumulerade värdet på alla sidor. Om du vill utföra mängdfrågor med hjälp av kod du behöver .NET SDK 1.12.0, .NET Core SDK 1.1.0 eller Java SDK 1.9.5 eller senare.
>

## <a id="BuiltinFunctions"></a>Inbyggda funktioner

Cosmos DB stöder också ett antal inbyggda funktioner för vanliga åtgärder som du kan använda i frågor som användardefinierade funktioner (UDF).

| Funktionsgrupp | Åtgärder |
|---------|----------|
| Matematiska funktioner | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Funktioner för typkontroll | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Strängfunktioner | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Matrisfunktioner | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH och ARRAY_SLICE |
| Spatiella funktioner | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Om du använder en användardefinierad funktion (UDF) som en inbyggd funktion är nu tillgänglig, blir motsvarande inbyggda funktionen går snabbare att köra och mer effektivt.

Den största skillnaden mellan Cosmos DB och ANSI SQL-funktioner är att Cosmos DB-funktioner är avsedda att fungera bra med schemalös och blandat schema. Till exempel om en egenskap saknas eller har ett icke-numeriska värde som `unknown`, objektet har hoppats över i stället för att returnera ett fel.

### <a name="mathematical-functions"></a>Matematiska funktioner

De matematiska funktionerna utför en beräkning baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde. Här är en tabell med inbyggda matematiska funktioner som stöds.

| Användning | Beskrivning |
|----------|--------|
| ABS (num_expr) | Returnerar det absoluta (positiva) värdet för det angivna numeriska uttrycket. |
| CEILING (num_expr) | Returnerar det minsta heltalsvärdet som är större än eller lika med det angivna numeriska uttrycket. |
| FLOOR (num_expr) | Returnerar det största heltalsvärdet som är mindre än eller lika med det angivna numeriska uttrycket. |
| EXP (num_expr) | Returnerar exponenten för det angivna numeriska uttrycket. |
| LOGG (num_expr, grundläggande) | Returnerar den naturliga logaritmen för det angivna numeriska uttrycket eller med hjälp av den angivna basen för logaritmen. |
| LOG10 (num_expr) | Returnerar det logaritmiska bas 10-värdet för det angivna numeriska uttrycket. |
| ROUND (num_expr) | Returnerar ett numeriskt värde avrundat till närmaste heltal. |
| TRUNC (num_expr) | Returnerar ett numeriskt värde trunkerat till närmaste heltal. |
| SQRT (num_expr) | Returnerar kvadratroten av det angivna numeriska uttrycket. |
| SQUARE (num_expr) | Returnerar kvadraten av det angivna numeriska uttrycket. |
| POWER (num_expr, num_expr) | Returnerar exponenten för det angivna numeriska uttrycket till det angivna värdet. |
| SIGN (num_expr) | Returnerar teckenvärdet (-1, 0, 1) för det angivna numeriska uttrycket. |
| ACOS (num_expr) | Returnerar vinkeln i radianer vars cosinus är det angivna numeriska uttrycket. Kallas även arccosinus. |
| ASIN (num_expr) | Returnerar vinkeln i radianer vars sinus är det angivna numeriska uttrycket. Den här funktionen kallas även arcsinus. |
| ATAN (num_expr) | Returnerar vinkeln i radianer vars tangent är det angivna numeriska uttrycket. Den här funktionen kallas även tangens. |
| ATN2 (num_expr) | Returnerar vinkeln i radianer mellan den positiva x- axeln och strålen från origo till punkten (y, x) där x och y är värdena för de två angivna flyttalsuttrycken. |
| COS (num_expr) | Returnerar trigonometrisk cosinus för den angivna vinkeln i radianer i det angivna uttrycket. |
| COS (num_expr) | Returnerar trigonometrisk cotangens för den angivna vinkeln i radianer i det angivna numeriska uttrycket. |
| DEGREES (num_expr) | Returnerar motsvarande vinkel i grader för en vinkel som anges i radianer. |
| PI () | Returnerar konstantvärdet för PI. |
| RADIANS (num_expr) | Returnerar radianer när ett numeriskt uttryck i grader anges. |
| SIN (num_expr) | Returnerar trigonometrisk sinus för den angivna vinkeln i radianer i det angivna uttrycket. |
| TAN (num_expr) | Returnerar tangens för indatauttrycket i det angivna uttrycket. |

Du kan köra frågor som i följande exempel:

```sql
    SELECT VALUE ABS(-4)
```

Resultatet är:

```json
    [4]
```

### <a name="type-checking-functions"></a>Funktioner för typkontroll

Funktioner för typkontroll kan du kontrollera vilken typ av ett uttryck i en SQL-fråga. Du kan använda typkontroll funktioner för att avgöra vilka typer av egenskaper i objekt i farten, när de är variabel eller okänd. Här är en tabell med stöds inbyggda funktioner för typkontroll:

| **Användning** | **Beskrivning** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | Returnerar ett booleskt värde som anger huruvida värdets typ är en matris. |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | Returnerar ett booleskt värde som anger huruvida värdets typ är ett booleskt värde. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | Returnerar ett booleskt värde som anger huruvida värdets typ är null. |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | Returnerar ett booleskt värde som anger huruvida värdets typ är ett tal. |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | Returnerar ett booleskt värde som anger huruvida värdets typ är ett JSON-objekt. |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | Returnerar ett booleskt värde som anger huruvida värdets typ är en sträng. |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | Returnerar ett booleskt värde som anger om typ av värde är en sträng, nummer, booleskt värde, eller null. |

Du kan använda dessa funktioner för att köra frågor som i följande exempel:

```sql
    SELECT VALUE IS_NUMBER(-4)
```

Resultatet är:

```json
    [true]
```

### <a name="string-functions"></a>Strängfunktioner

Följande skalärfunktioner utföra en åtgärd på ett strängvärde för indata och returnerar en sträng, numeriskt värde eller booleskt värde. Här är en tabell med inbyggda strängfunktioner:

| Användning | Beskrivning |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | Returnerar antalet tecken i angivet stränguttryck. |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Returnerar en sträng som är resultatet av en sammanfogning av två eller fler strängvärden. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Returnerar en del av ett stränguttryck. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Returnerar ett booleskt värde som anger om först stränguttryck börjar med andra. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Returnerar ett booleskt värde som anger om först stränguttryck slutar med andra. |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Returnerar startpositionen för den första förekomsten av andra stränguttryck i första angivet stränguttryck eller -1 om strängen inte hittas. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Returnerar den vänstra delen av en sträng med det angivna antalet tecken. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Returnerar den högra delen av en sträng med det angivna antalet tecken. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Returnerar ett stränguttryck efter att inledande blanksteg har tagits bort. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Returnerar ett stränguttryck efter att efterföljande blanksteg har trunkerats. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Ersätter alla förekomster av ett angivet strängvärde med ett annat strängvärde. |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | Upprepar ett strängvärde ett angivet antal gånger. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Returnerar den omvända ordningen av ett strängvärde. |

Med dessa funktioner kan du köra frågor som följande, vilket returnerar familjen `id` stora bokstäver:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

Resultatet är:

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Eller konkatenera strängar, som i det här exemplet:

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

Resultatet är:

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

Du kan också använda strängfunktioner i WHERE-satsen för att filtrera resultat, som i följande exempel:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

Resultatet är:

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Matrisfunktioner

Följande skalärfunktioner utföra en åtgärd på ett indatavärde för matrisen och returnerar ett numeriskt, booleskt värde eller matrisvärdet. Här är en tabell med inbyggda matrisfunktioner:

| Användning | Beskrivning |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Returnerar antalet element i det angivna matrisuttrycket. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Returnerar en matris som är resultatet av en sammanfogning av två eller fler matrisvärden. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Returnerar ett booleskt värde som anger huruvida matrisen innehåller det angivna värdet. Kan ange om matchningen är fullständig eller partiell. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Returnerar en del av ett matrisuttryck. |

Använda matrisfunktioner för att manipulera matriser i JSON. Här är till exempel en fråga som returnerar alla objekt `id`s där det är en av de `parents` är `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

Resultatet är:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Du kan ange ett partiellt fragment för matchande element i matrisen. Följande fråga söker efter alla objekt `id`er som har `parents` med den `givenName` av `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

Resultatet är:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Ett annat exempel där ARRAY_LENGTH används för att få en `children` per familj:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

Resultatet är:

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

### <a name="spatial-functions"></a>Spatiella funktioner

Cosmos DB stöder följande öppna geospatiala Consortium (OGC) inbyggda funktioner för geospatiala frågor: 

| Användning | Beskrivning |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Returnerar avståndet mellan två GeoJSON `Point`, `Polygon`, eller `LineString` uttryck. |
| T_WITHIN (point_expr, polygon_expr) | Returnerar ett booleskt uttryck som anger om det första GeoJSON-objektet (`Point`, `Polygon`, eller `LineString`) ligger inom det andra GeoJSON-objektet (`Point`, `Polygon`, eller `LineString`). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Returnerar ett booleskt uttryck som anger om två angivna GeoJSON-objekt (`Point`, `Polygon`, eller `LineString`) överlappar varandra. |
| ST_ISVALID | Returnerar ett booleskt värde som anger om den angivna GeoJSON `Point`, `Polygon`, eller `LineString` uttrycket är giltigt. |
| ST_ISVALIDDETAILED | Returnerar ett JSON-värde som innehåller ett booleskt värde om den angivna GeoJSON `Point`, `Polygon`, eller `LineString` uttrycket är giltigt, och om det är ogiltigt, orsak som ett strängvärde. |

Du kan använda spatial funktioner för att utföra närhetsförfrågningar mot spatialdata. Här är till exempel en fråga som returnerar alla family objekt som ligger inom 30 km för en angiven plats med hjälp av den inbyggda funktionen ST_DISTANCE:

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

Resultatet är:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Mer information om geospatialt stöd i Cosmos DB finns i avsnittet om att [arbeta med geospatiala data i Azure Cosmos DB](geospatial.md). 

## <a name="parameterized-queries"></a>Frågor med parametrar

Cosmos DB stöder frågor med parametrar som är uttryckt med bekant @ notation. Parametriserad SQL ger stabil hantering och undantagstecken indata från användaren och förhindrar oavsiktlig exponering av data via SQL-inmatning.

Du kan till exempel skriva en fråga som tar `lastName` och `address.state` som parametrar, och kör det för olika värden på `lastName` och `address.state` baserat på indata från användaren.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Du kan sedan skicka denna begäran till Cosmos DB som en fråga som innehåller JSON som liknar följande:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

I följande exempel anger argumentet ÖVERSTA med en fråga med parametrar: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parametervärden kan vara valfri giltig JSON: strängar, tal, booleska värden, null, och med matriser eller kapslad JSON. Eftersom Cosmos DB är schemalös verifieras parametrar inte mot alla typer.

## <a id="JavaScriptIntegration"></a>JavaScript-integrering

Azure Cosmos DB är en programmeringsmodell för att köra JavaScript-baserade programlogik direkt i behållare, med hjälp av lagrade procedurer och utlösare. Den här modellen har stöd för:

* Högpresterande transaktionella CRUD-åtgärder och frågor mot objekt i en behållare, tack vare den djupgående integrationen av JavaScript-körning inom databasmotorn.
* En naturlig modellering av Kontrollflöde, variabel omfång och tilldelning och integrering av hantering primitiver med databastransaktioner. 

Mer information om Azure Cosmos DB JavaScript-integrering finns i den [JavaScript API för serversidan](#JavaScriptServerSideApi) avsnittet.

### <a name="operator-evaluation"></a>Operatorutvärdering

Cosmos DB, som en JSON-databas ritar parallels med JavaScript-operatorer och utvärderingen semantik. Cosmos DB försöker bevara JavaScript-semantik i JSON-stöd, men åtgärden utvärderingen avviker i vissa fall.

I SQL-API, är till skillnad från traditionella SQL typer av värden ofta inte kända tills API: et hämtar värdena från databasen. För att effektivt köra frågor har de flesta av operatorerna strikta typkrav.

Till skillnad från JavaScript utföra inte SQL-API: T implicit konvertering. Exempelvis kan en fråga som `SELECT * FROM Person p WHERE p.Age = 21` matchar objekt som innehåller en `Age` egenskap vars värde är `21`. Det matchar inte några andra objekt vars `Age` -egenskap stämmer eventuellt oändlig varianter som `twenty-one`, `021`, eller `21.0`. Detta kan jämföras med JavaScript, där strängvärden implicit konvertera till tal baserat på operator, till exempel: `==`. Det här beteendet för SQL-API är avgörande för matchning av effektiv index.

## <a id="ExecutingSqlQueries"></a>SQL-frågekörning

Alla språk som stöder HTTP/HTTPS-förfrågningar kan anropa REST-API i Cosmos DB. Cosmos DB erbjuder också programmeringsbibliotek för .NET, Node.js, JavaScript och Python programmeringsspråk. REST API och bibliotek alla stöd för frågor via SQL och .NET SDK stöder också [LINQ-frågor](#Linq).

I följande exempel visas hur du skapar en fråga och skickar den mot ett Cosmos DB-databaskonto.

### <a id="RestAPI"></a>REST API

Cosmos DB erbjuder en öppen RESTful-programmeringsmodell via HTTP. Vilken resursmodell som består av en uppsättning resurser under ett databaskonto som etablerar en Azure-prenumeration. Kontot som består av en uppsättning *databaser*, som kan innehålla flera *behållare*, vilket i sin tur innehåller *objekt*, användardefinierade funktioner och andra typer av resurser. Varje Cosmos DB-resurs är adresserbara via en logisk och stabil URI. En uppsättning resurser kallas en *feed*. 

Grundläggande interaction modellen med dessa resurser är via HTTP-verb `GET`, `PUT`, `POST`, och `DELETE`, med sina standard tolkningar. Använd `POST` för att skapa en ny resurs, kör en lagrad procedur eller utfärda en Cosmos DB-fråga. Frågor är alltid skrivskyddade åtgärder utan bieffekter.

Följande exempel visar en `POST` för en SQL API-frågor mot en exempelobjekt. Frågan har en enkel filter på JSON- `name` egenskapen. Den `x-ms-documentdb-isquery` och Content-Type: `application/query+json` rubriker anger att åtgärden är en fråga. Ersätt `mysqlapicosmosdb.documents.azure.com:443` med URI: N för Cosmos DB-kontot.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Resultatet är:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

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
                "city":"Seattle"
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

Nästa och mer komplexa frågan returnerar flera resultat från en koppling:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
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
```

Resultatet är: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

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

Om en frågas resultat inte får plats på en enda sida, REST-API: et returnerar ett fortsättningstoken via den `x-ms-continuation-token` svarshuvudet. Klienter kan sidbryta resultat genom att inkludera rubriken i efterföljande resultaten. Du kan också styra hur många resultat per sida via den `x-ms-max-item-count` nummer rubrik. 

Om en fråga har en Aggregeringsfunktion som antal, kan sidan returnera en delvis aggregerat värde endast en resultatsida. Klienter måste utföra en andra nivån aggregering över dessa resultat för att skapa de slutliga resultaten. Till exempel summera över antal returneras i de enskilda sidorna att returnera det totala antalet.

Hantera Datapolicy för konsekvens för frågor med den `x-ms-consistency-level` rubrik som i alla REST API-begäranden. Sessionskonsekvens kräver också eko senast `x-ms-session-token` cookiehuvud i query-fråga. Den befrågade containerns indexeringsprincip kan också påverka konsekvensen för frågeresultatet. Med standardvärdet indexering principinställningar för behållare, indexet är alltid uppdaterad med objekt-innehållet och frågeresultat matcha konsekvens som valts för data. Mer information finns i [Azure Cosmos DB-konsekvensnivåer][consistency-levels].

Om den konfigurera indexprincip för behållaren inte stöder den angivna frågan, returnerar 400 ”Felaktig begäran” i Azure Cosmos DB-servern. Det här felmeddelandet returnerar för frågor med sökvägar som uttryckligen är undantagen från indexering. Du kan ange den `x-ms-documentdb-query-enable-scan` rubrik så att frågan för att utföra en genomsökning när ett index är inte tillgänglig.

Du kan få detaljerade mätvärden om frågekörning genom att ange den `x-ms-documentdb-populatequerymetrics` sidhuvud till `true`. Mer information finns i avsnittet om [SQL-frågemått för Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET SDK)

.NET SDK stöder både LINQ- och SQL-frågor. I följande exempel visas hur du utför den föregående filtreringsfrågan med .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

I följande exempel Jämför två egenskaper sinsemellan i varje objekt och använder anonym projektioner.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

I nästa exempel visas kopplingar, uttryckt genom LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET-klienten automatiskt går igenom alla sidor i frågeresultaten i den `foreach` blockerar, som visas i föregående exempel. Frågealternativ introducerades i den [REST API](#RestAPI) avsnittet är också tillgängliga i .NET-SDK med hjälp av den `FeedOptions` och `FeedResponse` klasser i den `CreateDocumentQuery` metoden. Du kan styra hur många sidor med hjälp av den `MaxItemCount` inställningen.

Du kan också uttryckligen styra sidindelning genom att skapa `IDocumentQueryable` med hjälp av den `IQueryable` objekt sedan genom att läsa den `ResponseContinuationToken` värden och skicka dem tillbaka som `RequestContinuationToken` i `FeedOptions`. Du kan ange `EnableScanInQuery` aktivera sökningar när frågan inte stöds av den konfigurera indexprincip. Du kan använda för partitionerad behållare `PartitionKey` att köra frågan mot en enskild partition, även om Azure Cosmos DB automatiskt extrahera detta från frågetexten. Du kan använda `EnableCrossPartitionQuery` att köra frågor mot flera partitioner.

Mer .NET-exempel med frågor finns i [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet) i GitHub.

### <a id="JavaScriptServerSideApi"></a>JavaScript-API på serversidan

Cosmos DB är en programmeringsmodell för att köra JavaScript-baserade programlogik direkt i behållare, med hjälp av lagrade procedurer och utlösare. JavaScript-logik som registrerats på behållarenivån kan sedan utfärda databasåtgärder i objekt av den angivna behållaren, och är inneslutna i omgivande ACID-transaktioner.

I följande exempel visas hur du använder `queryDocuments` i JavaScript-server API att göra förfrågningar från insidan lagrade procedurer och utlösare:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>LINQ to SQL API

LINQ är en .NET-programmeringsmodell som uttrycker beräkning som frågor för objekt-strömmar. Cosmos DB innehåller ett bibliotek på klientsidan för att samverka med LINQ genom att underlätta konvertering mellan JSON- och .NET-objekt och mappning från en delmängd av LINQ-frågor till Cosmos DB-frågor.

Följande diagram visar arkitekturen för stöd för LINQ-frågor med Cosmos DB. Med Cosmos DB-klienten kan du skapa en `IQueryable` objekt som direkt frågar Cosmos DB-frågeprovider och omvandlar LINQ-frågan till en Cosmos DB-fråga. Du kan sedan skicka frågan till Cosmos DB-server, som hämtar en uppsättning resultat i JSON-format. JSON-deserialiserare konverterar resultatet till en dataström med .NET-objekt på klientsidan.

![Arkitektur för att stödja LINQ-frågor med hjälp av SQL API – SQL-syntax, JSON-frågespråk, databasbegrepp och SQL-frågor][1]

### <a name="net-and-json-mapping"></a>.NET- och JSON-mappning

Mappningen mellan .NET-objekt och JSON-objekt är naturlig. Varje medlemsfält som mappar till ett JSON-objekt, där fältnamnet mappar till den *nyckel* en del av objektet och värde rekursivt mappar till den *värdet* en del av objektet. I följande kod maps den `Family` undergrupp till en JSON-objektet och skapar sedan en `Family` objekt:

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

Exemplet ovan skapar följande JSON-objekt:

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

### <a name="linq-to-sql-translation"></a>LINQ to SQL-översättning

Cosmos DB-frågeprovidern utför en mappning enligt bästa förmåga från en LINQ-fråga till en Cosmos DB SQL-fråga. Följande beskrivning förutsätter en grundläggande kunskaper om LINQ.

Typsystemet fråga providern stöder endast JSON primitiva typer: numeriska och booleskt, sträng, och null. 

Fråga providern har stöd för följande skalära uttryck:

- Konstanta värden, inklusive konstanta värden för de primitiva datatyperna vid frågetiden för utvärdering.
  
- Egenskapen/matris indexuttryck som refererar till egenskapen för ett objekt eller ett matriselement. Exempel:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetiska uttryck, inklusive vanliga matematiska uttryck på numeriska och booleska värden. Fullständig lista finns i den [Azure Cosmos DB SQL-specifikationen](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Sträng jämförelseuttryck bland annat jämför ett strängvärde till vissa konstant strängvärde.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objektmatris/skapande uttryck som returnerar ett objekt av typ sammansatt värde eller anonym typ eller en matris med sådana objekt. Du kan kapsla dessa värden.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>LINQ-operatorer som stöds

LINQ-provider som ingår i SQL .NET SDK stöder följande operatorer:

- **Select**: Projektioner översätts till SQL SELECT, inklusive objektkonstruktion.
- **Where**: Filter översätter till SQL WHERE och stöd mellan `&&`, `||`, och `!` till SQL-operatorer
- **SelectMany**: Tillåter uppspolning av matriser till SQL JOIN-satsen. Använd för att länka eller kapsla uttryck för att filtrera på matriselement.
- **OrderBy** och **OrderByDescending**: Omvandla till ORDER BY-med ASC eller DESC.
- Operatorerna **Count**, **Sum**, **Min**, **Max** och **Average** för sammansättning och deras async-motsvarigheter **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** och **AverageAsync**.
- **CompareTo**: Översätts till intervalljämförelser. Används vanligtvis för strängar, eftersom de inte är jämförbar i .NET.
- **Take**: Översätts till SQL längst upp för att begränsa resultaten av en fråga.
- **Matematikfunktioner**: Har stöd för översättning från .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, och `Truncate` till motsvarande SQL inbyggda funktioner.
- **Sträng funktioner**: Har stöd för översättning från .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, och `TrimStart` till motsvarande SQL inbyggda funktioner.
- **Matrisen funktioner**: Har stöd för översättning från .NET `Concat`, `Contains`, och `Count` till motsvarande SQL inbyggda funktioner.
- **Tillägg för geospatiala funktioner**: Har stöd för översättning av stub-metoder `Distance`, `IsValid`, `IsValidDetailed`, och `Within` till motsvarande SQL inbyggda funktioner.
- **En användardefinierad funktion tilläggsfunktion**: Har stöd för översättning från metoden stub- `UserDefinedFunctionProvider.Invoke` till motsvarande användardefinierade funktionen.
- **Miscellaneous** (Övrigt): Har stöd för översättning av `Coalesce` och villkorlig operatörer. Kan översätta `Contains` strängen innehåller, ARRAY_CONTAINS eller IN-SQL, beroende på kontext.

### <a name="sql-query-operators"></a>SQL-frågeoperatorer

I följande exempel visas hur några av standard LINQ-frågeoperatorer översätta på Cosmos DB-frågor.

#### <a name="select-operator"></a>Välj operator

Syntaxen är `input.Select(x => f(x))`, där `f` är ett skalärt uttryck.

**Välj operator, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Välj operator, exempel 2:** 

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Välj operator, exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>Operatorn SelectMany

Syntaxen är `input.SelectMany(x => f(x))`, där `f` är ett skalärt uttryck som returnerar en containertyp.

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Operatorn Where

Syntaxen är `input.Where(x => f(x))`, där `f` är ett skalärt uttryck som returnerar ett booleskt värde.

**Där operatör, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Där operatör, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>Sammansatta SQL-frågor

Du kan skapa föregående operatorer för att skapa mer kraftfulla frågor. Eftersom Cosmos DB stöder kapslade behållare, kan du sammanfoga eller kapsla sammansättning.

#### <a name="concatenation"></a>Sammanfogning

Syntaxen är `input(.|.SelectMany())(.Select()|.Where())*`. En sammansatt fråga kan börja med en valfri `SelectMany` fråga, följt av flera `Select` eller `Where` operatörer.

**Sammanfoga, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Sammanfoga exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Sammanfoga exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Sammanfoga exempel 4:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>Kapsling

Syntaxen är `input.SelectMany(x=>x.Q())` där `Q` är en `Select`, `SelectMany`, eller `Where` operator.

En kapslad fråga gäller den inre frågan för varje element i behållaren yttre. En viktig funktion är att den inre frågan kan referera till fält i element i behållaren yttre som en självkoppling.

**Kapsling, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Kapsling, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Kapsling, exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>Referenser

- [Azure Cosmos DB SQL-specifikation](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [JavaScript-specifikation](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Fråga efter utvärderingen tekniker för stora databaser](https://dl.acm.org/citation.cfm?id=152611). *ACM databehandling undersökningar* 25, inga. 2 (1993).
- Graefe, G. ”Kaskadspridas ramverket för Frågeoptimeringen”. *IEEE Data Eng. Bull.* 18, inga. 3 (1995).
- Lu, Ooi, Tan. ”Frågebearbetning i parallella relationsdatabassystem”. *IEEE datorn Society genom att trycka på* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar och Andrew Tomkins. ”Pig Latin: En inte så främmande språk för databearbetning ”. *SIGMOD* (2008).

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB][introduction]
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB-konsekvensnivåer][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
