---
title: Webhook-åtgärder för logg aviseringar i Azure-aviseringar
description: Den här artikeln beskriver hur du skapar en logg aviserings regel med hjälp av Log Analytics arbets ytan eller Application Insights, hur aviseringen skickar data som en HTTP-webhook och information om de olika anpassningar som är möjliga.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6c9bacfc4354351cbbf2eb735414ff3334cd7d0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84323679"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-åtgärder för logg aviserings regler
När en [logg avisering skapas i Azure](alerts-log.md)har du möjlighet att [Konfigurera den med hjälp av åtgärds grupper](action-groups.md) för att utföra en eller flera åtgärder. Den här artikeln beskriver de olika webhook-åtgärder som är tillgängliga och visar hur du konfigurerar en anpassad JSON-baserad webhook.

> [!NOTE]
> Du kan också använda [vanliga aviserings scheman](https://aka.ms/commonAlertSchemaDocs) för webhook-integreringar. Det vanliga aviserings schemat ger fördelarna med att ha en enda utöknings bar och enhetlig aviserings nytto last för alla aviserings tjänster i Azure Monitor. Observera att det gemensamma aviserings schemat inte uppfyller det anpassade JSON-alternativet för logg aviseringar. Den fördröjer den vanliga nytto lasten för aviserings schema om det är markerat oavsett vilken anpassning du kan ha gjort på varnings regel nivån. [Lär dig mer om vanliga aviserings schema definitioner.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook-åtgärder

Med webhook-åtgärder kan du anropa en extern process genom en enda HTTP POST-begäran. Tjänsten som anropas bör ha stöd för Webhooks och avgöra hur du använder den nytto last som den tar emot.

Webhook-åtgärder kräver egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| **Webhook-URL** |Webhookens URL. |
| **Anpassad JSON-nyttolast** |Den anpassade nytto lasten som ska skickas med webhooken när det här alternativet väljs när aviseringen skapas. Mer information finns i [Hantera logg aviseringar](alerts-log.md).|

> [!NOTE]
> Knappen **Visa webhook** tillsammans med alternativet **inkludera anpassad JSON-nyttolast för webhook** för logg aviseringen visar exempel på webhook-nyttolasten för den anpassning som tillhandahölls. Den innehåller inte faktiska data utan är representativ för det JSON-schema som används för logg aviseringar. 

Webhooks innehåller en URL och en nytto last som är formaterad i JSON som data som skickas till den externa tjänsten. Som standard innehåller nytto lasten värdena i följande tabell. Du kan välja att ersätta den här nytto lasten med en egen egen. I så fall använder du variablerna i tabellen för var och en av parametrarna för att ta med värdena i din anpassade nytto Last.


| Parameter | Variabel | Beskrivning |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Aviserings regelns namn. |
| *Allvarlighetsgrad* |#severity |Allvarlighets grad har angetts för den utlöst logg aviseringen. |
| *AlertThresholdOperator* |#thresholdoperator |Tröskel operator för varnings regeln, som använder större eller mindre än. |
| *AlertThresholdValue* |#thresholdvalue |Tröskel värde för varnings regeln. |
| *LinkToSearchResults* |#linktosearchresults |Länk till analys portalen som returnerar poster från frågan som skapade aviseringen. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Länk till analys-API: et som returnerar poster från frågan som skapade aviseringen. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Länk till analys portalen som returnerar poster från frågan som filtrerats efter dimensions värde kombinationer som skapade aviseringen. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Länk till den analys-API som returnerar poster från frågan som filtrerats efter dimensions värde kombinationer som skapade aviseringen. |
| *Resultcount som* |#searchresultcount |Antal poster i Sök resultaten. |
| *Slut tid för Sök intervall* |#searchintervalendtimeutc |Slut tid för frågan i UTC, med formatet MM/DD/ÅÅÅÅ HH: mm: SS. |
| *Sök intervall* |#searchinterval |Tids period för varnings regeln med formatet HH: mm: SS. |
| *Sök intervall StartTime* |#searchintervalstarttimeutc |Start tid för frågan i UTC, med formatet MM/DD/ÅÅÅÅ HH: mm: SS. 
| *SearchQuery* |#searchquery |Loggs öknings fråga som används av varnings regeln. |
| *SearchResults* |"IncludeSearchResults": true|Poster som returneras av frågan som en JSON-tabell, begränsade till de första 1 000 posterna. "IncludeSearchResults": true läggs till i en anpassad JSON webhook-definition som en egenskap på den översta nivån. |
| *Dimensioner* |"IncludeDimensions": true|Dimensions värde kombinationer som utlöste denna avisering som ett JSON-avsnitt. "IncludeDimensions": true läggs till i en anpassad JSON webhook-definition som en egenskap på den översta nivån. |
| *Aviserings typ*| #alerttype | Typ av logg aviserings regel som kon figurer ATS som [mått mätning](alerts-unified-log.md#metric-measurement-alert-rules) eller [antal resultat](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID för din Log Analytics-arbetsyta. |
| *Program-ID* |#applicationid |ID för Application Insights-appen. |
| *Prenumerations-ID:t* |#subscriptionid |ID för din Azure-prenumeration som används. 

> [!NOTE]
> De angivna länkarna skickar parametrar som *SearchQuery*, *sökintervallet StartTime*och *Sök INTERVALLETS slut tid* i URL: en till Azure Portal eller API.

Du kan till exempel ange följande anpassade nytto last som innehåller en enda parameter som kallas *text*. Tjänsten som denna webhook anropar förväntar sig denna parameter.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Den här exempel nytto lasten matchas till något som liknar följande när den skickas till webhooken:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Eftersom alla variabler i en anpassad webhook måste anges i ett JSON-hölje, t. ex. "#searchinterval", innehåller den resulterande webhooken även variabla data i höljen, till exempel "00:05:00".

Om du vill inkludera Sök resultat i en anpassad nytto Last kontrollerar du att **IncludeSearchResults** har angetts som en egenskap på den översta nivån i JSON-nyttolasten. 

## <a name="sample-payloads"></a>Exempel på nytto laster
I det här avsnittet visas exempel på nytto laster för Webhooks för logg aviseringar. Exempel på nytto laster innehåller exempel när nytto lasten är standard och när den är anpassad.

### <a name="standard-webhook-for-log-alerts"></a>Standard-webhook för logg aviseringar 
Båda dessa exempel har en provdockans nytto last med endast två kolumner och två rader.

#### <a name="log-alert-for-log-analytics"></a>Logg avisering för Log Analytics
Följande exempel på nytto Last är för en standard-webhook-åtgärd *utan ett anpassat JSON-alternativ* som används för aviseringar baserat på Log Analytics:

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
 ```

> [!NOTE]
> Värdet för fältet allvarlighets grad kan ändras om du har [ändrat API-inställningen](alerts-log-api-switch.md) för logg aviseringar på Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Logg avisering för Application Insights
Följande exempel på nytto Last är för en standard-webhook *utan ett anpassat JSON-alternativ* när den används för logg aviseringar baserat på Application Insights:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Logg avisering med anpassad JSON-nyttolast
Om du till exempel vill skapa en anpassad nytto last som endast innehåller aviserings namnet och Sök resultaten kan du använda följande: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Följande exempel på nytto Last är för en anpassad webhook-åtgärd för valfri logg avisering:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
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
- Lär dig mer om [logg aviseringar i Azure-aviseringar](alerts-unified-log.md).
- Förstå hur du [hanterar logg aviseringar i Azure](alerts-log.md).
- Skapa och hantera [Åtgärds grupper i Azure](action-groups.md).
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
- Läs mer om [logg frågor](../log-query/log-query-overview.md). 

