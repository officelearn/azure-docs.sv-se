---
title: Förfruhavanden för Azure Service Bus | Microsoft-dokument
description: Förbättra prestanda genom att förbeställa Azure Service Bus-meddelanden. Meddelanden är lätt tillgängliga för lokal hämtning innan programbegäranden för dem.
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
ms.openlocfilehash: 80717ab940d27e9bf108b3740309bcd7d71668fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760665"
---
# <a name="prefetch-azure-service-bus-messages"></a>Azure Service Bus-meddelanden före prefetch

När *Prefetch* är aktiverat i någon av de officiella Service Bus-klienterna, förvärvar mottagaren tyst fler meddelanden, upp till [PrefetchCount-gränsen,](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) utöver vad programmet ursprungligen bad om.

Ett enda första [Receive-](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) eller [ReceiveAsync-anrop](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) hämtar därför ett meddelande för omedelbar förbrukning som returneras så snart som möjligt. Klienten hämtar sedan ytterligare meddelanden i bakgrunden för att fylla prefetch-bufferten.

## <a name="enable-prefetch"></a>Aktivera prefetch

Med .NET aktiverar du funktionen Prefetch genom att ange egenskapen [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) för en **MessageReceiver,** **QueueClient**eller **SubscriptionClient** till ett tal som är större än noll. Om du ställer in värdet på noll stängs förfall.

Du kan enkelt lägga till den här inställningen på mottagningssidan av inställningarna [för KöerGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) eller [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) för att se effekten i dessa sammanhang.

Medan meddelanden är tillgängliga i prefetch-bufferten, uppfylls alla efterföljande **Receive**/**ReceiveAsync-anrop** omedelbart från bufferten och bufferten fylls på i bakgrunden när utrymme blir tillgängligt. Om det inte finns några meddelanden tillgängliga för leverans töms bufferten och väntar eller blockerar, som förväntat.

Prefetch fungerar också på samma sätt med [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) och [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API:er.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Om det är snabbare, varför är Prefetch inte standardalternativet?

Prefetch snabbar upp meddelandeflödet genom att ha ett meddelande lätt tillgängligt för lokal hämtning när och innan programmet ber om en. Den här genomströmningsvinsten är resultatet av en kompromiss som programförfattaren uttryckligen måste göra:

Med mottagningsläget [Mot OchDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) är alla meddelanden som hämtas till prefetch-bufferten inte längre tillgängliga i kön och finns bara i bufferten för förfärning i minnet tills de tas emot i programmet via **API:erna Receive**/**Receive ReceiveAsync** eller **OnMessage**/**OnMessageAsync.** Om programmet avslutas innan meddelandena tas emot i programmet går dessa meddelanden oåterkalleligen förlorade.

I ta emot [peeklock-läget](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) hämtas meddelanden som hämtas till prefetch-bufferten till bufferten i låst tillstånd och har timeout-klockan för låset som tickar. Om prefetch bufferten är stor, och bearbetningen tar så lång tid att meddelandelås upphör att gälla när de finns i prefetch bufferten eller ens medan programmet bearbetar meddelandet, kan det finnas några förvirrande händelser för programmet att hantera.

Programmet kan hämta ett meddelande med ett utgånget eller omedelbart utgående lås. I så fall kan programmet bearbeta meddelandet, men sedan upptäcker att det inte kan slutföra det på grund av ett lås förfallodatum. Ansökan kan kontrollera [egenskapen LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) (som är föremål för klocksnedställning mellan mäklaren och den lokala maskinklockan). Om meddelandelåset har upphört att gälla måste programmet ignorera meddelandet. inget API-anrop på eller med meddelandet ska göras. Om meddelandet inte har upphört att gälla men förfallodatum är nära förestående kan låset förnyas och utökas med en annan standardlåsperiod genom att ringa [meddelande. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Om låset upphör att gälla i förfämningsbufferten behandlas meddelandet som övergivet och görs återigen tillgängligt för hämtning från kön. Det kan leda till att den hämtas in i prefetchbufferten. placeras i slutet. Om prefetch-bufferten vanligtvis inte kan bearbetas under meddelandets utgångsdatum, medför detta att meddelanden upprepade gånger prefetched men aldrig effektivt levereras i ett användbart (giltigt låst) tillstånd, och så småningom flyttas till kön för obeställbara maximalt antal leveranser överskrids.

Om du behöver en hög grad av tillförlitlighet för meddelandebehandling, och bearbetning tar betydande arbete och tid, rekommenderas att du använder prefetch funktionen försiktigt, eller inte alls.

Om du behöver högt dataflöde och meddelandebearbetning är ofta billigt, prefetch ger betydande genomströmning fördelar.

Det maximala antalet prefetch och låstiden som konfigurerats i kön eller prenumerationen måste balanseras så att låstidsgränsen åtminstone överskrider den kumulativa förväntade meddelandebearbetningstiden för den maximala storleken på prefetch-bufferten plus ett meddelande. Samtidigt bör låstidsgränsen inte vara så lång att meddelanden kan överskrida sin maximala [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) när de av misstag tappas, vilket kräver att deras lås upphör att gälla innan de levereras om.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
