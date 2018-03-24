---
title: Service Fabric Reliable Actors översikt | Microsoft Docs
description: Introduktion till Service Fabric Reliable Actors programmeringsmodell.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6a13ced8b1c49239d1ad5fb96775f43de9c3943e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Introduktion till Service Fabric Reliable Actors
Reliable Actors är ett ramverk för Service Fabric-program som baseras på den [virtuella aktören](http://research.microsoft.com/en-us/projects/orleans/) mönster. API: et för tillförlitlig aktörer är en enkeltrådig programmeringsmodell som bygger på skalbarhet och tillförlitlighet garantier som Service Fabric.

## <a name="what-are-actors"></a>Vad är aktörer?
En aktör är en isolerad, oberoende beräknings- och tillstånd med Enkeltrådig körning. Den [aktören mönster](https://en.wikipedia.org/wiki/Actor_model) är en beräkningar modell för samtidiga eller distribuerade system som ett stort antal dessa aktörer kan köras samtidigt och oberoende av varandra. Aktörer kan kommunicera med varandra och de kan skapa flera aktörer.

### <a name="when-to-use-reliable-actors"></a>När du ska använda Reliable Actors
Service Fabric Reliable Actors är en implementering av aktören designmönstret. Precis som med alla programvara designmönstret passar beslut om du vill använda ett specifikt mönster görs baserat på om huruvida en programvara utforma problemet mönstret.

Även om aktören utforma mönstret får vara en bra plats till ett antal distribuerade system problem och scenarier, noggrann beaktandet av begränsningar av mönstret och framework implementeringen måste göras. Överväg att aktören mönstret modellera ditt problem eller scenario om som en allmän vägledning:

* Sidan problem innebär att ett stort antal (tusentalsavgränsare eller mer) liten, oberoende och isolerade enheter av tillstånd och logik.
* Du vill arbeta med Enkeltrådig objekt som inte kräver betydande interaktion från externa komponenter, inklusive frågor tillstånd på en uppsättning aktörer.
* Aktören-instanser blockera inte anropare med oväntade fördröjningar genom att utfärda o-åtgärder.

## <a name="actors-in-service-fabric"></a>Aktörer i Service Fabric
I Service Fabric aktörer implementeras i Reliable Actors framework: ett programramverk för aktören-mönster-baserade byggt ovanpå [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Varje tillförlitliga aktören-tjänst som du skriver är faktiskt en partitionerad tillståndskänslig tillförlitlig tjänst.

Varje aktören definieras som en instans av aktörstyp, samma sätt som ett .NET-objekt är en instans av en .NET-typ. Till exempel kan det finnas en aktörstyp som implementerar funktionerna i en kalkylator och det kan finnas flera aktörer av den typen som distribueras på olika noder i ett kluster. Varje sådan aktören identifieras unikt genom ett aktören-ID.

## <a name="actor-lifetime"></a>Aktören livslängd
Service Fabric aktörer är virtuell, vilket innebär att deras livstid inte är kopplat till deras InMemory-representation. Därför kan behöver de inte uttryckligen skapas eller förstörs. Körningsmiljön Reliable Actors aktiveras automatiskt en aktören första gången den tar emot en begäran om att aktören-ID. Om en aktör inte används för en viss tidsperiod, körningsmiljön Reliable Actors skräp-samlar in InMemory-objektet. Den kommer också upprätthålla kunskap om den aktören existens behöver den aktiveras igen senare. Mer information finns i [aktören livscykel och skräp samling](service-fabric-reliable-actors-lifecycle.md).

Denna virtuella aktören livstid framställning har vissa varningar på grund av virtuella aktören modellen och faktum Reliable Actors implementeringen avviker ibland från den här modellen.

* En aktör aktiveras automatiskt (och objektets aktören konstrueras) första gången ett meddelande skickas till dess aktören-ID. Objektet aktören samlas in som skräp efter en viss tidsperiod. I framtiden, gör använda aktörs-ID igen, ett nytt objekt aktören konstrueras. En aktörstillstånd användbara under begränsad livslängd för objektets när lagras i tillståndshanterarens.
* Anropar någon aktören för aktörs-ID aktiverar att aktören. Därför har aktören typer sin konstruktor anropas implicit av körningsmiljön. Därför kan inte klientkoden skicka parametrar till konstruktorn för typen aktören, även om parametrar kan överföras till den aktören konstruktorn av själva tjänsten. Resultatet är att aktörer kan konstrueras i tillståndet delvis initieras av den tid som andra metoder anropas på det, om aktören kräver initieringsparametrar från klienten. Det finns ingen enskild startpunkt för aktivering av en aktör från klienten.
* Även om Reliable Actors skapa implicit aktören objekt. du har möjlighet att ta bort en aktör och dess tillstånd.

## <a name="distribution-and-failover"></a>Distribution och växling vid fel
Ger bättre skalbarhet och tillförlitlighet, Service Fabric distribuerar aktörer i hela klustret och migrerar dem automatiskt från felande noder felfri dem efter behov. Detta är en abstraktion via en [partitionerade och tillståndskänsliga tillförlitlig tjänst](service-fabric-concepts-partitioning.md). Distribution, skalbarhet, tillförlitlighet och automatisk redundans finns alla angivna tack vare att aktörer körs inom en tillståndskänslig tillförlitlig tjänst kallas den *aktören tjänsten*.

Aktörer distribueras över partitioner för tjänsten aktören och dessa partitioner är fördelade på noder i ett Service Fabric-kluster. Varje tjänst partition innehåller en uppsättning aktörer. Service Fabric hanterar distribution och växling vid fel i tjänsten partitioner.

En aktören tjänst med nio partitioner som distribueras till tre noder med standardplacering aktören partition skulle till exempel fördelas thusly:

![Tillförlitliga aktörer distribution][2]

Aktören Framework hanterar schema och nyckeln intervallet partitionsinställningar för dig. Detta förenklar vissa alternativ men har viss planering:

* Reliable Services kan du välja en partitioneringsschema, viktiga intervallet (när du använder ett intervall som partitioneringsschema) och partitionera antal. Reliable Actors är begränsad till intervallet partitioneringsschema (uniform Int64 schemat) och kräver att du använder Int64 viktiga allt.
* Som standard placeras slumpmässigt aktörer partitioner, vilket resulterar i jämn fördelning.
* Eftersom aktörer placeras slumpmässigt, bör det förväntas att aktören operations alltid kommer att kräva nätverkskommunikation, inklusive serialisering och deserialisering av metoden anropsdata, medför latens och kostnader.
* I avancerade scenarier är det möjligt att kontrollen aktören partition placering med hjälp av Int64 aktören ID: N som mappar till specifika partitioner. Dock kan göra så resultera i ett Obalanserat fördelning av aktörer över partitioner.

Mer information om hur aktörstjänster partitioneras avser [partitionering begrepp för aktörer](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Aktören kommunikation
Aktören interaktioner definieras i ett gränssnitt som delas av aktören som implementerar gränssnittet och klienten som hämtar en proxy till en aktör via samma gränssnitt. Eftersom det här gränssnittet för att anropa aktören metoder asynkront, måste varje metod i gränssnittet vara åtgärdsreturnerande.

Anrop av metoden och deras svar slutligen leder nätverksbegäranden i klustret, så argumenten och resultattyper aktiviteter som de returnera måste kunna serialiseras av plattformen. I synnerhet de måste vara [data minimera serialiserbara](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Aktören-proxy
Reliable Actors klienten API ger kommunikation mellan en aktören-instans och en aktören-klient. För att kommunicera med en aktör skapar en klient ett aktören proxy-objekt som implementerar gränssnittet aktören. Klienten kommunicerar med aktören genom att anropa metoder på proxyobjekt. Aktören proxy kan användas för kommunikation i klient – aktören och aktören – aktören.

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


Observera att två typer av information som används för att skapa objektet aktören proxy aktörs-ID och namnet på programmet. Aktörs-ID identifierar aktören, medan programnamnet identifierar den [Service Fabric application](service-fabric-reliable-actors-platform.md#application-model) där aktören har distribuerats.

Den `ActorProxy`(C#) / `ActorProxyBase`(Java) klassen på klientsidan utför nödvändiga lösning att hitta aktören-ID: t och öppna en kommunikationskanal till den. Även ett nytt försök för att hitta aktören i fall av kommunikationsfel och växling vid fel. Därför har meddelandeleverans följande egenskaper:

* Meddelandeleverans är bästa prestanda.
* Aktörer får dubbla meddelanden från samma klient.

## <a name="concurrency"></a>Samtidighet
Körningsmiljön Reliable Actors ger en enkel bygger åtkomst-modell för att komma åt aktören metoder. Det innebär att mer än en tråd kan vara aktiva i objektets aktören kod när som helst. Stäng-baserad åtkomst förenklar samtidiga system som det ingen behövs synkroniseringsmekanismer för dataåtkomst. Det innebär också måste vara konstruerade med speciella överväganden vid art Enkeltrådig åtkomst för varje aktören-instans.

* En enda aktören-instans kan inte bearbeta fler än en begäran i taget. En instans av aktören kan orsaka en flaskhals för genomströmning om det förväntas att hantera samtidiga begäranden.
* Aktörer kan låsas i varandra om det finns en cirkulär begäran mellan två aktörer medan en extern begäran görs till en av berörda samtidigt. Aktören körningen tar för lång tid på aktörsanrop automatiskt och utlösa ett undantag till anroparen avbryta möjligt deadlock situationer.

![Tillförlitlig aktörer kommunikation][3]

### <a name="turn-based-access"></a>Stäng-baserad åtkomst
En tur består av fullständig körningen av en aktören metod som svar på en begäran från andra aktörer eller klienter eller fullständig körningen av en [timer/påminnelse](service-fabric-reliable-actors-timers-reminders.md) återanrop. Även om dessa metoder och återanrop asynkrona, interleave dem inte av körningsmiljön aktörer. En Stäng måste vara helt färdiga innan en ny Stäng tillåts. Med andra ord ett aktören metod eller timer/påminnelse motanrop som körs för tillfället måste vara helt färdiga innan ett nytt anrop till en metod eller återanrop tillåts. En metod eller ett återanrop anses är klar om körningen har returneras från metoden eller motringning och uppgiften som returneras av metoden eller återanrop har slutförts. Det är värt som betonar som bygger samtidighet följs även över olika metoder och timers återanrop.

Aktörer runtime tillämpar bygger samtidighet genom att skaffa ett lås per aktör i början av en tur och bort låset i slutet av aktivera. Därför tillämpas bygger samtidighet på grundval av per aktör och inte över aktörer. Aktören metoder och timer/påminnelse återanrop kan köra samtidigt för olika aktörer.

Följande exempel visar begrepp som ovan. Överväg ett aktörstyp som implementerar två asynkrona metoder (exempelvis *metod1* och *metod2*), en timer och en påminnelse. Diagrammet nedan visar ett exempel på en tidslinje för körning av dessa metoder och återanrop för två aktörer (*ActorId1* och *ActorId2*) som tillhör den här typen av aktören.

![Tillförlitliga aktörer runtime bygger samtidighet och åtkomst][1]

Det här diagrammet följer dessa regler:

* Varje lodrät linje visar logiska flödet av körning av en metod eller ett återanrop för en viss aktören.
* De händelser som markerats på varje lodrät linje uppstå i kronologisk ordning med nyare händelser som inträffar under äldre filer.
* Olika färger används för tidslinjer motsvarar olika aktörer.
* Markera används för att ange varaktighet som hålls lås per aktör åt en metod eller ett återanrop.

Några viktiga saker att tänka på:

* Medan *metod1* körs på uppdrag av *ActorId2* som svar på klientbegäran *xyz789*, en annan klientbegäran (*abc123*) anländer som kräver också *metod1* köras av *ActorId2*. Dock andra körningen av *metod1* börjar inte förrän den tidigare körningen har slutförts. På samma sätt kan en påminnelse registreras av *ActorId2* utlöses när *metod1* som körs som svar på klientbegäran *xyz789*. Påminnelse återanropet körs förrän både körningar av *metod1* har slutförts. Allt detta beror på bygger samtidighet som tillämpas för *ActorId2*.
* På samma sätt tillämpas även bygger samtidighet för *ActorId1*, enligt genom körning av *metod1*, *metod2*, och timer återanropet för *ActorId1* som händer i ett seriella sätt.
* Körningen av *metod1* för *ActorId1* överlappar körningen för *ActorId2*. Det beror på att bygger samtidighet tillämpas endast inom en aktör och inte över aktörer.
* I vissa metoden/återanrop-körningar av `Task`(C#) / `CompletableFuture`(Java) som returneras av metoden/återanrop slutförs när metoden returnerar. Den asynkrona åtgärden har redan slutförts när metoden/återanropet returnerar i vissa andra. I båda fallen släpps lås per aktör endast när både returnerar metoden/återanropet och den asynkrona åtgärden har slutförts.

### <a name="reentrancy"></a>Återinträde
Aktörer körning kan återinträde som standard. Det innebär att om en aktörsmetod av *aktören A* anropar en metod i *aktören B*, som i sin tur anropar en annan metod på *aktören A*, att metoden får köras. Det beror på att den är del av samma logiska anrop-kedjan kontext. Alla timer- och påminnelse anrop börja med den nya logiska anrop-kontexten. Finns det [Reliable Actors återinträde](service-fabric-reliable-actors-reentrancy.md) för mer information.

### <a name="scope-of-concurrency-guarantees"></a>Omfattning samtidighet garantier
Aktörer runtime tillhandahåller dessa samtidighet garantier i situationer där den styr anrop av metoderna. Till exempel tillhandahåller dessa garantier för metoden-anrop som görs som svar på en klientbegäran samt timer och påminnelser. Men om aktören koden anropar direkt metoderna utanför de metoder som tillhandahålls av körtidsbiblioteket aktörer, kan inte körningen ange samtidighet garantier. Till exempel om metoden anropas i kontexten för en uppgift som inte är associerad med aktiviteten som returneras av aktören metoder, sedan körningen kan inte tillhandahålla samtidighet garantier. Om metoden anropas från en tråd som aktören skapar själv, kan sedan körningen dessutom samtidighet garantier. Om du vill utföra bakgrundsåtgärder aktörer bör därför använda [aktören timers och aktören påminnelser](service-fabric-reliable-actors-timers-reminders.md) som respekterar bygger samtidighet.

## <a name="next-steps"></a>Nästa steg
Kom igång genom att skapa din första Reliable Actors-tjänst:
   * [Komma igång med Reliable Actors på .NET](service-fabric-reliable-actors-get-started.md)
   * [Komma igång med Reliable Actors Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
