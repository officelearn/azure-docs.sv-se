---
title: Med hjälp av T-SQL-loopar i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att använda T-SQL-slingor och ersätta markörer i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8d51c8f18d7c00d21fcc057efcda73e2a6b46cc7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598973"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Använda slingor för T-SQL i SQL Data Warehouse
Tips för att använda T-SQL-slingor och ersätta markörer i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="purpose-of-while-loops"></a>Syftet med WHILE-slingor

SQL Data Warehouse stöder den [medan](/sql/t-sql/language-elements/while-transact-sql) loop för att köra instruktionen block upprepade gånger. Denna loop STUND fortsätter för förutsatt att de angivna villkoren är SANT eller tills koden särskilt avbryter loopen med hjälp av nyckelordet BREAK. Slingor är användbara för att ersätta markörer som definierats i SQL-kod. Nästan alla markörer som har skrivits i SQL-kod är lyckligtvis sortens snabba framåtriktade, skrivskyddade. Därför [medan] slingor är ett bra alternativ för att ersätta markörer.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Ersätta markörer i SQL Data Warehouse
Men innan du dyker i huvudet först bör du fråga dig själv följande fråga ”: den här pekaren skrivas för att använda set-baserade operations?”. I många fall svaret är Ja och ofta är det bästa sättet. En set-baserade åtgärden ofta utförs snabbare än en iterativ, rad för rad-metod.

Snabba framåtriktade skrivskyddade markörer kan enkelt ersättas med en slinga konstruktion. Följande är ett enkelt exempel. Det här kodexemplet Uppdaterar statistiken för alla tabeller i databasen. Genom att gå över tabeller i en slinga kör varje kommando i sekvensen.

Börja med att skapa en tillfällig tabell som innehåller ett unikt radnummer som används för att identifiera de enskilda rapporterna:

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

Andra, initiera variablerna som krävs för att utföra slingan:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nu slinga över instruktioner som körs på en i taget:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Slutligen släppa den temporära tabellen skapas i det första steget

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

