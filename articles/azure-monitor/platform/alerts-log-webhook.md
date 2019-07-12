---
title: Webhook-åtgärder för loggaviseringar i Azure-aviseringar
description: Den här artikeln beskriver hur du skapar en loggvarningsregel med hjälp av Log Analytics-arbetsyta eller Application Insights, hur aviseringen skickar data som en HTTP-webhook och information om de olika anpassningar som är möjliga.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705196"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-åtgärder för loggaviseringsregler
När en [log aviseringen har skapats i Azure](alerts-log.md), har möjlighet att [konfigurera den med hjälp av åtgärdsgrupper](action-groups.md) att utföra en eller flera åtgärder. Den här artikeln beskriver de olika webhook-åtgärder som är tillgängliga och visar hur du konfigurerar en anpassad JSON-baserade webhook.

> [!NOTE]
> Du kan också använda den [gemensamma avisering schemat](https://aka.ms/commonAlertSchemaDocs) för webhook-integreringar. Det gemensamma schemat för avisering innehåller fördelen att en enda omfattande och enhetligt avisering nyttolast alla aviseringar tjänster i Azure Monitor. [Läs mer om vanliga avisering schemadefinitioner.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook-åtgärder

Du kan anropa en extern process via en HTTP POST-begäran med webhook-åtgärder. Den tjänst som kallas bör stöder webhooks och Bestäm hur du använder alla nyttolast som den tar emot.

Webhook-åtgärder kräver egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| **Webhook-URL** |URL till webhooken. |
| **Anpassad JSON-nyttolast** |Anpassad nyttolast ska skicka med webhooken när det här alternativet väljs under skapande av varning. Mer information finns i [hantera loggvarningar](alerts-log.md).|

> [!NOTE]
> Den **visa Webhook** knappen tillsammans med den **inkludera anpassad JSON-nyttolast för webhook** alternativet för log-aviseringen visar exempel webhook-nyttolasten för anpassning som har angetts. Den innehåller inte faktiska data men är representativ för JSON-schemat som används för aviseringar. 

Webhooks är en URL och en nyttolast som formaterats i JSON som informationen som skickas till den externa tjänsten. Som standard innehåller nyttolasten värdena i tabellen nedan. Du kan välja att ersätta den här nyttolasten med en anpassad på egen hand. I så fall använda variabler i tabellen för var och en av parametrarna för att inkludera deras värden i din anpassade nyttolast.


| Parameter | Variabel | Beskrivning |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Namnet på regeln. |
| *Allvarlighetsgrad* |#severity |Allvarlighetsgrad för aviseringen skickades log. |
| *AlertThresholdOperator* |#thresholdoperator |Tröskeloperator för regeln, som använder större eller mindre än. |
| *AlertThresholdValue* |#thresholdvalue |Tröskelvärde för regeln. |
| *LinkToSearchResults* |#linktosearchresults |Länka till Analytics-portalen som returnerar poster från den fråga som skapade aviseringen. |
| *ResultCount* |#searchresultcount |Antalet poster i sökresultaten. |
| *Söktiden för intervallslut* |#searchintervalendtimeutc |Sluttid för frågan i UTC, med formatet mm/dd/åååå hh: mm: ss AM/PM. |
| *Intervall för sökning* |#searchinterval |Tidsfönster för regeln, med formatet: mm: ss. |
| *Sök efter intervall StartTime* |#searchintervalstarttimeutc |Starttid för frågan i UTC, med formatet mm/dd/åååå hh: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |Logga sökfråga används av regeln. |
| *SearchResults* |"IncludeSearchResults": true|Poster som returneras av frågan som en JSON-tabell, begränsad till de första 1 000 posterna, om ”IncludeSearchResults”: true läggs till i en anpassad JSON webhook-definition som en översta egenskap. |
| *Typ av avisering*| #alerttype | Typ av loggvarningsregel som konfigurerats som [metriska måttenheter](alerts-unified-log.md#metric-measurement-alert-rules) eller [antal resultat](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID för Log Analytics-arbetsytan. |
| *Program-ID* |#applicationid |ID för Application Insights app. |
| *Prenumerations-ID* |#subscriptionid |ID för din Azure-prenumeration används. 

> [!NOTE]
> *LinkToSearchResults* skickar parametrar som *SearchQuery*, *Search intervall StartTime*, och *Search intervall sluttid* i URL: en till Azure Portal för visning i avsnittet Analytics. Azure-portalen har en URI storleksgräns på cirka 2 000 tecken. Portalen kommer *inte* öppna länkarna i aviseringarna om parametervärdena som överskrider gränsen. Du kan manuellt ange information om du vill visa resultat i Analytics-portalen. Du kan också använda den [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) eller [Log Analytics REST API](/rest/api/loganalytics/) att hämta resultat programmässigt. 

Du kan till exempel ange följande anpassade nyttolasten som innehåller en enda parameter med namnet *text*. Den här parametern förväntar sig att den tjänst som denna webhook-anrop.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Den här exempel-nyttolasten löser till något som liknar följande när det skickas till webhooken:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Eftersom alla variabler i en anpassad webhook måste anges i ett JSON-hölje som ”#searchinterval”, gällande webhooken har också variabeln data i höljen, som ”00: 05:00”.

För att inkludera sökresultat i en anpassad nyttolast, kontrollerar du att **IncludeSearchResults** har angetts som en översta egenskap i JSON-nyttolast. 

## <a name="sample-payloads"></a>Exempel-nyttolaster
Det här avsnittet visas exempel nyttolaster för webhooks för aviseringar. Exempel-nyttolaster innehåller exempel när nyttolasten är standard och när den är anpassat.

### <a name="standard-webhook-for-log-alerts"></a>Standard-webbhook för loggaviseringar 
Båda dessa exempel har en dummy-nyttolast med bara två kolumner och två rader.

#### <a name="log-alert-for-log-analytics"></a>Log aviseringen för Log Analytics
Följande exempel nyttolasten är för en standard webhook-åtgärd *utan en anpassad JSON-alternativet* som används för aviseringar baserat på Log Analytics:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Fältet ”allvarlighetsgrad” kan ändras om du har [växlas föredrar API](alerts-log-api-switch.md) för loggaviseringar i Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Log aviseringen för Application Insights
Följande exempel nyttolasten är för en standard webhook *utan en anpassad JSON-alternativet* när den används för aviseringar baserat på Application Insights:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Log avisering med anpassad JSON-nyttolast
Om du vill skapa en anpassad nyttolast som innehåller bara aviseringsnamn och sökresultaten kan du till exempel använda följande: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Nyttolasten i följande exempel är en anpassad webhook-åtgärd för alla log-avisering:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [loggaviseringar i Azure-aviseringar](alerts-unified-log.md).
- Förstå hur du [hantera loggaviseringar i Azure](alerts-log.md).
- Skapa och hantera [åtgärdsgrupper i Azure](action-groups.md).
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
- Läs mer om [logga frågor](../log-query/log-query-overview.md). 

