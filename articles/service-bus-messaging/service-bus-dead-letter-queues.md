---
title: "Service Bus-köer för obeställbara | Microsoft Docs"
description: "Översikt över Azure Service Bus-köer för obeställbara"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: a82d70e7bf776bf470d14e7f061774ccbb136316
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Översikt över Service Bus-köer för obeställbara

Azure Service Bus-köer och ämnesprenumerationer ger en sekundär plats kö kallas en *kö med olevererade brev* (DQL). Kö för obeställbara meddelanden behöver inte uttryckligen skapas och kan inte tas bort eller annars hanteras oberoende av de huvudsakliga entitet.

Den här artikeln beskriver köer i Service Bus. Mycket av diskussionen visas den [förlorade köer exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) på GitHub.
 
## <a name="the-dead-letter-queue"></a>Kö för obeställbara meddelanden

Syftet med obeställbara meddelanden är att lagra meddelanden som inte levereras till alla mottagare eller meddelanden som inte kunde bearbetas. Meddelanden kan sedan tas bort från DQL och kontrolleras. Ett program kan med hjälp av en operatör korrigera problem och skicka meddelandet, logga in till att det uppstod ett fel och vidta åtgärder. 

Ur ett API och protokoll är i DQL främst liknar en kö, förutom att meddelanden kan endast skickas via förlorade åtgärden i den överordnade entiteten. Dessutom time to live inte observeras, och det går inte att ett meddelande från en DQL för obeställbara. Kö för obeställbara meddelanden har fullständigt stöd för leverans av titt Lås och transaktionell åtgärder.

Observera att det finns ingen automatisk rensning av DQL. Meddelanden ligger kvar i DQL tills du uttryckligen hämta dem från DQL och anropet [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) för obeställbara meddelande.

## <a name="moving-messages-to-the-dlq"></a>Flytta meddelanden till DQL

Det finns flera aktiviteter i Service Bus som medför att meddelanden flyttas till DQL från inom meddelanden motorn sig själv. Ett program kan också explicit flytta meddelanden till DQL. 

När meddelandet har flyttats av Service broker, två egenskaper läggs till i meddelandet eftersom Service broker anropar sin interna version av den [Systemkön](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metod på meddelandet: `DeadLetterReason` och `DeadLetterErrorDescription`.

Program kan definiera egna koder för den `DeadLetterReason` egenskap, men systemet anger du följande värden.

| Villkor | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Alltid |HeaderSizeExceeded |Storlekskvoten för dataströmmen har överskridits. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing och SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Meddelandet har upphört att gälla och har döda lettered. |
| SubscriptionDescription.RequiresSession |Sessions-id är null. |Sessionen aktiverat entitet kan inte meddelandet vars sessions-ID är null. |
| ! kö med olevererade brev |MaxTransferHopCountExceeded |Null |
| Programmet explicit döda oljekategori |Anges av programmet |Anges av programmet |

## <a name="exceeding-maxdeliverycount"></a>Överstiger MaxDeliveryCount

Köer och -prenumerationer har en [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) och [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) egenskap standardvärdet är 10. När ett meddelande som har levererats under ett lås ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), men har antingen explicit avbrutna eller låset har upphört att gälla, meddelandet [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) är stegvis. När [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) överskrider [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), flyttas meddelandet till DQL, ange den `MaxDeliveryCountExceeded` orsakskod.

Det här problemet kan inte inaktiveras, men du kan ange [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) till ett mycket stort antal.

## <a name="exceeding-timetolive"></a>Mer än TimeToLive

När den [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) eller [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnMessageExpiration) egenskap är inställd på **SANT** (standardvärdet är **FALSKT**), alla utgående meddelanden flyttas till DQL, ange den `TTLExpiredException` orsakskod.

Observera att inaktuella meddelanden endast rensas och flyttas till DQL när det finns minst en aktiv mottagaren dra från huvudkön eller prenumeration; Detta är avsiktligt.

## <a name="errors-while-processing-subscription-rules"></a>Fel vid bearbetning av prenumerationsregler

När den [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnFilterEvaluationExceptions) egenskapen är aktiverad för en prenumeration, eventuella fel som inträffar när en prenumeration SQL filterregeln kör fångas i DQL tillsammans med felaktiga meddelandet.

## <a name="application-level-dead-lettering"></a>Programnivå dead-lettering

Förutom funktionerna systembaserade dead-lettering kan program använda DQL uttryckligen avvisa oacceptabel meddelanden. Detta kan inkludera meddelanden som inte kan behandlas korrekt på grund av någon form av systemet, meddelanden som har felaktiga nyttolaster eller meddelanden som avbryts autentiseringen när vissa meddelandenivå säkerhetsprogram används.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Dead-lettering i ForwardTo eller SendVia scenarier

Meddelanden skickas till obeställbara överföringen under följande förhållanden:

- Ett meddelande som passerar genom mer än 3 köer och ämnen som [sammankopplade](service-bus-auto-forwarding.md).
- Målkön eller avsnittet är inaktiverad eller borttagen.
- Målkön eller avsnittet överskrider den maximala entity-storleken.

Om du vill hämta dessa lettered förlorade meddelanden, kan du skapa en mottagare med hjälp av den [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) verktyget metod.

## <a name="example"></a>Exempel

Följande kodavsnitt skapar en mottagare för meddelandet. I receive-slinga för huvudkön koden hämtar meddelandet med [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_Receive_System_TimeSpan_), där du tillfrågas broker omedelbart returnera alla meddelanden som är tillgänglig, eller så återgår med inget resultat. Om koden får ett meddelande visas den omedelbart avbryts det, vilka steg i `DeliveryCount`. När systemet flyttas meddelandet till DQL, huvudkön är tom och slingan avslutas som [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_ReceiveAsync_System_TimeSpan_) returnerar **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för mer information om Service Bus-köer:

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Azure-köer och Service Bus-köer jämfört med](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

