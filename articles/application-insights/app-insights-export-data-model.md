---
title: Azure Application Insights datamodellen | Microsoft Docs
description: "Beskriver egenskaper som exporteras från löpande export i JSON och används som filter."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: mbullwin
ms.openlocfilehash: 86aef6ba93224bbbb41bc7e651aaeec394fd8718
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-export-data-model"></a>Application Insights Export-datamodell
Den här tabellen innehåller egenskaper för telemetri som skickats från den [Programinsikter](app-insights-overview.md) SDK: er till portalen.
Du ser dessa egenskaper i data från [löpande Export](app-insights-export-telemetry.md).
De visas också egenskapsfilter i [mått Explorer](app-insights-metrics-explorer.md) och [diagnostiska Sök](app-insights-diagnostic-search.md).

Pekar på Observera:

* `[0]`i dessa tabeller anger du en punkt i sökvägen där du måste infoga ett index. men det är inte alltid 0.
* Tidsvaraktigheter finns i en mikrosekundnivå så 10000000 tiondels == 1 sekund.
* Datum och tider är UTC och anges i formatet ISO`yyyy-MM-DDThh:mm:ss.sssZ`


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
Alla typer av telemetri åtföljs av en kontext-avsnittet. Alla dessa fält skickas med varje datapunkt.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| Context.Custom.dimensions [0] |objektet] |Nyckel / värde-strängpar som anges av parametern anpassade egenskaper. Nyckeln får innehålla högst 100, värden får innehålla högst 1024. Fler än 100 unika värden egenskapen kan sökas men kan inte användas för segmentering. Max 200 nycklar per ikey. |
| Context.Custom.Metrics [0] |objektet] |Nyckel-värdepar anges av parametern anpassade mått och av TrackMetrics. Maximal nyckellängd 100, värdena kan vara numeriskt. |
| context.data.eventTime |Sträng |UTC |
| context.data.isSynthetic |Booleskt värde |Begäran verkar komma från en bot eller web test. |
| context.data.samplingRate |Antal |Procentandelen telemetri som genererats av SDK som skickas till portalen. Intervallet 0,0 100,0. |
| Context.Device |Objektet |Klientenheten |
| Context.Device.Browser |Sträng |Internet Explorer, Chrome... |
| context.device.browserVersion |Sträng |Chrome 48,0... |
| context.device.deviceModel |Sträng | |
| context.device.deviceName |Sträng | |
| Context.Device.ID |Sträng | |
| Context.Device.Locale |Sträng |en GB, de-DE... |
| Context.Device.Network |Sträng | |
| context.device.oemName |Sträng | |
| context.device.osVersion |Sträng |Värdens operativsystem |
| context.device.roleInstance |Sträng |ID för server-värd |
| context.device.roleName |Sträng | |
| Context.Device.Type |Sträng |PC webbläsare... |
| Context.Location |Objektet |Härleds från clientip. |
| Context.location.City |Sträng |Om den är känd som härrör från clientip, |
| Context.location.clientip |Sträng |Senaste Åttahörning anonym till 0. |
| Context.location.continent |Sträng | |
| Context.location.Country |Sträng | |
| Context.location.province |Sträng |Region |
| Context.operation.ID |Sträng |Objekt som har samma åtgärds-id visas som relaterade objekt i portalen. Vanligtvis begäran-id. |
| Context.operation.Name |Sträng |URL eller begäran |
| context.operation.parentId |Sträng |Tillåter kapslade relaterade objekt. |
| Context.session.ID |Sträng |ID för en grupp av åtgärder från samma källa. 30 minuter utan en åtgärd signalerar till slutet av en session. |
| context.session.isFirst |Booleskt värde | |
| context.user.accountAcquisitionDate |Sträng | |
| context.user.anonAcquisitionDate |Sträng | |
| context.user.anonId |Sträng | |
| context.user.authAcquisitionDate |Sträng |[Autentiserad användare](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |Booleskt värde | |
| internal.data.documentVersion |Sträng | |
| internal.data.ID |Sträng | |

## <a name="events"></a>Händelser
Anpassade händelser som genererats av [trackevent ()](app-insights-api-custom-events-metrics.md#trackevent).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| händelseantal [0] |heltal |100 / ([provtagning](app-insights-sampling.md) hastighet). Till exempel 4 =&gt; 25%. |
| händelsenamn [0] |Sträng |Händelsenamn.  Maxlängd 250. |
| händelsen [0] url |Sträng | |
| händelsen [0] urlData.base |Sträng | |
| händelsen [0] urlData.host |Sträng | |

## <a name="exceptions"></a>Undantag
Rapporter [undantag](app-insights-asp-net-exceptions.md) på servern och i webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| sammansättningen basicException [0] |Sträng | |
| Antal basicException [0] |heltal |100 / ([provtagning](app-insights-sampling.md) hastighet). Till exempel 4 =&gt; 25%. |
| basicException [0] exceptionGroup |Sträng | |
| basicException [0] exceptionType |Sträng | |
| basicException [0] failedUserCodeMethod |Sträng | |
| basicException [0] failedUserCodeAssembly |Sträng | |
| basicException [0] handledAt |Sträng | |
| basicException [0] hasFullStack |Booleskt värde | |
| basicException [0]-id |Sträng | |
| metoden basicException [0] |Sträng | |
| basicException [0] meddelande |Sträng |Undantagsmeddelande. Maxlängd 10k. |
| basicException [0] outerExceptionMessage |Sträng | |
| basicException [0] outerExceptionThrownAtAssembly |Sträng | |
| basicException [0] outerExceptionThrownAtMethod |Sträng | |
| basicException [0] outerExceptionType |Sträng | |
| basicException [0] outerId |Sträng | |
| basicException [0] parsedStack [0] sammansättning |Sträng | |
| basicException [0] parsedStack [0] filnamn |Sträng | |
| basicException [0] parsedStack [0] nivå |heltal | |
| basicException [0] parsedStack [0] rad |heltal | |
| basicException [0] parsedStack [0] metod |Sträng | |
| basicException [0] stack |Sträng |Maxlängd 10k |
| typeName basicException [0] |Sträng | |

## <a name="trace-messages"></a>Spåra meddelanden
Skickas av [TrackTrace](app-insights-api-custom-events-metrics.md#tracktrace), och av de [loggning kort](app-insights-asp-net-trace-logs.md).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| meddelandet [0] loggningsnamn |Sträng | |
| [0] meddelandeparametrar |Sträng | |
| meddelandet [0] rådata |Sträng |Loggmeddelande får innehålla högst 10k. |
| meddelandet [0] severityLevel |Sträng | |

## <a name="remote-dependency"></a>Fjärråtkomst beroende
Skickas av TrackDependency. Används för att rapportprestanda och användning av [anrop till beroenden](app-insights-asp-net-dependencies.md) i server och AJAX-anrop i webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| asynkrona remoteDependency [0] |Booleskt värde | |
| remoteDependency [0] baseName |Sträng | |
| remoteDependency [0] commandName |Sträng |Till exempel ”home/index” |
| Antal remoteDependency [0] |heltal |100 / ([provtagning](app-insights-sampling.md) hastighet). Till exempel 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |Sträng |HTTP, SQL... |
| remoteDependency [0] durationMetric.value |Antal |Tid vid anrop till svaret av beroende har slutförts |
| remoteDependency [0]-id |Sträng | |
| remoteDependency [0] namn |Sträng |URL-adressen. Maxlängd 250. |
| remoteDependency [0] resultCode |Sträng |från HTTP-beroendet |
| remoteDependency [0] lyckades |Booleskt värde | |
| typ av remoteDependency [0] |Sträng |HTTP, Sql... |
| remoteDependency [0] url |Sträng |Maxlängd 2000 |
| remoteDependency [0] urlData.base |Sträng |Maxlängd 2000 |
| remoteDependency [0] urlData.hashTag |Sträng | |
| remoteDependency [0] urlData.host |Sträng |Maxlängd 200 |

## <a name="requests"></a>Begäranden
Skickas av [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest). Modulerna som standard används för att rapporter serversvarstid, mätt på servern.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| antalet begäranden [0] |heltal |100 / ([provtagning](app-insights-sampling.md) hastighet). Till exempel: 4 =&gt; 25%. |
| begäran [0] durationMetric.value |Antal |Tid för begäran som inkommer till svar. 1e7 == 1s |
| id för förfrågan [0] |Sträng |Åtgärds-id |
| namn på förfrågan [0] |Sträng |GET/POST + bas-url.  Maxlängd 250 |
| begäran [0] responseCode |heltal |HTTP-svar som skickats till klienten |
| begäran [0] lyckades |Booleskt värde |Standard == (responseCode &lt; 400) |
| url-begäran [0] |Sträng |Inte inklusive värden |
| begäran [0] urlData.base |Sträng | |
| begäran [0] urlData.hashTag |Sträng | |
| begäran [0] urlData.host |Sträng | |

## <a name="page-view-performance"></a>Vyn sida prestanda
Skickas av webbläsaren. Mäter tid att bearbeta en sida från användare initierar en begäran om att visa fullständiga (exklusive asynkrona AJAX-anrop).

Kontexten värden visa klientens operativsystem och version på webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |heltal |Tiden från slutet av tar emot HTML för att visa sidan. |
| clientPerformance [0] namn |Sträng | |
| clientPerformance [0] networkConnection.value |heltal |Åtgången tid för att upprätta en nätverksanslutning. |
| clientPerformance [0] receiveRequest.value |heltal |Tiden från slutet av begäran skickades till mottagning HTML i svar. |
| clientPerformance [0] sendRequest.value |heltal |Tiden från att skicka HTTP-begäran. |
| clientPerformance [0] total.value |heltal |Tiden mellan start-och skicka begäran till visar sidan. |
| clientPerformance [0] url |Sträng |URL för den här begäran |
| clientPerformance [0] urlData.base |Sträng | |
| clientPerformance [0] urlData.hashTag |Sträng | |
| clientPerformance [0] urlData.host |Sträng | |
| clientPerformance [0] urlData.protocol |Sträng | |

## <a name="page-views"></a>Sidvisningar
Skickas av trackPageView() eller [stopTrackPage](app-insights-api-custom-events-metrics.md#page-views)

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| Visa [0] antal |heltal |100 / ([provtagning](app-insights-sampling.md) hastighet). Till exempel 4 =&gt; 25%. |
| Visa [0] durationMetric.value |heltal |Värdet som du kan också ställa in i trackPageView() eller startTrackPage() - stopTrackPage(). Inte samma som clientPerformance värden. |
| vynamn [0] |Sträng |Rubrik.  Maxlängd 250 |
| Visa [0] url |Sträng | |
| Visa [0] urlData.base |Sträng | |
| Visa [0] urlData.hashTag |Sträng | |
| Visa [0] urlData.host |Sträng | |

## <a name="availability"></a>Tillgänglighet
Rapporter [tillgänglighet webbtester](app-insights-monitor-web-app-availability.md).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| tillgänglighet [0] availabilityMetric.name |Sträng |availability |
| tillgänglighet [0] availabilityMetric.value |Antal |1.0 eller 0,0 |
| Antal tillgänglighet [0] |heltal |100 / ([provtagning](app-insights-sampling.md) hastighet). Till exempel 4 =&gt; 25%. |
| tillgänglighet [0] dataSizeMetric.name |Sträng | |
| tillgänglighet [0] dataSizeMetric.value |heltal | |
| tillgänglighet [0] durationMetric.name |Sträng | |
| tillgänglighet [0] durationMetric.value |Antal |Varaktighet för testet. 1e7 == 1s |
| meddelande om tillgänglighet [0] |Sträng |Fel diagnostik |
| tillgänglighet [0] resultat |Sträng |Eller inte |
| tillgänglighet [0] runLocation |Sträng |GEO-källan för HTTP-begäranden |
| tillgänglighet [0] testName |Sträng | |
| tillgänglighet [0] testRunId |Sträng | |
| tillgänglighet [0] testTimestamp |Sträng | |

## <a name="metrics"></a>Mått
Genereras av TrackMetric().

Måttet finns i context.custom.metrics[0]

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
Måttvärden, både i mått rapporter och på andra platser, rapporteras med en standard objektstruktur. Exempel:

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

För närvarande - men detta kan ändras i framtiden – alla värden som rapporterats från standard SDK-moduler, `count==1` och endast de `name` och `value` fälten är användbara. Det enda fallet där de skulle vara olika skulle vara om du skriver TrackMetric anrop i som du ange andra parametrar.

Syftet med de andra fälten är att mått ska aggregeras i SDK, för att minska trafiken till portalen. Du kan till exempel genomsnittlig flera efterföljande avläsningar innan du skickar varje mått rapport. Du skulle sedan beräkna min, max, standardavvikelse och samlat värde (sum eller medelvärde) och ange antal för antal värden som representeras av rapporten.

Vi har utelämnats används sällan fält count, min, max, stdDev och sampledValue i tabellerna ovan.

I stället före sammanställa statistik och, du kan använda [provtagning](app-insights-sampling.md) om du vill minska mängden telemetri.

### <a name="durations"></a>Varaktighet
Förutom där annat anges, representeras varaktighet i tiondels mikrosekundnivå, så att 10000000.0 innebär 1 sekund.

## <a name="see-also"></a>Se även
* [Application Insights](app-insights-overview.md)
* [Löpande Export](app-insights-export-telemetry.md)
* [Kodexempel](app-insights-export-telemetry.md#code-samples)
