---
title: Azure Service Bus som event grid-källa
description: Beskriver de egenskaper som tillhandahålls för Service Bus-händelser med Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 141a0e96071014dc3705d30f72b1a9257737298a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393243"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Azure Service Bus som en händelserutnätskälla

Den här artikeln innehåller egenskaper och schema för Service Bus-händelser.En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md).

## <a name="event-grid-event-schema"></a>Händelseschema för händelserutnät

### <a name="available-event-types"></a>Tillgängliga händelsetyper

Service Bus avger följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesTillgängligMedNoListeners | Utlöses när det finns aktiva meddelanden i en kö eller prenumeration och inga mottagare lyssnar. |
| Microsoft.ServiceBus.DeadletterMessagesTillgängligMedNoListener | Utlöses när det finns aktiva meddelanden i en kö för obeställbara meddelanden och inga aktiva lyssnare. |

### <a name="example-event"></a>Exempel händelse

I följande exempel visas schemat för aktiva meddelanden utan lyssnare händelse:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Schemat för en köhändelse för obeställbara meddelanden är liknande:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för bloblagring. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| namespaceName | sträng | Den servicebussnamnområde som resursen finns i. |
| begärUri | sträng | Uri till den specifika kön eller prenumerationen som avger händelsen. |
| entityType (entityType) | sträng | Typen av Service Bus-enhet som avger händelser (kö eller prenumeration). |
| queueName (queueName) | sträng | Kön med aktiva meddelanden om du prenumererar på en kö. Värde null om du använder ämnen / prenumerationer. |
| topicName (ämnesnamn) | sträng | Ämnet servicebussprenumerationen med aktiva meddelanden tillhör. Värde null om du använder en kö. |
| subscriptionName | sträng | Service Bus-prenumerationen med aktiva meddelanden. Värde null om du använder en kö. |

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner
|Titel  |Beskrivning  |
|---------|---------|
| [Självstudiekurs: Exempel på integrering av Azure Service Bus till Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid skickar meddelanden från Service Bus-avsnittet för att fungera app och logikapp. |
| [Azure Service Bus to Event Grid-integrering](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Översikt över att integrera Service Bus med Event Grid. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
* Mer information om hur du använder Azure Event Grid med Service Bus finns i [översikten över integrering av Service Bus till Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Prova [att ta emot Service Bus-händelser med funktioner eller Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
