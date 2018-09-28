---
title: Azure Service Bus-meddelanden prefetch | Microsoft Docs
description: Förbättra prestanda genom förhämtar, Azure Service Bus-meddelanden.
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
ms.date: 08/30/2018
ms.author: spelluru
ms.openlocfilehash: 92a5a90424de56b5cef97b790c6f8b3685fa833f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410178"
---
# <a name="prefetch-azure-service-bus-messages"></a>Förhämtning av Azure Service Bus-meddelanden

När *Prefetch* har aktiverats i någon av de officiella Service Bus-klienterna, mottagaren tyst skaffar fler meddelanden, upp till den [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) gränsen, utöver vad programmet först bad om.

En enda initial [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) eller [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) anrop därför får ett meddelande för omedelbar konsumtion som returneras så snart som tillgängliga. Klienten erhåller sedan ytterligare meddelanden i bakgrunden, fylla prefetch bufferten.

## <a name="enable-prefetch"></a>Aktivera prefetch

Med .NET och aktivera Prefetch funktionen genom att ange den [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) egenskapen för en **MessageReceiver**, **QueueClient**, eller **SubscriptionClient**  till ett tal som är större än noll. Ställer in värdet på noll stänger av prefetch.

Du kan enkelt lägga till den här inställningen i mottagarsidan av den [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) eller [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) exempel inställningar för att se effekten i dessa sammanhang.

Även om det finns meddelanden i prefetch buffert all efterföljande **Receive**/**ReceiveAsync** anrop uppfylls direkt från bufferten och bufferten fylls på i den Background som utrymme. Om det finns inga meddelanden för leverans, tömmer Mottagningsåtgärden den buffert och väntar eller block som förväntat.

Prefetch fungerar på samma sätt som med den [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) och [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API: er.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Om det är snabbare, varför är Prefetch inte standardalternativet?

Prefetch påskyndar meddelande flödet genom att ett meddelande som är tillgängliga för hämtning av lokala när och innan programmet begär en. Den här dataflöde vinst är resultatet av en kompromiss programförfattaren måste explicit se:

Med den [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mottagningsläge, alla meddelanden som är upptagna till prefetch-buffert är inte längre tillgängliga i kön och finns bara i InMemory-prefetch bufferten tills de tas emot i programmet via den **får**/**ReceiveAsync** eller **OnMessage**/**OnMessageAsync** API: er. Om programmet avslutas innan meddelanden tas emot i programmet, försvinner oåterkalleligt meddelandena.

I den [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) mottagningsläge, meddelanden som hämtas till Prefetch buffert är upptagna till buffert i låst läge och har klockan timeout för lås ska. Om bearbetningen tar så lång tid meddelandet tiden upphör samtidigt som finns i bufferten prefetch eller även om programmet bearbetar meddelandet prefetch bufferten är stor, kan det finnas vissa förvirrande händelser för att programmet ska hantera.

Programmet kan hämta ett meddelande med ett lås som har upphört att gälla eller imminently upphör. I så, fall kan programmet bearbeta meddelandet, men sedan hitta att det går inte att slutföra på grund av ett lås upphör att gälla. Programmet kan kontrollera den [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) egenskapen (som omfattas av klockan skeva mellan broker och klockan för lokal dator). Om meddelandelåset har upphört att gälla, måste programmet ignorera meddelandet. Inga API-anrop eller med meddelandet ska göras. Om meddelandet inte har upphört att gälla men upphör att gälla är nära förestående, låset kan förnyas och utökas med ett annat Lås standardvärdet genom att anropa [meddelande. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Om låset tyst går ut i bufferten prefetch meddelandet behandlas som avbrutet och igen görs tillgänglig för hämtning från kön. Som kan orsaka det hämtas till prefetch buffert; placeras i slutet. Om prefetch bufferten vanligtvis att lista via under förfallodatum för meddelanden, detta medför att meddelanden ska upprepade gånger prefetched men aldrig effektivt levererade oanvändbar (Åtgärdsparametern låst) och slutligen flyttas till kön för obeställbara meddelanden när de Maximalt antal leveranser har överskridits.

Om du behöver en hög grad av tillförlitlighet för meddelandehantering och bearbetningen tar betydande arbete och tid, rekommenderas att du använder funktionen prefetch var eller inte alls.

Om du behöver hög i hela och meddelandebehandling är ofta billiga, ger prefetch betydande prestandafördelarna.

Antalet maximala prefetch och varaktighet för lås som konfigurerats på kö eller prenumeration måste balanseras så att timeout för lås överskrider minst meddelandet ackumulerade förväntade bearbetningstid för den maximala storleken på bufferten prefetch plus ett meddelande. På samma gång, timeout för lås bör inte vara så lång att meddelanden kan överskrida sin högsta [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) när de av misstag ignoreras därför kräver sina lås på att gå ut innan som once.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
