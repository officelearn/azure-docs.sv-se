---
title: OFFSET LIMIT-sats i Azure Cosmos DB
description: Lär dig hur du använder OFFSET LIMIT-satsen för att hoppa över och ta vissa värden när du frågar i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76771576"
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
  
  Både `OFFSET` count och `LIMIT` Count krävs i- `OFFSET LIMIT` satsen. Om en valfri `ORDER BY` sats används skapas resultat uppsättningen genom att hoppa över de beställda värdena. Annars returnerar frågan en fast ordning med värden.

  Avgiften för en fråga med ökar i `OFFSET LIMIT` takt med att antalet förskjutningar ökar. För frågor som har flera resultat sidor rekommenderar vi vanligt vis att du använder fortsättnings-token. Fortsättnings-token är ett "bok märke" för platsen där frågan senare kan återupptas. Det finns inget `OFFSET LIMIT`bok märke om du använder det. Om du vill returnera nästa sida för frågan måste du starta från början.
  
  Du bör använda `OFFSET LIMIT` för fall när du vill hoppa över dokumenten helt och spara klient resurser. Du bör till exempel använda `OFFSET LIMIT` om du vill hoppa över till 1000th-frågeresultaten och inte behöver visa resultat 1 till 999. På Server delen läser `OFFSET LIMIT` fortfarande varje dokument, inklusive de som hoppas över. Prestanda fördelarna är ett besparingar i klient resurser genom att undvika bearbetnings dokument som inte behövs.

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
- [SELECT-sats](sql-query-select.md)
- [ORDER BY-sats](sql-query-order-by.md)
