---
title: Händelseschema för Azure Activity Log
description: Beskriver händelseschemat för varje kategori i Azure Activity-loggen.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 12/04/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c2f171c79423e0cfe8b57c05b8248679f9ada9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472749"
---
# <a name="azure-activity-log-event-schema"></a>Händelseschema för Azure Activity Log
[Azure-aktivitetsloggen](platform-logs-overview.md) ger insikt i alla händelser på prenumerationsnivå som har inträffat i Azure. I den här artikeln beskrivs händelseschemat för varje kategori. 

Exemplen nedan visar schemat när du öppnar aktivitetsloggen från portalen, PowerShell, CLI och REST API. Schemat är annorlunda när du [streamar aktivitetsloggen till lagring eller Event Hubs](resource-logs-stream-event-hubs.md). En mappning av egenskaperna till [resursloggschemat](diagnostic-logs-schema.md) finns i slutet av artikeln.

## <a name="administrative"></a>Administrativ
Den här kategorin innehåller posten för alla åtgärder för att skapa, uppdatera, ta bort och vidta åtgärder som utförs via Resource Manager. Exempel på de typer av händelser som visas i den här kategorin är "skapa virtuell dator" och "ta bort nätverkssäkerhetsgrupp" Varje åtgärd som vidtas av en användare eller ett program som använder Resource Manager modelleras som en åtgärd på en viss resurstyp. Om operationstypen är Skriv, Ta bort eller Åtgärd registreras posterna för både start och lyckad eller misslyckad åtgärd i kategorin Administration. Kategorin Administration innehåller även alla ändringar av rollbaserad åtkomstkontroll i en prenumeration.

### <a name="sample-event"></a>Exempel på händelse
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
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
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

### <a name="property-descriptions"></a>Fastighetsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| auktorisering |Blob av RBAC-egenskaper för händelsen. Innehåller vanligtvis egenskaperna "åtgärd", "roll" och "scope". |
| Ringer |E-postadress till den användare som har utfört åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. |
| Kanaler |Ett av följande värden: "Admin", "Operation" |
| Hävdar |JWT-token som används av Active Directory för att autentisera användaren eller programmet för att utföra den här åtgärden i Resource Manager. |
| correlationId |Vanligtvis ett GUID i strängformat. Händelser som delar en correlationId tillhör samma uber-åtgärd. |
| description |Statisk textbeskrivning av en händelse. |
| eventDataId |Unik identifierare för en händelse. |
| händelseNamn | Eget namn på det administrativa evenemanget. |
| category | Alltid "administrativ" |
| httpRequest (på) |Blob som beskriver http-begäran. Innehåller vanligtvis "clientRequestId", "clientIpAddress" och "method" (HTTP-metoden. Till exempel PUT). |
| nivå |Nivå av händelsen. Ett av följande värden: "Kritiskt", "Fel", "Varning" och "Informativt" |
| resourceGroupName |Namn på resursgruppen för den påverkade resursen. |
| resursProviderNamn |Namn på resursprovidern för den påverkade resursen |
| resourceType | Den typ av resurs som påverkades av en administrativ händelse. |
| resourceId |Resurs-ID för den påverkade resursen. |
| operationId |Ett GUID delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Operationens namn. |
| properties |Uppsättning `<Key, Value>` par (det villika på en ordlista) som beskriver information om händelsen. |
| status |Sträng som beskriver åtgärdens status. Några vanliga värden är: Startat, Pågår, lyckades, Misslyckades, Aktiv, Löst. |
| understatus |Vanligtvis HTTP-statuskoden för motsvarande REST-anrop, men kan även innehålla andra strängar som beskriver en understatus, till exempel dessa vanliga värden: OK (HTTP-statuskod: 200), Skapad (HTTP-statuskod: 201), Godkänd (HTTP-statuskod: 202), Inget innehåll (HTTP-status Kod: 204), felaktig begäran (HTTP-statuskod: 400), hittades inte (HTTP-statuskod: 404), Konflikt (HTTP-statuskod: 409), internt serverfel (HTTP-statuskod: 500), Tjänsten är inte tillgänglig (HTTP-statuskod: 503), timeout för gateway (HTTP-statuskod: 504). |
| händelseTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| inlämningTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |Azure-prenumerations-ID. |

