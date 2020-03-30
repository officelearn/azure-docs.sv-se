---
title: Intelligent Insights prestandadiagnostik logg
description: Intelligent Insights tillhandahåller en diagnostiklogg med prestandaproblem i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bb62b087451140261aee7aaa2fab0de14ea36283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209454"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Använd prestandadiagnostikloggen för Intelligent Insights Azure SQL Database

Den här sidan innehåller information om hur du använder prestandadiagnostboken för Azure SQL Database som genereras av [Intelligent Insights](sql-database-intelligent-insights.md), dess format och de data som den innehåller för dina anpassade utvecklingsbehov. Du kan skicka den här diagnostikloggen till [Azure Monitor-loggar,](../azure-monitor/insights/azure-sql.md) [Azure Event Hubs,](../azure-monitor/platform/resource-logs-stream-event-hubs.md) [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)eller en tredjepartslösning för anpassade DevOps-aviseringar och rapporteringsfunktioner.

## <a name="log-header"></a>Logga huvud

Diagnostikloggen använder JSON-standardformat för att mata ut intelligenta insikter. Den exakta kategoriegenskapen för åtkomst till en Intelligent Insights-logg är det fasta värdet "SQLInsights".

Loggens rubrik är vanlig och består av tidsstämpeln (TimeGenerated) som visas när en post skapades. Den innehåller också ett resurs-ID (ResourceId) som refererar till den specifika SQL-databas som posten avser. Kategorin (Kategori), nivå (nivå) och operationsnamn (OperationName) är fasta egenskaper vars värden inte ändras. De anger att loggposten är informativ och att den kommer från Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Ärende-ID och databas som påverkas

Egenskapen problemidentifiering (issueId_d) är ett sätt att unikt spåra prestandaproblem tills den har lösts. Flera händelseposter i loggrapportstatus för samma utleverans kommer att dela samma ärende-ID.

Tillsammans med ärende-ID:t rapporterar diagnostikloggen start-(intervalStartTime_t) och sluttidsstämplar (intervalEndTme_t) för den aktuella händelsen som är relaterad till ett problem som rapporteras i diagnostikloggen.

