---
title: Arbeta med matriser och objekt i Azure Cosmos DB
description: Läs mer om array- och skapa en SQL-syntax för Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342768"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Arbeta med matriser och objekt i Azure Cosmos DB

En viktig funktion i Azure Cosmos DB SQL API är att skapa en matris och objekt.

## <a name="arrays"></a>matriser

Du kan skapa matriser, som visas i följande exempel:

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

Du kan också använda den [matrisuttryck](sql-query-subquery.md#array-expression) att konstruera en matris från [underfråga](sql-query-subquery.md) resultat. Den här frågan hämtar alla distinkta angivna namn på barnen i en matris.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteration

SQL-API har stöd för att iterera över JSON-matriser med en ny konstruktion som lagts till via den [i nyckelordet](sql-query-keywords.md#in) i FROM-källan. I följande exempel:

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

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Kopplingar](sql-query-join.md)