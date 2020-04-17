---
title: Datamodell för Azure Application Insights | Microsoft-dokument
description: Beskriver egenskaper som exporteras från kontinuerlig export i JSON och används som filter.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 9891bea1d52c61197fa32fa5c0764df5450b563c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536853"
---
# <a name="application-insights-export-data-model"></a>Exportera datamodell för programinsikter
I den här tabellen visas egenskaperna för telemetri som skickas från [SDK:erna för Programinsikter](../../azure-monitor/app/app-insights-overview.md) till portalen.
Du ser dessa egenskaper i datautdata från [Kontinuerlig export](export-telemetry.md).
De visas också i egenskapsfilter i [Metric Explorer](../../azure-monitor/platform/metrics-charts.md) och [Diagnostic Search](../../azure-monitor/app/diagnostic-search.md).

Punkter att notera:

* `[0]`I dessa tabeller anges en punkt i banan där du måste infoga ett index. men det är inte alltid 0.
* Tidslängderna är i tiondelar av en mikrosekt, så 10000000 == 1 sekund.
* Datum och tider är UTC och anges i ISO-format`yyyy-MM-DDThh:mm:ss.sssZ`


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
Alla typer av telemetri åtföljs av ett sammanhangsavsnitt. Alla dessa fält överförs inte med varje datapunkt.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| context.custom.dimensions [0] |objekt [ ] |Strängpar för nyckelvärde som anges efter parametern anpassade egenskaper. Nyckel max längd 100, värden max längd 1024. Mer än 100 unika värden, egenskapen kan sökas men kan inte användas för segmentering. Max 200 nycklar per ikey. |
| context.custom.metrics [0] |objekt [ ] |Nyckelvärdespar som ställts in efter anpassad måttparameter och trackmetrics. Nyckel max längd 100, kan värden vara numeriska. |
| context.data.eventTime |sträng |UTC |
| context.data.isSynthetic |boolean |Begäran verkar komma från en bot eller webbtest. |
| context.data.samplingRate |nummer |Procentandel telemetri som genereras av SDK som skickas till portalen. Räckvidd 0,0-100,0. |
| context.device (enhet) |objekt |Klientenhet |
| context.device.browser |sträng |DVS Chrome, ... |
| context.device.browserVersion |sträng |Chrome 48,0, ... |
| context.device.deviceModel |sträng | |
| context.device.deviceName |sträng | |
| context.device.id |sträng | |
| context.device.locale |sträng |sv-GB, de-DE, ... |
| context.device.network |sträng | |
| context.device.oemName |sträng | |
| context.device.os |sträng | |
| context.device.osVersion |sträng |Värdoperativsystem |
| context.device.roleInstance |sträng |ID för servervärd |
| context.device.roleName |sträng | |
| context.device.screenResolution |sträng | |
| context.device.type |sträng |PC, Webbläsare, ... |
| context.location (plats) |objekt |Härledd `clientip`från . |
| context.location.city |sträng |Härleds `clientip`från , om det är känt |
| context.location.clientip |sträng |Sist oktagon anonymiseras till 0. |
| context.location.continent |sträng | |
| context.location.land |sträng | |
| context.location.province |sträng |Delstat eller provins |
| context.operation.id |sträng |Objekt som har `operation id` samma visas som Relaterade objekt i portalen. Vanligtvis `request id`. |
| context.operation.name |sträng |url eller begär namn |
| context.operation.parentId |sträng |Tillåter kapslade relaterade objekt. |
| context.session.id |sträng |`Id`en grupp av operationer från samma källa. En period på 30 minuter utan en operation signalerar slutet på en session. |
| context.session.isFörsta |boolean | |
| context.user.accountAcquisitionDate |sträng | |
| context.user.accountId |sträng | |
| context.user.anonAcquisitionDate |sträng | |
| context.user.anonId |sträng | |
| context.user.authAcquisitionDate |sträng |[Autentiserat användare](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |sträng | |
| context.user.isAuthenticated |boolean | |
| context.user.storeRegion |sträng | |
| internt.data.documentVersion |sträng | |
| internal.data.id |sträng | `Unique id`som tilldelas när ett objekt förtärs till Application Insights |

## <a name="events"></a>Händelser
Anpassade händelser som genereras av [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| händelse [0] antal |heltal |100/([sampling](../../azure-monitor/app/sampling.md) provtagningshastighet). Till exempel 4 =&gt; 25%. |
| händelse [0] namn |sträng |Händelsenamn.  Max längd 250. |
| händelse [0] url |sträng | |
| händelse [0] urlData.base |sträng | |
| händelse [0] urlData.host |sträng | |

## <a name="exceptions"></a>Undantag
Rapporterar [undantag](../../azure-monitor/app/asp-net-exceptions.md) på servern och i webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| basicException [0] montering |sträng | |
| basicException [0] antal |heltal |100/([sampling](../../azure-monitor/app/sampling.md) provtagningshastighet). Till exempel 4 =&gt; 25%. |
| basicException [0] exceptionGroup |sträng | |
| basicException [0] exceptionType |sträng | |
| basicException [0] failedUserCodeMethod |sträng | |
| basicException [0] failedUserCodeAssembly |sträng | |
| basicException [0] hanterasAt |sträng | |
| basicException [0] hasFullStack |boolean | |
| basicException [0]`id` |sträng | |
| basicException [0] metod |sträng | |
| basicException [0] meddelande |sträng |Undantagsmeddelande. Max längd 10k. |
| basicException [0] outerExceptionMessage |sträng | |
| basicException [0] outerExceptionThrownAtAssembly basicException [0] outerExceptionThrownAtAssembly basicException [0] outerExceptionThrownAtAssembly basicEx |sträng | |
| basicException [0] outerExceptionThrownAtMethod |sträng | |
| basicException [0] outerExceptionType |sträng | |
| basicException [0] outerId |sträng | |
| basicException [0] parsedStack [0] montering |sträng | |
| basicException [0] tolkatStack [0] filnamn |sträng | |
| basicException [0] parsedStack [0] nivå |heltal | |
| basicException [0] tolkasStack [0] linje |heltal | |
| basicException [0] parsedStack [0] metod |sträng | |
| basicException [0] stack |sträng |Max längd 10k |
| basicException [0] typeName |sträng | |

## <a name="trace-messages"></a>Spåra meddelanden
Skickas av [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)och av [loggningsadaptrarna](../../azure-monitor/app/asp-net-trace-logs.md).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| meddelande [0] loggerName |sträng | |
| meddelande [0] parametrar |sträng | |
| meddelande [0] rå |sträng |Loggmeddelandet, max längd 10k. |
| meddelande [0] allvarlighetsgradNivå |sträng | |

## <a name="remote-dependency"></a>Fjärrberoende
Skickas av TrackDependency. Används för att rapportera prestanda och användning av [samtal till beroenden](../../azure-monitor/app/asp-net-dependencies.md) på servern och AJAX-anrop i webbläsaren.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| remoteDependency [0] async |boolean | |
| remoteDependency [0] baseName |sträng | |
| fjärrberoende [0] kommandoNamn |sträng |Till exempel "hem/index" |
| remoteDependency [0] antal |heltal |100/([sampling](../../azure-monitor/app/sampling.md) provtagningshastighet). Till exempel 4 =&gt; 25%. |
| remoteDependency [0] beroendeTypeName |sträng |HTTP, SQL, ... |
| fjärrberoende [0] durationMetric.value |nummer |Tid från samtal till slutförande av svar efter beroende |
| fjärrberoende [0]`id` |sträng | |
| remoteDependency [0] namn |sträng |Url. Max längd 250. |
| fjärrberoende [0] resultCode |sträng |från HTTP-beroende |
| remoteDependency [0] framgång |boolean | |
| fjärrberoende [0] typ |sträng |Http, Sql,... |
| remoteDependency [0] url |sträng |Max längd 2000 |
| remoteDependency [0] urlData.base |sträng |Max längd 2000 |
| remoteDependency [0] urlData.hashTag |sträng | |
| remoteDependency [0] urlData.host |sträng |Max längd 200 |

## <a name="requests"></a>Begäranden
Skickas av [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Standardmodulerna använder detta för att rapportera serverns svarstid, mätt på servern.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| begäran [0] antal |heltal |100/([sampling](../../azure-monitor/app/sampling.md) provtagningshastighet). Till exempel: 4 =&gt; 25%. |
| begäran [0] durationMetric.value |nummer |Tid från begäran anländer till svar. 1e7 == 1s |
| begäran [0]`id` |sträng |`Operation id` |
| namn på begäran [0] |sträng |GET / POST + url bas.  Max längd 250 |
| begär [0] responseCode |heltal |HTTP-svar som skickas till klienten |
| begäran [0] framgång |boolean |Standard == (responseCode &lt; 400) |
| begär [0] url |sträng |Exklusive värd |
| begär [0] urlData.base |sträng | |
| begär [0] urlData.hashTag |sträng | |
| begär [0] urlData.host |sträng | |

## <a name="page-view-performance"></a>Prestanda för sidvisning
Skickas av webbläsaren. Mäter tiden för att bearbeta en sida, från användaren som initierar begäran om att visa fullständig (exklusive async AJAX-anrop).

Kontextvärden visar klientoperativsystemet och webbläsarversionen.

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |heltal |Tid från slutet av mottagandet av HTML till att visa sidan. |
| namn för clientPerformance [0] |sträng | |
| clientPerformance [0] networkConnection.value |heltal |Det tar att upprätta en nätverksanslutning. |
| clientPerformance [0] receiveRequest.value |heltal |Tid från slutet av att skicka begäran till att ta emot HTML som svar. |
| clientPerformance [0] sendRequest.value |heltal |Tid från taget för att skicka HTTP-begäran. |
| clientPerformance [0] total.värde |heltal |Tid från början för att skicka begäran till att visa sidan. |
| url för klientperformance [0] |sträng |URL för den här begäran |
| clientPerformance [0] urlData.base |sträng | |
| clientPerformance [0] urlData.hashTag |sträng | |
| clientPerformance [0] urlData.host |sträng | |
| clientPerformance [0] urlData.protocol |sträng | |

## <a name="page-views"></a>Sidvisningar
Skickas via trackPageView() eller [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| visa [0] antal |heltal |100/([sampling](../../azure-monitor/app/sampling.md) provtagningshastighet). Till exempel 4 =&gt; 25%. |
| visa [0] durationMetric.value |heltal |Värde som eventuellt anges i trackPageView() eller av startTrackPage() - stopTrackPage(). Inte samma sak som clientPerformance-värden. |
| visa [0] namn |sträng |Sidrubrik.  Max längd 250 |
| visa [0] url |sträng | |
| visa [0] urlData.base |sträng | |
| visa [0] urlData.hashTag |sträng | |
| visa [0] urlData.host |sträng | |

## <a name="availability"></a>Tillgänglighet
Rapporterar [webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md).

| Sökväg | Typ | Anteckningar |
| --- | --- | --- |
| tillgänglighet [0] availabilityMetric.name |sträng |availability |
| tillgänglighet [0] tillgänglighetMetric.value |nummer |1.0 eller 0.0 |
| tillgänglighet [0] antal |heltal |100/([sampling](../../azure-monitor/app/sampling.md) provtagningshastighet). Till exempel 4 =&gt; 25%. |
| tillgänglighet [0] dataSizeMetric.name |sträng | |
| tillgänglighet [0] dataSizeMetric.value |heltal | |
| tillgänglighet [0] durationMetric.name |sträng | |
| tillgänglighet [0] durationMetric.value |nummer |Testets varaktighet. 1e7==1s |
| tillgänglighet [0] meddelande |sträng |Diagnostik vid fel |
| tillgänglighet [0] resultat |sträng |Godkänn/Underkänn |
| tillgänglighet [0] runLocation |sträng |Geo källa till http req |
| tillgänglighet [0] testNamn |sträng | |
| tillgänglighet [0] testRunId |sträng | |
| tillgänglighet [0] testTimestamp |sträng | |

## <a name="metrics"></a>Mått
Genereras av TrackMetric().

Måttvärdet finns i context.custom.metrics[0]

Ett exempel:

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
Måttvärden, både i måttrapporter och på andra ställen, rapporteras med en standardobjektstruktur. Ett exempel:

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

För närvarande - även om detta kan ändras i framtiden - `count==1` i alla `name` `value` värden som rapporteras från standard SDK-moduler, och endast och fälten är användbara. Det enda fallet där de skulle vara annorlunda skulle vara om du skriver dina egna TrackMetric samtal där du ställer in de andra parametrarna.

Syftet med de andra fälten är att tillåta att mått aggregeras i SDK, för att minska trafiken till portalen. Du kan till exempel beräkna flera efterföljande avläsningar innan du skickar varje måttrapport. Sedan beräknar du min, max, standardavvikelse och aggregerat värde (summa eller medelvärde) och ange antalet avläsningar som representeras av rapporten.

I tabellerna ovan har vi utelämnat de sällan använda fälten räknas, min, max, stdDev och sampledValue.

I stället för att sammanställa mått kan du använda [sampling](../../azure-monitor/app/sampling.md) om du behöver minska volymen av telemetri.

### <a name="durations"></a>Varaktigheter
Om inget annat anges representeras varaktigheterna i tiondelar av en mikrosekt, så att 10000000,0 betyder 1 sekund.

## <a name="see-also"></a>Se även
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Kontinuerlig export](export-telemetry.md)
* [Kodexempel](export-telemetry.md#code-samples)
