---
title: Översikt över transaktionsbearbetning i Azure Service Bus
description: Den här artikeln ger dig en översikt över transaktionsbearbetning och skicka via-funktionen i Azure Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 22744ecbced40b3195f4d047227b1e2a37228102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260909"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Översikt över bearbetning av Service Bus-transaktioner

I den här artikeln beskrivs transaktionsfunktionerna i Microsoft Azure Service Bus. Mycket av diskussionen illustreras av [AMQP-transaktioner med servicebussprovet](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Den här artikeln är begränsad till en översikt över transaktionsbearbetning och *funktionen skicka via* i Service Bus, medan exemplet Med atomtransaktioner är bredare och mer komplext i omfattning.

## <a name="transactions-in-service-bus"></a>Transaktioner i servicebuss

En *transaktion* grupperar två eller flera operationer tillsammans i ett *körningsomfång*. En sådan transaktion måste till sin natur säkerställa att alla transaktioner som tillhör en viss grupp av transaktioner antingen lyckas eller misslyckas gemensamt. I detta avseende fungerar transaktioner som en enhet, som ofta kallas *atomicitet*.

Service Bus är en transaktionsmeddelandemäklare och säkerställer transaktionsintegritet för alla interna åtgärder mot sina meddelandelager. Alla överföringar av meddelanden i Service Bus, till exempel att flytta meddelanden till en [kö för obeställbara meddelanden](service-bus-dead-letter-queues.md) eller automatisk [vidarebefordran](service-bus-auto-forwarding.md) av meddelanden mellan entiteter, är transaktionella. Om Service Bus accepterar ett meddelande har det redan lagrats och märkts med ett sekvensnummer. Från och med då är alla meddelandeöverföringar inom Service Bus samordnade åtgärder mellan entiteter och leder varken till förlust (källan lyckas och målet misslyckas) eller till duplicering (källan misslyckas och målet lyckas) av meddelandet.

Service Bus stöder grupperingsåtgärder mot en enskild meddelandeenhet (kö, ämne, prenumeration) inom en transaktion. Du kan till exempel skicka flera meddelanden till en kö från ett transaktionsomfång, och meddelandena kommer bara att vara engagerade i köns logg när transaktionen har slutförts.

## <a name="operations-within-a-transaction-scope"></a>Transaktioner inom ett transaktionsomfång

De åtgärder som kan utföras inom ett transaktionsomfång är följande:

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Send, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Komplett, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

Mottagningsåtgärder ingår inte, eftersom det antas att programmet hämtar meddelanden med hjälp av läget [ReceiveMode.PeekLock,](/dotnet/api/microsoft.azure.servicebus.receivemode) inuti vissa mottagningsloopar eller med en [OnMessage-motringning,](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) och först därefter öppnas ett transaktionsomfång för bearbetning av meddelandet.

Dispositionen av meddelandet (fullständig, överge, död bokstav, skjuta upp) sker sedan inom ramen för, och beroende av, det totala resultatet av transaktionen.

## <a name="transfers-and-send-via"></a>Överföringar och "skicka via"

Om du vill aktivera transaktionsöverlämning av data från en kö till en behandlare och sedan till en annan kö stöder Service Bus *överföringar*. I en överföringsåtgärd skickar en avsändare först ett meddelande till en *överföringskö*och överföringskön flyttar omedelbart meddelandet till den avsedda målkön med samma robusta överföringsimplementering som funktionen för automatisk vidarebefordran är beroende av. Meddelandet har aldrig åtagit sig att överföra kön logg på ett sätt som det blir synligt för överföringskön konsumenter.

Kraften i den här transaktionsfunktionen blir uppenbar när själva överföringskön är källan till avsändarens indatameddelanden. Med andra ord kan Service Bus överföra meddelandet till målkön "via" överföringskön, när du utför en fullständig (eller skjuta upp, eller obeställd) åtgärd på indatameddelandet, allt i en atomåtgärd. 

### <a name="see-it-in-code"></a>Se den i kod

Om du vill ställa in sådana överföringar skapar du en meddelandeavsändare som riktar sig till målkön via överföringskön. Du har också en mottagare som hämtar meddelanden från samma kö. Ett exempel:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

En enkel transaktion använder sedan dessa element, som i följande exempel. Om du vill referera till det fullständiga exemplet läser du [källkoden på GitHub:](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)

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

Mer information om servicebussköer finns i följande artiklar:

* [Så här använder du servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Kedja servicebussentiteter med automatisk vidarebefordran](service-bus-auto-forwarding.md)
* [Exempel på automatisk vidarebefordran](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomtransaktioner med servicebussprov](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure-köer och servicebussköer jämförs](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


