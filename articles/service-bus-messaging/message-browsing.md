---
title: Bläddra i Azure Service Bus meddelanden | Microsoft Docs
description: Bläddra och granska Service Bus-meddelanden
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: spelluru
ms.openlocfilehash: 7ce2e870be0178420d80682bd18adbef814c162f
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857473"
---
# <a name="message-browsing"></a>Bläddra i meddelanden

Bläddra i meddelanden eller granskning, aktiverar du en Service Bus-klient att räkna upp alla meddelanden som finns i en kö eller prenumeration, vanligtvis för diagnos och felsökning.

Peek-åtgärder returnerar alla meddelanden som finns i Meddelandelogg kö eller prenumeration inte endast de tillgängliga för omedelbar med `Receive()` eller `OnMessage()` loop. Den `State` egenskapen för varje meddelande som anger om meddelandet är aktiv (tillgänglig som ska tas emot), [uppskjutna](message-deferral.md), eller [schemalagda](message-sequencing.md).

Förbrukade och utgångna meddelanden rensas av en asynkron ”skräpinsamling” kör och inte nödvändigtvis exakt när meddelanden upphör att gälla och därför `Peek` verkligen kan returnera meddelanden som redan har upphört att gälla och tas bort eller dead lettered när en receive-åtgärden anropas sedan i kö eller prenumeration.

Detta är särskilt viktigt att tänka på när du försöker återställa uppskjutna meddelanden från kön. Ett meddelande som den [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) snabbmeddelanden har passerat inte längre är berättigad för regelbundna hämtning på annat sätt, även när det är som returneras av Peek. Returnera dessa meddelanden är avsiktlig, eftersom Peek är ett verktyg för diagnostik som återger det aktuella tillståndet för loggen.

Granska även Returnerar meddelanden som låstes och bearbetas för närvarande av andra mottagare, men ännu inte har slutförts. Men eftersom Peek returnerar en frånkopplad ögonblicksbild, status för aktiveringslås för ett meddelande från nebyly nalezeny instance förhandsgranskas meddelanden och [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) och [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) egenskaper throw en [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) när programmet försöker läsa dem.

## <a name="peek-apis"></a>Granska API: er

Den [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) och [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) metoder finns i alla .NET och Java-klientbibliotek och på alla mottagare objekt: **MessageReceiver**, **MessageSession**, **QueueClient**, och **SubscriptionClient**. Granska fungerar på alla köer och prenumerationer och deras respektive köer.

När den anropas upprepade gånger metoden Peek räknar alla meddelanden som finns i kö eller prenumeration loggen, i sekvens nummer ordning, från det lägsta tillgängliga sekvensnumret till högst upp. Detta är den ordning som lagts till meddelanden i kön och är inte den ordning i vilken meddelanden så småningom kan hämtas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) hämtar flera meddelanden och returnerar dem som en uppräkning. Om det finns några meddelanden, är uppräkning tom, inte null.

Du kan också initiera en överlagringen för metoden med en [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) som du vill starta och sedan anropa metoden parameterlös överlagring att räkna upp ytterligare. **PeekBatch** funktioner equivalently men hämtar en uppsättning meddelanden på samma gång.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
