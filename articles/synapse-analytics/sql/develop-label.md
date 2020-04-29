---
title: Använda fråge etiketter i Synapse SQL
description: I den här artikeln finns några viktiga tips för att använda fråge etiketter i Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430037"
---
# <a name="use-query-labels-in-synapse-sql"></a>Använda fråge etiketter i Synapse SQL
I den här artikeln finns några viktiga tips för att använda fråge etiketter i Synapse SQL.

> [!NOTE]
> SQL på begäran (för hands version) stöder inte märknings frågor.

## <a name="what-are-query-labels"></a>Vad är fråge etiketter
SQL-poolen stöder ett begrepp som kallas fråge etiketter. Låt oss titta på ett exempel innan du går igenom ett djup:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sista raden Taggar strängen "min fråga etikett" till frågan. Den här taggen är särskilt användbar eftersom etiketten är frågekörning genom DMV: er. Fråga efter etiketter är en mekanism för att hitta problem frågor och hjälpa till att identifiera förloppet genom en ELT körning.

Bra namngivnings konventioner är mest användbara. Exempel: om du startar etiketten med PROJECT, PROCEDURe, STATEMENT eller COMMENT identifieras frågan bland all kod i käll kontrollen.

Följande fråga använder en vy med dynamisk hantering för att söka efter etikett:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Det är viktigt att sätta hakparenteser eller dubbla citat tecken runt ordet etikett vid frågor. Label är ett reserverat ord och orsakar ett fel när det inte är avgränsat. 
> 
> 

## <a name="next-steps"></a>Nästa steg
Mer utvecklings tips finns i [utvecklings översikt](develop-overview.md).


