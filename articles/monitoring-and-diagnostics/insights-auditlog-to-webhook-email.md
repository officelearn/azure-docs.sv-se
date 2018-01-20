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
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
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

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Elementnamn | Beskrivning |
| --- | --- |
| status |Används för mått aviseringar. Alltid inställt på ”Aktivera” för aktivitetsloggen aviseringar. |
| Kontexten |Kontexten för händelsen. |
| activityLog | Loggegenskaperna för händelsen.|
| Auktorisering |RBAC-egenskaperna för händelsen. Dessa omfattar vanligtvis ”åtgärd”, ”roll” och ”omfattningen”. |
| åtgärd | Åtgärd som avbildas av aviseringen. |
| Omfång | Omfånget för aviseringen (dvs resurs).|
| kanaler | Åtgärd |
| Anspråk | En samling information som finns på relaterar till anspråk. |
| uppringare |GUID eller användarnamnet för användaren som utförde åtgärden, UPN-anspråk eller SPN-anspråk baserat på tillgänglighet. Kan vara null för vissa system-anrop. |
| correlationId |Vanligtvis ett GUID i strängformat. Händelser med correlationId tillhör samma större åtgärd och vanligtvis delar en correlationId. |
| description |Aviseringsbeskrivningen som angetts under skapandet av aviseringen. |
| eventSource |Namnet på Azure-tjänsten eller infrastruktur som genererade händelsen. |
| eventTimestamp |Tid som händelsen inträffade. |
| eventDataId |Unik identifierare för händelsen. |
| nivå |Ett av följande värden: ”kritiska”, ”Error”, ”varning”, ”information” och ”utförlig”. |
| operationName |Namnet på åtgärden. |
| operationId |Vanligtvis ett GUID som delas mellan de händelser som motsvarar en enda åtgärd. |
| resourceId |Resurs-ID för resursen påverkas. |
| resourceGroupName |Namnet på resursgruppen för resursen som påverkas |
| resourceProviderName |Resursprovidern för resursen påverkas. |
| status |Sträng. Status för åtgärden. Vanliga värden är: ”starta”, ”pågående”, ”Succeeded”, ”misslyckades”, ”aktiv”, ”löst”. |
| subStatus |Normalt innehåller HTTP-statuskod för motsvarande REST-anrop. Det kan även innehålla andra strängar som beskriver en sådan. Vanliga understatus värden är: OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inte innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400), inte att hitta (HTTP-statuskod: 404), konflikt (HTTP-statuskod: 409), internt serverfel (HTTP-statuskod: 500), tjänsten är inte tillgänglig (HTTP-statuskod: 503), Gateway-Timeout (HTTP-statuskod: 504) |
| subscriptionId |Azure prenumerations-ID. |
| submissionTimestamp |Tid då händelsen skapades av Azure-tjänsten som bearbetade förfrågan. |
| resourceType | Typ av resurs som genererade händelsen.|
| properties |En uppsättning `<Key, Value>` par (d.v.s. `Dictionary<String, String>`) som innehåller information om händelsen. |

## <a name="next-steps"></a>Nästa steg
* [Mer information om aktivitetsloggen](monitoring-overview-activity-logs.md)
* [Köra Azure Automation-skript (Runbooks) på Azure-aviseringar](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Använd Logikapp för att skicka ett SMS via Twilio från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Det här exemplet är för mått aviseringar, men kan ändras för att fungera med en aktivitetsloggen avisering.
* [Använda Logikapp för att skicka en Slack-meddelande från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Det här exemplet är för mått aviseringar, men kan ändras för att fungera med en aktivitetsloggen avisering.
* [Använd Logikapp för att skicka ett meddelande till en Azure-kö i en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Det här exemplet är för mått aviseringar, men kan ändras för att fungera med en aktivitetsloggen avisering.
