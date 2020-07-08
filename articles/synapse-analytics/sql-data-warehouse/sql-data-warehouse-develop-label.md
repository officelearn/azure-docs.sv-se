---
title: Använda etiketter för instrument frågor
description: Tips för att använda etiketter för att utforma frågor i Synapse SQL-pool för utveckling av lösningar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d4459547300f5dfc7b7c22d1e531b928a13aa66c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213439"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Använda etiketter för instrument frågor i Synapse SQL-pool

I den här artikeln finns tips för att utveckla lösningar med etiketter till instrument frågor i SQL-poolen.

Tips om hur du använder etiketter till instrument frågor i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="what-are-labels"></a>Vad är etiketter?

SQL-poolen stöder ett begrepp som kallas fråge etiketter. Låt oss titta på ett exempel innan du går igenom ett djup:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sista raden Taggar strängen "min fråga etikett" till frågan. Den här taggen är användbar eftersom etiketten är frågekörning genom DMV: er.

Fråga efter etiketter är en mekanism för att hitta problem frågor och hjälpa till att identifiera förloppet genom en ELT körning.

En bra namngivnings konvention hjälper egentligen. Exempel: om du startar etiketten med PROJECT, PROCEDURe, STATEMENT eller COMMENT identifieras frågan bland all kod i käll kontrollen.

Följande fråga använder en vy med dynamisk hantering för att söka efter etikett:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Det är viktigt att sätta hakparenteser eller dubbla citat tecken runt ordet etikett vid frågor. Label är ett reserverat ord och orsakar ett fel när det inte är avgränsat.

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).
