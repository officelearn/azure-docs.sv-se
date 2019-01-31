---
title: Med hjälp av etiketter för att instrumentera frågor i SQL Data Warehouse | Microsoft Docs
description: Tips för att använda etiketter för att instrumentera frågor i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 5c60a7dc44471599834c4b1225b397c8e6eabbd5
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55456467"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Använda etiketter för att instrumentera frågor i Azure SQL Data Warehouse
Tips för att använda etiketter för att instrumentera frågor i Azure SQL Data Warehouse för utveckling av lösningar.


## <a name="what-are-labels"></a>Vad är etiketter?
SQL Data Warehouse stöder ett begrepp som kallas fråga etiketter. Innan du går in i varje djup, nu ska vi titta på ett exempel:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sista raden taggar strängen ”min fråga etiketten” i frågan. Den här taggen är särskilt användbart eftersom etiketten är frågan kan via de DMV: er. Fråga efter etiketter är en mekanism för att hitta problem med frågor och hjälper till att identifiera förloppet via en ELT kör.

En bra namngivningskonvention hjälper verkligen. Till exempel hjälper börjar etiketten med projekt, proceduren, INSTRUKTIONEN eller kommentar till att unikt identifiera frågan bland all kod i källkontrollen.

Följande fråga använder en dynamisk hanteringsvy för att söka efter etikett.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Det är viktigt att placera hakparenteser eller dubbla citattecken runt word etiketten vid frågor. Etiketten är ett reserverat ord och orsakar ett fel när den inte är avgränsad. 
> 
> 

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).


