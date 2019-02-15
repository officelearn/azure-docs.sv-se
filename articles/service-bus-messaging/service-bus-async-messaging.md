---
title: Asynkrona Service Bus-meddelanden | Microsoft Docs
description: Beskrivning av Azure Service Bus asynkrona meddelanden.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301578"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynkrona meddelandemönster och hög tillgänglighet

Asynkrona meddelanden kan implementeras på ett antal olika sätt. Köer, ämnen och prenumerationer stöder Azure Service Bus asynchronism via en store och vidarebefordra mekanism. Under normal drift av (synkron), skicka meddelanden till köer och ämnen och ta emot meddelanden från köer och prenumerationer. Program som du skriver är beroende av dessa entiteter som alltid är tillgängliga. När entitetshälsa ändras på grund av ett antal olika omständigheter, behöver du ett sätt att ge en minskad funktionen entitet som kan uppfylla de flesta behov.

Program använder vanligtvis asynkrona meddelandemönster för att aktivera ett antal kommunikationsscenarier. Du kan skapa program där klienter kan skicka meddelanden till tjänster, även när tjänsten inte körs. För program att du ska få toppar i kommunikation, en kö kan hjälpa att utjämna belastningen genom att tillhandahålla en plats till bufferten kommunikation. Slutligen kan du få en tydlig och effektiv belastningsutjämnare för att distribuera meddelanden över flera datorer.

Överväg att ett antal olika sätt som de här entiteterna kan visas inte tillgängligt för en hållbar meddelandesystem för att upprätthålla tillgänglighet av någon av dessa enheter. Generellt sett kan vi se entitet som blir otillgänglig för program som vi skriva på följande sätt:

* Det går inte att skicka meddelanden.
* Det går inte att ta emot meddelanden.
* Det går inte att hantera entiteter (skapa, hämta, uppdatera eller ta bort entiteter).
* Det går inte att kontakta tjänsten.

För var och en av de här felen finns olika fellägen som gör att ett program att fortsätta att utföra arbetet på vissa nivå av minskad kapacitet. Ett system som kan skicka meddelanden, men inte tar emot dem fortfarande kan ta emot order från kunder men kan inte bearbeta dessa order. Det här avsnittet beskrivs möjliga problem som kan uppstå och hur dessa problem begränsas. Service Bus har infört ett antal åtgärder som du måste välja och det här avsnittet beskrivs också de regler som reglerar användningen av dessa opt-in-åtgärder.

## <a name="reliability-in-service-bus"></a>Tillförlitlighet i Service Bus
Det finns flera sätt att hantera problem med meddelandet och entiteten och det finns riktlinjer som reglerar lämplig användning av dessa åtgärder. För att förstå riktlinjerna, måste du först förstå vad kan misslyckas i Service Bus. Samtliga av dessa fel brukar vara kortlivade på grund av utformningen av Azure-system. På hög nivå visas de olika orsakerna till otillgängliga på följande sätt:

* Begränsning från ett externt system som Service Bus är beroende av. Begränsning inträffar på samverkan med lagrings- och resurser.
* Problem för ett system som Service Bus är beroende av. Till exempel kan en viss del av lagring uppleva problem.
* Fel i Service Bus på enskild undersystem. I så fall kan en beräkningsnod kan hamna i ett inkonsekvent tillstånd och måste startas om, gör att alla entiteter som den används för att belastningsutjämna till andra noder. Detta kan i sin tur medföra en kort period av långsam meddelandebehandling.
* Fel i Service Bus i ett Azure-datacenter. Det här är en ”katastrof” under vilken systemet kan inte nås för många minuter eller ett par timmar.

> [!NOTE]
> Termen **storage** kan innebära både Azure Storage och SQL Azure.
> 
> 

Service Bus innehåller ett antal åtgärder för de här problemen. I följande avsnitt beskrivs olika problemen och deras respektive åtgärder.

### <a name="throttling"></a>Begränsning
Med Service Bus kan begränsning samverkande meddelande rate management. Varje enskild nod i Service Bus innehåller många entiteter. Var och en av dessa entiteter blir krav på systemet när det gäller processor, minne, lagring och andra aspekter. När någon av dessa fasetter identifierar användning som överskrider definierade tröskelvärden, Service Bus kan neka en viss begäran. Anroparen får en [ServerBusyException] [ ServerBusyException] och återförsök efter 10 sekunder.

Som en migrering av koden läsa felet och stoppa alla återförsök för meddelandet i minst 10 sekunder. Eftersom felet kan inträffa mellan delar av programmet för kunden, förväntas det att varje del oberoende körs logik för omprövning. Koden kan minska sannolikheten för begränsas genom att aktivera partitionering på en kö eller ämne.

### <a name="issue-for-an-azure-dependency"></a>Problem för beroende av ett Azure
Andra komponenter i Azure kan ibland ha problem med tjänsten. Till exempel när ett system som använder Service Bus uppgraderas uppleva systemet tillfälligt minskade funktioner. Du kan undvika dessa typer av problem, Service Bus regelbundet undersöker och implementerar åtgärder. Sidoeffekter av dessa åtgärder visas. För att hantera problem med storage implementerar till exempel Service Bus ett system som gör att skicka meddelandeåtgärder till arbetar konsekvent. På grund av minskningen, kan skickade meddelandet ta upp till 15 minuter att visas i den berörda kö eller prenumeration och var redo för en receive-åtgärd. Generellt sett de flesta entiteter inte det här problemet uppstår. Dock krävs givet antalet entiteter i Service Bus i Azure, den här säkerhetsfunktionen ibland för en liten delmängd av Service Bus-kunder.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus-fel på ett enda undersystem
Med alla program kan omständigheter orsaka en intern komponenten i Service Bus blir inkonsekventa. När Service Bus upptäcker att detta, samlar in data från program som gör det enklare att diagnostisera vad som hände. När data har samlats in startas programmet i ett försök att returnera den till ett konsekvent tillstånd. Den här processen sker ganska snabbt och resultatet i en entitet som ser ut att vara otillgänglig för upp till ett par minuter, även om de typiska stopptider är mycket kortare.

I dessa fall kan klientprogrammet genererar en [System.TimeoutException] [ System.TimeoutException] eller [MessagingException] [ MessagingException] undantag. Service Bus innehåller en lösning för det här problemet i form av logik för omprövning av automatiserade klienten. När försök under perioden är slut och meddelandet skickas inte, du kan utforska med annan anges i artikeln om [hantera avbrott och katastrofer][handling outages and disasters].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna för asynkrona meddelanden i Service Bus, Läs mer om [hantera avbrott och katastrofer][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
