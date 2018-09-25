---
title: Azure Händelseschema för aktivitetslogg
description: Förstå Händelseschema för data som skickas till aktivitetsloggen
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 4/12/2018
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: d267ffd5085c27c60e9eb229e2d9026fa83ef848
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998256"
---
# <a name="azure-activity-log-event-schema"></a>Azure Händelseschema för aktivitetslogg
Den **Azure-aktivitetsloggen** är en logg som ger insikt i alla händelser på prenumerationsnivå som har inträffat i Azure. Den här artikeln beskriver Händelseschema per kategori av data. Schemat för data skiljer sig beroende på om du läser data i portalen, PowerShell, CLI, eller direkt via REST API jämfört med [strömmande data till lagring eller Event Hubs med en Loggprofil](./monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). Exemplen nedan visar schemat som gjorts tillgängliga via portalen, PowerShell, CLI och REST API. En mappning av dessa egenskaper så att den [Azure diagnostisk loggar schemat](./monitoring-diagnostic-logs-schema.md) tillhandahålls i slutet av artikeln.

## <a name="administrative"></a>Administrativ
Den här kategorin innehåller en post för alla skapa, uppdatera och ta bort åtgärden åtgärder som utförs via Resource Manager. Exempel på typer av händelser som visas i den här kategorin är ”Skapa virtuell dator” och ”ta bort nätverkssäkerhetsgruppen” varje åtgärd som en användare eller program med hjälp av Resource Manager är utformat som en åtgärd på en viss resurstyp. Om åtgärdstypen är skriva, ta bort eller åtgärden, registreras poster i start- och lyckas eller misslyckas av åtgärden i den administrativa kategorin. Den administrativa kategorin omfattar även ändringar av rollbaserad åtkomstkontroll i en prenumeration.

### <a name="sample-event"></a>Exempelhändelse
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Auktorisering |BLOB för RBAC egenskaper för händelsen. Vanligtvis inkluderar egenskaper för ”action”, ”roll” och ”omfång”. |
| uppringare |E-postadress för den användare som har utfört den åtgärden eller UPN-anspråket SPN-anspråk baserat på tillgänglighet. |
| kanaler |Något av följande värden: ”Admin”, ”åtgärden” |
| anspråk |Detta JWT-token som används av Active Directory för att autentisera användaren eller programmet för den här åtgärden i Resource Manager. |
| correlationId |Vanligtvis ett GUID i formatet för strängen. Händelser som delar en correlationId tillhöra samma uber åtgärd. |
| beskrivning |Statisk textbeskrivning av en händelse. |
| eventDataId |Unik identifierare för en händelse. |
| httpRequest |BLOB-objekt som beskriver Http-begäran. Vanligtvis innehåller ”clientRequestId”, ”clientIpAddress” och ”method” (HTTP-metoden. Till exempel PLACERA). |
| nivå |Nivån på händelsen. Något av följande värden: ”kritisk”, ”Error”, ”varning” och ”information” |
| resourceGroupName |Namnet på resursgruppen för resursen som påverkas. |
| resourceprovidername får |Namnet på resursprovidern för resursen som påverkas |
| resourceId |Resurs-ID för resursen som påverkas. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Åtgärdens namn. |
| properties |Uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver informationen om händelsen. |
| status |Sträng som anger status för åtgärden. Vissa vanliga värden är: igång i pågår, slutfört, misslyckades, aktiva, löst. |
| understatus |Vanligtvis HTTP-statuskod för motsvarande RESTEN anropa, men kan även innehålla andra strängar som beskriver en understatus, till exempel dessa vanliga värden: OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inget innehåll (HTTP-Status Kod: 204), felaktig begäran (HTTP-statuskod: 400), fanns inte (HTTP-statuskod: 404), konflikt (HTTP-statuskod: 409), interna serverfel (HTTP-statuskod: 500), Service inte tillgänglig (HTTP-statuskod: 503), Gateway-Timeout (HTTP-statuskod: 504). |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure behandlingen av begäran som motsvarande händelsen. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure-prenumerations-ID. |

