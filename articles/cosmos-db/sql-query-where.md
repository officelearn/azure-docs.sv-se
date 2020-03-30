---
title: WHERE-satsen i Azure Cosmos DB
description: Lär dig mer om SQL WHERE-satsen för Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898782"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WHERE-satsen i Azure Cosmos DB

Den valfria`WHERE <filter_condition>`WHERE-satsen ( ) anger villkor som källan JSON-objekt måste uppfylla för att frågan ska kunna inkludera dem i resultat. En JSON-artikel måste utvärdera `true` de angivna villkor som ska beaktas för resultatet. Indexlagret använder WHERE-satsen för att bestämma den minsta delmängden av källobjekt som kan vara en del av resultatet.
  
## <a name="syntax"></a>Syntax
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argument

- `<filter_condition>`  
  
   Anger villkoret som ska uppfyllas för att dokumenten ska returneras.  
  
- `<scalar_expression>`  
  
   Uttryck som representerar det värde som ska beräknas. Mer information finns i [Skalningsuttryck.](sql-query-scalar-expressions.md)  
  
## <a name="remarks"></a>Anmärkningar
  
  För att dokumentet ska kunna returneras måste ett uttryck som anges som filtervillkor utvärderas till true. Endast booleskt värde `true` kommer att uppfylla villkoret, något annat värde: odefinierat, null, falskt, Tal, Array eller Objekt kommer inte att uppfylla villkoret.

  Om du inkluderar partitionsnyckeln `WHERE` i satsen som en del av ett likhetsfilter filtreras frågan automatiskt till endast relevanta partitioner.

## <a name="examples"></a>Exempel

Följande fråga begär objekt `id` som innehåller `AndersenFamily`en egenskap vars värde är . Det utesluter alla objekt som `id` inte har en egenskap `AndersenFamily`eller vars värde inte matchar .

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skaläruttryck i WHERE-satsen

I föregående exempel visades en enkel likhetsfråga. SQL API stöder också olika [skalenade uttryck](sql-query-scalar-expressions.md). De mest använda är binära och unära uttryck. Egenskapsreferenser från JSON-källobjektet är också giltiga uttryck.

Du kan använda följande binära operatorer som stöds:  

|**Operatortyp**  | **Värden** |
|---------|---------|
|Aritmetiska | +,-,*,/,% |
|Binära    | \|, &, ^, <<, >>, >>> (högerskiftning med nollfyllning) |
|Logiska    | AND, OR, NOT (och, eller, inte)      |
|Jämförelse | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (sammanfoga) |

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

Du kan också använda de oariska operatorerna +,-, ~och INTE i frågor, som visas i följande exempel:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Du kan också använda egenskapsreferenser i frågor. Returnerar `SELECT * FROM Families f WHERE f.isRegistered` till exempel JSON-objektet `isRegistered` som innehåller `true`egenskapen med ett värde som är lika med . Alla andra värden, `false` `null`till `Undefined` `<number>`exempel `<string>` `<object>`, `<array>`, , , , eller , exkluderar objektet från resultatet.

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Nyckelordet IN (I)](sql-query-keywords.md#in)
- [FRÅN-satsen](sql-query-from.md)