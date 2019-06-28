---
title: WHERE-satsen i Azure Cosmos DB
description: Läs mer om SQL WHERE-sats för Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342748"
---
# <a name="where-clause"></a>WHERE-satsen

Valfria WHERE-satsen (`WHERE <filter_condition>`) anger villkor att käll-JSON-objekt måste uppfylla för frågan och lägga till dem i resultaten. Ett JSON-objekt måste utvärderas de angivna villkoren till `true` man ta hänsyn till resultatet. Index-lagret använder WHERE-satsen för att fastställa den minsta delmängden källobjekt som kan ingå i resultatet.
  
## <a name="syntax"></a>Syntax
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argument

- `<filter_condition>`  
  
   Anger villkoret vara uppfyllda för dokument som ska returneras.  
  
- `<scalar_expression>`  
  
   Uttryck som representerar värdet som ska beräknas. Se [skaläruttryck](sql-query-scalar-expressions.md) information.  
  

## <a name="remarks"></a>Kommentarer
  
  Villkoret måste utvärderas till SANT för dokumentet som ska returneras ett uttryck har angetts som filter. Endast booleska värdet true kommer uppfyller villkoren, ett annat värde: Odefinierad, null, false, tal, matris, eller ett objekt kommer inte uppfyller villkoret. 

## <a name="examples"></a>Exempel

Följande fråga begäranden-objekt som innehåller en `id` egenskap vars värde är `AndersenFamily`. Den omfattar inte alla objekt som inte har en `id` egenskapen eller vars värde inte matchar `AndersenFamily`.

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

### <a name="scalar-expressions-in-the-where-clause"></a>Skalära uttryck i WHERE-satsen

I föregående exempel visades en enkel likhetsfråga. SQL-API: et stöder olika [skaläruttryck](sql-query-scalar-expressions.md). De mest använda är binära och unära uttryck. Egenskapsreferenser från JSON-källobjektet är också giltiga uttryck.

Du kan använda följande operatorer som stöds binär:  

|**Operatortyp**  | **Värden** |
|---------|---------|
|Aritmetiska | +,-,*,/,% |
|Binära    | \|, &, ^, <<, >>, >>> (högerskiftning med nollfyllning) |
|Logiska    | AND, OR, NOT (och, eller, inte)      |
|Jämförelse | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (sammanfoga) |

Följande frågor använder de binära operatorerna som:

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

Du kan också använda unära operatorer +,-, ~, och inte i frågor som visas i följande exempel:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Du kan också använda egenskapen referenser i frågor. Till exempel `SELECT * FROM Families f WHERE f.isRegistered` returnerar JSON-objektet som innehåller egenskapen `isRegistered` med värdet som är lika med `true`. Någon annan värde, till exempel `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`, eller `<array>`, utesluter objektet från resultatet. 

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [FROM-satsen](sql-query-from.md)