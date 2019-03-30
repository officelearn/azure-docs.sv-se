---
title: Utveckla enhetstester för tillståndskänsliga tjänster i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du utvecklar enhetstester för tillståndskänslig Service Fabric-tjänster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: b066296ca52d3067f8985245161eb4fa7b484a07
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669120"
---
# <a name="create-unit-tests-for-stateful-services"></a>Skapa enhetstester för tillståndskänsliga tjänster
Enhetstestning Service Fabric tillståndskänsliga tjänster ger vanliga misstag som inte skulle nödvändigtvis fångas upp av konventionella program eller domänspecifika Enhetstestning. Det finns vissa saker som ska finnas i åtanke när du utvecklar enhetstester för tillståndskänsliga tjänster.

1. Varje replik kör programkod men under annan kontext. Om tjänsten använder tre repliker, körs kod som på tre noder parallellt under olika kontexten/roll.
2. Tillstånd som lagras i den tillståndskänsliga tjänsten ska vara konsekvent mellan alla repliker. State manager och tillförlitliga samlingar ger den här konsekvens out-of the box. Dock behöver InMemory-tillstånd som ska hanteras av programkoden.
3. Varje replik ändras roller vid en viss tidpunkt när du kör på klustret. En sekundär replik blir en primär i händelse av att den nod som är värd för primärt blir inte tillgänglig eller så är överbelastad. Detta är naturlig beteende för Service Fabric därför tjänster måste du planera för att köra så småningom under en annan roll.

Den här artikeln förutsätter att [Enhetstestning tillståndskänsliga tjänster i Service Fabric](service-fabric-concepts-unit-testing.md) har lästs.

## <a name="the-servicefabricmocks-library"></a>ServiceFabric.Mocks-bibliotek
Från och med version 3.3.0, [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) tillhandahåller ett API för simulerade både för samordning av replikerna och tillståndshantering. Detta används i exemplen.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks är inte ägs eller hanteras av Microsoft. Det är dock för närvarande Microsofts rekommenderade biblioteket för Enhetstestning tillståndskänsliga tjänster.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Ställa in fingerad orchestration och tillstånd
Som en del av den ordna delen av ett test, en fingerad replikuppsättning och tillståndshanterare kommer att skapas. Replikuppsättningen kommer sedan äger för att skapa en instans av tjänsten testade för varje replik. Det kommer också ägare Livscykelhändelser för verkställande som `OnChangeRole` och `RunAsync`. Fingerad tillståndshanterare garanterar åtgärder som utförs mot tillstånd manager körs och sparas som hanteraren för bibliotekets aktuella tillstånd.

1. Skapa ett service fabrik ombud som instantierar tjänsten som testas. Detta bör vara liknande eller densamma som service fabrik återanrop som vanligtvis finns i `Program.cs` för en Service Fabric-tjänst eller en aktör. Detta bör följa följande signaturen:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Skapa en instans av `MockReliableStateManager` klass. Detta kommer fingera all interaktion med hanteraren för tillstånd.
3. Skapa en instans av `MockStatefulServiceReplicaSet<TStatefulService>` där `TStatefulService` är typ av tjänsten som testas. Detta kräver ombud som skapades i steg #1 och tillståndshanterare instansierats i #2
4. Lägga till repliker i uppsättningen. Ange rollen (till exempel primär, ActiveSecondary, IdleSecondary) och ID för repliken
   > Vänta till replik-ID: N! Dessa sannolikt kommer att användas under åtgärden och assert delar av en enhetstest.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Köra tjänstbegäranden
Tjänstbegäranden kan köras på en specifik replik med hjälp av praktiska skäl egenskaper och sökningar.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Köra tjänsten flytta
Fingerad replikuppsättningen visar flera bekväma metoder för att utlösa olika typer av tjänsten flyttar.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Färdigställa allt
Följande test visar hur du konfigurerar en replikuppsättning med tre noder och verifiera att data är tillgängliga från en sekundär efter en rolländring av. Ett vanligt problem i det här kan fånga upp är om data har lagts till under `InsertAsync` sparades till något i minnet eller till en tillförlitlig samling utan att köra `CommitAsync`. I båda fallen är sekundär synkroniserad med primärt. Detta skulle leda till inkonsekvent svar när tjänsten har flyttats.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Nästa steg
Lär dig hur du testar [tjänst-till-tjänst-kommunikation](service-fabric-testability-scenarios-service-communication.md) och [simulera fel med kontrollerat chaos](service-fabric-controlled-chaos.md).
