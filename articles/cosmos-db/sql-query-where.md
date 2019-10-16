---
title: WHERE-sats i Azure Cosmos DB
description: Läs om SQL WHERE-satsen för Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: cd5643d8be06afcd43c5bfe38d6f5e9caa6f906e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326647"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WHERE-sats i Azure Cosmos DB

Den valfria WHERE-satsen (`WHERE <filter_condition>`) anger villkor som käll-JSON-objekten måste uppfylla för att frågan ska inkluderas i resultaten. Ett JSON-objekt måste utvärdera de angivna villkoren för att `true` ska beaktas för resultatet. Index skiktet använder WHERE-satsen för att fastställa den minsta delmängd av käll objekt som kan ingå i resultatet.
  
## <a name="syntax"></a>Syntax
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argument

- `<filter_condition>`  
  
   Anger det villkor som ska uppfyllas för de dokument som ska returneras.  
  
- `<scalar_expression>`  
  
   Uttryck som representerar det värde som ska beräknas. Mer information finns i [skalära uttryck](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Kommentarer
  
  För att det ska gå att returnera ett uttryck som har angetts som filter villkor måste det utvärderas till sant. Endast det booleska värdet True uppfyller villkoret, vilket innebär att alla andra värden: undefined, null, false, Number, array eller Object inte uppfyller villkoret. 

## <a name="examples"></a>Exempel

Följande fråga begär objekt som innehåller en `id`-egenskap vars värde är `AndersenFamily`. Alla objekt som saknar egenskapen `id` eller vars värde inte matchar `AndersenFamily` undantas.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultaten är:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skalära uttryck i WHERE-satsen

I föregående exempel visades en enkel likhetsfråga. SQL-API: et stöder också olika [skalära uttryck](sql-query-scalar-expressions.md). De mest använda är binära och unära uttryck. Egenskapsreferenser från JSON-källobjektet är också giltiga uttryck.

Du kan använda följande binära operatorer som stöds:  

|**Operatortyp**  | **Värden** |
|---------|---------|
|Aritmetiska | +,-,*,/,% |
|Binära    | \|, &, ^, <<, >>, >>> (högerskiftning med nollfyllning) |
|Logiska    | AND, OR, NOT (och, eller, inte)      |
|Jämförelse | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Sträng     |  \|\| (sammanfoga) |

Följande frågor använder binära operatorer:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Du kan också använda unära operatorer +,-, ~ och inte i frågor, som du ser i följande exempel:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Du kan också använda egenskaps referenser i frågor. @No__t-0 returnerar till exempel JSON-objektet som innehåller egenskapen `isRegistered` med värdet som är lika med `true`. Andra värden, till exempel `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, eller `<array>`, utesluter objektet från resultatet. 

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [FROM-sats](sql-query-from.md)