## <a name="service-health"></a>Service Health
Den här kategorin innehåller en post för alla service health incidenter som har inträffat i Azure. Ett exempel på typen av händelse som du ser i den här kategorin är ”SQL Azure i östra USA har drabbats av driftstopp”. Service health-händelser levereras i fem sorterna: åtgärd krävs, Assisted-återställning, Incident, underhåll, Information eller säkerhet, och visas bara om du har en resurs i den prenumeration som skulle påverkas av händelsen.

### <a name="sample-event"></a>Exempelhändelse
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Referera till den [service health meddelanden](./monitoring-service-notifications.md) artikeln efter dokumentation om värdena i egenskaperna.

## <a name="resource-health"></a>Resurshälsa
Den här kategorin innehåller en post för eventuella resource health-händelser som har inträffat för dina Azure-resurser. Ett exempel på typen av händelse som du ser i den här kategorin är ”virtuell dator hälsostatus ändrats till inte tillgänglig”. Resource health-händelser kan representera en av fyra health-status: tillgänglig, är inte tillgänglig, Degraderad och okänd. Dessutom kan resurshälsotillståndshändelser kategoriseras som användaren startat eller plattform initieras.

### <a name="sample-event"></a>Exempelhändelse

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription Id>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| kanaler | Alltid ”Admin, åtgärd” |
| correlationId | Ett GUID i formatet för strängen. |
| beskrivning |Statisk textbeskrivning av händelsen avisering. |
| eventDataId |Unik identifierare för händelsen avisering. |
| category | Alltid ”ResourceHealth” |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure behandlingen av begäran som motsvarande händelsen. |
| nivå |Nivån på händelsen. Något av följande värden: ”kritisk”, ”Error”, ”varning”, ”information” och ”utförlig” |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Åtgärdens namn. |
| resourceGroupName |Namnet på resursgruppen som innehåller resursen. |
| resourceprovidername får |Alltid ”Microsoft.Resourcehealth/healthevent/action”. |
| ResourceType | Typ av resurs som påverkades av en Resource Health-händelse. |
| resourceId | Namn på resurs-ID för resursen som påverkas. |
| status |Sträng som anger status för hälsohändelsen. Värdena kan vara: aktiv, löst, pågår, uppdaterad. |
| understatus | Vanligtvis null för aviseringar. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure-prenumerations-Id. |
| properties |Uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver informationen om händelsen.|
| Properties.title | En användarvänlig-sträng som beskriver resursen hälsostatus. |
| Properties.details | En användarvänlig-sträng som innehåller ytterligare information om händelsen. |
| properties.currentHealthStatus | Aktuellt tillstånd för resursen. Något av följande värden: ”tillgänglig”, ”ej tillgänglig”, ”Degraderad” och ”okänt”. |
| properties.previousHealthStatus | Föregående hälsostatus för resursen. Något av följande värden: ”tillgänglig”, ”ej tillgänglig”, ”Degraderad” och ”okänt”. |
| properties.type | En beskrivning av typen av resource health händelse. |
| Properties.Cause | En beskrivning av orsaken till hälsohändelsen resurs. ”UserInitiated” och ”PlatformInitiated”. |


## <a name="alert"></a>Varning
Den här kategorin innehåller en post för alla Azure-aviseringar-aktiveringar. Ett exempel på typen av händelse som du ser i den här kategorin är ”processor på myVM har varit över 80 under de senaste 5 minuterna”. En mängd olika Azure-system har en datastyrd begrepp – du kan definiera en regel av något slag och få ett meddelande när villkoren matchar regeln. Varje gång en stöds Azure aviseringstyp ”aktiverar,' eller villkoren uppfylls för att generera ett meddelande, en post med aktiveringen skickas också till den här kategorin för aktivitetsloggen.

