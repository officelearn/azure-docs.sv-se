---
title: Service Bus asynkrona meddelanden | Microsoft-dokument
description: Lär dig hur Azure Service Bus stöder asynkronism via en butik och framåt mekanism med köer, ämnen och prenumerationer.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 554260f403104d815b9b63c576c7ba0a2f3cf1e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761040"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynkrona meddelandemönster och hög tillgänglighet

Asynkrona meddelanden kan implementeras på en mängd olika sätt. Med köer, ämnen och prenumerationer stöder Azure Service Bus asynkronism via en butik och framåt-mekanism. I normal (synkron) åtgärd skickar du meddelanden till köer och ämnen och får meddelanden från köer och prenumerationer. Program som du skriver beror på att dessa entiteter alltid är tillgängliga. När entiteten hälsa ändras, på grund av en mängd olika omständigheter, behöver du ett sätt att ge en minskad kapacitet entitet som kan uppfylla de flesta behov.

Program använder vanligtvis asynkrona meddelandemönster för att aktivera ett antal kommunikationsscenarier. Du kan skapa program där klienter kan skicka meddelanden till tjänster, även när tjänsten inte körs. För program som upplever kommunikationsutbrott kan en kö hjälpa till att jämna ut belastningen genom att tillhandahålla en plats för buffring av kommunikation. Slutligen kan du få en enkel men effektiv belastningsutjämnare för att distribuera meddelanden över flera datorer.

För att upprätthålla tillgängligheten för någon av dessa entiteter bör du överväga ett antal olika sätt på vilka dessa entiteter kan visas otillgängliga för ett varaktigt meddelandesystem. Generellt sett ser vi att entiteten blir otillgänglig för program som vi skriver på följande olika sätt:

* Det gick inte att skicka meddelanden.
* Det gick inte att ta emot meddelanden.
* Det gick inte att hantera entiteter (skapa, hämta, uppdatera eller ta bort entiteter).
* Det gick inte att kontakta tjänsten.

För vart och ett av dessa fel finns det olika fellägen som gör att ett program kan fortsätta att utföra arbete på någon nivå med nedsatt kapacitet. Ett system som kan skicka meddelanden men inte ta emot dem kan till exempel fortfarande ta emot order från kunder men kan inte bearbeta dessa order. I det här avsnittet beskrivs potentiella problem som kan uppstå och hur dessa problem mildras. Service Bus har infört ett antal mildrande åtgärder som du måste välja, och det här avsnittet diskuterar också reglerna för användningen av dessa opt-in mildrande åtgärder.

## <a name="reliability-in-service-bus"></a>Tillförlitlighet i servicebuss
Det finns flera sätt att hantera meddelande- och entitetsproblem, och det finns riktlinjer för lämplig användning av dessa åtgärder. För att förstå riktlinjerna måste du först förstå vad som kan misslyckas i Service Bus. På grund av utformningen av Azure-system tenderar alla dessa problem att vara kortlivade. På en hög nivå, de olika orsakerna till otillgänglighet visas enligt följande:

* Begränsning från ett externt system som Service Bus är beroende av. Begränsning sker från interaktioner med lagrings- och beräkningsresurser.
* Problem för ett system som Service Bus är beroende av. En viss del av lagringen kan till exempel stöta på problem.
* Fel på Service Bus på ett enda delsystem. I det här fallet kan en beräkningsnod hamna i ett inkonsekvent tillstånd och måste starta om sig själv, vilket gör att alla entiteter som används för att belastningsbalansen till andra noder. Detta i sin tur kan orsaka en kort period av långsam meddelandebearbetning.
* Fel på Service Bus i ett Azure-datacenter. Detta är ett "katastrofalt fel" under vilket systemet inte kan nås i många minuter eller några timmar.

> [!NOTE]
> Termen **lagring** kan innebära både Azure Storage och SQL Azure.
> 
> 

Service Bus innehåller ett antal åtgärder för dessa problem. I följande avsnitt beskrivs varje fråga och deras respektive mildrande åtgärder.

### <a name="throttling"></a>Begränsning
Med Service Bus möjliggör begränsning kooperativ meddelandefrekvenshantering. Varje enskild Service Bus-nod rymmer många entiteter. Var och en av dessa enheter ställer krav på systemet när det gäller CPU, minne, lagring och andra aspekter. När någon av dessa aspekter upptäcker användning som överskrider definierade tröskelvärden kan Service Bus neka en viss begäran. Anroparen tar emot en [ServerBusyException][ServerBusyException] och försöker igen efter 10 sekunder.

Som en begränsning måste koden läsa felet och stoppa alla återförsök i meddelandet i minst 10 sekunder. Eftersom felet kan inträffa över delar av kundprogrammet, förväntas det att varje del självständigt kör logiken för återförsök. Koden kan minska sannolikheten för att begränsas genom att aktivera partitionering i en kö eller ett ämne.

### <a name="issue-for-an-azure-dependency"></a>Problem för ett Azure-samband
Andra komponenter i Azure kan ibland ha serviceproblem. När till exempel ett system som Service Bus använder uppgraderas kan det systemet tillfälligt uppleva minskade funktioner. För att komma runt dessa typer av problem undersöker och implementerar Service Bus regelbundet och implementerar begränsningsåtgärder. Biverkningar av dessa mildrande åtgärder visas. För att till exempel hantera tillfälliga problem med lagring implementerar Service Bus ett system som gör att meddelandesändningsåtgärder kan fungera konsekvent. På grund av begränsningens natur kan det ta upp till 15 minuter innan ett skickat meddelande visas i den berörda kön eller prenumerationen och vara redo för en mottagningsåtgärd. Generellt sett kommer de flesta entiteter inte uppleva detta problem. Med tanke på antalet entiteter i Service Bus i Azure behövs dock den här begränsningen ibland för en liten delmängd av Service Bus-kunder.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus-fel på ett enda delsystem
Med alla program kan omständigheterna leda till att en intern komponent i Service Bus blir inkonsekvent. När Service Bus upptäcker detta samlar den in data från programmet för att hjälpa till att diagnostisera vad som hände. När data har samlats in startas programmet om i ett försök att återställa det till ett konsekvent tillstånd. Denna process sker ganska snabbt, och resulterar i en entitet som verkar vara otillgänglig i upp till några minuter, men typiska ner tider är mycket kortare.

I dessa fall genererar klientprogrammet ett [System.TimeoutException-][System.TimeoutException] eller [MessagingException-undantag.][MessagingException] Service Bus innehåller en begränsning för det här problemet i form av automatisk klientförsökslogik. När återförsöksperioden är uttömd och meddelandet inte levereras kan du utforska med andra som nämns i artikeln om [hantering av avbrott och katastrofer][handling outages and disasters].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i asynkrona meddelanden i Service Bus, läs mer om hantering av [avbrott och katastrofer][handling outages and disasters].

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
