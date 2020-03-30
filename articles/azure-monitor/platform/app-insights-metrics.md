---
title: Loggbaserade mätvärden för Azure Application Insights | Microsoft-dokument
description: I den här artikeln visas Azure Application Insights-mått med aggregeringar och dimensioner som stöds. Informationen om loggbaserade mått inkluderar de underliggande Kusto-frågesatserna.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664923"
---
# <a name="application-insights-log-based-metrics"></a>Programinsikter loggbaserade mått

Med application insights-loggbaserade mått kan du analysera hälsotillståndet för dina övervakade appar, skapa kraftfulla instrumentpaneler och konfigurera aviseringar. Det finns två typer av mätvärden:

* [Loggbaserade mätvärden](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) bakom scenen översätts till [Kusto-frågor](https://docs.microsoft.com/azure/kusto/query/) från lagrade händelser.
* Standardmått lagras som föraggregerade [tidsserier.](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)

Eftersom *standardmått* är föraggregerade under insamlingen har de bättre prestanda vid frågetid. Detta gör dem till ett bättre val för instrumentpaneler och i realtidsavisering. De *loggbaserade måtten* har fler dimensioner, vilket gör dem till det överlägsna alternativet för dataanalys och ad hoc-diagnostik. Använd [namnområdesväljaren](metrics-getting-started.md#create-your-first-metric-chart) för att växla mellan loggbaserade och standardmått i [statistikutforskaren](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Tolka och använda frågor från den här artikeln

I den här artikeln visas mått med aggregeringar och dimensioner som stöds. Informationen om loggbaserade mått inkluderar de underliggande Kusto-frågesatserna. För enkelhetens skull använder varje fråga standardvärden för tidsgranularitet, diagramtyp och ibland delningsdimension som förenklar användningen av frågan i Logganalys utan att det behövs ändringar.

När du ritar samma mått i [statistikutforskaren](metrics-getting-started.md)finns det inga standardvärden – frågan justeras dynamiskt baserat på diagraminställningarna:

- Det valda **tidsintervallet** översätts till ytterligare en *där tidsstämpel...* satsen för att bara välja händelser från valt tidsintervall. Ett diagram som visar data för de senaste 24 timmarna innehåller till exempel *frågan | där tidsstämpeln > sedan(24 h)*.

- Den valda **Tidsgranularitet** sätts in i den slutliga *sammanfattningen ... efter bin(tidsstämpel, [tidskorn]) sats.*

- Alla valda **filterdimensioner** översätts till ytterligare *varsatser.*

- Den valda **splitdiagramdimensionen** översätts till en extra sammanfattande egenskap. Om du till exempel delar diagrammet efter *plats*och ritar med en 5-minuters tidsgranularitet sammanfattas *summarize-satsen* *... lagerplats (tidsstämpel, 5 m), plats*.

> [!NOTE]
> Om du inte har tidigare i Frågespråket Kusto börjar du med att kopiera och klistra in Kusto-satser i frågefönstret i Log Analytics utan att göra några ändringar. Klicka på **Kör** om du vill se det grundläggande diagrammet. När du börjar förstå syntaxen för frågespråket kan du börja göra små ändringar och se effekten av ändringen. Att utforska dina egna data är ett bra sätt att börja inse den fulla kraften i [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) och [Azure Monitor](../../azure-monitor/overview.md).

## <a name="availability-metrics"></a>Tillgänglighetsmått

Mått i kategorin Tillgänglighet gör att du kan se hälsotillståndet för ditt webbprogram som observerats från platser runt om i världen. [Konfigurera tillgänglighetstesterna](../../azure-monitor/app/monitor-web-app-availability.md) för att börja använda alla mått från den här kategorin.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Tillgänglighet (tillgänglighetResultat/tillgänglighetPercentage)
Måttet *Tillgänglighet* visar procentandelen av webbtestkörningarna som inte upptäckte några problem. Det lägsta möjliga värdet är 0, vilket indikerar att alla webbtestkörningar har misslyckats. Värdet 100 innebär att alla webbtestkörningar klarade valideringskriterierna.

|Måttenhet|Aggregeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Procent|Medel|Kör plats, Testnamn|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Varaktighet för tillgänglighetstest (tillgänglighetResultat/varaktighet)

Måttet *Tillgänglighetstestvaraktighet* visar hur lång tid det tog för webbtestet att köras. För [webbtester i flera steg](../../azure-monitor/app/availability-multistep.md)återspeglar måttet den totala körningstiden för alla steg.

|Måttenhet|Aggregeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Millisekunder|Genomsnitt, Min, Max|Kör plats, Testnamn, Testresultat

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Tillgänglighetstester (tillgänglighetResultat/antal)

Måttet *Tillgänglighetstester* återspeglar antalet webbtester som körs av Azure Monitor.

|Måttenhet|Aggregeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Antal|Antal|Kör plats, Testnamn, Testresultat|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Webbläsarmått

Webbläsarmått samlas in av Application Insights JavaScript SDK från riktiga slutanvändarens webbläsare. De ger bra insikter om användarnas upplevelse med din webbapp. Webbläsarmått tas vanligtvis inte i prov, vilket innebär att de ger högre precision i användningsnumren jämfört med mätvärden på serversidan som kan vara skeva genom sampling.

> [!NOTE]
> Om du vill samla in webbläsarmått måste ditt program vara instrumenterat med [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Inläsningstid för webbläsarsidan (webbläsareTimings/totalDuration)

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, Min, Max|Inget|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Klientbearbetningstid (webbläsareTiming/bearbetningUnderydning)

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, Min, Max|Inget|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Sidans inläsning av nätverksanslutningstid (webbläsareTimings/nätverkUnderyrering)

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, Min, Max|Inget|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Ta emot svarstid (webbläsareTimings/receiveDuration)

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, Min, Max|Inget|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Skicka begärandetid (webbläsareTimings/sendDuration)

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, Min, Max|Inget|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Mått för fel

Måtten i **Fel** visar problem med bearbetning av begäranden, beroendeanrop och intringade undantag.

### <a name="browser-exceptions-exceptionsbrowser"></a>Webbläsarundantag (undantag/webbläsare)

Det här måttet visar antalet inkastade undantag från programkoden som körs i webbläsaren. Endast undantag som spåras ```trackException()``` med ett API-anrop för Application Insights ingår i måttet.

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|Anteckningar|
|---|---|---|---|
|Antal|Antal|Inget|Logbaserad version **Sum** använder Summaaggregering|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Fel i beroendeanrop (beroenden/misslyckades)

Antalet misslyckade beroendeanrop.

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|Anteckningar|
|---|---|---|---|
|Antal|Antal|Inget|Logbaserad version **Sum** använder Summaaggregering|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Undantag (undantag/antal)

Varje gång du loggar ett undantag till Application Insights anropas [metoden trackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) för SDK. Måttet Undantag visar antalet loggade undantag.

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|Anteckningar|
|---|---|---|---|
|Antal|Antal|Molnrollnamn, molnrollinstans, Enhetstyp|Logbaserad version **Sum** använder Summaaggregering|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Misslyckade begäranden (begäranden/misslyckades)

Antalet spårade serverbegäranden som har *markerats*som misslyckade . Som standard markerar Application Insights SDK automatiskt varje serverbegäran som returnerade HTTP-svarskoden 5xx eller 4xx som en misslyckad begäran. Du kan anpassa den här logiken genom att ändra den *lyckade* egenskapen för begärandettmetriobjekt i en [anpassad telemetriinitierare](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|Anteckningar|
|---|---|---|---|
|Antal|Antal|Molnrollinstans, Molnrollnamn, Verklig eller syntetisk trafik, Begärandeprestanda, Svarskod|Logbaserad version **Sum** använder Summaaggregering|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Serverundantag (undantag/server)

Det här måttet visar antalet serverundantag.

|Måttenhet|Aggregeringar som stöds|Föraggregerade dimensioner|Anteckningar|
|---|---|---|---|
|Antal|Antal|Molnrollnamn, molnrollinstans|Logbaserad version **Sum** använder Summaaggregering|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Prestandaräknare

Använd mått i kategorin **Prestandaräknare** för att komma åt [systemprestandaräknare som samlats in av Application Insights](../../azure-monitor/app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Tillgängligt minne (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Undantagsfrekvens (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>HTTP-körningskörningstid (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP-begäranden (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-begäranden i programkön (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Process-CPU (performanceCounters/processCpuPercentage)

Måttet visar hur mycket av den totala processorkapaciteten som förbrukas av processen som är värd för din övervakade app.

|Måttenhet|Aggregeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Procent|Genomsnitt, Min, Max|Molnrollinstans

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Process-I/O-hastighet (performanceCounters/processIOBytesPerSecond)

|Måttenhet|Aggregeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Byte per sekund|Genomsnitt, Min, Max|Molnrollinstans

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Bearbeta privata byte (performanceCounters/processPrivateBytes)

Mängden icke-delat minne som den övervakade processen allokerade för sina data.

|Måttenhet|Aggregeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Byte|Genomsnitt, Min, Max|Molnrollinstans

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processortid (performanceCounters/processorCpuPercentage)

CPU-förbrukning av *alla* processer som körs på den övervakade serverinstansen.

|Måttenhet|Aggregeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Procent|Genomsnitt, Min, Max|Molnrollinstans

>[!NOTE]
> Processortidsmåttet är inte tillgängligt för de program som finns i Azure App Services. Använd [process-CPU-måttet](#process-cpu-performancecountersprocesscpupercentage) för att spåra CPU-användning av webbprogram som finns i App Services.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Servermått

### <a name="dependency-calls-dependenciescount"></a>Beroendeanrop (beroenden/antal)

Det här måttet är i förhållande till antalet beroendeanrop.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Beroendevaraktighet (beroende/varaktighet)

Det här måttet refererar till varaktigheten för beroendeanrop.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Serverbegäranden (begäranden/antal)

Det här måttet visar antalet inkommande serverbegäranden som togs emot av webbprogrammet.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Svarstid för servern (begäranden/varaktighet)

Det här måttet återspeglar den tid det tog för servrarna att bearbeta inkommande begäranden.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Användningsstatistik

### <a name="page-view-load-time-pageviewsduration"></a>Inläsningstid för sidvy (pageViews/duration)

Det här måttet refererar till hur lång tid det tog för PageView-händelser att läsas in.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Sidvisningar (pageViews/count)

Antalet PageView-händelser som loggats med Api:et för Application Insights för TrackPageView().

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessioner (sessioner/antal)

Det här måttet refererar till antalet olika sessions-ID:er.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Spår (spår/antal)

Antalet spårningssatser som loggats med API-anropet TrackTrace() Application Insights.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Användare (användare/antal)

Antalet olika användare som har åtkomst till ditt program. Noggrannheten i det här måttet kan påverkas avsevärt med hjälp av telemetriprovtagning och filtrering.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Användare, Autentiserade (användare/autentiserade)

Antalet olika användare som autentiserats i ditt program.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