## <a name="service-health"></a>Service Health:
Den här kategorin innehåller en post för alla tjänsthälsoincidenter som har inträffat i Azure. Ett exempel på den typ av händelse som du skulle se i den här kategorin är "SQL Azure i östra USA upplever driftstopp." Hälsohändelser för tjänsten finns i fem varianter: Åtgärd krävs, Assisterad återställning, Incident, Underhåll, Information eller Säkerhet, och visas bara om du har en resurs i prenumerationen som skulle påverkas av händelsen.

### <a name="sample-event"></a>Exempel på händelse
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
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
I artikeln [servicehälsomeddelanden](./../../azure-monitor/platform/service-notifications.md) finns dokumentation om värdena i egenskaperna.

## <a name="resource-health"></a>Resurshälsa
Den här kategorin innehåller en post för alla resurshälsohändelser som har inträffat till dina Azure-resurser. Ett exempel på vilken typ av händelse som visas i den här kategorin är "Hälsostatus för virtuell dator har ändrats till otillgänglig". Resurshälsohändelser kan representera en av fyra hälsotillstånd: Tillgängliga, ej tillgängliga, försämrade och okända. Dessutom kan resurshälsohändelser kategoriseras som plattformsinitierade eller användarinitierade.

### <a name="sample-event"></a>Exempel på händelse

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
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
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
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
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

### <a name="property-descriptions"></a>Fastighetsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Kanaler | Alltid "Admin, Operation" |
| correlationId | Ett GUID i strängformat. |
| description |Statisk textbeskrivning av aviseringshändelsen. |
| eventDataId |Unik identifierare för aviseringshändelsen. |
| category | Alltid "ResourceHealth" |
| händelseTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| nivå |Nivå av händelsen. Ett av följande värden: "Kritiskt", "Fel", "Varning", "Informativt" och "Utförligt" |
| operationId |Ett GUID delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Operationens namn. |
| resourceGroupName |Namn på resursgruppen som innehåller resursen. |
| resursProviderNamn |Alltid "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | Den typ av resurs som påverkades av en resource health-händelse. |
| resourceId | Namn på resurs-ID för den påverkade resursen. |
| status |Sträng som beskriver hälsohändelsens status. Värden kan vara: Aktiv, Löst, InProgress, Uppdaterad. |
| understatus | Vanligtvis null för aviseringar. |
| inlämningTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |Azure-prenumerations-ID. |
| properties |Uppsättning `<Key, Value>` par (det villika på en ordlista) som beskriver information om händelsen.|
| egenskaper.titel | En användarvänlig sträng som beskriver resursens hälsostatus. |
| egenskaper.detaljer | En användarvänlig sträng som beskriver ytterligare information om händelsen. |
| egenskaper.aktuellHälsostatus | Resursens aktuella hälsostatus. Ett av följande värden: "Tillgänglig", "Ej tillgänglig", "Degraderad" och "Okänd". |
| egenskaper.föregåendeHälsostatus | Resursens tidigare hälsostatus. Ett av följande värden: "Tillgänglig", "Ej tillgänglig", "Degraderad" och "Okänd". |
| egenskaper.typ | En beskrivning av typen av resurshälsohändelse. |
| egenskaper.orsak | En beskrivning av orsaken till resurshälsohändelsen. Antingen "UserInitiated" och "PlatformInitiated". |


## <a name="alert"></a>Varning
Den här kategorin innehåller posten för alla aktiveringar av Azure-aviseringar. Ett exempel på den typ av händelse som du skulle se i den här kategorin är "CPU % på myVM har varit över 80 under de senaste 5 minuterna." En mängd olika Azure-system har ett aviseringskoncept – du kan definiera en regel av något slag och få ett meddelande när villkoren matchar den regeln. Varje gång en Azure-aviseringstyp som stöds aktiveras eller villkoren är uppfyllda för att generera ett meddelande, skickas en post av aktiveringen också till den här kategorin i aktivitetsloggen.

### <a name="sample-event"></a>Exempel på händelse

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

