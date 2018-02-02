---
title: Asynkrona Service Bus-meddelanden | Microsoft Docs
description: Beskrivning av Azure Service Bus asynkrona meddelanden.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: e48e95d99847e68bdb218b341ad2fbcd44eb31f4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynkrona meddelandemönster och hög tillgänglighet

Asynkrona meddelanden kan implementeras i en mängd olika sätt. Köer, ämnen och prenumerationer stöder Azure Service Bus asynchronism via en store och vidarebefordra mekanism. Under normal drift av (synkron), skicka meddelanden till köer och ämnen och ta emot meddelanden från köer och -prenumerationer. Program som du skriver är beroende av dessa enheter alltid är tillgängliga. När entiteten hälsotillståndet ändras på grund av ett antal olika omständigheter, behöver du ett sätt att ge en minskad kapacitet entitet som kan uppfylla de flesta behov.

Program använder vanligtvis asynkrona meddelandemönster för att aktivera ett antal kommunikationsscenarier. Du kan skapa program som klienter kan skicka meddelanden till tjänster, även när tjänsten inte körs. För program nivå belastning i kommunikation, en kö kan hjälpa dig att du ska få belastningen genom att tillhandahålla en plats till bufferten kommunikationen. Slutligen kan du få en tydlig och effektiv belastningsutjämnare distribuerar meddelanden över flera datorer.

Överväg att ett antal olika sätt som dessa enheter kan visas inte tillgängligt för en beständig meddelandesystem för att upprätthålla tillgänglighet av någon av dessa enheter. Generellt sett finns vi entiteten vara tillgänglig för program som vi skriva i olika sätt:

* Det går inte att skicka meddelanden.
* Det går inte att ta emot meddelanden.
* Det går inte att hantera enheter (skapa, hämta, uppdatera eller ta bort enheter).
* Det går inte att kontakta tjänsten.

För var och en av dessa fel finns olika feltillstånd som gör att ett program ska kunna utföra arbete på vissa nivå för minskade kapaciteten. Ett system som kan skicka meddelanden, men inte tagit emot dem fortfarande kan ta emot order från kunder men kan inte bearbeta dessa order. Det här avsnittet beskrivs problem som kan uppstå och hur dessa problem begränsas. Service Bus har införs ett antal åtgärder som du måste välja i och det här avsnittet beskrivs även reglerna för användning av dessa Anmäl åtgärder.

## <a name="reliability-in-service-bus"></a>Tillförlitlighet i Service Bus
Det finns flera sätt att hantera problem med meddelandet och entiteten och det finns riktlinjer för rätt användning av dessa åtgärder. För att förstå riktlinjerna, måste du först förstå vad kan misslyckas i Service Bus. Alla dessa problem brukar vara tillfällig på grund av utformningen av Azure-system. På en hög nivå visas de olika orsakerna till inte finns på följande sätt:

* Begränsning från ett externt system som Service Bus är beroende av. Begränsning inträffar på samverkan med resurser för lagring och beräkning.
* Problemet för ett system som Service Bus beroende. En viss del av lagring kan exempelvis stöter på problem.
* Fel i Service Bus på enskild undersystemet. I så fall kan en beräkningsnod få till ett inkonsekvent tillstånd och måste startas om, vilket gör alla entiteter som den används för att belastningsutjämna till andra noder. I sin tur kan orsaka en kort period av långsam meddelandebehandling.
* Fel i Service Bus i ett Azure-datacenter. Detta är ett ”oåterkalleligt fel” som då systemet kan inte nås för många minuter eller några timmar.

> [!NOTE]
> Termen **lagring** kan omfatta både Azure-lagring och SQL Azure.
> 
> 

Service Bus innehåller ett antal åtgärder för dessa problem. I följande avsnitt beskrivs varje problem och deras respektive ändringar.

### <a name="throttling"></a>Begränsning
Med Service Bus begränsning gör det möjligt samverkande meddelandet hastighet. Varje enskild nod för Service Bus innehåller många entiteter. Var och en av dessa enheter ansluter krav på systemet vad gäller CPU, minne, lagring och andra facets. När någon av dessa aspekter identifierar användning som överskrider definierade tröskelvärden, Service Bus kan neka en viss begäran. Anroparen tar emot en [ServerBusyException] [ ServerBusyException] och försöker efter 10 sekunder.

Som en minskning koden läsa felet och stoppa alla försök av meddelandet för minst 10 sekunder. Eftersom felet kan inträffa mellan delar av kunden programmet förväntas att varje oberoende körs logik för omprövning. Koden kan minska sannolikheten för begränsas genom att aktivera partitionering på en kö eller ett ämne.

