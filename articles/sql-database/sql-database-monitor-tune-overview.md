---
title: Övervakning och prestandajustering
description: En översikt över funktioner och metoder för övervakning och prestandajustering i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268735"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Övervakning och prestandajustering i Azure SQL Database

Om du vill övervaka prestanda för en databas i Azure SQL Database börjar du med att övervaka cpu- och IO-resurser som används av din arbetsbelastning i förhållande till den nivå av databasprestanda du valde när du väljer en viss tjänstnivå och prestandanivå. För att åstadkomma detta avger Azure SQL Database resursmått som kan visas i Azure-portalen eller med hjälp av något av dessa SQL-hanteringsverktyg: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

För enstaka och poolade databaser tillhandahåller Azure SQL Database ett antal databasrådgivare för att tillhandahålla intelligenta prestandajusteringsrekommendationer och automatiska justeringsalternativ för att förbättra prestanda. Dessutom visar Query Performance Insight information om de frågor som är ansvariga för mest CPU- och IO-användning för enskilda och poolade databaser.

Azure SQL Database tillhandahåller ytterligare övervaknings- och justeringsfunktioner som backas upp av artificiell intelligens för att hjälpa dig att felsöka och maximera prestanda för dina databaser och lösningar. Du kan välja att konfigurera [direktuppspelningsexport](sql-database-metrics-diag-logging.md) av dessa [intelligenta insikter](sql-database-intelligent-insights.md) och andra SQL Database-resursloggar och mått till en av flera destinationer för förbrukning och analys, särskilt med [SQL Analytics](../azure-monitor/insights/azure-sql.md)). Azure SQL Analytics är en avancerad molnövervakningslösning för övervakning av prestanda för alla dina Azure SQL-databaser i stor skala och över flera prenumerationer i en enda vy. En lista över loggar och mått som du kan exportera finns i [diagnostiktelemetri för export](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Slutligen har SQL sina egna övervaknings- och diagnostikfunktioner med [SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) och dynamiska [hanteringsvyer (DMVs)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Se [Övervaka med DV:er](sql-database-monitoring-with-dmvs.md) för skript som ska övervakas för en mängd olika prestandaproblem.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Övervaknings- och justeringsfunktioner i Azure-portalen

I Azure-portalen tillhandahåller Azure SQL Database övervakning av resursmått för alla distributionstyper. För databaser med enstaka och poolade databaser tillhandahåller databasrådgivare och Frågeprestandainsikt frågejusteringsrekommendationer och frågeprestandaanalys. Slutligen kan du i Azure-portalen aktivera automatiska för logiska servrar och deras enstaka och poolade databaser.

### <a name="sql-database-resource-monitoring"></a>Övervakning av SQL-databasresurser

Du kan snabbt övervaka följande resursmått i Azure-portalen i **vyn Mått:**

- **DTU-användning**

  Kontrollera om en databas eller elastisk pool når 100 procent av DTU-användningen under en längre tid. Hög DTU-användning indikerar att din arbetsbelastning kan behöva mer CPU- eller I/I/O-resurser. Det kan också tyda på frågor som måste optimeras.
- **CPU-användning**

  Kontrollera om en databas, elastisk pool eller hanterad instans når 100 procent av CPU-användningen under en längre tid. Hög CPU indikerar att din arbetsbelastning kan behöva mer CPU eller IO-resurser. Det kan också tyda på frågor som måste optimeras.
- **I/o-användning**

  Kontrollera om en databas, elastisk pool eller hanteringsinstans når IO-gränserna för det underliggande lagringsutrymmet. Hög I/O-användning indikerar att din arbetsbelastning kan behöva mer CPU- eller I/I/O-resurser. Det kan också tyda på frågor som måste optimeras.

  ![Resursmått](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Databasrådgivare
' Azure SQL Database innehåller databasrådgivare som tillhandahåller [prestandajusteringsrekommendationer](sql-database-advisor.md) för enskilda och poolade databaser. Dessa rekommendationer är tillgängliga i Azure-portalen samt med hjälp av [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Du kan också aktivera [automatisk justering](sql-database-automatic-tuning.md) så att SQL Database automatiskt kan implementera dessa justeringsrekommendationer.

### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) visar prestanda i Azure-portalen för de vanligaste tidskrävande och längsta frågorna för enskilda och poolade databaser.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generera intelligenta bedömningar av prestandaproblem

Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) använder inbyggd intelligens för att kontinuerligt övervaka databasanvändningen genom artificiell intelligens och identifiera störande händelser som orsakar dåliga prestanda. Intelligent Insights identifierar automatiskt prestandaproblem med databaser i Azure SQL Database baserat på väntetider för körning av frågor, fel eller time-outs. När det har identifierats utförs en detaljerad analys som genererar en resurslogg (kallas SQLInsights) med en [intelligent bedömning av problemen](sql-database-intelligent-insights-troubleshoot-performance.md). Den här utvärderingen består av en grundorsaksanalys av databasprestandaproblemet och, om möjligt, rekommendationer för prestandaförbättringar.

Intelligent Insights är en unik funktion med inbyggd Azure-intelligens som ger följande värde:

- Proaktiv övervakning
- Skräddarsydda prestandainsikter
- Tidig upptäckt av databasprestandaförsämring
- Rotorsaksanalys av upptäckta problem
- Rekommendationer för prestandaförbättring
- Skala ut kapacitet på hundratusentals databaser
- Positiv inverkan på DevOps-resurser och den totala ägandekostnaden

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Aktivera direktuppspelning av mått och resursloggar

Du kan aktivera och konfigurera [direktuppspelningsexport av diagnostiktelemetri](sql-database-metrics-diag-logging.md) till ett av flera mål, inklusive resursloggen Intelligent Insights. Använd [SQL Analytics](../azure-monitor/insights/azure-sql.md) och andra funktioner för att använda den här ytterligare diagnostiska telemetrin för att identifiera och lösa prestandaproblem.

Du konfigurerar diagnostikinställningar för att strömma kategorier av mått och resursloggar för enskilda databaser, poolade databaser, elastiska pooler, hanterade instanser och instansdatabaser till någon av följande Azure-resurser.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics-arbetsyta i Azure-övervakaren

Du kan strömma mått och resursloggar till en [Log Analytics-arbetsyta i Azure Monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Data som strömmas här kan förbrukas av [SQL Analytics](../azure-monitor/insights/azure-sql.md), som är en moln endast övervakningslösning som ger intelligent övervakning av dina databaser som innehåller prestandarapporter, aviseringar och rekommendationer begränsning. Data som strömmas till en Log Analytics-arbetsyta kan analyseras med andra övervakningsdata som samlas in och gör det också möjligt för dig att utnyttja andra Azure Monitor-funktioner som aviseringar och visualiseringar.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Du kan strömma mått och resursloggar till [Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Strömmande diagnostiktelemetri till händelsehubbar för att tillhandahålla följande funktioner:

- **Strömma loggar till loggnings- och telemetrisystem från tredje part**

  Strömma alla dina mått och resursloggar till en enda händelsehubb för att leda loggdata till ett SIEM- eller logganalysverktyg från tredje part.
- **Skapa en anpassad telemetri- och loggningsplattform**

  Den mycket skalbara publiceringsprenumerationen hos händelsehubbar gör att du flexibelt kan använda mått och resursloggar i en anpassad telemetriplattform. Mer information finns i [Designa och dimensionera en telemetriplattform för global skala på Azure Event Hubs.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)
- **Visa tjänstens hälsotillstånd genom att strömma data till Power BI**

  Använd Event Hubs, Stream Analytics och Power BI för att omvandla diagnostikdata till insikter i nära realtid om dina Azure-tjänster. Se [Stream Analytics och Power BI: En instrumentpanel för analys i realtid för direktuppspelning av data](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) för mer information om den här lösningen.

### <a name="azure-storage"></a>Azure Storage

Strömma mått och resursloggar till [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md). Använd Azure-lagring för att arkivera stora mängder diagnostiktelemetri för en bråkdel av kostnaden för de tidigare två direktuppspelningsalternativen.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Använda utökade händelser i SQL-databasmotorn

Dessutom kan du använda [utökade händelser](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) i SQL för ytterligare avancerad övervakning och felsökning. Arkitekturen utökade händelser gör det möjligt för användare att samla in så mycket eller så lite data som krävs för att felsöka eller identifiera ett prestandaproblem. Information om hur du använder utökade händelser i SQL Database finns [i Utökade händelser i SQL Database](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om intelligenta prestandarekommendationer för enstaka och poolade databaser finns i [prestandarekommendationer för databasrådgivare](sql-database-advisor.md).
- Mer information om hur du automatiskt övervakar databasprestanda med automatiserad diagnostik och grundorsaksanalys av prestandaproblem finns i [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
'''''''''