### <a name="property-descriptions"></a>Fastighetsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Ringer | Alltid Microsoft.Insights/alertRules |
| Kanaler | Alltid "Admin, Operation" |
| Hävdar | JSON-blob med SPN (tjänstens huvudnamn) eller resurstyp för aviseringsmotorn. |
| correlationId | Ett GUID i strängformat. |
| description |Statisk textbeskrivning av aviseringshändelsen. |
| eventDataId |Unik identifierare för aviseringshändelsen. |
| category | Alltid "Alert" |
| nivå |Nivå av händelsen. Ett av följande värden: "Kritiskt", "Fel", "Varning" och "Informativt" |
| resourceGroupName |Namn på resursgruppen för den påverkade resursen om det är en måttavisering. För andra aviseringstyper är det namnet på resursgruppen som innehåller själva aviseringen. |
| resursProviderNamn |Namn på resursprovidern för den påverkade resursen om det är en måttavisering. För andra aviseringstyper är det namnet på resursprovidern för själva aviseringen. |
| resourceId | Namn på resurs-ID för den påverkade resursen om det är en måttavisering. För andra aviseringstyper är det resurs-ID för själva aviseringsresursen. |
| operationId |Ett GUID delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Operationens namn. |
| properties |Uppsättning `<Key, Value>` par (det villika på en ordlista) som beskriver information om händelsen. |
| status |Sträng som beskriver åtgärdens status. Några vanliga värden är: Startat, Pågår, lyckades, Misslyckades, Aktiv, Löst. |
| understatus | Vanligtvis null för aviseringar. |
| händelseTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| inlämningTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |Azure-prenumerations-ID. |

### <a name="properties-field-per-alert-type"></a>Egenskapsfält per aviseringstyp
Egenskapsfältet innehåller olika värden beroende på källan till aviseringshändelsen. Två vanliga aviseringshändelseleverantörer är aktivitetsloggaviseringar och måttaviseringar.

#### <a name="properties-for-activity-log-alerts"></a>Egenskaper för aktivitetsloggaviseringar
| Elementnamn | Beskrivning |
| --- | --- |
| properties.subscriptionId | Prenumerations-ID:t från aktivitetslogghändelsen som orsakade att den här aktivitetsloggaviseringsregeln aktiverades. |
| egenskaper.eventDataId | Händelsedata-ID:t från aktivitetslogghändelsen som orsakade att den här aktivitetsloggaviseringsregeln aktiverades. |
| egenskaper.resourceGroup | Resursgruppen från aktivitetslogghändelsen som orsakade att den här aktivitetsloggaviseringsregeln aktiverades. |
| egenskaper.resourceId | Resurs-ID:t från aktivitetslogghändelsen som orsakade att den här aktivitetsloggaviseringsregeln aktiverades. |
| egenskaper.eventTimestamp | Händelsetidsstämpeln för aktivitetslogghändelsen som orsakade att den här aktivitetsloggaviseringsregeln aktiverades. |
| egenskaper.operationName | Åtgärdsnamnet från aktivitetslogghändelsen som orsakade att den här aktivitetsloggaviseringsregeln aktiverades. |
| egenskaper.status | Statusen från aktivitetslogghändelsen som orsakade att den här aktivitetsloggaviseringsregeln aktiverades.|

#### <a name="properties-for-metric-alerts"></a>Egenskaper för måttaviseringar
| Elementnamn | Beskrivning |
| --- | --- |
| Egenskaper. RuleUri (härska) | Resurs-ID för själva måttaviseringsregeln. |
| Egenskaper. Regelnamn | Namnet på måttaviseringsregeln. |
| Egenskaper. Regelbeskrivning | Beskrivningen av måttvarningsregeln (enligt definitionen i varningsregeln). |
| Egenskaper. Tröskel | Tröskelvärdet som används vid utvärderingen av måttaviseringsregeln. |
| Egenskaper. FönsterStorlekInMinuter | Fönsterstorleken som används i utvärderingen av måttaviseringsregeln. |
| Egenskaper. Aggregation | Aggregeringstypen som definieras i måttvarningsregeln. |
| Egenskaper. Operatör | Den villkorliga operatorn som används vid utvärderingen av måttaviseringsregeln. |
| Egenskaper. MetricName (Mätnamn) | Måttnamnet för måttet som används i utvärderingen av måttvarningsregeln. |
| Egenskaper. MetricUnit (metriska) | Måttenheten för måttet som används i utvärderingen av måttvarningsregeln. |

