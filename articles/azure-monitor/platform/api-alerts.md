---
title: Med hjälp av Log Analytics avisering REST API
description: 'Med Log Analytics aviserings REST API kan du skapa och hantera aviseringar i Log Analytics, som är en del av Log Analytics.  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.'
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2018
ms.author: bwren
ms.openlocfilehash: e8209a2d2034818a00ab9390a9af96d5b0287b5b
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663215"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Skapa och hantera Varningsregler i Log Analytics med REST API
Log Analytics avisering REST-API kan du skapa och hantera aviseringar i Log Analytics.  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.

> [!IMPORTANT]
> Som du [presenterade tidigare](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), kan Log Analytics-arbetsytor som skapats efter den *1 juni 2019* -hantera aviserings regler med **bara** Azure scheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), [Azure Resource Manager-mall](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) och [ PowerShell-cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Kunder kan enkelt [ändra sina önskade metoder för varnings regel hantering](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) för äldre arbets ytor för att utnyttja Azure Monitor scheduledQueryRules som standard och få många [nya fördelar](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) som möjligheten att använda inbyggda PowerShell-cmdlets, ökad lookback tids period i regler, skapande av regler i separat resurs grupp eller prenumeration och mycket mer.

Log Analytics Search REST API är RESTful och kan nås via Azure Resource Manager REST API. I det här dokumentet hittar du exempel där API: T hämtas från en PowerShell från kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager. Användning av ARMClient och PowerShell är ett av många alternativ för att få åtkomst till Log Analytics Search-API. Du kan använda RESTful Azure Resource Manager-API för att göra anrop till Log Analytics-arbetsytor och utföra sökkommandon i dem med de här verktygen. API: et kommer mata ut sökresultat till dig i JSON-format, så att du kan använda sökresultaten på många olika sätt programmässigt.

## <a name="prerequisites"></a>Förutsättningar
Aviseringar kan för närvarande kan bara skapas med en sparad sökning i Log Analytics.  Du kan referera till den [Log Search REST API](../../azure-monitor/log-query/log-query-overview.md) för mer information.

## <a name="schedules"></a>Scheman
En sparad sökning kan ha ett eller flera scheman. Schemat definierar hur ofta sökningen är kör och det tidsintervall som villkoren har identifierats.
Scheman har egenskaper i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Interval |Hur ofta sökningen körs. Mätt i minuter. |
| QueryTimeSpan |Det tidsintervall som villkoren utvärderas. Måste vara lika med eller större än intervall. Mätt i minuter. |
| Version |API-version som används.  För närvarande ska detta alltid vara inställd på 1. |

Anta exempelvis att en fråga med ett intervall på 15 minuter och Timespan 30 minuter. I det här fallet frågan skulle köras var 15: e minut och en avisering skulle aktiveras om villkoren fortsatt att lösa till SANT över ett 30-minuters intervall.

### <a name="retrieving-schedules"></a>Hämtning av scheman
Använda Get-metoden för att hämta alla scheman för en sparad sökning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Använda Get-metoden med ett schema-ID för att hämta ett visst schema för en sparad sökning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Följande är ett exempelsvar för ett schema.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Skapa ett schema
Använda Put-metoden med ett unikt schema-ID för att skapa ett nytt schema.  Två scheman kan inte ha samma ID även om de är kopplade till olika sparade sökningar.  När du skapar ett schema i Log Analytics-konsolen, skapas en GUID för schema-ID.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics-API måste vara i gemener.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Ändringar i schemat
Använd metoden för att skicka med ett befintligt schema-ID för samma sparade sökning för att ändra schemat. i exemplet nedan är schemat inaktiverat. Bröd texten i begäran måste innehålla *etag* för schemat.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Ta bort scheman
Använd Delete-metoden med ett schema-ID för att ta bort ett schema.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Åtgärder
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer för att utföra, till exempel skickar ett e-postmeddelande eller starta en runbook eller den kan definiera ett tröskelvärde som bestämmer när resultatet av en sökning som matchar vissa kriterier.  Vissa åtgärder kommer definiera både så att processerna som utförs när tröskelvärdet är uppfyllt.

Alla åtgärder har egenskaper i följande tabell.  Olika typer av aviseringar har olika ytterligare egenskaper som beskrivs nedan.

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |Typ av åtgärd.  Möjliga värden är för närvarande aviseringen och Webhook. |
| `Name` |Visningsnamn för aviseringen. |
| `Version` |API-version som används.  För närvarande ska detta alltid vara inställd på 1. |

### <a name="retrieving-actions"></a>Hämta åtgärder

Använda Get-metoden för att hämta alla åtgärder för ett schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Använd Get-metoden med åtgärds-ID för att hämta en viss åtgärd för ett schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Skapa eller redigera åtgärder
Använda Put-metoden med en åtgärds-ID som är unik för schema för att skapa en ny åtgärd.  När du skapar en åtgärd i Log Analytics-konsolen, måste ett GUID för åtgärden-ID.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics-API måste vara i gemener.

Använda Put-metoden med en befintlig åtgärds-ID för samma sparad sökning om du vill ändra schemat.  Brödtexten i begäran måste innehålla etag för schemat.

Format för förfrågan för att skapa en ny åtgärd varierar åtgärdstyp så att de här exemplen finns i avsnitten nedan.

### <a name="deleting-actions"></a>Ta bort åtgärder

Använda Delete-metoden med åtgärds-ID för att ta bort en åtgärd.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Aviseringsåtgärder
Ett schema måste ha en Aviseringsåtgärd.  Aviseringsåtgärder har en eller flera av avsnitten i följande tabell.  Beskrivs i mer detalj nedan.

| Section | Beskrivning | Användning |
|:--- |:--- |:--- |
| Tröskelvärde |Kriterier för när åtgärden har körts.| Krävs för varje avisering före eller efter att de har utökats till Azure. |
| Severity |Etikett som används för att klassificera avisering när den utlöses.| Krävs för varje avisering före eller efter att de har utökats till Azure. |
| Utelämna |Alternativet för att stoppa meddelanden från aviseringen. | Valfritt för varje avisering innan eller efter att de har utökats till Azure. |
| Åtgärdsgrupper |ID: N för Azure ActionGroup där åtgärder som krävs har angetts, - e-postmeddelanden, SMSs, röstsamtal, Webhooks, Automation-Runbooks, ITSM-anslutningsprogram, t.ex.| Krävs när aviseringar har utökats till Azure|
| Anpassa åtgärder|Ändra standardutdata för väljer åtgärder från ActionGroup| Du kan använda valfritt för varje avisering när aviseringar har utökats till Azure. |

### <a name="thresholds"></a>Tröskel
En Aviseringsåtgärd bör ha ett och endast ett tröskelvärde.  När resultatet av en sparad sökning matchar tröskelvärdet i en åtgärd som är associerade med sökningen, körs alla andra processer i åtgärden.  En åtgärd kan också innehålla endast ett tröskelvärde så att den kan användas med åtgärder från andra typer som inte innehåller tröskelvärden.

Tröskelvärden har egenskaper i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| `Operator` |Operator för tröskelvärde för jämförelse. <br> gt = större än <br> lt = mindre än |
| `Value` |Värde för tröskelvärdet. |

Anta exempelvis att en fråga med ett intervall på 15 minuter, Timespan 30 minuter och ett tröskelvärde för större än 10. I det här fallet frågan skulle köras var 15: e minut och en avisering ska utlösas om 10 händelser som har skapats under en 30-minuters returnerades.

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

Använda Put-metoden med en unik åtgärds-ID för att skapa en ny åtgärd för tröskelvärde för ett schema.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en åtgärd för tröskelvärde för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Log Analytics kan du klassificera aviseringar i kategorier så att enklare hantering och prioritering. Aviseringens allvarlighetsgrad definierats är: information, varning och kritiskt. Dessa är mappade till normaliserade allvarlighetsgrad skalan för Azure-aviseringar som:

|Allvarlighetsgrad för log Analytics  |Allvarlighetsgrad för Azure-aviseringar  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

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

Använda Put-metoden med en unika åtgärds-ID för att skapa en ny åtgärd för ett schema med allvarlighetsgrad.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en allvarlighetsgrad-åtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Utelämna
Log Analytics bygger fråga aviseringar utlöses varje gång tröskelvärde har uppnåtts eller överskridits. Baserat på logiken underförstådd i frågan kan detta resultera i aviseringen komma utlöses för en serie intervall och kan därför meddelanden också skickas kontinuerligt. För att förhindra sådana scenariot kan en användare ange utelämna alternativet anvisningar om hur Log Analytics för att vänta tills en stipulerade lång tid innan meddelande utlöses den andra gången för regeln. Om utelämna anges under 30 minuter. sedan kommer aviseringen utlöses första gången och skicka meddelanden som har konfigurerats. Men sedan vänta i 30 minuter innan meddelandet för regeln används igen. Varningsregeln fortsätter att köras i övergångsperioden, – endast meddelande undertrycks av Log Analytics för angiven tidpunkt, oavsett hur många gånger varningsregeln har utlösts under den här perioden.

Utelämna egenskapen för Log Analytics varningsregel anges med hjälp av den *begränsning* värde och den Undertryckning period med *DurationInMinutes* värde.

Följande är ett exempel svar för en åtgärd med endast egenskapen Threshold, allvarlighets grad och utelämna

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

Använda Put-metoden med en unika åtgärds-ID för att skapa en ny åtgärd för ett schema med allvarlighetsgrad.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en allvarlighetsgrad-åtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Åtgärdsgrupper
Alla aviseringar i Azure, använda åtgärdsgrupp som standardmekanism för att hantera åtgärder. Med åtgärdsgrupp kan du ange dina åtgärder en gång och sedan associerar åtgärdsgrupp att flera aviseringar – i Azure. Utan att behöva flera gånger deklarera samma åtgärder om och om igen. Åtgärdsgrupper stöd för flera åtgärder – inklusive e-post, SMS, röstsamtal, ITSM-anslutningen, Automation-Runbook, Webhook URI med mera. 

För användare som har utökat sina aviseringar till Azure bör ett schema nu ha åtgärds grupp information som skickas tillsammans med tröskelvärdet för att kunna skapa en avisering. Information om e-post, Webhook-URL: er, Runbook Automation-information och andra åtgärder måste vara definierade i sida en åtgärdsgrupp innan du kan skapa en avisering; går att skapa [åtgärdsgrupp från Azure Monitor](../../azure-monitor/platform/action-groups.md) i portalen eller Använd [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Ange det unika Azure Resource Manager-ID för åtgärdsgruppen i varningsdefinitionen för att lägga till associationen mellan åtgärdsgrupp till en avisering. En exempel-bilden finns nedan:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
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

Använda Put-metoden med en unika åtgärds-ID för att associera redan befintlig åtgärdsgrupp för ett schema.  Följande är ett Exempelillustration av användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en åtgärdsgrupp som är associerade för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Anpassa åtgärder
Följ standardmall och format för meddelanden av standardåtgärder. Men användaren kan anpassa vissa åtgärder, även om de styrs av åtgärdsgrupper. För närvarande är det möjligt för e-postmeddelandets ämne och Webhook-nyttolasten med anpassning.

##### <a name="customize-e-mail-subject-for-action-group"></a>Anpassa e-postämne för åtgärdsgrupp
Som standard är e-postmeddelandets ämne för aviseringar: Aviserings `<AlertName>` meddelande `<WorkspaceName>`för. Men detta kan anpassas, så att du kan vissa specifika ord eller taggar – så att du kan enkelt använda filterregler i din inkorg. Anpassa e-huvudet information behöver skicka tillsammans med ActionGroup information, som i exemplet nedan.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Använda Put-metoden med en unik åtgärds-ID för att associera redan befintlig åtgärdsgrupp med anpassning för ett schema.  Följande är ett Exempelillustration av användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en åtgärdsgrupp som är associerade för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Anpassa Webhook-nyttolasten för åtgärdsgrupp
Webhooken som skickas via åtgärdsgrupp för log analytics har en fast struktur som standard. Men en kan anpassa JSON-nyttolasten med hjälp av specifika variabler som stöds för att uppfylla kraven för webhook-slutpunkt. Mer information finns i [Webhook-åtgärd för loggaviseringsregler](../../azure-monitor/platform/alerts-log-webhook.md). 

Anpassa webhook information måste du skicka tillsammans med ActionGroup information och ska tillämpas på alla Webhook URI som anges i åtgärdsgruppen; som i exemplet nedan.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Använda Put-metoden med en unik åtgärds-ID för att associera redan befintlig åtgärdsgrupp med anpassning för ett schema.  Följande är ett Exempelillustration av användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en åtgärdsgrupp som är associerade för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Nästa steg

* Använd den [REST API för att utföra sökningar i loggen](../../azure-monitor/log-query/log-query-overview.md) i Log Analytics.
* Lär dig mer om [logg aviseringar i Azure Monitor](../../azure-monitor/platform/alerts-unified-log.md)
* [Skapa, redigera eller hantera logg aviserings regler i Azure Monitor](../../azure-monitor/platform/alerts-log.md)

