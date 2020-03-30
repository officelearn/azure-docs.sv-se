---
title: Webhook-åtgärder för loggaviseringar i Azure-aviseringar
description: I den här artikeln beskrivs hur du skapar en loggaviseringsregel med hjälp av log analytics-arbetsytan eller application insights, hur aviseringen skickar data som en HTTP-webhook och information om de olika anpassningar som är möjliga.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667711"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-åtgärder för loggvarningsregler
När en [loggavisering skapas i Azure](alerts-log.md)har du möjlighet att konfigurera den [med hjälp av åtgärdsgrupper](action-groups.md) för att utföra en eller flera åtgärder. I den här artikeln beskrivs de olika webhook-åtgärder som är tillgängliga och hur du konfigurerar en anpassad JSON-baserad webhook.

> [!NOTE]
> Du kan också använda det [vanliga varningsschemat](https://aka.ms/commonAlertSchemaDocs) för webhook-integreringar. Det gemensamma varningsschemat ger fördelen av att ha en enda utökningsbar och enhetlig varningsnyttolast över alla varningstjänster i Azure Monitor.Observera att det gemensamma varningsschemat inte uppfyller det anpassade JSON-alternativet för loggaviseringar. Den skjuter upp till den vanliga nyttolasten för varningsschema om det är markerat oavsett vilken anpassning du kan ha gjort på varningsregelnivå. [Läs mer om de vanliga definitionerna för varningsscheman.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook-åtgärder

Med webhook-åtgärder kan du anropa en extern process via en enda HTTP POST-begäran. Tjänsten som anropas bör stödja webhooks och bestämma hur du använder någon nyttolast den tar emot.

Webhook-åtgärder kräver egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| **Webhook URL** |Url:en för webhooken. |
| **Anpassad JSON-nyttolast** |Den anpassade nyttolasten som ska skickas med webhooken när det här alternativet väljs när aviseringen skapas. Mer information finns i [Hantera loggaviseringar](alerts-log.md).|

> [!NOTE]
> Knappen **Visa webhook** tillsammans med alternativet **Inkludera anpassad JSON-nyttolast för webhook** för loggvarningen visar exempelwebbkroksnyttolasten för den anpassning som tillhandahölls. Den innehåller inte faktiska data men är representativ för JSON-schemat som används för loggaviseringar. 

Webhooks innehåller en URL och en nyttolast formaterad i JSON att de data som skickas till den externa tjänsten. Som standard innehåller nyttolasten värdena i följande tabell. Du kan välja att ersätta den här nyttolasten med en egen. I så fall använder du variablerna i tabellen för var och en av parametrarna för att inkludera deras värden i din anpassade nyttolast.


| Parameter | Variabel | Beskrivning |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Namnet på varningsregeln. |
| *Allvarlighetsgrad* |#severity |Allvarlighetsgrad inställd för den avfyrade loggvarningen. |
| *AlertThresholdOperator* |#thresholdoperator |Tröskelvärdesoperator för varningsregeln, som använder mer än eller mindre än. |
| *AlertThresholdValue* |#thresholdvalue |Tröskelvärde för aviseringsregeln. |
| *LinkToSearchResultats* |#linktosearchresults |Länk till Analytics-portalen som returnerar posterna från frågan som skapade aviseringen. |
| *Resultaträkning* |#searchresultcount |Antal poster i sökresultaten. |
| *Sluttid för sökintervall* |#searchintervalendtimeutc |Sluttid för frågan i UTC, med formatet mm/dd/yyyy HH:mm:ss AM/PM. |
| *Sökintervall* |#searchinterval |Tidsfönster för varningsregeln med formatet HH:mm:ss. |
| *Starttid för sökintervall* |#searchintervalstarttimeutc |Starttid för frågan i UTC, med formatet mm/dd/yyyy HH:mm:ss AM/PM. 
| *Sökknry* |#searchquery |Loggsökningsfråga som används av varningsregeln. |
| *SökResultat* |"IncludeSearchResults": sant|Poster som returneras av frågan som en JSON-tabell, begränsade till de första 1 000 posterna, om "IncludeSearchResults": true läggs till i en anpassad JSON webhook-definition som en egenskap på den högsta nivån. |
| *Typ av varning*| #alerttype | Den typ av loggaviseringsregel som konfigurerats som [måttmätning](alerts-unified-log.md#metric-measurement-alert-rules) eller [Antal resultat](alerts-unified-log.md#number-of-results-alert-rules).|
| *ArbetsutrymmeID* |#workspaceid |ID för din Log Analytics-arbetsyta. |
| *Program-ID:t* |#applicationid |ID för appen Application Insights. |
| *Prenumerations-ID* |#subscriptionid |ID för din Azure-prenumeration som används. 

> [!NOTE]
> *LinkToSearchResults* skickar parametrar som *SearchQuery,* *Search Interval StartTime*och *Sökintervall sluttid* i URL:en till Azure-portalen för visning i analytics-avsnittet. Azure-portalen har en URI-storleksgräns på cirka 2 000 tecken. Portalen kommer *inte* att öppna länkar som tillhandahålls i aviseringar om parametervärdena överskrider gränsen. Du kan mata in information manuellt om du vill visa resultat i Analytics-portalen. Du kan också använda [REST-API:et för Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) eller [REST-API:et för logganalys](/rest/api/loganalytics/) för att hämta resultat programmässigt. 

Du kan till exempel ange följande anpassade nyttolast som innehåller en enda parameter som kallas *text*. Tjänsten som den här webhook anropar förväntar sig den här parametern.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Det här exemplet nyttolast löser till något liknande när den skickas till webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Eftersom alla variabler i en anpassad webhook måste anges i en JSON-hölje, till exempel "#searchinterval", har den resulterande webhooken också variabla data inuti höljen, till exempel "00:05:00".

Om du vill inkludera sökresultat i en anpassad nyttolast kontrollerar du att **IncludeSearchResults** anges som en egenskap på den högsta nivån i JSON-nyttolasten. 

## <a name="sample-payloads"></a>Exempel på nyttolaster
I det här avsnittet visas exempelnyttolaster för webhooks för loggaviseringar. Exempelnyttolaster innehåller exempel när nyttolasten är standard och när den är anpassad.

### <a name="standard-webhook-for-log-alerts"></a>Standardwebbkrok för loggvarningar 
Båda dessa exempel har en dummy nyttolast med bara två kolumner och två rader.

#### <a name="log-alert-for-log-analytics"></a>Loggavisering för Logganalys
Följande exempelnyttolast är för en standard webhook-åtgärd *utan ett anpassat JSON-alternativ* som används för aviseringar baserat på Logganalys:

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
> Fältvärdet "Allvarlighetsgrad" kan ändras om du har [bytt API-inställningar](alerts-log-api-switch.md) för loggaviseringar i Logganalys.


#### <a name="log-alert-for-application-insights"></a>Loggavisering för programinsikter
Följande exempelnyttolast är för en vanlig webhook *utan ett anpassat JSON-alternativ* när den används för loggaviseringar baserat på Application Insights:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Loggavisering med anpassad JSON-nyttolast
Om du till exempel vill skapa en anpassad nyttolast som bara innehåller aviseringsnamnet och sökresultaten kan du använda följande: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Följande exempelnyttolast är för en anpassad webhook-åtgärd för alla loggaviseringar:
    
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
- Läs mer om [loggaviseringar i Azure-aviseringar](alerts-unified-log.md).
- Förstå hur [du hanterar loggaviseringar i Azure](alerts-log.md).
- Skapa och hantera [åtgärdsgrupper i Azure](action-groups.md).
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
- Läs mer om [loggfrågor](../log-query/log-query-overview.md). 

