---
title: Scheman för Azure Security Center aviseringar
description: I den här artikeln beskrivs de olika scheman som används av Azure Security Center för säkerhets aviseringar.
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
ms.openlocfilehash: 9b8d7f241f79b087f318c9c416dcadf92838b084
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126220"
---
# <a name="security-alerts-schemas"></a>Säkerhets aviserings scheman

Användare av Azure Security Center standard nivå får säkerhets aviseringar när Security Center identifierar hot mot sina resurser.

Du kan visa dessa säkerhets aviseringar i Azure Security Center s sidor för **skydd mot hot** , eller via externa verktyg som:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) – Microsofts moln-interna Siem. Indikatorn Connector hämtar varningar från Azure Security Center och skickar dem till [Log Analytics arbets ytan](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) för Azure Sentinel.
- Tredjeparts-Siem – Använd Security Centers [löpande export](continuous-export.md) verktyg för att skicka data till [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). Integrera sedan Event Hub-data med en SIEM från tredje part.
- [REST API](https://docs.microsoft.com/rest/api/securitycenter/) – om du använder REST API för att få åtkomst till aviseringar kan du läsa mer i [API-dokumentationen för online-aviseringar](https://docs.microsoft.com/rest/api/securitycenter/alerts).

Om du använder några programmerings metoder för att använda aviseringarna behöver du rätt schema för att hitta de fält som är relevanta för dig. Om du exporterar till en Händelsehubben eller försöker utlösa arbets flödes automatisering med allmänna HTTP-kopplingar använder du scheman för att korrekt parsa JSON-objekten.

>[!IMPORTANT]
> Schemat är något annorlunda för var och en av dessa scenarier, så se till att du väljer fliken relevant nedan.


## <a name="the-schemas"></a>Scheman 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automatisering av arbets flöde och löpande export till Event Hub](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Exempel-JSON för aviseringar som skickas till Logic Apps, Event Hub och Siem från tredje part

Nedan hittar du schemat för de aviserings händelser som skickats till:

- Azure Logic App-instanser som konfigurerades i Security Center s arbets flödes automatisering
- Azure Event Hub med Security Centers kontinuerliga export funktion

Mer information om funktionen för arbets flödes automatisering finns i [automatisera svar på aviseringar och rekommendationer](workflow-automation.md).
Mer information om kontinuerlig export finns i [Exportera aviseringar och rekommendationer](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel och Log Analytics arbets ytor](#tab/schema-sentinel)

Indikatorn Connector hämtar varningar från Azure Security Center och skickar dem till Log Analytics arbets ytan för Azure Sentinel. 

Om du vill skapa ett kontroll trycks fall eller en incident som använder Security Center aviseringar behöver du schemat för de aviseringar som visas nedan. 

Mer information om Azure Sentinel finns i [dokumentationen](https://docs.microsoft.com/azure/sentinel/).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure-aktivitetslogg](#tab/schema-activitylog)

Azure Security Center granskar skapade säkerhets aviseringar som händelser i Azure aktivitets logg.

Du kan visa säkerhets aviserings händelser i aktivitets loggen genom att söka efter aktivera aviserings händelsen som visas:

[![Söker i aktivitets loggen efter händelsen aktivera avisering](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Exempel-JSON för aviseringar som skickats till Azure aktivitets logg

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

### <a name="the-data-model-of-the-schema"></a>Data modellen för schemat

|Fält|Beskrivning|
|----|----|
|**kanal**|Konstant, "åtgärd"|
|**correlationId**|Azure Security Center aviserings-ID|
|**beteckning**|Beskrivning av aviseringen|
|**eventDataId**|Se correlationId|
|**eventName**|Underfälten värde och localizedValue innehåller aviseringens visnings namn|
|**kategori**|Under fälten värde och localizedValue är konstant – "säkerhet"|
|**eventTimestamp**|UTC-tidsstämpel för när aviseringen genererades|
|**identitet**|Fullständigt kvalificerat aviserings-ID|
|**nivå**|Konstant, "information"|
|**operationId**|Se correlationId|
|**operationName**|Värde fältet är konstant – "Microsoft. Security/locations/Alerts/Activate/Action", och det lokaliserade värdet är "Aktivera avisering" (kan eventuellt lokaliseras av användar språket)|
|**resourceGroupName**|Kommer att inkludera resurs gruppens namn|
|**resourceProviderName**|Under fälten värde och localizedValue är konstant – "Microsoft. Security"|
|**Typer**|Under fälten värde och localizedValue är konstant – "Microsoft. Security/locations/Alerts"|
|**resourceId**|Det fullständigt kvalificerade Azure-resurs-ID: t|
|**statusfältet**|Under fälten värde och localizedValue är konstant-"Active"|
|**subStatus**|Under fälten värde och localizedValue är tomma|
|**submissionTimestamp**|UTC-tidsstämpeln för händelse överföringen till aktivitets loggen|
|**subscriptionId**|Prenumerations-ID för den komprometterade resursen|
|**egenskaperna**|En JSON-uppsättning med ytterligare egenskaper som gäller för aviseringen. Dessa kan ändras från en avisering till en annan, men följande fält visas i alla aviseringar:<br>-allvarlighets grad: attackens allvarlighets grad<br>-compromisedEntity: namnet på den komprometterade resursen<br>-remediationSteps: matrisen med åtgärder som ska vidtas<br>– avsikt: Kill-kedje syftet för aviseringen. Möjliga avsikter dokumenteras i [tabellen avsikter](alerts-reference.md#intentions)|
|**relatedEvents**|Konstant tom matris|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph är gatewayen till data och information i Microsoft 365. Det ger en enhetlig programmerings modell som du kan använda för att få åtkomst till den fantastiska mängden data i Microsoft 365, Windows 10 och Enterprise Mobility + Security. Använd stora mängder data i Microsoft Graph för att bygga appar för organisationer och konsumenter som interagerar med miljon tals användare.

Schemat och en JSON-representation för säkerhets varningar som skickas till MS Graph finns i [Microsoft Graph-dokumentationen](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0).

---


## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs scheman som Azure Security Center skydds verktygen för hot som används för att skicka säkerhets aviserings information.

Mer information om hur du kommer åt säkerhets aviseringar utanför Security Center finns på följande sidor:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) – Microsofts moln – inbyggd Siem
- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/) – Microsofts fullständigt hanterade data inmatnings tjänst i real tid
- Security Centers [kontinuerliga export funktion](continuous-export.md)
- [Log Analytics arbets ytor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) – Azure Monitor lagrar loggdata i en Log Analytics arbets yta, en behållare som innehåller data och konfigurations information
