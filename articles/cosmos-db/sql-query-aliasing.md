---
title: Aliasing i Azure Cosmos DB
description: Lär dig hur du använder aliasing i Azure Cosmos DB SQL-frågor för att skilja två egenskaper med samma namn
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873479"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing i Azure Cosmos DB

Du kan uttryckligen alias värden i frågor. Om en fråga har två egenskaper med samma namn använder du alias för att byta namn på en eller båda av egenskaperna så att de är oeniga i det beräknade resultatet.

## <a name="examples"></a>Exempel

Nyckelordet AS som används för aliasing är valfritt, vilket visas `NameInfo`i följande exempel när det andra värdet projiceras som:

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

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-sats](sql-query-select.md)
- [FRÅN-satsen](sql-query-from.md)
