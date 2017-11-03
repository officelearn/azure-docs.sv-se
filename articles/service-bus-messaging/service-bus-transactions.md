---
title: "Översikt över transaktionsbearbetning i Azure Service Bus | Microsoft Docs"
description: "Översikt över Azure Service Bus atomiska transaktioner och skicka via"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: sethm
ms.openlocfilehash: dac0bf117e56c788adf46bc0647f684eccf8cf42
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="overview-of-service-bus-transaction-processing"></a>Översikt över Service Bus transaktionsbearbetning
Den här artikeln beskriver funktionerna i Azure Service Bus transaktion. Mycket av diskussionen visas den [atomiska transaktioner med Service Bus-exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). Den här artikeln är begränsad till en översikt över transaktionsbearbetning och *skicka via* funktion i Service Bus, medan atomiska transaktioner exempel större och mer komplexa omfång.

## <a name="transactions-in-service-bus"></a>Transaktioner i Service Bus
En [ *transaktion* ](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) för att gruppera två eller flera åtgärder i en *körningen scope*. Enligt natur har sådana en transaktion måste se till att alla åtgärder som tillhör en viss grupp av åtgärder lyckas eller misslyckas gemensamt. I detta avseende transaktioner ska fungera som en enhet, som ofta kallas *odelbarhet*. 

Service Bus är en transaktionsmeddelande broker och säkerställer transaktionella integritet för alla interna åtgärder mot dess meddelanden sparas. Alla överföringar av meddelanden i Service Bus, till exempel flytta meddelanden till en [kö med olevererade brev](service-bus-dead-letter-queues.md) eller [automatisk vidarebefordran](service-bus-auto-forwarding.md) är transaktionella meddelanden mellan entiteter. Därför om Service Bus tar emot ett meddelande, har det redan lagras och märkta med ett sekvensnummer. Därefter överföringar av alla meddelanden i Service Bus är samordnade åtgärder mellan enheter och kommer varken leda till förlust av (lyckas källa och mål misslyckas) eller duplicering (misslyckas källa och mål lyckas) för meddelandet.

Service Bus stöder gruppering åtgärder mot en enskild meddelandeentitet (kö, ämne, prenumeration) omfattas av en transaktion. Exempelvis kan du skicka flera meddelanden till en kö från inom ett transaktionsomfång och meddelandena som ska allokeras till köns loggen när transaktionen har slutförts.

## <a name="operations-within-a-transaction-scope"></a>Åtgärder i ett transaktions-scope
De åtgärder som kan utföras inom ett transaktionsomfång är följande:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: skicka, SendAsync SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: klar CompleteAsync, avbryta, AbandonAsync, Systemkön, DeadletterAsync, skjuta upp, DeferAsync, RenewLock, RenewLockAsync 

Ta emot operations ingår inte i listan, eftersom det antas att programmet hämtar meddelanden med hjälp av den [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) läge i några få loop eller med en [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) motringning och endast sedan öppnar ett transaktions-scope för bearbetning av meddelandet.

Disposition av meddelandet (slutförd, avbryta, obeställbara, skjuta upp) sker sedan inom omfånget för, och beroende på övergripande resultatet av transaktionen.

## <a name="transfers-and-send-via"></a>Överföringar och ”skicka”
Om du vill aktivera transaktionella övergång av data från en kö till en processor och sedan till en annan kö, Service Bus stöder *överföringar*. I en överföringen en avsändare först skickar ett meddelande till en *överföringskön*, och överföringskön omedelbart flyttas meddelandet till kön avsedda mål med samma robust överföring implementering som automatisk vidarebefordring funktionen bygger på. Meddelandet har aldrig allokeras till överföringskön logga in så att den blir synlig för överföringskön konsumenter.

Kraften i funktionen transaktionella blir tydligt när köns överföringen är källan till avsändarens inkommande meddelanden. Med andra ord Service Bus kan överföra meddelandet till målkön ”via” överföringskön, när du utför en fullständig (eller skjuta upp, eller förlorade) åtgärden på Indatameddelandet i en atomisk åtgärd. 

### <a name="see-it-in-code"></a>Se den i koden
Om du vill konfigurera överföringarna skapa avsändarens som riktar sig till målkön via överföringskön. Du kan också ha en mottagare som tar emot meddelanden från samma kön. Exempel:

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

En enkel transaktion sedan använder de här elementen som i följande exempel:

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för mer information om Service Bus-köer:

* [Använd Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Länkning Service Bus-entiteter med automatisk vidarebefordring](service-bus-auto-forwarding.md)
* [Automatisk vidarebefordring exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomiska transaktioner med Service Bus-exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure-köer och Service Bus-köer jämfört med](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


