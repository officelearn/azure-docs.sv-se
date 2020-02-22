---
title: Azure Service Bus-meddelande bläddring
description: Bläddra och granska Service Bus meddelanden gör det möjligt för en Azure Service Bus klient att räkna upp alla meddelanden som finns i en kö eller prenumeration.
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
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539373"
---
# <a name="message-browsing"></a>Bläddra i meddelanden

Genom att söka efter meddelanden eller granska, kan en Service Bus-klient räkna upp alla meddelanden som finns i en kö eller prenumeration, vanligt vis för diagnostik-och fel söknings syfte.

Gransknings åtgärderna returnerar alla meddelanden som finns i kön eller prenumerations meddelande loggen, inte bara de som är tillgängliga för omedelbar förvärv med `Receive()` eller `OnMessage()`-slingan. Egenskapen `State` för varje meddelande anger om meddelandet är aktivt (tillgängligt för mottagning), [uppskjutet](message-deferral.md)eller [schemalagt](message-sequencing.md).

Förbrukade och utgångna meddelanden rensas upp av en asynkron "skräp insamling" som körs och inte nödvändigt vis exakt när meddelanden upphör att gälla, och därför kan `Peek` faktiskt returnera meddelanden som redan har gått ut och tas bort eller tas bort från kön när en mottagnings åtgärd anropas nästa gång den tar emot kön eller prenumerationen.

Detta är särskilt viktigt att tänka på när du försöker återställa uppskjutna meddelanden från kön. Ett meddelande om att [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) Instant har passerat är inte längre tillgängligt för vanlig hämtning på något annat sätt, även när det returneras av Peek. Att returnera dessa meddelanden är avsiktligt eftersom Peek är ett diagnos verktyg som återspeglar loggens aktuella status.

Peek returnerar även meddelanden som låsts och bearbetas för närvarande av andra mottagare, men ännu inte har slutförts. Men eftersom Peek returnerar en frånkopplad ögonblicks bild kan inte lås statusen för ett meddelande observeras på granskade meddelanden, och egenskaperna [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) och [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) genererar en [InvalidOperationException](/dotnet/api/system.invalidoperationexception) när programmet försöker läsa dem.

## <a name="peek-apis"></a>Granska API: er

Metoderna [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) och [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) finns i alla .net-och Java-klient bibliotek och på alla mottagares objekt: **MessageReceiver**, **MessageSession**. Peek fungerar på alla köer och prenumerationer och deras respektive köer för obeställbara meddelanden.

När den anropas flera gånger, räknar gransknings metoden alla meddelanden som finns i kön eller prenumerations loggen i ordnings nummer ordningen, från det lägsta tillgängliga sekvensnumret till högsta. Detta är ordningen i vilken meddelanden har placerats i kö och inte i den ordning som meddelanden kan komma att hämtas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) hämtar flera meddelanden och returnerar dem som en uppräkning. Om inga meddelanden är tillgängliga, är uppräknings objektet tomt, inte null.

Du kan också dirigera en överlagring av metoden med en [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) som ska startas och sedan anropa den parameter lös metoden överbelastning för att räkna upp ytterligare. **PeekBatch** -funktioner likvärdigt, men hämtar en uppsättning meddelanden samtidigt.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
