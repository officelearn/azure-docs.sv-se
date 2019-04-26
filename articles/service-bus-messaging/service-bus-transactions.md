---
title: Översikt över transaktionsbearbetning i Azure Service Bus | Microsoft Docs
description: Översikt över Azure Service Bus atomiska transaktioner och skicka via
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2018
ms.author: aschhab
ms.openlocfilehash: a839a4cad824a74bde388317cf3aaddf9c5bd47f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332354"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Översikt över Service Bus transaktionsbearbetning

Den här artikeln beskrivs funktionerna i Microsoft Azure Service Bus transaktion. Mycket av diskussionen illustreras av den [AMQP transaktioner med Service Bus-exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Den här artikeln är begränsad till en översikt över transaktionsbearbetning och *skicka via* funktionen i Service Bus, medan atomiska transaktioner exemplet har en större och mer komplexa omfång.

## <a name="transactions-in-service-bus"></a>Transaktioner i Service Bus

En *transaktion* för att gruppera två eller flera åtgärder i en *körning omfång*. Enligt natur har sådana en transaktion måste se till att alla åtgärder som hör till en viss grupp av åtgärder lyckas eller misslyckas gemensamt. I detta avseende transaktioner som fungerar som en enhet, vilket ofta kallas *Atomicitet*.

Service Bus är en transaktionell asynkron meddelandekö och säkerställer transaktionsintegritet för alla interna åtgärder mot butikerna meddelande. Alla överföringar av meddelanden i Service Bus, till exempel flytta meddelanden till en [obeställbara meddelanden](service-bus-dead-letter-queues.md) eller [automatisk vidarebefordran](service-bus-auto-forwarding.md) är transaktionella meddelanden mellan entiteter. Därför om Service Bus tar emot ett meddelande, har det redan lagras och som är märkt med ett sekvensnummer. Kommer alla överföringar av meddelanden i Service Bus är samordnad åtgärder över entiteter och kommer varken leda till förlust (lyckas källa och mål misslyckas) eller duplicering (källan startar och mål lyckas) för meddelandet.

Service Bus stöder grupperingsåtgärder mot en enskild meddelandeenhet (kö, ämne, prenumeration) inom en transaktion. Exempelvis kan du kan skicka flera meddelanden till en kö från i ett transaktionsomfång och meddelanden kommer bara att förbinder sig att köns log när transaktionen har slutförts.

## <a name="operations-within-a-transaction-scope"></a>Åtgärder i ett transaktionsomfång

De åtgärder som kan utföras i ett transaktionsomfång är följande:

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Send, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

Ta emot operations ingår inte, eftersom det antas att programmet hämtar meddelanden med den [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) läge i några få slinga eller med en [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) motringning, och först då öppnas ett transaktionsomfång för behandlingen av meddelandet.

Disposition av meddelandet (slutförd, avbrott, förlorade, skjuta upp) inträffar sedan inom omfånget för, och beroende på övergripande resultatet av transaktionen.

## <a name="transfers-and-send-via"></a>Överföringar och ”skicka”

Om du vill aktivera transaktionell jourtjänstöverlämnande av data från en kö till en processor och sedan till en annan kö, Service Bus stöder *överföringar*. I en överföringen en avsändare först skickar ett meddelande till en *kön överföring*, och omedelbart flyttar meddelandet till avsedda målkön med samma robust överföring implementeringen i kön överföring som automatisk vidarebefordring funktionen förlitar sig på. Meddelandet strävar aldrig in kön överföring på ett sätt att den blir synlig för kön överföring konsumenter.

Kraften i den här transaktionella funktionen blir tydligt när kön överföring själva är källan till avsändarens inkommande meddelanden. Med andra ord, Service Bus kan överföra meddelandet till målkön ”via” kön överföring när du utför en fullständig (eller skjuta upp, eller förlorade) åtgärden för det inkommande meddelandet, allt i en atomisk åtgärd. 

### <a name="see-it-in-code"></a>Se den i koden

Om du vill konfigurera sådana överföringar, skapar du en avsändaren som riktar sig till målkön via kön överföring. Du kan också ha en mottagare som tar emot meddelanden från den samma kö. Exempel:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

En enkel transaktion sedan använder de här elementen som i följande exempel. Om du vill referera fullständigt exempel, finns det [källkod på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för mer information om Service Bus-köer:

* [Använd Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Länkning av Service Bus-enheter med automatisk vidarebefordran](service-bus-auto-forwarding.md)
* [Automatisk exemplet](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomiska transaktioner med Service Bus-exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure-köer och Service Bus köer jämfört med](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


