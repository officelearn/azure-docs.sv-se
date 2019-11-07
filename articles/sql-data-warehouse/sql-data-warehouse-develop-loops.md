---
title: Använda T-SQL-slingor
description: Tips för att använda T-SQL-slingor och ersätta markörer i Azure SQL Data Warehouse för att utveckla lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b57358e32bda83ef51fe67aa1057411d51773fa6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685821"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Använda T-SQL-slingor i SQL Data Warehouse
Tips för att använda T-SQL-slingor och ersätta markörer i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="purpose-of-while-loops"></a>Syfte med WHILe-slingor

SQL Data Warehouse stöder [while](/sql/t-sql/language-elements/while-transact-sql) -slingan för att köra instruktions block upprepade gånger. Den här WHILe-slingan fortsätter så länge som de angivna villkoren är sanna eller tills koden specifikt avslutar loopen med hjälp av nyckelordet BREAK. Slingor är användbara för att ersätta markörer som definierats i SQL-kod. Lyckligt vis är nästan alla markörer som skrivs i SQL-kod av den snabba, skrivskyddade sorten. Därför är det ett bra alternativ att ersätta markörer med [WHILe]-slingor.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Ersätta markörer i SQL Data Warehouse
Men innan du simhopp i head borde du först fråga dig själv om följande fråga: "kan den här markören skrivas om för att använda de set-baserade åtgärderna?". " I många fall är svaret ja och är ofta den bästa metoden. En Set-baserad åtgärd utför ofta snabbare än en upprepad rad med rad metod.

Snabba framåtriktade skrivskyddade markörer kan enkelt ersättas med en loop-konstruktion. Följande är ett enkelt exempel. I den här kod exemplet uppdateras statistiken för alla tabeller i databasen. Genom att iterera över tabellerna i slingan körs varje kommando i följd.

Skapa först en temporär tabell som innehåller ett unikt rad nummer som används för att identifiera de enskilda uttrycken:

```
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

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Loopar över instruktioner som kör dem en i taget:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Ta slutligen bort den tillfälliga tabellen som skapades i det första steget

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nästa steg
Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).

