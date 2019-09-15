---
title: WHERE-sats i Azure Cosmos DB
description: Läs om SQL WHERE-satsen för Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 362024868de269ed64a440a25e8c19c5b68bef80
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003465"
---
# <a name="where-clause"></a>WHERE-sats

Den valfria WHERE-satsen (`WHERE <filter_condition>`) anger villkor som käll-JSON-objekten måste uppfylla för att frågan ska inkluderas i resultaten. Ett JSON-objekt måste utvärdera de angivna villkoren `true` för att kunna beaktas för resultatet. Index skiktet använder WHERE-satsen för att fastställa den minsta delmängd av käll objekt som kan ingå i resultatet.
  
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
  

## <a name="remarks"></a>Kommentarer
  
  Villkoret måste utvärderas till SANT för dokumentet som ska returneras ett uttryck har angetts som filter. Endast booleska värdet true kommer uppfyller villkoren, ett annat värde: Odefinierad, null, false, tal, matris, eller ett objekt kommer inte uppfyller villkoret. 

## <a name="examples"></a>Exempel

Följande fråga begär objekt som innehåller en `id` egenskap vars värde är. `AndersenFamily` Det utesluter alla objekt som inte har någon `id` egenskap eller vars värde inte matchar. `AndersenFamily`

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

Du kan också använda egenskaps referenser i frågor. `SELECT * FROM Families f WHERE f.isRegistered` Returnerar till exempel JSON-objektet som innehåller egenskapen `isRegistered` med värdet lika `true`med. Alla andra värden, till exempel `false` `Undefined`, `null` `<number>` `<string>` `<array>`,,,, eller, utesluter objektet från resultatet. `<object>` 

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [FROM-satsen](sql-query-from.md)