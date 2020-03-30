---
title: Service Bus dead-letter köer | Microsoft-dokument
description: Beskriver köer för obeställbara meddelanden i Azure Service Bus. Service Bus-köer och ämnesprenumerationer ger en sekundär underkö, en så kallad kö för obeställbara meddelanden.
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
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 9c1a0cb92fbaf98d25799ffb5a85e666e7c05f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158917"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Översikt över köer för servicebuss av obeställbara meddelanden

Azure Service Bus-köer och ämnesprenumerationer ger en sekundär underkö, kallad kö för *obeställbara meddelanden* (DLQ). Kön för obeställbara meddelanden behöver inte uttryckligen skapas och kan inte tas bort eller på annat sätt hanteras oberoende av huvudentiteten.

I den här artikeln beskrivs köer för obeställbara meddelanden i Service Bus. Mycket av diskussionen illustreras av [exemplet med köer i obeställbara meddelanden](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) på GitHub.
 
## <a name="the-dead-letter-queue"></a>Kön för obeställbara brev

Syftet med kön för obeställbara meddelanden är att innehålla meddelanden som inte kan levereras till någon mottagare eller meddelanden som inte kunde bearbetas. Meddelanden kan sedan tas bort från DLQ och inspekteras. Ett program kan, med hjälp av en operatör, korrigera problem och skicka meddelandet igen, logga det faktum att det uppstod ett fel och vidta korrigerande åtgärder. 

Ur ett API- och protokollperspektiv liknar DLQ oftast någon annan kö, förutom att meddelanden endast kan skickas via åtgärden för obeställbara meddelanden för den överordnade entiteten. Dessutom observeras inte tid att leva och du kan inte göra ett inlägg i ett meddelande från en DLQ. Kön för obeställbara meddelanden stöder fullständigt peek-lock-leverans och transaktionsåtgärder.

Det finns ingen automatisk rensning av DLQ. Meddelanden finns kvar i DLQ tills du uttryckligen hämtar dem från DLQ och [anropar Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) i meddelandet om obeställbara meddelanden.

## <a name="dlq-message-count"></a>Antal DLQ-meddelanden
Det går inte att få tag på meddelanden i kön för obeställbara meddelanden på ämnesnivå. Det beror på att meddelanden inte sitter på ämnesnivå om inte Service Bus genererar ett internt fel. När en avsändare skickar ett meddelande till ett ämne vidarebefordras meddelandet i stället till prenumerationer för ämnet inom millisekunder och finns därför inte längre på ämnesnivå. Så kan du se meddelanden i DLQ som är associerade med prenumerationen för ämnet. I följande exempel visar **Service Bus Explorer** att det för närvarande finns 62 meddelanden i DLQ för prenumerationen "test1". 

![Antal DLQ-meddelanden](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Du kan också få antalet DLQ-meddelanden med [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show)hjälp av Azure CLI-kommandot: . 

## <a name="moving-messages-to-the-dlq"></a>Flytta meddelanden till DLQ

Det finns flera aktiviteter i Service Bus som gör att meddelanden skjuts till DLQ inifrån själva meddelandemotorn. Ett program kan också uttryckligen flytta meddelanden till DLQ. 

När meddelandet flyttas av mäklaren läggs två egenskaper till i meddelandet när mäklaren anropar sin `DeadLetterReason` interna `DeadLetterErrorDescription`version av [DeadLetter-metoden](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) i meddelandet: och .

Program kan definiera sina `DeadLetterReason` egna koder för egenskapen, men systemet anger följande värden.

| Villkor | DödBreverReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Alltid |HeaderSizeExceed |Storlekskvoten för dataströmmen har överskridits. |
| ! TopicDescription.<br />AktiveraFilteringMessagesBeforePublicering och SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |Undantag. GetType(). Namn |Undantag. Meddelande |
| AktiveraDeadLetteringOnMessageExpiration |TTLExpiredException |Meddelandet har gått ut och blev obeställbart. |
| SubscriptionDescription.RequiresSession |Sessions-ID är null. |Sessionsaktiverad entitet tillåter inte ett meddelande vars sessions-ID är null. |
| !kön för dött brev | MaxTransferHopCountExceed | Det maximala antalet tillåtna hopp vid vidarebefordran mellan köer. Värdet är inställt på 4. |
| Ansökan explicit död bokstäver |Angivet per program |Angivet per program |

## <a name="exceeding-maxdeliverycount"></a>Överskrider MaxDeliveryCount

Köer och prenumerationer har vardera [egenskapen QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) och [SubscriptionDescription.MaxDeliveryCount.](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) standardvärdet är 10. När ett meddelande har levererats under ett lås ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), men har antingen uttryckligen övergivits eller låset har upphört att gälla, ökas meddelandet [BrokeredMessage.DeliveryCount.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) När [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) överskrider [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount)flyttas meddelandet till DLQ `MaxDeliveryCountExceeded` och anger orsakskoden.

