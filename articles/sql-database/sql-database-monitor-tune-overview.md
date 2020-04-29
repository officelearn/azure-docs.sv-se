---
title: Övervakning och prestandajustering
description: En översikt över funktioner och metoder för övervakning och prestanda justering i Azure SQL Database.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268735"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Övervakning och prestanda justering i Azure SQL Database

Om du vill övervaka prestanda för en databas i Azure SQL Database börjar du med att övervaka de processor-och IO-resurser som används av arbets belastningen i förhållande till den nivå av databas prestanda som du valde när du väljer en viss tjänst nivå och prestanda nivå. För att åstadkomma detta genererar Azure SQL Database resurs mått som kan visas i Azure Portal eller genom att använda något av dessa SQL-hanterings verktyg: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) eller [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL Database innehåller ett antal databas rådgivare för enkla och grupperade databaser för att tillhandahålla intelligenta prestanda justerings rekommendationer och automatiska justerings alternativ för att förbättra prestanda. Dessutom visar Query Performance Insight information om de frågor som ansvarar för den mest CPU-och i/o-användningen för enskilda databaser och databaser i pooler.

Azure SQL Database ger ytterligare övervaknings-och justerings funktioner som backas upp av artificiell intelligens för att hjälpa dig att felsöka och maximera prestanda för dina databaser och lösningar. Du kan välja att konfigurera [strömnings exporten](sql-database-metrics-diag-logging.md) av dessa [Intelligent Insights](sql-database-intelligent-insights.md) och andra SQL Database resurs loggar och mät värden till en av flera destinationer för förbrukning och analys, särskilt med [SQL Analytics](../azure-monitor/insights/azure-sql.md)). Azure SQL-analys är en avancerad lösning för moln övervakning för att övervaka prestanda för alla dina Azure SQL-databaser i stor skala och över flera prenumerationer i en enda vy. För en lista över loggar och mått som du kan exportera, se [diagnostisk telemetri för export](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Slutligen har SQL sina egna funktioner för övervakning och diagnostik med [SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) och [vyer för dynamisk hantering (DMV: er)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Se [övervakning med DMV: er](sql-database-monitoring-with-dmvs.md) för skript för att övervaka ett antal prestanda problem.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Övervaknings-och justerings funktioner i Azure Portal

I Azure Portal Azure SQL Database tillhandahålla övervakning av resurs mått för alla distributions typer. Ytterligare för databaser med enkel och pool, databas rådgivare och Query Performance Insight ger rekommendationer för att ställa frågor och ställa frågor till prestanda analys. I Azure Portal kan du slutligen aktivera automatisk för logiska servrar och deras enskilda databaser och databaser i pooler.

### <a name="sql-database-resource-monitoring"></a>SQL Database resurs övervakning

Du kan snabbt övervaka följande resurs mått i Azure Portal i vyn **mått** :

- **DTU-användning**

  Kontrol lera om en databas eller elastisk pool når 100 procent av DTU-användningen under en längre tids period. Hög DTU-användning anger att arbets belastningen kan kräva fler CPU-eller i/o-resurser. Det kan också indikera frågor som måste optimeras.
- **CPU-användning**

  Kontrol lera om en databas, elastisk pool eller hanterad instans når 100 procent av CPU-användningen under en längre tids period. Hög CPU visar att arbets belastningen kan kräva fler CPU-eller i/o-resurser. Det kan också indikera frågor som måste optimeras.
- **I/o-användning**

  Kontrol lera om en databas, elastisk pool eller hantera instans når IO-gränserna för det underliggande lagrings utrymmet. Hög i/o-användning anger att arbets belastningen kan kräva fler CPU-eller i/o-resurser. Det kan också indikera frågor som måste optimeras.

  ![Resurs mått](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Databas rådgivare
"Azure SQL Database innehåller [databas rådgivare](sql-database-advisor.md) som ger rekommendationer för prestanda justering för enskilda databaser och databaser i pooler. Dessa rekommendationer är tillgängliga i Azure Portal och med [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor). Du kan också aktivera [Automatisk justering](sql-database-automatic-tuning.md) så att SQL Database automatiskt kan implementera dessa justerings rekommendationer.

### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) visar prestanda i Azure Portal för de vanligaste och mest använda frågorna för enskilda databaser och databaser i pooler.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generera intelligenta utvärderingar av prestanda problem

Azure SQL Database [intelligent Insights](sql-database-intelligent-insights.md) använder inbyggd intelligens för att kontinuerligt övervaka databas användningen via artificiell intelligens och identifiera störande händelser som orsakar dåliga prestanda. Intelligent Insights identifierar automatiskt prestanda problem med databaser i Azure SQL Database baserat på vänte tider, fel eller tids gränser för frågekörning. När den har identifierats utförs en detaljerad analys som genererar en resurs logg (kallas SQLInsights) med en [intelligent utvärdering av problemen](sql-database-intelligent-insights-troubleshoot-performance.md). Den här utvärderingen består av en rotor Saks analys av databasens prestanda problem och, om möjligt, rekommendationer för prestanda förbättringar.

Intelligent Insights är en unik funktion i Azure inbyggda intelligens som ger följande värde:

- Proaktiv övervakning
- Skräddarsydda prestanda insikter
- Tidig identifiering av databas prestanda försämring
- Rotor Saks analys av problem som upptäckts
- Rekommendationer för prestanda förbättring
- Skala ut kapacitet på hundratals tusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägande kostnaden

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Aktivera strömnings export av mått och resurs loggar

Du kan aktivera och konfigurera den [strömmande exporten av Diagnostic-telemetri](sql-database-metrics-diag-logging.md) till en av flera destinationer, inklusive intelligent Insights resurs loggen. Använd [SQL Analytics](../azure-monitor/insights/azure-sql.md) och andra funktioner för att använda denna ytterligare diagnostiska telemetri för att identifiera och lösa prestanda problem.

Du konfigurerar diagnostikinställningar till att strömma kategorier av mått och resurs loggar för enskilda databaser, databaser i pooler, elastiska pooler, hanterade instanser och instans databaser till någon av följande Azure-resurser.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics arbets yta i Azure Monitor

Du kan strömma mått och resurs loggar till en [Log Analytics arbets yta i Azure Monitor](../azure-monitor/platform/resource-logs-collect-workspace.md). Data som strömmas här kan användas av [SQL Analytics](../azure-monitor/insights/azure-sql.md), som är en övervaknings lösning för endast molnet som ger intelligent övervakning av dina databaser som innehåller prestanda rapporter, aviseringar och rekommendationer. Data som strömmas till en Log Analytics arbets yta kan analyseras med andra övervaknings data som samlas in och du kan också använda andra Azure Monitor funktioner, till exempel aviseringar och visualiseringar.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Du kan strömma mått och resurs loggar till [Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md). Att strömma Diagnostic-telemetri till Event Hubs för att tillhandahålla följande funktioner:

- **Strömma loggar till loggnings-och telemetri system från tredje part**

  Strömma alla dina mått och resurs loggar till en enda händelse hubb för att skicka loggdata till en SIEM-eller Log Analytics-verktyg från tredje part.
- **Bygg en anpassad telemetri-och loggnings plattform**

  Med händelse hubbarna med hög skalbarhet för publicerings prenumerationer kan du samla in mått och resurs loggar på ett flexibelt sätt i en anpassad telemetri-plattform. Mer information finns i [utforma och ändra storlek på en plattform för global skalnings telemetri på Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Visa tjänstens hälsa genom att strömma data till Power BI**

  Använd Event Hubs, Stream Analytics och Power BI för att omvandla dina diagnostikdata till nära real tids insikter om dina Azure-tjänster. Mer information om den här lösningen finns i [Stream Analytics och Power BI: en analys instrument panel i real tid för att strömma data](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) .

### <a name="azure-storage"></a>Azure Storage

Strömma mått och resurs loggar till [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md). Använd Azure Storage för att arkivera stora mängder diagnostiska telemetri för en bråkdel av kostnaden för de föregående två strömnings alternativen.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Använda utökade händelser i SQL Database-motorn

Dessutom kan du använda [utökade händelser](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) i SQL för ytterligare avancerad övervakning och fel sökning. Arkitekturen för utökade händelser gör det möjligt för användare att samla in så mycket eller så lite data som behövs för att felsöka eller identifiera ett prestanda problem. Information om hur du använder utökade händelser i SQL Database finns i [utökade händelser i SQL Database](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om intelligenta prestanda rekommendationer för enskilda databaser och grupperade databaser finns i [prestanda rekommendationer för Database Advisor](sql-database-advisor.md).
- Mer information om automatisk övervakning av databas prestanda med automatiserad diagnostik och rotor Saks analys av prestanda problem finns i [Azure SQL intelligent Insights](sql-database-intelligent-insights.md).
'''''''''