## <a name="autoscale"></a>Automatisk skalning
Den här kategorin innehåller en post för alla händelser som är relaterade till hur motorn för automatisk skalning fungerar baserat på alla inställningar för automatisk skalning som du har definierat i din prenumeration. Ett exempel på den typ av händelse som visas i den här kategorin är "Automatisk skala upp åtgärden misslyckades." Med automatisk skalning kan du automatiskt skala ut eller skala in antalet instanser i en resurstyp som stöds baserat på tid på dagen och/eller läsa in (mått) data med hjälp av en inställning för automatisk skalning. När villkoren är uppfyllda för att skala upp eller ned registreras start- och efterföljande eller misslyckade händelser i den här kategorin.

### <a name="sample-event"></a>Exempel på händelse
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

### <a name="property-descriptions"></a>Fastighetsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Ringer | Alltid Microsoft.Insights/autoscaleSettings |
| Kanaler | Alltid "Admin, Operation" |
| Hävdar | JSON-blob med SPN (tjänstens huvudnamn) eller resurstyp för motorn för automatisk skalning. |
| correlationId | Ett GUID i strängformat. |
| description |Statisk textbeskrivning av händelsen för automatisk skalning. |
| eventDataId |Unik identifierare för händelsen för automatisk skalning. |
| nivå |Nivå av händelsen. Ett av följande värden: "Kritiskt", "Fel", "Varning" och "Informativt" |
| resourceGroupName |Namn på resursgruppen för inställningen för automatisk skalning. |
| resursProviderNamn |Namn på resursprovidern för inställningen för automatisk skalning. |
| resourceId |Resurs-ID för inställningen för automatisk skalning. |
| operationId |Ett GUID delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Operationens namn. |
| properties |Uppsättning `<Key, Value>` par (det villika på en ordlista) som beskriver information om händelsen. |
| Egenskaper. Beskrivning | Detaljerad beskrivning av vad autoskala motorn gjorde. |
| Egenskaper. Resursnamn | Resurs-ID för den påverkade resursen (den resurs som skalningsåtgärden utfördes på) |
| Egenskaper. OldInstancesCount | Antalet instanser innan åtgärden för automatisk skalning trädde i kraft. |
| Egenskaper. NewInstancesCount | Antalet instanser efter att åtgärden för automatisk skalning trädde i kraft. |
| Egenskaper. Senaste skalartid | Tidsstämpeln för när åtgärden automatisk skalning inträffade. |
| status |Sträng som beskriver åtgärdens status. Några vanliga värden är: Startat, Pågår, lyckades, Misslyckades, Aktiv, Löst. |
| understatus | Vanligtvis null för automatisk skalning. |
| händelseTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| inlämningTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |Azure-prenumerations-ID. |

## <a name="security"></a>Säkerhet
Den här kategorin innehåller posten alla aviseringar som genereras av Azure Security Center. Ett exempel på vilken typ av händelse du skulle se i den här kategorin är "Misstänkt dubbel förlängning fil körs."

### <a name="sample-event"></a>Exempel på händelse
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

### <a name="property-descriptions"></a>Fastighetsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Kanaler | Alltid "Operation" |
| correlationId | Ett GUID i strängformat. |
| description |Statisk textbeskrivning av säkerhetshändelsen. |
| eventDataId |Unik identifierare för säkerhetshändelsen. |
| händelseNamn |Eget namn på säkerhetshändelsen. |
| category | Alltid "Säkerhet" |
| ID |Unik resursidentifierare för säkerhetshändelsen. |
| nivå |Nivå av händelsen. Ett av följande värden: "Kritiskt", "Fel", "Varning" eller "Informativt" |
| resourceGroupName |Namn på resursgruppen för resursen. |
| resursProviderNamn |Namn på resursprovidern för Azure Security Center. Alltid "Microsoft.Security". |
| resourceType |Den typ av resurs som genererade säkerhetshändelsen, till exempel "Microsoft.Security/locations/alerts" |
| resourceId |Resurs-ID för säkerhetsaviseringen. |
| operationId |Ett GUID delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Operationens namn. |
| properties |Uppsättning `<Key, Value>` par (det villika på en ordlista) som beskriver information om händelsen. Dessa egenskaper varierar beroende på vilken typ av säkerhetsvarning. Se [den här sidan](../../security-center/security-center-alerts-overview.md) för en beskrivning av de typer av aviseringar som kommer från Security Center. |
| Egenskaper. Svårighetsgrad |Allvarlighetsgraden. Möjliga värden är "Hög", "Medel" eller "Låg". |
| status |Sträng som beskriver åtgärdens status. Några vanliga värden är: Startat, Pågår, lyckades, Misslyckades, Aktiv, Löst. |
| understatus | Vanligtvis null för säkerhetshändelser. |
| händelseTimestamp |Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| inlämningTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |Azure-prenumerations-ID. |

