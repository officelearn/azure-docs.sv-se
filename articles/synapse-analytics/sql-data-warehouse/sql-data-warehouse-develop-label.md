---
title: Använda etiketter för instrumentfrågor
description: Tips om hur du använder etiketter för att instrumentera frågor i Azure SQL Data Warehouse för att utveckla lösningar.
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
ms.openlocfilehash: 828e4a406cd0fb12877af44263ab1f338c20850c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351684"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Använda etiketter för instrumentfrågor i Azure SQL Data Warehouse
Tips om hur du använder etiketter för att instrumentera frågor i Azure SQL Data Warehouse för att utveckla lösningar.


## <a name="what-are-labels"></a>Vad är etiketter?
SQL Data Warehouse stöder ett koncept som kallas frågeetiketter. Innan du går in på något djup, låt oss titta på ett exempel:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sista raden taggar strängen "Min frågeetikett" till frågan. Den här taggen är särskilt användbar eftersom etiketten är frågeduglig via DMVs. Att fråga efter etiketter är en mekanism för att hitta problemfrågor och hjälpa till att identifiera förloppet via en ELT-körning.

En bra namngivningskonvention hjälper verkligen. Om du till exempel startar etiketten med PROJEKT, FÖRFARANDE, UTDRAG ELLER KOMMENTAR kan du unikt identifiera frågan bland all kod i källkoden.

Följande fråga använder en dynamisk hanteringsvy för att söka efter etikett.

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


