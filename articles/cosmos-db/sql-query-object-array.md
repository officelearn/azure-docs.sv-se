---
title: Arbeta med matriser och objekt i Azure Cosmos DB
description: Lär dig mer om att skapa SQL-syntax för matris och objekt i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 17a0e4ddf5acd267a4cfbb68c218fe9409a91d57
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003925"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Arbeta med matriser och objekt i Azure Cosmos DB

En viktig funktion i Azure Cosmos DB SQL API är matris-och objekt skapande.

## <a name="arrays"></a>Lagringsmatriser

Du kan skapa matriser, som du ser i följande exempel:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Resultaten är:

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

Du kan också använda [mat ris uttrycket](sql-query-subquery.md#array-expression) för att skapa en matris från under [frågans](sql-query-subquery.md) resultat. Den här frågan hämtar alla distinkta namn på underordnade objekt i en matris.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteration

SQL API ger stöd för att iterera över JSON-matriser, med en ny konstruktion som lagts till via [nyckelordet](sql-query-keywords.md#in) i från-källan. I följande exempel:

```sql
    SELECT *
    FROM Families.children
```

Resultaten är:

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

Nästa fråga utför iteration över `children` `Families` i behållaren. Den utgående matrisen skiljer sig från föregående fråga. Det här exemplet delar `children`upp och fören klar resultatet till en enda matris:  

```sql
    SELECT *
    FROM c IN Families.children
```

Resultaten är:

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

Du kan filtrera efter varje enskild post i matrisen, som du ser i följande exempel:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Resultaten är:

```json
    [{
      "givenName": "Lisa"
    }]
```

Du kan också aggregera över resultatet av en mat ris iteration. Följande fråga räknar till exempel antalet underordnade objekt bland alla familjer:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Resultaten är:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Kopplingar](sql-query-join.md)