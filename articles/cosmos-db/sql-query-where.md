---
title: WHERE-sats i Azure Cosmos DB
description: Läs om SQL WHERE-satsen för Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: tisande
ms.openlocfilehash: 31653b598f0f3a79bf7f9c09231b1d111f167a16
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982237"
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
  
   Anger villkoret vara uppfyllda för dokument som ska returneras.  
  
- `<scalar_expression>`  
  
   Uttryck som representerar värdet som ska beräknas. Mer information finns i [skalära uttryck](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Anmärkningar
  
  Villkoret måste utvärderas till SANT för dokumentet som ska returneras ett uttryck har angetts som filter. Endast booleska värdet true kommer uppfyller villkoren, ett annat värde: Odefinierad, null, false, tal, matris, eller ett objekt kommer inte uppfyller villkoret. 

## <a name="examples"></a>Exempel

Följande fråga begär objekt som innehåller en `id`-egenskap vars värde är `AndersenFamily`. Alla objekt som inte har en `id` egenskap eller vars värde inte matchar `AndersenFamily`utelämnas.

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

Du kan också använda unära operatorer +,-, ~ och inte i frågor, som du ser i följande exempel:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Du kan också använda egenskaps referenser i frågor. `SELECT * FROM Families f WHERE f.isRegistered` returnerar till exempel JSON-objektet som innehåller egenskapen `isRegistered` med värdet lika med `true`. Andra värden, till exempel `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`eller `<array>`, utesluter objektet från resultatet.

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [I nyckelord](sql-query-keywords.md#in)
- [FROM-satsen](sql-query-from.md)