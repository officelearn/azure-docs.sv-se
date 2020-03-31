---
title: Använda T-SQL-loopar
description: Tips om hur du använder T-SQL-loopar och ersätter markörer i Azure SQL Data Warehouse för att utveckla lösningar.
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
ms.openlocfilehash: afb2160cb9b4e34d3d17db86bac9cd3be79886d0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351593"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Använda T-SQL-loopar i SQL Data Warehouse
Tips om hur du använder T-SQL-loopar och ersätter markörer i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="purpose-of-while-loops"></a>Syftet med WHILE-loopar

SQL Data Warehouse stöder [WHILE-loopen](/sql/t-sql/language-elements/while-transact-sql) för upprepade programsatser. Den här WHILE-loopen fortsätter så länge de angivna villkoren är sanna eller tills koden specifikt avslutar loopen med nyckelordet BREAK. Loopar är användbara för att ersätta markörer som definierats i SQL-kod. Lyckligtvis är nästan alla markörer som är skrivna i SQL-kod av snabbspolning framåt, skrivskyddad sort. Därför [MEDAN] loopar är ett bra alternativ för att ersätta markörer.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Ersätta markörer i SQL Data Warehouse
Men innan du dyker i huvudet först bör du fråga dig själv följande fråga: "Kan denna markör skrivas om för att använda set-baserade operationer?." I många fall är svaret ja och är ofta det bästa tillvägagångssättet. En set-baserad åtgärd utförs ofta snabbare än en iterativ, rad för rad-metod.

Snabbspola skrivskyddade markörer kan enkelt ersättas med en looping konstruktion. Följande är ett enkelt exempel. I det här kodexemplet uppdateras statistiken för varje tabell i databasen. Genom att iterera över tabellerna i loopen körs varje kommando i följd.

Skapa först en temporär tabell som innehåller ett unikt radnummer som används för att identifiera de enskilda uttrycken:

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

För det andra initierar du de variabler som krävs för att utföra loopen:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nu loop över uttalanden som utför dem en i taget:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Släpp slutligen den temporära tabellen som skapades i det första steget

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