### <a name="issue-for-an-azure-dependency"></a>Problem för ett Azure beroende
Andra komponenter i Azure kan ibland ha problem med tjänsten. Till exempel när ett system som använder Service Bus uppgraderas uppleva systemet tillfälligt minskade funktioner. Undvik dessa typer av problem med Service Bus regelbundet undersöker och implementerar åtgärder. Sidoeffekter av dessa åtgärder visas. Om du vill hantera tillfälliga problem med lagring, implementerar exempelvis Service Bus ett system som kan skicka meddelandeåtgärder till arbetar konsekvent. På grund av minskning, kan ett meddelande skickat ta upp till 15 minuter att visas i den eller de berörda prenumeration och klar för en receive-åtgärd. De flesta enheter får generellt sett inte det här problemet. Dock krävs antal entiteter i Service Bus i Azure får den här säkerhetsfunktionen ibland för en liten del av Service Bus-kunder.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Service Bus-fel på en enda undersystemet
Med valfritt program kan omständigheter orsaka en intern komponenten i Service Bus blir inkonsekventa. När Service Bus identifierar detta, samlar in data från program som gör det enklare att diagnostisera vad hände. När data samlas in, startas programmet i ett försök att återgå till ett konsekvent tillstånd. Den här processen sker ganska snabbt och resulterar i en entitet som inte är tillgänglig för upp till några minuter, även om de vanliga stopptider är mycket kortare.

I dessa fall klientprogrammet genererar en [System.TimeoutException] [ System.TimeoutException] eller [MessagingException] [ MessagingException] undantag. Service Bus innehåller en lösning på problemet i form av automatiserade klienten logik. När försök perioden är slut och meddelandet levereras inte kan du utforska med andra funktioner, till exempel [länkas namnområden][paired namespaces]. Parad namnområden ha andra upplysningar som beskrivs i artikeln.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Fel i Service Bus i ett Azure-datacenter
För ett fel i ett Azure-datacenter trolig orsak är en misslyckad uppgradering distribution av Service Bus eller ett beroende system. Eftersom plattformen har mognadslagrats har sannolikheten för den här typen av fel minskat. Ett fel i datacenter kan också inträffa skäl som inkluderar följande:

* Elektriskt avbrott (strömförsörjning och genererar power försvinner).
* Anslutningen (internet bryta mellan klienter och Azure).

I båda fallen kan orsakade en fysisk eller syntetiska katastrof problemet. Att komma runt detta och kontrollera att du kan fortfarande skicka meddelanden, kan du använda [länkas namnområden] [ paired namespaces] meddelanden ska skickas till en annan plats medan den primära platsen görs felfri igen. Mer information finns i [bästa praxis för isolering program mot Service Bus-avbrott och katastrofer][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Kopplade namnområden
Den [länkas namnområden] [ paired namespaces] funktionen stöder scenarier där en Service Bus-entitet eller distribution inom ett datacenter blir otillgänglig. När den här händelsen inträffar sällan, måste distribuerade system fortfarande vara beredd att hantera värsta fall. Den här händelsen inträffar normalt eftersom vissa element som Service Bus beroende har uppstått ett kortsiktiga problem. Om du vill behålla tillgänglighet under ett avbrott, kan Service Bus användare använda två separata namnområden, helst i olika datacenter, som värd för sin meddelandeentiteter. Resten av det här avsnittet använder följande termer:

* Primär namnområde: namnområdet som ditt program samverkar för skicka och ta emot åtgärder.
* Sekundär namnområde: det namnområde som fungerar som en säkerhetskopia till primära namnområdet. Programlogik interagerar inte med det här namnområdet.
* Intervall för redundans: hur lång tid att acceptera normal fel innan programmet växlar från namnområdet primär till sekundär namnområdet.

Länkas namnområden stöd *skicka tillgänglighet*. Skicka tillgänglighet bevarar möjligheten att skicka meddelanden. Om du vill använda Skicka tillgänglighet måste ditt program uppfylla följande krav:

1. Meddelanden tas bara emot från det primära namnområdet.
2. Skicka meddelanden till en viss kö eller avsnittet kan tas emot i rätt ordning.
3. Meddelanden i en session kan tas emot i rätt ordning. Det här är ett avbrott från normala funktionerna för sessioner. Det innebär att programmet använder sessioner till logiskt gruppera meddelanden.
4. Sessionstillstånd bevaras endast på primära namnområdet.
5. Primär kön kan anslutas och börja ta emot meddelanden innan sekundär kö levererar alla meddelanden i kön primära.

I följande avsnitt beskrivs de API: er, hur de API: er implementeras och visar exempelkoden som använder funktionen. Observera att fakturering effekter som är associerade med den här funktionen.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>The MessagingFactory.PairNamespaceAsync API
Funktionen parad namnområden innehåller den [PairNamespaceAsync] [ PairNamespaceAsync] -metoden i den [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] klass:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

När uppgiften har slutförts länkning av namnområdet är också fullständig och redo att vidta åtgärder för alla [MessageReceiver][MessageReceiver], [QueueClient][QueueClient], eller [TopicClient] [ TopicClient] skapats med den [MessagingFactory] [ MessagingFactory] instans. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] är basklass för de olika typerna av länkar som är tillgängliga med en [MessagingFactory] [ MessagingFactory] objekt. För närvarande den enda härledda klassen är ett med namnet [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], som implementerar skicka tillgänglighet. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] har en uppsättning konstruktorer som bygger på varandra. Granskar konstruktorn med de flesta parametrar kan förstå du beteendet för andra konstruktorer.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Dessa parametrar har följande:

