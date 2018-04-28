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
ms.date: 04/06/2018
ms.author: vinagara
ms.openlocfilehash: 0de596f454a1e79b1f5540854897bd15f8de88c4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>Hur du utökar (kopiera) aviseringar från OMS till Azure
Från **den 14 maj 2018**, alla kunder som använder aviseringar som har konfigurerats i [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), utökas till Azure. Aviseringar som har utökats till Azure fungerar på samma sätt som i OMS. Övervakningsfunktioner förblir intakta. Utöka aviseringar som skapats i OMS till Azure ger många fördelar. Mer information om fördelar och utöka aviseringar från OMS till Azure finns [utöka aviseringar från OMS till Azure](monitoring-alerts-extend.md).

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

Och slutligen, om alla aviseringar i arbetsytan angivna redan är schemalagda att utökas till Azure - svaret på POST blir 403 förbjuden. Visa felmeddelanden eller förstå om utöka processen har fastnat, användare kan göra en GET-anrop och fel meddelandet om någon returneras och översikt.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
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

## <a name="troubleshooting"></a>Felsökning 
Under processen med att utöka aviseringar från OMS i Azure, det kan vara tillfälliga problem som förhindrar att skapa nödvändiga [åtgärdsgrupper](monitoring-action-groups.md). I sådana fall visas ett felmeddelande i OMS-portalen via banderoll i aviseringen och GET-anrop som utfärdat till API: T.

Nedan visas steg för varje fel:
1. **Fel: Prenumerationen har inte registrerats för användning av namnrymden 'microsoft.insights'**: ![OMS aviseringsinställningar Portalsida med registrering felmeddelande](./media/monitor-alerts-extend/ErrorMissingRegistration.png)

    a. Den prenumeration som är associerad med din OMS-arbetsyta - har inte registrerats för att använda Azure-Monitor (microsoft.insights)-funktionalitet. på grund av att det går inte att utöka aviseringar i Azure-Monitor & aviseringar och vilka OMS.
    
    b. Lös, registrera microsoft.insights (Azure övervakaren & aviseringar) används i din prenumeration med hjälp av Powershell, Azure CLI eller Azure-portalen. Mer information läser du artikeln på [kodproblem på registrering av resursprovider](../azure-resource-manager/resource-manager-register-provider-errors.md)
    
    c. När löst enligt stegen som beskrivs i artikeln kommer OMS utökar aviseringarna till Azure i nästa dag schemalagda körning; utan att någon åtgärd eller initiering.
2. **Fel: Scope Lås finns på prenumerationen/resursgruppsnivå för skrivåtgärder**: ![OMS aviseringsinställningar Portalsida ScopeLock felmeddelande](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. När Scope låsa är aktiverat, att begränsa nya ändringar prenumeration eller resursgrupp som innehåller arbetsytan logganalys (OMS); systemet kan inte utöka (kopiera) aviseringar i Azure och skapa grupper för nödvändiga åtgärder.
    
    b. Lös genom att ta bort den *ReadOnly* Lås för prenumerationen eller resursen gruppen som innehåller arbetsytan; med Azure-portalen, Powershell, Azure CLI eller API. Mer information läser du artikeln på [Lås Resursanvändning](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. När löst enligt stegen som beskrivs i artikeln kommer OMS utökar aviseringarna till Azure i nästa dag schemalagda körning; utan att någon åtgärd eller initiering.

3. **Fel: Principen finns på prenumerationen/resursgruppsnivå**: ![OMS aviseringsinställningar Portalsida med princip felmeddelande](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. När [Azure princip](../azure-policy/azure-policy-introduction.md) används, att begränsa en ny resurs i prenumeration eller resursgrupp som innehåller arbetsytan logganalys (OMS); systemet kan inte utöka (kopiera) aviseringar i Azure och skapa grupper för nödvändiga åtgärder.
    
    b. Lös genom att redigera principen orsakar *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* fel, vilket förhindrar att skapa nya resurser på din prenumerationen eller resursen grupp som innehåller arbetsytan. Med hjälp av Azure portal, Powershell, Azure CLI eller API; Du kan granska åtgärder för att hitta den lämpliga principen som orsakar felet. Mer information läser du artikeln på [visa aktivitetsloggar om du vill granska åtgärder](../azure-resource-manager/resource-group-audit.md). 
    
    c. När löst enligt stegen som beskrivs i artikeln kommer OMS utökar aviseringarna till Azure i nästa dag schemalagda körning; utan att någon åtgärd eller initiering.


## <a name="next-steps"></a>Nästa steg

* Mer information om den nya [Azure aviseringar uppstår](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure aviseringar](monitor-alerts-unified-log.md).