Det här problemet kan inte inaktiveras, men du kan ange [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) till ett stort antal.

## <a name="exceeding-timetolive"></a>Mer än TimeToLive

När egenskapen [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) eller [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) är inställd på **true** (standard är **false)** flyttas alla `TTLExpiredException` meddelanden som upphör att gälla till DLQ och anger orsakskoden.

Utgångna meddelanden rensas bara och flyttas till DLQ när det finns minst en aktiv mottagare som hämtar från huvudkön eller prenumerationen. detta beteende är avsiktligt.

## <a name="errors-while-processing-subscription-rules"></a>Fel vid bearbetning av prenumerationsregler

När egenskapen [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) är aktiverad för en prenumeration, fångas alla fel som uppstår medan en prenumerations SQL-filterregel körs i DLQ tillsammans med det felande meddelandet.

## <a name="application-level-dead-lettering"></a>Obeskrivande på programnivå

Förutom de systembaserade funktioner för obeställbara meddelanden kan program använda DLQ för att uttryckligen avvisa oacceptabla meddelanden. De kan innehålla meddelanden som inte kan bearbetas korrekt på grund av någon form av systemproblem, meddelanden som innehåller felaktiga nyttolaster eller meddelanden som misslyckas när vissa säkerhetsschema på meddelandenivå används.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Obeskrivande i ForwardTo- eller SendVia-scenarier

Meddelanden skickas till kön för att skicka obeställbara meddelanden på följande villkor:

- Ett meddelande går igenom fler än fyra köer eller ämnen som är [sammankopplade](service-bus-auto-forwarding.md).
- Målkön eller målämnet är inaktiverat eller borttaget.
- Målkön eller målämnet överskrider den maximala entitetsstorleken.

Om du vill hämta dessa meddelanden med obeställda meddelanden kan du skapa en mottagare med hjälp av verktygsmetoden [FormatTransferDeadletterPath.](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath)

## <a name="example"></a>Exempel

Följande kodavsnitt skapar en meddelandemottagare. I mottagningsloopen för huvudkön hämtar koden meddelandet med [Receive(TimeSpan.Zero),](/dotnet/api/microsoft.servicebus.messaging.messagereceiver)som ber mäklaren att omedelbart returnera alla meddelanden som är lättillgängliga eller att returnera utan resultat. Om koden tar emot ett meddelande överger den `DeliveryCount`omedelbart det, vilket ökar . När systemet flyttar meddelandet till DLQ är huvudkön tom och loopen avslutas, när [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) returnerar **null**.

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
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Om du använder .NET SDK kan du hämta sökvägen till kön för obeställbara meddelanden med hjälp av metoden SubscriptionClient.FormatDeadLetterPath(). Den här metoden tar ämnesnamn/prenumerationsnamn och suffix med **/$DeadLetterQueue**.


## <a name="next-steps"></a>Nästa steg

Mer information om servicebussköer finns i följande artiklar:

* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Azure-köer och servicebussköer jämförs](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

