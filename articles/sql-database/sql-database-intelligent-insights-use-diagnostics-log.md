---
title: Intelligent insikter prestanda diagnostik logg - Azure SQL Database | Microsoft Docs
description: "Intelligent insikter ger en diagnostik logg över Azure SQL Database prestandaproblem"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 358986f58c431aebfe7b41daa8c40ba641dc408a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Använd Intelligent insikter Azure SQL Database prestanda diagnostik loggen

Den här sidan innehåller information om hur du använder Azure SQL Database prestanda diagnostik loggen genereras av [Intelligent insikter](sql-database-intelligent-insights.md), dess format och de data som den innehåller för din anpassade utvecklingen. Du kan skicka loggen till diagnostik [Azure logganalys](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), eller en fristående lösning för anpassade DevOps avisering och rapportering funktioner.

## <a name="log-header"></a>Log-huvud

Diagnostik-loggen använder standard JSON-format till utdata Intelligent insikter resultaten. Egenskapen exakt kategori för att komma åt en Intelligent insikter logg är det fasta värdet ”SQLInsights”.

Rubriken på loggen är vanligt och består av tidsstämpeln (TimeGenerated) som visar när en post har skapats. Den innehåller också en resurs-ID (ResourceId) som refererar till viss SQL-databasen transaktionen avser. Kategori (kategori), nivån (nivån) och åtgärdsnamn (OperationName) är fasta egenskaper vars värden inte ändras. De anger att loggposten är informativt och att det kommer från Intelligent insikter (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Problem-ID och databasen som påverkas

Egenskapen problemet identifiering (issueId_d) ger ett sätt att spåra prestandaproblem unikt tills de är löst. Intelligent insikter registrerar varje problemet livscykel som ”aktiv”, ”verifiera” eller ”klar”. Via respektive fas status registrera Intelligent insikter flera händelseposter i loggen. För var och en av dessa poster förblir unika ID-numret på problemet. Intelligent insikter spårar problemet under dess livscykel och genererar en inblick i loggen för diagnostik var 15: e minut.

När ett prestandaproblem har identifierats och för så länge den varar problemet rapporteras som ”aktiv” under statusegenskapen (status_s). När ett identifierade problem minskas har den verifierats och rapporteras som ”verifiera” under statusegenskapen (status_s). Statusegenskapen (status_s) rapporterar problemet som ”Slutför” om problemet inte längre finns.

Tillsammans med problem-ID rapporterar diagnostik-loggen (intervalStartTime_t) för start och slut (intervalEndTme_t) tidsstämplar händelse som rör ett problem som rapporteras i loggen för diagnostik.

Egenskapen elastisk pool (elasticPoolName_s) anger vilka elastiska poolen tillhör databasen med ett problem. Om databasen inte är en del av en elastisk pool kan har den här egenskapen inget värde. Databasen som en problemet upptäcktes lämnas i databas-namnegenskapen (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Identifierade problem

I nästa avsnitt av Intelligent insikter prestanda loggen innehåller prestandaproblem som upptäckts via inbyggda styrs av datorn. Identifieringar detta i egenskaper i loggen för JSON-diagnostik. Dessa identifieringar består av kategorin på ett problem, effekten av problemet, frågor som påverkas och måtten. Egenskaperna identifieringar kan innehålla flera prestandaproblem som har upptäckts.

Identifierade prestandaproblem rapporteras med följande identifieringar egenskapen struktur:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Prestandamönster kan upptäckas och den information som utdata till diagnostik loggen finns i följande tabell.

### <a name="detection-category"></a>Identifiering av kategori

Egenskapen kategori (kategori) beskriver kategorin kan upptäckas prestandamönster. Se tabellen nedan för alla möjliga kategorier av kan upptäckas prestandamönster. Mer information finns i [Felsök problem med databasprestanda med Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Prestandaproblem som identifierats information för utdata i diagnostiken loggfilen skiljer sig därför.

| Kan upptäckas prestandamönster | Information för utdata |
| :------------------- | ------------------- |
| Nå gränserna | <li>Resurser som påverkas</li><li>Frågan hashvärden</li><li>Resursen förbrukning procent</li> |
| Ökning av arbetsbelastning | <li>Antal frågor vars körning ökade</li><li>Fråga hash-värden för frågor med största bidrar till ökad arbetsbelastning</li> |
| Minnesbelastning | <li>Minnes-Clerk under</li> |
| Låsning | <li>Påverkas frågan hashvärden</li><li>Blockerar frågan hashvärden</li> |
| Ökad MAXDOP | <li>Frågan hashvärden</li><li>Väntetiden för CXP</li><li>Vänta gånger</li> |
| Pagelatch konkurrens | <li>Fråga hash-värden för frågor som orsakar konkurrens</li> |
| Index som saknas | <li>Frågan hashvärden</li> |
| Ny fråga | <li>Fråga hash för de nya frågorna</li> |
| Onormal vänta statistik | <li>Onormal vänta typer</li><li>Frågan hashvärden</li><li>Väntetiden för frågan</li> |
| TempDB konkurrens | <li>Fråga hash-värden för frågor som orsakar konkurrens</li><li>Fråga fördelning på övergripande databasen pagelatch konkurrens väntetiden [%]</li> |
| Elastisk Pool DTU lite | <li>Elastisk pool</li><li>Övre DTU-förbrukning databas</li><li>Procent av poolen DTU som används av övre konsumenten</li> |
| Planera Regression | <li>Frågan hashvärden</li><li>Bra plan ID: N</li><li>Felaktigt plan ID: N</li> |
| Databas-omfattande värdet konfigurationsändring | <li>Databas-omfattande konfigurationsändringar jämfört med standardvärden</li> |
| Långsam klienten | <li>Frågan hashvärden</li><li>Vänta gånger</li> |
| Priser för nedgradering av nivå | <li>Textmeddelande</li> |

### <a name="impact"></a>Påverkan

Inverkan (påverkan) egenskapen beskriver hur mycket identifierade ett beteende som bidrog till problemet som har en databas. Påverkan på intervallet från 1 till 3 med 3 som högsta bidrag, 2 som Måttlig och 1 som lägsta bidrag. Värdet påverkan kan användas som indata för anpassade aviseringar automation, beroende på dina specifika behov. Frågor om egenskaper påverkas (QueryHashes) innehåller en lista över frågan hashvärden som har påverkats av en viss identifiering.

### <a name="impacted-queries"></a>Påverkas frågor

I nästa avsnitt av Intelligent insikter loggen innehåller information om specifika frågor som har påverkats av identifierade prestandaproblem. Den här informationen anges som en matris med objekt som är inbäddade i egenskapen impact_s. Egenskapen påverkan består av enheter och mått. Enheter som refererar till en viss fråga (typ: fråga). Unik frågan hash lämnas under egenskapen value (värde). Alla frågor som visas är dessutom följt av ett mått och ett värde som indikerar ett identifierade prestandaproblem.

I exemplet nedan loggen frågan med hash-0x9102EXZ4 har identifierats som har en ökad varaktighet för körning (mått: DurationIncreaseSeconds). Värdet för 110 sekunder anger att viss frågan tog längre 110 sekunder för att utföra. Eftersom flera frågor kan identifieras kan i det här avsnittet för speciell logg innehålla flera poster för frågan.

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

Måttenheten för varje mått som rapporteras tillhandahålls under egenskapen mått (mått) med möjliga värden i sekunder, antal och procent. Värdet på ett genomtänkt mått rapporteras i egenskapen value (värde).

Egenskapen DurationIncreaseSeconds ger måttenheten i sekunder. Måttenheten CriticalErrorCount är ett tal som representerar ett antal fel.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Rot-orsaken analys och förbättring rekommendationer

Den sista delen av Intelligent insikter prestanda loggen gäller automatiserade Rotorsaksanalys problemets identifierade prestanda försämras. Informationen visas i mänskliga eget ordflöde i egenskapen rot orsak analys (rootCauseAnalysis_s). Förbättring rekommendationer ingår i loggen, där det är möjligt.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Du kan använda Intelligent insikter prestandalogg med [Azure logganalys]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) eller en fristående lösning för anpassade DevOps varningar och rapporteringsfunktioner.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Intelligent insikter](sql-database-intelligent-insights.md) begrepp.
- Lär dig hur du [felsöka Azure SQL Database prestandaproblem med Intelligent insikter](sql-database-intelligent-insights-troubleshoot-performance.md).
- Lär dig hur du [övervaka Azure SQL Database med hjälp av Azure SQL Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql).
- Lär dig hur du [samla in och använda loggdata från resurserna i Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



