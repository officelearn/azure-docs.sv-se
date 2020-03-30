---
title: Pålitliga aktörer på servicetyg
description: Beskriver hur Reliable Actors är skiktad på Reliable Services och använder funktionerna i Service Fabric-plattformen.
author: vturecek
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 92c717fa2c82dd147acd3c28333e37ccf8dd2e89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282307"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hur reliable actors använder plattformen Service Fabric
I den här artikeln beskrivs hur reliable actors fungerar på Azure Service Fabric-plattformen. Reliable Actors körs i ett ramverk som är värd i ett genomförande av en tillståndskänslig tillförlitlig tjänst som kallas *aktörstjänsten*. Aktörstjänsten innehåller alla komponenter som behövs för att hantera livscykeln och meddelandesändningen för dina aktörer:

* Aktörskörningen hanterar livscykel, skräpinsamling och tillämpar åtkomst med enkeltråd.
* En aktörstjänst som nedgraderar lyssnaren accepterar fjärråtkomstanrop till aktörer och skickar dem till en avsändare för att dirigera till lämplig aktörsinstans.
* Aktörstillståndsprovidern avslutar tillståndsleverantörer (till exempel tillståndsprovidern för tillförlitliga samlingar) och tillhandahåller ett kort för hantering av aktörstillstånd.

Dessa komponenter bildar tillsammans ramen för tillförlitlig aktör.

## <a name="service-layering"></a>Lager av service
Eftersom aktörstjänsten i sig är en tillförlitlig tjänst gäller alla [programmodell-](service-fabric-application-model.md)och livscykel-, [paketerings-,](service-fabric-package-apps.md) [uppgraderings-](service-fabric-deploy-remove-applications.md)och skalningsbegrepp för reliable services på samma sätt för aktörstjänster.

![Lager av aktörstjänst][1]

I föregående diagram visas förhållandet mellan programramverken för Service Fabric och användarkod. Blå element representerar reliable services-programramverket, orange representerar ramen för tillförlitlig aktör och grönt representerar användarkod.

I Reliable Services ärver `StatefulService` din tjänst klassen. Den här klassen härleds från `StatefulServiceBase` (eller `StatelessService` för tillståndslösa tjänster). I Reliable Actors använder du aktörstjänsten. Aktörstjänsten är en annan `StatefulServiceBase` implementering av klassen som implementerar aktörsmönstret där dina skådespelare körs. Eftersom aktörstjänsten i sig `StatefulServiceBase`bara är en implementering av kan `ActorService` du skriva en egen tjänst som härstammar från och implementera funktioner på servicenivå på samma sätt som när du `StatefulService`ärver, till exempel:

* Säkerhetskopiering och återställning av tjänsten.
* Delad funktionalitet för alla aktörer, till exempel en kretsbrytare.
* Fjärrproceduranrop på aktörstjänsten själv och på varje enskild aktör.

Mer information finns [i Implementera funktioner på tjänstnivå i aktörstjänsten](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Programmodell
Aktörstjänster är reliable services, så programmodellen är densamma. Men aktören ramverk bygga verktyg generera några av programmodell filer för dig.

### <a name="service-manifest"></a>Servicemanifest
Bildaktörsramverksversionsverktygen genererar automatiskt innehållet i aktörstjänstens ServiceManifest.xml-fil. Den här filen innehåller:

* Tjänsttyp för aktör. Typnamnet genereras baserat på aktörens projektnamn. Baserat på attributet persistens på din aktör anges även Flaggan HasPersistedState i enlighet med detta.
* Kodpaket.
* Config-paketet.
* Resurser och slutpunkter.

### <a name="application-manifest"></a>Programmanifest
Bildaktörsramverksversionsverktygen skapar automatiskt en standardtjänstdefinition för aktörstjänsten. Byggverktygen fyller standardserviceegenskaperna:

* Antalet replikuppsättningar bestäms av attributet persistens för aktören. Varje gång attributet persistens för aktören ändras återställs antalet replikuppsättningar i standardtjänstdefinitionen i enlighet med detta.
* Partitionsschema och intervall är inställda på Uniform Int64 med hela Int64 nyckelintervall.

## <a name="service-fabric-partition-concepts-for-actors"></a>Partitionskoncept för Service Fabric för aktörer
Aktörstjänster är partitionerade tillståndskänsliga tjänster. Varje partition i en aktörstjänst innehåller en uppsättning aktörer. Tjänstpartitioner distribueras automatiskt över flera noder i Service Fabric. Aktörsinstanser distribueras som ett resultat.

![Skådespelarepartitionering och distribution][5]

Reliable Services kan skapas med olika partitionsscheman och partitionsnyckelintervall. Aktörstjänsten använder Int64-partitioneringsschemat med hela Int64-nyckelintervallet för att mappa aktörer till partitioner.

### <a name="actor-id"></a>Aktörs-ID
Varje aktör som har skapats i tjänsten har ett unikt ID som är associerat med det, `ActorId` representerat av klassen. `ActorId`är ett ogenomskinligt ID-värde som kan användas för enhetlig distribution av aktörer över tjänstpartitionerna genom att generera slumpmässiga ID:a

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Varje `ActorId` hashed till en Int64. Det är därför aktörstjänsten måste använda ett Int64-partitioneringsschema med hela Int64-nyckelintervallet. Anpassade ID-värden kan dock `ActorID`användas för en , inklusive GUIDs/UUIDs, strängar och Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

När du använder GUIDs/UUIDs och strängar hasheras värdena till en Int64. Men när du uttryckligen tillhandahåller en Int64 till en `ActorId`mappas Int64 direkt till en partition utan ytterligare hashing. Du kan använda den här tekniken för att styra vilken partition skådespelarna placeras i.


## <a name="next-steps"></a>Nästa steg
* [Hantering av aktörstillstånd](service-fabric-reliable-actors-state-management.md)
* [Skådespelarens livscykel och skräpinsamling](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentation för ACTORS API-referens](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [EXEMPELkod för .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
