---
title: Azure Service Bus har parats ihop namnområden | Microsoft Docs
description: Information om implementering av kopplat namnområde och kostnader
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 35c643b9bb4f348b790577e560eaf14d3a19802f
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848352"
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Länkad implementeringsdetaljer för namnområde och kostnaden effekter

Den [PairNamespaceAsync] [ PairNamespaceAsync] metoden med hjälp av en [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instans, utför uppgifter som är synliga på din räkning. Eftersom det är att lagra överväganden när du använder funktionen, är det viktigt att förstå dessa uppgifter så att du förväntar dig beteendet när det händer. Följande automatiska funktioner för din räkning talar med API: et:

* Skapa eftersläpning köer.
* Skapa en [MessageSender] [ MessageSender] objekt som pratar med köer eller ämnen.
* När en meddelandeentitet blir otillgänglig kan pinga skickar meddelanden till entiteten i ett försök att identifiera när denna entitet blir tillgänglig igen.
* Du kan också skapas av en uppsättning ”meddelande pumpar” som flytta meddelanden från köer med kvarvarande uppgifter till de primära köerna.
* Samordnar avslutande/felaktigt av primära och sekundära [MessagingFactory] [ MessagingFactory] instanser.

På hög nivå, funktionen fungerar på följande sätt: när den primära entiteten är felfri, inga ändringar sker. När den [FailoverInterval] [ FailoverInterval] varaktighet är slut och de primära entiteten ser inte lyckas skickar efter en icke tillfälliga [MessagingException] [ MessagingException] eller en [TimeoutException][TimeoutException], händer följande:

1. Skicka åtgärder till den primära entiteten är inaktiverade och systemet pingar den primära entiteten tills pingar kan levereras.
2. En slumpmässig för eftersläpning i kö har valts.
3. [BrokeredMessage] [ BrokeredMessage] dirigeras objekt till valda för eftersläpning i kön.
4. Om en åtgärd för send till valda för eftersläpning i kön misslyckas kön hämtas från rotationen och en ny kö har valts. Alla avsändare på den [MessagingFactory] [ MessagingFactory] instans Lär dig om felet.

I följande figurer visas sekvensen. Först skickar avsändaren meddelanden.

![Kopplade namnområden][0]

Vid fel ska skickas till den primära kön börjar avsändaren skicka meddelanden till en slumpmässigt vald för eftersläpning i kö. Samtidigt, startar en ping-uppgiften.

![Kopplade namnområden][1]

I det här läget meddelanden finns kvar i den sekundära kön och inte har levererats till den primära kön. När den primära kön är felfri igen, bör minst en process med syphon. Syphon levererar meddelanden från alla olika eftersläpning köer till rätt destination entiteter (köer och ämnen).

![Kopplade namnområden][2]

Resten av det här avsnittet beskrivs specifik information om hur dessa delar fungerar.

## <a name="creation-of-backlog-queues"></a>Skapandet av eftersläpning köer
Den [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] objekt skickades till den [PairNamespaceAsync] [ PairNamespaceAsync] metoden anger antalet Eftersläpning för köer som du vill använda. Varje eftersläpning-kö skapas med följande egenskaper uttryckligen ange (alla andra värden är inställda på den [QueueDescription] [ QueueDescription] standardvärden):

| Sökväg | [primärt namnområde] / x-servicebus-överföra / [index] där [index] är ett värde i [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int.MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1 minut |
| EnableDeadLetteringOnMessageExpiration |true |
| EnableBatchedOperations |true |

Till exempel den första för eftersläpning i kön skapas för namnområde **contoso** heter `contoso/x-servicebus-transfer/0`.

När du skapar köer, kontrollerar koden först om det finns sådan kö. Om kön inte finns, skapas i kön. Koden inte rensa ”extra” eftersläpning köer. Mer specifikt om programmet som det primära namnområdet **contoso** begär fem eftersläpning köer men en eftersläpning kö med sökvägen `contoso/x-servicebus-transfer/7` finns, extra för eftersläpning i kön är kvar men används inte. Systemet kan uttryckligen extra eftersläpning köer finns som inte används. Du är ansvarig för att rensa eventuella oanvända/oönskad eftersläpning för köer som ägare till namnområdet. Orsaken till det här beslutet är att Service Bus inte kan vet vilket syfte som finns för alla köer i namnområdet. Dessutom, om en kö med det angivna namnet finns men inte uppfyller den antagen [QueueDescription][QueueDescription], och sedan din orsakerna är ditt eget för Ändra standardbeteende. Inga garantier görs efter ändringar av köer eftersläpning av din kod. Se till att testa ändringarna noggrant.

## <a name="custom-messagesender"></a>Custom MessageSender
När du skickar, alla meddelanden går igenom en intern [MessageSender] [ MessageSender] objekt som fungerar sedan normalt när allt fungerar och omdirigerar till eftersläpningen köer när saker ”bryta”. När tas emot ett icke tillfälliga fel, startar en timer. När du har en [TimeSpan] [ TimeSpan] period som består av den [FailoverInterval] [ FailoverInterval] egenskapsvärdet då skickas inga meddelanden som skickats, redundansen är engagerade. Följande saker händer nu för varje entitet:

* En ping-aktivitet körs varje [PingPrimaryInterval] [ PingPrimaryInterval] att kontrollera om enheten är tillgänglig. När den här uppgiften har slutförts startar alla klientkod som använder entiteten omedelbart skicka nya meddelanden till det primära namnområdet.
* Framtida begäranden till att skicka till samma entiteten från andra sändare leder den [BrokeredMessage] [ BrokeredMessage] som skickas till ändras så att de finns i för eftersläpning i kön. Ändringen tar bort vissa egenskaper från den [BrokeredMessage] [ BrokeredMessage] objekt och lagrar dem någon annanstans. Följande egenskaper är avmarkerad och lagts till under ett nytt alias, vilket gör att Service Bus och SDK att bearbeta meddelanden på ett enhetligt sätt:

| Gamla egenskapsnamn | Den nya egenskapens namn |
| --- | --- |
| sessions-ID |x-ms-sessions-ID |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

Den ursprungliga målsökvägen lagras också i meddelandet som en egenskap med namnet x-ms-sökväg. Den här designen kan meddelanden för många entiteter att finnas i en enda för eftersläpning i kö. Egenskaperna översätts tillbaka av syphon.

Anpassat [MessageSender] [ MessageSender] objekt kan uppleva problem när meddelanden hanterar 256 KB-gränsen och växling vid fel är engagerade. Anpassat [MessageSender] [ MessageSender] objektet lagrar meddelanden för alla köer och ämnen tillsammans i eftersläpning köer. Det här objektet blandas meddelanden från många USA: s presidentval tillsammans i eftersläpning köer. För att hantera belastningsutjämningen bland många klienter som inte vet varandra SDK slumpvis en eftersläpning för kö för varje [QueueClient] [ QueueClient] eller [TopicClient] [ TopicClient] du skapar i kod.

## <a name="pings"></a>Pingar
Ett ping-meddelande är en tom [BrokeredMessage] [ BrokeredMessage] med dess [ContentType] [ ContentType] -egenskapen angetts till program/vnd.ms-servicebus-ping och en [TimeToLive] [ TimeToLive] värde på 1 sekund. Den här pingen har en särskild egenskap i Service Bus: en ping skickas aldrig när alla anroparen begär en [BrokeredMessage][BrokeredMessage]. Därför får du aldrig att lära dig att ta emot och ignorera dessa meddelanden. Varje entitet (unikt kö eller ämne) per [MessagingFactory] [ MessagingFactory] instans per klient ska pingas när de anses vara ej tillgänglig. Som standard sker detta en gång per minut. Pingmeddelanden anses vara vanliga Service Bus-meddelanden och kan resultera i kostnader för bandbredd och meddelanden. När klienter identifiera att systemet är tillgänglig, stoppa meddelanden.

## <a name="the-syphon"></a>Syphon
Minst ett körbart program i programmet bör aktivt köra syphon. Syphon utför en lång avsökning får som pågår i 15 minuter. När alla entiteter är tillgängliga och du har 10 eftersläpning för köer, anropar det program som är värd för syphon Mottagningsåtgärden 40 gånger per timme, 960 gånger per dag och 28800 gånger i 30 dagar. När syphon flyttar aktivt meddelanden från eftersläpningen till den primära kön, inträffar följande avgifter (standardavgifter för meddelandestorlek och bandbredd som gäller i alla led) i varje meddelande:

1. Skicka till eftersläpningen.
2. Ta emot från eftersläpningen.
3. Skicka till primärt.
4. Ta emot från primärt.

## <a name="closefault-behavior"></a>Stäng/fel beteende
I ett program som är värd för syphon, när primärt eller sekundära [MessagingFactory] [ MessagingFactory] faults eller stängs utan dess partner också felaktiga eller stängda och syphon identifierar det här tillståndet i syphon fungerar. Om den andra [MessagingFactory] [ MessagingFactory] inte stängs inom 5 sekunder på syphon faults fortfarande öppna [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>Nästa steg
Se [asynkron meddelandehantering mönster och hög tillgänglighet] [ Asynchronous messaging patterns and high availability] för en detaljerad beskrivning av asynkrona meddelanden i Service Bus. 

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
