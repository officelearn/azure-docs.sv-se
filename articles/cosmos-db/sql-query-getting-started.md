---
title: Komma igång med SQL-frågor i Azure Cosmos DB
description: Lär dig hur du använder SQL-frågor för att fråga data från Azure Cosmos DB. Du kan överföra exempeldata till en behållare i Azure Cosmos DB och fråga den.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873343"
---
# <a name="getting-started-with-sql-queries"></a>Komma igång med SQL-frågor

Azure Cosmos DB SQL API-konton stöder frågeobjekt med SQL (Structured Query Language) som JSON-frågespråk. Designmålen för frågespråket Azure Cosmos DB är att:

* Stöd SQL, ett av de mest välbekanta och populära frågespråken, i stället för att uppfinna ett nytt frågespråk. SQL tillhandahåller en formell programmeringsmodell för omfattande frågor över JSON-objekt.  

* Använd JavaScript:s programmeringsmodell som grund för frågespråket. JavaScript typ system, uttryck utvärdering och funktion åkallan är rötterna till SQL API. Dessa rötter ger en naturlig programmeringsmodell för funktioner som relationsprojektioner, hierarkisk navigering över JSON-objekt, självkopplingar, rumsliga frågor och anrop av användardefinierade funktioner (UDFs) som skrivits helt i JavaScript.

## <a name="upload-sample-data"></a>Ladda upp exempeldata

Skapa en behållare som heter `Families`. Skapa två enkla JSON-objekt i behållaren. Du kan köra de flesta exempelfrågorna i Azure Cosmos DB-frågedokumenten med den här datauppsättningen.

### <a name="create-json-items"></a>Skapa JSON-objekt

Följande kod skapar två enkla JSON-objekt om familjer. De enkla JSON-objekten för familjerna Andersen och Wakefield inkluderar föräldrar, barn och deras husdjur, adress och registreringsinformation. Det första objektet har strängar, siffror, booleaner, matriser och kapslade egenskaper.


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

Det andra `givenName` objektet `familyName` använder `firstName` `lastName`och i stället för och .

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

Prova några frågor mot JSON-data för att förstå några av de viktigaste aspekterna av Azure Cosmos DB:s SQL-frågespråk.

Följande fråga returnerar de `id` objekt `AndersenFamily`där fältet matchar . Eftersom det är `SELECT *` en fråga är utdata för frågan det fullständiga JSON-objektet. Mer information om SELECT-syntax finns i [SELECT-sats](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Frågeresultaten är: 

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

Följande fråga formaterar om JSON-utdata till en annan form. Frågan projicerar ett `Family` nytt JSON-objekt `Name` `City`med två markerade fält och när adressstaden är densamma som tillståndet. "NY, NY" matchar detta fall.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Frågeresultaten är:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Följande fråga returnerar alla angivna namn på `id` `WakefieldFamily`underordnade i familjen vars matchningar , sorterade efter ort.

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

## <a name="remarks"></a>Anmärkningar

De föregående exemplen visar flera aspekter av cosmos DB-frågespråket:  

* Eftersom SQL API fungerar på JSON-värden, handlar det om trädformade entiteter i stället för rader och kolumner. Du kan referera till trädnoderna på `Node1.Node2.Node3…..Nodem`valfritt djup, till exempel `<table>.<column>` , liknande den tvådelade referensen i ANSI SQL.

* Eftersom frågespråket fungerar med schemalösa data måste typsystemet vara dynamiskt bundet. Samma uttryck kan ge olika typer på olika objekt. Resultatet av en fråga är ett giltigt JSON-värde, men är inte garanterat ett fast schema.  

* Azure Cosmos DB stöder endast strikta JSON-objekt. Typsystemet och uttrycken är begränsade till att endast hantera JSON-typer. Mer information finns i [JSON-specifikationen](https://www.json.org/).  

* En Cosmos-behållare är en schemafri samling JSON-objekt. Relationerna inom och mellan behållarobjekt fångas implicit upp av inneslutning, inte av primärnyckel- och sekundärnyckelrelationer. Den här funktionen är viktig för de kopplingar mellan objekt som beskrivs senare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-sats](sql-query-select.md)
