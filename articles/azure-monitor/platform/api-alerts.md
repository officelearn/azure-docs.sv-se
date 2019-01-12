---
title: Med hjälp av Log Analytics avisering REST API
description: 'Log Analytics avisering REST-API kan du skapa och hantera aviseringar i Log Analytics som ingår i Log Analytics.  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.'
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
ms.date: 04/10/2018
ms.author: bwren
ms.openlocfilehash: d6096967c33866f9498f413a4a73fc1d7eae9ede
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231367"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Skapa och hantera Varningsregler i Log Analytics med REST API
Log Analytics avisering REST-API kan du skapa och hantera aviseringar i Log Analytics.  Den här artikeln innehåller information om API: et och flera exempel för att utföra olika åtgärder.

Log Analytics Search REST API är RESTful och kan nås via Azure Resource Manager REST API. I det här dokumentet hittar du exempel där API: T hämtas från en PowerShell från kommandoraden med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager. Användning av ARMClient och PowerShell är ett av många alternativ för att få åtkomst till Log Analytics Search-API. Du kan använda RESTful Azure Resource Manager-API för att göra anrop till Log Analytics-arbetsytor och utföra sökkommandon i dem med de här verktygen. API: et kommer mata ut sökresultat till dig i JSON-format, så att du kan använda sökresultaten på många olika sätt programmässigt.

## <a name="prerequisites"></a>Förutsättningar
Aviseringar kan för närvarande kan bara skapas med en sparad sökning i Log Analytics.  Du kan referera till den [Log Search REST API](../../azure-monitor/log-query/log-query-overview.md) för mer information.

## <a name="schedules"></a>Scheman
En sparad sökning kan ha ett eller flera scheman. Schemat definierar hur ofta sökningen är kör och det tidsintervall som villkoren har identifierats.
Scheman har egenskaper i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Intervall |Hur ofta sökningen körs. Mätt i minuter. |
| queryTimeSpan |Det tidsintervall som villkoren utvärderas. Måste vara lika med eller större än intervall. Mätt i minuter. |
| Version |API-version som används.  För närvarande ska detta alltid vara inställd på 1. |

Anta exempelvis att en fråga med ett intervall på 15 minuter och Timespan 30 minuter. I det här fallet frågan skulle köras var 15: e minut och en avisering skulle aktiveras om villkoren fortsatt att lösa till SANT över ett 30-minuters intervall.

### <a name="retrieving-schedules"></a>Hämtning av scheman
Använda Get-metoden för att hämta alla scheman för en sparad sökning.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Använda Get-metoden med ett schema-ID för att hämta ett visst schema för en sparad sökning.

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
Använda Put-metoden med ett unikt schema-ID för att skapa ett nytt schema.  Observera att två scheman inte kan ha samma ID även om de är associerade med olika sparade sökningar.  När du skapar ett schema i Log Analytics-konsolen, skapas en GUID för schema-ID.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics-API måste vara i gemener.

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
Ett schema kan ha flera åtgärder. En åtgärd kan definiera en eller flera processer för att utföra, till exempel skickar ett e-postmeddelande eller starta en runbook eller den kan definiera ett tröskelvärde som bestämmer när resultatet av en sökning som matchar vissa kriterier.  Vissa åtgärder kommer definiera både så att processerna som utförs när tröskelvärdet är uppfyllt.

Alla åtgärder har egenskaper i följande tabell.  Olika typer av aviseringar har olika ytterligare egenskaper som beskrivs nedan.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Typ |Typ av åtgärd.  Möjliga värden är för närvarande aviseringen och Webhook. |
| Namn |Visningsnamn för aviseringen. |
| Version |API-version som används.  För närvarande ska detta alltid vara inställd på 1. |

### <a name="retrieving-actions"></a>Hämta åtgärder

