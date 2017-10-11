---
title: "Använda etiketter på betalningsinstrument frågor i SQL Data Warehouse | Microsoft Docs"
description: "Tips för att använda etiketter på betalningsinstrument frågor i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 44988de8-04c1-4fed-92be-e1935661a4e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 9e75bbe528a427724a623305fbd45e2277e9d0af
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Använd etiketter till betalningsinstrument frågor i SQL Data Warehouse
SQL Data Warehouse stöder ett begrepp som kallas frågan etiketter. Innan du fortsätter till varje djup ska vi titta på ett exempel på en:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Det här sista raden taggar strängen 'Min fråga etiketten' i frågan. Detta är särskilt användbara etiketten är frågan kan via de av DMV: er. Detta ger oss en mekanism för att spåra problem frågor och för att identifiera förlopp genom ett ETL-körning.

En bra namngivningskonvention hjälper verkligen finns här. Till exempel liknande ' projekt: procedur: INSTRUKTIONEN: kommentar ' skulle bidra till att identifiera frågan i bland all kod i källkontroll.

Du kan använda följande fråga som använder de dynamiska hanteringsvyer om du vill söka efter etikett:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Det är viktigt att du omsluter hakparenteser eller dubbla citattecken runt word etiketten när du frågar. Etiketten är ett reserverat ord och kommer orsakade ett fel om det inte har avgränsad.
> 
> 

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
