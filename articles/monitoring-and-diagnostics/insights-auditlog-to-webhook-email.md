---
title: Anropa en webhook i Azure-aktivitetsloggen aviseringar | Microsoft Docs
description: "Vidarebefordra aktivitet logghändelser till andra tjänster för anpassade åtgärder. Till exempel skicka SMS, logga programfel eller meddela ett team via chatt/meddelandetjänst."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 341ab32ad0ec691285fbf1537ee298ab30156a5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>Anropa en webhook i Azure-aktivitetsloggen aviseringar
Webhooks kan du dirigera Azure aviseringsmeddelanden till andra system för efterbearbetning eller anpassade åtgärder. Du kan använda en webhook på en avisering och dirigerar den till tjänster som skicka SMS, logga programfel, meddela ett team via chatt/meddelandetjänster eller göra en mängd olika andra åtgärder. Den här artikeln beskriver hur du ställer in en webhook anropas när en varning utlöses Azure-aktivitetsloggen. Den visar även hur nyttolasten för HTTP POST till en webhook ser ut. Mer information om installation och schemat för en Azure mått avisering [se den här sidan i stället](insights-webhooks-alerts.md). Du kan också ställa in en avisering i aktivitetsloggen att skicka e-post när aktiverad.

> [!NOTE]
> Den här funktionen är för närvarande under förhandsgranskning och tas bort vid en viss tidpunkt i framtiden.
>
>

Du kan ställa in en aktivitetsloggen avisering med hjälp av den [Azure PowerShell-Cmdlets](insights-powershell-samples.md#create-metric-alerts), [plattformsoberoende CLI](insights-cli-samples.md#work-with-alerts), eller [REST-API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). För närvarande kan du ange ett in med Azure-portalen.

## <a name="authenticating-the-webhook"></a>Autentisera webhooken
Webhooken kan autentisera med någon av följande metoder:

1. **Tokenbaserad auktorisering** -webhooken URI sparas med ett token ID, t.ex.`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Grundläggande auktorisering** -URI sparas med ett användarnamn och lösenord, till exempel webhooken`https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Nyttolasten i schemat
POST-åtgärden innehåller följande JSON-nyttolast och schemat för alla aktivitetsloggen-baserade aviseringar. Det här schemat liknar den som används av måttet-baserade aviseringar.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

| Elementnamn | Beskrivning |
| --- | --- |
| status |Används för mått aviseringar. Alltid inställt på ”Aktivera” för aktivitetsloggen aviseringar. |
| Kontexten |Kontexten för händelsen. |
| resourceProviderName |Resursprovidern för resursen påverkas. |
| conditionType |Alltid ”Event”. |
| namn |Namnet på regeln. |
| id |Resurs-ID för aviseringen. |
| description |Aviseringsbeskrivningen som angetts under skapandet av aviseringen. |
| subscriptionId |Azure prenumerations-ID. |
| tidsstämpel |Tid då händelsen skapades av Azure-tjänsten som bearbetade förfrågan. |
| resourceId |Resurs-ID för resursen påverkas. |
| resourceGroupName |Namnet på resursgruppen för resursen som påverkas |
| properties |En uppsättning `<Key, Value>` par (d.v.s. `Dictionary<String, String>`) som innehåller information om händelsen. |
| Händelse |Element som innehåller metadata om händelsen. |
| Auktorisering |RBAC-egenskaperna för händelsen. Dessa omfattar vanligtvis ”åtgärd”, ”roll” och ”omfattningen”. |
| category |Kategori för händelsen. Värden som stöds omfattar: administrativa, varning, säkerhet, ServiceHealth och rekommendation. |
| Anroparen |E-postadressen för användaren som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. Kan vara null för vissa system-anrop. |
| correlationId |Vanligtvis ett GUID i strängformat. Händelser med correlationId tillhör samma större åtgärd och vanligtvis delar en correlationId. |
| eventDescription |Statisk textbeskrivning av händelsen. |
| eventDataId |Unik identifierare för händelsen. |
| eventSource |Namnet på Azure-tjänsten eller infrastruktur som genererade händelsen. |
| httpRequest |Inkluderar vanligtvis ”clientRequestId”, ”clientIpAddress” och ”metod” (HTTP-metoden anger t.ex.). |
| nivå |Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig”. |
| Åtgärds-ID |Vanligtvis ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| operationName |Namnet på åtgärden. |
| properties |Egenskaper för händelsen. |
| status |Sträng. Status för åtgärden. Vanliga värden är: ”starta”, ”pågående”, ”Succeeded”, ”misslyckades”, ”aktiv”, ”löst”. |
| subStatus |Normalt innehåller HTTP-statuskod för motsvarande REST-anrop. Det kan även innehålla andra strängar som beskriver en sådan. Vanliga understatus värden är: OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inte innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400), inte att hitta (HTTP-statuskod: 404), konflikt (HTTP-statuskod: 409), internt serverfel (HTTP-statuskod: 500), tjänsten är inte tillgänglig (HTTP-statuskod: 503), Gateway-Timeout (HTTP-statuskod: 504) |

## <a name="next-steps"></a>Nästa steg
* [Mer information om aktivitetsloggen](monitoring-overview-activity-logs.md)
* [Köra Azure Automation-skript (Runbooks) på Azure-aviseringar](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Använd Logikapp för att skicka ett SMS via Twilio från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Det här exemplet är för mått aviseringar, men kan ändras för att fungera med en aktivitetsloggen avisering.
* [Använda Logikapp för att skicka en Slack-meddelande från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Det här exemplet är för mått aviseringar, men kan ändras för att fungera med en aktivitetsloggen avisering.
* [Använd Logikapp för att skicka ett meddelande till en Azure-kö i en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Det här exemplet är för mått aviseringar, men kan ändras för att fungera med en aktivitetsloggen avisering.
