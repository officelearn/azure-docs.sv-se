---
title: Prestandajustering med cachelagring av resultatuppsättningar
description: Översikt över cachelagring av resultatuppsättning för SQL Analytics i Azure Synapse Analytics
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
ms.openlocfilehash: 0c2190c29054301a8e21a9a27eb078802fbc9612
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350864"
---
# <a name="performance-tuning-with-result-set-caching"></a>Prestandajustering med cachelagring av resultatuppsättningar  
När cachelagring av resultatuppsättning är aktiverat cachelagrar SQL Analytics automatiskt frågeresultat i användardatabasen för upprepad användning.  Detta gör att efterföljande frågekörningar kan hämta resultat direkt från den beständiga cachen så att omdämning inte behövs.   Cachelagring av resultat ger resultat förbättrar frågeprestanda och minskar beräkningsresursanvändningen.  Dessutom använder frågor som använder cachelagrade resultatuppsättning inte några samtidighetsplatser och räknas därför inte mot befintliga samtidighetsgränser. För säkerhet kan användare bara komma åt de cachelagrade resultaten om de har samma behörigheter för dataåtkomst som de användare som skapar de cachelagrade resultaten.  

## <a name="key-commands"></a>Kommandon för nyckel
[Aktivera/av resultatuppsättning cachelagring för en användardatabas](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Aktivera/av resultatuppsättning för en session](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Kontrollera storleken på cachelagrade resultatuppsättning](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Rensa cacheminnet](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Vad är inte cachelagrat  

När cachelagring av resultatuppsättning har aktiverats för en databas cachelagras resultaten för alla frågor tills cacheminnet är fullt, med undantag för dessa frågor:
- Frågor som använder icke-deterministiska funktioner som DateTime.Now()
- Frågor med användardefinierade funktioner
- Frågor som använder tabeller med säkerhet på radnivå eller kolumnnivå aktiverad
- Frågor som returnerar data med radstorlek som är större än 64KB

> [!IMPORTANT]
> Åtgärderna för att skapa resultatuppsättningscachen och hämta data från cacheminnet sker på kontrollnoden för en SQL Analytics-instans.
> När cachelagring av resultatuppsättning är aktiverat kan frågor som returnerar stora resultatuppsättningar (till exempel >1 miljon rader) orsaka hög CPU-användning på kontrollnoden och sakta ned det övergripande frågesvaret på instansen.  Dessa frågor används ofta under datautforskning eller ETL-åtgärder. För att undvika att framtämda kontrollnoden och orsaka prestandaproblem bör användare inaktivera avresultatuppsättningscachening i databasen innan de körs av dessa typer av frågor.  

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

Kör det här kommandot för att kontrollera om en fråga har körts med en resultatcacheträff eller miss. Om det finns en cache träff, kommer result_cache_hit tillbaka 1.

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
