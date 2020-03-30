---
title: Utveckla enhetstester för tillståndskänsliga tjänster
description: Lär dig mer om enhetstestning i Azure Service Fabric för tillståndskänsliga tjänster och särskilda överväganden att tänka på under utvecklingen.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c657bd8295d01a4e0fa4e44e969b33946684bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639844"
---
# <a name="create-unit-tests-for-stateful-services"></a>Skapa enhetstester för tillståndskänsliga tjänster
Enhetstestning Service Fabric tillståndskänsliga tjänster avslöjar vanliga misstag som inte nödvändigtvis skulle fångas av konventionella program eller domänspecifika enhetstestning. Samtidigt utveckla enhetstester för tillståndskänsliga tjänster, det finns några särskilda överväganden som bör hållas i åtanke.

1. Varje replik kör programkod men under olika kontexter. Om tjänsten använder tre repliker körs tjänstkoden på tre noder parallellt under olika kontext/roll.
2. Tillstånd som lagras inom den tillståndskänsliga tjänsten bör vara konsekvent bland alla repliker. Tillståndshanteraren och tillförlitliga samlingar kommer att ge denna konsekvens out-of-the-box. Minnestillstånd måste dock hanteras av programkoden.
3. Varje replik ändrar roller någon gång när du kör på klustret. En sekundär replik blir en primär, i händelse av att noden som är värd för den primära blir otillgänglig eller överbelastad. Detta är naturligt beteende för Service Fabric därför tjänster måste planera för att så småningom köra under en annan roll.

Den här artikeln förutsätter att [tillståndskänsliga enhetstesttjänster i Service Fabric](service-fabric-concepts-unit-testing.md) har lästs.

## <a name="the-servicefabricmocks-library"></a>Biblioteket ServiceFabric.Mocks
Från och med version 3.3.0 tillhandahåller [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) ett API för att håna både orkestrering av replikerna och tillståndshanteringen. Detta kommer att användas i exemplen.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks ägs eller underhålls inte av Microsoft. Detta är dock för närvarande Microsoft rekommenderade biblioteket för enhetstestning tillståndskänsliga tjänster.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Ställ in mock orkestrering och tillstånd
Som en del av ordna delen av ett test, en mock replikuppsättning och tillståndshanterare kommer att skapas. Replikuppsättningen äger sedan skapa en instans av den testade tjänsten för varje replik. Den kommer också att äga kör `OnChangeRole` `RunAsync`livscykelhändelser som och . Den falska tillståndshanteraren kommer att se till att alla åtgärder som utförs mot tillståndshanteraren körs och hålls som den faktiska tillståndshanteraren skulle.

1. Skapa ett servicefabriksombud som instansierar den tjänst som testas. Detta bör vara liknande eller samma som servicefabriken motringning som vanligtvis finns i `Program.cs` för en Service Fabric-tjänst eller aktör. Detta bör följa följande signatur:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Skapa en `MockReliableStateManager` förekomst av klass. Detta kommer att håna alla interaktioner med tillståndshanteraren.
3. Skapa en `MockStatefulServiceReplicaSet<TStatefulService>` instans `TStatefulService` av var är typen av tjänst som testas. Detta kräver att det ombud som skapats i steg #1 och tillståndshanteraren instansieras i #2
4. Lägg till repliker i replikuppsättningen. Ange rollen (till exempel Primär, ActiveSecondary, IdleSecondary) och ID för repliken
   > Håll i replik-ID:na! Dessa kommer sannolikt att användas under handlingen och hävda delar av ett enhetstest.

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

## <a name="execute-service-requests"></a>Kör tjänstbegäranden
Tjänstbegäranden kan köras på en specifik replik med hjälp av bekvämlighetsegenskaper och uppslag.
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

## <a name="execute-a-service-move"></a>Utföra en tjänstflytt
Den falska replikuppsättningen visar flera bekvämlighetsmetoder för att utlösa olika typer av tjänstflyttningar.
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
Följande test visar att du ställer in en replikuppsättning med tre nodord och verifierar att data är tillgängliga från en sekundär efter en rolländring. Ett typiskt problem som detta kan `InsertAsync` fånga är om de data som lades till `CommitAsync`under sparades antingen i något i minnet eller till en tillförlitlig samling utan att köra . I båda fallen skulle den sekundära vara osynkroniserad med den primära. Detta skulle leda till inkonsekventa svar efter tjänsten flyttar.

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
Lär dig hur du testar [service-to-service-kommunikation](service-fabric-testability-scenarios-service-communication.md) och [simulerar fel med kontrollerat kaos](service-fabric-controlled-chaos.md).
