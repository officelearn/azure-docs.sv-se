---
title: Migrera din SQL-kod till SQL Data Warehouse | Microsoft Docs
description: Tips för att migrera din SQL-kod till Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: f36ee3ce6a8d678cdd846474576fc049df0b4f88
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472311"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrera din SQL-kod till SQL Data Warehouse
Den här artikeln förklarar ändringar i koden behöver du förmodligen göra när du migrerar din kod från en annan databas till SQL Data Warehouse. Vissa SQL Data Warehouse-funktioner kan avsevärt förbättra prestanda eftersom de har utformats för att arbeta i en distribuerad sätt. Men för att upprätthålla prestanda och skalning, är vissa funktioner också inte tillgängliga.

## <a name="common-t-sql-limitations"></a>Vanliga T-SQL-begränsningar
I följande lista sammanfattas de vanligaste funktioner som inte har stöd för SQL Data Warehouse. Länken tar dig till lösningar för funktionerna som inte stöds:

* [ANSI kopplingar på uppdateringar][ANSI joins on updates]
* [ANSI kopplingar till borttagningar][ANSI joins on deletes]
* [merge-instruktion][merge statement]
* kopplingar mellan databaser
* [Markörer][cursors]
* [INSERT..EXEC][INSERT..EXEC]
* Output-sats
* infogade användardefinierade funktioner
* flera instruktioner funktioner
* [vanliga tabelluttryck](#Common-table-expressions)
* [rekursiva cte (CTE)] (#Recursive-common-table-expressions-(CTE)
* CLR-funktioner och procedurer
* $partition funktion
* tabellvariabler
* tabellen värdet parametrar
* Distribuerade transaktioner
* Commit / rollback arbete
* Spara transaktionen
* körningen sammanhang (EXECUTE AS)
* [Group by-satser med Samlad / kub / grupperingsalternativ uppsättningar][group by clause with rollup / cube / grouping sets options]
* [kapslingsnivåer utöver 8][nesting levels beyond 8]
* [Uppdatera via vyer][updating through views]
* [användning av väljer för variabeltilldelning][use of select for variable assignment]
* [Inga MAX datatyp för dynamisk SQL-strängar][no MAX data type for dynamic SQL strings]

Som tur är kan de flesta av dessa begränsningar utföras runt. Förklaringar finns i de relevanta utvecklingsartiklarna som anges ovan.

## <a name="supported-cte-features"></a>CTE-funktioner som stöds
Vanliga tabelluttryck (cte-referenser) stöds delvis i SQL Data Warehouse.  Följande CTE-funktioner stöds för närvarande:

* En CTE kan anges i en SELECT-instruktion.
* En CTE kan anges i en CREATE VIEW-instruktion.
* En CTE kan anges i en CREATE TABLE AS SELECT (CTAS)-instruktion.
* En CTE kan anges i en skapa FJÄRRANSLUTEN tabell som Välj (CRTAS)-instruktion.
* En CTE kan anges i en Skapa extern tabell som Välj (CETAS)-instruktion.
* En fjärrtabell kan refereras från en CTE.
* En extern tabell kan refereras från en CTE.
* Flera CTE fråga definitioner kan definieras i en CTE.

## <a name="cte-limitations"></a>CTE begränsningar
Vanliga tabelluttryck har vissa begränsningar i SQL Data Warehouse, inklusive:

* En CTE måste följas av en enda SELECT-instruktion. INSERT, UPDATE-, DELETE och MERGE-instruktioner stöds inte.
* Ett vanligt tabelluttryck som innehåller referenser till sig själv (ett rekursivt vanligt tabelluttryck) stöds inte (se nedan avsnittet).
* Att ange fler än en WITH-satsen i en CTE tillåts inte. Om en CTE_query_definition innehåller en underfråga, det går inte att exempelvis den underfrågan innehåller en kapslad WITH-satsen som definierar en annan CTE.
* En ORDER BY-sats kan inte användas i CTE_query_definition, förutom när en TOP-satsen har angetts.
* När en CTE används i en instruktion som är en del av en batch, måste instruktionen innan den följas av ett semikolon.
* När det används i rapporter som sammanställts av sp_prepare fungerar cte-referenser på samma sätt som andra SELECT-uttryck i PDW. Men om cte-referenser används som en del av CETAS som sammanställts av sp_prepare kan beteendet fördröja från SQL Server och andra PDW-instruktioner på grund av det sätt som bindningen har implementerats för sp_prepare. Om du väljer att refererar till CTE använder en fel kolumn som inte finns i CTE, skickar sp_prepare utan att identifiera felet, men felet uppstod under ett sp_execute i stället.

## <a name="recursive-ctes"></a>Rekursiva cte-referenser
Rekursiva cte-referenser stöds inte i SQL Data Warehouse.  Migreringen av rekursiva CTE kan vara ganska komplicerat och på bästa sätt är att bryta ned den i flera steg. Du kan normalt använda en slinga och fylla i en tillfällig tabell som du kan iterera över rekursiva mellanliggande frågor. När den temporära tabellen fylls kan du sedan returnera data som en enda resultatmängd. Ett liknande tillvägagångssätt som har använts för att lösa `GROUP BY WITH CUBE` i den [group by-satser med Samlad / kub / grupperingsalternativ uppsättningar] [ group by clause with rollup / cube / grouping sets options] artikeln.

## <a name="unsupported-system-functions"></a>Funktioner som inte stöds
Det finns även vissa systemfunktioner som inte stöds. Några av de huvudsakliga som kanske vanligtvis används i datalagret är:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Vissa av dessa problem går att arbeta runt.

## <a name="rowcount-workaround"></a>@@ROWCOUNT lösning
Att kringgå avsaknaden av stöd för @@ROWCOUNT, skapa en lagrad procedur som ska hämta senaste radantalet från sys.dm_pdw_request_steps och sedan köra `EXEC LastRowCount` efter en DML-instruktionen.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Nästa steg
En fullständig lista över alla T-SQL-uttryck som stöds finns i [Transact-SQL-ämnen][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
