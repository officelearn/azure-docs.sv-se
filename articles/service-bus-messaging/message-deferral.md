---
title: Skjut upp Azure Service Bus | Microsoft Docs
description: Skjut upp leveransen av Service Bus-meddelanden
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: spelluru
ms.openlocfilehash: f9dbd663ce3b15e6a825f0ef73f3dd5d1f5df76b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697551"
---
# <a name="message-deferral"></a>Skjut upp meddelanden

När en kö eller prenumeration klient tar emot ett meddelande om att det är att bearbeta, men för vilka bearbetningen går för närvarande inte på grund av särskilda omständigheter inuti programmet, den har alternativet att ”skjuta upp” hämtning av meddelandet till en senare tidpunkt. Meddelandet finns kvar i kön eller prenumerationen, men det ställs åt sidan.

Uppskjutningsperiod är en funktion som skapats enbart för arbetsflödesbearbetning scenarier. Ramverk för arbetsflödet kan kräva vissa åtgärder som ska bearbetas i en viss ordning och kan behöva vänta bearbetningen av vissa mottagna meddelanden tills föreskrivna tidigare arbete som andra meddelanden informeras om har slutförts.

Ett enkelt färgkodats exempel för det här är en order Bearbetningsordning där betalning visas ett meddelande från en extern betalningsförmedlare i ett system innan den matchande inköpsordern har spritts framifrån store att betjäna-system. I så fall kan fördröja betjäna systemet bearbeta meddelandet betalning tills det finns en order som ska associeras med den. Körningsordning för i realtid kan verkligen vara rätt i rendezvous-scenarier, där meddelanden från olika källor driver ett arbetsflöde framåt, men de återger resultat kan inkommer i fel ordning.

Slutligen uppskjutande hjälper till med omsortering meddelanden från den ordning som ankomst till en order där de kan bearbetas samtidigt som dessa meddelanden på ett säkert sätt i meddelandearkiv för som bearbetning måste skjutas upp.

## <a name="message-deferral-apis"></a>Skjut upp API: er

API: et är [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) eller [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) i .NET Framework-klienter [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) i .NET Standard-klienter och **mesageReceiver.defer** eller **messageReceiver.deferSync** i Java-klient. 

Uppskjutna meddelanden i huvudkön tillsammans med andra aktiva meddelanden (till skillnad från obeställbara meddelanden som bor i en underordnad kö), men de kan inte längre tas emot med hjälp av de vanliga Receive/ReceiveAsync-funktionerna. Uppskjutna meddelanden kan identifieras [bläddring bland meddelanden](message-browsing.md) om förlorar koll på dem för ett program.

Om du vill hämta ett uppskjutet meddelande ägaren ansvarar för att komma ihåg det [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) som den skjuter upp. Någon mottagare som känner sekvensnumret för ett uppskjutet meddelande kan senare ta emot meddelandet uttryckligen med `Receive(sequenceNumber)`.

Om ett meddelande inte kan bearbetas eftersom en viss resurs för hantering av meddelandet är inte tillgänglig för tillfället men meddelandebehandling bör inte pausas summarily, ett sätt att placera meddelandet på sida för ett par minuter är att komma ihåg det  **SequenceNumber** i en [schemalagt meddelande](message-sequencing.md) att publiceras på några minuter och hämta det uppskjutna meddelandet igen när den schemalagda meddelanden anländer. Observera att om en meddelandehanterare är beroende av en databas för alla åtgärder och att databasen är otillgänglig, det bör inte använda uppskjutande, men i stället inaktivera ta emot meddelanden funktionen helt och hållet tills databasen är tillgänglig igen.

Skjuta upp meddelanden påverkar inte förfallodatum för meddelanden, vilket innebär att uppskjutna meddelanden fortfarande går ut vid den ursprungligen schemalagda tiden och sedan flyttas till kön för obeställbara meddelanden, om detta konfigureras.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)