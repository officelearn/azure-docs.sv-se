---
title: Service Bus-köer för obeställbara | Microsoft Docs
description: Översikt över Azure Service Bus-köer för obeställbara meddelanden
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 0364304a203e03faf69868174a45cb41850ce112
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713970"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Översikt över Service Bus-köer för obeställbara meddelanden

Azure Service Bus-köer och ämnesprenumerationer ger en sekundära underordnade kö, kallas en *obeställbara meddelanden* (DLQ). Obeställbara meddelanden behöver inte uttryckligen skapas och kan inte tas bort eller annars hanteras oberoende av den huvudsakliga entiteten.

Den här artikeln beskriver köer i Service Bus. Mycket av diskussionen illustreras av den [förlorade köer exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) på GitHub.
 
## <a name="the-dead-letter-queue"></a>Obeställbara meddelanden

Syftet med obeställbara meddelanden är att lagra meddelanden som inte kan levereras till någon mottagare eller meddelanden som inte kunde bearbetas. Meddelanden kan sedan tas bort från DLQ och kontrolleras. Ett program kan med hjälp av en operatör åtgärda problem och skicka meddelandet, loggar det faktum att det uppstod ett fel och vidta åtgärder. 

Ur ett API och protokoll är DLQ huvudsakligen liknar en kö, förutom att meddelanden kan endast skickas via åtgärden för obeställbara meddelanden i den överordnade entiteten. Dessutom time-to-live inte observeras, och du kan inte förlorade ett meddelande från en DLQ. Obeställbara meddelanden har fullständigt stöd för leverans av peek-lock och transaktionella åtgärder.

Observera att det finns ingen automatisk rensning av DLQ. Meddelanden som ligger kvar i DLQ tills du uttryckligen hämta dem från DLQ och anropa [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) på obeställbara meddelanden.

## <a name="moving-messages-to-the-dlq"></a>Flytta meddelanden till DLQ

Det finns flera aktiviteter i Service Bus som orsakar meddelanden för att få push-överfört till DLQ från inom den asynkrona motorn. Ett program kan också uttryckligen flytta meddelanden till DLQ. 

När meddelandet har flyttats av Service broker, två egenskaper läggs till i meddelandet som den asynkrona meddelandekön anropar sin interna version av den [Systemkön](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) metod för meddelandet: `DeadLetterReason` och `DeadLetterErrorDescription`.

Program kan definiera egna koder för den `DeadLetterReason` egenskapen, men systemet anger du följande värden.

| Tillstånd | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Alltid |HeaderSizeExceeded |Storlekskvot för den här strömmen har överskridits. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing och SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Meddelandet har gått ut och har död lettered. |
| SubscriptionDescription.RequiresSession |Sessions-id är null. |Sessionen aktiverat entitet tillåter inte att ett meddelande som vars sessions-ID är null. |
| ! kö för obeställbara |MaxTransferHopCountExceeded |Null |
| Programmet explicit död oljekategori |Anges av program |Anges av program |

## <a name="exceeding-maxdeliverycount"></a>Exceeding MaxDeliveryCount

Köer och prenumerationer har en [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) och [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) egenskap standardvärdet är 10. När ett meddelande har levererats under ett lås ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), men har antingen uttryckligen övergivna eller låset har upphört att gälla, meddelandet [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) är stegvis. När [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) överskrider [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), meddelandet flyttas till DLQ, ange den `MaxDeliveryCountExceeded` orsakskod.

Det här beteendet kan inte inaktiveras, men du kan ange [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) till ett stort antal.

## <a name="exceeding-timetolive"></a>Överstiger TimeToLive

När den [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) är inställd på **SANT** (standardvärdet är **FALSKT**), alla utgående meddelanden flyttas till DLQ, ange den `TTLExpiredException` orsakskod.

Observera att utgångna meddelanden endast rensas och flyttas till DLQ när det finns minst en aktiv mottagare hämta från den huvudsakliga kö eller prenumeration; att beteendet är avsiktligt.

## <a name="errors-while-processing-subscription-rules"></a>Fel vid bearbetning av prenumerationsregler

När den [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) egenskapen är aktiverad för en prenumeration, eventuella fel som uppstår när en prenumeration SQL-filterregeln körs samlas i DLQ längs med felaktiga meddelandet.

## <a name="application-level-dead-lettering"></a>Programnivå dead-lettering

Program kan använda DLQ uttryckligen avvisa oacceptabla meddelanden förutom systemdefinierade dead-lettering funktioner. Detta kan inkludera meddelanden som inte bearbetas korrekt på grund av alla slags systemproblem, meddelanden som har felaktiga nyttolaster eller meddelanden som inte autentisering när vissa meddelandenivå säkerhetsschema används.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Dead-lettering i ForwardTo eller SendVia scenarier

Meddelanden skickas till kön för obeställbara överföring följande villkor:

- Ett meddelande som passerar genom fler än 4 köer eller ämnen som är [sammankopplade](service-bus-auto-forwarding.md).
- Målkön eller ämnet är inaktiverat eller Borttaget.
- Målkön eller ämnet överskrider den maximala entity-storleken.

Om du vill hämta dessa lettered förlorade meddelanden kan du skapa en mottagare med hjälp av den [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) verktyget metod.

## <a name="example"></a>Exempel

Följande kodfragment skapar en mottagare för meddelandet. I receive-loop för huvudkön koden hämtar meddelandet med [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), som begär den asynkrona meddelandekön att returnera alla meddelanden som är tillgängliga direkt, eller att returnera med inget resultat. Om koden tar emot ett meddelande, den omedelbart lämnar den, vilket ökar den `DeliveryCount`. När systemet flyttar meddelandet till DLQ, inte tomt och slingan avslutas som [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) returnerar **null**.

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
* [Azure-köer och Service Bus köer jämfört med](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

