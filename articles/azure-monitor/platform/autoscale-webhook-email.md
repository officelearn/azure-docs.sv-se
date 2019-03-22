---
title: Använd automatisk skalning för att skicka e-post och webhook-aviseringar
description: 'Se hur du använder automatiska skalningsåtgärderna att anropa-URL: webbadresser eller skicka e-postmeddelanden i Azure Monitor. '
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 25ef2541dfa0b4cbd6e11d64381da645acfe653a
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259303"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Använda automatisk skalning åtgärder för att skicka e-post och webhook-aviseringar i Azure Monitor
Den här artikeln visar hur du konfigurerar utlösare så att du kan anropa Webb-URL: er eller skicka e-postmeddelanden baserat på åtgärder för automatisk skalning i Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks kan du dirigera Azure aviseringsmeddelanden till andra system för efterbearbetning eller anpassade meddelanden. Till exempel routning aviseringen till tjänster som kan hantera en inkommande webbegäran att skicka SMS, log buggar, meddela ett team med chatt eller messaging services osv. Webhooken URI måste vara en giltig HTTP eller HTTPS-slutpunkt.

## <a name="email"></a>E-post
E-post kan skickas till någon giltig e-postadress. Administratörer och medadministratörer i prenumerationen där regeln körs kommer också att meddelas.

## <a name="cloud-services-and-web-apps"></a>Molntjänster och Web Apps
Du kan anmäla sig från Azure portal för molntjänster och servergrupper (Webbappar).

* Välj den **skala genom att** mått.

![skala genom att](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för Virtual Machines
För nyare virtuella datorer skapas med Resource Manager (VM-skalningsuppsättningar), kan du konfigurera detta med hjälp av REST API, Resource Manager-mallar, PowerShell och CLI. Ett portalgränssnitt är inte tillgänglig ännu.
När du använder REST API eller Resource Manager-mallen inkludera meddelanden-element med följande alternativ.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Fält | Obligatorisk? | Beskrivning |
| --- | --- | --- |
| åtgärd |ja |Värdet måste vara ”skala” |
| sendToSubscriptionAdministrator |ja |Värdet måste vara ”sant” eller ”FALSKT” |
| sendToSubscriptionCoAdministrators |ja |Värdet måste vara ”sant” eller ”FALSKT” |
| customEmails |ja |Värdet kan vara null [] eller Strängmatrisen av e-postmeddelanden |
| webhooks |ja |Värdet kan vara null eller ogiltig Uri |
| serviceUri |ja |en giltig https Uri |
| properties |ja |Värdet måste vara tom {} eller kan innehålla nyckel / värde-par |

## <a name="authentication-in-webhooks"></a>Autentisering i webhooks
Webhooken kan autentisera med hjälp av tokenbaserad autentisering, där du kan spara webhooken URI med ett token-ID som frågeparameter. Till exempel https: \/ /mysamplealert/webcallback? tokenid = sometokenid & someparameter = somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Automatisk skalning meddelande webhook-nyttolasten schema
När meddelandet Autoskala genereras ingår följande metadata i webhook-nyttolasten:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Fält | Obligatorisk? | Beskrivning |
| --- | --- | --- |
| status |ja |Den status som indikerar att en åtgärd för automatisk skalning genererades |
| åtgärd |ja |För en ökning av instanser blir ”skala ut” och för en minskning i instanser, det är ”skala In” |
| Kontext |ja |Åtgärd-kontexten automatisk skalning |
| tidsstämpel |ja |Tidsstämpel när autoskalning utlöstes |
| id |Ja |Resource Manager-ID för autoskalningsinställningen |
| namn |Ja |Namnet på autoskalningsinställningen |
| detaljer |Ja |Förklaring av åtgärden tog för tjänsten för automatisk skalning och förändringen i instansantalet |
| subscriptionId |Ja |Prenumerations-ID för målresursen som skalas |
| resourceGroupName |Ja |Resursgruppsnamnet för målresursen som skalas |
| resourceName |Ja |Namnet på målresursen som skalas |
| ResourceType |Ja |Tre värden som stöds: ”microsoft.classiccompute/domainnames/slots/roles” - molntjänst roller, ”microsoft.compute/virtualmachinescalesets” - Virtual Machine Scale Sets och ”Microsoft.Web/serverfarms” - Webbapp |
| resourceId |Ja |Resource Manager-ID för målresursen som skalas |
| portalLink |Ja |Azure portal-länk till sidan Sammanfattning av målresurs |
| oldCapacity |Ja |Det aktuella (gamla) instansantalet när autoskalning tog en skalningsåtgärd |
| newCapacity |Ja |Ny instansantalet som automatisk skalning skalas till resursen |
| Egenskaper |Nej |Valfri. Uppsättning < nyckel, värde > par (till exempel Dictionary < String, String >). För egenskapsfältet är valfritt. I en anpassad användargränssnittet eller baserade logikapparbetsflöde kan ange du nycklar och värden som kan skickas med nyttolasten. Ett annat sätt att skicka anpassade egenskaper tillbaka till det utgående webhook-anropet är att använda webhooken URI (som frågeparametrar) |

