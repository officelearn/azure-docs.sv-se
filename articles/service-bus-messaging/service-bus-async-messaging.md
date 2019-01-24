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
ms.openlocfilehash: 0ecc277e1b9bd94558c54b1c808fdc24f47c402e
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845086"
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

I dessa fall kan klientprogrammet genererar en [System.TimeoutException] [ System.TimeoutException] eller [MessagingException] [ MessagingException] undantag. Service Bus innehåller en lösning för det här problemet i form av logik för omprövning av automatiserade klienten. När försök under perioden är slut och meddelandet skickas inte, du kan utforska med andra funktioner som [ihop namnområden][paired namespaces]. Kopplade namnområden har andra upplysningar som beskrivs i den här artikeln.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Fel i Service Bus i ett Azure-datacenter
En trolig orsak för ett fel i ett Azure-datacenter är en misslyckad uppgradering distribution av Service Bus eller ett beroende system. Eftersom plattformen har mognadslagrats har sannolikheten för den här typen av fel minskat. Ett datacenterfel kan också inträffa av skäl som inkluderar följande:

* Elektriska avbrott (strömförsörjning och generera power försvinner).
* Anslutningen (internet radbrytning mellan klienter och Azure).

I båda fallen kan en fysisk eller syntetiska katastrof som orsakade problemet. Du kan undvika detta och se till att du kan fortfarande skicka meddelanden, du kan använda [ihop namnområden] [ paired namespaces] meddelanden ska skickas till en annan plats medan den primära platsen görs felfri igen. Mer information finns i [bästa praxis för isolering av program mot Service Bus-avbrott och katastrofer][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Kopplade namnområden
Den [ihop namnområden] [ paired namespaces] funktionen stöder scenarier där en Service Bus-entiteten eller distribution inom ett datacenter blir otillgänglig. Medan den här händelsen inträffar sällan, måste distribuerade system fortfarande förberedas för att hantera sämsta användningsfall. Den här händelsen inträffar normalt eftersom vissa element som Service Bus är beroende av upplever ett kortsiktiga problem. Om du vill upprätthålla tillgänglighet under ett avbrott, kan Service Bus-användare använda två separata namnområden noggrannhet på olika datacenter, som värd för sina enheter för meddelanden. Resten av det här avsnittet använder följande termer:

* Primärt namnområde: Det namnområde som samverkar för skicka och ta emot för dina program.
* Sekundärt namnområde: Det namnområde som fungerar som en reserv till det primära namnområdet. Programlogiken interagerar inte med det här namnområdet.
* Intervall för redundans: Hur lång tid att godkänna normala fel innan programmet växlar från det primära namnområdet till det sekundära namnområdet.

Länkad namnområden support *skicka tillgänglighet*. Skicka tillgänglighet bevarar möjligheten att skicka meddelanden. För att använda Skicka tillgänglighet, måste programmet uppfylla följande krav:

1. Meddelanden tas bara emot från det primära namnområdet.
2. Skicka meddelanden till en given kö eller ämne tas emot i fel ordning.
3. Meddelanden i en session tas emot i fel ordning. Det här är en paus från normal drift av sessioner. Det innebär att ditt program använder logiskt gruppmeddelanden-sessioner.
4. Sessionstillstånd underhålls bara på det primära namnområdet.
5. Den primära kön kan anslutas och börja ta emot meddelanden innan den sekundära kön skickar alla meddelanden till den primära kön.

I följande avsnitt beskrivs de API: er, hur API: er implementeras och visar exempelkod som använder funktionen. Observera att faktureringen effekter som är associerade med den här funktionen.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync API
Kopplade namnområden funktionen inkluderar den [PairNamespaceAsync] [ PairNamespaceAsync] metoden på den [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] klass:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

När uppgiften slutförs, namnområde kopplingen är också klar och redo att agera på för alla [MessageReceiver][MessageReceiver], [QueueClient] [ QueueClient] , eller [TopicClient] [ TopicClient] skapats med den [MessagingFactory] [ MessagingFactory] instans. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] är basklass för de olika typerna av parkoppling som är tillgängliga med en [MessagingFactory] [ MessagingFactory] objekt. För närvarande den enda härledda klassen är en med namnet [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], som implementerar skicka tillgänglighetskrav. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] har en uppsättning konstruktorer som bygger på varandra. Du kan titta på konstruktorn med de flesta parametrar, för att förstå beteendet för andra konstruktorer.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Dessa parametrar har följande:

