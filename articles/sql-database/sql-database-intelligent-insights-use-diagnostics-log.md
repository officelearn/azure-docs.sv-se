---
title: Intelligent Insights prestanda diagnostikloggen – Azure SQL Database | Microsoft Docs
description: Intelligent Insights tillhandahåller en diagnostiklogg över Azure SQL Database-prestandaproblem
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: a2b4f85ac9c62c93e06f73090b6b144ff79319d1
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561883"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Använda diagnostikloggen för Intelligent Insights Azure SQL Database-prestanda

Den här sidan innehåller information om hur du använder Azure SQL Database prestanda diagnostik loggen genereras av [smarta insikter](sql-database-intelligent-insights.md), dess format och de data som den innehåller för anpassad utveckling behöver. Du kan skicka den här diagnostikloggen [Azure Log Analytics](../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), eller en lösning från tredje part för anpassad DevOps avisering och rapportering funktioner.

## <a name="log-header"></a>Log-huvud

Diagnostikloggen använder standard JSON-format för att mata ut smarta insikter resultat. Exakta kategori-egenskapen för att komma åt en logg med smarta insikter är det fasta värdet ”SQLInsights”.

Rubriken på loggen är vanligt och består av tidsstämpeln (TimeGenerated) som visar när en post skapades. Den innehåller också ett resurs-ID (resurs-ID) som refererar till den specifika SQL-databasen på posten som relaterar till. Kategori (kategori), nivå (nivå) och åtgärdsnamn på (OperationName) är fasta egenskaper vars värden inte ändras. De anger att loggposten visas endast i informationssyfte och att det kommer från smarta insikter (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Problem-ID och databasen som påverkas

Egenskapen problemet identifiering (issueId_d) ger ett sätt att spåra unikt prestandaproblem förrän löst. Flera händelseposter i loggen rapporterar status för samma problem kommer att dela samma problem-ID.

Tillsammans med ärende-ID rapporterar diagnostikloggen start (intervalStartTime_t)- och slut (intervalEndTme_t) tidsstämplar händelse som rör ett problem som rapporteras i diagnostikloggen för.

Egenskapen elastisk pool (elasticPoolName_s) anger vilka elastisk pool databasen med ett problem som hör till. Om databasen inte är en del av en elastisk pool, har den här egenskapen inget värde. Databasen som ett problem har identifierats lämnas i databas-namnegenskapen (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Identifierade problem

Nästa avsnitt av Intelligent Insights prestandaloggen innehåller prestandaproblem som hittades via inbyggd artificiell intelligens. Identifieringar detta i egenskaper i JSON-diagnostikloggen. Dessa identifieringar bestå av kategorin på ett problem, effekten av problemet, frågor som påverkas och mått. Egenskaper för identifieringar kan innehålla flera prestandaproblem som har identifierats.

Identifierade prestandaproblem rapporteras med följande identifieringar egenskapen struktur:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Flashminnet prestandamönster och den information som utdata in diagnostik finns i följande tabell.

### <a name="detection-category"></a>Identifiering av kategori

Egenskapen kategori (kategori) beskriver kategorin för flashminnet prestandamönster. Se tabellen nedan för alla möjliga sorters flashminnet prestandamönster. Mer information finns i [Felsöka prestandaproblem för databasen med intelligenta insikter](sql-database-intelligent-insights-troubleshoot-performance.md).

Prestandaproblemet som har identifierats, information för utdata i diagnostiken loggfilen skiljer sig därför.

| Prestandamönster för flashminnet | Information för utdata |
| :------------------- | ------------------- |
| Nådde resursbegränsningar | <li>Resurser som påverkas</li><li>Fråga hashvärden</li><li>Resursen förbrukning procent</li> |
| Ökning av arbetsbelastning | <li>Antal frågor vars körning ökat</li><li>Fråga hash-värden för frågor med största bidraget till den arbetsbelastning ökade</li> |
| Minnesbelastning | <li>Minne under fulltextinitieringen</li> |
| Låsning | <li>Påverkas fråga hashvärden</li><li>Blockera fråga hashvärden</li> |
| Ökad MAXDOP | <li>Fråga hashvärden</li><li>CXP väntetider</li><li>Väntetid</li> |
| Pagelatch konkurrens | <li>Fråga hash-värden för frågor som orsakar konkurrens</li> |
| Index som saknas | <li>Fråga hashvärden</li> |
| Ny fråga | <li>Fråge-hash för de nya frågorna</li> |
| Ovanlig vänta statistik | <li>Ovanlig vänta typer</li><li>Fråga hashvärden</li><li>Fråga väntetider</li> |
| TempDB konkurrens | <li>Fråga hash-värden för frågor som orsakar konkurrens</li><li>Fråga attribution för övergripande databasen pagelatch konkurrens väntetiden [%]</li> |
| Elastisk pool DTU brist | <li>Elastisk pool</li><li>Främsta DTU förbrukar databas</li><li>Procent av pool-DTU som används av övre konsumenten</li> |
| Planera Regression | <li>Fråga hashvärden</li><li>Bra planering ID: N</li><li>Felaktig plan ID: N</li> |
| Databasbegränsade konfigurationen ändras | <li>Databasbegränsade konfigurationsändringar jämfört med standardvärden</li> |
| Långsam klienten | <li>Fråga hashvärden</li><li>Väntetid</li> |
| Priser för nedgradering av nivå | <li>Textmeddelande</li> |

### <a name="impact"></a>Påverkan

Effekten (inverkan) egenskapen beskriver hur mycket ett identifierade beteende bidragit till problemet som har en databas. Påverkan på mellan 1 och 3, med 3 som högsta bidrag, 2 som Måttlig och 1 som lägst bidrag. Påverkan värdet kan användas som indata för anpassade aviseringar automation, beroende på dina specifika behov. Frågor om egenskaper påverkas (QueryHashes) tillhandahåller en lista över frågan hashvärden som har påverkats av en viss identifiering.

### <a name="impacted-queries"></a>Frågor som påverkas

Nästa avsnitt av Intelligent Insights loggen innehåller information om specifika frågor som har påverkats av de identifierade prestandaproblem. Den här informationen anges som en matris med objekt som är inbäddade i egenskapen impact_s. Hur egenskapen påverkan består av entiteter och mått. Entiteter som refererar till en viss fråga (typ: Fråga). Unikt fråge-hash anges under egenskapen value (värde). Dessutom är alla frågor uppges följt av ett mått och ett värde som indikerar ett identifierade prestandaproblem.

I exemplet nedan log fråga med hash-0x9102EXZ4 har identifierats som har en ökad varaktighet för körning (mått: DurationIncreaseSeconds). Värdet för 110 sekunder anger att den här specifika frågan tog längre 110 sekunder för att utföra. Eftersom flera frågor kan identifieras, kan i det här avsnittet för speciell logg innehålla flera poster i frågan.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Mått

Måttenhet för varje mått som rapporteras tillhandahålls under egenskapen metric (mått) med de möjliga värdena för sekunder, antal och procentandel. Värdet för ett uppmätt mått rapporteras i egenskapen value (värde).

Egenskapen DurationIncreaseSeconds ger mätenhet i sekunder. Måttenhet CriticalErrorCount är ett tal som representerar ett antal fel.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Rot orsak analys och rekommendationer till förbättringar

Den sista delen av Intelligent Insights prestandaloggen gäller automatiserade Rotorsaksanalys av identifierade försämring prestandaproblemet. Informationen visas i human-vänlig ordflöde i egenskapen root orsak analysis (rootCauseAnalysis_s). Rekommendationer om prestandaförbättring ingår i loggen, där det är möjligt.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Du kan använda intelligenta insikter prestandaloggen med [Azure Log Analytics]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) eller en lösning från tredje part för anpassad DevOps varningar och rapporter.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [smarta insikter](sql-database-intelligent-insights.md) begrepp.
- Lär dig hur du [Felsöka prestandaproblem för Azure SQL Database med intelligenta insikter](sql-database-intelligent-insights-troubleshoot-performance.md).
- Lär dig hur du [övervaka Azure SQL Database med hjälp av Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Lär dig hur du [samla in och använda loggdata från resurserna i Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



