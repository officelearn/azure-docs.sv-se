---
title: SQL-frågor för Azure Cosmos DB | Microsoft Docs
description: Läs mer om SQL-syntax, databasbegrepp och SQL-frågor för Azure Cosmos DB. SQL kan användas som JSON-frågespråk i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/15/2018
ms.author: mjbrown
ms.openlocfilehash: f9ec8ff1fbd5f6341d2d949d15d963f8abe15200
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52166622"
---
# <a name="query-azure-cosmos-db-data-with-sql-queries"></a>Köra frågor mor Azure Cosmos DB-data med SQL-frågor

Azure Cosmos DB stöder frågekörning mot objekt med hjälp av SQL (Structured Query Language) som JSON-frågespråk på SQL API-konton. Vid utformningen av frågespråket för Azure Cosmos DB beaktas följande två mål:

* I stället för att uppfinna ett nytt frågespråk skapade vi Azure Cosmos DB för att stödja SQL, ett av de mest använda och populära frågespråken. Azure Cosmos DB SQL erbjuder en formell programmeringsmodell för komplexa frågor via JSON-objekt.  

* Azure Cosmos DB använder JavaScript-programmeringsmodellen som grund för frågespråket. SQL API grundas på typsystemet, uttrycksutvärderingen och funktionsanropen i JavaScript. Den här ger i sin tur en naturlig programmeringsmodell för relationella projektioner, hierarkisk navigering mellan JSON-objekt, självkopplingar, spatiella förfrågningar och anrop av användardefinierade funktioner (UDF) helt skrivna i JavaScript, bland andra funktioner.

Den här artikeln vägleder dig igenom några SQL-exempelfrågor med hjälp av enkla JSON-objekt. Mer information om syntaxen för Azure Cosmos DB SQL-språket finns i artikeln [SQL-syntaxreferens](sql-api-sql-query-reference.md).

## <a id="GettingStarted"></a>Kom igång med SQL-kommandon

Vi skapar två enkla JSON-objekt och kör frågor mot dessa data. Överväg två JSON-objekt om familjer, infoga dessa JSON-objekt i en container och kör sedan frågor mot dessa data. Här har vi ett enkelt JSON objekt för familjerna Andersen och Wakefield, föräldrarna, barnen (och deras husdjur), adress och registreringsinformation. Objektet har strängar, tal, booleska värden, matriser och kapslade egenskaper.

**Objekt1**

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

Här är ett andra objekt med en subtil skillnad – `givenName` och `familyName` används i stället för `firstName` och `lastName`.

**Objekt2**

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

Nu provar vi några frågor mot dessa data för att förstå några av de viktigaste aspekterna i Azure Cosmos DB:s SQL-frågespråk.

