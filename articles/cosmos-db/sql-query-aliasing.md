---
title: Alias i Azure Cosmos DB
description: Lär dig mer om aliasing-värden i Azure Cosmos DB SQL-frågor
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002075"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Alias i Azure Cosmos DB

Du kan uttryckligen Ali Aset värden i frågor. Om en fråga har två egenskaper med samma namn, använder du alias för att byta namn på en eller båda av egenskaperna så att de är disambiguated i det planerade resultatet.

## <a name="examples"></a>Exempel

AS-nyckelordet som används för alias är valfritt, som du ser i följande exempel när du projicerar det andra `NameInfo`värdet som:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultaten är:

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
- [SELECT-satsen](sql-query-select.md)
- [FROM-satsen](sql-query-from.md)
