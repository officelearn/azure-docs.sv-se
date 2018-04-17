---
title: Azure händelse för aktivitetslogg | Microsoft Docs
description: Förstå Händelseschema för data som sänds till aktivitetsloggen
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2018
ms.author: dukek
ms.openlocfilehash: 4264bfd733f586dcdabdee8f29494bfffd9a7a76
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-activity-log-event-schema"></a>Azure aktivitetsloggen Händelseschema
Den **Azure-aktivitetsloggen** är en logg som ger inblick i prenumerationsnivån händelser som har inträffat i Azure. Den här artikeln beskriver Händelseschema per kategori av data.

## <a name="administrative"></a>Administrativ
Den här kategorin innehåller posten för alla skapa, uppdatera, ta bort och åtgärden åtgärder utföras via Resource Manager. Exempel på vilka typer av händelser som visas i den här kategorin är ”Skapa virtuell dator” och ”ta bort nätverkssäkerhetsgrupp” varje åtgärd som en användare eller program med hjälp av hanteraren för filserverresurser modelleras som en åtgärd på en viss resurstyp. Om åtgärdstypen är Write-, Delete- eller åtgärd, är start-och lyckas eller misslyckas för denna åtgärd registrerade i den administrativa kategorin. Den administrativa kategorin omfattar även ändringar rollbaserad åtkomstkontroll i en prenumeration.

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
| Auktorisering |BLOB RBAC egenskaper för händelsen. Inkluderar vanligtvis egenskaperna ””, ”roll” och ”omfattning”. |
| uppringare |E-postadressen för användaren som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. |
| kanaler |Ett av följande värden: ”Admin”, ”åtgärden” |
| Anspråk |Detta JWT-token som används av Active Directory för att autentisera användaren eller programmet att utföra åtgärden i hanteraren för filserverresurser. |
| correlationId |Vanligtvis ett GUID i strängformatet. Händelser som delar en correlationId tillhöra samma uber åtgärd. |
| description |Statisk textbeskrivning av en händelse. |
| eventDataId |Unik identifierare för en händelse. |
| httpRequest |BLOB som beskriver Http-begäran. Inkluderar vanligtvis ”clientRequestId”, ”clientIpAddress” och ”metod” (HTTP-metod. Till exempel PLACERA). |
| nivå |Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig” |
| resourceGroupName |Namnet på resursgruppen för resursen påverkas. |
| resourceProviderName |Namnet på resursprovidern för resursen påverkas |
| resourceId |Resurs-id för resursen påverkas. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Namnet på åtgärden. |
| properties |En uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver information om händelsen. |
| status |Sträng som beskriver status för åtgärden. Vissa vanliga värden: startas i pågår, slutfört, misslyckades, aktiv, löst. |
| subStatus |Vanligtvis HTTP-statuskoden motsvarande rest anropa, men kan även innehålla andra strängar som beskriver en sådan, till exempel värdena vanliga: OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inte innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400), det gick inte att hitta (HTTP-statuskod: 404), konflikt (HTTP-statuskod : 409), internt serverfel (HTTP-statuskod: 500), tjänsten inte tillgänglig (HTTP-statuskod: 503), Gateway-Timeout (HTTP-statuskod: 504). |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure motsvarande händelsen begäran bearbetades. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure prenumerations-Id. |

## <a name="service-health"></a>Service Health
Den här kategorin innehåller post för varje service hälsa incidenter som har inträffat i Azure. Ett exempel på typen av händelse visas i den här kategorin är ”SQL Azure i östra USA upplever driftstopp”. Hälsa av tjänsten-händelser finns i fem sorter: åtgärd krävs stödd återställning, Incident, underhåll, Information eller säkerhet, och visas endast om du har en resurs i den prenumeration som skulle påverkas av händelsen.

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
Referera till den [tjänsten meddelanden om hälsostatus](./monitoring-service-notifications.md) artikel dokumentation om värdena i egenskaperna.

