---
title: "Anropa en webhook för ett Azure log varning | Microsoft Docs"
description: "Lär dig mer om att skicka aktiviteten logghändelser till andra tjänster för anpassade åtgärder. Du kan exempelvis skicka SMS-meddelanden, loggar buggar eller meddela ett team via en chatt eller meddelandetjänst."
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
ms.openlocfilehash: 9872c30d123f0a7443e28dc58ee0d4e16572a390
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="call-a-webhook-on-an-azure-activity-log-alert"></a>Anropa en webhook för ett Azure log varning
Du kan använda webhooks för att dirigera Azure aviseringsmeddelanden till andra system för efter bearbetning eller för anpassade åtgärder. Du kan använda en webhook på en avisering och dirigerar den till tjänster som skickar SMS-meddelanden att logga buggar, för att meddela ett team via chatt eller -tjänster eller för olika åtgärder. Du kan också ställa in en varning för loggen att skicka e-post när en avisering aktiveras.

Den här artikeln beskriver hur du ställer in en webhook anropas när aviseringen utlöses av en Azure aktivitet loggen. Den visar även hur nyttolasten för HTTP POST till en webhook ser ut. Information om installation och schemat för en Azure mått avisering finns [konfigurera en webhook på en Azure mått avisering](insights-webhooks-alerts.md). 

> [!NOTE]
> Funktionen som stödjer anropar en webhook på ett Azure log varning är för närvarande under förhandsgranskning.
>
>

Du kan ställa in en varning för loggfilen genom att använda [Azure PowerShell-cmdlets](insights-powershell-samples.md#create-metric-alerts), [plattformsoberoende CLI](insights-cli-samples.md#work-with-alerts), eller [Azure övervakaren REST API: er](https://msdn.microsoft.com/library/azure/dn933805.aspx). För närvarande kan använda du inte Azure-portalen för att ställa in en varning för loggen.

## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan autentisera med någon av följande metoder:

* **Tokenbaserad auktorisering**. Webhooken URI sparas med ett token-ID. Exempel: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
* **Grundläggande auktorisering**. Webhooken URI sparas med ett användarnamn och lösenord. Exempel: `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Nyttolasten i schemat
POST-åtgärden innehåller följande JSON-nyttolast och schemat för alla aktivitet log-aviseringar. Det här schemat liknar den som används för måttet-baserade aviseringar.

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
| status |Används för mått aviseringar. För aktiviteten Logga varningar alltid inställd på aktiverad.|
| Kontexten |Kontexten för händelsen. |
| activityLog | Loggegenskaperna för händelsen.|
| Auktorisering |Rollbaserad åtkomstkontroll (RBAC)-egenskaperna för händelsen. Dessa egenskaper innehåller vanligtvis **åtgärd**, **rollen**, och **omfång**. |
| åtgärd | Den åtgärd som avbildas av aviseringen. |
| Omfång | Omfattningen för aviseringen (resurs).|
| kanaler | Åtgärden. |
| Anspråk | En samling information som relaterar till anspråk. |
| uppringare |GUID eller användarnamnet för användaren som utförde åtgärden eller UPN-anspråket SPN-anspråk baserat på tillgänglighet. Kan vara ett null-värde för vissa system-anrop. |
| correlationId |Vanligtvis ett GUID i strängformat. Händelser med **correlationId** tillhör samma större åtgärd. De har samma vanligtvis **correlationId** värde. |
| description |Aviseringsbeskrivningen som angavs när aviseringen skapades. |
| eventSource |Namnet på Azure-tjänst eller infrastruktur som genererade händelsen. |
| eventTimestamp |Den tid som händelsen inträffade. |
| eventDataId |Unik identifierare för händelsen. |
| nivå |Ett av följande värden: kritisk, fel, varning, information eller utförlig. |
| operationName |Namnet på åtgärden. |
| operationId |Vanligtvis ett GUID som delas mellan händelser. GUID som motsvarar vanligtvis en enda åtgärd. |
| resourceId |Resurs-ID för resursen som påverkas. |
| resourceGroupName |Namnet på resursgruppen för resursen som påverkas. |
| resourceProviderName |Resursprovidern för resursen som påverkas. |
| status |Ett strängvärde som anger status för åtgärden. Vanliga värden är igång, pågår, slutfört, misslyckades, aktiv och löst. |
| subStatus |Normalt innehåller HTTP-statuskod för motsvarande REST-anrop. Det kan även innehålla andra strängar som beskriver en sådan. Vanliga understatus värden är OK (HTTP-statuskod: 200), skapade (HTTP-statuskod: 201), godkända (HTTP-statuskod: 202), inte innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400), det gick inte att hitta (HTTP-statuskod: 404), konflikt (HTTP-statuskod: 409 ), Internt serverfel (HTTP-statuskod: 500), tjänsten inte tillgänglig (HTTP-statuskod: 503), och Gateway-Timeout (HTTP-statuskod: 504). |
| subscriptionId |Azure prenumerations-ID. |
| submissionTimestamp |Den tid då händelsen skapades av Azure-tjänsten som bearbetade förfrågan. |
| resourceType | Typ av resurs som genererade händelsen.|
| properties |En uppsättning nyckel/värde-par som innehåller information om händelsen. Till exempel `Dictionary<String, String>`. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [aktivitetsloggen](monitoring-overview-activity-logs.md).
* Lär dig hur du [köra Azure Automation-skript (runbooks) på Azure-aviseringar](http://go.microsoft.com/fwlink/?LinkId=627081).
* Lär dig hur du [använder en logikapp för att skicka ett SMS-meddelande via Twilio från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Det här exemplet är för mått aviseringar, men du kan ändra den fungerar med en varning för loggen.
* Lär dig hur du [använder en logikapp för att skicka meddelandet Slack från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Det här exemplet är för mått aviseringar, men du kan ändra den fungerar med en varning för loggen.
* Lär dig hur du [använder en logikapp för att skicka ett meddelande till en Azure-kö från en Azure avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Det här exemplet är för mått aviseringar, men du kan ändra den fungerar med en varning för loggen.
