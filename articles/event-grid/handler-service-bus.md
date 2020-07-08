---
title: Service Bus köer och ämnen som händelse hanterare för Azure Event Grid händelser
description: Beskriver hur du kan använda Service Bus köer och ämnen som händelse hanterare för Azure Event Grid händelser.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9e1c9aa664065371595ed34a3af28330bd7e0db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83798869"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus köer och ämnen som händelse hanterare för Azure Event Grid händelser
En händelse hanterare är den plats där händelsen skickas. Hanteraren vidtar ytterligare åtgärder för att bearbeta händelsen. Flera Azure-tjänster konfigureras automatiskt för att hantera händelser och **Azure Service Bus** är en av dem. 

Du kan använda en tjänst kö eller ett ämne som hanterare för händelser från Event Grid. 

## <a name="service-bus-queues"></a>Service Bus-köer
Du kan dirigera händelser i Event Grid direkt till Service Bus köer för användning i buffert-eller kommando & kontroll scenarier i företags program.

När du skapar en händelse prenumeration i Azure Portal väljer du **Service Bus kö** som typ av slut punkt och klickar sedan på **Välj en slut punkt** för att välja en Service Bus kö.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Använda CLI för att lägga till en Service Bus Queue-hanterare

I följande exempel prenumererar och ansluter ett event Grid-ämne till en Service Bus kö för Azure CLI:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus ämnen

Du kan dirigera händelser i Event Grid direkt till Service Bus ämnen för att hantera Azure system Events med Service Bus ämnen, eller för kommando & kontroll meddelande scenarier.

När du skapar en händelse prenumeration i Azure Portal väljer du **Service Bus ämne** som typ av slut punkt och klickar sedan på **Välj och slut punkt** för att välja ett Service Bus ämne.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Använda CLI för att lägga till en Service Bus ämnes hanterare

I följande exempel prenumererar och ansluter ett event Grid-ämne till en Service Bus kö för Azure CLI:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>Meddelande egenskaper
Om du använder ett **Service Bus ämne eller en kö** som händelse hanterare för händelser från Event Grid anger du följande meddelandehuvuden: 

| Egenskapsnamn | Beskrivning |
| ------------- | ----------- | 
| AEG-prenumeration-namn | Namn på händelse prenumerationen. |
| AEG – antal | <p>Antal försök som har gjorts för händelsen.</p> <p>Exempel: "1"</p> |
| AEG-händelse-Type | <p>Typ av händelse.</p><p> Exempel: "Microsoft. Storage. blobCreated"</p> | 
| AEG – metadata-version | <p>Händelsens metadata-version.</p> <p>Exempel: "1".</p><p> För **Event Grid händelse schema**representerar den här egenskapen metadata-versionen och för **moln händelse schema**, den representerar **Specifikations versionen**. </p>|
| AEG – data version | <p>Händelsens data version.</p><p>Exempel: "1".</p><p>För **Event Grid händelse schema**representerar den här egenskapen data versionen och för **moln händelse schema**, den gäller inte.</p> |

## <a name="message-headers"></a>Meddelandehuvuden
När du skickar en händelse till en Service Bus kö eller ämne som ett Service Broker-meddelande, är det asynkrona `messageid` meddelandet **händelse-ID**.

Händelse-ID: t kommer att behållas i omleverans av händelsen så att du kan undvika dubbla leveranser genom att aktivera **dubblettidentifiering** på Service Bus-entiteten. Vi rekommenderar att du aktiverar varaktigheten för dubblettidentifiering på Service Bus entiteten till antingen TTL (Time-to-Live) för händelsen eller Max värdet för återförsök, beroende på vilket som är längre.

## <a name="rest-examples-for-put"></a>REST-exempel (för placering)

### <a name="service-bus-queue"></a>Service Bus-kö

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus kö – leverans med hanterad identitet

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
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Service Bus-ämne

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus ämne – leverans med hanterad identitet

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över händelse hanterare som stöds finns i artikeln [händelse hanterare](event-handlers.md) . 
