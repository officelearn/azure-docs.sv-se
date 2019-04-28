---
title: Med hjälp av T-SQL-slingor i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att använda T-SQL-slingor och ersätta markörer i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5aa26aeb27d962e6e6289a754ef57b49158b68db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439204"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Med hjälp av T-SQL-slingor i SQL Data Warehouse
Tips för att använda T-SQL-slingor och ersätta markörer i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="purpose-of-while-loops"></a>Syftet med vid slingor

SQL Data Warehouse stöder den [medan](/sql/t-sql/language-elements/while-transact-sql) loop för att köra instruktionen block upprepade gånger. Den här tag-loopen fortsätter så länge de angivna villkoren är sant, eller tills koden särskilt avbryter loopen med hjälp av nyckelordet BREAK. Loopar är användbara för att ersätta markörer som definierats i SQL-kod. Nästan alla markörer som är skrivna i SQL-kod är som tur är kan sortens snabba framåtriktade, skrivskyddade. Därför [medan] slingor är ett perfekt alternativ för att ersätta markörer.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Ersätt markörer i SQL Data Warehouse
Men innan du börjar i head först bör du fråga dig själv följande fråga: ”Kan den här markören skrivas om för att använda set-baserade åtgärder?”. I många fall svaret är Ja och är ofta det bästa sättet. En set-baserade åtgärden ofta utförs snabbare än en iterativ, rad för rad metod.

Snabba framåtriktade skrivskyddade markörer kan enkelt ersättas med uvozuje konstruktor cyklu. Följande är ett enkelt exempel. Det här kodexemplet uppdaterar statistik för varje tabell i databasen. Genom att gå över tabeller i loopen körs varje kommando i följd.

Börja med att skapa en tillfällig tabell som innehåller ett unikt radnummer som används för att identifiera enskilda uttryck:

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

Andra initiera variabler som krävs för att utföra loopen:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nu köras i slinga över instruktioner som körs på en i taget:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Slutligen släppa den temporära tabellen som skapats i det första steget

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

