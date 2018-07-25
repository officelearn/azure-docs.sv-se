---
title: Schema för Azure Event Grid Service Bus-händelse
description: Beskriver de egenskaper som har angetts för Service Bus-händelser med Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 07/23/2018
ms.author: babanisa
ms.openlocfilehash: 39bf8df69f491aace546386b1b3aabce9ea6c696
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226551"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Händelseschema för Azure Event Grid för Service Bus

Den här artikeln innehåller egenskaperna och schemat för Service Bus-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Följande händelsetyper genererar Service Bus:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Utlöses när det finns aktiva meddelanden i en kö eller prenumeration och inga mottagare lyssnar. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Utlöses när det finns aktiva meddelanden i en kö för obeställbara meddelanden och inga aktiva lyssnare. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visar schemat för aktiva meddelanden med ingen lyssnare händelse:

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

Schemat för en obeställbara kö händelsen liknar:

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

## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| Ämne | sträng | Publisher-definierade sökvägen till ämne för händelsen. |
| Händelsetyp | sträng | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för BLOB storage. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| namespaceName | sträng | Service Bus-namnområdet resursen finns i. |
| requestUri | sträng | URI: N till den eller de specifika prenumeration som genererar händelsen. |
| entityType | sträng | Typ av Service Bus-entiteten som sänder händelser (kö eller prenumeration). |
| Könamn | sträng | Kön med aktiva meddelanden om prenumerera på en kö. Värdet null om du använder ämnen / prenumerationer. |
| topicName | sträng | Avsnittet om Service Bus-prenumerationen med aktiva meddelanden tillhör. Värdet null om du använder en kö. |
| subscriptionName | sträng | Service Bus-prenumerationen med aktiva meddelanden. Värdet null om du använder en kö. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
* Mer information om hur du använder Azure Event Grid med Service Bus finns i den [Service Bus till Event Grid-integration: översikt](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Försök [tar emot Service Bus-händelser med Functions eller Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
