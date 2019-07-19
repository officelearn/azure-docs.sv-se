---
title: Azure Service Bus förfallo datum för meddelande | Microsoft Docs
description: Förfallo tid och tid för att leva Azure Service Bus meddelanden
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 109ecc671b43365c433a626ff8d9fe55a5a626b5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310298"
---
# <a name="message-expiration-time-to-live"></a>Förfallodatum för meddelanden (Time to Live)

Nytto lasten i ett meddelande, eller ett kommando eller en fråga om att ett meddelande skickas till en mottagare, är nästan alltid beroende av en viss typ av förfallo datum för program nivå. Efter en sådan tids gräns levereras inte längre innehållet eller så körs inte den begärda åtgärden längre.

För utvecklings-och test miljöer där köer och ämnen ofta används i samband med delvis körning av program eller program delar, är det också önskvärt att insamlade test meddelanden automatiskt kan samlas in så att nästa test körning kan Starta rensning.

Förfallo datum för enskilda meddelanden kan styras genom att ange system egenskapen [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) , som anger en relativ varaktighet. Förfallo datumet blir en absolut omedelbarhet när meddelandet är i kö i entiteten. Vid detta tillfälle tar egenskapen [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) med värdet [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). TTL-inställningen (Time-to-Live) på ett Service Broker-meddelande är inte tvingande när det inte finns några klienter aktivt som lyssnar.

Tidigare **ExpiresAtUtc** -meddelanden blir inkompatibla för hämtning. Förfallo tiden påverkar inte meddelanden som för närvarande är låsta för leverans. dessa meddelanden hanteras fortfarande som vanligt. Om låset går ut eller meddelandet överges, börjar giltighets tiden att gälla omedelbart.

Även om meddelandet är under låst kan programmet ha ett meddelande som har upphört att gälla. Huruvida programmet är villigt att fortsätta med bearbetning eller väljer att överge meddelandet är upp till Implementeraren.

## <a name="entity-level-expiration"></a>Förfallo datum för enhets nivå

Alla meddelanden som skickas till en kö eller ett ämne är underkastade ett standard förfallo datum som anges på enhets nivå med egenskapen [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) och som även kan anges i portalen när den skapas och justeras senare. Standard förfallo datum används för alla meddelanden som skickas till entiteten där [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) inte uttryckligen anges. Standard förfallo tiden fungerar också som ett tak för **TimeToLive** -värdet. Meddelanden som har en längre **TimeToLive** förfallo tid än standardvärdet justeras tyst till **defaultMessageTimeToLive** -värdet innan de placeras i kö.

> [!NOTE]
> Standardvärdet för [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) för ett brokerat meddelande är [TimeSpan. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) om inget annat anges.
>
> För meddelande enheter (köer och ämnen) är standard förfallo tiden också [TimeSpan. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) för Service Bus standard-och Premium-nivån.  För Basic-nivån är standard förfallo tiden 14 dagar.

Förfallna meddelanden kan alternativt flyttas till en [kö för obeställbara](service-bus-dead-letter-queues.md) meddelanden genom att ange egenskapen [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) , eller genom att markera respektive ruta i portalen. Om alternativet lämnas inaktiverat, släpps utgångna meddelanden. Förfallna meddelanden som flyttats till kön för obeställbara meddelanden kan särskiljas från andra meddelanden om obeställbara meddelanden genom att utvärdera egenskapen [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) som Service Broker lagrar i avsnittet användar egenskaper. värdet är [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) i det här fallet.

I det fall då meddelandet skyddas från förfallo datum under lås och om flaggan har angetts för entiteten, flyttas meddelandet till kön för obeställbara meddelanden när låset överges eller upphör att gälla. Det flyttas dock inte om meddelandet har kvittats, vilket förutsätter att programmet har hanterat det, trots det nominella förfallo datumet.

Kombinationen av [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) och automatisk (och transaktionell) obeställbara meddelanden vid förfallo datum är ett värdefullt verktyg för att fastställa om ett jobb har tilldelats en hanterare eller en grupp med hanterare under en tids gräns hämtas för bearbetning som tids gräns har nåtts.

Anta till exempel att du har en webbplats som behöver köra jobb på ett tillförlitligt sätt på en begränsad Server del och som ibland upplever trafik toppar eller vill vara isolerad mot tillgänglighets avsnitt i den server delen. I det vanliga fallet skickar Server sidans hanterare för de inskickade användar data informationen till en kö och därefter får du ett svar som bekräftar lyckad hantering av transaktionen i en svarskö. Om det finns en trafik ökning och Server dels hanteraren inte kan bearbeta sina efter släpning-objekt i tid, returneras de utgångna jobben i kön för obeställbara meddelanden. Den interaktiva användaren kan meddelas att den begärda åtgärden tar lite längre tid än vanligt, och begäran kan sedan placeras i en annan kö för en bearbetnings väg där det slutliga bearbetnings resultatet skickas till användaren via e-post. 


## <a name="temporary-entities"></a>Temporära entiteter

Service Bus köer, ämnen och prenumerationer kan skapas som temporära entiteter, vilka tas bort automatiskt när de inte har använts under en angiven tids period.
 
Automatisk rensning är användbart i utvecklings-och test scenarier där entiteter skapas dynamiskt och inte rensas efter användningen, på grund av avbrott i testet eller fel söknings körningen. Det är också användbart när ett program skapar dynamiska entiteter, t. ex. en svarskö, för att få svar tillbaka till en webb Server process, eller till ett annat relativt kortvarigt objekt där det är svårt att säkert rensa dessa enheter när objektet instansen försvinner.

Funktionen aktive ras med egenskapen [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) . Den här egenskapen anges till den varaktighet för vilken en entitet måste vara inaktiv (oanvänd) innan den tas bort automatiskt. Det minsta värdet för den här egenskapen är 5.
 
Egenskapen **autoDeleteOnIdle** måste anges via en Azure Resource Manager-åtgärd eller via .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -API: er. Du kan inte ange den i portalen.

## <a name="idleness"></a>Inaktivitetskrav

Det här är vad som anses vara inaktivitet i entiteter (köer, ämnen och prenumerationer):

- Köer
    - Inga sändningar  
    - Inga mottagningar  
    - Inga uppdateringar i kön  
    - Inga schemalagda meddelanden  
    - Ingen Browse/spetsig 
- Ämnen  
    - Inga sändningar  
    - Inga uppdateringar av ämnet  
    - Inga schemalagda meddelanden 
- Prenumerationer
    - Inga mottagningar  
    - Inga uppdateringar av prenumerationen  
    - Inga nya regler har lagts till i prenumerationen  
    - Ingen Browse/spetsig  
 


## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