### <a name="sample-event"></a>Exempelhändelse

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| uppringare | Alltid Microsoft.Insights/alertRules |
| kanaler | Alltid ”Admin, åtgärd” |
| anspråk | JSON-blob med den SPN (service principal name) eller resursen typ av avisering motorn. |
| correlationId | Ett GUID i formatet för strängen. |
| beskrivning |Statisk textbeskrivning av händelsen avisering. |
| eventDataId |Unik identifierare för händelsen avisering. |
| nivå |Nivån på händelsen. Något av följande värden: ”kritisk”, ”Error”, ”varning” och ”information” |
| resourceGroupName |Namnet på resursgruppen för resursen som påverkas om det är en metrisk varning. För andra aviseringstyper kan är det här namnet på resursgruppen som innehåller aviseringen själva. |
| resourceprovidername får |Namnet på resursprovidern för resursen som påverkas om det är en metrisk varning. För andra aviseringstyper kan är det här namnet på resursprovidern för aviseringen själva. |
| resourceId | Namn på resurs-ID för resursen som påverkas om det är en metrisk varning. För andra aviseringstyper kan är det här resurs-ID för själva avisering resursen. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Åtgärdens namn. |
| properties |Uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver informationen om händelsen. |
| status |Sträng som anger status för åtgärden. Vissa vanliga värden är: igång i pågår, slutfört, misslyckades, aktiva, löst. |
| understatus | Vanligtvis null för aviseringar. |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure behandlingen av begäran som motsvarande händelsen. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure-prenumerations-ID. |

### <a name="properties-field-per-alert-type"></a>Egenskapsfältet per typ av avisering
Egenskapsfältet innehåller olika värden beroende på källan till aviseringen händelsen. Två vanliga varning avisering-providrar är aktivitetsloggsaviseringar och måttaviseringar.

#### <a name="properties-for-activity-log-alerts"></a>Egenskaper för aviseringar för aktivitetsloggen
| Elementnamn | Beskrivning |
| --- | --- |
| properties.subscriptionId | Prenumerations-ID från den händelse i aktivitetsloggen som gjorde att den här aviseringsregeln för aktivitetsloggen aktiveras. |
| properties.eventDataId | Data event ID från den händelse i aktivitetsloggen som gjorde att den här aviseringsregeln för aktivitetsloggen aktiveras. |
| properties.resourceGroup | Resursgruppen från den händelse i aktivitetsloggen som gjorde att den här aviseringsregeln för aktivitetsloggen aktiveras. |
| properties.resourceId | Resurs-ID från den händelse i aktivitetsloggen som gjorde att den här aviseringsregeln för aktivitetsloggen aktiveras. |
| properties.eventTimestamp | Händelsetidsstämpel för den händelse i aktivitetsloggen som gjorde att den här aviseringsregeln för aktivitetsloggen aktiveras. |
| properties.operationName | Åtgärdens namn från den händelse i aktivitetsloggen som gjorde att den här aviseringsregeln för aktivitetsloggen aktiveras. |
| Properties.status | Status från den händelse i aktivitetsloggen som gjorde att den här aviseringsregeln för aktivitetsloggen aktiveras.|

#### <a name="properties-for-metric-alerts"></a>Egenskaper för aviseringar för mått
| Elementnamn | Beskrivning |
| --- | --- |
| Egenskaper. RuleUri | Resurs-ID för måttaviseringsregel själva. |
| Egenskaper. Regelnamn | Namnet på måttaviseringsregel. |
| Egenskaper. RuleDescription | Beskrivning av måttaviseringsregel (enligt definitionerna i varningsregeln). |
| Egenskaper. Tröskelvärde | Tröskelvärdet används vid utvärderingen av måttaviseringsregel. |
| Egenskaper. WindowSizeInMinutes | Fönstret storleken som används vid utvärderingen av måttaviseringsregel. |
| Egenskaper. Sammansättning | Sammansättningstyp som definierats i måttaviseringsregel. |
| Egenskaper. Operator | Villkorsstyrd operator som används vid utvärderingen av måttaviseringsregel. |
| Egenskaper. MetricName | Måttet namnet på det mått som används vid utvärderingen av måttaviseringsregel. |
| Egenskaper. MetricUnit | Metrisk enhet för det mått som används vid utvärderingen av måttaviseringsregel. |

