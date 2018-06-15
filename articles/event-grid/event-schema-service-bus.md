---
title: Azure Service Bus för händelsen rutnätet Händelseschema
description: Beskriver de egenskaper som har angetts för Service Bus-händelser med Azure händelse rutnätet
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 02/21/2018
ms.author: babanisa
ms.openlocfilehash: 991679eeb0f7c98606133750b193a5895f39178f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34303325"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Azure händelse rutnätet Händelseschema för Service Bus

Den här artikeln innehåller egenskaperna och schemat för Service Bus-händelser. En introduktion till händelse scheman, se [Azure händelse rutnätet Händelseschema](event-schema.md).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Service Bus skickar följande händelse:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Utlöses när det finns aktiva meddelanden i en kö eller prenumerationen och inga mottagare som lyssnar på. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Utlöses när det finns aktiva meddelanden i en kö med olevererade brev och inga aktiva lyssnare. |

## <a name="example-event"></a>Exempel-händelse

I följande exempel visas schemat för en aktiv meddelanden med ingen lyssnare händelse:

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

Schemat för en händelse för obeställbara kön är ungefär:

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
| Avsnittet | sträng | Fullständigt labbresurs sökvägen till händelsekällan. Det här fältet är skrivskyddat. Händelsen rutnätet innehåller det här värdet. |
| Ämne | sträng | Publisher-definierade sökvägen till ämnet för händelsen. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekälla. |
| EventTime | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | BLOB storage händelsedata. |
| dataVersion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversionen. |
| metadataVersion | sträng | Schemaversion för händelsemetadata. Händelsen rutnätet definierar schemat för egenskaperna på den översta nivån. Händelsen rutnätet innehåller det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| nameSpaceName | sträng | Service Bus-namnrymd resursen finns i. |
| requestUri | sträng | URI: N till den eller de specifika prenumeration avger händelsen. |
| EntityType | sträng | Typ av Service Bus-entitet avger händelser (kön eller prenumeration). |
| Könamn | sträng | Kön med aktiva meddelanden om prenumerera på en kö. Värdet null om avsnitt om att använda / prenumerationer. |
| topicName | sträng | Avsnittet Service Bus-prenumerationen med aktiva meddelanden tillhör. Värdet null om du använder en kö. |
| SubscriptionName | sträng | Service Bus-prenumeration med aktiva meddelanden. Värdet null om du använder en kö. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)
* Mer information om hur du skapar en prenumeration på Azure händelse rutnätet finns [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).
* Mer information om hur du använder Azure Event rutnät med Service Bus finns i [Service Bus händelse rutnätet integreringsöversikt över](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Försök [ta emot händelser för Service Bus med funktioner eller Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
