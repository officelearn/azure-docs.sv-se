---
title: Förfallodatum för Azure Service Bus-meddelanden | Microsoft Docs
description: Förfallodatum och TTL-värde för Azure Service Bus-meddelanden
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
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: c1c38370ae508dffc6888dd7e94514406b8da327
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405279"
---
# <a name="message-expiration-time-to-live"></a>Förfallodatum för meddelanden (Time to Live)

Nyttolasten i ett meddelande eller ett kommando eller en förfrågan som ett meddelande som förmedlas till en mottagare är nästan alltid omfattas av någon form av programnivå utgångsdatum. Innehållet levereras inte längre efter tidsgräns, eller den begärda åtgärden körs inte längre.

För utveckling och test-miljöer som köer och ämnen används ofta i samband med partiella körningar av program eller program delar kan är det också önskvärt för tvinnad testmeddelanden automatiskt vara skräpinsamlats så att nästa testkörning kan Starta ren.

Förfallodatum för alla enskilda meddelanden kan kontrolleras genom att ange den [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) Systemegenskapen som anger en relativ varaktighet. Förfallodatum blir ett absolut ögonblick när meddelandet är i kö till entiteten. Då den [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) egenskapen tar på värdet [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Senaste den **ExpiresAtUtc** omedelbar, meddelanden blir inte berättigade för hämtning. Förfallodatum påverkar inte meddelanden som för närvarande är låsta för leverans. Dessa meddelanden hanteras fortfarande normalt. Om låset upphör att gälla eller meddelandet överges, tar förfallodatum börjar gälla omedelbart.

Meddelandet är under lås, kanske programmet tillgång ett meddelande som har upphört att gälla. Om programmet är villig att gå vidare med bearbetning eller väljer att lämna meddelandet är upp till Implementeraren.

## <a name="entity-level-expiration"></a>På entitetsnivå upphör att gälla

Alla meddelanden som skickas till en kö eller ämne som är föremål för en standard-utgångsdatum som anges på entiteten med det [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) egenskap och som kan också ange i portalen när du skapar och justeras senare. Standard-förfallodatum används för alla meddelanden som skickas till enheten där [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) anges inte uttryckligen. Standard-giltighetstid fungerar också som ett tak för den **TimeToLive** värde. Meddelanden som har en längre **TimeToLive** giltighetstid än standardvärdet tyst justeras efter den **defaultMessageTimeToLive** värdet innan du kan i kön.

Utgångna meddelanden kan du kan också flyttas till en [obeställbara meddelanden](service-bus-dead-letter-queues.md) genom att ange den [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) egenskap eller respektive kryssruta i portalen. Om alternativet är inaktiverat, ignoreras utgångna meddelanden. Utgångna meddelanden i kön för obeställbara meddelanden kan särskiljas från andra lettered förlorade meddelanden genom att utvärdera den [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) egenskap som den asynkrona meddelandekön lagrar i avsnittet användare egenskaper; värdet är [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) i det här fallet.

I det tidigare nämnda fallet där meddelandet är skyddat från upphör att gälla samtidigt under Lås och om flaggan är inställd på entiteten flyttas meddelandet till kön för obeställbara låset överges eller upphör att gälla. Det är dock inte flytta om meddelandet är har reglerats, vilket förutsätter att programmet har hanterat, trots nominell förfallodatum.

Kombinationen av [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) och automatisk (och transaktionell) dead-lettering vid utgången är ett värdefullt verktyg för att upprätta förtroende om ett jobb som ges till en hanterare eller en grupp av hanterare under en tidsgräns hämtas för bearbetning som tidsgränsen har nåtts.

Anta exempelvis att en webbplats som kräver att tillförlitligt köra jobb på en skala begränsad serverdel och som ibland upplevelser trafik toppar eller vill isoleras mot tillgänglighet avsnitt av den serverdelen. I vanliga fall serversidan hanteraren för skickade användardata skickar informationen till en kö och därefter tar emot ett svar som bekräftar lyckade hanteringen av transaktionen i en kö för meddelandesvar. Om det finns ett trafikstopp och backend-hanteraren kan inte behandla dess eftersläpning för objekt i tid, returneras jobb som har upphört att gälla i kön för obeställbara meddelanden. Den interaktiva användaren kan bli meddelad att den begärda åtgärden kan ta lite längre tid än vanligt begäran kan sedan placeras på en annan kö för bearbetning av banor där eventuell bearbetning resultatet skickas till användaren via e-post. 

## <a name="temporary-entities"></a>Tillfällig entiteter

Service Bus-köer, ämnen och prenumerationer kan skapas som tillfällig enheter som tas bort automatiskt när de inte har använts under en angiven tidsperiod.
 
Automatisk rensning är användbart i utvecklings- och scenarier där entiteter skapas dynamiskt och inte rensas efter användning på grund av vissa avbrott i testning eller felsökning kör. Det är också användbart när ett program skapar dynamiska entiteter, till exempel en svarskö för att ta emot svar tillbaka till en process som webbservern eller till ett annat relativt kortvarig objekt där det är svårt att på ett tillförlitligt sätt att rensa upp dessa entiteter när objektet instans försvinner.

Funktionen är aktiverad med hjälp av den [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) egenskapen, som anger den varaktighet som en entitet måste vara inaktiv (som inte används) innan den tas bort automatiskt. Minimilängden är 5 minuter.
 
Den **autoDeleteOnIdle** egenskapen måste anges via en Azure Resource Manager-åtgärd eller via .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API: er. Det kan inte ställas in via portalen.


## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)