**Fråga1**: Till exempel returnerar följande fråga de objekt där ID-fältet matchar `AndersenFamily`. Eftersom det är en `SELECT *` är utdata för frågan det fullständiga JSON-objektet. Mer information om syntaxen finns i [SELECT-instruktionen](sql-api-sql-query-reference.md#select-query):

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

**Fråga2:** Nu tar vi fallet där vi behöver formatera om JSON-utdata i en annan form. Den här frågan genererar ett nytt JSON-objekt med två valda fält, Namn och Stad, där adressens stad har samma namn som delstaten. I det här fallet matchar ”NY, NY”.

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

**Fråga3**: Den här frågan returnerar alla förnamn på de barn i familjen vars ID matchar `WakefieldFamily`, sorterade efter deras hemort.

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

Här följer några aspekter av Cosmos DB-frågespråket via de exempel som du har sett hittills:  

* Eftersom SQL API arbetar med JSON-värden hanterar det trädformade entiteter i stället för rader och kolumner. Därför gör språket att du kan referera till noder i trädet vid ett godtyckligt djup, som `Node1.Node2.Node3…..Nodem`, vilket liknar det sätt som relationell SQL refererar till referensen med två delar för `<table>.<column>`.

* Det strukturerade frågespråket fungerar med schemalösa data. Därför måste typsystemet vara dynamiskt bundet. Samma uttryck kan ge olika typer på olika objekt. Resultatet av en fråga är ett giltigt JSON-värde men är inte nödvändigtvis av ett fast schema.  

* Azure Cosmos DB stöder endast strikta JSON-objekt. Det innebär att typsystemet och uttryck är begränsade till att endast hantera JSON-typer. Mer information finns i [JSON-specifikationen](http://www.json.org/).  

* En Cosmos DB-container är en schemafri samling med JSON-objekt. Relationer i dataentiteter inom och mellan objekt i en container registreras implicit av inneslutning, inte av primärnyckel- och sekundärnyckelrelationer. Det här är en viktig aspekt med tanke på de intraobjektkopplingar som tas upp senare i den här artikeln.

## <a id="SelectClause"></a>Select-sats

Varje fråga består av en SELECT-sats och valfria FROM- och WHERE-satser enligt ANSI-SQL-standarderna. Vanligtvis räknas källan i FROM-satsen upp för varje fråga. Sedan tillämpas filtret i WHERE-satsen på källan för att hämta en delmängd av JSON-objekt. Slutligen används SELECT-satsen för att beräkna de begärda JSON-värdena i select-listan. Läs om syntaxen i [SELECT-syntax](sql-api-sql-query-reference.md#bk_select_query).

I följande exempel visas en typisk SELECT-fråga.

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

I följande exempel projicerar vi två kapslade egenskaper, `f.address.state` och `f.address.city`.

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

Projektion stöder även JSON-uttryck, som det visas i följande exempel:

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

Vi tittar på rollen för `$1` här. Satsen `SELECT` behövs för att skapa ett JSON-objekt, och eftersom ingen nyckel har angetts använder vi implicita argumentvariabelnamn som börjar med `$1`. Till exempel returnerar den här frågan två implicita argumentvariabler märkta `$1` och `$2`.

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

## <a id="FromClause"></a>FROM-sats

FROM-satsen <from_specification> är valfri såvida inte källan filtreras eller projiceras senare i frågan. Läs om syntaxen i [FROM-syntax](sql-api-sql-query-reference.md#bk_from_clause). En fråga som `SELECT * FROM Families` anger att hela Families-containern är den källa över vilken det ska räknas upp. En särskild identifierar-ROOT kan användas för att representera containern i stället för containerns namn används.
Följande lista innehåller de regler som tillämpas per fråga:

* Containern kan vara ett alias, till exempel `SELECT f.id FROM Families AS f` eller bara `SELECT f.id FROM Families f`. Här motsvarar `f` `Families`. `AS` är ett valfritt nyckelord för ge identifieraren ett alias.  

* När alias har getts kan originalkällan inte bindas. Till exempel är `SELECT Families.id FROM Families f` syntaktiskt felaktigt eftersom identifieraren ”Families” inte kan lösas längre.  

* Alla egenskaper som behöver refereras måste vara fullständigt kvalificerade. Om det inte finns strikt schemaöverensstämmelse tillämpas detta så att tvetydiga bindningar undviks. Därför är `SELECT id FROM Families f` syntaktiskt felaktigt eftersom egenskapen `id` inte är bunden.

### <a name="get-subitems-using-from-clause"></a>Hämta underobjekt med hjälp av FROM-satsen

Källan kan även reduceras till en mindre delmängd. Till exempel skulle underroten kunna bli källan för att endast räkna upp ett underträd i varje objekt, som följande exempel illustrerar:

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

I exemplet ovan användes en matris som källa, men det skulle även gå att använda ett objekt som källa. Detta visas i följande exempel: alla giltiga JSON-värden (ej odefinierat) som kan hittas i källan kan ingå i frågans resultat. Om några familjer inte har ett `address.state`-värde ingår de inte i frågeresultatet.

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

## <a id="WhereClause"></a>WHERE-sats

WHERE-satsen (**`WHERE <filter_condition>`**) är valfri. Den anger de villkor som de JSON-objekt som tillhandahålls av källan måste uppfylla för att inkluderas i resultatet. JSON-objekt måste utvärdera de angivna villkoren till ”true” (sant) för att kunna inkluderas i resultatet. WHERE-satsen används av indexlagret för att avgöra den absoluta minsta delmängden av källobjekt som kan ingå i resultatet. Läs om syntaxen i [WHERE-syntax](sql-api-sql-query-reference.md#bk_where_clause).

Följande fråga begär objekt som innehåller en namnegenskapen vars värde är `AndersenFamily`. Andra objekt som inte har en namnegenskap, eller där värdet inte motsvarar `AndersenFamily`, utesluts.

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

I föregående exempel visades en enkel likhetsfråga. SQL API stöder även en mängd olika skalära uttryck. De mest använda är binära och unära uttryck. Egenskapsreferenser från JSON-källobjektet är också giltiga uttryck.

De följande binära operatorerna stöds för närvarande och kan användas i frågor som visas i följande exempel:  

|**Operatortyp**  | **Värden** |
|---------|---------|
|Aritmetiska | +,-,*,/,% |
|Binära    | \|, &, ^, <<, >>, >>> (högerskiftning med nollfyllning) |
|Logiska    | AND, OR, NOT (och, eller, inte)      |
|Jämförelse | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Sträng     |  \|\| (sammanfoga) |

Vi tar en titt på några frågor som använder binära operatorer.

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

De unära operatorerna +,-, ~, och NOT stöds också och kan användas i frågor som i följande exempel:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Utöver binära och unära operatorer tillåts även egenskapsreferenser. Till exempel returnerar `SELECT * FROM Families f WHERE f.isRegistered` det JSON-objekt som innehåller egenskapen `isRegistered`, där egenskapens värde är lika med JSON `true`-värdet. Alla övriga värden (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>` osv.) leder till att källobjektet exkluderas från resultatet. 

### <a name="equality-and-comparison-operators"></a>Likhets- och jämförelseoperatorer
I följande tabell visas resultatet av likhetsjämförelser i SQL API mellan två godtyckliga JSON-typer.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** | | **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | | **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined | | **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | | **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | | **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | | **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | | **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

För andra jämförelseoperatorer såsom >, >=, !=, < och <= gäller följande regler:

* Jämförelse mellan typer resulterar i Undefined (odefinierad).  
* Jämförelse mellan två objekt eller två matriser resulterar i Undefined.

Om resultatet av det skalära uttrycket i filtret är Undefined inkluderas inte motsvarande objekt i resultatet eftersom Undefined logiskt inte motsvarar ”true” (sant).

## <a name="between-keyword"></a>Nyckelordet BETWEEN (mellan)
Du kan även använda nyckelordet BETWEEN för att uttrycka frågor mot intervall med värden som i ANSI SQL. BETWEEN kan användas mot strängar eller siffror.

Till exempel returnerar den här frågan alla familjeobjekt där det första barnets betyg är mellan 1–5 (båda inkluderande).

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Till skillnad från i ANSI-SQL kan du även använda BETWEEN-satsen i FROM-satsen som i följande exempel.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Den största skillnaden mellan att använda BETWEEN i SQL API och ANSI SQL är att du kan uttrycka intervallfrågor mot egenskaper av blandade typer – du kan till exempel låta ”grade” (betyg) vara ett tal (5) i vissa objekt och strängar i andra (”grade4”). Som i JavaScript resulterar en jämförelse mellan två olika typer i dessa fall i ”undefined” (odefinierad), och objektet hoppas över.

> [!NOTE]
> För att få snabbare frågekörningstider bör du skapa en indexeringsprincip som använder en intervallindextyp mot alla numeriska egenskaper/vägar som filtreras i BETWEEN-satsen.

### <a name="logical-and-or-and-not-operators"></a>Logiska operatorer (AND, OR och NOT) (och, eller samt inte)
Logiska operatorer arbetar med booleska värden. Tabellerna med logisk sanning för de här operatorerna visas i följande tabeller.

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

## <a name="in-keyword"></a>Nyckelordet IN (I)

Nyckelordet IN kan användas för att kontrollera om ett angivet värde matchar något värde i en lista. Till exempel returnerar den här frågan alla familjeobjekt där ID är något av ”WakefieldFamily” eller ”AndersenFamily”.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Det här exemplet returnerar alla objekt där tillståndet är något av de angivna värdena.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="ternary--and-coalesce--operators"></a>Operatorerna Ternary (?) and Coalesce (??)

Operatorerna Ternary och Coalesce kan användas för att skapa villkorsuttryck på ett sätt som liknar populära programmeringsspråk som C# och JavaScript. Operatorn Ternary (?) kan vara praktisk när du skapar nya JSON-egenskaper i farten. Till exempel kan du nu skriva frågor för att klassificera kursnivåerna i ett format som kan läsas av människor, såsom Beginner/Intermediate/Advanced (Nybörjare/Erfaren/Avancerad) nedan.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Du kan även kapsla anropen till operator som i frågan nedan.

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel
    FROM Families.children[0] c
```

Som med andra frågeoperatorer gäller att om de refererade egenskaperna i villkorsuttrycket saknas i något objekt, eller om de typer som jämförs är olika, så exkluderas de objekten i frågeresultatet.

Operatorn Coalesce (??) kan användas för att effektivt söka efter förekomsten av en egenskap (dvs. som är definierad) i ett objekt. Den här operatorn är användbar när du kör frågor mot halvstrukturerade eller data av blandade typer. Till exempel returnerar den här frågan ”lastName” om det finns eller ”surname” om det inte finns.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="EscapingReservedKeywords"></a>Accessor med citatomsluten egenskap
Du kan även komma åt egenskaper med hjälp av operatorn med citatomsluten egenskap `[]`. Till exempel är `SELECT c.grade` och `SELECT c["grade"]` likvärdiga. Den här syntaxen är användbar när du behöver undvika en egenskap som innehåller blanksteg eller specialtecken eller som råkar dela samma namn som ett SQL-nyckelord eller ett reserverat ord.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="aliasing"></a>Alias

Nu utöver vi exemplet ovan med explicita alias för värden. AS är det nyckelord som används för alias. Det är valfritt såsom visas vid projektion av det andra värdet som `NameInfo`.

Om en fråga har två egenskaper med samma namn måste alias användas för att byta namn på en eller båda egenskaper så att de kan skiljas åt i det projicerade resultatet.

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

## <a name="scalar-expressions"></a>Skalära uttryck

Utöver egenskapsreferenser stöder SELECT-satsen även skalära uttryck såsom konstanter, aritmetiska uttryck, logiska uttryck osv. Till exempel är det är en enkel ”Hello World”-fråga.

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

I följande exempel är resultatet av det skalära uttrycket ett booleskt värde.

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

## <a name="object-and-array-creation"></a>Skapande av objekt och matris

En annan viktig funktion i SQL API är skapande av matris/objekt. I det föregående exemplet skapade du ett nytt JSON-objekt. På samma sätt går det även att skapa matriser såsom det visas i följande exempel:

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

## <a id="ValueKeyword"></a>Nyckelordet VALUE

Nyckelordet **VALUE** (Värde) ger ett sätt att returnera JSON-värden. Till exempel returnerar den fråga som visas nedan skalären `"Hello World"` i stället för `{$1: "Hello World"}`.

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

Följande fråga returnerar JSON-värdet utan etiketten `"address"` i resultatet.

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

Följande exempel utökas för att visa hur du returnerar primitiva JSON-värden (lövnivån i JSON-trädet).

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

## <a name="-operator"></a>Operatorn *
Specialoperatorn (*) stöds för projektion av objektet som det är. När den används måste den vara det enda projicerade fältet. En fråga som `SELECT * FROM Families f` är giltig, men `SELECT VALUE * FROM Families f` och `SELECT *, f.id FROM Families f` är ogiltiga.

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

Nyckelordet TOP kan användas för att begränsa antalet värden från en fråga. När TOP används tillsammans med ORDER BY-satsen begränsas resultatmängden till de fösta N sorterade värdena. Annars returnerar det de första N värdena av resultat i en odefinierad ordning. Som bästa praxis bör du alltid använda en ORDER BY-sats med TOP-satsen i en SELECT-instruktion. Att kombinera dessa två satser är det enda sättet att förutsägbart indikera vilka rader som påverkas av TOP. 

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

TOP kan användas med ett konstant värde (som visas ovan) eller med ett variabelt värde använda med hjälp av parametriserade frågor. Mer information finns i de parametriserade frågorna nedan.

## <a id="Aggregates"></a>Mängdfunktioner

Du kan även utföra sammansättningar i satsen `SELECT`. Mängdfunktioner utför en beräkning på en uppsättning värden och returnerar ett enskilt värde. Till exempel returnerar följande fråga antalet familjeobjekt i containern.

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

Du kan även returnera skalärvärdet för samlingen med hjälp av nyckelordet `VALUE`. Till exempel returnerar följande fråga antalet värden som ett enskilt tal:

**Fråga**

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

**Results**

```json
    [ 2 ]
```

Du kan även utföra sammansättningar i kombination med filter. Till exempel returnerar följande fråga antalet objekt med adressen i delstaten Washington.

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

I följande tabell visas listan över mängdfunktioner som stöds i SQL API. `SUM` och `AVG` utförs över numeriska värden medan `COUNT`, `MIN`, och `MAX` kan utföras över tal, strängar, booleska värden och null-värden.

| Användning | Beskrivning |
|-------|-------------|
| COUNT | Returnerar antalet objekt i uttrycket. |
| SUM   | Returnerar summan av alla värden i uttrycket. |
| MIN   | Returnerar minimivärdet i uttrycket. |
| MAX   | Returnerar maxvärdet i uttrycket. |
| AVG   | Returnerar medelvärdet av värdena i uttrycket. |

Samlingar kan även utföras över resultatet av en matrisiteration. Mer information finns i avsnittet om [matrisiteration i frågor](#Iteration).

> [!NOTE]
> När du använder Datautforskaren i Azure-portalen kan mängdfrågor returnera de delvis sammansatta resultaten över en frågesida. SDK:erna skapar ett enskilt ackumulerat värde över alla sidor.
>
> För att kunna utföra mängdfrågor med hjälp av kod behöver du .NET SDK 1.12.0, .NET Core SDK 1.1.0 eller Java SDK 1.9.5 eller senare.
>

## <a id="OrderByClause"></a>ORDER BY-satsen

Som i ANSI-SQL kan du inkludera en valfri Order By-sats vid frågekörning. Satsen kan inkludera ett valfritt ASC/DESC-argument för att ange ordningen i vilken resultat måste hämtas.

Här är till exempel en fråga som hämtar familjer ordnat efter namnet på hemorten.

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

Och här är en fråga som hämtar familjer efter skapandedatum, som lagras som ett tal som representerar epoktiden dvs. förfluten tid sedan den 1 januari 1970 i sekunder.

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

En ny konstruktion lades till via nyckelordet **IN** i SQL API för att tillhandahålla stöd för att iterera över JSON-matriser. FROM-källan ger stöd för iteration. Vi börjar med följande exempel:

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

Nu tittar vi på en annan fråga som utför iteration över barn i containern. Observera skillnaden i utdatamatrisen. Det här exemplet delar upp `children` och plattar ut resultatet till en enskild matris.  

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

Detta kan ytterligare användas för att filtrera på varje enskild post i matrisen såsom visas i följande exempel:

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

Du kan även utföra sammansättning över resultatet av matrisiteration. Till exempel räknar följande fråga antalet barn bland alla serier.

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

I en relationsdatabas är behovet av att koppla över tabeller viktigt. Det är den logiska följden av att utforma normaliserade scheman. Däremot hanterar SQL API den avnormaliserade datamodellen med schemafria objekt, vilket är den logiska motsvarigheten av en ”självkoppling”.

Den syntax som språket stöder är `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Allmänt sett returnerar den här frågan en uppsättning med **N**-tupplar (tuppel med **N** värden). Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar. Med andra ord utför den här frågan en fullständig kryssprodukt av de uppsättningar som deltar i kopplingen.

I följande exempel visas hur JOIN-satsen fungerar. I följande exempel är resultatet tomt eftersom kryssprodukten för varje objekt från källan och en tom uppsättning är tom.

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

I följande exempel är kopplingen mellan objektroten och `children`-underroten. Det är en kryssprodukt mellan två JSON-objekt. Det faktum att underordnade element är en matris gäller inte i JOIN eftersom vi hanterar en enskild rot som är matrisen för underordnade element. Därför innehåller resultatet bara två resultat eftersom kryssprodukten för varje objekt med matrisen ger exakt och endast ett objekt.

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

I följande exempel visas en mer konventionell koppling:

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

Det första du bör observera är att `from_source` för **JOIN**-satsen är en iterator. Därför är flödet i det här fallet följande:  

* Expandera varje underordnat element **c** i matrisen.
* Tillämpa en kryssprodukt med roten för objektet **f** med varje underordnat element **c** som tillplattades i det första steget.
* Slutligen projicerar du namnegenskapen för rotobjektet **f** enskilt.

Det första objektet (`AndersenFamily`) innehåller endast ett underordnat element, så resultatuppsättningen innehåller bara ett enda objekt som motsvarar det här objektet. Det andra objektet (`WakefieldFamily`) innehåller två underordnade element. Därför skapar kryssprodukten ett separat objekt för varje underordnat element, vilket resulterar i två objekt, ett för varje underordnat element som motsvarar det här objektet. Rotfälten i båda dessa objekt är detsamma, vilket är det som förväntas i en kryssprodukt.

Den främsta nyttan med JOIN är att bilda tupplar från kryssprodukten i en form som annars är svår att projicera. Dessutom kan du, som det visas i exemplet nedan, filtrera på kombinationen av en tuppel som gör att användaren kan välja ett villkor som uppfylls av tupplarna överlag.

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

Det här exemplet är en naturlig förlängning av det föregående exemplet och skapar en dubbel koppling. Därför kan kryssprodukten ses som följande pseudokod:

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

`AndersenFamily` har ett barn som har ett husdjur. Därför ger kryssprodukten en rad (1\*1\*1) från den här familjen. WakefieldFamily har dock två barn, men bara ett barn, ”Jesse”, har husdjur. Jesse har dock två husdjur. Därför ger kryssprodukten 1\*1\*2 = 2 rader från den här familjen.

I nästa exempel finns det ett ytterligare filter på `pet`, vilket utesluter alla tupplar där husdjursnamnet inte är ”Shadow”. Observera att vi kan skapa tupplar från matriser, filtrera på valfritt element i tuppeln och projicera valfri kombination av elementen.

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

Azure Cosmos DB är en programmeringsmodell för att köra JavaScript-baserad programlogik direkt på containrarna vad gäller lagrade procedurer och utlösare. Den här metoden stöder:

* Möjligheten att utföra högpresterande transaktionella CRUD-åtgärder och frågor mot objekt i en container via den djupa integreringen av JavaScript-körning direkt i databasmotorn.
* En naturlig modellering av kontrollflöde, variabelomfång och tilldelning och integrering av undantagshanteringsprimitiver med databastransaktioner. Mer information om Azure Cosmos DB-stöd för JavaScript-integrering finns i dokumentationen om programmerbarhet för JavaScript-serversidan.

### <a id="UserDefinedFunctions"></a>Användardefinierade funktioner (UDF:er)

Utöver de typer som redan definierats i den här artikeln ger SQL API stöd för UDF:er (User Defined Functions, användardefinierade funktioner). I synnerhet stöds skalära UDF:er där utvecklarna kan skicka in noll eller flera argument och returnera tillbaka ett enda argumentresultat. Vart och ett av de här argumenten kontrolleras om huruvida de är giltiga JSON-värden.  

SQL-syntaxen utökas så att den stöder anpassad programlogik med hjälp av dessa användardefinierade funktioner. UDF:er kan registreras med SQL API och sedan refereras till som en del av en SQL-fråga. Faktum är att UDF:er har utmärkt utformning för att anropas från frågor. Som en följd av det här valet har UDF:er inte åtkomst till det kontextobjekt som andra JavaScript-typer (lagrade procedurer och utlösare) har. Eftersom frågor körs i skrivskyddat läge kan de köras antingen på primära eller sekundära repliker. Därför är UDF:er utformade för att köras på sekundära repliker till skillnad från andra JavaScript-typer.

Nedan visas ett exempel på hur en UDF kan registreras vid Cosmos DB-databasen, specifikt under en objektcontainer.

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

I föregående exempel skapas en UDF vars namn är `REGEX_MATCH`. Den accepterar två JSON-strängvärden, `input` och `pattern`, och kontrollerar om det första matchar det mönster som anges i det andra med hjälp av JavaScript-funktionen string.match().

Vi kan nu använda den här UDF:en i en fråga i en projektion. UDF:er måste kvalificeras med det skiftlägeskänsliga prefixet ”udf.” när de anropas inifrån frågor.

> [!NOTE]
> Före 2015-03-17 hade Cosmos DB stöd för UDF-anrop utan prefixet ”udf.” såsom SELECT REGEX_MATCH(). Det här anropsmönstret är inaktuellt.  
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

UDF:en kan även användas inuti ett filter såsom det visas i exemplet nedan, som även kvalificeras med ”udf.”- prefixet:

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

I princip är UDF:er giltiga skalära uttryck och kan användas i både projektioner och filter.

För att expandera på kraften hos UDF:er tittar vi på ett annat exempel med villkorslogik:

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
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Nedan visas ett exempel som utövar UDF:en.

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

Som föregående exempel demonstrerar integrerar UDF:er kraften hos JavaScript-språket med SQL API för att tillhandahålla ett omfattande programmerbart gränssnitt för att utföra komplex procedurmässig, villkorsstyrd logik med hjälp av inbyggda JavaScript-körningsfunktioner.

SQL API tillhandahåller argumenten till UDF:erna för varje objekt i källan vid det aktuella steget (WHERE-satsen eller SELECT-satsen) för bearbetning av UDF:en. Resultatet införlivas sömlöst i den övergripande körningspipelinen. Om de egenskaper som refereras till av UDF-parametrarna inte är tillgängliga i JSON-värdet anses parametern vara odefinierad, och därför hoppas UDF-anropet över helt. På samma sätt inkluderas inte resultatet av UDF i resultatet om det är odefinierat.

Sammanfattningsvis är UDF:er utmärkta verktyg för att utföra komplicerad affärslogik som en del av frågan.

### <a name="operator-evaluation"></a>Operatorutvärdering

Eftersom Cosmos DB är en JSON-databas finns det likheter med JavaScript-operatorer och deras utvärderingssemantik. Även om Cosmos DB försöker bevara JavaScript-semantik vad gäller JSON-stöd avviker operatorutvärderingen i vissa instanser.

Till skillnad från traditionellt SQL är typerna av värden i SQL API ofta inte kända förrän värdena hämtas från en databas. För att effektivt köra frågor har de flesta av operatorerna strikta typkrav.

SQL API utför inte implicit konvertering, till skillnad från JavaScript. Exempelvis matchar en fråga som `SELECT * FROM Person p WHERE p.Age = 21` objekt som innehåller en Age-egenskap (ålder) vars värde är 21. Andra objekt vars Age-egenskap matchar strängen ”21” eller andra eventuellt oändliga variationer som ”021”, ”21,0”, ”0021”, ”00021” osv. matchas inte. Detta skiljer sig från JavaScript, där strängvärdena implicit omvandlas till siffror (baserat på operatorn, t.ex.: ==). Det här valet är avgörande för effektiv indexmatchning i SQL API.

## <a name="parameterized-sql-queries"></a>Parametriserade SQL-frågor

Cosmos DB har stöd för frågor med parametrar som uttrycks med den bekanta \@-notationen. Parametriserat SQL ger robust hantering av och undantagstecken för användarindata, vilket förhindrar oavsiktlig exponering av data via SQL-inmatning.

Du kan till exempel skriva en fråga som tar efternamn och adressdelstat som parametrar och sedan köra den för olika värden av efternamn och adressdelstat baserat på användarindata.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Den här begäran kan sedan skickas till Cosmos DB som en parametriserad JSON-fråga såsom det visas nedan.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Argumentet till TOP kan konfigureras att använda parametriserade frågor såsom det visas nedan.

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parametervärden kan vara valfria giltiga JSON (strängar, tal, booleska värden, null och även matriser eller kapslad JSON). Och eftersom Cosmos DB är schemalöst verifieras parametrar inte mot någon typ.

## <a id="BuiltinFunctions"></a>Inbyggda funktioner

Cosmos DB stöder även ett antal inbyggda funktioner för vanliga åtgärder som kan användas inuti frågor som användardefinierade funktioner (UDF:er).

| Funktionsgrupp | Åtgärder |
|---------|----------|
| Matematiska funktioner | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Funktioner för typkontroll | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Strängfunktioner | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Matrisfunktioner | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH och ARRAY_SLICE |
| Spatiella funktioner | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Om du för närvarande använder en användardefinierad funktion (UDF) som en inbyggd funktion nu är tillgänglig för bör du använda motsvarande inbyggda funktion, eftersom den körs snabbare och effektivare.

### <a name="mathematical-functions"></a>Matematiska funktioner

De matematiska funktionerna utför en beräkning baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde. Här är en tabell med inbyggda matematiska funktioner som stöds.

| Användning | Beskrivning |
|----------|--------|
| [[ABS (num_expr)](#bk_abs) | Returnerar det absoluta (positiva) värdet för det angivna numeriska uttrycket. |
| [CEILING (num_expr)](#bk_ceiling) | Returnerar det minsta heltalsvärdet som är större än eller lika med det angivna numeriska uttrycket. |
| [FLOOR (num_expr)](#bk_floor) | Returnerar det största heltalsvärdet som är mindre än eller lika med det angivna numeriska uttrycket. |
| [EXP (num_expr)](#bk_exp) | Returnerar exponenten för det angivna numeriska uttrycket. |
| [LOG (num_expr [,base])](#bk_log) | Returnerar den naturliga logaritmen för det angivna numeriska uttrycket eller den logaritm som använder den angivna basen |
| [LOG10 (num_expr)](#bk_log10) | Returnerar det logaritmiska bas 10-värdet för det angivna numeriska uttrycket. |
| [ROUND (num_expr)](#bk_round) | Returnerar ett numeriskt värde avrundat till närmaste heltal. |
| [TRUNC (num_expr)](#bk_trunc) | Returnerar ett numeriskt värde trunkerat till närmaste heltal. |
| [SQRT (num_expr)](#bk_sqrt) | Returnerar kvadratroten av det angivna numeriska uttrycket. |
| [SQUARE (num_expr)](#bk_square) | Returnerar kvadraten av det angivna numeriska uttrycket. |
| [POWER (num_expr, num_expr)](#bk_power) | Returnerar exponenten för det angivna numeriska uttrycket till det angivna värdet. |
| [SIGN (num_expr)](#bk_sign) | Returnerar teckenvärdet (-1, 0, 1) för det angivna numeriska uttrycket. |
| [ACOS (num_expr)](#bk_acos) | Returnerar vinkeln i radianer vars cosinus är det angivna numeriska uttrycket. Kallas även arccosinus. |
| [ASIN (num_expr)](#bk_asin) | Returnerar vinkeln i radianer vars sinus är det angivna numeriska uttrycket. Den här funktionen kallas även arcsinus. |
| [ATAN (num_expr)](#bk_atan) | Returnerar vinkeln i radianer vars tangent är det angivna numeriska uttrycket. Detta kallas även arctangens. |
| [ATN2 (num_expr)](#bk_atn2) | Returnerar vinkeln i radianer mellan den positiva x- axeln och strålen från origo till punkten (y, x) där x och y är värdena för de två angivna flyttalsuttrycken. |
| [COS (num_expr)](#bk_cos) | Returnerar trigonometrisk cosinus för den angivna vinkeln i radianer i det angivna uttrycket. |
| [COT (num_expr)](#bk_cot) | Returnerar trigonometrisk cotangens för den angivna vinkeln i radianer i det angivna numeriska uttrycket. |
| [DEGREES (num_expr)](#bk_degrees) | Returnerar motsvarande vinkel i grader för en vinkel som anges i radianer. |
| [PI ()](#bk_pi) | Returnerar konstantvärdet för PI. |
| [RADIANS (num_expr)](#bk_radians) | Returnerar radianer när ett numeriskt uttryck i grader anges. |
| [SIN (num_expr)](#bk_sin) | Returnerar trigonometrisk sinus för den angivna vinkeln i radianer i det angivna uttrycket. |
| [TAN (num_expr)](#bk_tan) | Returnerar tangens för indatauttrycket i det angivna uttrycket. |

Till exempel kan du nu köra frågor på det sätt som visas i följande exempel:

**Fråga**

```sql
    SELECT VALUE ABS(-4)
```

**Results**

```json
    [4]
```

Den största skillnaden mellan Cosmos DB-funktioner jämfört med ANSI SQL är att de är utformade för att fungera väl med schemalösa data och data med blandade scheman. Om du till exempel har ett objekt där egenskapen Size (Storlek) saknas, eller har ett icke-numeriskt värde såsom ”unknown” (okänt), hoppas objektet över i stället för att returnera ett fel.

### <a name="type-checking-functions"></a>Funktioner för typkontroll

Med funktionerna för typkontroll kan du kontrollera typen av ett uttryck i SQL-frågor. Funktionerna för typkontroll kan användas för att bestämma typen av egenskaper i objekt i farten när de är variabler eller okända. Här är en tabell med inbyggda funktioner för typkontroll som stöds.

| **Användning** | **Beskrivning** |
|-----------|------------|
| [IS_ARRAY (expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array) | Returnerar ett booleskt värde som anger huruvida värdets typ är en matris. |
| [IS_BOOL (expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool) | Returnerar ett booleskt värde som anger huruvida värdets typ är ett booleskt värde. |
| [IS_NULL (expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null) | Returnerar ett booleskt värde som anger huruvida värdets typ är null. |
| [IS_NUMBER (expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number) | Returnerar ett booleskt värde som anger huruvida värdets typ är ett tal. |
| [IS_OBJECT (expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object) | Returnerar ett booleskt värde som anger huruvida värdets typ är ett JSON-objekt. |
| [IS_STRING (expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string) | Returnerar ett booleskt värde som anger huruvida värdets typ är en sträng. |
| [IS_DEFINED (expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined) | Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde. |
| [IS_PRIMITIVE (expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive) | Returnerar ett booleskt värde som anger huruvida värdets typ är en sträng, ett tal, ett booleskt värde eller null. |

Med hjälp av de här funktionerna kan du nu köra frågor på det sätt som visas i följande exempel:

**Fråga**

```sql
    SELECT VALUE IS_NUMBER(-4)
```

**Results**

```json
    [true]
```

### <a name="string-functions"></a>Strängfunktioner

Följande skalärfunktioner utför en åtgärd på ett strängindatavärde och returnerar en sträng, ett numeriskt värde eller ett booleskt värde. Här är en tabell med inbyggda strängfunktioner:

| Användning | Beskrivning |
| --- | --- |
| [LENGTH (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) | Returnerar antalet tecken i det angivna stränguttrycket |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) | Returnerar en sträng som är resultatet av en sammanfogning av två eller fler strängvärden. |
| [SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) | Returnerar en del av ett stränguttryck. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) | Returnerar ett booleskt värde som anger huruvida det första stränguttrycket börjar med det andra |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) | Returnerar ett booleskt värde som anger huruvida det första stränguttrycket slutar med det andra |
| [CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) | Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) | Returnerar startpositionen för den första förekomsten av det andra stränguttrycket i det första angivna stränguttrycket eller -1 om strängen inte hittas. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) | Returnerar den vänstra delen av en sträng med det angivna antalet tecken. |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) | Returnerar den högra delen av en sträng med det angivna antalet tecken. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) | Returnerar ett stränguttryck efter att inledande blanksteg har tagits bort. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) | Returnerar ett stränguttryck efter att efterföljande blanksteg har trunkerats. |
| [LOWER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) | Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener. |
| [UPPER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) | Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler. |
| [REPLACE (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) | Ersätter alla förekomster av ett angivet strängvärde med ett annat strängvärde. |
| [REPLICATE (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) | Upprepar ett strängvärde ett angivet antal gånger. |
| [REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) | Returnerar den omvända ordningen av ett strängvärde. |

Med hjälp av de här funktionerna kan du nu köra frågor på följande sätt. Till exempel kan du returnera efternamnet i versaler på följande sätt:

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

Eller sammanfoga strängar som i det här exemplet:

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

Strängfunktioner kan även användas i WHERE-satsen för att filtrera resultat som i följande exempel:

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

Följande skalärfunktioner utför en åtgärd på ett matrisindatavärde och returnerar ett numeriskt värde, ett booleskt värde eller ett matrisvärde. Här är en tabell med inbyggda matrisfunktioner:

| Användning | Beskrivning |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Returnerar antalet element i det angivna matrisuttrycket. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Returnerar en matris som är resultatet av en sammanfogning av två eller fler matrisvärden. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Returnerar ett booleskt värde som anger huruvida matrisen innehåller det angivna värdet. Kan ange om matchningen är fullständig eller partiell. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Returnerar en del av ett matrisuttryck. |

Matrisfunktioner kan användas för att manipulera matriser i JSON. Här är till exempel en fråga som returnerar alla objekt där en av föräldrarna är ”Robin Wakefield”. 

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

Du kan ange ett partiellt fragment för matchande element i matrisen. Följande fråga hittar alla överordnade element med `givenName` `Robin`.

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

Här är ett annat exempel där ARRAY_LENGTH används för att hämta antalet barn per familj.

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

### <a name="spatial-functions"></a>Spatiella funktioner

Cosmos DB stöder följande inbyggda OGC-funktioner (Open Geospatial Consortium) för geospatial frågekörning. 

| Användning | Beskrivning |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Returnerar avståndet mellan de två GeoJSON Point-, Polygon- eller LineString-uttrycken. |
| T_WITHIN (point_expr, polygon_expr) | Returnerar ett booleskt uttryck som anger huruvida det första GeoJSON-objektet (Point, Polygon eller LineString) finns i det andra GeoJSON-objektet (Point, Polygon eller LineString). |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Returnerar ett booleskt uttryck som anger huruvida de två angivna GeoJSON-objekten (Point, Polygon eller LineString) överlappar varandra. |
| ST_ISVALID | Returnerar ett booleskt värde som anger huruvida det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt. |
| ST_ISVALIDDETAILED | Returnerar ett JSON-värde som innehåller ett booleskt värde om det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt, och orsaken som ett strängvärde om det är ogiltigt. |

Spatiella funktioner kan användas för att köra närhetsfrågor mot rumsliga data. Här är till exempel en fråga som returnerar alla familjeobjekt som ligger inom 30 km från den angivna platsen med hjälp av den inbyggda funktionen ST_DISTANCE.

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

Mer information om geospatialt stöd i Cosmos DB finns i avsnittet om att [arbeta med geospatiala data i Azure Cosmos DB](geospatial.md). Då är vi klara med spatiella funktioner och SQL-syntax för Cosmos DB. Nu tittar vi på hur LINQ-frågekörning fungerar och hur det interagerar med den syntax som vi har sett hittills.

## <a id="Linq"></a>LINQ to SQL API

LINQ är en .NET-programmeringsmodell som uttrycker beräkning som frågor på strömmar av objekt. Cosmos DB innehåller ett bibliotek på klientsidan för att samverka med LINQ genom att underlätta konvertering mellan JSON- och .NET-objekt och mappning från en delmängd av LINQ-frågor till Cosmos DB-frågor.

Bilden nedan visar arkitekturen för att stödja LINQ-frågor med hjälp av Cosmos DB.  Med hjälp av Cosmos DB-klienten kan utvecklare skapa ett **IQueryable**-objekt som direkt kör frågor mot Cosmos DB-frågeprovidern, som sedan översätter LINQ-frågan till en Cosmos DB-fråga. Frågan skickas sedan till Cosmos DB-servern för att hämta en uppsättning resultat i JSON-format. Det returnerade resultatet deserialiseras till en ström av .NET-objekt på klientsidan.

![Arkitektur för att stödja LINQ-frågor med hjälp av SQL API – SQL-syntax, JSON-frågespråk, databasbegrepp och SQL-frågor][1]

### <a name="net-and-json-mapping"></a>.NET- och JSON-mappning

Mappningen mellan .NET-objekt och JSON-objekt är naturlig – varje datamedlemsfält mappas till ett JSON-objekt, där fältnamnet mappas till ”nyckeldelen” i objektet och ”värdedelen” rekursivt mappas till värdedelen i objektet. Ta följande som exempel: familjeobjektet mappas till JSON-objektet enligt bilden nedan. Och omvänt mappas JSON-objektet tillbaka till ett .NET-objekt.

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


### <a name="linq-to-sql-translation"></a>LINQ to SQL-översättning

Cosmos DB-frågeprovidern utför en mappning enligt bästa förmåga från en LINQ-fråga till en Cosmos DB SQL-fråga. I följande beskrivning förutsätter vi att läsaren har grundläggande kunskaper om LINQ.

Till att börja med stöder vi för typsystemet alla primitive JSON-typer – numeriska typer, booleskt värde, sträng och null. Endast dessa JSON-typer stöds. Följande skalära uttryck stöds.

* Konstanta värden – däribland konstanta värden för de primitiva datatyperna vid den tidpunkt då frågan utvärderas.
* Egenskaps-/matrisindexuttryck – dessa uttryck syftar till egenskapen för ett objekt eller ett matriselement.
  
     family.Id;    family.children[0].familyName;    family.children[0].grade;    family.children[n].grade; //n är en int-variabel
* Aritmetiska uttryck – däribland vanliga aritmetiska uttryck på numeriska och booleska värden. En fullständig lista finns i SQL-specifikationen.
  
     2 * family.children[0].grade;    x + y;
* Strängjämförelseuttryck – däribland jämförelse av ett strängvärde med ett visst konstant strängvärde.  
  
     mother.familyName == "Smith";    child.givenName == s; //s är en strängvariabel
* Uttryck för skapande av objekt/matris – dessa uttryck returnerar ett objekt med sammansatt värdetyp eller anonym typ eller en matris med sådana objekt. Dessa värden kan kapslas.
  
     new Parent { familyName = "Smith", givenName = "Joe" }; new { first = 1, second = 2 }; //en anonym typ med två fält              
     new int[] { 3, child.grade, 5 };

### <a id="SupportedLinqOperators"></a>Lista över LINQ-operatorer som stöds

Här är en lista över LINQ-operatorer som stöds i den LINQ-provider som ingår i SQL .NET SDK.

* **Select**: Projektioner översätts till SQL SELECT inklusive objektkonstruktion
* **Where**: Filter översätter till SQL WHERE och stöder översättning mellan && , || och ! till SQL-operatorerna
* **SelectMany**: Tillåter uppspolning av matriser till SQL JOIN-satsen. Kan användas för att kedja/kapsla uttryck för att filtrera på matriselement
* **OrderBy och OrderByDescending**: Översätts till ORDER BY (Sortera efter) stigande/fallande
* Operatorerna **Count**, **Sum**, **Min**, **Max** och **Average** för sammansättning och deras async-motsvarigheter **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** och **AverageAsync**.
* **CompareTo**: Översätts till intervalljämförelser. Används ofta för strängar eftersom de inte är jämförbara i .NET
* **Take**: Översätts till SQL TOP för att begränsa resultat av en fråga
* **Math Functions** (Matematikfunktioner): Stöder översättning från .NET:s Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan och Truncate till motsvarande inbyggda SQL-funktioner.
* **String Functions** (Strängfunktioner): Stöder översättning från .NET:s Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString och ToUpper till motsvarande inbyggda SQL-funktioner.
* **Array Functions** (Matrisfunktioner): Stöder översättning från .NET:s Concat, Contains och Count till motsvarande inbyggda SQL-funktioner.
* **Geospatial Extension Functions** (Geospatiala tilläggsfunktioner): Stöder översättning från stub-metoderna Distance, Within, IsValid och IsValidDetailed till motsvarande inbyggda SQL-funktioner.
* **User-Defined Function Extension Function** (Användardefinierad tilläggsfunktion): Stöder översättning från stub-metoden UserDefinedFunctionProvider.Invoke till motsvarande inbyggda SQL-funktion.
* **Miscellaneous** (Övrigt): Stöder översättning av Coalesce och de villkorliga operatorerna. Kan översätta Contains till String CONTAINS, ARRAY_CONTAINS eller SQL IN beroende på kontext.

### <a name="sql-query-operators"></a>SQL-frågeoperatorer

Här följer några exempel som illustrerar hur några av LINQ-standardfrågeoperatorerna översätts ned till Cosmos DB-frågor.

#### <a name="select-operator"></a>Operatorn Select

Syntaxen är `input.Select(x => f(x))`, där `f` är ett skalärt uttryck.

**LINQ-lambdauttryck**

```csharp
    input.Select(family => family.parents[0].familyName);
```

**SQL** 

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
```

**LINQ-lambdauttryck**

```csharp
    input.Select(family => family.children[0].grade + c); // c is an int variable
```

**SQL**

```sql
    SELECT VALUE f.children[0].grade + c
    FROM Families f
```

**LINQ-lambdauttryck**

```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
```

**SQL** 

```sql
    SELECT VALUE {"name":f.children[0].familyName,
                  "grade": f.children[0].grade + 3 }
    FROM Families f
```


#### <a name="selectmany-operator"></a>Operatorn SelectMany

Syntaxen är `input.SelectMany(x => f(x))`, där `f` är ett skalärt uttryck som returnerar en containertyp.

**LINQ-lambdauttryck**

```csharp
    input.SelectMany(family => family.children);
```

**SQL**

```sql
    SELECT VALUE child
    FROM child IN Families.children
```

#### <a name="where-operator"></a>Operatorn Where

Syntaxen är `input.Where(x => f(x))`, där `f` är ett skalärt uttryck som returnerar ett booleskt värde.

**LINQ-lambdauttryck**

```csharp
    input.Where(family=> family.parents[0].familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**LINQ-lambdauttryck**

```csharp
    input.Where(
        family => family.parents[0].familyName == "Smith" &&
        family.children[0].grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3
```

### <a name="composite-sql-queries"></a>Sammansatta SQL-frågor

Ovanstående operatorer kan sammansättas för att bilda kraftfullare frågor. Eftersom Cosmos DB stöder kapslade containrar kan sammansättningen antingen sammanfogas eller kapslas.

#### <a name="concatenation"></a>Sammanfogning

Syntaxen är `input(.|.SelectMany())(.Select()|.Where())*`. En sammanfogad fråga kan börja med en valfri `SelectMany`-fråga följt av flera `Select`- eller `Where`- operatorer.

**LINQ-lambdauttryck**

```csharp
    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
```

**LINQ-lambdauttryck**

```csharp
    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);
```

**SQL**

```sql
    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3
```

**LINQ-lambdauttryck**

```csharp
    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
```

**SQL**

```sql
    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)
```

**LINQ-lambdauttryck**

```csharp
    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");
```

**SQL**

```sql
    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"
```

#### <a name="nesting"></a>Kapsling

Syntaxen är `input.SelectMany(x=>x.Q())`, där Q är en `Select`-, `SelectMany`-, eller `Where`-operator.

I en kapslad fråga tillämpas den inre frågan på varje element i den yttre containern. En viktig funktion är att den inre frågan kan referera till fält i elementen i den yttre containern såsom självkopplingar.

**LINQ-lambdauttryck**

```csharp
    input.SelectMany(family=>
        family.parents.Select(p => p.familyName));
```

**SQL**

```sql
    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents
```

**LINQ-lambdauttryck**

```csharp
    input.SelectMany(family =>
        family.children.Where(child => child.familyName == "Jeff"));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"
```

**LINQ-lambdauttryck**

```csharp
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));
```

**SQL**

```sql
    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName
```

## <a id="ExecutingSqlQueries"></a>Köra SQL-frågor

Cosmos DB visar resurser via ett REST-API som kan anropas av alla språk som kan göra HTTP/HTTPS-begäranden. Dessutom ger Cosmos DB programmeringsbibliotek för flera populära språk som .NET, Node.js, JavaScript och Python. REST API och de olika biblioteken har alla stöd för frågor via SQL. .NET SDK stöder LINQ-frågor utöver SQL.

I följande exempel visas hur du skapar en fråga och skickar den mot ett Cosmos DB-databaskonto.

### <a id="RestAPI"></a>REST API

Cosmos DB erbjuder en öppen RESTful-programmeringsmodell via HTTP. Databaskonton kan etableras med hjälp av en Azure-prenumeration. Cosmos DB-resursmodellen består av en uppsättning resurser under ett databaskonto som var och en är adresserbar via en logisk och stabil URI. En uppsättning resurser kallas för ett flöde i det här objektet. Ett databaskonto består av en uppsättning databaser som var och en innehåller flera containrar, som i sin tur innehåller objekt, UDF:er och andra resurstyper.

Den grundläggande interaktionsmodellen med dessa resurser är via HTTP-verben GET, PUT, POST och DELETE med deras standardtolkning. Verbet POST används för att skapa en ny resurs, för att köra en lagrad procedur eller för att utfärda en Cosmos DB-fråga. Frågor är alltid skrivskyddade åtgärder utan bieffekter.

I följande exempel visas en POST för en SQL API-fråga som görs mot en container som innehåller de två exempelobjekt som vi har granskat hittills. Frågan har ett enkelt filter på JSON-namnegenskapen. Observera användningen av den `x-ms-documentdb-isquery` och Content-Type: `application/query+json`-huvuden för att ange att åtgärden är en fråga.

**Förfrågan**
```
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
```

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
```
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
```

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

Om resultatet för en fråga inte ryms inom en enda resultatsida returnerar REST API en fortsättningstoken via `x-ms-continuation-token`-svarshuvudet. Klienter kan sidnumrera resultat genom att inkludera rubriken i efterföljande resultat. Antalet resultat per sida kan även styras via `x-ms-max-item-count`-nummerrubriken. Om den angivna frågan har en sammansättningsfunktion som `COUNT` kan frågan returnera ett delvis sammansatt värde över sidan med resultat. Klienterna måste utföra en sammansättning på andra nivån över dessa resultat för att bilda slutresultatet, till exempel summan över de mängder som returneras på de enskilda sidorna för att returnera totalmängden.

För att hantera datakonsekvensprincipen för frågor använder du `x-ms-consistency-level`-huvudet som med alla REST API-begäranden. För sessionskonsekvens krävs det att det senaste `x-ms-session-token` Cookie-huvudet ekosänds i frågebegäran. Den befrågade containerns indexeringsprincip kan också påverka konsekvensen för frågeresultatet. Med standardinställningarna för indexeringsprincip är index för containrar alltid aktuellt med de objektinnehåll och frågeresultat som matchar den konsekvens som valts för data. Om indexprincipen sänks till Lazy (Lat) kan frågorna returnera inaktuella resultat. Mer information finns i [Konsekvensnivåer i Azure Cosmos DB][consistency-levels].

Om den konfigurerade indexeringsprincipen i containern inte har stöd för den angivna frågan returnerar Azure Cosmos DB-servern 400 ”Bad Request” (Felaktig begäran). Det här felmeddelandet returneras för intervallfrågor mot sökvägar som konfigurerats för hash-sökningar (likhet) och för sökvägar som uttryckligen exkluderas från indexering. `x-ms-documentdb-query-enable-scan`-huvudet kan anges att tillåta att frågan utför en genomsökning när ett index inte är tillgängligt.

Du kan få detaljerade mått om frågekörning genom att ange `x-ms-documentdb-populatequerymetrics`-huvudet till `True`. Mer information finns i avsnittet om [SQL-frågemått för Azure Cosmos DB](sql-api-sql-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET) SDK

.NET SDK stöder både LINQ- och SQL-frågor. I följande exempel visas hur du utför den filterfråga som introducerade tidigare i det här objektet.
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

Det här exemplet jämför två egenskaper vad gäller kvalitet i varje objekt och använder anonyma projektioner.

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

I nästa exempel visas kopplingar, som uttryckts genom LINQ SelectMany.

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

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET-klienten itererar automatiskt igenom alla sidor i frågeresultatet i foreach-blocken enligt vad som visas ovan. De frågealternativ som introducerades i REST API-avsnittet är även tillgängliga i .NET SDK med hjälp av klasserna `FeedOptions` och `FeedResponse` i metoden CreateDocumentQuery. Antalet sidor kan kontrolleras med inställningen `MaxItemCount`.

Du kan även explicit styra sidonumrering genom att skapa `IDocumentQueryable` med hjälp av objektet `IQueryable` och sedan läsa ` ResponseContinuationToken`-värdena och skicka dem tillbaka som `RequestContinuationToken` i `FeedOptions`. `EnableScanInQuery` kan ställas in för att möjliggöra genomsökningar när frågan inte kan stödjas av den konfigurerade indexeringsprincipen. För partitionerade containrar kan du använda `PartitionKey` för att köra frågan mot en enskild partition (Azure Cosmos DB kan dock automatiskt extrahera detta från frågetexten) och `EnableCrossPartitionQuery` för att köra frågor som kan behöva köras mot flera partitioner.

Fler exempel som innehåller frågor finns i [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet).

### <a id="JavaScriptServerSideApi"></a>JavaScript-API på serversidan

Cosmos DB är en programmeringsmodell för att köra JavaScript-baserad programlogik direkt på containrarna med hjälp av lagrade procedurer och utlösare. Den JavaScript-logik som registreras på en containernivå kan sedan utfärda databasåtgärder i åtgärderna på objekten i den angivna containern. De här åtgärderna omsluts i omgivande ACID-transaktioner.

I följande exempel visas hur du använder queryDocuments i JavaScript-server-API för att göra frågor inifrån lagrade procedurer och utlösare.

```javascript
    function businessLogic(name, author) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="References"></a>Referenser

1. [Introduktion till Azure Cosmos DB][introduction]
2. [Azure Cosmos DB SQL-specifikation](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
4. [Konsekvensnivåer i Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. JavaScript-specifikation [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Frågeutvärderingstekniker för stora databaser [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13. G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
