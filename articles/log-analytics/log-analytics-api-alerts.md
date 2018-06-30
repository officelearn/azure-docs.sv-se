---
title: Med hjälp av OMS Log Analytics avisering REST API
description: 'Log Analytics avisering REST-API kan du skapa och hantera aviseringar i logganalys som är en del av Operations Management Suite (OMS).  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.'
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 9097ca13bf4f65db4b0924044a9c0f075e3703af
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128902"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Skapa och hantera Varningsregler i logganalys med REST API
Log Analytics avisering REST-API kan du skapa och hantera aviseringar i Operations Management Suite (OMS).  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.

Log Analytics Sök REST API är RESTful och kan nås via Azure Resource Manager REST API. I det här dokumentet hittar du exempel där API: et kan nås från ett PowerShell-kommandorad med [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager. Användning av ARMClient och PowerShell är en av många alternativ för att komma åt Log Analytics Sök-API. Med dessa verktyg kan du använda RESTful Azure Resource Manager API för att göra anrop till OMS arbetsytor och utföra sökkommandon i dem. API: et utdata sökresultat för dig i JSON-format, så att du kan använda sökresultatet på många olika sätt programmässigt.

## <a name="prerequisites"></a>Förutsättningar
Aviseringar kan för närvarande kan endast skapas med en sparad sökning i logganalys.  Du kan referera till den [loggen Sök REST API](log-analytics-log-search-api.md) för mer information.

## <a name="schedules"></a>Scheman
En sparad sökning kan ha ett eller flera scheman. Schemat definierar hur ofta sökningen ska köras och det tidsintervall under vilken kriterierna som identifieras.
Scheman har egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Intervall |Hur ofta sökningen körs. Mätt i minuter. |
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

| Egenskap  | Beskrivning |
|:--- |:--- |
| Typ |Typ av åtgärd.  Möjliga värden är för närvarande aviseringen och Webhooken. |
| Namn |Visningsnamn för aviseringen. |
| Version |API-versionen som används.  Detta bör för närvarande alltid anges till 1. |

### <a name="retrieving-actions"></a>Hämta åtgärder

> [!NOTE]
> Från den 14 maj 2018 utökas alla aviseringar i en arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). För användare som utökar aviseringar till Azure, kontrolleras nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar utökas till Azure, kan du hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

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

> [!NOTE]
> Från den 14 maj 2018 utökas alla aviseringar i en arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). För användare som utökar aviseringar till Azure, kontrolleras nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar utökas till Azure, kan du hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Använda Delete-metoden med åtgärds-ID för att ta bort en åtgärd.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Aviseringsåtgärder
Ett schema ska ha en Aviseringsåtgärd.  Aviseringsåtgärder har en eller flera av avsnitten i följande tabell.  Alla beskrivs i mer detalj nedan.

| Section | Beskrivning | Användning |
|:--- |:--- |:--- |
| Tröskelvärde |Kriterier för när instruktionen körs.| Krävs för varje avisering före eller efter att de har utökats till Azure. |
| Severity |Etiketten används för att klassificera avisering när den utlöses.| Krävs för varje avisering före eller efter att de har utökats till Azure. |
| Åtgärdsgrupper |ID: N för Azure ActionGroup där åtgärder som krävs har angetts som - e-postmeddelanden, SMSs, röstsamtal, Webhooks, Automation-Runbooks, ITSM kopplingar och så vidare.| Krävs när aviseringar har utökats till Azure|
| Anpassa åtgärder|Ändra standardutdata för väljer åtgärder från ActionGroup| Valfritt för varje avisering kan användas när aviseringar har utökats till Azure. |
| EmailNotification |Skicka e-post till flera mottagare. | Krävs inte, om aviseringar har utökats till Azure|
| Åtgärd |Starta en runbook i Azure Automation för att försöka åtgärda identifierade problem. |Krävs inte, om aviseringar har utökats till Azure|
| Webhook-åtgärder | Skicka data från aviseringar, till önskad tjänst som JSON |Krävs inte, om aviseringar har utökats till Azure|

> [!NOTE]
> Från den 14 maj 2018 utökas alla aviseringar i en arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md).

#### <a name="thresholds"></a>Tröskel
En åtgärd måste ha ett och endast ett tröskelvärde.  När resultatet av en sparad sökning matchar tröskelvärdet i en åtgärd som är associerade med sökningen kör några andra processer i åtgärden.  En åtgärd kan också innehålla endast ett tröskelvärde så att den kan användas med åtgärder för andra typer som inte innehåller tröskelvärden.

Tröskelvärden har egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Operator |Operator för tröskelvärde jämförelse. <br> gt = större än <br> lt = mindre än |
| Värde |Värdet för tröskelvärdet. |

Anta till exempel att en fråga med ett intervall på 15 minuter, Timespan 30 minuter och ett tröskelvärde som är större än 10. I det här fallet frågan skulle köras var 15: e minut och en avisering skulle aktiveras om det returnerade 10 händelser som har skapats under en period på 30 minuter.

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

#### <a name="severity"></a>Severity
Log Analytics kan du klassificera aviseringar i kategorier så att enklare hantering och prioritering. Allvarlighetsgrad som definierats är: information, varningar och kritiska. Dessa mappas till normaliserade allvarlighetsgrad skalan för Azure-aviseringar som:

|Allvarlighetsgrad för log Analytics  |Allvarlighetsgrad för Azure-aviseringar  |
|---------|---------|
|Kritiska |Sev 0|
|Varning |Sev 1|
|Information | Sev 2|

