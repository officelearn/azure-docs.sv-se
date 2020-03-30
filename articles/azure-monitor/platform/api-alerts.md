---
title: Använda REST-API för logganalysvarning
description: Med LOG Analytics Alert REST API kan du skapa och hantera aviseringar i Log Analytics, som är en del av Log Analytics.  Den här artikeln innehåller information om API:et och flera exempel för att utföra olika åtgärder.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665008"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Skapa och hantera varningsregler i Log Analytics med REST API 

Med LOG Analytics Alert REST API kan du skapa och hantera aviseringar i Log Analytics.  Den här artikeln innehåller information om API:et och flera exempel för att utföra olika åtgärder.

> [!IMPORTANT]
> Som [tidigare meddelats](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)kan log analytics-arbetsyta som skapats efter *den 1 juni 2019* hantera varningsregler med **endast** Azure scheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), Azure Resource [Mananger Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) och [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Kunder kan enkelt byta sina önskade sätt att [hantera varningsregel för](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) äldre arbetsytor för att utnyttja Azure Monitor-schemalagdasekvenser som standard och få många [nya fördelar](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) som möjligheten att använda inbyggda PowerShell-cmdlets, ökad tillbakablickstid i regler, skapande av regler i separat resursgrupp eller prenumeration och mycket mer.

Log Analytics Search REST API är RESTful och kan nås via Azure Resource Manager REST API. I det här dokumentet hittar du exempel där API:et nås från en PowerShell-kommandorad med [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg med öppen källkod som förenklar anstiftan till Azure Resource Manager API. Användningen av ARMClient och PowerShell är ett av många alternativ för att komma åt Logg Analytics Search API. Med dessa verktyg kan du använda API:et för RESTful Azure Resource Manager för att ringa samtal till Log Analytics-arbetsytor och utföra sökkommandon i dem. API:et ger sökresultat till dig i JSON-format, så att du kan använda sökresultaten på många olika sätt programmässigt.

## <a name="prerequisites"></a>Krav
För närvarande kan aviseringar endast skapas med en sparad sökning i Log Analytics.  Du kan läsa [REST API för loggsökning](../../azure-monitor/log-query/log-query-overview.md) för mer information.

## <a name="schedules"></a>Scheman
En sparad sökning kan ha ett eller flera scheman. Schemat definierar hur ofta sökningen körs och det tidsintervall som kriterierna identifieras över.
Scheman har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Intervall |Hur ofta sökningen körs. Mätt i minuter. |
| QueryTimeSpan |Det tidsintervall som kriterierna utvärderas över. Måste vara lika med eller större än Intervall. Mätt i minuter. |
| Version |API-versionen som används.  För närvarande bör detta alltid ställas in på 1. |

Tänk dig till exempel en händelsefråga med ett intervall på 15 minuter och en tidsspann på 30 minuter. I det här fallet skulle frågan köras var 15:e minut och en avisering skulle utlösas om kriterierna fortsatte att matchas till true över ett 30-minutersintervall.

### <a name="retrieving-schedules"></a>Hämta scheman
Använd metoden Hämta för att hämta alla scheman för en sparad sökning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Använd metoden Hämta med ett schema-ID för att hämta ett visst schema för en sparad sökning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Följande är ett exempel svar för ett schema.

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
Använd metoden Placera med ett unikt schema-ID för att skapa ett nytt schema.  Två scheman kan inte ha samma ID även om de är associerade med olika sparade sökningar.  När du skapar ett schema i Log Analytics-konsolen skapas ett GUID för schema-ID: t.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics-API:et måste vara i gemener.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Redigera ett schema
Använd metoden Placera med ett befintligt schema-ID för samma sparade sökning för att ändra schemat. i exempel under schemat är inaktiverat. Begärans brödtext måste innehålla *etag* för schemat.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Ta bort scheman
Använd metoden Ta bort med ett schema-ID för att ta bort ett schema.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Åtgärder
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer som ska utföras, till exempel skicka ett e-postmeddelande eller starta en runbook, eller så kan den definiera ett tröskelvärde som avgör när resultatet av en sökning matchar vissa villkor.  Vissa åtgärder definierar båda så att processerna utförs när tröskelvärdet uppfylls.

Alla åtgärder har egenskaperna i följande tabell.  Olika typer av aviseringar har olika ytterligare egenskaper, som beskrivs nedan.

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |Typ av åtgärd.  För närvarande är de möjliga värdena Alert och Webhook. |
| `Name` |Visningsnamn för aviseringen. |
| `Version` |API-versionen som används.  För närvarande bör detta alltid ställas in på 1. |

### <a name="retrieving-actions"></a>Hämta åtgärder

Använd metoden Hämta för att hämta alla åtgärder för ett schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Använd metoden Hämta med åtgärds-ID för att hämta en viss åtgärd för ett schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Skapa eller redigera åtgärder
Använd metoden Placera med ett åtgärds-ID som är unikt för schemat för att skapa en ny åtgärd.  När du skapar en åtgärd i Log Analytics-konsolen är ett GUID för åtgärds-ID.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics-API:et måste vara i gemener.

Använd metoden Placera med ett befintligt åtgärds-ID för samma sparade sökning för att ändra schemat.  Begärans brödtext måste innehålla etag för schemat.

Formatet för begäran om att skapa en ny åtgärd varierar beroende på åtgärdstyp så att dessa exempel finns i avsnitten nedan.

### <a name="deleting-actions"></a>Ta bort åtgärder

Använd metoden Ta bort med åtgärds-ID:et för att ta bort en åtgärd.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Varningsåtgärder
Ett schema bör ha en och endast en aviseringsåtgärd.  Varningsåtgärder har ett eller flera avsnitt i följande tabell.  Var och en beskrivs närmare nedan.

| Section | Beskrivning | Användning |
|:--- |:--- |:--- |
| Tröskelvärde |Villkor för när åtgärden körs.| Krävs för varje avisering, före eller efter att de utökas till Azure. |
| Severity |Etikett som används för att klassificera avisering när den utlöses.| Krävs för varje avisering, före eller efter att de utökas till Azure. |
| Undertrycka |Möjlighet att stoppa aviseringar från avisering. | Valfritt för varje avisering, före eller efter att de har utökats till Azure. |
| Åtgärdsgrupper |ID:er för Azure ActionGroup där åtgärder som krävs anges, till exempel - E-post, SMÅ och medelstora företag, röstsamtal, Webhooks, Automation Runbooks, ITSM-kopplingar osv.| Krävs när aviseringar har utökats till Azure|
| Anpassa åtgärder|Ändra standardutdata för utvalda åtgärder från ActionGroup| Valfritt för varje avisering, kan användas när aviseringar har utökats till Azure. |

### <a name="thresholds"></a>Tröskelvärden
En varningsåtgärd bör ha ett och bara ett tröskelvärde.  När resultatet av en sparad sökning matchar tröskelvärdet i en åtgärd som är associerad med sökningen körs alla andra processer i den åtgärden.  En åtgärd kan också innehålla endast ett tröskelvärde så att den kan användas med åtgärder av andra typer som inte innehåller tröskelvärden.

Tröskelvärden har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| `Operator` |Operator för tröskeljämförelse. <br> gt = Större än <br> lt = mindre än |
| `Value` |Värde för tröskelvärdet. |

Tänk dig till exempel en händelsefråga med ett intervall på 15 minuter, en tidsspann på 30 minuter och ett tröskelvärde på mer än 10. I det här fallet skulle frågan köras var 15:e minut och en avisering skulle utlösas om den returnerade 10 händelser som skapades under ett 30-minutersintervall.

Följande är ett exempelsvar för en åtgärd med endast ett tröskelvärde.  

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

Använd metoden Placera med ett unikt åtgärds-ID för att skapa en ny tröskelåtgärd för ett schema.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Använd metoden Placera med ett befintligt åtgärds-ID för att ändra en tröskelåtgärd för ett schema.  Begärans organ skall innehålla åtgärdens etag.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Med Log Analytics kan du klassificera dina aviseringar i kategorier, för att möjliggöra enklare hantering och triage. Den definierade allvarlighetsgraden för avisering är: informations-, varnings- och kritisk. Dessa mappas till den normaliserade allvarlighetsgraden för Azure-aviseringar som:

|Allvarlighetsgrad för logganalys  |Allvarlighetsgrad för Azure-aviseringar  |
|---------|---------|
|`critical` |Sev 0 (På)|
|`warning` |Sev 1 (På andra)|
|`informational` | Allv.grad 2|

Följande är ett exempel svar för en åtgärd med endast ett tröskelvärde och allvarlighetsgrad. 

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

Använd metoden Placera med ett unikt åtgärds-ID för att skapa en ny åtgärd för ett schema med allvarlighetsgrad.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Använd metoden Placera med ett befintligt åtgärds-ID för att ändra en allvarlighetsgrad för ett schema.  Begärans organ skall innehålla åtgärdens etag.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Undertrycka
Log Analytics-baserade frågeaviseringar avfyras varje gång tröskelvärdet uppfylls eller överskrids. Baserat på logiken som antyds i frågan kan detta resultera i att aviseringen aktiveras för en serie intervall och därmed skickas även meddelanden hela tiden. För att förhindra ett sådant scenario kan en användare ange alternativet Undertryck som instruerar Log Analytics att vänta på en viss tid innan meddelandet utlöses andra gången för varningsregeln. Så om undertrycka är inställd på 30 minuter; sedan alert kommer brand första gången och skicka meddelanden konfigureras. Men vänta sedan i 30 minuter, innan anmälan för varningsregeln används igen. Under övergångsperioden fortsätter varningsregeln att köras - endast meddelande undertrycks av Log Analytics under angiven tid, oavsett hur många gånger varningsregeln som har avfyrats under den här perioden.

Dämpningsegenskapen för logganalysvarningsregeln anges med värdet *Begränsning* och undertrycksperiod med *värdet DurationInMinutes.*

Följande är ett exempelsvar för en åtgärd med endast ett tröskelvärde, allvarlighetsgrad och undertrycksegenskap

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

Använd metoden Placera med ett unikt åtgärds-ID för att skapa en ny åtgärd för ett schema med allvarlighetsgrad.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Använd metoden Placera med ett befintligt åtgärds-ID för att ändra en allvarlighetsgrad för ett schema.  Begärans organ skall innehålla åtgärdens etag.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Åtgärdsgrupper
Alla aviseringar i Azure använder Åtgärdsgrupp som standardmekanism för att hantera åtgärder. Med Åtgärdsgrupp kan du ange dina åtgärder en gång och sedan associera åtgärdsgruppen till flera aviseringar – i Azure. Utan behov, att upprepade gånger förklara samma åtgärder om och om igen. Åtgärdsgrupper stöder flera åtgärder - inklusive e-post, SMS, röstsamtal, ITSM-anslutning, Automation Runbook, Webhook URI med mera. 

För användare som har utökat sina aviseringar till Azure - ett schema bör nu ha information om åtgärdsgrupper som skickas tillsammans med tröskelvärdet för att kunna skapa en avisering. E-postinformation, Webhook-url:er, Runbook Automation-information och andra åtgärder måste definieras i sidan en åtgärdsgrupp först innan du skapar en avisering. Man kan skapa [åtgärdsgrupp från Azure Monitor](../../azure-monitor/platform/action-groups.md) i Portal eller använda [Åtgärdsgrupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Om du vill lägga till associering av åtgärdsgrupp i en avisering anger du det unika Azure Resource Manager-ID:et för åtgärdsgruppen i aviseringsdefinitionen. Nedan finns en exempelillustration:

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

Använd metoden Placera med ett unikt åtgärds-ID för att associera redan befintlig åtgärdsgrupp för ett schema.  Följande är ett exempel illustration av användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använd metoden Placera med ett befintligt åtgärds-ID om du vill ändra en åtgärdsgrupp som är associerad för ett schema.  Begärans organ skall innehålla åtgärdens etag.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Anpassa åtgärder
Som standardåtgärder följer du standardmall och format för meddelanden. Men användaren kan anpassa vissa åtgärder, även om de styrs av åtgärdsgrupper. För närvarande är anpassning möjlig för e-postämne och Webhook Nyttolast.

##### <a name="customize-e-mail-subject-for-action-group"></a>Anpassa e-postämne för åtgärdsgrupp
Som standard är e-postämnet för `<AlertName>` `<WorkspaceName>`aviseringar: Aviseringsmeddelande för . Men detta kan anpassas, så att du kan specifika ord eller taggar - så att du enkelt kan använda filterregler i inkorgen. Informationen om att anpassa e-posthuvudet måste skicka tillsammans med Information om ActionGroup, som i exemplet nedan.

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

Använd metoden Placera med ett unikt åtgärds-ID för att associera redan befintlig åtgärdsgrupp med anpassning för ett schema.  Följande är ett exempel illustration av användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använd metoden Placera med ett befintligt åtgärds-ID om du vill ändra en åtgärdsgrupp som är associerad för ett schema.  Begärans organ skall innehålla åtgärdens etag.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Anpassa Webhook Nyttolast för åtgärdsgrupp
Som standard har webhooken som skickas via Åtgärdsgrupp för logganalys en fast struktur. Men man kan anpassa JSON nyttolast med hjälp av specifika variabler som stöds, för att uppfylla kraven i webhook slutpunkten. Mer information finns i [Webhook-åtgärd för loggvarningsregler](../../azure-monitor/platform/alerts-log-webhook.md). 

Informationen om anpassa webhook måste skickas tillsammans med Information om ActionGroup och tillämpas på alla Webhook URI som anges i åtgärdsgruppen. som i provet nedan.

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

Använd metoden Placera med ett unikt åtgärds-ID för att associera redan befintlig åtgärdsgrupp med anpassning för ett schema.  Följande är ett exempel illustration av användning.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Använd metoden Placera med ett befintligt åtgärds-ID om du vill ändra en åtgärdsgrupp som är associerad för ett schema.  Begärans organ skall innehålla åtgärdens etag.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Nästa steg

* Använd [REST API för att utföra loggsökningar](../../azure-monitor/log-query/log-query-overview.md) i Log Analytics.
* Lär dig mer om [loggaviseringar i Azure-övervakaren](../../azure-monitor/platform/alerts-unified-log.md)
* Så här [skapar, redigerar eller hanterar du loggaviseringsregler i Azure-övervakaren](../../azure-monitor/platform/alerts-log.md)

