---
title: Reliable Actors på Service Fabric | Microsoft Docs
description: Beskriver hur Reliable Actors ovanpå Reliable Services och använda funktioner i Service Fabric-plattformen.
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
ms.openlocfilehash: f8e6ad4b23eeaf46cccac9c8ff9d41f71511129d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642860"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hur Reliable Actors använder Service Fabric-plattformen
Den här artikeln förklarar hur Reliable Actors fungerar på Azure Service Fabric-plattformen. Reliable Actors som körs i ett ramverk som är värd för en implementering av en tillståndskänslig tillförlitlig tjänst kallas den *aktören tjänsten*. Tjänsten aktören innehåller alla komponenter som krävs för att hantera livscykeln och meddelandet sändning för din aktörer:

* Aktören Runtime hanterar livscykeln skräpinsamling, och tillämpar Enkeltrådig åtkomst.
* En aktören service fjärrkommunikation lyssnare accepterar fjärråtkomst anrop till aktörer och skickar dem till en dispatcher att dirigera till lämplig aktören-instans.
* Aktören Tillståndsprovidern radbryts tillståndsprovidrar (till exempel tillståndsprovidern tillförlitliga samlingar) och ger ett nätverkskort för hantering av aktören tillstånd.

De här komponenterna utgör tillsammans tillförlitliga aktören ramen.

## <a name="service-layering"></a>Tjänsten lager
Eftersom tjänsten aktören är en tillförlitlig tjänst alla de [programmodell](service-fabric-application-model.md), livscykel, [paketering](service-fabric-package-apps.md), [distribution](service-fabric-deploy-remove-applications.md), uppgradera och skalning begreppet Reliable Services tillämpas på samma sätt i aktörstjänster.

![Aktören service lager][1]

Föregående diagram visar relationen mellan Service Fabric-programramverk och användarkod. Blå element representerar Reliable Services application framework, orange representerar tillförlitliga aktören framework och grönt representerar användarkod.

I Reliable Services tjänsten ärver den `StatefulService` klass. Den här klassen är härledd från `StatefulServiceBase` (eller `StatelessService` för tillståndslösa tjänster). I Reliable Actors använder du tjänsten aktören. Aktören-tjänsten är en annan implementering av den `StatefulServiceBase` klassen som implementerar aktören mönstret där din aktörer kör. Eftersom tjänsten aktören är bara en implementering av `StatefulServiceBase`, du kan skriva en egen tjänst som härleds från `ActorService` och implementera servicenivå funktioner på samma sätt som vid arv `StatefulService`, som:

* Tjänsten säkerhetskopiering och återställning.
* Delade funktioner för alla aktörer, till exempel strömbrytare.
* RPC-anrop på tjänsten aktören och på varje enskild aktören.

Mer information finns i [implementera servicenivå funktioner i tjänsten aktören](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Programmodell
Aktören tjänsterna är tillförlitliga, så att programmet modellen är samma. Dock generera aktören framework build-verktyg några av modellen programfilerna för dig.

### <a name="service-manifest"></a>Tjänstmanifestet
Aktören framework build-verktyg generera automatiskt innehållet i aktören tjänstens ServiceManifest.xml fil. Den här filen innehåller:

* Aktören tjänsttyp. Typnamnet genereras baserat på din aktören projektnamn. Baserat på attributet beständiga på din aktören ställs flaggan HasPersistedState också in därefter.
* Kodpaketet.
* Konfigurationspaketet.
* Resurser och slutpunkter.

### <a name="application-manifest"></a>Applikationsmanifestet
Aktören framework build-verktyg skapa automatiskt en standard service definition för aktören tjänsten. Build-verktyg fylla standardegenskaperna för tjänsten:

* Ange replikantalet bestäms av beständiga attribut i din aktören. Varje gång beständiga attribut i din aktören ändras återställs uppsättningen replikantalet i tjänstdefinitionen standard därför.
* Partitionsschemat och intervallet är inställda Uniform Int64 med fullständig viktiga Int64-intervall.

## <a name="service-fabric-partition-concepts-for-actors"></a>Service Fabric-partition begrepp för aktörer
Aktörstjänster är partitionerad tillståndskänsliga tjänster. Varje partition för en tjänst aktören innehåller en uppsättning aktörer. Tjänsten partitioner distribueras automatiskt över flera noder i Service Fabric. Aktören instanser distribueras som ett resultat.

![Aktören partitionering och distribution][5]

Reliable Services kan skapas med annan partitionsscheman och partition nyckelintervall. Tjänsten aktören använder Int64 partitioneringsschema med fullständig viktiga Int64-intervall för att mappa aktörer till partitioner.

### <a name="actor-id"></a>Aktörs-ID
Varje aktören som skapas i tjänsten har ett unikt ID som är associerade med den representeras av den `ActorId` klass. `ActorId` är ett täckande ID-värde som kan användas för jämn fördelning av aktörer över partitioner för tjänsten genom att generera slumpmässigt ID: N:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Varje `ActorId` hash-kodas till en Int64. Det är därför aktören tjänsten måste använda ett Int64 partitioneringsschema med fullständig viktiga Int64-intervall. Dock anpassade ID-värden kan användas för en `ActorID`, inklusive GUID/UUID: er, strängar och Int64s.

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

När du använder GUID/UUID: er och strängar värdena-kodas till en Int64. Men när du uttryckligen ger Int64 till en `ActorId`, Int64 mappas direkt till en partition utan ytterligare hash. Du kan använda den här tekniken för att styra vilka partition aktörer placeras i.


## <a name="next-steps"></a>Nästa steg
* [Aktören tillståndshantering](service-fabric-reliable-actors-state-management.md)
* [Aktören livscykel och skräp samling](service-fabric-reliable-actors-lifecycle.md)
* [Aktörer API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exempelkod för .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkod](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
