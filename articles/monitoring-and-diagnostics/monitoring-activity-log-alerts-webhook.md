---
title: "Förstå webhook-schemat som används i aktiviteten loggen aviseringar | Microsoft Docs"
description: "Läs mer om schemat för JSON som skickas till en Webhooksadressen när en aktivitet loggen avisering aktiveras."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 75c71bcd16573d4f4dd3377c623aa9b414aa3906
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks för Azure aktiviteten Logga varningar
Som en del av definitionen för en grupp, kan du konfigurera webhook slutpunkter för att ta emot aviseringar för aktiviteten loggen. Du kan använda webhooks, för att vidarebefordra meddelandena till andra system för efterbearbetning eller anpassade åtgärder. Den här artikeln visar hur nyttolasten för HTTP POST till en webhook ser ut.

Mer information om aktiviteten loggen aviseringar finns så [skapa Azure aktivitet Logga varningar](monitoring-activity-log-alerts.md).

Mer information om åtgärdsgrupper finns så [skapa åtgärdsgrupper](monitoring-action-groups.md).

## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan du använda tokenbaserad auktorisering för autentisering. Webhooken URI sparas med ett token ID, till exempel `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Nyttolasten i schemat
JSON-nyttolast som ingår i POST-åtgärden skiljer sig baserat på den nyttolasten data.context.activityLog.eventSource fält.

###<a name="common"></a>Common
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
###<a name="administrative"></a>Administrativa
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
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

Specifika schemainformation om tjänsten notification aktivitet loggen hälsovarningar, finns [tjänsten meddelanden om hälsostatus](monitoring-service-notifications.md).

Information om specifika schemat på alla andra aktiviteten loggen aviseringar finns [översikt över Azure aktivitetsloggen](monitoring-overview-activity-logs.md).

| Elementnamn | Beskrivning |
| --- | --- |
| status |Används för mått aviseringar. Alltid inställt på ”aktiverad” för aktiviteten loggen aviseringar. |
| Kontexten |Kontexten för händelsen. |
| resourceProviderName |Resursprovidern för resursen påverkas. |
| conditionType |Alltid ”Event”. |
| namn |Namnet på regeln. |
| id |Resurs-ID för aviseringen. |
| description |Aviseringsbeskrivningen när aviseringen har skapats. |
| subscriptionId |Azure prenumerations-ID. |
| tidsstämpel |Tid då händelsen skapades av Azure-tjänsten som bearbetade förfrågan. |
| resourceId |Resurs-ID för resursen påverkas. |
| resourceGroupName |Namnet på resursgruppen för resursen påverkas. |
| properties |En uppsättning `<Key, Value>` par (det vill säga `Dictionary<String, String>`) som innehåller information om händelsen. |
| Händelse |Element som innehåller metadata om händelsen. |
| Auktorisering |Rollbaserad åtkomstkontroll egenskaperna för händelsen. Dessa egenskaper innehåller vanligtvis åtgärden rollen och omfång. |
| category |Kategori för händelsen. Värden som stöds omfattar administrativa, varning, säkerhet, ServiceHealth och rekommendation. |
| Anroparen |E-postadressen för användaren som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. Kan vara null för vissa system-anrop. |
| correlationId |Vanligtvis ett GUID i strängformat. Händelser med correlationId tillhör samma större åtgärd och vanligtvis delar en correlationId. |
| eventDescription |Statisk textbeskrivning av händelsen. |
| eventDataId |Unik identifierare för händelsen. |
| eventSource |Namnet på Azure-tjänsten eller infrastruktur som genererade händelsen. |
| httpRequest |Begäran innehåller vanligtvis clientRequestId, clientIpAddress och HTTP-metoden (till exempel PLACERA). |
| nivå |Ett av följande värden: kritisk, fel, varning, information och utförlig. |
| Åtgärds-ID |Vanligtvis ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Namnet på åtgärden. |
| properties |Egenskaper för händelsen. |
| status |Sträng. Status för åtgärden. Vanliga värden är igång, pågår, slutfört, misslyckades, aktiv och löst. |
| subStatus |Normalt innehåller HTTP-statuskod för motsvarande REST-anrop. Det kan även innehålla andra strängar som beskriver en sådan. Vanliga understatus värden är OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inte innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400), det gick inte att hitta (HTTP-statuskod: 404), konflikt (HTTP-statuskod: 409 ), Internt serverfel (HTTP-statuskod: 500), tjänsten inte tillgänglig (HTTP-statuskod: 503), och Gateway-Timeout (HTTP-statuskod: 504). |

## <a name="next-steps"></a>Nästa steg
* [Mer information om aktivitetsloggen](monitoring-overview-activity-logs.md).
* [Köra Azure automation-skript (Runbooks) på Azure-aviseringar](http://go.microsoft.com/fwlink/?LinkId=627081).
* [Använd en logikapp för att skicka ett SMS via Twilio från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Det här exemplet är för mått aviseringar, men den kan ändras för att fungera med en varning för loggen.
* [Använd en logikapp för att skicka en Slack-meddelande från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Det här exemplet är för mått aviseringar, men den kan ändras för att fungera med en varning för loggen.
* [Använd en logikapp för att skicka ett meddelande till en Azure-kö i en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Det här exemplet är för mått aviseringar, men den kan ändras för att fungera med en varning för loggen.
