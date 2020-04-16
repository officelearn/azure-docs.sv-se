---
title: Använda frågeetiketter i Synapse SQL
description: I den här artikeln finns viktiga tips för hur du använder frågeetiketter i Synapse SQL.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430037"
---
# <a name="use-query-labels-in-synapse-sql"></a>Använda frågeetiketter i Synapse SQL
I den här artikeln finns viktiga tips för hur du använder frågeetiketter i Synapse SQL.

> [!NOTE]
> SQL on-demand (preview) stöder inte märkningsfrågor.

## <a name="what-are-query-labels"></a>Vad är frågeetiketter
SQL-poolen stöder ett koncept som kallas frågeetiketter. Innan du går in på något djup, låt oss titta på ett exempel:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sista raden taggar strängen "Min frågeetikett" till frågan. Den här taggen är särskilt användbar eftersom etiketten är frågeduglig via DMVs. Att fråga efter etiketter är en mekanism för att hitta problemfrågor och hjälper till att identifiera förloppet via en ELT-körning.

Bra namngivningskonvention är till stor hjälp. Om du till exempel startar etiketten med PROJECT, PROCEDURE, STATEMENT eller COMMENT identifierar frågan unikt bland all kod i källkoden.

Följande fråga använder en dynamisk hanteringsvy för att söka efter etikett:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Det är viktigt att sätta hakparenteser eller dubbla citattecken runt ordetiketten när du frågar. Etikett är ett reserverat ord och orsakar ett fel när det inte avgränsas. 
> 
> 

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips finns i [utvecklingsöversikt](develop-overview.md).