Följande är ett exempelsvar för en åtgärd med ett tröskelvärde och allvarlighetsgrad. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Använda Put-metoden med en unik åtgärds-ID för att skapa en ny åtgärd för ett schema med allvarlighetsgrad.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en allvarlighetsgrad åtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="action-groups"></a>Åtgärdsgrupper
Alla aviseringar i Azure, använda åtgärden gruppen standardmekanism för hantering av åtgärder. Med åtgärden grupp du ange dina åtgärder en gång och sedan associerar åtgärdsgruppen till flera aviseringar - i Azure. Utan att behöva, upprepade gånger deklarera samma åtgärder upprepade gånger. Åtgärdsgrupper har stöd för flera åtgärder – inklusive e-post, SMS, röst anropa, ITSM anslutning, Automation-Runbook, Webhook URI och mycket mer. 

För användare som har utökat sina aviseringar i Azure - bör ett schema nu ha grupp information skickas tillsammans med tröskelvärdet för att kunna skapa en avisering. Postinformation om e-, Webhook URL: er, Runbook Automation-information och andra åtgärder måste definieras på sidan av en grupp innan du kan skapa en avisering; går att skapa [grupp från Azure-Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) på portalen eller Använd [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Lägg till associationen av grupp i en avisering, ange unika Azure Resource Manager-ID för åtgärdsgruppen i varningsdefinitionen. En bild med exempel finns nedan:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Använda Put-metoden med en unik åtgärds-ID för att associera redan befintlig grupp för ett schema.  Följande är ett exempel bild för användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en grupp som är kopplad till ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Anpassa åtgärder
Följ standardmall och format för meddelanden av standardåtgärder. Men användaren kan anpassa vissa åtgärder, även om de styrs av åtgärdsgrupper. För närvarande är det möjligt för e-postmeddelandets ämne och Webhook nyttolast med anpassning.

##### <a name="customize-e-mail-subject-for-action-group"></a>Anpassa e-postämne för grupp
Ämnet för e-post för aviseringar är som standard: aviseringsmeddelanden <AlertName> för <WorkspaceName>. Men det kan anpassas, så att du kan specifika ord eller taggar - så att du kan enkelt utnyttja filterregler i din inkorg. Anpassa e-huvudet information behöver skicka tillsammans med ActionGroup information, som i exemplet nedan.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Använda Put-metoden med en unik åtgärds-ID för att associera redan befintlig grupp till anpassning för ett schema.  Följande är ett exempel bild för användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en grupp som är kopplad till ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Anpassa Webhook nyttolasten för grupp
Webhooken skickas via grupp för logganalys har en fast struktur som standard. Men en kan anpassa JSON-nyttolast med hjälp av specifika variabler som stöds för att uppfylla kraven i webhook-slutpunkten. Mer information finns i [Webhook åtgärd för log Varningsregler](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md). 

Anpassa webhook information behöver skicka tillsammans med ActionGroup information och kommer att tillämpas på alla Webhook URI som angavs i åtgärdsgruppen. som i exemplet nedan.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Använda Put-metoden med en unik åtgärds-ID för att associera redan befintlig grupp till anpassning för ett schema.  Följande är ett exempel bild för användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en grupp som är kopplad till ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>E-postmeddelande
E-postaviseringar skicka e-post till en eller flera mottagare.  De omfattar egenskaperna i följande tabell.

> [!NOTE]
> Från den 14 maj 2018 utökas alla aviseringar i en arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Åtgärder som e-postavisering styrs nu i Azure åtgärdsgrupper för användare som utökar aviseringar till Azure. När en arbetsyta och dess aviseringar utökas till Azure, kan du hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Egenskap  | Beskrivning |
|:--- |:--- |
| Mottagare |Lista över e-postadresser. |
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

#### <a name="remediation-actions"></a>Åtgärder
Reparationer startar en runbook i Azure Automation som försöker åtgärda problemet som identifieras av aviseringen.  Du måste skapa en webhook för den runbook som används i en Reparationsåtgärd och anger sedan URI: N i egenskapen WebhookUri.  När du skapar den här åtgärden med hjälp av OMS-konsolen, skapas automatiskt en ny webhook för runbook.

> [!NOTE]
> Från den 14 maj 2018 utökas alla aviseringar i en arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Åtgärder som reparation med hjälp av runbook styrs nu i Azure åtgärdsgrupper för användare som utökar aviseringar till Azure. När en arbetsyta och dess aviseringar utökas till Azure, kan du hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Reparationer innehålla egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
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

#### <a name="webhook-actions"></a>Webhook-åtgärder
Webhook-åtgärder kan du starta en process genom att anropa en URL och du kan också tillhandahålla en nyttolast som ska skickas.  De liknar reparationsåtgärder förutom de är avsedda för webhooks som kan anropa processer än Azure Automation-runbooks.  De ger också ytterligare alternativ för att tillhandahålla en nyttolast som ska levereras till fjärrprocessen.

> [!NOTE]
> Från den 14 maj 2018 utökas alla aviseringar i en arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utöka aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar till Azure från OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Åtgärder som Webhook styrs nu i Azure åtgärdsgrupper för användare som utökar aviseringar till Azure. När en arbetsyta och dess aviseringar utökas till Azure, kan du hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Webhook-åtgärder har inte ett tröskelvärde men i stället bör läggas till ett schema som har en åtgärd med ett tröskelvärde.  

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

##### <a name="create-or-edit-a-webhook-action"></a>Skapa eller redigera en webhook-åtgärd
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
* Lär dig mer om [Logga varningar i azure-aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)

