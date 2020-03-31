---
title: OFFSET LIMIT-satsen i Azure Cosmos DB
description: Lär dig hur du använder OFFSET LIMIT-satsen för att hoppa över och ta vissa värden när du frågar i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771576"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>OFFSET LIMIT-satsen i Azure Cosmos DB

OFFSET LIMIT-satsen är en valfri sats att hoppa över och sedan ta ett antal värden från frågan. Förskjutningsantalet och antalet LIMIT krävs i OFFSET LIMIT-satsen.

När OFFSET LIMIT används tillsammans med en ORDER BY-sats produceras resultatuppsättningen genom att hoppa över och ta på de beställda värdena. Om ingen ORDER BY-sats används resulterar den i en deterministisk värdeordning.

## <a name="syntax"></a>Syntax
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argument

- `<offset_amount>`

   Anger heltalsantalet objekt som frågeresultatet ska hoppa över.

- `<limit_amount>`
  
   Anger heltalsantalet objekt som frågeresultatet ska innehålla

## <a name="remarks"></a>Anmärkningar
  
  Både `OFFSET` antal och `LIMIT` antal krävs `OFFSET LIMIT` i satsen. Om en `ORDER BY` valfri sats används skapas resultatuppsättningen genom att hoppa över de ordnade värdena. Annars returnerar frågan en fast orderordning med värden.

  RU-avgiften för en `OFFSET LIMIT` fråga med ökar i takt med att antalet termer som förskjuts ökar. För frågor som har flera resultatsidor rekommenderar vi vanligtvis att du använder fortsättningstoken. Fortsättningstoken är ett "bokmärke" för den plats där frågan senare kan återupptas. Om du `OFFSET LIMIT`använder finns det inget "bokmärke". Om du vill returnera frågans nästa sida måste du börja från början.
  
  Du bör `OFFSET LIMIT` använda för fall när du vill hoppa över dokument helt och spara klientresurser. Du bör till `OFFSET LIMIT` exempel använda om du vill hoppa till det 1000:e frågeresultatet och inte behöver visa resultat 1 till 999. På backend `OFFSET LIMIT` läser fortfarande varje dokument, inklusive de som hoppas över. Prestandafördelen är en besparing i klientresurser genom att undvika bearbetning av dokument som inte behövs.

## <a name="examples"></a>Exempel

Här är till exempel en fråga som hoppar över det första värdet och returnerar det andra värdet (i ordning efter den inhemska stadens namn):

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

Här är en fråga som hoppar över det första värdet och returnerar det andra värdet (utan att beställa):

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
- [SELECT-sats](sql-query-select.md)
- [ORDER BY-satsen](sql-query-order-by.md)
