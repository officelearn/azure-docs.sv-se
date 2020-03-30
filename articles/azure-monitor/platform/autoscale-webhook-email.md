---
title: Använd automatisk skalning för att skicka aviseringar om e-post och webhook
description: Lär dig hur du använder åtgärder för automatisk skalning för att anropa webb-URL:er eller skicka e-postmeddelanden i Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: c82b170bb3801bdc701ed84230db57f5691523ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120698"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Använda åtgärder för automatisk skalning för att skicka e-post- och webhook-aviseringar i Azure Monitor
Den här artikeln visar hur du konfigurerar utlösare så att du kan ringa specifika webb-URL:er eller skicka e-postmeddelanden baserat på åtgärder för automatisk skalning i Azure.  

## <a name="webhooks"></a>Webhooks
Med Webhooks kan du dirigera Azure-aviseringar till andra system för efterbearbetning eller anpassade meddelanden. Till exempel, routing aviseringen till tjänster som kan hantera en inkommande webbbegäran att skicka SMS, logga buggar, meddela ett team med hjälp av chatt eller meddelandetjänster, etc. Webhook URI måste vara en giltig HTTP- eller HTTPS-slutpunkt.

## <a name="email"></a>E-post
E-post kan skickas till valfri giltig e-postadress. Administratörer och medadministratörer för prenumerationen där regeln körs meddelas också.

## <a name="cloud-services-and-app-services"></a>Molntjänster och apptjänster
Du kan anmäla dig från Azure-portalen för molntjänster och servergrupper (App Services).

* Välj **skala** efter mått.

![skala efter](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för Virtual Machines
För nyare virtuella datorer som skapats med Resource Manager (Virtual Machine scale sets) kan du konfigurera detta med REST API, Resource Manager-mallar, PowerShell och CLI. Ett portalgränssnitt är ännu inte tillgängligt.
När du använder MALLEN REST API eller Resource Manager ska du inkludera meddelandeelementet i dina [automatiska skalningsinställningar](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) med följande alternativ.

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

| Field | Obligatoriska? | Beskrivning |
| --- | --- | --- |
| Drift |ja |värdet måste vara "Skala" |
| sendToSubscriptionAdministrator |ja |värdet måste vara "sant" eller "falskt" |
| sendToSubscriptionCoAdministrators |ja |värdet måste vara "sant" eller "falskt" |
| customEmails |ja |värdet kan vara null [] eller strängmatris med e-postmeddelanden |
| webhooks (olika) |ja |värde kan vara null eller giltig Uri |
| serviceUri |ja |a valid https Uri |
| properties |ja |värdet måste {} vara tomt eller innehålla nyckelvärdespar |

## <a name="authentication-in-webhooks"></a>Autentisering i webhooks
Webhooken kan autentisera med hjälp av tokenbaserad autentisering, där du sparar webhook URI med ett token-ID som frågeparameter. Till exempel https:\//mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Schema för automatisk skalning av meddelande webhook
När meddelandet om automatisk skalning genereras inkluderas följande metadata i webhook-nyttolasten:

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


| Field | Obligatoriska? | Beskrivning |
| --- | --- | --- |
| status |ja |Statusen som anger att en åtgärd för automatisk skalning har genererats |
| Drift |ja |För en ökning av instanser, kommer det att vara "Skala ut" och för en minskning av instanser, kommer det att vara "Skala in" |
| Sammanhang |ja |Åtgärdskontexten för automatisk skalning |
| timestamp |ja |Tidsstämpel när åtgärden automatisk skalning utlöstes |
| id |Ja |Resurshanterarens ID för inställningen för automatisk skalning |
| namn |Ja |Namnet på inställningen för automatisk skalning |
| Detaljer |Ja |Förklaring av den åtgärd som tjänsten för automatisk skalning tog och ändringen i instansantalet |
| subscriptionId |Ja |Prenumerations-ID för målresursen som skalas |
| resourceGroupName |Ja |Resursgruppsnamn för målresursen som skalas |
| resourceName |Ja |Namnet på målresursen som skalas |
| resourceType |Ja |De tre värden som stöds: "microsoft.classiccompute/domainnames/slots/roles" - Cloud Service roles, "microsoft.compute/virtualmachinescalesets" - Virtual Machine Scale Sets och "Microsoft.Web/serverfarms" - Web App |
| resourceId |Ja |Resurshanterarens ID för målresursen som skalas |
| portalLink (portalLink) |Ja |Azure portal länk till sammanfattningssidan för målresursen |
| gamlaKapacitet |Ja |Den aktuella (gamla) instansen räknas när automatisk skalning vidtog en skalningsåtgärd |
| nykapacitet |Ja |Den nya instansen räknas som Automatisk skalning skalade resursen till |
| properties |Inga |Valfri. Uppsättning <nyckel, värde> par (till exempel Ordlista <Sträng, Sträng>). Egenskapsfältet är valfritt. I ett anpassat användargränssnitt eller logikappbaserat arbetsflöde kan du ange nycklar och värden som kan skickas med nyttolasten. Ett alternativt sätt att skicka anpassade egenskaper tillbaka till det utgående webhook-anropet är att använda själva webhook-URI:n (som frågeparametrar) |

