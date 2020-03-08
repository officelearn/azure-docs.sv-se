---
title: WHERE-sats i Azure Cosmos DB
description: Läs om SQL WHERE-satsen för Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898782"
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
  
  Villkoret måste utvärderas till SANT för dokumentet som ska returneras ett uttryck har angetts som filter. Endast booleskt värde `true` uppfyller villkoret, vilket ger något annat värde: undefined, null, false, Number, array eller Object uppfyller inte villkoret.

  Om du inkluderar din partitionsnyckel i `WHERE`-satsen som en del av ett likhets filter så filtreras frågan automatiskt till de relevanta partitionerna.

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
- [FROM-sats](sql-query-from.md)