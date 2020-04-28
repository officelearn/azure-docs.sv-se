---
title: Parametriserade frågor i Azure Cosmos DB
description: Lär dig hur SQL-parametriserade frågor ger robust hantering och avvisning av indata från användaren, och förhindra oavsiktlig exponering av data via SQL-inmatning.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74870827"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Parametriserade frågor i Azure Cosmos DB

Cosmos DB stöder frågor med parametrar som uttrycks av den välkända @-notationen. Parametriserade SQL ger robust hantering och avvisning av indata från användaren, och förhindrar oavsiktlig exponering av data via SQL-inmatning.

## <a name="examples"></a>Exempel

Du kan till exempel skriva en fråga som `lastName` använder och `address.state` som parametrar och köra den för olika värden för `lastName` och `address.state` baserat på användarindata.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Du kan sedan skicka denna begäran till Cosmos DB som en parameter-JSON-fråga som följande:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

I följande exempel anges det översta argumentet med en parametriserad fråga: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parameter värden kan vara valfri giltig JSON: strängar, siffror, booleska värden, null, jämna matriser eller kapslad JSON. Eftersom Cosmos DB är schema löst val IDE ras inte parametrar mot någon typ.


## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)
