---
title: Migrera dina SQL-kod till SQL Data Warehouse | Microsoft Docs
description: Tips för att migrera SQL-kod till Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: b17e8e306c01bef4c58658b35f3a67d0e721633c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527461"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrera dina SQL-kod till SQL Data Warehouse
Den här artikeln förklarar kodändringar behöver du antagligen se när du migrerar din kod från en annan databas till SQL Data Warehouse. Vissa funktioner i SQL Data Warehouse förbättrar prestanda eftersom de är avsedda att fungera i ett distribuerat sätt. För att upprätthålla prestanda och skalning så är vissa funktioner dock också inte tillgänglig.

## <a name="common-t-sql-limitations"></a>Vanliga T-SQL-begränsningar
I följande lista sammanfattas de vanligaste funktionerna som inte har stöd för SQL Data Warehouse. Länkarna om du vill lösningar för funktionerna som inte stöds:

* [ANSI kopplingar uppdateringar][ANSI joins on updates]
* [ANSI-kopplingar på borttagningar][ANSI joins on deletes]
* [merge-instruktion][merge statement]
* flera databaser kopplingar
* [Markörer][cursors]
* [INSERT... EXEC][INSERT..EXEC]
* Output-sats
* infogade användardefinierade funktioner
* flera instruktioner funktioner
* [vanliga tabelluttryck](#Common-table-expressions)
* [rekursiva cte (CTE)] (#Recursive-common-table-expressions-(CTE)
* CLR-funktioner och procedurer
* $partition funktion
* tabellvariabler
* tabellen värdet parametrar
* distribuerade transaktioner
* Commit / rollback arbete
* Spara transaktionen
* körningen kontexter (EXECUTE AS)
* [Group by-satser med Samlad / kub / grupperingsalternativ uppsättningar][group by clause with rollup / cube / grouping sets options]
* [kapslingsnivåer utöver 8][nesting levels beyond 8]
* [uppdatering i vyer][updating through views]
* [användning av väljer för variabeltilldelning][use of select for variable assignment]
* [Ingen MAX datatyp för dynamisk SQL-strängar][no MAX data type for dynamic SQL strings]

De flesta av dessa begränsningar kan Lyckligtvis arbetat runt. Förklaringar finns i de relevanta development artiklar som anges ovan.

## <a name="supported-cte-features"></a>CTE-funktioner som stöds
Cte (cte-referenser) stöds delvis i SQL Data Warehouse.  Följande CTE-funktioner stöds:

* En CTE kan anges i en SELECT-instruktion.
* En CTE kan anges i instruktionen CREATE VIEW.
* En CTE kan anges i instruktionen Skapa tabell AS Välj (CTAS).
* En CTE kan anges i instruktionen skapa REMOTE TABLE AS Välj (CRTAS).
* En CTE kan anges i instruktionen Skapa extern tabell AS Välj (CETAS).
* En fjärrtabell kan refereras från en CTE.
* En extern tabell kan refereras från en CTE.
* Flera CTE frågan definitioner kan definieras i en CTE.

## <a name="cte-limitations"></a>CTE begränsningar
Vanliga tabelluttryck har vissa begränsningar i SQL Data Warehouse, inklusive:

* En CTE måste följas av en enstaka SELECT-instruktion. INSERT-, UPDATE-, DELETE och MERGE-instruktioner stöds inte.
* Ett vanligt tabelluttryck som innehåller referenser till sig själv (ett rekursivt vanligt tabelluttryck) stöds inte (se nedan avsnitt).
* Ange fler än en med-satsen i en CTE är inte tillåtet. Om en CTE_query_definition innehåller en underfråga, kan till exempel denna underfråga innehåller en kapslad med-sats som definierar en annan CTE.
* En ORDER BY-sats kan inte användas i CTE_query_definition, utom när en TOP-instruktion har angetts.
* När en CTE används i en instruktion som är en del av en batch måste instruktionen innan den följas av ett semikolon.
* När de används i rapporter med sp_prepare fungerar cte-referenser på samma sätt som andra SELECT-satser i PDW. Men om cte-referenser används som en del av CETAS förberedda av sp_prepare beteendet kan skjuta upp från SQL Server och andra PDW-instruktioner på grund av hur bindning har implementerats för sp_prepare. Om väljer att refererar till CTE använder en fel kolumn som inte finns i CTE, sp_prepare skickas utan att identifiera felet, men felet uppstod under sp_execute i stället.

## <a name="recursive-ctes"></a>Rekursiva cte-referenser
Rekursiva cte-referenser stöds inte i SQL Data Warehouse.  Migrering av rekursiva CTE kan vara ganska komplicerat och på bästa sätt är att dela upp det i flera steg. Du kan normalt använda en loop och fylla i en tillfällig tabell som du iterera över rekursiva tillfälliga frågor. Du kan sedan returnera data som en enda resultatmängd när den temporära tabellen fylls i. Ett liknande sätt har använts för att lösa `GROUP BY WITH CUBE` i den [group by-satser med Samlad / kub / grupperingsalternativ anger] [ group by clause with rollup / cube / grouping sets options] artikel.

## <a name="unsupported-system-functions"></a>Systemfunktioner som inte stöds
Det finns även vissa systemfunktioner som inte stöds. Några av de huvudsakliga som kanske vanligtvis används i informationslager är:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Vissa av dessa problem kan du arbetade runt.

## <a name="rowcount-workaround"></a>@@ROWCOUNT lösning
Undvika bristande stöd för @@ROWCOUNT, skapa en lagrad procedur som hämtar det senaste antalet rader från sys.dm_pdw_request_steps och sedan köra `EXEC LastRowCount` efter en DML-instruktionen.

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
En fullständig lista över alla T-SQL-instruktioner som stöds finns i [Transact-SQL-avsnitt][Transact-SQL topics].

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
