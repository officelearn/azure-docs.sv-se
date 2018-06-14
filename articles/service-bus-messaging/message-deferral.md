---
title: Azure Service Bus meddelandet avstängning | Microsoft Docs
description: Skjut upp leverans av Service Bus-meddelanden
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
ms.author: sethm
ms.openlocfilehash: bece2be88a020610dfd3d22f15f7d276d99bb153
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196983"
---
# <a name="message-deferral"></a>Meddelandet avstängning

När en kö eller prenumeration klient tar emot ett meddelande om att det är att bearbeta, men för bearbetningen går för närvarande inte på grund av särskilda omständigheter i programmet, har möjlighet att ”vänta” hämtning av meddelandet till ett senare tillfälle. Meddelandet finns kvar i kön eller prenumeration, men den reserveras.

Avstängning är en funktion som skapats enbart för arbetsflödesbearbetning scenarier. Arbetsflödet ramverk kan kräva att vissa åtgärder som ska bearbetas i en viss ordning och kan behöva vänta bearbetning av vissa mottagna meddelanden tills föreskrivna tidigare arbete som andra meddelanden informeras om har slutförts.

Ett enkelt exempel exempel för det här är en order Bearbetningsordning där betalningen visas ett meddelande från en extern betalningsförmedlare i ett system med innan matchande inköpsordern har spritts framifrån store till automatiserat system. Automatiserat system kan i så fall kan skjuta upp bearbetning betalning meddelandet tills det finns en ordning som ska associeras med den. Ordning realtid utförs kanske verkligen är korrekt i rendezvous-scenarier där meddelanden från olika källor enhet ett arbetsflöde framåt, men meddelanden reflektion resultat kan tas emot i fel ordning.

Slutligen underlättar avstängning nytt beställning av meddelanden från ankomst ordning i en ordning som de kan bearbetas samtidigt dessa meddelanden på ett säkert sätt i meddelandearkivet för vilka bearbetning måste skjutas upp.

## <a name="message-deferral-apis"></a>Meddelandet avstängning API: er

API: et är [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) eller [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) i .NET Framework-klienten [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) i Standard .NET-klienten och **mesageReceiver.defer** eller **messageReceiver.deferSync** i Java-klienten. 

Uppskjuten meddelanden ligger kvar i huvudkön tillsammans med andra aktiva meddelanden (till skillnad från förlorade meddelanden som bor i en underordnad kö), men de kan inte längre tas emot med hjälp av de vanliga ta emot/ReceiveAsync-funktionerna. Uppskjuten meddelanden kan identifieras [meddelandet surfning](message-browsing.md) om ett program förlorar reda på dem.

Om du vill hämta en uppskjuten meddelandet ägaren ansvarar för att komma ihåg det [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) som den skjuts upp. Alla mottagare som känner sekvensnumret för ett meddelande om uppskjuten senare kan ta emot meddelandet explicit med `Receive(sequenceNumber)`.

Om ett meddelande inte kan bearbetas eftersom en viss resurs för hantering av meddelandet är inte tillgänglig för tillfället men meddelandebehandling bör inte pausas summarily, är ett sätt att placera meddelandet på sidan om en stund att komma ihåg det  **SequenceNumber** i en [schemalagda meddelandet](message-sequencing.md) läggas upp på några minuter och hämtas uppskjutna meddelandet igen när den schemalagda meddelanden tas emot. Observera att om en meddelandehanteraren är beroende av en databas för alla åtgärder och att databasen är inte tillgänglig för tillfället, den bör inte använda avstängning, men i stället pausa ta emot meddelanden helt och hållet tills databasen är tillgänglig igen.

Vänta meddelanden påverkar inte meddelandet upphör att gälla, vilket innebär att uppskjutna meddelanden fortfarande ut vid den ursprungligen schemalagda tiden och sedan flyttas till kön för obeställbara, om detta konfigureras.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)