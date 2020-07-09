---
title: Azure Function som händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Azure Functions som händelse hanterare för Event Grid händelser.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 8e48949bb5fecdf370fdf23146209ad757ffa062
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105769"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure Function som händelse hanterare för Event Grid händelser

En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar en åtgärd för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Functions** är en av dem. 

Använd **Azure Functions** i en server lös arkitektur för att svara på händelser från Event Grid. När du använder en Azure-funktion som hanterare använder du Event Grid-utlösaren i stället för den allmänna HTTP-utlösaren. Event Grid validerar automatiskt Event Grid-utlösare. Med allmänna HTTP-utlösare måste du implementera [verifierings svaret](webhook-event-delivery.md) själv.

Mer information finns i [Event Grid utlösare för Azure Functions](../azure-functions/functions-bindings-event-grid.md) för en översikt över hur du använder Event Grid-utlösaren i functions.

## <a name="tutorials"></a>Självstudier

|Titel  |Beskrivning  |
|---------|---------|
| [Snabb start: hantera händelser med funktion](custom-event-to-function.md) | Skickar en anpassad händelse till en funktion för bearbetning. |
| [Självstudie: automatisera storleks ändring av överförda bilder med hjälp av Event Grid](resize-images-on-storage-blob-upload-event.md) | Användare laddar upp bilder via webbapp till lagrings kontot. När en lagrings-BLOB skapas skickar Event Grid en händelse till Function-appen, som ändrar storlek på den överförda avbildningen. |
| [Självstudie: strömma Big data till ett informations lager](event-grid-event-hubs-integration.md) | När Event Hubs skapar en Infångnings fil, Event Grid skickar en händelse till en Function-app. Appen hämtar insamlings filen och migrerar data till ett data lager. |
| [Självstudie: Azure Service Bus till Azure Event Grid integrations exempel](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus ämne till en Function-app och en Logic-app. |

## <a name="rest-example-for-put"></a>REST-exempel (för placering)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 