> [!NOTE]
> Från och den 14 maj 2018 utökas alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utökade aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar från Log Analytics i Azure](../../azure-monitor/platform/alerts-extend.md). För användare som utökar aviseringar till Azure, styrs nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar har utökats till Azure, hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Använda Get-metoden för att hämta alla åtgärder för ett schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Använd Get-metoden med åtgärds-ID för att hämta en viss åtgärd för ett schema.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Skapa eller redigera åtgärder
Använda Put-metoden med en åtgärds-ID som är unik för schema för att skapa en ny åtgärd.  När du skapar en åtgärd i Log Analytics-konsolen, måste ett GUID för åtgärden-ID.

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med Log Analytics-API måste vara i gemener.

Använda Put-metoden med en befintlig åtgärds-ID för samma sparad sökning om du vill ändra schemat.  Brödtexten i begäran måste innehålla etag för schemat.

Format för förfrågan för att skapa en ny åtgärd varierar åtgärdstyp så att de här exemplen finns i avsnitten nedan.

### <a name="deleting-actions"></a>Ta bort åtgärder

> [!NOTE]
> Från och den 14 maj 2018 utökas alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utökade aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar från Log Analytics i Azure](../../azure-monitor/platform/alerts-extend.md). För användare som utökar aviseringar till Azure, styrs nu åtgärder i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar har utökats till Azure, hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Använda Delete-metoden med åtgärds-ID för att ta bort en åtgärd.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Aviseringsåtgärder
Ett schema måste ha en Aviseringsåtgärd.  Aviseringsåtgärder har en eller flera av avsnitten i följande tabell.  Beskrivs i mer detalj nedan.

| Section | Beskrivning | Användning |
|:--- |:--- |:--- |
| Tröskelvärde |Kriterier för när åtgärden har körts.| Krävs för varje avisering före eller efter att de har utökats till Azure. |
| Severity |Etikett som används för att klassificera avisering när den utlöses.| Krävs för varje avisering före eller efter att de har utökats till Azure. |
| Utelämna |Alternativet för att stoppa meddelanden från aviseringen. | Valfritt för varje avisering innan eller efter att de har utökats till Azure. |
| Åtgärdsgrupper |ID: N för Azure ActionGroup där åtgärder som krävs har angetts, - e-postmeddelanden, SMSs, röstsamtal, Webhooks, Automation-Runbooks, ITSM-anslutningsprogram, t.ex.| Krävs när aviseringar har utökats till Azure|
| Anpassa åtgärder|Ändra standardutdata för väljer åtgärder från ActionGroup| Du kan använda valfritt för varje avisering när aviseringar har utökats till Azure. |
| EmailNotification |Skicka e-post till flera mottagare. | Inte krävs, om aviseringar har utökats till Azure|
| Åtgärd |Starta en runbook i Azure Automation för att försöka åtgärda identifierade problem. |Inte krävs, om aviseringar har utökats till Azure|
| Webhook-åtgärder | Skicka data från aviseringar, till önskade tjänsten som JSON |Inte krävs, om aviseringar har utökats till Azure|

> [!NOTE]
> Från och den 14 maj 2018 utökas alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utökade aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar från Log Analytics i Azure](../../azure-monitor/platform/alerts-extend.md).

#### <a name="thresholds"></a>Tröskel
En Aviseringsåtgärd bör ha ett och endast ett tröskelvärde.  När resultatet av en sparad sökning matchar tröskelvärdet i en åtgärd som är associerade med sökningen, körs alla andra processer i åtgärden.  En åtgärd kan också innehålla endast ett tröskelvärde så att den kan användas med åtgärder från andra typer som inte innehåller tröskelvärden.

Tröskelvärden har egenskaper i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Operator |Operator för tröskelvärde för jämförelse. <br> gt = större än <br> lt = mindre än |
| Värde |Värde för tröskelvärdet. |

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
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en åtgärd för tröskelvärde för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Log Analytics kan du klassificera aviseringar i kategorier så att enklare hantering och prioritering. Aviseringens allvarlighetsgrad definierats är: information, varning och kritiskt. Dessa är mappade till normaliserade allvarlighetsgrad skalan för Azure-aviseringar som:

|Allvarlighetsgrad för log Analytics  |Allvarlighetsgrad för Azure-aviseringar  |
|---------|---------|
|kritisk |Sev 0|
|varning |Sev 1|
|Informationsmeddelande | Sev 2|

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
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en allvarlighetsgrad-åtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Utelämna
Log Analytics bygger fråga aviseringar utlöses varje gång tröskelvärde har uppnåtts eller överskridits. Baserat på logiken underförstådd i frågan kan detta resultera i aviseringen komma utlöses för en serie intervall och kan därför meddelanden också skickas kontinuerligt. För att förhindra sådana scenariot kan en användare ange utelämna alternativet anvisningar om hur Log Analytics för att vänta tills en stipulerade lång tid innan meddelande utlöses den andra gången för regeln. Om utelämna anges under 30 minuter. sedan kommer aviseringen utlöses första gången och skicka meddelanden som har konfigurerats. Men sedan vänta i 30 minuter innan meddelandet för regeln används igen. Varningsregeln fortsätter att köras i övergångsperioden, – endast meddelande undertrycks av Log Analytics för angiven tidpunkt, oavsett hur många gånger varningsregeln har utlösts under den här perioden.

Utelämna egenskapen för Log Analytics varningsregel anges med hjälp av den *begränsning* värde och den Undertryckning period med *DurationInMinutes* värde.

Följande är ett exempelsvar för en åtgärd med bara ett tröskelvärde, allvarlighetsgrad och utelämna egenskapen

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
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en allvarlighetsgrad-åtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Åtgärdsgrupper
Alla aviseringar i Azure, använda åtgärdsgrupp som standardmekanism för att hantera åtgärder. Med åtgärdsgrupp kan du ange dina åtgärder en gång och sedan associerar åtgärdsgrupp att flera aviseringar – i Azure. Utan att behöva flera gånger deklarera samma åtgärder om och om igen. Åtgärdsgrupper stöd för flera åtgärder – inklusive e-post, SMS, röstsamtal, ITSM-anslutningen, Automation-Runbook, Webhook URI med mera. 

För användare som har utökat sin aviseringar i Azure – bör ett schema nu ha åtgärdsgrupp information skickas tillsammans med tröskelvärdet för att kunna skapa en avisering. Information om e-post, Webhook-URL: er, Runbook Automation-information och andra åtgärder måste vara definierade i sida en åtgärdsgrupp innan du kan skapa en avisering; går att skapa [åtgärdsgrupp från Azure Monitor](../../azure-monitor/platform/action-groups.md) i portalen eller Använd [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

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
Som standard är e-postämnet för aviseringar Varningsavisering <AlertName> för <WorkspaceName>. Men detta kan anpassas, så att du kan vissa specifika ord eller taggar – så att du kan enkelt använda filterregler i din inkorg. Anpassa e-huvudet information behöver skicka tillsammans med ActionGroup information, som i exemplet nedan.

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

#### <a name="email-notification"></a>E-postmeddelande
E-postaviseringar skicka e-post till en eller flera mottagare.  De kan innehålla egenskaperna i följande tabell.

> [!NOTE]
> Från och den 14 maj 2018 utökas alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utökade aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar från Log Analytics i Azure](../../azure-monitor/platform/alerts-extend.md). Åtgärder som e-postavisering styrs nu i Azure åtgärdsgrupper för användare som utökar aviseringar till Azure. När en arbetsyta och dess aviseringar har utökats till Azure, hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Egenskap  | Beskrivning |
|:--- |:--- |
| Mottagare |Lista över e-postadresser. |
| Subjekt |Ämne för e-postmeddelandet. |
| Bifogad fil |Bifogade filer stöds inte för närvarande, så att det alltid har värdet ”None”. |

Följande är ett exempelsvar för en e-notification-åtgärd med ett tröskelvärde.  

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