## <a name="autoscale"></a>Automatisk skalning
Den här kategorin innehåller en post för alla händelser relaterade till driften av motorn för automatisk skalning baserat på alla inställningarna för automatisk skalning som du har definierat i din prenumeration. Ett exempel på typen av händelse som du ser i den här kategorin är ”autoskalning uppåt åtgärden misslyckades”. Med automatisk skalning kan du automatiskt skala ut eller skala antalet instanser i en resurstyp som stöds som är baserade på tid på dagen och/eller belastningen (mått) data med hjälp av en autoskalningsinställning. När villkoren är uppfyllda att skala upp eller ned början och lyckades eller misslyckades händelser kommer att läggas till i den här kategorin.

### <a name="sample-event"></a>Exempelhändelse
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| uppringare | Alltid Microsoft.Insights/autoscaleSettings |
| kanaler | Alltid ”Admin, åtgärd” |
| anspråk | JSON-blob med typen SPN (service principal name) eller resurs av motorn för automatisk skalning. |
| correlationId | Ett GUID i formatet för strängen. |
| beskrivning |Statisk textbeskrivning av händelsen automatisk skalning. |
| eventDataId |Unik identifierare för händelsen automatisk skalning. |
| nivå |Nivån på händelsen. Något av följande värden: ”kritisk”, ”Error”, ”varning” och ”information” |
| resourceGroupName |Namnet på resursgruppen för autoskalningsinställningen. |
| resourceprovidername får |Namnet på resursprovidern för autoskalningsinställningen. |
| resourceId |Resurs-ID för autoskalningsinställningen. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Åtgärdens namn. |
| properties |Uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver informationen om händelsen. |
| Egenskaper. Beskrivning | Detaljerad beskrivning av vad Autoskala motorn gjorde. |
| Egenskaper. ResourceName | Resurs-ID för resursen som påverkas (den resurs där skalningsåtgärden utfördes) |
| Egenskaper. OldInstancesCount | Antalet instanser innan åtgärden för automatisk skalning tog effekt. |
| Egenskaper. NewInstancesCount | Antalet instanser när autoskalning åtgärden tog effekt. |
| Egenskaper. LastScaleActionTime | Tidsstämpel för när åtgärden för automatisk skalning inträffade. |
| status |Sträng som anger status för åtgärden. Vissa vanliga värden är: igång i pågår, slutfört, misslyckades, aktiva, löst. |
| understatus | Vanligtvis null för automatisk skalning. |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure behandlingen av begäran som motsvarande händelsen. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure-prenumerations-ID. |

## <a name="security"></a>Säkerhet
Den här kategorin innehåller posten några aviseringar som genereras av Azure Security Center. Ett exempel på typen av händelse som du ser i den här kategorin är ”misstänkt dubbelt filnamnstillägg fil körs”.

### <a name="sample-event"></a>Exempelhändelse
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| kanaler | Alltid ”åtgärden” |
| correlationId | Ett GUID i formatet för strängen. |
| beskrivning |Statisk textbeskrivning av säkerhetshändelsen. |
| eventDataId |Unik identifierare för säkerhetshändelsen. |
| EventName |Eget namn på säkerhetshändelsen. |
| id |Unikt resurs-ID för säkerhetshändelsen. |
| nivå |Nivån på händelsen. Något av följande värden: ”kritisk”, ”Error”, ”varning” eller ”information” |
| resourceGroupName |Namnet på resursgruppen för resursen. |
| resourceprovidername får |Namnet på resursprovidern för Azure Security Center. Alltid ”Microsoft.Security”. |
| ResourceType |Typ av resurs som genererade säkerhetshändelser, till exempel ”Microsoft.Security/locations/alerts” |
| resourceId |Resurs-ID för säkerhetsaviseringen. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Åtgärdens namn. |
| properties |Uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver informationen om händelsen. De här egenskaperna varierar beroende på vilken typ av säkerhetsavisering. Se [den här sidan](../security-center/security-center-alerts-type.md) en beskrivning av typerna av aviseringar som kommer från Security Center. |
| Egenskaper. Allvarlighetsgrad |Allvarlighetsgrad. Möjliga värden är ”hög”, ”medel” eller ”låg”. |
| status |Sträng som anger status för åtgärden. Vissa vanliga värden är: igång i pågår, slutfört, misslyckades, aktiva, löst. |
| understatus | Vanligtvis null för säkerhetshändelser. |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure behandlingen av begäran som motsvarande händelsen. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure-prenumerations-ID. |

