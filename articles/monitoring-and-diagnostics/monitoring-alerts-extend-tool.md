---
title: Hur du utökar (kopiera) varningar från OMS-portalen i Azure | Microsoft Docs
description: 'API: er som utöka aviseringar från OMS i Azure aviseringar kan göras av kunder frivilligt och verktyg.'
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: c2e11d89f35915ef0a0c1e1f544b0be8df0473de
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>Hur du utökar (kopiera) aviseringar från OMS till Azure
Från **23 April 2018**, alla kunder som använder aviseringar som har konfigurerats i [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), utökas till Azure. Aviseringar som har utökats till Azure fungerar på samma sätt som i OMS. Övervakningsfunktioner förblir intakta. Utöka aviseringar som skapats i OMS till Azure ger många fördelar. Mer information om fördelar och utöka aviseringar från OMS till Azure finns [utöka aviseringar från OMS till Azure](monitoring-alerts-extend.md).

Kunder som vill flytta sina aviseringar från OMS till Azure omedelbart, kan göra det genom att använda ett av alternativen som anges.

## <a name="option-1---using-oms-portal"></a>Alternativ 1 - med OMS-portalen
Följ stegen nedan om du vill initiera frivilligt utöka av aviseringar från OMS-portalen i Azure.

1. Gå till inställningar och aviseringar avsnitt i OMS portal sidan Översikt. Klicka på knappen med etiketten ”utöka i Azure”, som markerade i bilden nedan.

    ![OMS aviseringsinställningar Portalsida med utöka alternativet](./media/monitor-alerts-extend/ExtendInto.png)

2. När du klickar på knappen visas en 3-steg-guiden med det första steget att tillhandahålla information om den här processen. Tryck på Nästa om du vill fortsätta.

    ![Utöka aviseringar från OMS-portalen till Azure - steg 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. I det andra steget visas en sammanfattning av den föreslagna ändringen av lista över lämpliga [åtgärdsgrupper](monitoring-action-groups.md), för aviseringar i OMS-portalen. Om liknande åtgärder ses över mer än en varning - förslag systemet att associera en enda åtgärdsgrupp med alla.  Grupp föreslagna följer namnkonventionen: *WorkspaceName_AG_ #Number*. Klicka på Nästa för att fortsätta.
En exempel-skärmen nedan.

    ![Utöka aviseringar från OMS-portalen till Azure - steg 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. I det sista steget i guiden kan du be OMS-portalen för att schemalägga Utöka alla aviseringar i Azure - genom att skapa nya åtgärdsgrupper och kopplar dem till aviseringar, som visas på skärmen tidigare. Fortsätta väljer Klicka på Slutför och bekräfta i Kommandotolken för att starta processen. Du kan också kan kunder också ge e-postadresser som de vill OMS-portalen för att skicka en rapport om Slutför bearbetningen.

    ![Utöka aviseringar från OMS-portalen till Azure - steg3](./media/monitor-alerts-extend/ExtendStep3.png)

5. När guiden har slutförts, kontrollen kommer tillbaka till sidan aviseringsinställningar och ”utöka i Azure” alternativet kommer att tas bort. I bakgrunden schemalägger OMS-portalen aviseringar i logganalys utökas till Azure; Detta kan ta lite tid och när åtgärden startar för en kort period aviseringar i OMS-portalen är inte tillgängligt för redigering. Aktuell status visas via banderoll och om-e-postadresser where anges under steg 4, kommer att vara informeras när bakgrunden har utökar alla aviseringar i Azure. 

6. Aviseringar fortsätter att visas i OMS-portalen, även när de få har utökats till Azure.

    ![När du utökar aviseringar i OMS-portalen till Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Alternativ 2 – med hjälp av API
För kunder som vill styra programmässigt eller automatisera processen med att utöka aviseringar i OMS-portalen till Azure; Microsoft tillhandahåller nya AlertsVersion API under logganalys.

Log Analytics AlertsVersion API är RESTful och kan nås via Azure Resource Manager REST API. I det här dokumentet hittar du exempel där API: et kan nås från ett PowerShell-kommandorad med [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager. Användning av ARMClient och PowerShell är en av många alternativ för att få åtkomst till API: et. API: et utdata resulterar i JSON-format, så att användning av resultaten på många olika sätt programmässigt.

Genom att använda GET API kan en kan hämta i resultat-sammanfattning av den föreslagna ändringen som lista över lämpliga [åtgärdsgrupper](monitoring-action-groups.md) för aviseringar i OMS-portalen i JSON-format. Om liknande åtgärder ses över mer än en avisering - systemet föreslår att skapa associera du en enda åtgärdsgrupp med alla.  Grupp föreslagna följer namnkonventionen: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> Hämta anrop till API: et resulterar inte i aviseringar i OMS-portalen hämtar utökats till Azure. Det ger endast som svar på Översikt över föreslagna ändringar. För att bekräfta ändringarna göras för att utöka aviseringar i Azure, anropa en POST som behöver göras-API: et.

Om GET-anrop till API: T fungerar tillsammans med 200 OK svar skulle en JSON-lista över aviseringar tillsammans med föreslagna åtgärdsgrupper anges. Exempelsvar nedan:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Om, det finns inga aviseringar i den angivna arbetsytan, tillsammans med 200 OK svar för åtgärden hämta skulle JSON vara:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Om alla aviseringar i arbetsytan angivna redan har utökats till Azure - är svar på GET-anrop:
```json
{
    "version": 2
}
```

Initiera en POST-API: et för att initiera schemaläggning för att utöka aviseringar i OMS-portalen till Azure. Om du gör detta anrop/kommando bekräftar användarens avsikt samt godkännande har sina aviseringar i OMS-portalen utökats till Azure och utföra ändringarna som anges i GET-anrop till API-svar. Användaren kan du kan också ange en lista över e-postadresser till vilka OMS e portal en rapport när schemalagda bakgrunden för att utöka aviseringar i OMS-portalen till Azure slutförs.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Resultat för att utöka OMS portal aviseringar i Azure, kan variera från sammanfattning som tillhandahålls av GET - på kontot för alla ändringar som gjorts i systemet. När schemat, blir aviseringar i OMS-portalen tillfälligt otillgänglig för redigering eller ändrar - när nya aviseringar kan skapas. 

Om det lyckas efter skall den besvaras 200 OK tillsammans med:
```json
{
    "version": 2
}
```
Anger att aviseringar har utökats till Azure, som anges av version 2. Den här versionen är bara för att kontrollera om aviseringar har utökats till Azure och har ingen betydelse i med [Log Analytics Sök API](../log-analytics/log-analytics-api-alerts.md). När aviseringar har utökats till Azure har, alla e-postadresser som angavs under GET skickas en rapport med information om ändringar som gjorts.


Och slutligen, om alla aviseringar i den angivna arbetsytan har redan schemalagts utökas till Azure - svaret på POST blir 403 förbjuden.


## <a name="next-steps"></a>Nästa steg

* Mer information om den nya [Azure aviseringar uppstår](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure aviseringar](monitor-alerts-unified-log.md).
