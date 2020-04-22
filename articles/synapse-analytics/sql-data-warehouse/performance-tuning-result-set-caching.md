---
title: Prestandajustering med cachelagring av resultatuppsättningar
description: Översikt över cachelagring av resultatuppsättning för Synapse SQL-pool i Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: eadbe13269ce1259b4560af117f5b15b3b294151
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730590"
---
# <a name="performance-tuning-with-result-set-caching"></a>Prestandajustering med cachelagring av resultatuppsättningar

När cachelagring av resultatuppsättning är aktiverat cachelagrar SQL Analytics automatiskt frågeresultat i användardatabasen för upprepad användning.  Detta gör att efterföljande frågekörningar kan hämta resultat direkt från den beständiga cachen så att omdämning inte behövs.   Cachelagring av resultat ger resultat förbättrar frågeprestanda och minskar beräkningsresursanvändningen.  Dessutom använder frågor som använder cachelagrade resultatuppsättning inte några samtidighetsplatser och räknas därför inte mot befintliga samtidighetsgränser. För säkerhet kan användare bara komma åt de cachelagrade resultaten om de har samma behörigheter för dataåtkomst som de användare som skapar de cachelagrade resultaten.  

## <a name="key-commands"></a>Kommandon för nyckel

[Aktivera/av resultatuppsättning cachelagring för en användardatabas](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Aktivera/av resultatuppsättning cachelagring för en användardatabas](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Aktivera/av resultatuppsättning för en session](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Kontrollera storleken på cachelagrade resultatuppsättning](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Rensa cacheminnet](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Vad är inte cachelagrat  

När cachelagring av resultatuppsättning har aktiverats för en databas cachelagras resultaten för alla frågor tills cacheminnet är fullt, med undantag för dessa frågor:

- Frågor som använder icke-deterministiska funktioner som DateTime.Now()
- Frågor med användardefinierade funktioner
- Frågor som använder tabeller med säkerhet på radnivå eller kolumnnivå aktiverad
- Frågor som returnerar data med radstorlek som är större än 64KB
- Frågor som returnerar stora data i storlek (>10 GB) 

> [!IMPORTANT]
> Åtgärderna för att skapa resultatuppsättningscachen och hämta data från cacheminnet sker på kontrollnoden för en Synapse SQL-poolinstans.
> När cachelagring av resultatuppsättning är aktiverat kan frågor som returnerar stora resultatuppsättningar (till exempel >1 GB) orsaka hög begränsning på kontrollnoden och sakta ned det övergripande frågesvaret på instansen.  Dessa frågor används ofta under datautforskning eller ETL-åtgärder. För att undvika att framtämda kontrollnoden och orsaka prestandaproblem bör användare inaktivera avresultatuppsättningscachening i databasen innan de körs av dessa typer av frågor.  

Kör den här frågan för den tid det tar för resultatuppsättningens cachelagringsåtgärder för en fråga:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Här är ett exempel på utdata för en fråga som körs med resultatuppsättningens cachelagring inaktiverad.

![Frågesteg med-rsc-inaktiverad](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Här är ett exempel på utdata för en fråga som körs med resultatuppsättningscacheaktivt.

![Frågesteg-med-rsc-aktiverad](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>När cachelagrade resultat används

Cachelagrade resultatuppsättning återanvänds för en fråga om alla följande krav är uppfyllda:

- Användaren som kör frågan har åtkomst till alla tabeller som refereras i frågan.
- Det finns en exakt matchning mellan den nya frågan och den föregående frågan som genererade resultatuppsättningscachen.
- Det finns inga data- eller schemaändringar i tabellerna där den cachelagrade resultatuppsättningen genererades från.

Kör det här kommandot för att kontrollera om en fråga har körts med en resultatcacheträff eller miss. Kolumnen result_cache_hit returnerar 1 för cacheträff, 0 för cachemiss och negativa värden av skäl till varför cachelagring av resultatuppsättning inte användes. Mer information finns [i sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Hantera cachelagrade resultat

Den maximala storleken på resultatuppsättningscachen är 1 TB per databas.  De cachelagrade resultaten ogiltigförklaras automatiskt när de underliggande frågedata ändras.  

Cachevräkningen hanteras automatiskt av SQL Analytics enligt det här schemat:

- Var 48:e timme om resultatuppsättningen inte har använts eller har ogiltigförklarats.
- När resultatuppsättningen närmar sig den maximala storleken.

Användare kan tömma hela resultatuppsättningscachen manuellt med något av följande alternativ:

- Inaktivera cachefunktionen för resultatuppsättningen för databasen
- Kör DBCC DROPRESULTSETCACHE när du är ansluten till databasen

Om du pausar en databas töms inte cachelagrade resultatuppsättningen.  

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).
