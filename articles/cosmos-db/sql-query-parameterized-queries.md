---
title: Parameteriserade frågor i Azure Cosmos DB
description: Lär dig hur SQL-parameteriserade frågor ger robust hantering och utrymning av användarindata och förhindrar oavsiktlig exponering av data genom SQL-injektion.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870827"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Parameteriserade frågor i Azure Cosmos DB

Cosmos DB stöder frågor med parametrar som uttrycks av den välbekanta @ notationen. Parameteriserad SQL ger robust hantering och utrymning av användarindata och förhindrar oavsiktlig exponering av data genom SQL-injektion.

## <a name="examples"></a>Exempel

Du kan till exempel skriva `lastName` en `address.state` fråga som tar och som `lastName` `address.state` parametrar och köra den för olika värden för och baserat på användarindata.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Du kan sedan skicka den här begäran till Cosmos DB som en parameteriserad JSON-fråga som följande:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

I följande exempel anges argumentet TOP med en parameteriserad fråga: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parametervärden kan vara giltiga JSON: strängar, tal, booleaner, null, till och med matriser eller kapslade JSON. Eftersom Cosmos DB är schemalös valideras inte parametrar mot någon typ.


## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)
