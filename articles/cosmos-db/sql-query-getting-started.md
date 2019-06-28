---
title: Komma igång med SQL-frågor i Azure Cosmos DB
description: Introduktion till SQL-frågor
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342886"
---
# <a name="getting-started-with-sql-queries"></a>Komma igång med SQL-frågor

Azure Cosmos DB SQL API-konton stöder förfrågningar till objekt som använder SQL Structured Query Language () som ett frågespråk för JSON. Designmålen för Azure Cosmos DB-frågespråket är att:

* Stöd för SQL, ett av de mest välkända och populära fråga språk, i stället för inventing ett nytt frågespråk. SQL är en formell programmeringsmodell för komplexa frågor via JSON-objekt.  

* Använda JavaScript-programmeringsmodell som grund för frågespråket. JavaScript-typsystemet, uttrycksutvärdering och funktionsanrop är roten i SQL-API. Dessa rötter tillhandahåller en naturlig programmeringsmodell för funktioner som relationella projektioner hierarkisk navigering i JSON-objekt, Självkopplingar, rumsliga förfrågningar och anrop av användardefinierade funktioner (UDF) helt skrivna i JavaScript.

## <a name="upload-sample-data"></a>Ladda upp exempeldata

I ditt SQL API Cosmos DB-konto skapar du en behållare som kallas `Families`. Skapa två enkla JSON-objekt i behållaren. Du kan köra de flesta av exempelfrågor i Azure Cosmos DB-fråga dokumenten med hjälp av den här datamängden.

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

Följande fråga returnerar objekt där den `id` fältet matchar `AndersenFamily`. Eftersom det är en `SELECT *` frågan, resultatet av frågan är fullständigt JSON-objekt. Mer information om hur du väljer syntax finns i [SELECT-instruktion](sql-query-select.md). 

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

Följande fråga returnerar alla angivna namn på barnen i familjen vars `id` matchar `WakefieldFamily`, ordnade efter ort.

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

I föregående exempel visas flera aspekter av frågespråket Cosmos DB:  

* Eftersom SQL-API fungerar på JSON-värden, behandlar den trädet formad entiteter i stället för rader och kolumner. Du kan referera till trädnoder på varje godtyckliga djup som `Node1.Node2.Node3…..Nodem`, ungefär som i två delar referens `<table>.<column>` i ANSI SQL.

* Frågespråket fungerar med schemalös data, att typsystemet måste bindas dynamiskt. Samma uttryck kan ge olika typer på olika objekt. Resultatet av en fråga är ett giltigt JSON-värde, men är inte garanterad ska vara av ett fast schema.  

* Azure Cosmos DB stöder endast strikta JSON-objekt. Den och uttryck är begränsade till bara handlar om JSON-typer. Mer information finns i den [JSON-specifikationen](https://www.json.org/).  

* En Cosmos DB-container är en schemafri samling med JSON-objekt. Relationer inom och mellan behållare objekt avbildas implicit av inneslutning, inte av primärnyckel och sekundärnyckel viktiga relationer. Den här funktionen är viktig för intra-item-kopplingar som beskrivs senare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT-satsen](sql-query-select.md)
