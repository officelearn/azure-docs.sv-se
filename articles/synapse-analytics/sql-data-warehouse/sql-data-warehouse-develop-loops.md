---
title: Använda T-SQL-loopar
description: Tips för lösningsutveckling med T-SQL-loopar och ersätter markörer i Synapse SQL-pool.
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
ms.openlocfilehash: 4cec4801f2a15ebf858f50377c9718fdacac4e29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619002"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Använda T-SQL-loopar i Synapse SQL-pool
I den här artikeln finns tips för utveckling av SQL-poollösning med T-SQL-loopar och ersätter markörer.

## <a name="purpose-of-while-loops"></a>Syftet med WHILE-loopar

Synapse SQL-pool [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) stöder WHILE-loopen för att upprepade gånger köra program statement block. Den här WHILE-loopen fortsätter så länge de angivna villkoren är sanna eller tills koden specifikt avslutar loopen med nyckelordet BREAK. 

Loopar är användbara för att ersätta markörer som definierats i SQL-kod. Lyckligtvis är nästan alla markörer som är skrivna i SQL-kod av snabbspolning framåt, skrivskyddad sort. Så, MEDAN loopar är ett bra alternativ för att ersätta markörer.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Ersätta markörer i Synapse SQL-pool
Men innan du dyker i huvudet först bör du fråga dig själv följande fråga: "Kan denna markör skrivas om för att använda set-baserade operationer?" 

I många fall är svaret ja och är ofta det bästa tillvägagångssättet. En set-baserad åtgärd utförs ofta snabbare än en iterativ, rad för rad-metod.

Snabbspola skrivskyddade markörer kan enkelt ersättas med en looping konstruktion. Följande exempel är enkelt. I det här kodexemplet uppdateras statistiken för varje tabell i databasen. Genom att iterera över tabellerna i loopen körs varje kommando i följd.

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