## <a name="alert"></a>Varning
Den här kategorin innehåller posten för alla aktiveringar av Azure-aviseringar. Ett exempel på typen av händelse visas i den här kategorin är ”processor på myVM har varit över 80 under de senaste fem minuterna”. En mängd Azure system har en aviseringar begrepp – du kan definiera en regel av något slag och ett meddelande när villkor matchar regeln. Varje gång en stöds Azure aviseringstyp 'aktiveras,' eller villkoren är uppfyllda för att generera ett meddelande, en post på aktiveringen skickas även till den här kategorin för aktivitetsloggen.

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
| kanaler | Alltid ”Admin, åtgärden” |
| Anspråk | JSON-blob med typen SPN (service principal name) eller resurs varning-motorn. |
| correlationId | Ett GUID i strängformatet. |
| description |Statisk textbeskrivning av händelsen avisering. |
| eventDataId |Unik identifierare för varning avisering. |
| nivå |Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig” |
| resourceGroupName |Namnet på resursgruppen för resursen påverkas om det är en avisering om mått. För andra aviseringstyper är namnet på resursgruppen som innehåller aviseringen sig själv. |
| resourceProviderName |Namnet på resursprovidern för resursen påverkas om det är en avisering om mått. För andra aviseringstyper kan är det här namnet på resursprovidern för aviseringen sig själv. |
| resourceId | Namnet på resurs-ID för resursen påverkas om det är en avisering om mått. För andra aviseringstyper är resurs-ID för avisering resursen sig själv. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Namnet på åtgärden. |
| properties |En uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver information om händelsen. |
| status |Sträng som beskriver status för åtgärden. Vissa vanliga värden: startas i pågår, slutfört, misslyckades, aktiv, löst. |
| subStatus | Vanligtvis är null för aviseringar. |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure motsvarande händelsen begäran bearbetades. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure prenumerations-Id. |

### <a name="properties-field-per-alert-type"></a>Egenskapsfältet per typ av avisering
Egenskapsfältet innehåller olika värden beroende på aviseringen händelsens källa. Två vanliga varning avisering providers är aktivitetsloggen aviseringar och mått aviseringar.

#### <a name="properties-for-activity-log-alerts"></a>Egenskaper för aktivitetsloggen aviseringar
| Elementnamn | Beskrivning |
| --- | --- |
| properties.subscriptionId | Prenumerations-ID från den aktivitet logga en händelse som orsakat den här aktiviteten loggen varningsregeln ska aktiveras. |
| properties.eventDataId | Händelse data-ID från den aktivitet logga en händelse som orsakat den här aktiviteten loggen varningsregeln ska aktiveras. |
| properties.resourceGroup | Resursgruppen från den aktivitet logga en händelse som orsakat den här aktiviteten loggen varningsregeln ska aktiveras. |
| properties.resourceId | Resurs-ID från den aktivitet logga en händelse som orsakat den här aktiviteten loggen varningsregeln ska aktiveras. |
| properties.eventTimestamp | Händelsen tidsstämpel för den aktiviteten logga en händelse som orsakat den här aktiviteten loggen varningsregeln ska aktiveras. |
| properties.operationName | Åtgärdsnamnet från den aktivitet logga en händelse som orsakat den här aktiviteten loggen varningsregeln ska aktiveras. |
| Properties.status | Status från den aktivitet logga en händelse som orsakat den här aktiviteten loggen varningsregeln ska aktiveras.|

#### <a name="properties-for-metric-alerts"></a>Egenskaper för mått aviseringar
| Elementnamn | Beskrivning |
| --- | --- |
| Egenskaper. RuleUri | Resurs-ID för mått varningsregeln sig själv. |
| Egenskaper. Regelnamn | Namnet på regeln mått. |
| Egenskaper. RuleDescription | Beskrivning av mått varningsregeln (enligt definitionen i varningsregeln). |
| Egenskaper. Tröskelvärde | Tröskelvärdet används i utvärderingen av mått varningsregeln. |
| Egenskaper. WindowSizeInMinutes | Fönsterstorleken användas vid utvärderingen av mått varningsregeln. |
| Egenskaper. Aggregeringen | Sammansättningstyp som definierats i regeln mått. |
| Egenskaper. Operatorn | Villkorsstyrd operatör som används i utvärderingen av mått varningsregeln. |
| Egenskaper. MetricName | Måttnamnet av måttet används i utvärderingen av mått varningsregeln. |
| Egenskaper. MetricUnit | Mått enhet för måttet används i utvärderingen av mått varningsregeln. |

