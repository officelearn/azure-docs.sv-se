---
title: Event Hub som en händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Event Hub som händelse hanterare för Azure Event Grid händelser.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: f46b17fdffc870e6afc5f3b0711169db8270a540
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83800447"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event Hub som en händelse hanterare för Azure Event Grid händelser
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar en åtgärd för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Event Hubs** är en av dem. 

Använd **Event Hubs** när din lösning hämtar händelser från Event Grid snabbare än den kan bearbeta händelserna. När händelserna är i en händelsehubben kan ditt program bearbeta händelser från händelsehubben enligt ett eget schema. Du kan skala händelse bearbetningen för att hantera inkommande händelser.

## <a name="tutorials"></a>Självstudier
Se följande exempel: 

|Titel  |Beskrivning  |
|---------|---------|
| [Snabb start: dirigera anpassade händelser till Azure Event Hubs med Azure CLI](custom-event-to-eventhub.md) | Skickar en anpassad händelse till en händelsehubben för bearbetning av ett program. |
| [Resource Manager-mall: skapa ett Event Grid anpassat ämne och skicka händelser till en Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| En Resource Manager-mall som skapar en prenumeration för ett anpassat ämne. Den skickar händelser till en Azure-Event Hubs. |

## <a name="message-properties"></a>Meddelande egenskaper
Om du använder en **Event Hub** som händelse hanterare för händelser från Event Grid anger du följande meddelandehuvuden: 

| Egenskapsnamn | Beskrivning |
| ------------- | ----------- | 
| AEG-prenumeration-namn | Namn på händelse prenumerationen. |
| AEG – antal | <p>Antal försök som har gjorts för händelsen.</p> <p>Exempel: "1"</p> |
| AEG-händelse-Type | <p>Typ av händelse.</p><p> Exempel: "Microsoft. Storage. blobCreated"</p> | 
| AEG – metadata-version | <p>Händelsens metadata-version.</p> <p>Exempel: "1".</p><p> För **Event Grid händelse schema**representerar den här egenskapen metadata-versionen och för **moln händelse schema**, den representerar **Specifikations versionen**. </p>|
| AEG – data version | <p>Händelsens data version.</p><p>Exempel: "1".</p><p>För **Event Grid händelse schema**representerar den här egenskapen data versionen och för **moln händelse schema**, den gäller inte.</p> |
| AEG-utdata-Event-ID | ID för Event Grid händelsen. |

## <a name="rest-examples-for-put"></a>REST-exempel (för placering)


### <a name="event-hub"></a>Händelsehubb

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Event Hub – leverans med hanterad identitet

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 