Egenskapen elastisk pool (elasticPoolName_s) anger vilken elastisk pool databasen med ett problem tillhör. Om databasen inte ingår i en elastisk pool har den här egenskapen inget värde. Databasen där ett problem upptäcktes visas i egenskapen databasnamn (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Upptäckta problem

Nästa avsnitt i prestandaloggen för Intelligent Insights innehåller prestandaproblem som upptäcktes genom inbyggd artificiell intelligens. Identifieringar avslöjas i egenskaper i JSON-diagnostikloggen. Dessa identifieringar består av kategorin för ett problem, problemets inverkan, de frågor som påverkas och måtten. Identifieringsegenskaperna kan innehålla flera prestandaproblem som har identifierats.

Identifierade prestandaproblem rapporteras med följande egenskapsstruktur för identifieringar:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Detekterbara prestandamönster och de detaljer som matas ut till diagnostikloggen finns i följande tabell.

### <a name="detection-category"></a>Identifieringskategori

Egenskapen category (category) beskriver kategorin av detekterbara prestandamönster. Se följande tabell för alla möjliga kategorier av detekterbara prestandamönster. Mer information finns i [Felsöka problem med databasens prestanda med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Beroende på vad som identifierats för prestandaproblemet skiljer sig informationen i diagnostikloggfilen åt i enlighet med detta.

| Identifierbara prestandamönster | Detaljer utdata |
| :------------------- | ------------------- |
| Nå resursgränser | <li>Berörda resurser</li><li>Frågehashar</li><li>Procent av resursförbrukning</li> |
| Ökning av arbetsbelastningen | <li>Antal frågor vars körning ökade</li><li>Frågeh hashar av frågor med det största bidraget till arbetsbelastningsökningen</li> |
| Minnestryck | <li>Minnesbiträde</li> |
| Låsning | <li>Berörda frågeh hashar</li><li>Blockera frågeh hashar</li> |
| Ökad MAXDOP | <li>Frågehashar</li><li>CXP väntetider</li><li>Väntetider</li> |
| Pagelatch Påstående | <li>Frågeh hashar av frågor som orsakar konkurrens</li> |
| Index saknas | <li>Frågehashar</li> |
| Ny fråga | <li>Frågehh för de nya frågorna</li> |
| Ovanlig väntestatistik | <li>Ovanliga väntetider</li><li>Frågehashar</li><li>Väntetider för frågor</li> |
| TempDB Påstående | <li>Frågeh hashar av frågor som orsakar konkurrens</li><li>Frågeattribution till den övergripande väntetiden för databas pagelatch-konkurrens [%]</li> |
| Elastisk pool DTU Brist | <li>Elastisk pool</li><li>Topp DTU-tidskrävande databas</li><li>Procent av pool DTU som används av den översta konsumenten</li> |
| Planera regression | <li>Frågehashar</li><li>Bra plan-ID</li><li>Felaktiga plan-ID:er</li> |
| Ändring av konfigurationsvärde för databasomfattning | <li>Konfigurationsändringar i databasomfattning jämfört med standardvärdena</li> |
| Långsam klient | <li>Frågehashar</li><li>Väntetider</li> |
| Nedgradering av prisnivån | <li>Meddelande om text</li> |

### <a name="impact"></a>Påverkan

Egenskapen Impact (impact) beskriver hur mycket ett upptäckt beteende bidrog till problemet som en databas har. Effekterna varierar från 1 till 3, med 3 som det högsta bidraget, 2 som måttlig, och 1 som det lägsta bidraget. Effektvärdet kan användas som indata för anpassad automatisering av aviseringar, beroende på dina specifika behov. Egenskapsfrågorna som påverkas (QueryHashes) innehåller en lista över frågehhes som påverkades av en viss identifiering.

### <a name="impacted-queries"></a>Påverkade frågor

I nästa avsnitt i loggen för Intelligent Insights finns information om särskilda frågor som påverkades av de identifierade prestandaproblemen. Den här informationen lämnas ut som en matris med objekt som är inbäddade i egenskapen impact_s. Egenskapen Impact består av entiteter och mått. Entiteter refererar till en viss fråga (Typ: Fråga). Den unika frågehhen avslöjas under egenskapen Value (Value). Dessutom följs var och en av de frågor som lämnas av ett mått och ett värde, som anger ett upptäckt prestandaproblem.

I följande loggexempel upptäcktes frågan med hash 0x9102EXZ4 för att ha en ökad varaktighet för körning (Mått: DurationIncreaseSeconds). Värdet på 110 sekunder anger att den här frågan tog 110 sekunder längre tid att köra. Eftersom flera frågor kan identifieras kan det här loggavsnittet innehålla flera frågeposter.

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

Måttenheten för varje mätvärde som rapporteras tillhandahålls under egenskapen mått (mått) med möjliga värden för sekunder, tal och procent. Värdet för ett uppmätt mått rapporteras i egenskapen värde (värde).

Egenskapen DurationIncreaseSeconds ger måttenheten på några sekunder. CriticalErrorCount-måttenheten är ett tal som representerar ett felantal.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Rekommendationer för rotorsaksanalys och förbättring

Den sista delen av prestandaloggen för Intelligent Insights gäller den automatiska grundorsaksanalysen av det identifierade prestandaförsämringsproblemet. Informationen visas i människovänlig verbiage i egenskapen för grundorsaksanalys (rootCauseAnalysis_s). Förbättringsrekommendationer ingår där det är möjligt i loggen.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Du kan använda prestandaloggen för Intelligent Insights med [Azure Monitor-loggar]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) eller en tredjepartslösning för anpassade DevOps-aviseringar och rapporteringsfunktioner.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [intelligenta insikter begrepp.](sql-database-intelligent-insights.md)
- Lär dig hur du [felsöker prestandaproblem i Azure SQL Database med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Lär dig hur du [övervakar Azure SQL Database med hjälp av Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Lär dig hur du [samlar in och använder loggdata från dina Azure-resurser](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).
