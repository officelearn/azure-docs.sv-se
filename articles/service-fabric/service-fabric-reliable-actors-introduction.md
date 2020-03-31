---
title: Översikt över pålitliga aktörer för serviceinfrastruktur
description: Introduktion till programmeringsmodellen Service Fabric Reliable Actors, baserad på mönstret För virtuell aktör.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6aafa2a3372c431f8afa7fad41051c26c3fe5fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645573"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introduktion till Service Fabric Pålitliga aktörer
Reliable Actors är ett service fabric-programramverk baserat på mönstret [för den virtuella aktören.](https://research.microsoft.com/en-us/projects/orleans/) Reliable Actors API ger en programmeringsmodell med en tråd som bygger på skalbarhets- och tillförlitlighetsgarantier som tillhandahålls av Service Fabric.

## <a name="what-are-actors"></a>Vad är skådespelare?
En aktör är en isolerad, oberoende enhet för beräkning och tillstånd med entrådad körning. [Aktörsmönstret](https://en.wikipedia.org/wiki/Actor_model) är en beräkningsmodell för samtidiga eller distribuerade system där ett stort antal av dessa aktörer kan köra samtidigt och oberoende av varandra. Aktörer kan kommunicera med varandra och de kan skapa fler aktörer.

### <a name="when-to-use-reliable-actors"></a>När ska reliable actors användas
Service Fabric Reliable Actors är en implementering av aktörens designmönster. Som med alla mönster för programvarudesign fattas beslutet om du vill använda ett visst mönster baserat på om ett problem med programvarudesign passar mönstret eller inte.

Även om aktörens designmönster kan passa ett antal problem och scenarier för distribuerade system, måste man noggrant överväga mönstrets begränsningar och det ramverk som implementerar det. Som allmän vägledning bör du tänka på aktörsmönstret för att modellera ditt problem eller scenario om:

* Ditt problemutrymme innebär ett stort antal (tusentals eller fler) av små, oberoende och isolerade enheter av tillstånd och logik.
* Du vill arbeta med objekt med en tråd som inte kräver betydande interaktion från externa komponenter, inklusive frågetillstånd över en uppsättning aktörer.
* Dina aktörsinstanser blockerar inte anropare med oförutsägbara fördröjningar genom att utfärda I/O-åtgärder.

## <a name="actors-in-service-fabric"></a>Aktörer i servicetyg
I Service Fabric implementeras aktörer i reliable actors-ramverket: Ett programramverk som bygger på [Service Fabric Reliable Services.](service-fabric-reliable-services-introduction.md) Varje tjänst för en tillförlitlig aktör som du skriver är faktiskt en partitionerad, tillståndskänslig tillförlitlig tjänst.

Varje aktör definieras som en förekomst av en aktörstyp, identisk med hur ett .NET-objekt är en förekomst av en .NET-typ. Det kan till exempel finnas en aktörstyp som implementerar funktionerna i en kalkylator och det kan finnas många aktörer av den typen som distribueras på olika noder över ett kluster. Varje sådan aktör identifieras unikt av ett aktörs-ID.

## <a name="actor-lifetime"></a>Skådespelare Livstid
Service Fabric-aktörer är virtuella, vilket innebär att deras livstid inte är knuten till deras representation i minnet. Som ett resultat behöver de inte uttryckligen skapas eller förstöras. Körningen Tillförlitliga aktörer aktiverar automatiskt en aktör första gången den tar emot en begäran om det aktörs-ID. Om en aktör inte används under en viss tid samlar det tillförlitliga skådespelarna skräpet in i minnesobjektet. Det kommer också att upprätthålla kunskap om skådespelarens existens om det skulle behöva återaktiveras senare. Mer information finns i [Skådespelares livscykel och skräpinsamling](service-fabric-reliable-actors-lifecycle.md).

Denna virtuella aktör livstid abstraktion bär några varningar som ett resultat av den virtuella aktören modellen, och i själva verket Reliable Actors genomförandet avviker ibland från denna modell.

* En aktör aktiveras automatiskt (vilket gör att ett aktörsobjekt skapas) första gången ett meddelande skickas till dess aktörs-ID. Efter en viss tid samlas aktörsobjektet skräp. I framtiden, med hjälp av aktören ID igen, orsakar en ny aktör objekt som ska konstrueras. En aktörs tillstånd överlever objektets livstid när det lagras i tillståndshanteraren.
* Om du anropar en aktörsmetod för ett aktörs-ID aktiveras aktören. Av denna anledning har aktörstyper sin konstruktor som anropas implicit av körningen. Klientkoden kan därför inte skicka parametrar till aktörstypens konstruktor, även om parametrar kan skickas till aktörens konstruktor av själva tjänsten. Resultatet är att aktörer kan konstrueras i ett delvis initierat tillstånd när andra metoder anropas, om aktören kräver initieringsparametrar från klienten. Det finns ingen enskild startpunkt för aktivering av en aktör från klienten.
* Även om reliable actors implicit skapar aktörsobjekt; Du har möjlighet att uttryckligen ta bort en aktör och dess tillstånd.

## <a name="distribution-and-failover"></a>Distribution och redundans
För att ge skalbarhet och tillförlitlighet distribuerar Service Fabric aktörer i hela klustret och migrerar dem automatiskt från misslyckade noder till felfria efter behov. Detta är en abstraktion över en [partitionerad, tillståndskänslig tillförlitlig tjänst](service-fabric-concepts-partitioning.md). Distribution, skalbarhet, tillförlitlighet och automatisk redundans tillhandahålls alla på grund av att aktörer körs inuti en tillståndskänslig reliable service som kallas *Aktörstjänsten*.

Aktörer distribueras över partitionerna i aktörstjänsten och dessa partitioner distribueras över noderna i ett Service Fabric-kluster. Varje tjänstpartition innehåller en uppsättning aktörer. Service Fabric hanterar distribution och redundans av tjänstpartitionerna.

En aktörstjänst med nio partitioner som distribueras till tre noder med standardpartitionsplacering för aktören distribueras till exempel:

![Distribution av pålitliga aktörer][2]

Aktörsramen hanterar partitionsschema och nyckelintervallinställningar åt dig. Detta förenklar vissa val men har också en viss hänsyn:

* Med Reliable Services kan du välja ett partitioneringsschema, nyckelintervall (när du använder ett intervallpartitioneringsschema) och partitionsantal. Reliable Actors är begränsat till intervallet partitionering systemet (den enhetliga Int64 schema) och kräver att du använder hela Int64 nyckelintervall.
* Som standard placeras aktörer slumpmässigt i partitioner vilket resulterar i enhetlig distribution.
* Eftersom aktörer placeras slumpmässigt, bör det förväntas att aktörsåtgärder alltid kräver nätverkskommunikation, inklusive serialisering och deserialisering av metodanropsdata, ådra sig svarstid och omkostnader.
* I avancerade scenarier är det möjligt att styra aktörspartitionsplacering med hjälp av Int64-aktörs-ID:er som mappar till specifika partitioner. Om du gör det kan det dock leda till en obalanserad distribution av aktörer över partitioner.

Mer information om hur aktörstjänster partitioneras finns i [partitioneringsbegrepp för aktörer](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Kommunikation av skådespelare
Aktörsinteraktioner definieras i ett gränssnitt som delas av aktören som implementerar gränssnittet och klienten som får en proxy till en aktör via samma gränssnitt. Eftersom det här gränssnittet används för att anropa aktörsmetoder asynkront måste varje metod i gränssnittet returneras.

Metodanrop och deras svar resulterar i slutändan i nätverksbegäranden i klustret, så argumenten och resultattyperna för de uppgifter som de returnerar måste vara serialiserbara av plattformen. I synnerhet måste de vara [datakontrakt serialiserbara](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Skådespelaren proxy
Reliable Actors-klient-API:et tillhandahåller kommunikation mellan en aktörsinstans och en aktörsklient. Om du vill kommunicera med en aktör skapar en klient ett proxyobjekt för aktör som implementerar aktörsgränssnittet. Klienten interagerar med aktören genom att anropa metoder på proxyobjektet. Aktörsproxyn kan användas för kommunikation mellan klient och aktör och aktör.

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


Observera att de två informationsdelar som används för att skapa aktörsproxyobjektet är aktörs-ID och programnamnet. Aktörs-ID:n identifierar aktören unikt, medan programnamnet identifierar [programmet Service Fabric](service-fabric-reliable-actors-platform.md#application-model) där aktören distribueras.

Klassen `ActorProxy`(C#) `ActorProxyBase`/ (Java) på klientsidan utför den upplösning som krävs för att hitta aktören efter ID och öppna en kommunikationskanal med den. Det försöker också att hitta aktören i fall av kommunikationsfel och redundans. Därför har meddelandeleverans följande egenskaper:

* Meddelandeleverans är bäst.
* Aktörer kan ta emot dubblettmeddelanden från samma klient.

## <a name="concurrency"></a>Samtidighet
Körningen Reliable Actors ger en enkel turbaserad åtkomstmodell för åtkomst till aktörsmetoder. Det innebär att högst en tråd kan vara aktiv inuti ett aktörsobjekts kod när som helst. Turbaserad åtkomst förenklar i hög grad samtidiga system eftersom det inte finns något behov av synkroniseringsmekanismer för dataåtkomst. Det innebär också att system måste utformas med särskilda överväganden för varje aktörs entrådiga åtkomstkaraktär.

* En instans av en aktör kan inte bearbeta mer än en begäran åt gången. En aktörsinstans kan orsaka en flaskhals genom dataflöde om den förväntas hantera samtidiga begäranden.
* Aktörer kan dödläge på varandra om det finns en cirkulär begäran mellan två aktörer medan en extern begäran görs till en av aktörerna samtidigt. Aktören runtime kommer automatiskt time out på skådespelare samtal och kasta ett undantag till den som ringer för att avbryta eventuella dödläge situationer.

![Tillförlitlig kommunikation mellan aktörer][3]

### <a name="turn-based-access"></a>Turbaserad åtkomst
En sväng består av fullständig körning av en aktörsmetod som svar på en begäran från andra aktörer eller klienter, eller en fullständig körning av en [timer/påminnelse](service-fabric-reliable-actors-timers-reminders.md) motringning. Även om dessa metoder och motringningar är asynkrona, interleave inte skådespelarna körningen dem. En sväng måste vara helt klar innan en ny sväng tillåts. Med andra ord måste en aktörsmetod eller en timer/påminnelse-motringning som körs för närvarande vara helt klar innan ett nytt anrop till en metod eller motringning tillåts. En metod eller motringning anses ha slutförts om körningen har returnerats från metoden eller motringningen och aktiviteten som returneras med metoden eller motringningen har slutförts. Det är värt att betona att tur-baserade samtidighet respekteras även över olika metoder, timers och motringningar.

Skådespelarna runtime upprätthåller tur-baserade samtidighet genom att förvärva en per-aktör lås i början av en tur och släppa låset i slutet av turn. Således är tur-baserade samtidighet verkställas per aktör och inte över aktörer. Aktörsmetoder och timer-/påminnelseåterklädnad kan köras samtidigt för olika aktörers räkning.

Följande exempel illustrerar ovanstående begrepp. Tänk dig en aktörstyp som implementerar två asynkrona metoder (t.ex. *metod 1* och *metod2*), en timer och en påminnelse. Diagrammet nedan visar ett exempel på en tidslinje för körningen av dessa metoder och motringningar för två aktörer (*ActorId1* och *ActorId2*) som tillhör den här aktörstypen.

![Pålitliga aktörer körtid tur-baserade samtidighet och tillgång][1]

Detta diagram följer dessa konventioner:

* Varje lodrät linje visar det logiska flödet för körning av en metod eller en motringning för en viss aktörs räkning.
* De händelser som markeras på varje vertikal linje inträffar i kronologisk ordning, med nyare händelser som inträffar under äldre händelser.
* Olika färger används för tidslinjer som motsvarar olika aktörer.
* Markering används för att ange hur länge låset per aktör hålls på uppdrag av en metod eller motringning.

Några viktiga punkter att tänka på:

* Medan *Metod1* körs på uppdrag av *ActorId2* som svar på klientbegäran *xyz789*, en annan klient begäran (*abc123*) anländer som också kräver *metod1* som ska utföras av *ActorId2*. Den andra körningen av *metod1* börjar dock inte förrän föregående körning har slutförts. På samma sätt utlöses en påminnelse som registrerats av *ActorId2* medan *Metod1* körs som svar på klientbegäran *xyz789*. Påminnelseuppringningen körs först när båda körningarna av *metod1* har slutförts. Allt detta beror på tur-baserade samtidighet verkställas för *ActorId2*.
* På samma sätt tillämpas även turbaserad samtidighet för *ActorId1*, vilket framgår av körningen av *metod1*, *metod2*, och timeradrering på uppdrag av *ActorId1* som sker på ett seriellt sätt.
* Körning av *metod1* på uppdrag av *ActorId1* överlappar dess körning på uppdrag av *ActorId2*. Detta beror på att tur-baserade samtidighet upprätthålls endast inom en aktör och inte över aktörer.
* I vissa av metod-/motringningskörningarna avslutas `Task`(C#) / `CompletableFuture`(Java) som returneras med metoden/motringningen när metoden returneras. I vissa andra har den asynkrona åtgärden redan avslutats när metoden/motringningsreturer returneras. I båda fallen frisläpps spärren per aktör först när både metoden/motringningsreturer och den asynkrona åtgärden har slutförts.

### <a name="reentrancy"></a>Återinträde
Skådespelarnas körning tillåter reentrancy som standard. Det innebär att om en aktörsmetod för *aktör A* anropar en metod för *aktör B*, som i sin tur anropar en annan metod på aktör *A*, tillåts den metoden att köras. Detta beror på att det är en del av samma logiska call chain-kontext. Alla timer- och påminnelsesamtal börjar med den nya logiska samtalskontexten. Mer information [finns i reliable actors-reentrancyen.](service-fabric-reliable-actors-reentrancy.md)

### <a name="scope-of-concurrency-guarantees"></a>Omfattningen av samtidighetsgarantier
Aktörernas körning ger dessa samtidighetsgarantier i situationer där den kontrollerar åkallan av dessa metoder. Det ger till exempel dessa garantier för metodanrop som görs som svar på en klientbegäran, samt för timer- och påminnelseåterkallelser. Men om aktörskoden direkt anropar dessa metoder utanför de mekanismer som tillhandahålls av Actors-körningen, kan körningen inte ge några samtidiga garantier. Om metoden till exempel anropas i kontexten för en aktivitet som inte är associerad med aktiviteten som returneras av aktörsmetoderna, kan körningen inte ge samtidiga garantier. Om metoden anropas från en tråd som aktören skapar på egen hand, kan körningen inte heller ge samtidiga garantier. För att utföra bakgrundsåtgärder bör aktörer därför använda [aktörs timers och aktörspåminnelser](service-fabric-reliable-actors-timers-reminders.md) som respekterar turbaserad samtidighet.

## <a name="next-steps"></a>Nästa steg
Kom igång genom att bygga din första tjänst för Reliable Actors:
   * [Komma igång med Reliable Actors på .NET](service-fabric-reliable-actors-get-started.md)
   * [Komma igång med Reliable Actors på Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
