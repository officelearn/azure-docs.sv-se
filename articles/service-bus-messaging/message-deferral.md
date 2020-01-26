---
title: Azure Service Bus-meddelande uppskjutande
description: I den här artikeln förklaras hur du uppskjuta leverans av Azure Service Bus meddelanden. Meddelandet finns kvar i kön eller prenumerationen, men det ställs åt sidan.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 249cf7414143f59540d198bb460d8b215f6a7664
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756359"
---
# <a name="message-deferral"></a>Skjut upp meddelanden

När en kö eller en prenumerations klient får ett meddelande om att det inte är möjligt att bearbeta den, men för vilken bearbetning inte är möjlig på grund av särskilda omständigheter i programmet, har den alternativet "uppskjuta" hämtning av meddelandet till en senare punkt. Meddelandet finns kvar i kön eller prenumerationen, men det ställs åt sidan.

Uppskjutande är en funktion som specifikt skapas för arbets flödes bearbetnings scenarier. Arbets flödes ramverk kan kräva att vissa åtgärder bearbetas i en viss ordning och kan behöva skjuta upp bearbetningen av vissa mottagna meddelanden tills före skrivet tidigare arbete som informeras av andra meddelanden har slutförts.

Ett enkelt exempel på exempel är en order bearbetnings ordning där ett betalnings meddelande från en extern betalnings leverantör visas i ett system innan den matchande inköps ordern har spridits från Store till uppfyllande system. I så fall kan uppfyllelse systemet skjuta upp bearbetningen av betalnings meddelandet tills det finns en order som associeras med. I Rendezvous-scenarier där meddelanden från olika källor driver ett arbets flöde framåt, kan körnings ordningen i real tid vara korrekt, men meddelanden som återspeglar resultatet kan komma att tas emot.

I slut ändan hjälper uppbelastningen att sortera om meddelanden från order införseln i en ordning där de kan bearbetas, samtidigt som de lämnar meddelandena på ett säkert sätt i meddelande arkivet där bearbetningen måste skjutas upp.

## <a name="message-deferral-apis"></a>API: er för avstängning av meddelande

API: et är [BrokeredMessage. Överlåt](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) eller [BrokeredMessage. DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) i .NET Framework-klienten, [MessageReceiver. DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) i .net standard-klienten och [IMessageReceiver. Överlåt](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) eller [IMessageReceiver. DeferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) i Java-klienten. 

Uppskjutna meddelanden finns kvar i huvud kön tillsammans med alla andra aktiva meddelanden (till skillnad från meddelanden med obeställbara meddelanden som bor i en underkö), men de kan inte längre tas emot med hjälp av de vanliga Receive-/ReceiveAsync-funktionerna. Uppskjutna meddelanden kan upptäckas via [meddelande bläddring](message-browsing.md) om ett program förlorar sitt spår.

För att hämta ett uppskjutet meddelande ansvarar dess ägare för att komma ihåg [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) när den skjuts upp. Alla mottagare som känner till sekvensnumret i ett uppskjutet meddelande kan senare ta emot meddelandet explicit med `Receive(sequenceNumber)`. För köer kan du använda [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient), ämnes prenumerationer använder [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient).

Om ett meddelande inte kan bearbetas på grund av att en viss resurs för hantering av meddelandet inte är tillgänglig för tillfället, men meddelande bearbetningen inte ska göras sammanfattas, är ett sätt att lägga till meddelandet på sidan i några minuter att komma ihåg att **SequenceNumber** i ett [schemalagt meddelande](message-sequencing.md) som ska publiceras på några minuter och sedan hämta det uppskjutna meddelandet när det schemalagda meddelandet anländer. Om en meddelande hanterare är beroende av en databas för alla åtgärder och databasen är tillfälligt otillgänglig, bör den inte använda uppskjutningen, utan att i stället pausa att ta emot meddelanden helt tills databasen är tillgänglig igen.


## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
