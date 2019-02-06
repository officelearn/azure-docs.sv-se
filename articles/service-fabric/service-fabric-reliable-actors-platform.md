---
title: Reliable Actors i Service Fabric | Microsoft Docs
description: Beskriver hur Reliable Actors lager på Reliable Services och använder funktionerna i Service Fabric-plattformen.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 82818df267fc08bf200178fda2c8d621bdd832ca
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753171"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hur Reliable Actors använder Service Fabric-plattformen
Den här artikeln förklarar hur Reliable Actors fungerar på Azure Service Fabric-plattformen. Reliable Actors som körs i ett ramverk som är värd för en implementering av en tillståndskänslig tillförlitlig tjänst kallas den *aktörstjänsten*. Aktörstjänsten innehåller alla komponenter som krävs för att hantera livscykel och skicka för din aktörer meddelande:

* Actor Runtime hanterar livscykeln, skräpinsamling, och tillämpar entrådiga åtkomst.
* En aktör service remoting lyssnare accepterar fjärråtkomst anrop till aktörer och skickar dem till en dispatcher att dirigera till lämplig aktör-instans.
* Tillståndsprovider aktören omsluter tillstånd providers (till exempel tillståndsprovider för tillförlitliga samlingar) och tillhandahåller ett kort för aktören tillståndshantering.

De här komponenterna utgör tillsammans Reliable Actor-ramverket.

## <a name="service-layering"></a>Tjänsten lagren
Eftersom aktörstjänsten själva är en tillförlitlig tjänst alla de [programmodell](service-fabric-application-model.md), livscykel, [paketering](service-fabric-package-apps.md), [distribution](service-fabric-deploy-remove-applications.md), uppgradera och skala begreppet pålitliga Tjänster gäller på samma sätt till aktörstjänster.

![Aktören service lagren][1]

Det föregående diagrammet visar relationen mellan Service Fabric-programramverk och användarkod. Blå element representerar Reliable Services application framework, orange representerar Reliable Actor-ramverket och grön är användarkod.

Reliable Services tjänsten ärver den `StatefulService` klass. Den här klassen är själva härleds från `StatefulServiceBase` (eller `StatelessService` för tillståndslösa tjänster). I Reliable Actors använder aktörstjänsten. Aktörstjänsten är en annan implementering av den `StatefulServiceBase` klass som implementerar mönstret aktör där din aktörer kör. Eftersom aktörstjänsten själva är bara en implementering av `StatefulServiceBase`, kan du skriva din egen tjänst som härleds från `ActorService` och implementera funktioner på servicenivå på samma sätt som vid arv `StatefulService`, till exempel:

* Tjänsten säkerhetskopiering och återställning.
* Delade funktioner för alla aktörer, till exempel en kretsbrytare.
* RPC-anrop på aktörstjänsten själva och på varje enskild skådespelare.

Mer information finns i [implementerar funktioner på servicenivå i din aktörstjänsten](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Programmodell
Aktörstjänster är pålitliga tjänster så programmodellen är samma. Dock skapa genereringsverktyg för aktören framework vissa av modellen programfiler för dig.

### <a name="service-manifest"></a>Tjänstmanifestet
Genereringsverktyg för aktören framework genererar automatiskt innehållet i din aktörstjänsten ServiceManifest.xml-filen. Den här filen innehåller:

* Typ av aktör tjänst. Namnet på genereras baserat på din aktörs projektnamn. Baserat på attributet persistence på aktören, anges flaggan HasPersistedState också i enlighet med detta.
* Kodpaketet.
* Konfigurationspaket.
* Resurser och slutpunkter.

### <a name="application-manifest"></a>Programmanifest
Genereringsverktyg för aktören framework skapar automatiskt en standard-tjänstdefinition för actor-tjänst. Build-verktyg anger egenskaperna för standard-tjänsten:

* Ange replikantal bestäms av attributet persistence för aktören. Varje gång persistence-attributet på aktören ändras återställs uppsättningen replikantal i tjänstdefinitionen standard därefter.
* Partitionsschemat och intervallet är inställda på Uniform Int64 med det fullständiga viktiga Int64-intervallet.

## <a name="service-fabric-partition-concepts-for-actors"></a>Begrepp för Service Fabric-partition för aktörer
Aktörstjänster är partitionerad tillståndskänsliga tjänster. Varje partition för en aktörstjänsten innehåller en uppsättning aktörer. Tjänstpartitioner fördelas automatiskt över flera noder i Service Fabric. Aktörsinstanser distribueras som ett resultat.

![Aktören partitionering och distribution][5]

Reliable Services kan skapas med olika partitionsscheman och partition nyckelintervall. Aktörstjänsten använder Int64 partitioneringsschemat med viktiga Int64 med allt för att mappa aktörer till partitioner.

### <a name="actor-id"></a>Aktörs-ID
Varje skådespelare som skapats i tjänsten har ett unikt ID som är associerade med det representeras av den `ActorId` klass. `ActorId` är en täckande ID-värde som kan användas för jämn fördelning av aktörer över tjänstpartitionerna genom att generera slumpmässiga ID: N:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Varje `ActorId` hash-kodas till en Int64. Det är därför aktörstjänsten måste använda en Int64 partitioneringsschemat med viktiga Int64 med allt. Dock anpassade ID-värden kan användas för en `ActorID`, inklusive GUID/UUID: N, strängar och Int64s.

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

När du använder GUID/UUID: N och strängar värdena är hash-kodas till en Int64. Men när du uttryckligen etablerar Int64 till en `ActorId`, Int64 mappas direkt till en partition utan ytterligare hash. Du kan använda den här tekniken för att styra vilken partition aktörer placeras i.


## <a name="next-steps"></a>Nästa steg
* [Aktören tillståndshantering](service-fabric-reliable-actors-state-management.md)
* [Aktör livscykel och skräpinsamling samling](service-fabric-reliable-actors-lifecycle.md)
* [Aktörer API-referensdokumentation](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [.NET-exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkoden](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
