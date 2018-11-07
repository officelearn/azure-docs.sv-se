---
title: Service Fabric tillförlitliga aktörer-översikt | Microsoft Docs
description: Introduktion till Service Fabric Reliable Actors-programmeringsmodell.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 363cba145ed4d5bcf138cf3f7130763891c51e8b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258069"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introduktion till Service Fabric Reliable Actors
Reliable Actors är ett ramverk för Service Fabric-program som baseras på den [virtuella aktören](https://research.microsoft.com/en-us/projects/orleans/) mönster. Reliable Actors-API är en single-threaded programmeringsmodell som bygger på skalbarhet och tillförlitlighet garantier som Service Fabric.

## <a name="what-are-actors"></a>Vad är aktörer?
En aktör är en isolerad, oberoende beräknings- och tillstånd med Enkeltrådig körning. Den [aktören mönstret](https://en.wikipedia.org/wiki/Actor_model) är en databaserad modell för samtidiga eller distribuerade system i som ett stort antal dessa aktörer kan köra samtidigt och oberoende av varandra. Aktörer kan kommunicera med varandra och de kan skapa flera aktörer.

### <a name="when-to-use-reliable-actors"></a>När du ska använda Reliable Actors
Service Fabric Reliable Actors är en implementering av designmönstret aktör. Precis som med alla designmönster för programvara passar beslutet om du vill använda ett specifikt mönster görs baserat på huruvida en programvara utforma problemet mönstret.

Även om aktören utforma mönster får vara en bra plats till ett antal scenarier, noggrann beaktandet av begränsningarna för mönstret och ramverk som implementerar den måste göras och problemen med distribuerade system. Överväg att aktören mönstret att utforma ditt problem eller scenario om som en allmän vägledning:

* Ditt problem adressutrymme innebär att ett stort antal (tusentals eller mer) små, oberoende och isolerat enheter av tillstånd och logik.
* Du vill arbeta med single-threaded-objekt som inte kräver betydande interaktion från externa komponenter, inklusive fråga tillstånd över en uppsättning aktörer.
* Dina aktörsinstanser blockerar inte anropare med oförutsägbara fördröjningar genom att utfärda i/o-åtgärder.

## <a name="actors-in-service-fabric"></a>Actors i Service Fabric
I Service Fabric actors implementeras inom ramen för Reliable Actors: en aktörbaserade med mönstret programramverk som bygger på [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Varje Reliable Actor-tjänst som du skriver är faktiskt en partitionerad, tillståndskänslig och tillförlitlig tjänst.

Varje aktör definieras som en instans av en typ av aktör, identiska med det sättet som ett .NET-objekt är en instans av en .NET-typ. Exempelvis kan det finnas en typ av aktör som implementerar funktionerna i en kalkylator och det kan finnas många aktörer av den typen som distribueras på olika noder i ett kluster. Varje sådan skådespelare identifieras unikt genom en aktör-ID.

## <a name="actor-lifetime"></a>Aktören livslängd
Service Fabric actors är virtuell, vilket innebär att deras livstid inte är kopplat till sina InMemory-representation. Därför kan behöver de inte uttryckligen skapas eller förstörs. Reliable Actors-runtime aktiveras automatiskt i en aktör först tid det tar emot en begäran för den aktör-ID. Om en aktör inte används för en viss tidsperiod, Reliable Actors-runtime skräpinsamling-samlar in InMemory-objektet. Den kommer också upprätthålla kunskap om den aktör förekomst bör den måste återaktiveras senare. Mer information finns i [aktör livscykel och skräpinsamling samling](service-fabric-reliable-actors-lifecycle.md).

Abstraktionen virtuella aktören livslängd har vissa varningar till följd av den virtuella aktörmodell och i själva verket Reliable Actors-implementering avviker ibland från den här modellen.

* En aktör aktiveras automatiskt (orsakar en aktörobjekt konstrueras) första gången ett meddelande skickas till dess aktör-ID. Efter en viss tidsperiod samlas objektet aktörer in som skräp. I framtiden, gör Använd aktörs-ID på nytt och ett nytt aktörobjekt konstrueras. En aktörstillstånd outlives objektets livstid när lagras i hanteraren för tillstånd.
* Anropar någon aktör för en aktörs-ID aktiverar den aktören. Därför har aktören typer sina konstruktor anropas implicit av körningen. Därför kan inte klientkoden skicka parametrar till aktörstypen konstruktor, även om parametrar kan överföras till den aktörs-konstruktorn av själva tjänsten. Resultatet är att aktörer kan konstrueras i ett delvis initierats tillstånd med andra metoder kallas, tiden om aktören kräver initieringsparametrar från klienten. Det finns ingen enskild startpunkt för aktivering av en aktör från klienten.
* Även om Reliable Actors skapa implicit aktören objekt. du har möjlighet att ta bort en aktör och dess tillstånd.

## <a name="distribution-and-failover"></a>Distribution och redundans
För att tillhandahålla skalbarhet och tillförlitlighet, Service Fabric distribuerar aktörer i hela klustret och migrerar dem automatiskt från misslyckade noderna felfri dem efter behov. Det här är en abstraktion över en [partitionerade, tillståndskänslig tillförlitlig tjänst](service-fabric-concepts-partitioning.md). Distribution, skalbarhet, tillförlitlighet och automatisk redundans finns alla angivna tack vare det faktum att aktörer körs i en tillståndskänslig tillförlitlig tjänst kallas den *Aktörstjänsten*.

Aktörer är fördelade mellan partitionerna för Actor-tjänst och dessa partitioner distribueras mellan noderna i ett Service Fabric-kluster. Varje partition service innehåller en uppsättning aktörer. Service Fabric hanterar distribution och redundans för tjänstpartitionerna.

Till exempel skulle en actor-tjänst med nio partitioner som distribueras till tre noder med standardplacering för partition av aktör distribueras thusly:

![Reliable Actors-distribution][2]

Ramverket hanterar partition schema och nyckeln intervallet-inställningarna åt dig. Detta förenklar vissa alternativ men även har viss:

* Reliable Services kan du välja ett partitioneringsschema, nyckelintervall (om du använder ett intervall som partitioneringsschema) och partitionera antal. Reliable Actors är begränsad till intervallet partitioneringsschema (uniform Int64 schemat) och kräver att du använder det fullständiga viktiga Int64-adressintervallet.
* Som standard placeras slumpmässigt aktörer i partitioner, vilket resulterar i jämn fördelning.
* Eftersom aktörer placeras slumpmässigt, bör det förväntas att aktören operations alltid kommer att kräva nätverkskommunikation, inklusive serialisering och deserialisering av metoden samtalsdata medför svarstid och kostnader.
* I avancerade scenarier är det möjligt att kontroll aktören partition placering med hjälp av Int64 aktören ID: N som mappar till specifika partitioner. Men kan detta så resultera i ett Obalanserat fördelning av aktörer över partitioner.

Mer information om hur aktörstjänster partitioneras avser [partitionering begrepp för aktörer](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Aktören kommunikation
Aktören interaktioner definieras i ett gränssnitt som delas av aktören som implementerar gränssnittet och klienten som hämtar en proxy till en aktör via samma gränssnitt. Eftersom det här gränssnittet används för att anropa metoder för aktören asynkront, vara varje metod i gränssnittet aktiviteten returnerar.

Metodanropen och deras svar slutligen nätverksbegäranden i klustret, så argumenten och kan medföra resultattyper uppgifter som returnerar måste kunna serialiseras av plattformen. I synnerhet de måste vara [data kontrakt serialiserbara](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Aktören-proxyn
Reliable Actors-klientens API ger kommunikation mellan en aktör-instans och en aktör-klient. För att kommunicera med en aktör, skapar en klient en aktör proxy-objekt som implementerar aktörsgränssnittet. Klienten samverkar med aktören genom att anropa metoder på proxyobjekt. Aktören-proxyn kan användas för klienten att aktören och aktören till aktören kommunikation.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Observera att de två typer av information som används för att skapa proxy aktörobjekt aktörs-ID och namnet på programmet. Aktörs-ID identifierar aktören, medan programnamnet identifierar den [Service Fabric-program](service-fabric-reliable-actors-platform.md#application-model) där aktören distribueras.

Den `ActorProxy`(C#) / `ActorProxyBase`(Java) klass på klientsidan utför de nödvändiga lösningen att leta reda på aktören efter ID och öppna en kommunikationskanal med den. Den försöker också leta reda på aktören i fall av kommunikationsfel och redundans. Därför har meddelandeleverans följande egenskaper:

* Meddelandeleverans är bästa prestanda.
* Aktörer får dubbletter av meddelanden från samma klient.

## <a name="concurrency"></a>Samtidighet
Reliable Actors-runtime får en enkel tur-baserade modell för att komma åt aktören metoder. Det innebär att mer än en tråd kan vara aktiva i en aktörobjekt kod när som helst. Tur-baserade åtkomst förenklar avsevärt samtidiga system som det ingen behövs synkroniseringsmekanismer för dataåtkomst. Det innebär också system måste utformas med tänka på för typen single-threaded åtkomst för varje skådespelare-instans.

* En enda aktör-instans kan inte bearbeta fler än en begäran i taget. En aktör-instans kan orsaka en flaskhals för dataflöde om det förväntas att hantera samtidiga begäranden.
* Aktörer kan låsas i varandra om det finns en cirkulär begäran mellan två aktörer medan en extern begäran görs till en av aktörer samtidigt. Actor runtime tar för lång tid på aktörsanrop automatiskt och utlöser ett undantag till anroparen att avbryta möjligt deadlock situationer.

![Reliable Actors-kommunikation][3]

### <a name="turn-based-access"></a>Aktivera åtkomst
Ett varv består av fullständiga körningen av en aktörsmetod som svar på en begäran från andra aktörer eller klienter eller fullständig körningen av en [timer/påminnelse](service-fabric-reliable-actors-timers-reminders.md) återanrop. Även om dessa metoder och återanrop är asynkron, interfoliering dem inte av aktörer runtime. Ett varv måste vara färdigt innan en ny aktivera tillåts. Med andra ord en aktör metoden eller timer/påminnelse återanrop som körs för tillfället måste vara färdigt innan ett nytt anrop till en metod eller återanrop tillåts. En metod eller en motringning anses är klar med om körningen har returneras från metoden eller motringning och uppgiften som returneras av metoden eller återanrop har slutförts. Det är värt betonar som tur-baserade samtidighet respekteras även över olika metoder och timers återanrop.

Aktörer runtime framtvingar tur-baserade samtidighet genom att skaffa ett lås per aktör i början av ett varv och bort låset i slutet av länken. Därför tillämpas bygger samtidighet på basis av per aktör och inte mellan aktörer. Aktören metoder och timer/påminnelse återanrop kan köra samtidigt åt olika aktörer.

I följande exempel illustrerar begrepp som ovan. Överväg en aktörstyp som implementerar två asynkrona metoder (exempelvis *Method1* och *Method2*), en timer och en påminnelse. Diagrammet nedan visar ett exempel på en tidslinje för körning av dessa metoder och återanrop för två aktörer (*ActorId1* och *ActorId2*) som tillhör denna typ av aktör.

![Reliable Actors runtime bygger samtidighet och åtkomst][1]

Det här diagrammet följer dessa regler:

* Varje lodrät linje visar det logiska flödet för körning av en metod eller ett återanrop för en viss aktören.
* De händelser som markerats på varje lodrät linje uppstå i kronologisk ordning med nyare händelser som inträffar under äldsta.
* Olika färger används för tidslinjer för olika aktörer.
* Markering används för att ange den varaktighet som lås per aktör lagras för en metod eller en motringning.

Vissa viktiga saker att tänka på:

* Medan *Method1* körs för *ActorId2* som svar på klientbegäran *xyz789*, en annan klientbegäran (*abc123*) tas emot som kräver också *Method1* som ska utföras av *ActorId2*. Men den andra körningen av *Method1* börjar inte förrän den tidigare körningen har slutförts. På samma sätt kan en påminnelse registreras av *ActorId2* utlöses när *Method1* körs som svar på klientbegäran *xyz789*. Påminnelse motringningen körs förrän både körningar av *Method1* har slutförts. Allt detta är på grund av tur-baserade samtidighet som tillämpas på *ActorId2*.
* På samma sätt tillämpas också tur-baserade samtidighet för *ActorId1*, som visas av körningen av *Method1*, *Method2*, och timer-motringning åt  *ActorId1* händer i en seriell sätt.
* Körningen av *Method1* för *ActorId1* överlappar med den kan körningen på uppdrag av *ActorId2*. Det beror på att tur-baserade samtidighet tillämpas endast i en aktör och inte över aktörer.
* En del av metoden/Motringnings-körningar den `Task`(C#) / `CompletableFuture`(Java) som returneras av metoden/återanrop slutförs när metoden returnerar. I vissa andra har redan den asynkrona åtgärden slutförts när metoden/motringningen returnerar. I båda fallen släpps lås per aktör endast när både returnerar metoden/motringningen och den asynkrona åtgärden har slutförts.

### <a name="reentrancy"></a>Återinträde
Aktörer runtime kan återinträde som standard. Det innebär att om en aktörsmetod för *aktör A* anropar en metod på *aktören B*, vilket i sin tur anropar en annan metod på *aktör A*, att metoden tillåts att köra. Det beror på att det är en del av samma logiska anropskedja kontext. Alla anrop för timer och påminnelse börjar med den nya logiska anrop kontexten. Se den [återinträde av Reliable Actors](service-fabric-reliable-actors-reentrancy.md) för mer information.

### <a name="scope-of-concurrency-guarantees"></a>Omfånget för samtidighet garantier
Aktörer runtime tillhandahåller dessa samtidighet garantier i situationer där som styrs av dessa metoder. Till exempel tillhandahåller dessa garantier för metodanropen som utförs som svar på en klientbegäran, samt för timer och påminnelse återanrop. Dock om aktören koden direkt anropar metoderna utanför mekanismer som tillhandahålls av aktörer runtime, kan inte sedan körningen ange några garantier för samtidighet. Till exempel om metoden anropas i kontexten för en uppgift som inte är associerad med aktivitet som returneras av metoderna som aktören, tillhandahålla sedan körningen inte samtidighet garantier. Om metoden anropas från en tråd som aktören skapas på egen hand, kan inte sedan körningen också ge garantier för samtidighet. Om du vill utföra bakgrundsåtgärder aktörer bör därför använda [aktören timers och påminnelser för aktören](service-fabric-reliable-actors-timers-reminders.md) som respektera tur-baserade samtidighet.

## <a name="next-steps"></a>Nästa steg
Kom igång genom att skapa din första Reliable Actors-tjänst:
   * [Komma igång med Reliable Actors i .NET](service-fabric-reliable-actors-get-started.md)
   * [Komma igång med Reliable Actors i Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
