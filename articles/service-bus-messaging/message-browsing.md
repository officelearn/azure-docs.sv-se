---
title: Azure Service Bus - meddelandebläddring
description: Bläddra och granska Service Bus-meddelanden gör det möjligt för en Azure Service Bus-klient att räkna upp alla meddelanden som finns i en kö eller prenumeration.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 6156557d10210535b287aa516070c0b5da416512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539373"
---
# <a name="message-browsing"></a>Bläddra i meddelanden

Med meddelandebläddring, eller genom att titta på, kan en Service Bus-klient räkna upp alla meddelanden som finns i en kö eller prenumeration, vanligtvis för diagnostik- och felsökningsändamål.

Peek-åtgärderna returnerar alla meddelanden som finns i kö- eller prenumerationsmeddelandeloggen, inte bara de som är tillgängliga för omedelbar anskaffning med `Receive()` eller loopen. `OnMessage()` Egenskapen `State` för varje meddelande anger om meddelandet är aktivt (tillgängligt för att tas emot), [uppskjutet](message-deferral.md)eller [schemalagt](message-sequencing.md).

Förbrukade och utgångna meddelanden rensas av en asynkron "skräpinsamling" och inte `Peek` nödvändigtvis exakt när meddelanden upphör att gälla, och därför kan faktiskt returnera meddelanden som redan har upphört att gälla och kommer att tas bort eller tas bort när en mottagningsåtgärd nästa anropas i kön eller prenumerationen.

Detta är särskilt viktigt att tänka på när du försöker återställa uppskjutna meddelanden från kön. Ett meddelande som [ExpiresAtUtc-instantn](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) har passerat är inte längre kvalificerat för regelbunden hämtning på något annat sätt, även när det returneras av Peek. Att returnera dessa meddelanden är avsiktligt, eftersom Peek är ett diagnostikverktyg som återspeglar loggens aktuella tillstånd.

Peek returnerar också meddelanden som var låsta och som för närvarande bearbetas av andra mottagare, men som ännu inte har slutförts. Men eftersom Peek returnerar en frånkopplad ögonblicksbild kan låstillståndet för ett meddelande inte observeras på peeked-meddelanden och egenskaperna [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) och [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) genererar en [InvalidOperationException](/dotnet/api/system.invalidoperationexception) när programmet försöker läsa dem.

## <a name="peek-apis"></a>Peek API:er

[Metoderna Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) och [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) finns i alla .NET- och Java-klientbibliotek och på alla mottagarobjekt: **MessageReceiver**, **MessageSession**. Peek fungerar på alla köer och prenumerationer och deras respektive köer för obeställbara meddelanden.

När den anropas upprepade gånger räknar Peek-metoden upp alla meddelanden som finns i kö- eller prenumerationsloggen, i sekvensnummerordning, från det lägsta tillgängliga sekvensnumret till det högsta. Det här är den ordning i vilken meddelanden var köade och inte den ordning i vilken meddelanden så småningom kan hämtas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) hämtar flera meddelanden och returnerar dem som en uppräkning. Om inga meddelanden är tillgängliga är uppräkningsobjektet tomt, inte null.

Du kan också dirigera en överbelastning av metoden med en [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) som du vill starta och sedan anropa den parameterlösa metodens överbelastning för att räkna upp ytterligare. **PeekBatch** fungerar på motsvarande sätt, men hämtar en uppsättning meddelanden på en gång.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
