---
title: Azure Service Bus prefetch meddelanden | Microsoft Docs
description: "Förbättra prestanda genom att förhämtar Azure Service Bus-meddelanden."
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
ms.date: 10/04/2017
ms.author: sethm
ms.openlocfilehash: 4a4a06f90c2c48d35d836f0be89fec9cc47f32c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="prefetch-azure-service-bus-messages"></a>Prefetch Azure Service Bus-meddelanden

När *Prefetch* är aktiverad i någon av de officiella Service Bus-klienterna, mottagaren tyst hämtar fler meddelanden, upp till den [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) gränsen, utöver vad programmet först efterfrågat.

En enda initial [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) eller [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) anrop därför får ett meddelande för omedelbar konsumtion som returneras så snart som tillgängliga. Klienten erhåller sedan ytterligare meddelanden i bakgrunden, för att fylla prefetch bufferten.

## <a name="enable-prefetch"></a>Aktivera prefetch

I .NET, aktivera Prefetch funktionen genom att ange den [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) -egenskapen för en **MessageReceiver**, **QueueClient**, eller **SubscriptionClient**  till ett tal som är större än noll. Ange värdet till noll inaktiveras prefetch.

Du kan enkelt lägga till den här inställningen mottagarsidan av den [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) eller [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) exempel inställningar för att se effekten i sådana sammanhang.

När meddelanden är tillgängliga i prefetch bufferten eventuella **Receive**/**ReceiveAsync** anrop uppfylls direkt från bufferten och bufferten fylls i den Background när utrymmet blir tillgänglig. Om det finns inga meddelanden för leverans, tömmer receive-åtgärden i bufferten och väntar eller block som förväntat.

Prefetch också fungerar på samma sätt med den [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) och [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API: er.

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Om det är snabbare och varför Prefetch inte är standardalternativet?

Prefetch snabbare flödet meddelandet genom att ett meddelande som är tillgängliga för hämtning av lokala när och innan programmet begär en. Denna genomströmning vinst är resultatet av en kompromiss beslut som utvecklaren av tillämpningsprogrammet måste uttryckligen göra:

Med den [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete) mottagningsläge, alla meddelanden som skaffas i prefetch bufferten är inte längre tillgängliga i kön och finns bara i InMemory-prefetch bufferten tills de tas emot av programmet via den **får**/**ReceiveAsync** eller **OnMessage**/**OnMessageAsync** API: er. Om programmet slutar innan meddelandena som tas emot av programmet, förloras overifierade dessa meddelanden.

I den [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock) mottagningsläge, meddelanden som hämtas in Prefetch buffert har köpt i bufferten i låst tillstånd och har klockan timeout för lås markeringar. Om prefetch bufferten är stort och bearbetning tar så lång tid meddelandet Lås gälla vid som finns i bufferten prefetch eller även om programmet bearbetar meddelandet, kan det finnas vissa förvirrande händelser för att programmet ska hantera.

Programmet kan få ett meddelande med ett utgånget eller imminently utgående Lås. I så fall, kan programmet bearbeta meddelandet, men Leta reda på att det går inte att slutföra på grund av en Låsets upphörande. Programmet kan kontrollera den [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) (som regleras klockavvikelser mellan broker och lokala datorns klocka). Om meddelandet låset har upphört att gälla, måste programmet ignorera meddelandet. Inga API-anrop eller med meddelandet ska göras. Om meddelandet inte har upphört att gälla men förfallodatum är nära förestående låset kan förnyas och utökas med en annan standardperioden Lås genom att anropa [meddelande. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Om låset tyst går ut i bufferten prefetch behandlas som avbrutna meddelandet och igen görs tillgängliga för hämtning från kön. Som kan göra att det ska hämtas till prefetch bufferten; Placera i slutet. Om prefetch bufferten vanligtvis att lista via under meddelande upphör att gälla, detta medför att meddelanden ska upprepade gånger prefetched men aldrig effektivt levererat oanvändbar (lagligt låst) och slutligen flyttas till kön för obeställbara en gång i leverans av maximalt antal överskrids.

Om du behöver en hög grad av tillförlitlighet för meddelandebehandling och har betydande arbete och tid, rekommenderas att du använder funktionen prefetch restriktiva eller inte alls.

Om du behöver hög under hela och meddelandebehandling är ofta billiga, ger prefetch betydande prestandafördelarna.

Antalet maximala prefetch och lås tidslängden som har konfigurerats på den eller de prenumerationen måste balanseras så att timeout för lås överskrider minst cumulative förväntade meddelandebehandling tid för den maximala storleken på bufferten som prefetch plus ett meddelande. På samma gång timeout för lås bör inte vara så lång tid att meddelanden kan överskrida sitt högsta [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) när de av misstag tas bort, vilket kräver deras lås att gälla innan som levereras på nytt.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
