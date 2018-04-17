---
title: Med hjälp av T-SQL-loopar i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att använda T-SQL-slingor och ersätta markörer i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: ''
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 2e49a0de0e4a6aba6639f7f3100f41c8db254220
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
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