## <a name="autoscale"></a>Automatisk skalning
Den här kategorin innehåller en förteckning över alla händelser relaterade till åtgärden Autoskala motorns baserat på automatiska inställningar du har definierat i din prenumeration. Ett exempel på typen av händelse visas i den här kategorin är ”Autoskala skalas upp misslyckades”. Använda autoskalning kan du automatiskt skala ut eller skala antalet instanser i en stöds resurstyp baserat på tid på dagen och/eller belastningen (mått) data med hjälp av en autoskalningsinställning. När villkoren är uppfyllda skala upp eller ned början och lyckades eller registreras misslyckade händelser i den här kategorin.

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
| kanaler | Alltid ”Admin, åtgärden” |
| Anspråk | JSON-blob med typen SPN (service principal name) eller resurs Autoskala-motorn. |
| correlationId | Ett GUID i strängformatet. |
| description |Statisk textbeskrivning av händelsen Autoskala. |
| eventDataId |Unik identifierare för händelsen Autoskala. |
| nivå |Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig” |
| resourceGroupName |Namnet på resursgruppen för autoskalningsinställningen. |
| resourceProviderName |Namnet på resursprovidern för autoskalningsinställningen. |
| resourceId |Resurs-id autoskalningsinställningens. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Namnet på åtgärden. |
| properties |En uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver information om händelsen. |
| Egenskaper. Beskrivning | Detaljerad beskrivning av vad Autoskala motorn gör. |
| Egenskaper. ResourceName | Resurs-ID för resursen påverkas (den resurs där skalningsåtgärden utfördes) |
| Egenskaper. OldInstancesCount | Antalet instanser innan åtgärden Autoskala tog effekt. |
| Egenskaper. NewInstancesCount | Antalet instanser efter åtgärden Autoskala tog effekt. |
| Egenskaper. LastScaleActionTime | Tidsstämpel när Autoskala åtgärden utfördes. |
| status |Sträng som beskriver status för åtgärden. Vissa vanliga värden: startas i pågår, slutfört, misslyckades, aktiv, löst. |
| subStatus | Vanligtvis är null för Autoskala. |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure motsvarande händelsen begäran bearbetades. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure prenumerations-Id. |

## <a name="security"></a>Säkerhet
Den här kategorin innehåller posten några aviseringar som genererats av Azure Security Center. Ett exempel på typen av händelse visas i den här kategorin är ”misstänkta dubbla tilläggsfilen utförs”.

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
| correlationId | Ett GUID i strängformatet. |
| description |Statisk textbeskrivning av händelsen säkerhet. |
| eventDataId |Unik identifierare för händelsen säkerhet. |
| EventName |Eget namn på händelsen säkerhet. |
| id |Unik resursidentifierare för händelsen säkerhet. |
| nivå |Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” eller ”utförlig” |
| resourceGroupName |Namnet på resursgruppen för resursen. |
| resourceProviderName |Namnet på resursprovidern för Azure Security Center. Alltid ”Microsoft.Security”. |
| resourceType |Typ av resurs som genererade händelsen säkerhet, till exempel ”Microsoft.Security/locations/alerts” |
| resourceId |Resurs-id för säkerhetsvarningen. |
| operationId |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Namnet på åtgärden. |
| properties |En uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver information om händelsen. De här egenskaperna varierar beroende på vilken typ av säkerhetsavisering. Se [den här sidan](../security-center/security-center-alerts-type.md) en beskrivning av typerna av aviseringar som kommer från Security Center. |
| Egenskaper. Allvarlighetsgrad |Allvarlighetsgrad. Möjliga värden är ”hög”, ”medel” eller ”låg”. |
| status |Sträng som beskriver status för åtgärden. Vissa vanliga värden: startas i pågår, slutfört, misslyckades, aktiv, löst. |
| subStatus | Vanligtvis är null för säkerhetshändelser. |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure motsvarande händelsen begäran bearbetades. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure prenumerations-Id. |

## <a name="next-steps"></a>Nästa steg
* [Mer information om aktivitetsloggen (tidigare granskningsloggar)](monitoring-overview-activity-logs.md)
* [Dataströmmen Azure aktivitetsloggen i Händelsehubbar](monitoring-stream-activity-logs-event-hubs.md)
