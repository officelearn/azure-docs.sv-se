---
title: Prestanda justering med cachelagring av resultat uppsättningar | Microsoft Docs
description: Översikt över funktioner
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.openlocfilehash: c659db91b8ca1ad65b00124bed347b8046328d2e
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045001"
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

Frågor med stora resultat uppsättningar (till exempel > 1 000 000 rader) kan uppleva sämre prestanda under den första körningen när resultat-cachen skapas.

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