Använda Put-metoden med en unik åtgärds-ID för att skapa en ny e-post-åtgärd för ett schema.  I följande exempel skapas ett e-postmeddelande med ett tröskelvärde så att e-postmeddelandet skickas när resultatet av den sparade sökningen överskrider tröskelvärdet.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en e poståtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Åtgärder
Reparationer startar en runbook i Azure Automation som försöker åtgärda problemet som identifierats av aviseringen.  Du måste skapa en webhook för den runbook som används i en Reparationsåtgärd och anger sedan URI: N i egenskapen WebhookUri.  När du skapar den här åtgärden med hjälp av Azure-portalen skapas automatiskt en ny webhook för runbooken.

> [!NOTE]
> Från och den 14 maj 2018 utökas alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utökade aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar från Log Analytics i Azure](../../azure-monitor/platform/alerts-extend.md). För användare som utökar aviseringar till Azure, styrs nu åtgärder som att åtgärder med hjälp av runbook i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar har utökats till Azure, hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Reparationer innehålla egenskaper i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| RunbookName |Namnet på runbooken. Detta måste matcha en publicerad runbook i automation-kontot som konfigurerats i Automation-lösningen i Log Analytics-arbetsytan. |
| WebhookUri |URI för webhooken. |
| Förfallodatum |Upphör att gälla och tid för webhooken.  Om webhooken inte har en giltighetstid, kan det vara valfritt giltigt framtida datum. |

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

Använda Put-metoden med en unik åtgärds-ID för att skapa en ny Reparationsåtgärd för ett schema.  I följande exempel skapas en reparation med ett tröskelvärde så att runbook startas när resultatet av den sparade sökningen överskrider tröskelvärdet.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en Reparationsåtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exempel
Följande är ett komplett exempel att skapa en ny e-postavisering.  Detta skapar ett nytt schema tillsammans med en åtgärd som innehåller ett tröskelvärde och e-post.

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
Webhook-åtgärder kan du starta en process genom att anropa en URL och att du kan också tillhandahålla en nyttolast som ska skickas.  De liknar åtgärder förutom de är avsedda för webhooks som kan anropa processer än Azure Automation-runbooks.  Användaren kan även ange ytterligare alternativ för att tillhandahålla en nyttolast som ska levereras till fjärr-processen.

> [!NOTE]
> Från och den 14 maj 2018 utökas alla aviseringar i en offentliga Azure-molninstans av Log Analytics-arbetsyta automatiskt till Azure. En användare kan frivilligt initiera utökade aviseringar till Azure innan den 14 maj 2018. Mer information finns i [utöka aviseringar från Log Analytics i Azure](../../azure-monitor/platform/alerts-extend.md). För användare som utökar aviseringar till Azure, styrs nu åtgärder som att Webhook i Azure åtgärdsgrupper. När en arbetsyta och dess aviseringar har utökats till Azure, hämta eller lägga till åtgärder med hjälp av den [åtgärd grupp API](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Webhook-åtgärder har inte ett tröskelvärde men i stället ska läggas till ett schema som har en Aviseringsåtgärd med ett tröskelvärde.  

Följande är en exempelsvaret för webhook-åtgärd och en tillhörande Aviseringsåtgärd med ett tröskelvärde.

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
Använda Put-metoden med en unik åtgärds-ID för att skapa en ny webhook-åtgärd för ett schema.  I följande exempel skapas en Webhook-åtgärd och en Aviseringsåtgärd med ett tröskelvärde så att webhooken ska utlösas när resultatet av den sparade sökningen överstiger tröskelvärdet.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Använda Put-metoden med en befintlig åtgärds-ID om du vill ändra en webhook-åtgärd för ett schema.  Brödtexten i begäran måste innehålla etag för åtgärden.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Nästa steg
* Använd den [REST API för att utföra sökningar i loggen](../../azure-monitor/log-query/log-query-overview.md) i Log Analytics.
* Lär dig mer om [loggaviseringar i azure-aviseringar](../../azure-monitor/platform/alerts-unified-log.md)

