---
title: Scheman för Azure Security Center-aviseringar
description: I den här artikeln beskrivs de olika scheman som används av Azure Security Center för säkerhetsaviseringar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062955"
---
# <a name="security-alerts-schemas"></a>Scheman för säkerhetsvarningar

Användare av Azure Security Centers standardnivå får säkerhetsaviseringar när Security Center identifierar hot mot sina resurser.

Du kan visa dessa säkerhetsaviseringar på Azure Security Centers **hotskyddssidor** eller via externa verktyg som:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - Microsofts molnbaserade SIEM. Sentinel Connector får aviseringar från Azure Security Center och skickar dem till [Log Analytics-arbetsytan](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) för Azure Sentinel.
- SIEM-tillverkare från tredje part – Använd Security Centers [kontinuerliga exportverktyg](continuous-export.md) för att skicka data till [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). Integrera sedan dina Event Hub-data med en SIEM från tredje part.
- [REST API](https://docs.microsoft.com/rest/api/securitycenter/) - Om du använder REST API för att komma åt aviseringar, se [online Alerts API-dokumentation](https://docs.microsoft.com/rest/api/securitycenter/alerts).

Om du använder några programmatiska metoder för att använda aviseringarna behöver du rätt schema för att hitta de fält som är relevanta för dig. Om du exporterar till en händelsehubb eller försöker utlösa Automatisering av Arbetsflöde med allmänna HTTP-kopplingar använder du schemana för att tolka JSON-objekten på rätt sätt.

>[!IMPORTANT]
> Schemat är något annorlunda för vart och ett av dessa scenarier, så se till att du väljer den relevanta fliken nedan.


## <a name="the-schemas"></a>Scheman 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automatisering av arbetsflöde och kontinuerlig export till eventhubben](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Exempel på JSON för aviseringar som skickas till Logic Apps, Event Hub och SIEMs från tredje part

Nedan hittar du schemat för de varningshändelser som skickas till:

- Azure Logic App-instanser som har konfigurerats i Security Centers arbetsflödesautomatisering
- Azure Event Hub med security centers kontinuerliga exportfunktion

Mer information om funktionen för automatisering av arbetsflöden finns i [Automatisera svar på aviseringar och rekommendationer](workflow-automation.md).
Mer information om kontinuerlig export finns i [Exportera aviseringar och rekommendationer](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel och Log Analytics arbetsytor](#tab/schema-sentinel)

Sentinel Connector får aviseringar från Azure Security Center och skickar dem till Log Analytics Workspace för Azure Sentinel. 

Om du vill skapa ett Sentinel-ärende eller en incident med Security Center-aviseringar behöver du schemat för de aviseringar som visas nedan. 

Mer information om Azure Sentinel finns [i dokumentationen](https://docs.microsoft.com/azure/sentinel/).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure-aktivitetslogg](#tab/schema-activitylog)

Azure Security Center-granskningar genererade säkerhetsaviseringar som händelser i Azure Activity Log.

Du kan visa säkerhetsaviseringar händelser i Aktivitetsloggen genom att söka efter aktivera avisering händelse som visas:

[![Söka i aktivitetsloggen efter händelsen Aktivera avisering](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Exempel på JSON för aviseringar som skickas till Azure Activity Log

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Schemats datamodell

|Field|Beskrivning|
|----|----|
|**Kanaler**|Konstant, "Operation"|
|**correlationId (korrelationId)**|Aviserings-ID för Azure Security Center|
|**Beskrivning**|Beskrivning av varningen|
|**eventDataId**|Se correlationId|
|**händelseNamn**|Underfälten Värde och lokaliseradVärde innehåller visningsnamnet för avisering|
|**Kategori**|Värdet och lokaliseradeValua delfält är konstanta - "Säkerhet"|
|**händelseTimestamp**|UTC-tidsstämpel för när aviseringen genererades|
|**Id**|Det fullständigt kvalificerade varnings-ID:t|
|**Nivå**|Konstant, "Informativ"|
|**operationId**|Se correlationId|
|**operationName**|Värdefältet är konstant - "Microsoft.Security/locations/alerts/activate/action", och det lokaliserade värdet blir "Aktivera avisering" (kan eventuellt lokaliseras till användarens språk)|
|**resursGroupName**|Kommer att innehålla resursgruppsnamnet|
|**resursProviderNamn**|Underfälten Värde och lokaliseradVärde är konstanta - "Microsoft.Security"|
|**Resurstypens**|Underfälten Värde och lokaliseradVärde är konstanta – "Microsoft.Security/locations/alerts"|
|**resourceId (resourceId)**|Fullständigt kvalificerat Azure-resurs-ID|
|**status**|Värdet och lokaliseradeValua delfält är konstanta - "Aktiv"|
|**understatus**|Underfälten Värde och lokaliseradVärde är tomma|
|**inlämningTimestamp**|UTC-tidsstämpeln för händelseinlämning till aktivitetslogg|
|**subscriptionId**|Prenumerations-ID för den komprometterade resursen|
|**Egenskaper**|En JSON-påse med ytterligare egenskaper som hänför sig till varningen. Dessa kan ändras från en avisering till en annan, men följande fält visas i alla aviseringar:<br>- Svårighetsgrad: Svårighetsgraden av attacken<br>- kompromettivitet: Namnet på den komprometterade resursen<br>- SaneringSteg: En rad åtgärder för sanering som ska vidtas<br>- Uppsåt: Aviseringens avsikt att döda kedjan. Möjliga avsikter dokumenteras i [tabellen Avsikter](alerts-reference.md#intentions)|
|**relateradeHändelser**|Konstant - tom matris|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph är porten till data och intelligens i Microsoft 365. Det ger en enhetlig programmerabilitetsmodell som du kan använda för att komma åt den enorma mängden data i Office 365, Windows 10 och Enterprise Mobility + Security. Använd den mängd data som finns i Microsoft Graph för att skapa appar för organisationer och konsumenter som interagerar med miljontals användare.

Schemat och en JSON-representation för säkerhetsaviseringar som skickas till MS Graph finns i [Dokumentationen för Microsoft Graph](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0).

---


## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs de scheman som Azure Security Centers verktyg för skydd mot hot använder när säkerhetsaviseringsinformation skickas.

Mer information om hur du kommer åt säkerhetsaviseringar utanför Security Center finns på följande sidor:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) – Microsofts molnbaserade SIEM
- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) – Microsofts fullständigt hanterade datainmatningstjänst i realtid
- Security Centers [kontinuerliga exportfunktion](continuous-export.md)
- [Log Analytics-arbetsytor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) - Azure Monitor lagrar loggdata på en Log Analytics-arbetsyta, en behållare som innehåller data- och konfigurationsinformation