## <a name="recommendation"></a>Rekommendation
Den här kategorin innehåller en post för alla nya rekommendationer som genereras för dina tjänster. Ett exempel på en rekommendation är "Använd tillgänglighetsuppsättningar för förbättrad feltolerans.", Det finns fyra typer av rekommendationshändelser som kan genereras: Hög tillgänglighet, Prestanda, Säkerhet och Kostnadsoptimering. 

### <a name="sample-event"></a>Exempel på händelse
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
### <a name="property-descriptions"></a>Fastighetsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Kanaler | Alltid "Operation" |
| correlationId | Ett GUID i strängformat. |
| description |Statisk textbeskrivning av rekommendationshändelsen |
| eventDataId | Unik identifierare för rekommendationshändelsen. |
| category | Alltid "Rekommendation" |
| ID |Unik resursidentifierare för rekommendationshändelsen. |
| nivå |Nivå av händelsen. Ett av följande värden: "Kritiskt", "Fel", "Varning" eller "Informativt" |
| operationName |Operationens namn.  Alltid "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Namn på resursgruppen för resursen. |
| resursProviderNamn |Namnet på resursprovidern för resursen som den här rekommendationen gäller för, till exempel "MICROSOFT.COMPUTE" |
| resourceType |Namn på resurstypen för resursen som den här rekommendationen gäller för, till exempel "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |Resurs-ID för den resurs som rekommendationen gäller för |
| status | Alltid "aktiv" |
| inlämningTimestamp |Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId |Azure-prenumerations-ID. |
| properties |Uppsättning `<Key, Value>` par (det vill vara en ordlista) som beskriver detaljerna i rekommendationen.|
| properties.recommendationSchemaVersion| Schemaversion av rekommendationsegenskaperna som publicerats i posten Aktivitetslogg |
| egenskaper.rekommendationKategori | Kategori av rekommendationen. Möjliga värden är "Hög tillgänglighet", "Prestanda", "Säkerhet" och "Kostnad" |
| properties.recommendationImpact| Effekten av rekommendationen. Möjliga värden är "Hög", "Medium", "Låg" |
| properties.recommendationRisk| Risk för rekommendationen. Möjliga värden är "Fel", "Varning", "Ingen" |

## <a name="policy"></a>Princip

Den här kategorin innehåller poster för alla effektåtgärdsåtgärder som utförs av [Azure Policy](../../governance/policy/overview.md). Exempel på de typer av händelser som visas i den här kategorin är _Granskning_ och _Neka_. Varje åtgärd som vidtas av principen modelleras som en åtgärd på en resurs.

### <a name="sample-policy-event"></a>Exempel på principhändelse

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Beskrivningar av egenskapsbeskrivningar för principhändelse

