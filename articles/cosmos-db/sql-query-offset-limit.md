---
title: OFFSET LIMIT-sats i Azure Cosmos DB
description: Lär dig hur du använder OFFSET LIMIT-satsen för att hoppa över och ta vissa värden när du frågar i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: a8df220be211c3c8d8cdeab8a8aebfd35e77ebf8
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732594"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>OFFSET LIMIT-sats i Azure Cosmos DB

OFFSET LIMIT-satsen är en optional-sats att hoppa över och sedan ta ett visst antal värden från frågan. Antalet OFFSET och LIMIT-antalet krävs i OFFSET LIMIT-satsen.

När OFFSET-gränsen används tillsammans med en ORDER BY-sats skapas resultat uppsättningen genom att hoppa över och ta på de beställda värdena. Om ingen ORDER BY-sats används kommer den att resultera i en deterministisk ordning med värden.

## <a name="syntax"></a>Syntax
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argument

- `<offset_amount>`

   Anger det heltals antal objekt som frågeresultaten ska hoppa över.

- `<limit_amount>`
  
   Anger det heltals antal objekt som frågeresultaten ska innehålla

## <a name="remarks"></a>Anmärkningar
  
  Både OFFSET-antalet och LIMIT-antalet krävs i OFFSET LIMIT-satsen. Om en valfri `ORDER BY`-sats används skapas resultat uppsättningen genom att hoppa över de beställda värdena. Annars returnerar frågan en fast ordning med värden. Den här satsen stöds nu för frågor inom en enda partition och frågor om flera partitioner.

## <a name="examples"></a>Exempel

Här är ett exempel på en fråga som hoppar över det första värdet och returnerar det andra värdet (i den inhemska ortens namn):

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

Här är en fråga som hoppar över det första värdet och returnerar det andra värdet (utan ordning):

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
