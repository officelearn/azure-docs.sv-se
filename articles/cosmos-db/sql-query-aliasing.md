---
title: Alias i Azure Cosmos DB
description: Lär dig mer om alias värdena i Azure Cosmos DB SQL-frågor
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342647"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Alias i Azure Cosmos DB

Du kan uttryckligen alias värden i frågor. Om en fråga har två egenskaper med samma namn, kan du använda alias för att byta namn på en eller båda egenskaperna så att de är skiljas åt i det beräknade resultatet.

## <a name="examples"></a>Exempel

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

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT-satsen](sql-query-select.md)
- [FROM-satsen](sql-query-from.md)
