---
title: Frågor med parametrar i Azure Cosmos DB
description: Lär dig mer om parametriserade SQL-frågor
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342790"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Frågor med parametrar i Azure Cosmos DB

Cosmos DB stöder frågor med parametrar som är uttryckt med bekant @ notation. Parametriserad SQL ger stabil hantering och undantagstecken indata från användaren och förhindrar oavsiktlig exponering av data via SQL-inmatning.

## <a name="examples"></a>Exempel

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


## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelldokumentdata](modeling-data.md)
