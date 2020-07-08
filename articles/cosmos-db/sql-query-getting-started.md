---
title: Komma igång med SQL-frågor i Azure Cosmos DB
description: Lär dig hur du använder SQL-frågor för att fråga efter data från Azure Cosmos DB. Du kan ladda upp exempel data till en behållare i Azure Cosmos DB och fråga den.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74873343"
---
# <a name="getting-started-with-sql-queries"></a>Komma igång med SQL-frågor

Azure Cosmos DB SQL API-konton har stöd för att skicka frågor till objekt med Structured Query Language (SQL) som ett JSON-frågespråk. Design målen för Azure Cosmos DB frågespråk är att:

* Stöd för SQL, ett av de mest välkända och populära frågespråket, i stället för att använda ett nytt frågespråk. SQL innehåller en formell programmerings modell för omfattande frågor över JSON-objekt.  

* Använd Java scripts programmerings modell som grund för frågespråket. Java Script: s typ system, uttrycks utvärdering och funktions anrop är rötter för SQL-API: et. Dessa rötter tillhandahåller en naturlig programmerings modell för funktioner som relationella projektioner, hierarkisk navigering över JSON-objekt, själv koppling, spatiala frågor och aktivering av användardefinierade funktioner (UDF: er) helt skrivna i Java Script.

## <a name="upload-sample-data"></a>Ladda upp exempel data

Skapa en behållare med namnet i ditt SQL API Cosmos DB-konto `Families` . Skapa två enkla JSON-objekt i behållaren. Du kan köra de flesta exempel frågorna i Azure Cosmos DB Query-dokument med hjälp av den här data uppsättningen.

### <a name="create-json-items"></a>Skapa JSON-objekt

Följande kod skapar två enkla JSON-objekt om familjer. De enkla JSON-objekten för Andersen-och Wakefield-familjer innehåller föräldrar, barn och deras hus djur, adress och registrerings information. Det första objektet har strängar, siffror, booleska värden, matriser och kapslade egenskaper.


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

Det andra objektet använder `givenName` och `familyName` i stället för `firstName` och `lastName` .

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

### <a name="query-the-json-items"></a>Fråga JSON-objekten

Prova några frågor mot JSON-data för att förstå några av de viktigaste aspekterna i Azure Cosmos DB SQL-frågespråket.

Följande fråga returnerar de objekt där `id` fältet matchar `AndersenFamily` . Eftersom det är en `SELECT *` fråga är resultatet av frågan det fullständiga JSON-objektet. Mer information om SELECT-syntax finns i [Select Statement](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Frågeresultatet är: 

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

Följande fråga formaterar om JSON-utdata till en annan form. Frågan Projects ett nytt JSON `Family` -objekt med två markerade fält, `Name` och `City` när adressens ort är samma som status. "NY, NY" matchar det här ärendet.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Frågeresultatet är:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Följande fråga returnerar alla namn på underordnade objekt i den familj vars `id` matchningar `WakefieldFamily` beställs efter ort.

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

## <a name="remarks"></a>Kommentarer

I föregående exempel visas flera aspekter av Cosmos DB frågespråket:  

* Eftersom SQL API fungerar med JSON-värden, hanterar den Tree-formade entiteter i stället för rader och kolumner. Du kan referera till trädnoden i godtyckligt djup, som `Node1.Node2.Node3…..Nodem` liknar referensen för två delar av `<table>.<column>` i ANSI SQL.

* Eftersom frågespråket fungerar med schemabaserade data, måste typ systemet bindas dynamiskt. Samma uttryck kan ge olika typer på olika objekt. Resultatet av en fråga är ett giltigt JSON-värde, men det är inte säkert att det är ett fast schema.  

* Azure Cosmos DB stöder endast strikta JSON-objekt. Typ systemet och uttryck är begränsade till att bara hantera JSON-typer. Mer information finns i JSON- [specifikationen](https://www.json.org/).  

* En Cosmos-behållare är en schema fri samling med JSON-objekt. Relationerna i och över behållar objekt samlas in implicit genom inne slutning, inte av primär nyckel och sekundär nyckel relationer. Den här funktionen är viktig för kopplingen mellan artiklar som diskuteras senare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-sats](sql-query-select.md)