## <a name="recommendation"></a>Rekommendation
Den här kategorin innehåller en post för alla nya rekommendationer som har genererats för dina tjänster. Ett exempel på en rekommendation är ”Använd tillgänglighetsuppsättningar för ökad feltolerans”. Det finns 4 typer av rekommendationshändelser som kan genereras: hög tillgänglighet, prestanda, säkerhet och Kostnadsoptimeringar. 

### <a name="sample-event"></a>Exempelhändelse
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| kanaler | Alltid ”åtgärden” |
| correlationId | Ett GUID i formatet för strängen. |
| beskrivning |Statisk textbeskrivning av händelsen rekommendation |
| eventDataId | Unik identifierare för händelsen rekommendation. |
| category | Alltid ”Recommendation” |
| id |Unikt resurs-ID för händelsen rekommendation. |
| nivå |Nivån på händelsen. Något av följande värden: ”kritisk”, ”Error”, ”varning” eller ”information” |
| operationName |Åtgärdens namn.  Alltid ”Microsoft.Advisor/generateRecommendations/action”|
| resourceGroupName |Namnet på resursgruppen för resursen. |
| resourceprovidername får |Namnet på resursprovidern för den resurs som den här rekommendationen gäller, till exempel ”MICROSOFT.COMPUTE” |
| ResourceType |Namnet på resurstypen för den resurs som den här rekommendationen gäller, till exempel ”MICROSOFT.COMPUTE/virtualmachines” |
| resourceId |Resurs-ID för den resurs som rekommendationen gäller för |
| status | Alltid ”aktiv” |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure-prenumerations-ID. |
| properties |Uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver information om rekommendationen.|
| properties.recommendationSchemaVersion| Schemaversion av egenskaper för rekommendationen som publicerats i aktiviteten logg |
| properties.recommendationCategory | Kategori för rekommendationen. Möjliga värden är ”hög tillgänglighet”, ”prestanda”, ”säkerhet” och ”Cost” |
| properties.recommendationImpact| Påverkan. Möjliga värden är ”hög”, ”medel”, ”låg” |
| properties.recommendationRisk| Risken för rekommendationen. Möjliga värden är ”fel”, ”varning”, ”None” |

## <a name="mapping-to-diagnostic-logs-schema"></a>Mappa till diagnostikloggar schema

Vid direktuppspelning av Azure-aktivitetsloggen till ett lagringskonto eller Event Hubs-namnområdet, data följer den [Azure diagnostisk loggar schemat](./monitoring-diagnostic-logs-schema.md). Här är mappningen av egenskaper från schemat ovan i diagnostikloggar schemat:

| Diagnostikloggar schemaegenskap | Aktivitetsegenskap Log REST API-schemat | Anteckningar |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | prenumerations-ID, resourceType, resourceGroupName är härledd från resourceId. |
| operationName | operationName.value |  |
| category | En del av åtgärdens namn | Nedbrytning av åtgärdstypen – ”Skriv-” / ”ta bort” / ”Action” |
| resultType | status.Value | |
| resultSignature | substatus.Value | |
| resultDescription | beskrivning |  |
| durationMs | Gäller inte | Alltid 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | anspråk och egenskaper för auktorisering |  |
| Nivå | Nivå |  |
| location | Gäller inte | Platsen för där händelsen bearbetades. *Detta är inte platsen för resursen, men i stället där händelsen bearbetades. Den här egenskapen tas bort i en kommande uppdatering.* |
| Egenskaper | properties.eventProperties |  |
| properties.eventCategory | category | Om det inte finns någon properties.eventCategory, är kategorin ”administrativa” |
| properties.eventName | EventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>Nästa steg
* [Läs mer om aktivitetsloggen (tidigare granskningsloggar)](monitoring-overview-activity-logs.md)
* [Stream Azure-aktivitetsloggen till Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