* *secondaryNamespaceManager*: Ett initierat [NamespaceManager] [ NamespaceManager] instansen för det sekundära namnområdet som den [PairNamespaceAsync] [ PairNamespaceAsync] metod kan använda för att ange Konfigurera det sekundära namnområdet. Namespace manager används för att erhålla en lista över köer i namnområdet och för att kontrollera att nödvändiga eftersläpning köer finns. Om dessa köer inte finns, skapas de. [NamespaceManager] [ NamespaceManager] kräver möjligheten att skapa en token med den **hantera** anspråk.
* *messagingFactory*: Den [MessagingFactory] [ MessagingFactory] instansen för det sekundära namnområdet. Den [MessagingFactory] [ MessagingFactory] objektet används för att skicka och, om den [EnableSyphon] [ EnableSyphon] är inställd på **SANT**, ta emot meddelanden från köer med kvarvarande uppgifter.
* *backlogQueueCount*: Antalet köer för att skapa. Det här värdet måste vara minst 1. När du skickar meddelanden till eftersläpningen, väljs en av dessa köer slumpmässigt. Om du ställer in värdet till 1 kan endast en kö skulle användas. När detta sker och en för eftersläpning i kön genererar fel, klienten har inte möjlighet att prova en annan för eftersläpning i kö och kan misslyckas med att skicka meddelandet. Vi rekommenderar att ange värdet till vissa större värde och standard värdet till 10. Du kan ändra detta till ett högre eller lägre värde beroende på hur mycket data ditt program skickar per dag. Varje eftersläpning kö kan innehålla upp till 5 GB av meddelanden.
* *failoverInterval*: Hur lång tid under vilken du kan acceptera fel på det primära namnområdet innan du växlar en enskild person till det sekundära namnområdet. Redundansväxlingar sker regelbundet en entitet av entiteten. Entiteter i en enda namnrymd som ofta live på olika noder i Service Bus. Ett fel i en entitet innebär inte ett fel i en annan. Du kan ange ett värde [System.TimeSpan.Zero] [ System.TimeSpan.Zero] att redundansväxla till sekundärt omedelbart efter din första och icke tillfälliga fel. Fel som utlöser redundans timern finns några [MessagingException] [ MessagingException] där den [IsTransient] [ IsTransient] egenskapen är false eller till en [ System.TimeoutException][System.TimeoutException]. Andra undantag, till exempel [UnauthorizedAccessException] [ UnauthorizedAccessException] inte orsakar redundans så att de anger att klienten är felaktigt konfigurerad. En [ServerBusyException] [ ServerBusyException] har inte orsak redundans eftersom rätt mönster är att vänta 10 sekunder sedan skicka meddelandet igen.
* *enableSyphon*: Anger att den här specifika parkoppling bör också syphon meddelanden från det sekundära namnområdet tillbaka till det primära namnområdet. I allmänhet program som skickar meddelanden ska ange ett värde och **FALSKT**; program som tar emot meddelanden ska ange ett värde och **SANT**. Anledningen är att ofta, det finns färre meddelande mottagare än avsändare. Beroende på antalet mottagare, kan du ha en enda programinstans som hanterar syphon uppgifter. Med hjälp av många mottagare har fakturering konsekvenser för varje för eftersläpning i kö.

För att använda koden, skapar du en primär [MessagingFactory] [ MessagingFactory] instans, en sekundär [MessagingFactory] [ MessagingFactory] instans, en sekundär [ NamespaceManager] [ NamespaceManager] instansen och en [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instans. Anropet kan vara så enkla som följande:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

När uppgiften returneras av den [PairNamespaceAsync] [ PairNamespaceAsync] metoden har slutförts, allt har installerats och klart att användas. Innan aktiviteten returneras, kan du inte har slutfört allt arbete som bakgrund krävs för att kopplingen ska fungera direkt. Du bör därför inte börja skicka meddelanden tills uppgiften returnerar. Eventuella fel, till exempel felaktiga autentiseringsuppgifter eller inte gick att skapa köer för eftersläpning i utlöses dessa undantag när uppgiften har slutförts. När aktiviteten returnerar, kontrollera att köer har hittades eller skapats genom att undersöka den [BacklogQueueCount] [ BacklogQueueCount] egenskapen på din [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] instans. För den föregående kod, som åtgärden visas på följande sätt:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna för asynkrona meddelanden i Service Bus, Läs mer om [ihop namnområden][paired namespaces].

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
