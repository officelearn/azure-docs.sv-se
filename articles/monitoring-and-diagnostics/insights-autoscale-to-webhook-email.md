---
title: "Använda automatiska åtgärder för att skicka e-post och webhook varningsmeddelanden. | Microsoft Docs"
description: "Lär dig mer om att använda automatiska åtgärder för att anropa webbadresser eller skicka e-postmeddelanden i Azure-Monitor. "
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: eb9a4c98-0894-488c-8ee8-5df0065d094f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: ancav
ms.openlocfilehash: 16caf14028494800e9259f0296c292b606d0210a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Använda automatiska åtgärder för att skicka e-post och webhook aviseringar i Azure-Monitor
Den här artikeln lär du hur du konfigurerar utlösare så att du kan anropa specifika webbadresser eller skicka e-postmeddelanden baserat på Autoskala åtgärder i Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks kan du dirigera Azure aviseringsmeddelanden till andra system för efterbearbetning eller anpassade meddelanden. Till exempel routning aviseringen för tjänster som kan hantera en inkommande webbegäran för att skicka SMS, log buggar, meddela ett team med chatt eller messaging services osv. Webhook-URI måste vara en giltig HTTP eller HTTPS-slutpunkt.

## <a name="email"></a>E-post
E-post kan skickas till en giltig e-postadress. Administratörer och medadministratörer i prenumerationen där regeln körs meddelas också.

## <a name="cloud-services-and-web-apps"></a>Molntjänster och Web Apps
Du kan anmäla sig från Azure-portalen för molntjänster och servergrupper (webbprogram).

* Välj den **skala genom** mått.

![skala med](./media/insights-autoscale-to-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Skaluppsättningar för den virtuella datorn
För nya virtuella datorer skapas med Resource Manager (skalningsuppsättningar i virtuella datorer), kan du konfigurera detta med hjälp av REST-API, Resource Manager-mallar, PowerShell och CLI. Ett gränssnitt för portalen är inte tillgänglig ännu.
När du använder mallen REST API: et eller Resource Manager, lägga till meddelanden element med följande alternativ.

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
| åtgärden |ja |Värdet måste vara ”skala” |
| sendToSubscriptionAdministrator |ja |Värdet måste vara ”sant” eller ”false” |
| sendToSubscriptionCoAdministrators |ja |Värdet måste vara ”sant” eller ”false” |
| customEmails |ja |Värdet kan vara null [] eller Strängmatrisen för e-post |
| Webhooks |ja |Värdet kan vara null eller giltiga Uri |
| serviceUri |ja |en giltig https Uri |
| properties |ja |Värdet måste vara tom {} eller kan innehålla nyckel / värde-par |

## <a name="authentication-in-webhooks"></a>Autentisering i webhooks
Webhooken kan autentisera med hjälp av tokenbaserad autentisering, där du kan spara webhooken URI med ett token-ID som en frågeparameter. Till exempel https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Autoskala meddelande webhook nyttolast schemat
När meddelandet Autoskala genereras ingår följande metadata i nyttolasten webhook:

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
| status |ja |Innebär att en Autoskala åtgärd har skapats |
| åtgärden |ja |För en ökning av instanser blir ”skala ut” och för en minskning av instanserna blir ”skala i” |
| Kontexten |ja |Kontexten Autoskala åtgärd |
| tidsstämpel |ja |Tidsstämpel när åtgärden Autoskala utlöstes |
| id |Ja |Hanteraren för filserverresurser ID autoskalningsinställningens |
| namn |Ja |Namnet på autoskalningsinställningen |
| Information |Ja |Förklaring av åtgärden som tjänsten Autoskala tog och ändringen i instansantalet |
| subscriptionId |Ja |Prenumerations-ID för målresursen som skalas |
| resourceGroupName |Ja |Resursgruppens namn för målresursen som skalas |
| resourceName |Ja |Namnet på målresursen som skalas |
| resourceType |Ja |Tre värden som stöds: ”microsoft.classiccompute/domainnames/slots/roles” - Molntjänsten roller, ”microsoft.compute/virtualmachinescalesets” - Skalningsuppsättningar i virtuella datorer, och ”Microsoft.Web/serverfarms” - webbprogram |
| resourceId |Ja |Resource Manager-ID för målresursen som skalas |
| portalLink |Ja |Azure portal länk till sidan Sammanfattning av målresurs |
| oldCapacity |Ja |Aktuella (gamla) instansantalet när Autoskala tog en skalningsåtgärd |
| newCapacity |Ja |Antal för nya instanser som Autoskala skalas till resursen |
| Egenskaper |Nej |Valfri. Ange < nyckel, värde > par (till exempel ordlista < String, String >). För egenskapsfältet är valfritt. I ett anpassat användargränssnitt eller logik app baserat arbetsflöde kan ange du nycklar och värden som kan skickas i nyttolasten. Ett annat sätt att skicka anpassade egenskaper tillbaka till det utgående webhook-anropet är att använda webhook URI (som frågeparametrar) |
