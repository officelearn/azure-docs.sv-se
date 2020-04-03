---
title: Använda etiketter för instrumentfrågor
description: Tips om hur du använder etiketter för att instrumentera frågor i Synapse SQL-pool för att utveckla lösningar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c1a4ffcab3d10f1dc91ce036e995ae0026a0d718
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619021"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Använda etiketter för instrumentfrågor i Synapse SQL-pool
I den här artikeln finns tips för att utveckla lösningar med hjälp av etiketter för att instrumentfrågor i SQL-pool.


## <a name="what-are-labels"></a>Vad är etiketter?
SQL-poolen stöder ett koncept som kallas frågeetiketter. Innan du går in på något djup, låt oss titta på ett exempel:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sista raden taggar strängen "Min frågeetikett" till frågan. Den här taggen är användbar eftersom etiketten är frågeduglig via DMVs. 

Att fråga efter etiketter är en mekanism för att hitta problemfrågor och hjälpa till att identifiera förloppet via en ELT-körning.

En bra namngivningskonvention hjälper verkligen. Om du till exempel startar etiketten med PROJECT, PROCEDURE, STATEMENT eller COMMENT identifierar frågan unikt bland all kod i källkoden.

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
Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).


