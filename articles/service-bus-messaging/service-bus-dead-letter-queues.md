---
title: Service Bus köer för obeställbara meddelanden | Microsoft Docs
description: Översikt över Azure Service Bus köer för obeställbara meddelanden
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
ms.date: 05/21/2019
ms.author: aschhab
ms.openlocfilehash: 79bc5e640498788ef805d07a26dd29e943117b58
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68476976"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Översikt över Service Bus köer för obeställbara meddelanden

Azure Service Bus köer och ämnes prenumerationer tillhandahåller en sekundär underordnad kö som kallas för en *kö för obeställbara meddelanden* (DLQ). Kön för obeställbara meddelanden behöver inte skapas explicit och kan inte tas bort eller hanteras på annat sätt oberoende av huvud enheten.

I den här artikeln beskrivs köer för obeställbara meddelanden i Service Bus. En stor del av diskussionen illustreras av [exemplet för köer för obeställbara meddelanden](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) på GitHub.
 
## <a name="the-dead-letter-queue"></a>Kön för obeställbara meddelanden

Syftet med kön för obeställbara meddelanden är att lagra meddelanden som inte kan levereras till någon mottagare eller meddelanden som inte kunde bearbetas. Meddelanden kan sedan tas bort från DLQ och granskas. Ett program kan, med hjälp av en operatör, korrigera problem och skicka meddelandet på nytt, logga det faktum att det uppstod ett fel och vidta lämpliga åtgärder. 

Från ett API-och protokoll perspektiv är DLQ mest likt andra köer, förutom att meddelanden bara kan skickas via den överordnade entitetens obeställbara åtgärder. Dessutom observeras inte Time-to-Live och du kan inte obeställbara ett meddelande från en DLQ. Kön för obeställbara meddelanden har fullt stöd för gransknings-och transaktions åtgärder.

Observera att det inte finns någon automatisk rensning av DLQ. Meddelanden finns kvar i DLQ tills du uttryckligen hämtar dem från DLQ och anropar [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) i meddelandet om obeställbara meddelanden.

## <a name="moving-messages-to-the-dlq"></a>Flytta meddelanden till DLQ

Det finns flera aktiviteter i Service Bus som gör att meddelanden skickas till DLQ inifrån själva meddelande motorn. Ett program kan också uttryckligen flytta meddelanden till DLQ. 

När meddelandet flyttas av utjämningen läggs två egenskaper till i meddelandet eftersom Broker anropar den interna versionen av [obeställbara meddelanden kön](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) -metoden i meddelandet: `DeadLetterReason` och. `DeadLetterErrorDescription`

Program kan definiera egna koder för `DeadLetterReason` egenskapen, men systemet anger följande värden.

| Tillstånd | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Alltid |HeaderSizeExceeded |Storlekskvoten för dataströmmen har överskridits. |
| ! TopicDescription.<br />EnableFilteringMessagesBeforePublishing och SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |Meddelandet har gått ut och blev obeställbart. |
| SubscriptionDescription.RequiresSession |Sessions-ID är null. |Sessionsaktiverad entitet tillåter inte ett meddelande vars sessions-ID är null. |
| ! kö för obeställbara meddelanden | MaxTransferHopCountExceeded | Maximalt antal tillåtna hopp vid vidarebefordran mellan köer. Värdet är inställt på 4. |
| Program explicit död brevning |Anges av programmet |Anges av programmet |

## <a name="exceeding-maxdeliverycount"></a>Överskrider MaxDeliveryCount

Köer och prenumerationer har var och en har egenskapen [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) respektive [SubscriptionDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) . Standardvärdet är 10. När ett meddelande har levererats under ett lås ([PeekLock ReceiveMode. PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), men har antingen uttryckligen övergivits eller om låset har upphört att gälla, ökar meddelandet [BrokeredMessage. DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . När [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) överskrider [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount)flyttas meddelandet till DLQ `MaxDeliveryCountExceeded` och anger orsaks koden.

Det går inte att inaktivera det här beteendet, men du kan ange [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) till ett mycket stort tal.

## <a name="exceeding-timetolive"></a>Överskrider TimeToLive

När egenskapen [QueueDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [SubscriptionDescription. EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) har angetts till **True** (Standardvärdet är **falskt**) är alla meddelanden som upphör att gälla Flyttad till DLQ och ange `TTLExpiredException` orsaks koden.

Observera att förfallna meddelanden bara rensas och flyttas till DLQ när det finns minst en aktiv mottagare från huvud kön eller prenumerationen. Detta beteende är avsiktligt.

## <a name="errors-while-processing-subscription-rules"></a>Fel vid bearbetning av prenumerations regler

När egenskapen [SubscriptionDescription. EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) har Aktiver ATS för en prenumeration, registreras eventuella fel som inträffar när en prenumerations SQL filter-regel körs i DLQ tillsammans med den felaktiga meddelande.

## <a name="application-level-dead-lettering"></a>Obeställbara meddelanden på program nivå

Förutom de funktioner för obeställbara meddelanden som har angetts kan program använda DLQ för att uttryckligen avvisa meddelanden som inte accepteras. Detta kan inkludera meddelanden som inte kan bearbetas korrekt på grund av en viss typ av systemfel, meddelanden som innehåller felaktiga nytto laster eller meddelanden som inte kan autentiseras när vissa säkerhets scheman på meddelande nivå används.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Obeställbara meddelanden i scenarier med ForwardTo eller SendVia

Meddelanden skickas till kön för överföring av obeställbara meddelanden under följande omständigheter:

- Ett meddelande skickas genom mer än 4 köer eller avsnitt [som är](service-bus-auto-forwarding.md)sammankopplade.
- Målkön eller avsnittet har inaktiverats eller tagits bort.
- Målkön eller avsnittet överskrider den maximala enhets storleken.

Om du vill hämta dessa meddelanden med obeställbara meddelanden kan du skapa en mottagare med hjälp av [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) -verktygs metoden.

## <a name="example"></a>Exempel

Följande kodfragment skapar en meddelande mottagare. I mottagnings-loopen för huvud kön hämtar koden meddelandet med [receive (TimeSpan. Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), som uppmanar koordinatorn att omedelbart returnera eventuella meddelanden som är tillgängliga eller returnera utan resultat. Om koden tar emot ett meddelande överges det omedelbart, vilket ökar `DeliveryCount`. När systemet flyttar meddelandet till DLQ är huvud kön Tom och loopen avslutas, så som [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) returnerar **Null**.

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

## <a name="path-to-the-dead-letter-queue"></a>Sökväg till kön för obeställbara meddelanden
Du kan komma åt kön för obeställbara meddelanden med hjälp av följande syntax:

```
<queue path>/$deadletterqueue
<topic path>/Subscription/<subscription path>/$deadletterqueue
```

Om du använder .NET SDK kan du hämta sökvägen till kön för obeställbara meddelanden med hjälp av metoden SubscriptionClient. FormatDeadLetterPath (). Den här metoden tar ämnets namn/prenumerations namn och suffix med **/$DeadLetterQueue**.


## <a name="next-steps"></a>Nästa steg

I följande artiklar finns mer information om Service Bus köer:

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Azure-köer och Service Bus köer jämförs](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

