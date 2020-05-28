---
title: Översikt över transaktions bearbetning i Azure Service Bus
description: Den här artikeln innehåller en översikt över transaktions bearbetning och funktionen Skicka via i Azure Service Bus.
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
ms.openlocfilehash: f79d0e917ba741e72e2bbecd4a1f94a4c99e5393
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996065"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Översikt över Service Bus transaktions bearbetning

I den här artikeln beskrivs transaktions funktionerna i Microsoft Azure Service Bus. En stor del av diskussionen illustreras av [AMQP-transaktioner med Service Bus exemplet](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Den här artikeln är begränsad till en översikt över transaktions bearbetning och funktionen *Skicka via* i Service Bus, medan exemplet för atom transaktioner är bredare och mer komplext i omfånget.

## <a name="transactions-in-service-bus"></a>Transaktioner i Service Bus

En *transaktion* grupper två eller flera åtgärder tillsammans i ett *körnings område*. En sådan transaktion måste efter beskaffenhet säkerställa att alla åtgärder som hör till en specifik grupp av åtgärder antingen lyckas eller Miss lyckas gemensamt. I detta hänseende fungerar transaktionerna som en enhet, vilket ofta kallas *Atomicitet*.

Service Bus är en transaktions meddelande Broker och säkerställer transaktions integriteten för alla interna åtgärder mot sina meddelande arkiv. Alla överföringar av meddelanden i Service Bus, till exempel att flytta meddelanden till en [kö för obeställbara](service-bus-dead-letter-queues.md) meddelanden eller [automatisk vidarebefordran](service-bus-auto-forwarding.md) av meddelanden mellan entiteter, är transaktionella. Om Service Bus accepterar ett meddelande har det redan lagrats och märkts med ett sekvensnummer. Från och med, är alla meddelande överföringar inom Service Bus koordinerade åtgärder över entiteter och kommer inte att leda till förlust (källan lyckas och målet Miss lyckas) eller för att duplicera (källan Miss lyckas och målet lyckas) för meddelandet.

Service Bus stöder grupperingsåtgärder mot en enskild meddelandeenhet (kö, ämne, prenumeration) inom en transaktion. Du kan till exempel skicka flera meddelanden till en kö inifrån ett transaktions omfång, och meddelandena skickas bara till köns logg när transaktionen har slutförts.

## <a name="operations-within-a-transaction-scope"></a>Åtgärder inom ett transaktions omfång

De åtgärder som kan utföras inom ett transaktions omfång är följande:

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: `Send` , `SendAsync` , `SendBatch` ,`SendBatchAsync`
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**:,,,,,,, `Complete` `CompleteAsync` `Abandon` `AbandonAsync` `Deadletter` `DeadletterAsync` `Defer` `DeferAsync` `RenewLock` ,`RenewLockAsync` 

Receive-åtgärder ingår inte, eftersom det förutsätts att programmet hämtar meddelanden med hjälp av läget [PeekLock ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) , i vissa mottagnings slingor eller med ett [motringningen OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) -motanrop, och öppnar bara ett transaktions omfång för bearbetning av meddelandet.

Dispositionen av meddelandet (fullständig, överge, obeställbara meddelanden, uppskjutning) sker inom omfånget för och är beroende av transaktionens övergripande resultat.

## <a name="transfers-and-send-via"></a>Överföringar och "Skicka via"

Om du vill aktivera transaktionell överlämnande av data från en kö till en processor, och sedan till en annan kö, Service Bus stöder *överföringar*. I en överförings åtgärd skickar en sändare först ett meddelande till en *överförings kö*, och överförings kön flyttar omedelbart meddelandet till den avsedda målkön med samma robusta överförings implementering som funktionen för vidarebefordran är beroende av. Meddelandet allokeras aldrig till överförings köns logg på ett sätt som är synligt för överförings köns konsumenter.

Kraften i denna transaktions funktion blir tydlig när själva överförings kön är källan till avsändarens indatameddelande. Med andra ord kan Service Bus överföra meddelandet till målkön "via" överförings kön, samtidigt som en fullständig (eller överskjutande eller obeställbara meddelanden) utförs i Indataporten, allt i en atomisk åtgärd. 

### <a name="see-it-in-code"></a>Se det i kod

Om du vill konfigurera sådana överföringar skapar du en meddelande avsändare som är riktad mot målkön via överförings kön. Du har också en mottagare som hämtar meddelanden från samma kö. Ett exempel:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

En enkel transaktion använder sedan dessa element, som i följande exempel. Om du vill referera till det fullständiga exemplet, referera till [käll koden på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

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

## <a name="timeout"></a>Timeout
En transaktions tids gräns efter 2 minuter. Timern för transaktioner startar när den första åtgärden i transaktionen börjar. 

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns mer information om Service Bus köer:

* [Använd Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Kedja Service Bus entiteter med vidarebefordran](service-bus-auto-forwarding.md)
* [Exempel på autoforward](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomiska transaktioner med Service Bus exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure-köer och Service Bus köer jämförs](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


