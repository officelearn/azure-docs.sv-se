---
title: Prestanda justering med cachelagring av resultat uppsättningar
description: Översikt över cachelagring av resultat uppsättningar för Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: seo-lt-2019
ms.openlocfilehash: 461320b9c3ed48176fb60fe695704c582edcd552
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692948"
---
# <a name="performance-tuning-with-result-set-caching"></a>Prestanda justering med cachelagring av resultat uppsättningar  
När cachelagring av resultat uppsättningar är aktiverat cachelagrar Azure SQL Data Warehouse automatiskt frågeresultat i användar databasen för upprepad användning.  Detta gör att efterföljande fråge körningar kan hämta resultat direkt från det sparade cacheminnet så att omberäkning inte behövs.   Cachelagring av resultat uppsättningar förbättrar prestanda för frågor och minskar användningen av beräknings resurser.  Dessutom använder frågor som använder cachelagrade resultat uppsättningar inte några samtidiga platser och räknas därför inte över mot befintliga samtidighets gränser. Användare kan bara komma åt de cachelagrade resultaten om de har samma data åtkomst behörigheter som de användare som skapar de cachelagrade resultaten.  

## <a name="key-commands"></a>Nyckel kommandon
[Aktivera/inaktivera cachelagring av resultat uppsättningar för en användar databas](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Aktivera/inaktivera cachelagring av resultat uppsättningar för en session](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Kontrol lera storleken på den cachelagrade resultat uppsättningen](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Rensa cacheminnet](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Vad är inte cachelagrat  

När cachelagring av resultat uppsättningar har Aktiver ATS för en databas cachelagras resultaten för alla frågor tills cachen är full, förutom följande frågor:
- Frågor som använder icke-deterministiska funktioner som DateTime. Now ()
- Frågor med användardefinierade funktioner
- Frågor som använder tabeller med säkerhet på radnivå eller på kolumn nivå har Aktiver ATS
- Frågor som returnerar data med en rad storlek som är större än 64 KB

> [!IMPORTANT]
> Åtgärderna för att skapa cache för resultat uppsättning och hämta data från cachen sker på noden kontroll i en informations lager instans. När cachelagring av resultat uppsättningar är aktiverat, kan körnings frågor som returnerar stor resultat uppsättning (till exempel > 1 miljon rader) orsaka hög CPU-användning på noden kontroll och sakta ned det övergripande svaret på instansen.  Frågorna används ofta vid data utforskning eller ETL-åtgärder. För att undvika att kontrol lera noden och orsaka prestanda problem bör användarna stänga av resultat uppsättningens cachelagring i databasen innan du kör dessa typer av frågor.  

Kör den här frågan för den tid som krävs för cachelagring av resultat uppsättningar för en fråga:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Här är ett exempel på utdata för en fråga som körs med mellanlagring av resultat uppsättning inaktive rad.

![Fråga – steg-med-RSC-inaktive rad](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Här är ett exempel på utdata för en fråga som körs med cachelagring av resultat uppsättningar aktiverat.

![Fråga – steg-för-RSC-aktiverat](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>När cachelagrade resultat används

Den cachelagrade resultat uppsättningen återanvänds för en fråga om alla följande krav är uppfyllda:
- Användaren som kör frågan har åtkomst till alla tabeller som refereras i frågan.
- Det finns en exakt matchning mellan den nya frågan och den föregående frågan som genererade resultat uppsättningens cacheminne.
- Det finns inga data eller schema ändringar i tabellerna där den cachelagrade resultat uppsättningen genererades.

Kör det här kommandot för att kontrol lera om en fråga kördes med en resultat-cacheträffar eller missar. Om det finns en cacheträff, kommer result_cache_hit att returnera 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Hantera cachelagrade resultat 

Den maximala storleken för cache för resultat uppsättning är 1 TB per databas.  De cachelagrade resultaten blir automatiskt ogiltiga när den underliggande frågans data ändras.  

Cache-avtagningen hanteras av Azure SQL Data Warehouse automatiskt enligt det här schemat: 
- Var 48: e timme om resultat uppsättningen inte har använts eller har ogiltig förklarats. 
- När cachen för resultat uppsättningen närmar sig den maximala storleken.

Användare kan manuellt tömma hela resultat uppsättningens cacheminne genom att använda något av följande alternativ: 
- Inaktivera funktionen för cachelagring av resultat uppsättningar för databasen 
- Kör DBCC DROPRESULTSETCACHE medan du är ansluten till databasen

Om du pausar en databas töms inte cachelagrad resultat uppsättning.  

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling](sql-data-warehouse-overview-develop.md). 
