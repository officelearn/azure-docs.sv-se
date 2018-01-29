---
title: "Utgångna Azure Service Bus | Microsoft Docs"
description: "Förfallodatum och TTL av Azure Service Bus-meddelanden"
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 6e1f6177ccacf24955763982189bcdb1ef69c788
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="message-expiration-time-to-live"></a>Utgångna (Time to Live)

Nyttolasten i ett meddelande eller ett kommando eller en förfrågan som meddelandet förmedlas till en mottagare har nästan alltid följer någon form av programnivå utgångsdatum. Innehållet levereras inte längre efter en tidsgräns, eller den begärda åtgärden är inte längre köras.

För utveckling och testmiljöer som köer och ämnen används ofta i samband med partiellt körs av program eller program delar är det också önskvärt för tvinnad testmeddelanden ska automatiskt skräpinsamlats så att nästa testkörning kan Starta ren.

Upphör att gälla för alla enskilda meddelanden kan kontrolleras genom att ange den [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) Systemegenskapen som anger en relativ varaktighet. Upphör att gälla blir ett absolut ögonblick när meddelandet är i kö i entiteten. Då den [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) egenskap tar på sig värdet [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Senaste den **ExpiresAtUtc** snabbmeddelanden, meddelanden blir inte tillgänglig för hämtning. Upphör att gälla påverkar inte meddelanden som för närvarande är låsta för leverans. Dessa meddelanden hanteras fortfarande normalt. Om låset upphör att gälla eller meddelandet överges, tar upphör att gälla omedelbart.

När meddelandet är under lås, kanske programmet som har tillgång till ett meddelande som har upphört att gälla. Om programmet är villigt att gå vidare med bearbetning eller väljer att avbryta meddelandet är Implementeraren.

## <a name="entity-level-expiration"></a>På entitetsnivå upphör att gälla

Alla meddelanden som skickas till en kö eller ett ämne som är föremål en standard giltighetstid som är inställt på enheten med den [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) egenskap och som kan också vara i portalen under skapandet och anpassade senare. Standard upphör att gälla används för alla meddelanden som skickas till enheten där [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) inte uttryckligen har angett. Standard upphör att gälla fungerar också som ett tak för den **TimeToLive** värde. Meddelanden som har en längre **TimeToLive** giltighetstid än standardvärdet tyst justeras efter den **defaultMessageTimeToLive** värde innan köas.

Inaktuella meddelanden kan eventuellt flyttas till en [kö med olevererade brev](service-bus-dead-letter-queues.md) genom att ange den [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) egenskap eller en kryssruta respektive i portalen. Om alternativet är inaktiverat, tas inaktuella meddelanden bort. Inaktuella meddelanden i kön för obeställbara kan särskiljas från andra lettered förlorade meddelanden genom att utvärdera den [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) egenskap som Service broker lagras i avsnittet användare egenskaper; värdet är [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) i det här fallet.

I dessa fall där meddelandet är skyddat från upphör att gälla när under Lås och om flaggan är inställd på entiteten flyttas meddelandet till kön för obeställbara låset överges eller upphör att gälla. Det är dock inte flytta om meddelandet har regleras, vilket förutsätter att programmet har hanterat, trots nominell upphör att gälla.

Kombinationen av [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) och automatisk (och transaktionell) dead-lettering vid utgången är ett ovärderligt verktyg för att upprätta förtroende om ett jobb som anges för en hanterare eller en grupp av hanterare under en tidsgräns hämtas för bearbetning som tidsgränsen har nåtts.

Anta till exempel att en webbplats som måste köra jobb på ett tillförlitligt sätt på en skala begränsad serverdel och som ibland upplevelser trafik ger spikar i diagrammet eller vill isoleras mot tillgänglighet avsnitt av det backend. I vanliga fall serversidan hanteraren för skickade användardata skickar informationen till en kö och därefter får ett svar som bekräftar lyckade hanteringen av transaktionen i en reply-kö. Om det är en topp trafik och backend-hanteraren kan inte behandla eftersläpning element i tid, returneras utgångna jobb i kö för obeställbara meddelanden. Interaktiv användare kan få ett meddelande att den begärda åtgärden kan ta lite längre tid än vanligt och begäran kan sedan placeras på en annan kö för bearbetningssökvägen där eventuell bearbetning resultatet skickas till användaren via e-post. 

## <a name="temporary-entities"></a>Tillfällig entiteter

Service Bus-köer, ämnen och prenumerationer kan skapas som tillfällig enheter som tas bort automatiskt när de inte har använts för en angiven tidsperiod.
 
Automatisk rensning är användbart i utvecklings- och scenarier där entiteter skapas dynamiskt och inte rensa efter användning, på grund av vissa avbrott i test- eller felsökning kör. Det är också användbart när ett program skapar dynamiska entiteter, till exempel en kö för svar, för att ta emot svar tillbaka till en process som webbservern eller till ett annat relativt tillfällig objekt när det är svårt att på ett tillförlitligt sätt Rensa dessa enheter när objektet instansen försvinner.

Funktionen är aktiverad med hjälp av den [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) -egenskap som anger den varaktighet som en enhet måste vara inaktiv (som inte används) innan den tas bort automatiskt. Minimilängden är 5 minuter.
 
Den **autoDeleteOnIdle** egenskapen måste anges via en Azure Resource Manager-åtgärd och .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API: er. Det går inte att ange via portalen.


## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)