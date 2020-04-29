---
title: Använda T-SQL-slingor
description: Tips för utveckling av lösningar med T-SQL-slingor och ersättning av markörer i Synapse SQL-pool.
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
ms.openlocfilehash: 72a39804931c0834233e91190aacffa8d35912df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633481"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Använda T-SQL-slingor i Synapse SQL-pool

I den här artikeln finns tips för utveckling av SQL-pooler med T-SQL-slingor och ersättning av markörer.

## <a name="purpose-of-while-loops"></a>Syfte med WHILe-slingor

Synapse SQL-pool stöder [while](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) -loopen för att köra instruktions block upprepade gånger. Den här WHILe-slingan fortsätter så länge som de angivna villkoren är sanna eller tills koden specifikt avslutar loopen med hjälp av nyckelordet BREAK.

Slingor är användbara för att ersätta markörer som definierats i SQL-kod. Lyckligt vis är nästan alla markörer som skrivs i SQL-kod av den snabba, skrivskyddade sorten. Detta innebär att slingor är ett bra alternativ för att ersätta markörer.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Ersätta markörer i Synapse SQL-pool

Men innan du simhopp i head borde du först fråga dig själv om följande fråga: "kan den här markören skrivas om för att använda set-based Operations?"

I många fall är svaret ja och det är ofta den bästa metoden. En Set-baserad åtgärd utför ofta snabbare än en upprepad rad med rad metod.

Snabba framåtriktade skrivskyddade markörer kan enkelt ersättas med en loop-konstruktion. Följande exempel är en enkel. I den här kod exemplet uppdateras statistiken för alla tabeller i databasen. Genom att iterera över tabellerna i slingan körs varje kommando i följd.

Skapa först en temporär tabell som innehåller ett unikt rad nummer som används för att identifiera de enskilda uttrycken:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Sedan initierar du de variabler som krävs för att utföra slingan:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Loopar över instruktioner som kör dem en i taget:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Ta slutligen bort den tillfälliga tabellen som skapades i det första steget

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).
