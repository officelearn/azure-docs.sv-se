---
title: Använda etiketter för instrument frågor
description: Tips om hur du använder etiketter till instrument frågor i Azure SQL Data Warehouse för att utveckla lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4ca0b3564418aafe774158057bc3efd541f71f66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692833"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Använda etiketter för instrument frågor i Azure SQL Data Warehouse
Tips om hur du använder etiketter till instrument frågor i Azure SQL Data Warehouse för att utveckla lösningar.


## <a name="what-are-labels"></a>Vad är etiketter?
SQL Data Warehouse stöder ett begrepp som kallas fråge etiketter. Låt oss titta på ett exempel innan du går igenom ett djup:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sista raden Taggar strängen "min fråga etikett" till frågan. Den här taggen är särskilt användbar eftersom etiketten är frågekörning genom DMV: er. Fråga efter etiketter är en mekanism för att hitta problem frågor och hjälpa till att identifiera förloppet genom en ELT körning.

En bra namngivnings konvention hjälper egentligen. Om du till exempel startar etiketten med projekt, procedur, uttryck eller kommentar kan du identifiera frågan bland all kod i käll kontrollen på ett unikt sätt.

Följande fråga använder en vy med dynamisk hantering för att söka efter etikett.

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
Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).


