---
title: Azure Service Bus - meddelandet upphör att gälla
description: I den här artikeln beskrivs hur förfallodatum och tid att leva i Azure Service Bus-meddelanden. Efter en sådan tidsfrist levereras inte längre meddelandet.
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
ms.openlocfilehash: e86c92fa1cfb13929d5617502224f479709efdd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756342"
---
# <a name="message-expiration-time-to-live"></a>Förfallodatum för meddelanden (Time to Live)

Nyttolasten i ett meddelande, eller ett kommando eller en förfrågan som ett meddelande förmedlar till en mottagare, är nästan alltid föremål för någon form av utgångsdatum på programnivå. Efter en sådan tidsfrist levereras inte längre innehållet eller så utförs inte längre den begärda åtgärden.

För utvecklings- och testmiljöer där köer och ämnen ofta används i samband med partiella körningar av program eller programdelar är det också önskvärt att strandsatta testmeddelanden automatiskt samlas in så att nästa testkörning kan börja rent.

Förfallodatumet för ett enskilt meddelande kan styras genom att ange egenskapen [TimeToLive-system,](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) som anger en relativ varaktighet. Förfallodatumet blir ett absolut ögonblick när meddelandet är enqueued i entiteten. Då tar egenskapen [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) på värdet [**(EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**).](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) Inställningen time-to-live (TTL) på ett förmedlat meddelande tillämpas inte när det inte finns några klienter som aktivt lyssnar.

Förbi **ExpiresAtUtc-snabben** blir meddelanden inte berättigade till hämtning. Förfallodatumet påverkar inte meddelanden som för närvarande är låsta för leverans. dessa meddelanden hanteras fortfarande normalt. Om låset upphör att gälla eller om meddelandet överges börjar förfallodatumet omedelbart.

Medan meddelandet är låst kan programmet ha tillgång till ett meddelande som har upphört att gälla. Om programmet är villigt att gå vidare med bearbetning eller väljer att överge meddelandet är upp till genomföraren.

## <a name="entity-level-expiration"></a>Förfallodatum på entitetsnivå

Alla meddelanden som skickas till en kö eller ett ämne är föremål för en standardförfallotid som anges på entitetsnivå med egenskapen [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) och som också kan ställas in i portalen när den skapas och justeras senare. Standardförfallodatumet används för alla meddelanden som skickas till entiteten där [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) inte uttryckligen anges. Standardutgången fungerar också som ett tak för **TimeToLive-värdet.** Meddelanden som har en längre **TimeToLive-förfallotid** än standardvärdet justeras tyst till **standardvärdetMessageTimeToLive** innan de tillämpas.

> [!NOTE]
> Standardvärdet [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) för ett förmedlat meddelande är [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) om inget annat anges.
>
> För meddelandeentiteter (köer och ämnen) är standardtiden för förfallodatum också [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) för standardnivåer för servicebuss och premiumnivåer.  För den grundläggande nivån är standardtiden för förfallodatum 14 dagar.

Utgångna meddelanden kan eventuellt flyttas till en kö för [obeställbara meddelanden](service-bus-dead-letter-queues.md) genom att ange egenskapen [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) eller markera respektive ruta i portalen. Om alternativet lämnas inaktiverat tas utgångna meddelanden bort. Utgångna meddelanden som flyttas till kön för obeställbara meddelanden kan skiljas från andra meddelanden med obeställda meddelanden genom att utvärdera egenskapen [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) som mäklaren lagrar i avsnittet användaregenskaper. värdet är [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) i det här fallet.

I det ovannämnda fallet där meddelandet är skyddat från förfallodatum under lås och om flaggan är inställd på entiteten flyttas meddelandet till kön för obeställbara meddelanden när låset överges eller upphör att gälla. Det flyttas dock inte om meddelandet har kvittats, vilket sedan förutsätter att programmet har hanterat det, trots den nominella förfallotiden.

Kombinationen av [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) och automatisk (och transaktionell) dödbokstavning vid utgången är ett värdefullt verktyg för att skapa förtroende för om ett jobb som ges till en hanterare eller en grupp hanterare under en tidsfrist hämtas för bearbetning när tidsfristen har nåtts.

Tänk dig till exempel en webbplats som måste utföra jobb på ett tillförlitligt sätt på en skalenlig backend och som ibland upplever trafiktoppar eller vill isoleras mot tillgänglighetsavsnitt av den backend. I det vanliga fallet skickar serverhanteraren för de inlämnade användardata informationen till en kö och får därefter ett svar som bekräftar en lyckad hantering av transaktionen i en svarskö. Om det finns en trafiktopp och backend-hanteraren inte kan bearbeta sina eftersläpningsobjekt i tid, returneras de utgångna jobben i kön för obeställbara meddelanden. Den interaktiva användaren kan meddelas om att den begärda åtgärden kommer att ta lite längre tid än vanligt, och begäran kan sedan placeras i en annan kö för en bearbetningssökväg där det slutliga bearbetningsresultatet skickas till användaren via e-post. 


## <a name="temporary-entities"></a>Tillfälliga enheter

Service Bus-köer, ämnen och prenumerationer kan skapas som tillfälliga entiteter, som tas bort automatiskt när de inte har använts under en viss tidsperiod.
 
Automatisk rensning är användbart i utvecklings- och testscenarier där entiteter skapas dynamiskt och inte rensas efter användning, på grund av vissa avbrott i testet eller felsökningskörningen. Det är också användbart när ett program skapar dynamiska entiteter, till exempel en svarskö, för att ta emot svar tillbaka till en webbserverprocess eller till ett annat relativt kortlivade objekt där det är svårt att på ett tillförlitligt sätt rensa dessa entiteter när objektet instans försvinner.

Funktionen är aktiverad med egenskapen [autoDeleteOnIdle.](/azure/templates/microsoft.servicebus/namespaces/queues) Den här egenskapen är inställd på den varaktighet för vilken en entitet måste vara inaktiv (oanvänd) innan den tas bort automatiskt. Minimivärdet för den här egenskapen är 5.
 
Egenskapen **autoDeleteOnIdle** måste anges via en Azure Resource Manager-åtgärd eller via .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API:er. Du kan inte ställa in den i portalen.

## <a name="idleness"></a>Sysslolöshet

Här är vad som anses sysslolös av entiteter (köer, ämnen och prenumerationer):

- Köer
    - Inga skickar  
    - Ingen tar emot  
    - Inga uppdateringar av kön  
    - Inga schemalagda meddelanden  
    - Ingen bläddra/kika 
- Ämnen  
    - Inga skickar  
    - Inga uppdateringar av ämnet  
    - Inga schemalagda meddelanden 
- Prenumerationer
    - Ingen tar emot  
    - Inga uppdateringar av prenumerationen  
    - Inga nya regler har lagts till i prenumerationen  
    - Ingen bläddra/kika  
 


## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
