---
title: Utnyttja T-SQL-loopar i Azure SQL Data Warehouse | Microsoft Docs
description: "Tips för Transact-SQL-slingor och ersätta markörer i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 40a872ff310f48bfd543ac184fe7301b85b50258
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="loops-in-sql-data-warehouse"></a>Slingor i SQL Data Warehouse
SQL Data Warehouse stöder den [medan][medan] loop för att köra instruktionen block upprepade gånger. Detta fortsätter för förutsatt att de angivna villkoren är SANT eller tills koden specifikt avslutar en loop med hjälp av den `BREAK` nyckelord. Slingor är särskilt användbara för att ersätta markörer som definierats i SQL-kod. Lyckligtvis läses nästan alla markörer som har skrivits i SQL-kod för snabb framåtriktad endast olika. Därför [medan] slingor är ett bra alternativ om du själv behöva ersätta någon.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Utnyttja slingor och ersätta markörer i SQL Data Warehouse
Men innan du dyker i huvudet först bör du fråga dig själv följande fråga: ”kunde markören nytt skrivas till använda uppsättning baserad operations”?. I många fall svaret blir Ja och är ofta det bästa sättet. En uppsättning baserad åtgärd ofta utför mycket snabbare än en iterativ, rad för rad-metod.

Snabba framåtriktade skrivskyddade markörer kan enkelt ersättas med en slinga konstruktion. Nedan visas ett enkelt exempel. Det här kodexemplet Uppdaterar statistiken för alla tabeller i databasen. Genom att gå över tabeller i en slinga kommer du att köra varje kommando i sekvensen.

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


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[medan]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
