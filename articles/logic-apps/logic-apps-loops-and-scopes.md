---
title: "Skapa slingor och scope eller debatch data i arbetsflöden - Azure Logic Apps | Microsoft Docs"
description: "Skapa slingor att gå igenom data, gruppen åtgärder till scope, eller debatch data för att starta flera arbetsflöden i Azure Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 64b8f414efe8cd886589084f05e04486c9a0d05c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logic Apps-slingor, -omfattningar och Debatching
  
Logic Apps innehåller ett antal sätt att arbeta med matriser, samlingar, batchar, och körs i en loop i ett arbetsflöde.
  
## <a name="foreach-loop-and-arrays"></a>ForEach-loop och matriser
  
Logic Apps kan du slinga över en uppsättning data och utföra en åtgärd för varje objekt.  Loopa över en samling är möjligt via den `foreach` åtgärd.  I designer kan du lägga till en för varje loop.  När du har valt den matris som du vill iterera över, kan du börja lägga till åtgärder.  Du kan lägga till flera åtgärder per foreach-loop.  En gång inom loopen, kan du ange vad som ska hända vid varje värde i matrisen.

  Det här exemplet skickar ett e-postmeddelande för varje e-postadress som innehåller ”microsoft.com”. Om du använder vyn kod, kan du ange en för varje loop som i följande exempel:

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  En `foreach` åtgärd kan iterera över matriser med tusentals entiteter.  Iterationer köra parallellt som standard.  Se [gränser och konfiguration](logic-apps-limits-and-config.md) information om gränser för matrisen och samtidighet.

### <a name="sequential-foreach-loops"></a>Sekventiella ForEach slingor

Så här aktiverar du en foreach-slinga för att köra, den `Sequential` åtgärden alternativet ska läggas till.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Tills loop
  
  Du kan utföra en åtgärd eller en serie åtgärder förrän ett villkor uppfylls.  Det vanligaste scenariot för att använda en tills loop anropar en slutpunkt förrän du får svar som du letar efter.  Du kan ange i designer för att lägga till en tills loop.  När du lägger till åtgärder i den här slingan, du kan ange villkoret för avslutning, samt slingan gränser.
  
  Det här exemplet anropar en HTTP-slutpunkt tills svarstexten har värdet ”slutförd”.  Den är klar när antingen: 
  
  * HTTP-svar som har statusen ”Slutförd”
  * Det har försökt under en timme
  * Det har körts i en slinga 100 gånger
  
  Om du använder vyn kod, kan du ange en tills loop som i följande exempel:
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn och debatching

En utlösare kan ibland visas en matris med objekt som du vill debatch och starta ett arbetsflöde per artikel.  Den här debatching kan utföras via den `spliton` kommando.  Som standard om utlösaren-swagger anger en nyttolast som är en matris, en `spliton` har lagts till. Den `spliton` kommando startar en körning per objekt i matrisen.  SplitOn kan bara läggas till en utlösare som kan vara manuellt konfigurerade eller åsidosätts. Du kan inte ha en `spliton` och även implementera synkron svar-mönster.  Alla arbetsflöden som har en `response` åtgärd förutom `spliton` körs asynkront och skickar en omedelbar `202 Accepted` svar.  

  Det här exemplet tar emot en matris med objekt och debatches på varje rad. SplitOn kan anges i vyn kod som i följande exempel:

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Omfång

Det är möjligt att gruppera en serie åtgärder tillsammans med en omfattning.  Scope är användbara för att implementera undantagshantering.  I Designern kan du lägga till ett nytt scope och börja lägga till alla åtgärder i den.  Du kan definiera scope i kodvy som i följande exempel:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
