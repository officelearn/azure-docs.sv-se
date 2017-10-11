---
title: "Med hjälp av OMS Log Analytics avisering REST API"
description: "Log Analytics avisering REST-API kan du skapa och hantera aviseringar i logganalys som är en del av Operations Management Suite (OMS).  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ce72ffef4394bf3bbe39fa420c4fcaa965ae35c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Skapa och hantera Varningsregler i logganalys med REST API
Log Analytics avisering REST-API kan du skapa och hantera aviseringar i Operations Management Suite (OMS).  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.

Log Analytics Sök REST API är RESTful och kan nås via Azure Resource Manager REST API. I det här dokumentet hittar du exempel där API: et kan nås från ett PowerShell-kommandorad med [ARMClient](https://github.com/projectkudu/ARMClient), en öppen källkod kommandoradsverktyg som förenklar anropar API: et för Azure Resource Manager. Användning av ARMClient och PowerShell är en av många alternativ för att komma åt Log Analytics Sök-API. Med dessa verktyg kan du använda RESTful Azure Resource Manager API för att göra anrop till OMS arbetsytor och utföra sökkommandon i dem. API: et utdata sökresultat för dig i JSON-format, så att du kan använda sökresultatet på många olika sätt programmässigt.

## <a name="prerequisites"></a>Krav
Aviseringar kan för närvarande kan endast skapas med en sparad sökning i logganalys.  Du kan referera till den [loggen Sök REST API](log-analytics-log-search-api.md) för mer information.

## <a name="schedules"></a>Scheman
En sparad sökning kan ha ett eller flera scheman. Schemat definierar hur ofta sökningen ska köras och det tidsintervall under vilken kriterierna som identifieras.
Scheman har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| intervall |Hur ofta sökningen körs. Mätt i minuter. |
| QueryTimeSpan |Det tidsintervall som villkoren utvärderas. Måste vara lika med eller större än intervall. Mätt i minuter. |
| Version |API-versionen som används.  Detta bör för närvarande alltid anges till 1. |

Anta till exempel att en fråga med ett intervall på 15 minuter och Timespan 30 minuter. I det här fallet frågan skulle köras var 15: e minut och en avisering ska utlösas om villkoren fortsatte att matcha till true över ett 30 minuters intervall.

### <a name="retrieving-schedules"></a>Hämtar scheman
Använd Get-metoden för att hämta alla scheman för en sparad sökning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Använd Get-metoden med ett schema-ID för att hämta ett visst schema för en sparad sökning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Följande är ett exempelsvar för ett schema.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Skapa ett schema
Använda Put-metoden med ett unikt schema-ID för att skapa ett nytt schema.  Observera att två scheman inte kan ha samma ID även om de är kopplade till olika sparade sökningar.  När du skapar ett schema i OMS-konsolen, skapas ett GUID för schema-ID.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med API: et för Log Analytics måste vara i gemener.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Ändringar i schemat
Använda Put-metoden med ett befintligt schema-ID för samma sparad sökning om du vill ändra schemat.  Brödtexten i begäran måste innehålla etag för schemat.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Ta bort scheman
Använd Delete-metoden med ett schema-ID för att ta bort ett schema.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Åtgärder
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer för att utföra som skickar ett e-post eller starta en runbook eller den kan definiera ett tröskelvärde som bestämmer när resultatet av en sökning som matchar vissa villkor.  Vissa åtgärder definiera både så att processerna som utförs när tröskelvärdet är uppfyllda.

Alla åtgärder ha egenskaperna i följande tabell.  Olika typer av aviseringar har olika ytterligare egenskaper som beskrivs nedan.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ |Typ av åtgärd.  Möjliga värden är för närvarande aviseringen och Webhooken. |
| Namn |Visningsnamn för aviseringen. |
| Version |API-versionen som används.  Detta bör för närvarande alltid anges till 1. |

### <a name="retrieving-actions"></a>Hämta åtgärder
Använd Get-metoden för att hämta alla åtgärder för ett schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Använd Get-metoden med åtgärds-ID för att hämta en viss åtgärd för ett schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Skapa eller redigera åtgärder
Använda Put-metoden med en åtgärds-ID som är unik för schema för att skapa en ny åtgärd.  När du skapar en åtgärd i OMS-konsolen är ett GUID för åtgärds-ID.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med API: et för Log Analytics måste vara i gemener.

Använda Put-metoden med en befintlig åtgärds-ID för samma sparad sökning om du vill ändra schemat.  Brödtexten i begäran måste innehålla etag för schemat.

Det begärandeformatet för att skapa en ny åtgärd varierar beroende på typ så att de här exemplen finns i avsnitten nedan.

### <a name="deleting-actions"></a>Ta bort åtgärder
Använda Delete-metoden med åtgärds-ID för att ta bort en åtgärd.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Aviseringsåtgärder
Ett schema ska ha en Aviseringsåtgärd.  Aviseringsåtgärder har en eller flera av avsnitten i följande tabell.  Alla beskrivs i mer detalj nedan.

| Avsnittet | Beskrivning |
|:--- |:--- |
| Tröskelvärde |Kriterier för när instruktionen körs. |
| EmailNotification |Skicka e-post till flera mottagare. |
| Reparation |Starta en runbook i Azure Automation för att försöka åtgärda identifierade problem. |

#### <a name="thresholds"></a>Tröskelvärden
En åtgärd måste ha ett och endast ett tröskelvärde.  När resultatet av en sparad sökning matchar tröskelvärdet i en åtgärd som är associerade med sökningen kör några andra processer i åtgärden.  En åtgärd kan också innehålla endast ett tröskelvärde så att den kan användas med åtgärder för andra typer som inte innehåller tröskelvärden.

Tröskelvärden har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Operatorn |Operator för tröskelvärde jämförelse. <br> gt = större än <br> lt = mindre än |
| Värde |Värdet för tröskelvärdet. |

Anta till exempel att en fråga med ett intervall på 15 minuter, Timespan 30 minuter och ett tröskelvärde som är större än 10. I det här fallet frågan skulle köras var 15: e minut och skulle att utlösa en avisering om det returnerade 10 händelser som har skapats under ett 30 minuters intervall.

Följande är ett exempelsvar för en åtgärd med bara ett tröskelvärde.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Använda Put-metoden med en unik åtgärds-ID för att skapa en ny tröskelvärdet åtgärd för ett schema.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en åtgärd för tröskelvärde för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>E-postavisering
E-postaviseringar skicka e-post till en eller flera mottagare.  De omfattar egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| mottagare |Lista över e-postadresser. |
| Ämne |Ämnet för e-postmeddelandet. |
| Bifogad fil |Bifogade filer stöds inte för närvarande, så att det alltid har värdet ”None”. |

Följande är ett exempelsvar för en e-postavisering åtgärd med ett tröskelvärde.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Använda Put-metoden med en unik åtgärds-ID för att skapa en ny e-åtgärd för ett schema.  I följande exempel skapas ett e-postmeddelande med en tröskel så skickas när resultatet av den sparade sökningen överskrider tröskelvärdet.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en e-åtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Reparationsåtgärder
Reparationer startar en runbook i Azure Automation som försöker åtgärda problemet som identifieras av aviseringen.  Du måste skapa en webhook för den runbook som används i en Reparationsåtgärd och anger sedan URI: N i egenskapen WebhookUri.  När du skapar den här åtgärden med hjälp av OMS-konsolen, skapas automatiskt en ny webhook för runbook.

Reparationer innehålla egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| RunbookName |Namnet på runbook. Detta måste matcha en publicerad runbook i automation-kontot som konfigurerats i Automation-lösningen i OMS-arbetsyta. |
| WebhookUri |URI för webhooken. |
| Förfallodatum |Förfallodatum och tid för webhooken.  Om webhooken inte har ett förfallodatum, kan det vara ett giltigt framtida datum. |

Följande är ett exempelsvar för en Reparationsåtgärd med ett tröskelvärde.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Använda Put-metoden med en unik åtgärds-ID för att skapa en ny Reparationsåtgärd för ett schema.  I följande exempel skapas en med ett tröskelvärde så runbook startas om resultatet av den sparade sökningen överstiger tröskelvärdet.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en Reparationsåtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exempel
Följande är en komplett exempel att skapa en ny e-postavisering.  Detta skapar ett nytt schema tillsammans med en åtgärd som innehåller ett tröskelvärde och e-post.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Webhook-åtgärder
Webhook-åtgärder kan du starta en process genom att anropa en URL och du kan också tillhandahålla en nyttolast som ska skickas.  De liknar reparationsåtgärder förutom de är avsedda för webhooks som kan anropa processer än Azure Automation-runbooks.  De ger också ytterligare alternativ för att tillhandahålla en nyttolast som ska levereras till fjärrprocessen.

Webhook-åtgärder har inte ett tröskelvärde men i stället bör läggas till ett schema som har en åtgärd med ett tröskelvärde.  Du kan lägga till flera Webhook-åtgärder som alla körs när tröskelvärdet är uppfyllda.

Webhook-åtgärder innehålla egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| WebhookUri |Ämnet för e-postmeddelandet. |
| CustomPayload |Anpassad nyttolast skickas till webhooken.  Formatet beror på vad webhooken förväntas. |

Följande är ett exempelsvar för webhook åtgärden och en associerad åtgärd med ett tröskelvärde.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Skapa eller redigera en webhook-åtgärd
Använda Put-metoden med en unik åtgärds-ID för att skapa en ny webhook-åtgärd för ett schema.  I följande exempel skapas en Webhook-åtgärd och en åtgärd med ett tröskelvärde så att webhooken ska utlösas när resultatet av den sparade sökningen överstiger tröskelvärdet.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en webhook-åtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Nästa steg
* Använd den [REST API för att utföra sökningar loggen](log-analytics-log-search-api.md) i logganalys.

