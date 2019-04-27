---
title: Datamodell för Azure Application Insights | Microsoft Docs
description: Beskriver egenskaper som exporteras från löpande export i JSON och som filter.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: mbullwin
ms.openlocfilehash: 12025dfb93bbcfc86ae301f8fb63e7ac74697cf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898924"
---
# <a name="application-insights-export-data-model"></a>Datamodell för Application Insights-Export
Den här tabellen anger egenskaperna för telemetri som skickas från den [Application Insights](../../azure-monitor/app/app-insights-overview.md) SDK: er till portalen.
Ser du de här egenskaperna i utdata från [löpande Export](export-telemetry.md).
De visas också i egenskapsfilter i [Metric Explorer](../../azure-monitor/app/metrics-explorer.md) och [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md).

Saker att Observera:

* `[0]` i dessa tabeller anger du en punkt i sökvägen där du måste infoga ett index. men det är inte alltid 0.
* Tidsvaraktigheter finns i en databehandlingsnoder så 10000000 tiondels == 1 sekund.
* Datum och tider är UTC och anges i ISO-format `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Exempel
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Kontext
Alla typer av telemetri åtföljs av en kontext-avsnittet. Inte alla de här fälten överförs med varje datapunkt.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| context.custom.dimensions [0] |objektet] |Nyckel / värde-strängpar anges av parametern anpassade egenskaper. Maximal nyckellängd 100, värden får innehålla högst 1024. Fler än 100 unika värden egenskapen kan sökas men kan inte användas för segmentering. Max 200 nycklar per ikey. |
| context.custom.metrics [0] |objektet] |Nyckel / värde-par av anpassade mått-parametern och av TrackMetrics. Maximal nyckellängd 100, värden kan vara numerisk. |
| context.data.eventTime |string |UTC |
| context.data.isSynthetic |boolesk |Begäran ser ut att komma från en bot eller web-test. |
| context.data.samplingRate |nummer |Den procentandel av telemetri som genereras av SDK som skickas till portalen. Intervallet 0,0 100,0. |
| context.device |objekt |Klientenheten |
| context.device.browser |string |Internet Explorer, Chrome... |
| context.device.browserVersion |string |Chrome 48,0... |
| context.device.deviceModel |string | |
| context.device.deviceName |string | |
| context.device.id |string | |
| context.device.locale |string |en-GB, de-DE, ... |
| context.device.network |string | |
| context.device.oemName |string | |
| context.device.os |string | |
| context.device.osVersion |string |Gästoperativsystem |
| context.device.roleInstance |string |ID för server-värd |
| context.device.roleName |string | |
| context.device.screenResolution |string | |
| context.device.type |string |PC, webbläsare... |
| context.location |objekt |Hämtad från clientip. |
| context.location.city |string |Härleds från clientip, om det är möjligt |
| context.location.clientip |string |Senaste Åttahörning är maskerade till 0. |
| context.location.continent |string | |
| context.location.country |string | |
| context.location.province |string |Region |
| context.operation.id |string |Objekt som har samma åtgärds-id visas som relaterade objekt i portalen. Vanligtvis begäran-id. |
| context.operation.name |string |webbadressen eller förfrågan |
| context.operation.parentId |string |Tillåter kapslade relaterade objekt. |
| context.session.id |string |ID för en grupp av åtgärder från samma källa. 30 minuter utan att en åtgärd signalerar till slutet av en session. |
| context.session.isFirst |boolesk | |
| context.user.accountAcquisitionDate |string | |
| context.user.accountId |string | |
| context.user.anonAcquisitionDate |string | |
| context.user.anonId |string | |
| context.user.authAcquisitionDate |string |[Autentiserad användare](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |string | |
| context.user.isAuthenticated |boolesk | |
| context.user.storeRegion |string | |
| internal.data.documentVersion |string | |
| internal.data.id |string | Unikt id som tilldelas när ett objekt matas till Application Insights |

## <a name="events"></a>Händelser
Anpassade händelser som genererats av [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| händelseantal [0] |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| händelsenamn [0] |string |Händelsenamn.  Maxlängd 250. |
| URL: en för händelsen [0] |string | |
| händelsen [0] urlData.base |string | |
| händelsen [0] urlData.host |string | |

## <a name="exceptions"></a>Undantag
Rapporter [undantag](../../azure-monitor/app/asp-net-exceptions.md) på servern och i webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| basicException [0] sammansättning |string | |
| basicException [0] antal |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| basicException [0] exceptionGroup |string | |
| basicException [0] exceptionType |string | |
| basicException [0] failedUserCodeMethod |string | |
| basicException [0] failedUserCodeAssembly |string | |
| basicException [0] handledAt |string | |
| basicException [0] hasFullStack |boolesk | |
| basicException [0]-id |string | |
| metoden basicException [0] |string | |
| basicException [0] meddelande |string |Undantagsmeddelande. Maxlängd 10k. |
| basicException [0] outerExceptionMessage |string | |
| basicException [0] outerExceptionThrownAtAssembly |string | |
| basicException [0] outerExceptionThrownAtMethod |string | |
| basicException [0] outerExceptionType |string | |
| basicException [0] outerId |string | |
| basicException [0] parsedStack [0] sammansättning |string | |
| basicException [0] parsedStack [0] filnamn |string | |
| basicException [0] parsedStack [0] nivå |heltal | |
| basicException [0] parsedStack [0] rad |heltal | |
| basicException [0] parsedStack [0]-metod |string | |
| basicException [0]-stack |string |Maxlängd 10k |
| typeName basicException [0] |string | |

## <a name="trace-messages"></a>Spårningsmeddelanden
Skickas av [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace), och av den [loggning kort](../../azure-monitor/app/asp-net-trace-logs.md).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| meddelande [0] loggningsnamn |string | |
| [0] meddelandeparametrar |string | |
| meddelande [0] rådata |string |Loggmeddelande maxlängden 10k. |
| meddelande [0] severityLevel |string | |

## <a name="remote-dependency"></a>Remote beroende
Skickas av TrackDependency. Används för att rapportprestanda och användning av [anrop till beroenden](../../azure-monitor/app/asp-net-dependencies.md) på den servern och AJAX-anrop i webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| asynkrona remoteDependency [0] |boolesk | |
| remoteDependency [0] baseName |string | |
| remoteDependency [0] commandName |string |Till exempel ”home/index” |
| remoteDependency [0] antal |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |string |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |nummer |Tid vid anrop till slutförandet av svar av beroende |
| remoteDependency [0]-id |string | |
| namn på remoteDependency [0] |string |URL: en. Maxlängd 250. |
| remoteDependency [0] Resultatkod |string |från HTTP-beroenden |
| remoteDependency [0] lyckades |boolesk | |
| typ av remoteDependency [0] |string |HTTP, Sql... |
| remoteDependency [0] url |string |Maxlängd 2000 |
| remoteDependency [0] urlData.base |string |Maxlängd 2000 |
| remoteDependency [0] urlData.hashTag |string | |
| remoteDependency [0] urlData.host |string |Maxlängd 200 |

## <a name="requests"></a>Begäranden
Skickas av [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Moduler som standard används för att rapporter serversvarstid, mätt på servern.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| Antal förfrågningar [0] |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Exempel: 4 =&gt; 25%. |
| begäran [0] durationMetric.value |nummer |Tid från begäran kommer att svaret. 1e7 == 1s |
| id för förfrågan [0] |string |Åtgärds-ID |
| namnet på begäran [0] |string |GET/POST + url-bas.  Maxlängd 250 |
| begäran [0] responseCode |heltal |HTTP-svar som skickas till klienten |
| [0] slutförda begäranden |boolesk |Standard == (responseCode &lt; 400) |
| [0] url för begäran |string |Inkluderar inte värden |
| begäran [0] urlData.base |string | |
| begäran [0] urlData.hashTag |string | |
| begäran [0] urlData.host |string | |

## <a name="page-view-performance"></a>Prestanda för Sidvisning
Skickas av webbläsaren. Mäter tid att bearbeta en sida från användaren initierar begäran om att visa fullständiga (exklusive asynkrona AJAX-anrop).

Kontext värden visar klientoperativsystem och webbläsarversion.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |heltal |Tid från slutet av tar emot HTML att visa sidan. |
| namn på clientPerformance [0] |string | |
| clientPerformance [0] networkConnection.value |heltal |Åtgången tid för att upprätta en nätverksanslutning. |
| clientPerformance [0] receiveRequest.value |heltal |Tid från slutet av skickar en begäran att ta emot HTML i svar. |
| clientPerformance [0] sendRequest.value |heltal |Tid från att skicka HTTP-begäran. |
| clientPerformance [0] total.value |heltal |Tiden mellan start-och skicka begäran till visar sidan. |
| clientPerformance [0] url |string |URL: en för den här begäran |
| clientPerformance [0] urlData.base |string | |
| clientPerformance [0] urlData.hashTag |string | |
| clientPerformance [0] urlData.host |string | |
| clientPerformance [0] urlData.protocol |string | |

## <a name="page-views"></a>Sidvisningar
Skickas av trackPageView() eller [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| visningar [0] |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| Visa [0] durationMetric.value |heltal |Värdet som du kan också ställa i trackPageView() eller genom att startTrackPage() - stopTrackPage(). Inte lika clientPerformance värden. |
| vynamn [0] |string |Sidrubrik.  Maxlängd 250 |
| Visa [0] url |string | |
| Visa [0] urlData.base |string | |
| Visa [0] urlData.hashTag |string | |
| Visa [0] urlData.host |string | |

## <a name="availability"></a>Tillgänglighet
Rapporter [webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| tillgänglighet [0] availabilityMetric.name |string |availability |
| tillgänglighet [0] availabilityMetric.value |nummer |1.0 eller 0,0 |
| tillgänglighet [0] antal |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| tillgänglighet [0] dataSizeMetric.name |string | |
| tillgänglighet [0] dataSizeMetric.value |heltal | |
| tillgänglighet [0] durationMetric.name |string | |
| tillgänglighet [0] durationMetric.value |nummer |Varaktighet för testning. 1e7 == 1s |
| tillgänglighetsmeddelande [0] |string |Fel vid sändning |
| tillgänglighetsresultat [0] |string |Avgörande |
| tillgänglighet [0] runLocation |string |GEO-källan för http-begäranden |
| tillgänglighet [0] testName |string | |
| tillgänglighet [0] testRunId |string | |
| tillgänglighet [0] testTimestamp |string | |

## <a name="metrics"></a>Mått
Genereras av TrackMetric().

Mätvärdet finns i context.custom.metrics[0]

Exempel:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Om måttvärden
Måttvärden, både i mått rapporter och på andra platser, rapporteras med en objektstruktur för standard. Exempel:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

För närvarande - men detta kan ändras i framtiden, i alla värden som rapporterats från standardmoduler för SDK `count==1` och endast de `name` och `value` fält är användbara. Det enda fallet där de är olika skulle vara om du skriva din egen TrackMetric-anrop i där du ställa in andra parametrar.

Syftet med de andra fälten är att mått ska aggregeras i SDK, för att minska trafiken till portalen. Du kan till exempel genomsnittlig flera på varandra följande information innan du skickar varje mätrapporten. Du kan sedan beräkna min, max, standardavvikelse och samlat värde (summa eller medelvärde) och ange antal antalet värden som representeras av rapporten.

I tabellerna ovan har vi utelämnas används sällan fält count, min, max, stdDev och sampledValue.

I stället förväg sammanställning av mått, kan du använda [sampling](../../azure-monitor/app/sampling.md) om du vill minska mängden telemetri.

### <a name="durations"></a>Varaktighet
Förutom där annat anges, representeras varaktigheter i tiondels databehandlingsnoder, så att 10000000.0 innebär 1 sekund.

## <a name="see-also"></a>Se också
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Löpande Export](export-telemetry.md)
* [Kodexempel](export-telemetry.md#code-samples)
