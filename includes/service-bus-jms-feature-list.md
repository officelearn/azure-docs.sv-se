---
title: inkludera fil
description: inkludera fil
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 9030080d0b8c8e032cb2992a62275efcdb04aabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798145"
---
I följande tabell visas de JMS-funktioner (Java Message Service) som Azure Service Bus för närvarande stöder. Den visar även funktioner som inte stöds.


| Funktion | API |Status |
|---|---|---|
| Köer   | <ul> <li> JMSContext. createQueue (String queueName) </li> </ul>| **Tillåtna** |
| Ämnen   | <ul> <li> JMSContext. createTopic (String topicName) </li> </ul>| **Tillåtna** |
| Tillfälliga köer |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Tillåtna** |
| Tillfälliga ämnen |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Tillåtna** |
| Meddelande tillverkare/<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Tillåtna** |
| Köa webbläsare |<ul> <li> JMSContext. createBrowser (Queue queue) </li> <li> JMSContext. createBrowser (Queue Queue, String messageSelector) </li> </ul> | **Tillåtna** |
| Meddelande förbrukare/ <br/> JMSConsumer | <ul> <li> JMSContext. createConsumer (mål destination) </li> <li> JMSContext. createConsumer (mål destination, sträng messageSelector) </li> <li> JMSContext. createConsumer (mål destination, sträng messageSelector, boolesk nolocal)</li> </ul>  <br/> Det finns för närvarande inte stöd för den lokala datorn | **Tillåtna** |
| Delade varaktiga prenumerationer | <ul> <li> JMSContext. createSharedDurableConsumer (ämne, sträng namn) </li> <li> JMSContext. createSharedDurableConsumer (ämne, sträng namn, String messageSelector) </li> </ul>| **Tillåtna**|
| Delade varaktiga prenumerationer | <ul> <li> JMSContext. createDurableConsumer (ämne, sträng namn) </li> <li> createDurableConsumer (ämne, sträng namn, sträng messageSelector, boolesk nolocal) </li> </ul> <br/> nolocal stöds inte för närvarande och ska vara inställt på falskt | **Tillåtna** |
| Delade icke-varaktiga prenumerationer |<ul> <li> JMSContext. createSharedConsumer (ämne, String sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (ämne, sträng sharedSubscriptionName, sträng messageSelector) </li> </ul> | **Tillåtna** |
| Ej delade icke-varaktiga prenumerationer |<ul> <li> JMSContext. createConsumer (mål destination) </li> <li> JMSContext. createConsumer (mål destination, sträng messageSelector) </li> <li> JMSContext. createConsumer (mål destination, sträng messageSelector, boolesk nolocal) </li> </ul> <br/> nolocal stöds inte för närvarande och ska vara inställt på falskt | **Tillåtna** |
| Meddelande väljare | beror på att klienten har skapats | **Tillåtna** |
| Leverans fördröjning (schemalagda meddelanden) | <ul> <li> JMSProducer. setDeliveryDelay (lång deliveryDelay) </li> </ul>|**Tillåtna**|
| Ett meddelande har skapats |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (serialiserbar objekt) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (sträng text) </li> </ul>| **Tillåtna** |
| Distribuerade transaktioner || Stöds inte |
