---
title: Azure Service Bus-meddelande och surfning | Microsoft Docs
description: "Bläddra och granska Service Bus-meddelanden"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: b0bc1ef7570ccac07975e2560a1d0501d3cde2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="message-browsing"></a>Meddelande-surfning

Meddelandet surfning (”granskning”) kan en klient att räkna upp alla meddelanden som finns i en kö eller en prenumeration, vanligtvis för diagnostik och felsökning.

Åtgärderna titt returnera alla meddelanden som finns i kön eller prenumeration meddelandeloggen, inte bara de tillgängliga för omedelbar med *Receive()* eller *OnMessage()* loop. Den *tillstånd* -egenskapen för varje meddelande som anger om meddelandet är aktiv (tillgänglig som ska tas emot), uppskjutna (Se avstängning [TBD länk]) eller schemalagda (se schemalagda meddelanden [TBD länk]).

Förbrukade och upphört att gälla meddelanden rensas upp av en asynkron ”skräpinsamling” kör inte nödvändigtvis exakt när meddelanden upphör att gälla och därför titt verkligen kan returnera meddelanden som redan har upphört att gälla och kommer att tas bort eller förlorade lettered när en Receive-aktivitet därefter startas på kö eller prenumeration.

Detta är särskilt viktigt att tänka på när du försöker återställa uppskjutna meddelanden från kön. Ett meddelande som den [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) snabbmeddelanden har passerat inte längre är berättigad till vanliga hämtning på annat sätt, även när det är som returneras av titt. Returnera dessa meddelanden är avsiktlig, eftersom titt är en diagnostikverktyget reflektion av det aktuella tillståndet för loggen.

Granska även Returnerar meddelanden som har låst och är för närvarande bearbetas av andra mottagare, men ännu inte har slutförts. Men eftersom titt returnerar en frånkopplad ögonblicksbild, ett meddelande låser upp går inte att Observera på peeked meddelanden och [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) och [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) egenskaper Kasta ett [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) när programmet försöker läsa dem.

## <a name="peek-apis"></a>Granska API: er

Den [titt/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) och [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) finns i alla .NET och Java klientbibliotek och på alla objekt som mottagare: **MessageReceiver**, **MessageSession**, **QueueClient**, och **SubscriptionClient**. Granska fungerar i alla köer och -prenumerationer och deras respektive köer.

När den anropas flera gånger metoden titt räknar upp alla meddelanden som finns i kön eller prenumeration loggen, i sekvens nummer ordning, från det lägsta tillgängliga numret till de högsta. Detta är den ordning som har meddelanden i kö; Det är inte den ordning som meddelanden så småningom kan hämtas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) hämtar flera meddelanden och returnerar dem som en uppräkning. Om det finns inga meddelanden, är uppräkning objektet tom, inte null.

Du kan också initiera en överlagring för metoden med en [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) som du vill starta och sedan anropa parameterlös metodöverlagringen att räkna upp ytterligare. **PeekBatch** equivalently fungerar, men hämtar meddelanden på samma gång.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)