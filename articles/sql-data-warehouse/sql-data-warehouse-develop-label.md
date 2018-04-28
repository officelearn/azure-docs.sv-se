---
title: Använda etiketter på betalningsinstrument frågor i SQL Data Warehouse | Microsoft Docs
description: Tips för att använda etiketter på betalningsinstrument frågor i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 22737faa146d83f1f31489125dee4146c7d11ac1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Med etiketter till betalningsinstrument frågor i Azure SQL Data Warehouse
Tips för att använda etiketter på betalningsinstrument frågor i Azure SQL Data Warehouse för utveckling av lösningar.


## <a name="what-are-labels"></a>Vad är etiketter?
SQL Data Warehouse stöder ett begrepp som kallas frågan etiketter. Innan du fortsätter till varje djup ska vi titta på ett exempel:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sista raden taggar strängen 'Min fråga etiketten' i frågan. Den här taggen är särskilt användbara eftersom etiketten är frågan kan via de av DMV: er. Fråga om etiketter tillhandahåller en mekanism för att hitta problemet frågor och hjälper till att identifiera pågår via en ELT kör.

En bra namngivningskonvention hjälper verkligen. Till exempel hjälper från etiketten med projekt procedur, INSTRUKTION eller kommentar till att identifiera frågan bland all kod i källkontroll.

Följande fråga använder en dynamisk hanteringsvy för att söka efter etikett.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Det är viktigt att placera hakparenteser eller dubbla citattecken runt word etiketten vid fråga. Etiketten är ett reserverat ord och orsakar ett fel när den inte är avgränsad. 
> 
> 

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).


