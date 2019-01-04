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
ms.date: 03/21/2016
ms.author: mbullwin
ms.openlocfilehash: c906f313ae367fcc660d1ce1df0b3d667c7f04a3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016134"
---
# <a name="application-insights-export-data-model"></a>Datamodell för Application Insights-Export
Den här tabellen anger egenskaperna för telemetri som skickas från den [Application Insights](../../application-insights/app-insights-overview.md) SDK: er till portalen.
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
| Context.Custom.dimensions [0] |objektet] |Nyckel / värde-strängpar anges av parametern anpassade egenskaper. Maximal nyckellängd 100, värden får innehålla högst 1024. Fler än 100 unika värden egenskapen kan sökas men kan inte användas för segmentering. Max 200 nycklar per ikey. |
| Context.Custom.Metrics [0] |objektet] |Nyckel / värde-par av anpassade mått-parametern och av TrackMetrics. Maximal nyckellängd 100, värden kan vara numerisk. |
| context.data.eventTime |sträng |UTC |
| context.data.isSynthetic |boolesk |Begäran ser ut att komma från en bot eller web-test. |
| context.data.samplingRate |nummer |Den procentandel av telemetri som genereras av SDK som skickas till portalen. Intervallet 0,0 100,0. |
| Context.Device |objekt |Klientenheten |
| Context.Device.Browser |sträng |Internet Explorer, Chrome... |
| context.device.browserVersion |sträng |Chrome 48,0... |
| context.device.deviceModel |sträng | |
| context.device.deviceName |sträng | |
| Context.Device.ID |sträng | |
| Context.Device.Locale |sträng |en-GB, de-DE... |
| Context.Device.Network |sträng | |
| context.device.oemName |sträng | |
| context.device.osVersion |sträng |Gästoperativsystem |
| context.device.roleInstance |sträng |ID för server-värd |
| context.device.roleName |sträng | |
| Context.Device.Type |sträng |PC, webbläsare... |
| Context.Location |objekt |Hämtad från clientip. |
| Context.location.City |sträng |Härleds från clientip, om det är möjligt |
| Context.location.clientip |sträng |Senaste Åttahörning är maskerade till 0. |
| Context.location.continent |sträng | |
| Context.location.Country |sträng | |
| Context.location.province |sträng |Region |
| Context.operation.ID |sträng |Objekt som har samma åtgärds-id visas som relaterade objekt i portalen. Vanligtvis begäran-id. |
| Context.operation.Name |sträng |webbadressen eller förfrågan |
| context.operation.parentId |sträng |Tillåter kapslade relaterade objekt. |
| Context.session.ID |sträng |ID för en grupp av åtgärder från samma källa. 30 minuter utan att en åtgärd signalerar till slutet av en session. |
| context.session.isFirst |boolesk | |
| context.user.accountAcquisitionDate |sträng | |
| context.user.anonAcquisitionDate |sträng | |
| context.user.anonId |sträng | |
| context.user.authAcquisitionDate |sträng |[Autentiserad användare](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |boolesk | |
| internal.data.documentVersion |sträng | |
| internal.data.ID |sträng | Unikt id som tilldelas när ett objekt matas till Application Insights |

## <a name="events"></a>Händelser
Anpassade händelser som genererats av [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| händelseantal [0] |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| händelsenamn [0] |sträng |Händelsenamn.  Maxlängd 250. |
| URL: en för händelsen [0] |sträng | |
| händelsen [0] urlData.base |sträng | |
| händelsen [0] urlData.host |sträng | |

## <a name="exceptions"></a>Undantag
Rapporter [undantag](../../azure-monitor/app/asp-net-exceptions.md) på servern och i webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| basicException [0] sammansättning |sträng | |
| basicException [0] antal |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| basicException [0] exceptionGroup |sträng | |
| basicException [0] exceptionType |sträng | |
| basicException [0] failedUserCodeMethod |sträng | |
| basicException [0] failedUserCodeAssembly |sträng | |
| basicException [0] handledAt |sträng | |
| basicException [0] hasFullStack |boolesk | |
| basicException [0]-id |sträng | |
| metoden basicException [0] |sträng | |
| basicException [0] meddelande |sträng |Undantagsmeddelande. Maxlängd 10k. |
| basicException [0] outerExceptionMessage |sträng | |
| basicException [0] outerExceptionThrownAtAssembly |sträng | |
| basicException [0] outerExceptionThrownAtMethod |sträng | |
| basicException [0] outerExceptionType |sträng | |
| basicException [0] outerId |sträng | |
| basicException [0] parsedStack [0] sammansättning |sträng | |
| basicException [0] parsedStack [0] filnamn |sträng | |
| basicException [0] parsedStack [0] nivå |heltal | |
| basicException [0] parsedStack [0] rad |heltal | |
| basicException [0] parsedStack [0]-metod |sträng | |
| basicException [0]-stack |sträng |Maxlängd 10k |
| typeName basicException [0] |sträng | |

## <a name="trace-messages"></a>Spårningsmeddelanden
Skickas av [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace), och av den [loggning kort](../../azure-monitor/app/asp-net-trace-logs.md).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| meddelande [0] loggningsnamn |sträng | |
| [0] meddelandeparametrar |sträng | |
| meddelande [0] rådata |sträng |Loggmeddelande maxlängden 10k. |
| meddelande [0] severityLevel |sträng | |

## <a name="remote-dependency"></a>Remote beroende
Skickas av TrackDependency. Används för att rapportprestanda och användning av [anrop till beroenden](../../azure-monitor/app/asp-net-dependencies.md) på den servern och AJAX-anrop i webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| asynkrona remoteDependency [0] |boolesk | |
| remoteDependency [0] baseName |sträng | |
| remoteDependency [0] commandName |sträng |Till exempel ”home/index” |
| remoteDependency [0] antal |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |sträng |HTTP, SQL... |
| remoteDependency [0] durationMetric.value |nummer |Tid vid anrop till slutförandet av svar av beroende |
| remoteDependency [0]-id |sträng | |
| namn på remoteDependency [0] |sträng |URL: en. Maxlängd 250. |
| remoteDependency [0] Resultatkod |sträng |från HTTP-beroenden |
| remoteDependency [0] lyckades |boolesk | |
| typ av remoteDependency [0] |sträng |HTTP, Sql... |
| remoteDependency [0] url |sträng |Maxlängd 2000 |
| remoteDependency [0] urlData.base |sträng |Maxlängd 2000 |
| remoteDependency [0] urlData.hashTag |sträng | |
| remoteDependency [0] urlData.host |sträng |Maxlängd 200 |

## <a name="requests"></a>Begäranden
Skickas av [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Moduler som standard används för att rapporter serversvarstid, mätt på servern.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| Antal förfrågningar [0] |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Exempel: 4 =&gt; 25%. |
| begäran [0] durationMetric.value |nummer |Tid från begäran kommer att svaret. 1e7 == 1s |
| id för förfrågan [0] |sträng |Åtgärds-ID |
| namnet på begäran [0] |sträng |GET/POST + url-bas.  Maxlängd 250 |
| begäran [0] responseCode |heltal |HTTP-svar som skickas till klienten |
| [0] slutförda begäranden |boolesk |Standard == (responseCode &lt; 400) |
| [0] url för begäran |sträng |Inkluderar inte värden |
| begäran [0] urlData.base |sträng | |
| begäran [0] urlData.hashTag |sträng | |
| begäran [0] urlData.host |sträng | |

## <a name="page-view-performance"></a>Prestanda för Sidvisning
Skickas av webbläsaren. Mäter tid att bearbeta en sida från användaren initierar begäran om att visa fullständiga (exklusive asynkrona AJAX-anrop).

Kontext värden visar klientoperativsystem och webbläsarversion.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |heltal |Tid från slutet av tar emot HTML att visa sidan. |
| namn på clientPerformance [0] |sträng | |
| clientPerformance [0] networkConnection.value |heltal |Åtgången tid för att upprätta en nätverksanslutning. |
| clientPerformance [0] receiveRequest.value |heltal |Tid från slutet av skickar en begäran att ta emot HTML i svar. |
| clientPerformance [0] sendRequest.value |heltal |Tid från att skicka HTTP-begäran. |
| clientPerformance [0] total.value |heltal |Tiden mellan start-och skicka begäran till visar sidan. |
| clientPerformance [0] url |sträng |URL: en för den här begäran |
| clientPerformance [0] urlData.base |sträng | |
| clientPerformance [0] urlData.hashTag |sträng | |
| clientPerformance [0] urlData.host |sträng | |
| clientPerformance [0] urlData.protocol |sträng | |

## <a name="page-views"></a>Sidvisningar
Skickas av trackPageView() eller [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| visningar [0] |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| Visa [0] durationMetric.value |heltal |Värdet som du kan också ställa i trackPageView() eller genom att startTrackPage() - stopTrackPage(). Inte lika clientPerformance värden. |
| vynamn [0] |sträng |Sidrubrik.  Maxlängd 250 |
| Visa [0] url |sträng | |
| Visa [0] urlData.base |sträng | |
| Visa [0] urlData.hashTag |sträng | |
| Visa [0] urlData.host |sträng | |

## <a name="availability"></a>Tillgänglighet
Rapporter [webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| tillgänglighet [0] availabilityMetric.name |sträng |availability |
| tillgänglighet [0] availabilityMetric.value |nummer |1.0 eller 0,0 |
| tillgänglighet [0] antal |heltal |100 / ([sampling](../../azure-monitor/app/sampling.md) pris). Till exempel 4 =&gt; 25%. |
| tillgänglighet [0] dataSizeMetric.name |sträng | |
| tillgänglighet [0] dataSizeMetric.value |heltal | |
| tillgänglighet [0] durationMetric.name |sträng | |
| tillgänglighet [0] durationMetric.value |nummer |Varaktighet för testning. 1e7 == 1s |
| tillgänglighetsmeddelande [0] |sträng |Fel vid sändning |
| tillgänglighetsresultat [0] |sträng |Avgörande |
| tillgänglighet [0] runLocation |sträng |GEO-källan för http-begäranden |
| tillgänglighet [0] testName |sträng | |
| tillgänglighet [0] testRunId |sträng | |
| tillgänglighet [0] testTimestamp |sträng | |

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
* [Application Insights](../../application-insights/app-insights-overview.md)
* [Löpande Export](export-telemetry.md)
* [Kodexempel](export-telemetry.md#code-samples)
