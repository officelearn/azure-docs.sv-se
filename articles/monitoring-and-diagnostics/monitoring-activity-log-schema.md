---
title: "Azure händelse för aktivitetslogg | Microsoft Docs"
description: "Förstå Händelseschema för data som sänds till aktivitetsloggen"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: johnkem
ms.openlocfilehash: a4ceb822e0ec3e1c1dc31ece1db761834e795f6c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-activity-log-event-schema"></a>Azure aktivitetsloggen Händelseschema
Den **Azure-aktivitetsloggen** är en logg som ger inblick i prenumerationsnivån händelser som har inträffat i Azure. Den här artikeln beskriver Händelseschema per kategori av data.

## <a name="administrative"></a>Administrativa
Den här kategorin innehåller posten för alla skapa, uppdatera, ta bort och åtgärden åtgärder utföras via Resource Manager. Exempel på vilka typer av händelser som visas i den här kategorin är ”Skapa virtuell dator” och ”ta bort nätverkssäkerhetsgrupp” varje åtgärd som en användare eller program med hjälp av hanteraren för filserverresurser modelleras som en åtgärd på en viss resurstyp. Om åtgärdstypen är Write-, Delete- eller åtgärd, är start-och lyckas eller misslyckas för denna åtgärd registrerade i den administrativa kategorin. Den administrativa kategorin omfattar även ändringar rollbaserad åtkomstkontroll i en prenumeration.

