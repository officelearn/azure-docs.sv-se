---
title: Använda T-SQL-loopar
description: Tips om hur du använder T-SQL-loopar, ersätter markörer och utvecklar relaterade lösningar med SQL-pool i Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429959"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>Använda T-SQL-loopar i Synapse SQL
Den här artikeln ger dig viktiga tips för hur du använder T-SQL-loopar, ersätter markörer och utvecklar relaterade lösningar med SQL-pool i Synapse SQL.

## <a name="purpose-of-while-loops"></a>Syftet med WHILE-loopar

Synapse SQL [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) stöder WHILE-loopen för att upprepade gånger köra statement block. Den här WHILE-loopen fortsätter så länge de angivna villkoren är sanna eller tills koden specifikt avslutar loopen med nyckelordet BREAK. 

Loopar i SQL-poolen är användbara för att ersätta markörer som definierats i SQL-kod. Lyckligtvis är nästan alla markörer som är skrivna i SQL-kod av snabbspolning framåt, skrivskyddad sort. Så, [MEDAN] loopar är ett bra alternativ för att ersätta markörer.

## <a name="replacing-cursors-in-sql-pool"></a>Ersätta markörer i SQL-pool

Innan du dyker in bör följande fråga övervägas: "Kan den här markören skrivas om för att använda set-baserade operationer?" I många fall är svaret ja och är ofta det bästa tillvägagångssättet. En set-baserad åtgärd körs ofta snabbare än en iterativ, rad för rad-metod.

Snabbspola skrivskyddade markörer ersätts enkelt med en loopkonstruktion. Följande kod är ett enkelt exempel. I det här kodexemplet uppdateras statistiken för varje tabell i databasen. Genom att iterera över tabellerna i loopen körs varje kommando i följd.

Skapa först en temporär tabell som innehåller ett unikt radnummer som används för att identifiera de enskilda uttrycken:

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

För det andra initierar du de variabler som krävs för att köra loopen:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nu loop över uttalanden som utför dem en i taget:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Släpp slutligen den temporära tabellen som skapades i det första steget

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [utvecklingsöversikt](develop-overview.md).