| Elementnamn | Beskrivning |
| --- | --- |
| auktorisering | Matris med RBAC-egenskaper för händelsen. För nya resurser är detta åtgärden och omfattningen av den begäran som utlöste utvärderingen. För befintliga resurser är åtgärden "Microsoft.Resources/checkPolicyCompliance/read". |
| Ringer | För nya resurser, identiteten som initierade en distribution. För befintliga resurser är GUID för Microsoft Azure Policy Insights RP. |
| Kanaler | Principhändelser använder endast kanalen "Operation". |
| Hävdar | JWT-token som används av Active Directory för att autentisera användaren eller programmet för att utföra den här åtgärden i Resource Manager. |
| correlationId | Vanligtvis ett GUID i strängformat. Händelser som delar en correlationId tillhör samma uber-åtgärd. |
| description | Det här fältet är tomt för principhändelser. |
| eventDataId | Unik identifierare för en händelse. |
| händelseNamn | Antingen "BeginRequest" eller "EndRequest". "BeginRequest" används för fördröjd granskningOmutexister och deployIfNotExists utvärderingar och när en deployIfNotExists-effekt startar en malldistribution. Alla andra operationer returnerar "EndRequest". |
| category | Deklarerar aktivitetslogghändelsen som tillhörande "Policy". |
| händelseTimestamp | Tidsstämpel när händelsen genererades av Azure-tjänsten som bearbetar begäran som motsvarar händelsen. |
| ID | Unik identifierare för händelsen på den specifika resursen. |
| nivå | Nivå av händelsen. Granskning använder "Varning" och Neka använder "Fel". En auditIfNotExists eller deployIfNotExists fel kan generera "Varning" eller "Fel" beroende på allvarlighetsgrad. Alla andra policyhändelser använder "Informational". |
| operationId | Ett GUID delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName | Namnet på åtgärden och korrelerar direkt till effekten Princip. |
| resourceGroupName | Namn på resursgruppen för den utvärderade resursen. |
| resursProviderNamn | Namn på resursprovidern för den utvärderade resursen. |
| resourceType | För nya resurser är det den typ som utvärderas. För befintliga resurser returnerar du "Microsoft.Resources/checkPolicyCompliance". |
| resourceId | Resurs-ID för den utvärderade resursen. |
| status | Sträng som beskriver status för resultatet för principutvärdering. De flesta principutvärderingar returnerar "Lyckades", men en nekad effekt returnerar "Misslyckades". Fel i auditIfNotExists eller deployIfNotExists returnerar också "Misslyckades". |
| understatus | Fältet är tomt för principhändelser. |
| inlämningTimestamp | Tidsstämpel när händelsen blev tillgänglig för frågor. |
| subscriptionId | Azure-prenumerations-ID. |
| egenskaper.isComplianceCheck | Returnerar "False" när en ny resurs distribueras eller en befintlig resurs Resource Manager-egenskaper uppdateras. Alla andra [utvärderingsutlösare resulterar](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) i "True". |
| egenskaper.resourceLocation | Azure-regionen för den resurs som utvärderas. |
| properties.förfäder | En kommaavgränsad lista över överordnade hanteringsgrupper som beställts från direkt förälder till farförälder. |
| egenskaper.principer | Innehåller information om principdefinition, tilldelning, effekt och parametrar som den här principutvärderingen är ett resultat av. |
| relateradeHändelser | Det här fältet är tomt för principhändelser. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Schema från lagringskonto och händelsehubbar
När du streamar Azure Activity-loggen till ett lagringskonto eller en händelsenav följer data [resursloggschemat](diagnostic-logs-schema.md). Tabellen nedan innehåller en mappning av egenskaper från schemat ovan till resursloggschemat.

> [!IMPORTANT]
> Formatet på aktivitetsloggdata som skrivits till ett lagringskonto ändrades till JSON Lines den 1 november 2018. Se [Förbereda för formatändring till Azure Monitor-resursloggar som arkiverats i ett lagringskonto](diagnostic-logs-append-blobs.md) för mer information om den här formatändringen.


| Egenskapen schema för resursloggar | Egenskapen REST API-schema för aktivitetslogg | Anteckningar |
| --- | --- | --- |
| time | händelseTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType, resourceGroupName härledas alla från resourceId. |
| operationName | operationName.värde |  |
| category | Del av operationsnamnet | Breakout av operationstypen - "Skriv"/"Ta bort"/"Åtgärd" |
| resultType | status.värde | |
| resultSignature | understatus.värde | |
| resultDescription | description |  |
| durationMs | Ej tillämpligt | Alltid 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | anspråk och auktoriseringsegenskaper |  |
| Nivå | Nivå |  |
| location | Ej tillämpligt | Plats för var händelsen bearbetades. *Det här är inte resursens plats, utan var händelsen bearbetades. Den här egenskapen tas bort i en framtida uppdatering.* |
| Egenskaper | egenskaper.eventEgenskaper |  |
| egenskaper.eventKategori | category | Om properties.eventCategory inte finns är kategorin "Administrativ" |
| egenskaper.eventName | händelseNamn |  |
| egenskaper.operationId | operationId |  |
| egenskaper.eventEgenskaper | properties |  |

Följande är ett exempel på en händelse med det här schemat..

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Nästa steg
* [Läs mer om aktivitetsloggen](platform-logs-overview.md)
* [Skapa en diagnostikinställning för att skicka aktivitetslogg till Log Analytics-arbetsyta, Azure-lagring eller händelsehubbar](diagnostic-settings.md)

