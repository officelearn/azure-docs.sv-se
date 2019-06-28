---
title: GRÄNSEN för OFFSET-sats i Azure Cosmos DB
description: Läs mer om GRÄNSEN för OFFSET-sats för Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342620"
---
# <a name="offset-limit-clause"></a>GRÄNSEN för OFFSET-sats

GRÄNSEN för OFFSET-sats är en valfri sats att hoppa över och sedan vidta vissa antal värden från frågan. Antalet förskjutning och GRÄNSEN för antal måste anges i instruktionen förskjutning GRÄNSEN.

När GRÄNSEN för förskjutning används tillsammans med en ORDER BY-sats, skapas genom att göra hoppa över resultatuppsättningen och utför på sorterad värdena. Om någon ORDER BY-sats används, resulterar det i en deterministisk ordning med värden.

## <a name="syntax"></a>Syntax
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argument

- `<offset_amount>`

   Anger antalet objekt som resultatet av frågan ska hoppa över heltal.

- `<limit_amount>`
  
   Anger antalet objekt som resultatet av frågan ska innehålla heltal

## <a name="remarks"></a>Kommentarer
  
  Både antalet förskjutning och GRÄNSEN för antal måste anges i instruktionen förskjutning GRÄNSEN. Om en valfri `ORDER BY` satsen används, resultatuppsättningen skapas genom att göra den hoppa över de beställda värden. I annat fall returneras ett fast ordning med värden. För närvarande den här satsen stöds för frågor inom en enda partition, över partitioner frågor ännu stöd inte för den.

## <a name="examples"></a>Exempel

Här är till exempel en fråga som det första värdet och returnerar det andra värdet (i ordningen för fasta stadens namn):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Resultatet är:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Här är en fråga som det första värdet och returnerar det andra värdet (utan att beställa):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Resultatet är:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-satsen](sql-query-select.md)
- [ORDER BY-sats](sql-query-order-by.md)
