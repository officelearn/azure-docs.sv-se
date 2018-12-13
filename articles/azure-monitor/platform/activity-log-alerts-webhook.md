---
title: Förstå webhook-schema som används i aktivitetsloggaviseringar
description: Läs mer om schemat för JSON som publiceras till en webhook-URL när en aktivitetsloggavisering aktiverar.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 3fb68370f9b26aacc33d0e2e6090fe819010f468
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285013"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks för aviseringar för Azure-aktivitetsloggar
Som en del av definitionen av en åtgärdsgrupp kan konfigurera du webhook-slutpunkter för att få aviseringar för aktiviteten log. Du kan använda webhooks, för att vidarebefordra dessa meddelanden till andra system för efterbearbetning eller anpassade åtgärder. Den här artikeln visar hur nyttolast för HTTP-POST till en webhook som ser ut.

Läs mer på aktivitetsloggsaviseringar så [skapa aviseringar för Azure aktivitetsloggen](activity-log-alerts.md).

Information om åtgärdsgrupper finns i så här [skapa åtgärdsgrupper](../../azure-monitor/platform/action-groups.md).

## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan du kan också använda tokenbaserad auktorisering för autentisering. Webhooken URI sparas med ett token-ID, till exempel `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Nyttolast-schema
JSON-nyttolasten i POST-åtgärden skiljer sig beroende på den nyttolast data.context.activityLog.eventSource fält.

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
### <a name="servicehealth"></a>ServiceHealth
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

### <a name="resourcehealth"></a>ResourceHealth
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
                    "cause": "PlatformInitiated",
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

Visst schemainformation om service health meddelande aktivitetsloggaviseringar, finns i [Service health meddelanden](../../monitoring-and-diagnostics/monitoring-service-notifications.md). Läs dessutom hur du [konfigurera service health webhook-aviseringar med din befintliga lösningar på problem management](../../service-health/service-health-alert-webhook-guide.md).

Information om specifika schemat på alla andra aktivitetsloggaviseringar, se [översikt över Azure-aktivitetsloggen](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

| Elementnamn | Beskrivning |
| --- | --- |
| status |Används för aviseringar för mått. Alltid inställt på ”aktiverad” för aktivitetsloggaviseringar. |
| Kontext |Kontext för händelsen. |
| resourceprovidername får |Resursprovidern för resursen som påverkas. |
| conditionType |Alltid ”händelse”. |
| namn |Namnet på regeln. |
| id |Resurs-ID för aviseringen. |
| beskrivning |Aviseringsbeskrivningen när aviseringen har skapats. |
| subscriptionId |Azure-prenumerations-ID. |
| tidsstämpel |Tid då händelsen genererades av Azure-tjänsten som bearbetade förfrågan. |
| resourceId |Resurs-ID för resursen som påverkas. |
| resourceGroupName |Namnet på resursgruppen för resursen som påverkas. |
| properties |Uppsättning `<Key, Value>` par (det vill säga `Dictionary<String, String>`) som innehåller information om händelsen. |
| händelse |Element som innehåller metadata om händelsen. |
| Auktorisering |Role-Based Access Control-egenskaper för händelsen. Dessa egenskaper innehåller vanligtvis åtgärden, rollen och omfång. |
| category |Händelsens kategori. Värden som stöds omfattar administrativa, varning, säkerhet, ServiceHealth och rekommendation. |
| uppringare |E-postadressen för användaren som utförde åtgärden, UPN-anspråket och SPN-anspråk baserat på tillgänglighet. Kan vara null för vissa systemanrop. |
| correlationId |Vanligtvis ett GUID i strängformat. Händelser med correlationId tillhör samma större åtgärd och vanligtvis dela en correlationId. |
| eventDescription |Statisk textbeskrivning av händelsen. |
| eventDataId |Unik identifierare för händelsen. |
| Händelsekälla |Namnet på Azure-tjänst eller infrastruktur som genererade händelsen. |
| httpRequest |Begäran innehåller vanligtvis clientRequestId, clientIpAddress och HTTP-metoden (till exempel PLACERA). |
| nivå |En av följande värden: Kritiskt, fel, varning och information. |
| operationId |Vanligtvis ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Åtgärdens namn. |
| properties |Egenskaper för händelsen. |
| status |sträng. Status för åtgärden. Vanliga värden är startad, pågår, slutfört, misslyckades, aktiv och löst. |
| understatus |Innefattar vanligtvis HTTP-statuskod för motsvarande REST-anropet. Det kan även innehålla andra strängar som beskriver en understatus. Vanliga understatus värden är OK (HTTP-statuskod: 200) skapade (HTTP-statuskod: 201), godkänt (HTTP-statuskod: 202), inget innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400) hittades inte (HTTP-statuskod: 404) konflikt (HTTP-statuskod: 409), interna serverfel (HTTP-statuskod: 500), tjänsten är inte tillgänglig (HTTP-statuskod: 503) och Gateway-Timeout (HTTP-statuskod: 504). |

## <a name="next-steps"></a>Nästa steg
* [Läs mer om aktivitetsloggen](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).
* [Köra automatiseringsskript i Azure (Runbooks) på Azure-aviseringar](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Använd en logikapp för att skicka ett SMS via Twilio från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Det här exemplet är för måttaviseringar, men den kan ändras för att fungera med en aktivitetsloggavisering.
* [Använd en logikapp för att skicka ett Slack-meddelande från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Det här exemplet är för måttaviseringar, men den kan ändras för att fungera med en aktivitetsloggavisering.
* [Använd en logikapp för att skicka ett meddelande till en Azure-kö från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Det här exemplet är för måttaviseringar, men den kan ändras för att fungera med en aktivitetsloggavisering.