* *secondaryNamespaceManager*: en initierad [NamespaceManager] [ NamespaceManager] instanser för sekundära namnområdet som den [PairNamespaceAsync] [ PairNamespaceAsync] metod kan använda för att konfigurera sekundära namnområdet. Namespace manager för att hämta listan över köer i namnområdet och se till att de nödvändiga eftersläpning köerna finns. Om de köerna som inte finns, skapas de. [NamespaceManager] [ NamespaceManager] kräver möjlighet att skapa en token med den **hantera** anspråk.
* *messagingFactory*: den [MessagingFactory] [ MessagingFactory] instanser för sekundära namnområdet. Den [MessagingFactory] [ MessagingFactory] objektet används för att skicka och, om den [EnableSyphon] [ EnableSyphon] egenskap är inställd på **SANT**, ta emot meddelanden från eftersläpning köer.
* *backlogQueueCount*: antalet eftersläpning köer om du vill skapa. Det här värdet måste vara minst 1. När du skickar meddelanden till eftersläpningen, väljs något av dessa köer slumpmässigt. Om du anger värdet till 1 kan endast en kö skulle användas. När detta sker och en eftersläpning kön genererar fel, klienten har inte möjlighet att prova en annan eftersläpning kö och misslyckas med att skicka meddelandet. Vi rekommenderar att om det här värdet till vissa större värde och standard värdet till 10. Du kan ändra det till ett högre eller lägre värde beroende på hur mycket data som programmet skickar per dag. Varje eftersläpning kö kan innehålla upp till 5 GB meddelanden.
* *failoverInterval*: tidsperiod under vilken du kan acceptera fel på det primära namnområdet innan du byter en enda entitet till det sekundära namnområdet. Redundans inträffar för en entitet av entiteten. Entiteter i en enda namnrymd live ofta på olika noder i Service Bus. Ett fel i en entitet innebär inte ett fel i en annan. Du kan ange ett värde [System.TimeSpan.Zero] [ System.TimeSpan.Zero] ska gå över till sekundärt omedelbart efter din första, icke-tillfälligt fel. Fel som utlöser timern redundans finns några [MessagingException] [ MessagingException] där den [IsTransient] [ IsTransient] egenskapen är false, eller en [System.TimeoutException][System.TimeoutException]. Andra undantag som [UnauthorizedAccessException] [ UnauthorizedAccessException] inte orsakar växling vid fel, eftersom de anger att klienten är felaktigt konfigurerad. En [ServerBusyException] [ ServerBusyException] inte orsak redundans eftersom rätt mönstret är att vänta 10 sekunder sedan skickar meddelandet igen.
* *enableSyphon*: Anger att den här viss länkning bör också syphon meddelanden från namnområdet sekundära tillbaka till det primära namnområdet. I allmänhet program som skickar meddelanden ska du ange **FALSKT**; program som tar emot meddelanden ska du ange **SANT**. Anledningen är att ofta, det finns färre meddelandet mottagare än avsändare. Beroende på antalet mottagare, kan du ha ett enda program-instans hanterar syphon uppgifter. Många mottagare har fakturering konsekvenser för varje eftersläpning kö.

Om du vill använda koden, skapar du en primär [MessagingFactory] [ MessagingFactory] instans, en sekundär [MessagingFactory] [ MessagingFactory] instans, en sekundär [NamespaceManager] [ NamespaceManager] instans, och en [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instans. Anropet kan vara så enkelt som följande:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

När uppgiften som returneras av den [PairNamespaceAsync] [ PairNamespaceAsync] metoden har slutförts, allt har installerats och redo att användas. Innan aktiviteten returneras kan kanske du inte har slutförts alla bakgrundsjobbet krävs för att kopplingen ska fungera rätt. Du bör därför inte börja skicka meddelanden tills uppgiften returnerar. Eventuella fel, till exempel felaktiga autentiseringsuppgifter eller inte gick att skapa köer eftersläpning utlöstes dessa undantag när uppgiften har slutförts. När uppgiften returnerar, kontrollera att köerna hittades eller skapas genom att undersöka den [BacklogQueueCount] [ BacklogQueueCount] -egenskapen i din [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instans. För den föregående kod, som åtgärden visas på följande sätt:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om asynkrona meddelanden i Service Bus kan du läsa mer information om [länkas namnområden][paired namespaces].

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
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
