---
title: "Webhook-åtgärder för logg-aviseringar i Azure aviseringar (förhandsversion) | Microsoft Docs"
description: "Den här artikeln beskriver hur en logg varningsregeln med log analytics eller application insights, skickar data som HTTP-webhook och information om olika anpassningar möjligt."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 49905638-f9f2-427b-8489-a0bcc7d8b9fe
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: 4af1bb61888810011ce64fde7931cabfefe76ab6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-åtgärder för log Varningsregler
När en [aviseringen har skapats i Azure (förhandsversion)](monitor-alerts-unified-usage.md), har möjlighet att [konfigurera med åtgärdsgrupper](monitoring-action-groups.md) att utföra en eller flera åtgärder.  Den här artikeln beskrivs olika webhook-åtgärder som är tillgängliga och information om hur du konfigurerar anpassade JSON-baserade webhooken.


## <a name="webhook-actions"></a>Webhook-åtgärder

Webhook-åtgärder kan du anropa en extern process via en enkel HTTP POST-begäran.  Tjänsten som anropas bör stöder webhooks och kontrollera hur den använder alla nyttolast tas emot.  Du kan också kontakta en REST-API som inte uttryckligen stöder webhooks som begäran finns i ett format som kan användas med API: et.  Exempel på användning av en webhook som svar på en avisering skickas ett meddelande [Slack](http://slack.com) eller skapa en incident i [PagerDuty](http://pagerduty.com/).  En fullständig genomgång för att skapa en aviseringsregel med en webhook att anropa Slack finns på [Webhooks i logganalys aviseringar](../log-analytics/log-analytics-alerts-webhooks.md).

Webhook-åtgärder kräver egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Webhooksadressen |URL till webhooken. |
| Anpassad JSON-nyttolast |Anpassad nyttolast för att skicka med webhooken.  Om alternativet är valt under skapande av varning. Information som finns på [Hantera aviseringar via Azure aviseringar (förhandsgranskning)](monitor-alerts-unified-usage.md) |

> [!NOTE]
> Testa Webhook-knappen bredvid *inkludera anpassad JSON-nyttolast för webhook* för loggen avisering, utlöser dummy anrop för att testa Webhooksadressen. Den innehåller inte faktiska data eller representativ för JSON-schema för logg-aviseringar. Medan du kan testa alla en webhook med anpassad JSON i representant skickas alla webhooks som konfigurerats i grupp med anpassad JSON-nyttolast.

Webhooks är en URL och en nyttolast som har formaterats i JSON som är data som skickas till externa-tjänsten.  Som standard innehåller nyttolasten värdena i tabellen nedan.  Du kan välja att ersätta den här nyttolasten med ett anpassat egen.  Du kan i så fall använda variabler i tabellen för var och en av parametrarna ta sina värdet i din anpassade nyttolast.


| Parameter | Variabel | Beskrivning |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Namnet på regeln. |
| AlertThresholdOperator |#thresholdoperator |Tröskelvärdet operator för regeln.  *Större än* eller *mindre än*. |
| AlertThresholdValue |#thresholdvalue |Tröskelvärde för regeln. |
| LinkToSearchResults |#linktosearchresults |Länka till logganalys loggen sökning som returnerar poster från frågan som skapade aviseringen. |
| ResultCount |#searchresultcount |Antalet poster i sökresultaten. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Sluttid för frågan i UTC-format. |
| SearchIntervalInSeconds |#searchinterval |Tidsfönstret för regeln. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Starttid för frågan i UTC-format. |
| searchQuery |#searchquery |Loggen sökfråga används av regeln. |
| SearchResults |"IncludeSearchResults":true|Poster som returneras av frågan som en JSON-tabellen, begränsas till de första 1 000 poster/raderna; Om ”IncludeSearchResults”: true läggs till i anpassad JSON webhook definition som en egenskap för på den översta nivån |
| WorkspaceID |#workspaceid |ID för logganalys-arbetsytan. |
| Allvarsgrad |#severity |Allvarlighetsgrad för aviseringen Eldad loggen. |

Du kan till exempel ange följande anpassad nyttolast som innehåller en enda parameter med namnet *text*.  Den tjänst som denna webhook anropar skulle förväntas den här parametern.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Nyttolasten i det här exemplet skulle matchas till något som liknar följande när du skickar webhooken.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Se till att inkludera sökresultat i en anpassad nyttolast genom **IncudeSearchResults** har angetts som en översta egenskap i json-nyttolast.


Du kan gå igenom en komplett exempel på hur du skapar en aviseringsregel med en webhook att starta en extern tjänst på [skapar en avisering webhook-åtgärd i logganalys att skicka meddelande till Slack](../log-analytics/log-analytics-alerts-webhooks.md).

## <a name="sample-payload"></a>Exempel nyttolast
Det här avsnittet visas exempel nyttolasten för webhook för loggen aviseringar, inklusive när nyttolasten är standard och när dess egna.

> [!NOTE]
> För att säkerställa bakåtkompatibilitet standard webhook nyttolasten för aviseringar via Azure Log Analytics är samma som [OMS Varna management](../log-analytics/log-analytics-solution-alert-management.md). Men för log aviseringar via [Programinsikter](../application-insights/app-insights-analytics.md), standard webhook-nyttolasten är baserad på grupp schema

### <a name="standard-webhook-for-log-alerts"></a>Standard Webhook för logg-aviseringar
Båda dessa exempel vi ha angett en dummy nyttolast med bara två kolumner och två rader.

#### <a name="log-alert-for-azure-log-analytics"></a>Avisering om loggfilen för Azure logganalys
Följande är ett exempel nyttolasten för en åtgärd som standard webhook utan anpassade Json när den används för log analytics-baserade log-aviseringar.

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
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }



#### <a name="log-alert-for-azure-application-insights"></a>Avisering om loggfilen för Azure Application Insights
Följande är ett exempel nyttolasten för en åtgärd som standard webhook utan anpassade Json när den används för application insights-baserade log-aviseringar.


    {
    "schemaId":"LogAlert","data":
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
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>Logga avisering med anpassad JSON-nyttolast
Om du vill skapa en anpassad nyttolast som innehåller bara aviseringsnamn och sökresultaten kan du exempelvis använda följande.

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

Följande är ett exempel nyttolasten för en anpassad webhook-åtgärder för alla aviseringar om loggen.


    {
    "AlertRuleName":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
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




## <a name="next-steps"></a>Nästa steg
- Skapa och hantera [åtgärdsgrupper i Azure](monitoring-action-groups.md)
- Lär dig mer om [loggen aviseringar i Azure aviseringar (förhandsgranskning)](monitor-alerts-unified-log.md)
