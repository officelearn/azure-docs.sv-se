---
title: Azure Service Bus - meddelande uppskov
description: I den här artikeln beskrivs hur du skjuter upp leverans av Azure Service Bus-meddelanden. Meddelandet finns kvar i kön eller prenumerationen, men det ställs åt sidan.
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
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538404"
---
# <a name="message-deferral"></a>Skjut upp meddelanden

När en kö- eller prenumerationsklient får ett meddelande som den är villig att bearbeta, men för vilken bearbetning för närvarande inte är möjlig på grund av särskilda omständigheter i programmet, har den möjlighet att "skjuta upp" hämtning av meddelandet till en senare punkt. Meddelandet finns kvar i kön eller prenumerationen, men det ställs åt sidan.

Uppskov är en funktion som skapats speciellt för arbetsflödesbearbetningsscenarier. Arbetsflödesramverk kan kräva att vissa åtgärder bearbetas i en viss ordning och kan behöva skjuta upp bearbetningen av vissa mottagna meddelanden tills föreskrivet tidigare arbete som har informerats av andra meddelanden har slutförts.

Ett enkelt belysande exempel är en orderbearbetningssekvens där ett betalningsmeddelande från en extern betalningsleverantör visas i ett system innan den matchande inköpsordern har spridits från butiksfronten till uppfyllelsesystemet. I så fall kan uppfyllelsesystemet skjuta upp behandlingen av betalningsanmälan tills det finns en order som den ska associeras med. I rendezvous scenarier, där meddelanden från olika källor driva ett arbetsflöde framåt, kan realtidskörningsordern verkligen vara korrekt, men de meddelanden som återspeglar resultaten kan komma i fel ordning.

I slutändan, uppskov hjälpmedel för att ordna meddelanden från ankomstordern till en ordning där de kan behandlas, samtidigt som dessa meddelanden säkert i meddelandelagret för vilka behandlingen måste skjutas upp.

## <a name="message-deferral-apis"></a>Api:er för att skjuta upp meddelanden

API:et är [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) eller [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) i .NET Framework-klienten, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) i .NET Standard-klienten och [IMessageReceiver.defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) eller [IMessageReceiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) i Java-klienten. 

Uppskjutna meddelanden finns kvar i huvudkön tillsammans med alla andra aktiva meddelanden (till skillnad från meddelanden med obeställbara meddelanden som finns i en underkö), men de kan inte längre tas emot med hjälp av de vanliga funktionerna Receive/ReceiveAsync. Uppskjutna meddelanden kan upptäckas via [meddelandebläddring](message-browsing.md) om ett program förlorar reda på dem.

För att hämta ett uppskjutet meddelande är dess ägare ansvarig för att komma ihåg [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) när det skjuter upp det. Alla mottagare som känner till sekvensnumret för ett uppskjutet meddelande kan senare ta emot meddelandet uttryckligen med `Receive(sequenceNumber)`.

Om ett meddelande inte kan bearbetas eftersom en viss resurs för hantering av meddelandet inte är tillgänglig för tillfället, men meddelandebearbetningen inte bör avbrytas summariskt, är ett sätt att placera meddelandet på sidan i några minuter att komma ihåg **SequenceNumber** i ett [schemalagt meddelande](message-sequencing.md) som ska publiceras på några minuter och hämta det uppskjutna meddelandet igen när det schemalagda meddelandet anländer. Om en meddelandehanterare är beroende av en databas för alla åtgärder och databasen inte är tillgänglig för tillfället bör den inte använda uppskov, utan i stället avbryta mottagandet av meddelanden helt och hållet tills databasen är tillgänglig igen.


## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
