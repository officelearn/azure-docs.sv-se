---
title: Förstå webhook-schemat som används i aktivitetsloggaviseringar
description: Lär dig mer om schemat för JSON som publiceras på en webhook-URL när en aktivitetsloggavisering aktiveras.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: c076b8dcea350f9ddd66977e89ce99b81f377b17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669054"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks för Azure-aktivitetsloggaviseringar
Som en del av definitionen av en åtgärdsgrupp kan du konfigurera webhook-slutpunkter för att ta emot aviseringar om aktivitetslogg. Med webhooks kan du dirigera dessa meddelanden till andra system för efterbearbetning eller anpassade åtgärder. Den här artikeln visar hur nyttolasten för HTTP-post till en webhook ser ut.

Mer information om aviseringar om aktivitetsloggar finns i hur du [skapar Azure-aktivitetsloggaviseringar](activity-log-alerts.md).

Information om åtgärdsgrupper finns i hur du [skapar åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Du kan också använda det [gemensamma varningsschemat](https://aka.ms/commonAlertSchemaDocs), som ger fördelen av att ha en enda utökningsbar och enhetlig varningsnyttolast över alla varningstjänster i Azure Monitor, för dina webhook-integreringar. [Läs mer om de vanliga definitionerna för varningsscheman.](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan eventuellt använda tokenbaserad auktorisering för autentisering. Webhook URI sparas med ett token-ID, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`till exempel .

## <a name="payload-schema"></a>Schema för nyttolast
JSON-nyttolasten som finns i post-åtgärden skiljer sig åt beroende på nyttolastens data.context.activityLog.eventSource.

### <a name="common"></a>Common

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administrativ

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Säkerhet

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Rekommendation

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHälsa

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Specifik schemainformation om tjänsthälsomeddelandeaktivitetsloggvarningar finns i [Servicehälsomeddelanden](../../azure-monitor/platform/service-notifications.md). Läs dessutom om hur du [konfigurerar webhook-meddelanden för tjänstens hälsotillstånd med dina befintliga problemhanteringslösningar](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth (ResourceHealth)

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Elementnamn | Beskrivning |
| --- | --- |
| status |Används för måttaviseringar. Ställ alltid in på "aktiverad" för aktivitetsloggvarningar. |
| Sammanhang |Sammanhanget för händelsen. |
| resursProviderNamn |Resursleverantören för den påverkade resursen. |
| conditionType (villkorstyp) |Alltid "Händelse". |
| namn |Namnet på varningsregeln. |
| id |Resurs-ID för aviseringen. |
| description |Aviseringsbeskrivning som anges när aviseringen skapas. |
| subscriptionId |Azure-prenumerations-ID. |
| timestamp |Tid då händelsen genererades av Azure-tjänsten som bearbetade begäran. |
| resourceId |Resurs-ID för den påverkade resursen. |
| resourceGroupName |Namn på resursgruppen för den påverkade resursen. |
| properties |Uppsättning `<Key, Value>` par (det villika `Dictionary<String, String>`) som innehåller information om händelsen. |
| händelse |Element som innehåller metadata om händelsen. |
| auktorisering |De rollbaserade åtkomstkontrollegenskaperna för händelsen. Dessa egenskaper innehåller vanligtvis åtgärden, rollen och omfånget. |
| category |Kategori av händelsen. Värden som stöds är administration, varning, säkerhet, servicehälsa och rekommendation. |
| Ringer |E-postadress till den användare som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. Kan vara null för vissa systemanrop. |
| correlationId |Vanligtvis ett GUID i strängformat. Händelser med correlationId tillhör samma större åtgärd och delar vanligtvis en correlationId. |
| händelseBeskrivning |Statisk textbeskrivning av händelsen. |
| eventDataId |Unik identifierare för händelsen. |
| eventKälla |Namn på Azure-tjänsten eller infrastrukturen som genererade händelsen. |
| httpRequest (på) |Begäran innehåller vanligtvis metoden clientRequestId, clientIpAddress och HTTP (till exempel PUT). |
| nivå |Ett av följande värden: Kritisk, Fel, Varning och Informationsformat. |
| operationId |Vanligtvis delas ett GUID mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Operationens namn. |
| properties |Egenskaper för händelsen. |
| status |Sträng. Status för operationen. Vanliga värden är Startad, Pågår, Lyckades, Misslyckades, Aktiv och Löst. |
| understatus |Innehåller vanligtvis HTTP-statuskoden för motsvarande REST-anrop. Det kan också innehålla andra strängar som beskriver en understatus. Vanliga understatusvärden är OK (HTTP-statuskod: 200), Skapad (HTTP-statuskod: 201), Godkänd (HTTP-statuskod: 202), Inget innehåll (HTTP-statuskod: 204), Felaktig begäran (HTTP-statuskod: 400), Hittades inte (HTTP-statuskod: 404), Konflikt (HTTP-statuskod: 409 ), Internt serverfel (HTTP-statuskod: 500), Tjänsten är inte tillgänglig (HTTP-statuskod: 503) och TIMEOUT för gateway (HTTP-statuskod: 504). |

Specifik schemainformation om alla andra aktivitetsloggaviseringar finns [i Översikt över Azure-aktivitetsloggen](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Nästa steg
* [Läs mer om aktivitetsloggen](../../azure-monitor/platform/platform-logs-overview.md).
* [Kör Azure automation-skript (Runbooks) på Azure-aviseringar](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Använd en logikapp för att skicka ett SMS via Twilio från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Det här exemplet är för måttaviseringar, men det kan ändras för att fungera med en aktivitetsloggavisering.
* [Använd en logikapp för att skicka ett Slack-meddelande från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Det här exemplet är för måttaviseringar, men det kan ändras för att fungera med en aktivitetsloggavisering.
* [Använd en logikapp för att skicka ett meddelande till en Azure-kö från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Det här exemplet är för måttaviseringar, men det kan ändras för att fungera med en aktivitetsloggavisering.

