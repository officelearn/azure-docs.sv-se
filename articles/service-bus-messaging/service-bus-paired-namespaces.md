---
title: Azure Service Bus länkas namnområden | Microsoft Docs
description: Implementeringsdetaljer parad namnområde och kostnad
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: f16c65286b0aa079889c9d53e98bf54e3d57c95f
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
ms.locfileid: "27159549"
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Länkas namnområde implementeringsdetaljer och cost effekter

Den [PairNamespaceAsync] [ PairNamespaceAsync] metod med en [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instansen, utför synliga uppgifter å dina vägnar. Eftersom det är kostnad överväganden när du använder funktionen, är det viktigt att förstå dessa uppgifter så att du förväntar dig beteendet när det sker. API: et aktiverar följande automatiska funktioner för din räkning:

* Skapa eftersläpning köer.
* Skapa en [MessageSender] [ MessageSender] objekt som kommunicerar köer och ämnen.
* Om en meddelandeentitet blir otillgänglig, pinga skickar meddelanden till entiteten i ett försök att identifiera när enheten blir tillgänglig igen.
* Du kan även skapa en uppsättning ”meddelande pumpar” som flytta meddelanden från eftersläpning köer till de primära köerna.
* Samordnar avslutande/felaktig av primära och sekundära [MessagingFactory] [ MessagingFactory] instanser.

På en hög nivå funktionen fungerar på följande sätt: när den primära entiteten är felfri, ingen funktionsändringar sker. När den [FailoverInterval] [ FailoverInterval] varaktighet är slut och de primära entiteten ser inte lyckas skickar efter en inte är tillfällig [MessagingException] [ MessagingException] eller en [TimeoutException][TimeoutException], händer följande:

1. Skicka till den primära entiteten har inaktiverats och systemet pingar den primära enheten förrän pingar har levereras.
2. En slumpmässig eftersläpning kö har valts.
3. [BrokeredMessage] [ BrokeredMessage] objekt dirigeras till valda eftersläpning kön.
4. Om en send-åtgärd till valda eftersläpning kön misslyckas kön hämtas från rotationen och en ny kö har valts. Alla avsändare på den [MessagingFactory] [ MessagingFactory] instans Lär dig mer om felet.

I följande figurer visas sekvensen. Först skickar avsändare meddelanden.

![Parad namnområden][0]

Vid fel ska skickas till primära kön börjar avsändaren skicka meddelanden till en slumpmässigt vald eftersläpning kö. Samtidigt, startar en ping-aktivitet.

![Parad namnområden][1]

Nu finns kvar i sekundär kö meddelanden och inte har levererats till primära kön. När primära kön är felfri igen, ska minst en process köras syphon. Syphon levererar meddelanden från alla olika eftersläpning köer till rätt mål-enheter (köer och ämnen).

![Parad namnområden][2]

Resten av det här avsnittet innehåller information om specifik information om hur dessa delar fungerar.

## <a name="creation-of-backlog-queues"></a>Skapandet av eftersläpning köer
Den [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] objekt har överförts till den [PairNamespaceAsync] [ PairNamespaceAsync] metoden anger antalet eftersläpning köer du vill använda. Varje kö eftersläpning skapas med följande egenskaper uttryckligen ange (alla andra värden är inställda på att den [QueueDescription] [ QueueDescription] standardvärden):

| Sökväg | [primära namnutrymme] / x-servicebus-överföring / [index] där [index] är ett värde i [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |heltal. MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| Varaktighet |1 minut |
| EnableDeadLetteringOnMessageExpiration |sant |
| EnableBatchedOperations |sant |

Till exempel den första eftersläpning kön skapas för namnområdet **contoso** heter `contoso/x-servicebus-transfer/0`.

När du skapar köerna kontrollerar koden först om det finns en sådan kö. Om det inte finns kön, skapa kön. Koden inte rensa ”extra” eftersläpning köer. I synnerhet om programmet med primära namnområde **contoso** begär fem eftersläpning köer, men en eftersläpning kö med sökvägen `contoso/x-servicebus-transfer/7` finns extra eftersläpning kön är kvar men används inte. Systemet tillåter uttryckligen extra eftersläpning köer finns som inte används. Du är ansvarig för att rensa alla oanvända/oönskad eftersläpning köer som ägare namnområde. Orsaken till detta beslut är att Service Bus inte kan vet vilket syfte som finns för alla köer i namnområdet. Dessutom, om en kö med det angivna namnet finns men inte uppfyller den antagna [QueueDescription][QueueDescription], din orsaker är din egen för ändra standardbeteendet. Inga garantier görs för att ändringarna till eftersläpning köer av din kod. Se till att testa dina ändringar noggrant.

## <a name="custom-messagesender"></a>Anpassade MessageSender
När du skickar, alla meddelanden gå igenom en intern [MessageSender] [ MessageSender] objekt som fungerar sedan normalt när allt fungerar och omdirigerar till eftersläpningen köer när saker ”delar”. Vid mottagning av ett fel uppstod, startar en timer. När en [TimeSpan] [ TimeSpan] period som består av den [FailoverInterval] [ FailoverInterval] egenskapens värde under vilket inga lyckade meddelanden skickas, växling vid fel används. Nu händer följande för varje entitet:

* Ping-uppgiften körs varje [PingPrimaryInterval] [ PingPrimaryInterval] att kontrollera om enheten är tillgänglig. När den här aktiviteten lyckas startar alla klientkod som använder enheten omedelbart skicka nya meddelanden till det primära namnområdet.
* Kommande begäranden att skicka till att samma entitet från andra sändare leder den [BrokeredMessage] [ BrokeredMessage] som skickas till ändras så att de är placerade i eftersläpning kön. Detta tar bort vissa egenskaper från den [BrokeredMessage] [ BrokeredMessage] objekt och lagrar dem på en annan plats. Följande egenskaper tas bort och lagts till under ett nytt alias, vilket gör att Service Bus och SDK att bearbeta meddelanden enhetligt:

| Gamla egenskapsnamn | Nya egenskapsnamn |
| --- | --- |
| Sessions-ID |x-ms-sessions-ID |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-sökväg |

Den ursprungliga målsökvägen lagras också i meddelandet som en egenskap med namnet x-ms-sökväg. Den här designen kan meddelanden för många enheter som ska finnas i en enda eftersläpning kö. Egenskaperna översätts igen genom syphon.

Anpassat [MessageSender] [ MessageSender] objekt problem kan uppstå när meddelanden hanterar 256 KB-gränsen och växling vid fel används. Anpassat [MessageSender] [ MessageSender] -objektet lagrar meddelanden för alla köer och ämnen tillsammans i eftersläpning köer. Det här objektet blandas meddelanden från många primärfärgerna tillsammans i eftersläpning köer. Om du vill hantera belastningsutjämningen bland många klienter som inte vet varandra SDK slumpvis en eftersläpning kö för varje [QueueClient] [ QueueClient] eller [TopicClient] [ TopicClient] du skapar i koden.

## <a name="pings"></a>Pingar
Ett ping-meddelande är ett tomt [BrokeredMessage] [ BrokeredMessage] med dess [ContentType] [ ContentType] -egenskapen angetts till programmet/vnd.ms-servicebus-ping och en [TimeToLive] [ TimeToLive] värdet 1 sekund. Den här ping har en särskild egenskap i Service Bus: ett ping skickas aldrig när alla anroparen begär en [BrokeredMessage][BrokeredMessage]. Du har aldrig alltså att lära dig att ta emot och ignorera dessa meddelanden. Varje entitet (unikt kö eller ett ämne) per [MessagingFactory] [ MessagingFactory] instans per klient ska pingas när de anses vara otillgänglig. Som standard är detta sker en gång per minut. Ping-meddelanden anses vara vanliga Service Bus-meddelanden och kan resultera i kostnader för bandbredd och meddelanden. När klienter identifiera att systemet är tillgängligt, stoppa meddelanden.

## <a name="the-syphon"></a>Syphon
Minst ett körbart program i programmet bör aktivt köra syphon. Syphon utför lång avsökning får som pågår i 15 minuter. När du har 10 eftersläpning köer och alla entiteter som är tillgängliga anropar programmet som är värd för syphon mottagningsåtgärd 40 gånger per timme, 960 gånger per dag och 28800 gånger i 30 dagar. När syphon är aktivt flyttar meddelanden från eftersläpningen till primära kön, inträffar följande avgifterna (standard kostnader för meddelandestorlek och bandbredd som används i alla led) i varje meddelande:

1. Skicka till eftersläpningen.
2. Ta emot från eftersläpningen.
3. Skicka till den primära servern.
4. Ta emot från den primära servern.

## <a name="closefault-behavior"></a>Stäng/fel beteende
I ett program som är värd för syphon, när primärt eller sekundära [MessagingFactory] [ MessagingFactory] hos eller stängs utan sin partner också fel eller stängd och syphon identifierar det här tillståndet den syphon fungerar. Om den andra [MessagingFactory] [ MessagingFactory] inte är stängd inom 5 sekunder hos syphon fortfarande öppen [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>Nästa steg
Se [asynkrona meddelanden mönster och hög tillgänglighet] [ Asynchronous messaging patterns and high availability] för en detaljerad beskrivning av asynkrona Service Bus-meddelanden. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
