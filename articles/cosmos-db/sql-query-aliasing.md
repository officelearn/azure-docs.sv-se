---
title: Alias i Azure Cosmos DB
description: Lär dig hur du använder alias i Azure Cosmos DB SQL-frågor för att skilja två egenskaper med samma namn
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873479"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Alias i Azure Cosmos DB

Du kan uttryckligen Ali Aset värden i frågor. Om en fråga har två egenskaper med samma namn, använder du alias för att byta namn på en eller båda av egenskaperna så att de är disambiguated i det planerade resultatet.

## <a name="examples"></a>Exempel

AS-nyckelordet som används för alias är valfritt, som visas i följande exempel när du projicerar det andra värdet som `NameInfo`:

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
- [SELECT-satsen](sql-query-select.md)
- [FROM-satsen](sql-query-from.md)
