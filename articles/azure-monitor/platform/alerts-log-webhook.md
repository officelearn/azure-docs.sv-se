---
title: Webhook-åtgärder för loggaviseringar i Azure-aviseringar
description: Den här artikeln beskrivs hur en aviseringsregel för loggen med hjälp av log analytics eller application insights skickar data som HTTP-webhook och information om de olika anpassningarna möjligt.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 908422927feabd156c5dcdc7a04d44ff8fc42094
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442893"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-åtgärder för loggaviseringsregler
När en [log aviseringen har skapats i Azure](alerts-log.md), har möjlighet att [konfigurera med åtgärdsgrupper](action-groups.md) att utföra en eller flera åtgärder.  Den här artikeln beskrivs olika webhook-åtgärder som är tillgängliga och information om hur du konfigurerar anpassade JSON-baserade webhooken.


## <a name="webhook-actions"></a>Webhook-åtgärder

Webhook-åtgärder kan du anropa en extern process via en HTTP POST-begäran.  Tjänsten som anropas ska stödja webhooks och bestämma hur den använder alla nyttolast tas emot.    

Webhook-åtgärder kräver egenskaperna i följande tabell:

| Egenskap  | Beskrivning |
|:--- |:--- |
| Webhook-URL |URL till webhooken. |
| Anpassad JSON-nyttolast |Anpassad nyttolast ska skicka med webhook, när det här alternativet väljs under skapande av varning. Information som är tillgängliga på [Hantera aviseringar](alerts-log.md) |

> [!NOTE]
> Visa Webhook-knappen bredvid *inkludera anpassad JSON-nyttolast för webhook* för Log aviseringar kommer att visa exempel webhook-nyttolasten för anpassning som tillhandahålls. Den innehåller inte faktiska data och representativ för JSON-schema används för aviseringar. 

Webhooks är en URL och en nyttolast som formaterats i JSON som är data som skickas till den externa tjänsten.  Som standard innehåller nyttolasten värdena i tabellen nedan:  Du kan välja att ersätta den här nyttolasten med en anpassad på egen hand.  Du kan i så fall använda variabler i tabellen för var och en av parametrarna ta sina värdet i din anpassade nyttolast.


| Parameter | Variabel | Beskrivning |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Namnet på regeln. |
| Severity |#severity |Allvarlighetsgrad för aviseringen skickades log. |
| AlertThresholdOperator |#thresholdoperator |Tröskeloperator för regeln.  *Större än* eller *mindre än*. |
| AlertThresholdValue |#thresholdvalue |Tröskelvärde för regeln. |
| LinkToSearchResults |#linktosearchresults |Länka till Analytics-portalen som returnerar poster från den fråga som skapade aviseringen. |
| Resultcount som |#searchresultcount |Antalet poster i sökresultaten. |
| Söktiden för intervallslut |#searchintervalendtimeutc |Sluttid för frågan i UTC, format - mm/dd/åååå hh: mm: ss AM/PM. |
| Intervall för sökning |#searchinterval |Tidsperioden för aviseringen regeln för format -: mm: ss. |
| Sök efter intervall StartTime |#searchintervalstarttimeutc |Starttid för frågan i UTC, format - mm/dd/åååå hh: mm: ss AM/PM... 
| SearchQuery |#searchquery |Logga sökfråga används av regeln. |
| SearchResults |"IncludeSearchResults": true|Poster som returneras av frågan som en JSON-tabell, begränsad till de första 1 000 posterna. Om ”IncludeSearchResults”: true läggs till i anpassade JSON webhook-definition som en översta egenskap. |
| WorkspaceID |#workspaceid |ID för Log Analytics-arbetsytan. |
| Program-ID:t |#applicationid |ID för Application Insights app. |
| Prenumerations-ID:t |#subscriptionid |ID för din Azure-prenumeration som används med Application Insights. 

> [!NOTE]
> LinkToSearchResults skickar parametrar som SearchQuery, Sök intervall StartTime & intervallslut för sökning i URL: en till Azure-portalen för visning i Analytics-avsnittet. Azure-portalen har URI storleksgränsen på cirka 2 000 tecken och kommer *inte* öppna länken i aviseringar om parametervärdena överskrider denna gräns. Användarna kan manuellt ange information om du vill visa resultatet i Analytics-portalen eller använda den [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) eller [Log Analytics REST API](https://dev.loganalytics.io/reference) att hämta resultat programmässigt 

Du kan till exempel ange följande anpassade nyttolasten som innehåller en enda parameter med namnet *text*.  Den tjänst som denna webhook-anrop skulle förväntas den här parametern.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Den här exempel-nyttolasten löser till något som liknar följande när de skickas till webhooken.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Eftersom alla variabler i en anpassad webhook måste anges i JSON-hölje som ”#searchinterval”, har också gällande webhooken variabeln data i hölje som ”00: 05:00”.

För att inkludera sökresultat i en anpassad nyttolast, kontrollerar du att **IncludeSearchResults** har angetts som en översta egenskap i json-nyttolast. 

## <a name="sample-payloads"></a>Exempel-nyttolaster
Det här avsnittet visas exempel på en nyttolast för webhook för Loggaviseringar, inklusive när nyttolasten är standard och när dess anpassade.

### <a name="standard-webhook-for-log-alerts"></a>Standard-Webbhook för Loggaviseringar 
Båda exemplen ha angett en dummy-nyttolast med bara två kolumner och två rader.

#### <a name="log-alert-for-azure-log-analytics"></a>Log avisering för Azure logganalys
Följande är ett exempel på en nyttolast för en standard webhook-åtgärd *utan anpassade Json-alternativet* som används för log analytics-baserade aviseringar.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
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
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```

> [!NOTE]
> Allvarlighetsgrad för fältet kan ändras om du har [växlas föredrar API](alerts-log-api-switch.md) för loggaviseringar i Log Analytics.


#### <a name="log-alert-for-azure-application-insights"></a>Log avisering för Azure Application Insights
Följande är ett exempel på en nyttolast för en standard webhook *utan anpassade Json-alternativet* när den används för application insights-baserade log-aviseringar.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
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
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Loggvarningsregler med anpassad JSON-nyttolast
Om du vill skapa en anpassad nyttolast som innehåller bara aviseringsnamn och sökresultaten kan du till exempel använda följande: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Följande är ett exempel på en nyttolast för en anpassad webhook-åtgärd för alla log-avisering.
    
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
- Lär dig mer om [Loggaviseringar i Azure-aviseringar](alerts-unified-log.md)
- Förstå [hantera loggaviseringar i Azure](alerts-log.md)
- Skapa och hantera [åtgärdsgrupper i Azure](action-groups.md)
- Läs mer om [Application Insights](../../azure-monitor/app/analytics.md)
- Läs mer om [Log Analytics](../../azure-monitor/log-query/log-query-overview.md). 

