---
title: Svar på aviseringar i Azure Log Analytics | Microsoft Docs
description: Aviseringar i Log Analytics kan identifiera viktig information på din arbetsyta för Azure och proaktivt meddelar dig om problem eller anropa åtgärder om du vill försöka åtgärda.  Den här artikeln beskriver hur du skapar en aviseringsregel och information om olika åtgärder som de kan ta.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 717adf1b19b9de8542ec507df3a01b187d0df8a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31521072"
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Lägg till åtgärder i Varningsregler i logganalys

> [!NOTE]
> Aviseringar i logganalys [förlängs till Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md).  Aviseringar i Azure används [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md) definiera sina åtgärder i stället för informationen i den här artikeln.


När en [avisering skapas i logganalys](log-analytics-alerts.md), har möjlighet att [konfigurera varningsregeln](log-analytics-alerts.md) att utföra en eller flera åtgärder.  Den här artikeln beskrivs olika åtgärder som är tillgängliga och information om hur du konfigurerar varje slag.

| Åtgärd | Beskrivning |
|:--|:--|
| [E-post](#email-actions) | Skicka ett e-postmeddelande med information om aviseringen till en eller flera mottagare. |
| [Webhook](#webhook-actions) | Anropa en extern process via en enkel HTTP POST-begäran. |
| [runbook](#runbook-actions) | Starta en runbook i Azure Automation. |


## <a name="email-actions"></a>E-post-åtgärder
E-post-åtgärder skicka ett e-postmeddelande med information om aviseringen till en eller flera mottagare.  Du kan ange ämnet för e-postmeddelandet, men innehållet är ett standardformat som Log Analytics sammanställer.  Den innehåller översiktsinformation till exempel namnet för aviseringen förutom information om upp till tio poster som returneras av loggen sökningen.  Den innehåller också en länk till en logg sökning i logganalys som returnerar hela uppsättningen av poster från frågan.   Avsändaren av e-postmeddelandet är *Microsoft Operations Management Suite-teamet &lt; noreply@oms.microsoft.com &gt;* . 

E-post-åtgärder kräver egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Ämne |Ämne för e-postmeddelandet.  Du kan inte ändra innehållet i e-postmeddelandet. |
| Mottagare |Tar med alla e-postmottagare.  Om du anger mer än en adress kan du avgränsa dem med semikolon (;). |


## <a name="webhook-actions"></a>Webhook-åtgärder

Webhook-åtgärder kan du anropa en extern process via en enkel HTTP POST-begäran.  Tjänsten som anropas bör stöder webhooks och avgöra hur den använder alla nyttolast tas emot.  Du kan också kontakta en REST-API som inte uttryckligen stöder webhooks som begäran finns i ett format som kan användas med API: et.  Exempel på användning av en webhook som svar på en avisering skickas ett meddelande [Slack](http://slack.com) eller skapa en incident i [PagerDuty](http://pagerduty.com/).  En fullständig genomgång för att skapa en aviseringsregel med en webhook att anropa Slack finns på [Webhooks i logganalys aviseringar](log-analytics-alerts-webhooks.md).

Webhook-åtgärder kräver egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Webhooksadressen |URL till webhooken. |
| Anpassad JSON-nyttolast |Anpassad nyttolast för att skicka med webhooken.  Mer information finns i nedan. |


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
| SearchResults |Se nedan |Poster som returneras av frågan i JSON-format.  Begränsad till de första 5 000 posterna. |
| WorkspaceID |#workspaceid |ID för logganalys-arbetsytan. |

Du kan till exempel ange följande anpassad nyttolast som innehåller en enda parameter med namnet *text*.  Den tjänst som denna webhook anropar skulle förväntas den här parametern.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Nyttolasten i det här exemplet skulle matchas till något som liknar följande när du skickar webhooken.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Inkludera sökresultat i en anpassad nyttolast genom att lägga till följande rad som en översta egenskap i json-nyttolast.  

    "IncludeSearchResults":true

Om du vill skapa en anpassad nyttolast som innehåller bara aviseringsnamn och sökresultaten kan du exempelvis använda följande. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Du kan gå igenom en komplett exempel på hur du skapar en aviseringsregel med en webhook att starta en extern tjänst på [skapar en avisering webhook-åtgärd i logganalys att skicka meddelande till Slack](log-analytics-alerts-webhooks.md).


## <a name="runbook-actions"></a>Runbook-åtgärder
Runbook-åtgärder startar en runbook i Azure Automation.  För att kunna använda den här typen av åtgärden, måste du ha den [automatiseringslösning](log-analytics-add-solutions.md) installeras och konfigureras i logganalys-arbetsytan.  Du kan välja från runbooks i automation-kontot som du konfigurerade i Automation-lösningen.

Runbook-åtgärder kräver egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:---|
| Runbook | Runbook som du vill starta när en avisering skapas. |
| Kör på | Ange **Azure** att köra runbook i molnet.  Ange **Hybrid worker** att köra runbook på en agent med [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md ) installerad.  |

Runbook-åtgärder starta en runbook med hjälp av en [webhook](../automation/automation-webhooks.md).  När du skapar regeln skapas automatiskt en ny webhook för runbook med namnet **OMS avisering reparation** följt av ett GUID.  

Du kan inte direkt fylla parametrar i runbooken men [$WebhookData parametern](../automation/automation-webhooks.md) finns information om aviseringen, inklusive resultaten av den logg som den skapades.  Runbook måste definiera **$WebhookData** som en parameter att komma åt egenskaper för aviseringen.  Aviseringen data är tillgängliga i json-format i en enda egenskap som kallas **SearchResult** (för runbook-åtgärder och webhook-åtgärder med standard nyttolast) eller **SearchResults** (webhook-åtgärder med anpassade nyttolasten inklusive **IncludeSearchResults ”: true**) i den **RequestBody** -egenskapen för **$WebhookData**.  Detta har med egenskaperna i följande tabell.

>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), och sedan runbook-nyttolasten har ändrats.  Mer information om formatet finns i [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse).  Du kan se ett exempel i [exempel](#sample-payload) nedan.  

| Node | Beskrivning |
|:--- |:--- |
| id |Sökvägen och GUID för sökningen. |
| __metadata |Information om aviseringen, inklusive antalet poster och status i sökresultaten. |
| värde |Separat post för varje post i sökresultatet.  Information om transaktionen matchar egenskaperna och värdena för posten. |

Följande runbooks skulle exempelvis extrahera de poster som returneras av loggen sökningen och tilldela olika egenskaper baserat på vilken typ av varje post.  Observera att startar runbook genom att konvertera **RequestBody** från json så att den kan bearbetas med som ett objekt i PowerShell.

>[!NOTE]
> Denna runbook använder **SearchResult** egenskap som innehåller resultat för runbook-åtgärder och webhook-åtgärder med standard nyttolast.  Om runbook har anropas från en webhook-svaret med hjälp av en anpassad nyttolast, behöver du ändra den här egenskapen till **SearchResults**.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody

    # Get all metadata properties    
    $AlertRuleName = $RequestBody.AlertRuleName
    $AlertThresholdOperator = $RequestBody.AlertThresholdOperator
    $AlertThresholdValue = $RequestBody.AlertThresholdValue
    $AlertDescription = $RequestBody.Description
    $LinktoSearchResults =$RequestBody.LinkToSearchResults
    $ResultCount =$RequestBody.ResultCount
    $Severity = $RequestBody.Severity
    $SearchQuery = $RequestBody.SearchQuery
    $WorkspaceID = $RequestBody.WorkspaceId
    $SearchWindowStartTime = $RequestBody.SearchIntervalStartTimeUtc
    $SearchWindowEndTime = $RequestBody.SearchIntervalEndtimeUtc
    $SearchWindowInterval = $RequestBody.SearchIntervalInSeconds

    # Get detailed search results
    if($RequestBody.SearchResult -ne $null)
    {
        $SearchResultRows    = $RequestBody.SearchResult.tables[0].rows 
        $SearchResultColumns = $RequestBody.SearchResult.tables[0].columns;

        foreach ($SearchResultRow in $SearchResultRows)
        {   
            $Column = 0
            $Record = New-Object –TypeName PSObject 
        
            foreach ($SearchResultColumn in $SearchResultColumns)
            {
                $Name = $SearchResultColumn.name
                $ColumnValue = $SearchResultRow[$Column]
                $Record | Add-Member –MemberType NoteProperty –Name $name –Value $ColumnValue -Force
                        
                $Column++
            }

            # Include code to work with the record. 
            # For example $Record.Computer to get the computer property from the record.
            
        }
    }



## <a name="sample-payload"></a>Exempel nyttolast
Det här avsnittet visas exempel nyttolasten för webhook och runbook-åtgärder.

### <a name="webhook-actions"></a>Webhook-åtgärder
Det här exemplet används **SearchResult** egenskap som innehåller resultat för webhook-åtgärder med standard nyttolast.  Om webhooken används en anpassad nyttolast som innehåller sökresultat, den här egenskapen är **SearchResults**.

Följande är ett exempel nyttolast för ett webhook-åtgärd.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbooks

Följande är ett exempel nyttolasten för en runbook-åtgärden.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>Nästa steg
- Slutföra en genomgång för [konfigurerar en webook](log-analytics-alerts-webhooks.md) med en aviseringsregel.  
- Lär dig hur du skriver [runbooks i Azure Automation](https://azure.microsoft.com/documentation/services/automation) att åtgärda problem som identifieras av aviseringar.