### <a name="sample-event"></a>Exempelhändelse
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
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
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
}
```

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Auktorisering |BLOB RBAC egenskaper för händelsen. Inkluderar vanligtvis egenskaperna ””, ”roll” och ”omfattning”. |
| Anroparen |E-postadressen för användaren som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. |
| kanaler |Ett av följande värden: ”Admin”, ”åtgärden” |
| Anspråk |Detta JWT-token som används av Active Directory för att autentisera användaren eller programmet att utföra åtgärden i hanteraren för filserverresurser. |
| correlationId |Vanligtvis ett GUID i strängformatet. Händelser som delar en correlationId tillhöra samma uber åtgärd. |
| Beskrivning |Statisk textbeskrivning av en händelse. |
| eventDataId |Unik identifierare för en händelse. |
| httpRequest |BLOB som beskriver Http-begäran. Inkluderar vanligtvis ”clientRequestId”, ”clientIpAddress” och ”metod” (HTTP-metod. Till exempel PLACERA). |
| nivå |Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig” |
| resourceGroupName |Namnet på resursgruppen för resursen påverkas. |
| resourceProviderName |Namnet på resursprovidern för resursen påverkas |
| resourceId |Resurs-id för resursen påverkas. |
| Åtgärds-ID |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Namnet på åtgärden. |
| properties |En uppsättning `<Key, Value>` par (det vill säga en ordlista) som beskriver information om händelsen. |
| status |Sträng som beskriver status för åtgärden. Vissa vanliga värden: startas i pågår, slutfört, misslyckades, aktiv, löst. |
| subStatus |Vanligtvis HTTP-statuskoden motsvarande rest anropa, men kan även innehålla andra strängar som beskriver en sådan, till exempel värdena vanliga: OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inte innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400), det gick inte att hitta (HTTP-statuskod: 404), konflikt (HTTP-statuskod : 409), internt serverfel (HTTP-statuskod: 500), tjänsten inte tillgänglig (HTTP-statuskod: 503), Gateway-Timeout (HTTP-statuskod: 504). |
| eventTimestamp |Tidsstämpel när händelsen skapades av tjänsten Azure motsvarande händelsen begäran bearbetades. |
| submissionTimestamp |Tidsstämpel när händelsen blev tillgängliga för frågor. |
| subscriptionId |Azure prenumerations-Id. |

## <a name="service-health"></a>Tjänstens hälsa
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
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
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
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
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

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
Elementnamn | Beskrivning
-------- | -----------
kanaler | Är ett av följande värden: ”Admin”, ”åtgärden”
correlationId | Är vanligtvis ett GUID i strängformatet. Händelser med som tillhör samma uber åtgärd vanligtvis delar samma correlationId.
Beskrivning | Beskrivning av händelsen.
eventDataId | Den unika identifieraren för en händelse.
EventName | Rubrik på händelsen.
nivå | Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig”
resourceProviderName | Namnet på resursprovidern för resursen påverkas. Detta kan vara null om det är inte känt.
resourceType| Typ av resurs för resursen påverkas. Detta kan vara null om det är inte känt.
subStatus | Vanligtvis är null för händelser för Hälsotjänst.
eventTimestamp | Tidsstämpel när händelselogg har genererats och skickats till aktivitetsloggen.
submissionTimestamp |   Tidsstämpel när händelsen blev tillgängliga i aktivitetsloggen.
subscriptionId | Azure-prenumerationen som den här händelsen loggades.
status | Sträng som beskriver status för åtgärden. Vissa vanliga värden: aktiva, löst.
operationName | Namnet på åtgärden. Vanligtvis Microsoft.ServiceHealth/incident/action.
category | ”ServiceHealth”
resourceId | Resurs-id för påverkas resursen, om den är känd. Prenumerations-ID har angetts på annat sätt.
Properties.title | Lokaliserade rubriken för den här kommunikationen. Engelska är standardspråk.
Properties.Communication | Lokaliserad information om kommunikationen med HTML-kod. Engelska är standard.
Properties.incidentType | Möjliga värden: AssistedRecovery, ActionRequired, Information, incidenter, underhåll, säkerhet
Properties.trackingId | Identifierar den här händelsen är associerad med incidenten. Används för att korrelera händelser relaterade till en incident.
Properties.impactedServices | En ESC JSON-blob som beskriver de tjänster och regioner som påverkas av incidenten. En lista över tjänster, som har en ServiceName och en lista över ImpactedRegions, som har en RegionName.
Properties.defaultLanguageTitle | Kommunikation på engelska
Properties.defaultLanguageContent | Kommunikation på engelska som HTML-kod eller oformaterad text
Properties.Stage | Möjliga värden för AssistedRecovery, ActionRequired, Information, incidenter, säkerhet: är aktiv, löst. De är för underhåll: aktiv, planerad, InProgress, Avbruten, Rescheduled, löst, Slutför
Properties.communicationId | Kommunikationen den här händelsen är kopplat.

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
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
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
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
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
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Anroparen | Alltid Microsoft.Insights/alertRules |
| kanaler | Alltid ”Admin, åtgärden” |
| Anspråk | JSON-blob med typen SPN (service principal name) eller resurs varning-motorn. |
| correlationId | Ett GUID i strängformatet. |
| Beskrivning |Statisk textbeskrivning av händelsen avisering. |
| eventDataId |Unik identifierare för varning avisering. |
| nivå |Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig” |
| resourceGroupName |Namnet på resursgruppen för resursen påverkas om det är en avisering om mått. För andra aviseringstyper är namnet på resursgruppen som innehåller aviseringen sig själv. |
| resourceProviderName |Namnet på resursprovidern för resursen påverkas om det är en avisering om mått. För andra aviseringstyper kan är det här namnet på resursprovidern för aviseringen sig själv. |
| resourceId | Namnet på resurs-ID för resursen påverkas om det är en avisering om mått. För andra aviseringstyper är resurs-ID för avisering resursen sig själv. |
| Åtgärds-ID |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
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
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
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
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
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
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>Egenskapsbeskrivningar
| Elementnamn | Beskrivning |
| --- | --- |
| Anroparen | Alltid Microsoft.Insights/autoscaleSettings |
| kanaler | Alltid ”Admin, åtgärden” |
| Anspråk | JSON-blob med typen SPN (service principal name) eller resurs Autoskala-motorn. |
| correlationId | Ett GUID i strängformatet. |
| Beskrivning |Statisk textbeskrivning av händelsen Autoskala. |
| eventDataId |Unik identifierare för händelsen Autoskala. |
| nivå |Nivå av händelsen. Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig” |
| resourceGroupName |Namnet på resursgruppen för autoskalningsinställningen. |
| resourceProviderName |Namnet på resursprovidern för autoskalningsinställningen. |
| resourceId |Resurs-id autoskalningsinställningens. |
| Åtgärds-ID |Ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
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

## <a name="next-steps"></a>Nästa steg
* [Mer information om aktivitetsloggen (tidigare granskningsloggar)](monitoring-overview-activity-logs.md)
* [Dataströmmen Azure aktivitetsloggen i Händelsehubbar](monitoring-stream-activity-logs-event-hubs.md)
