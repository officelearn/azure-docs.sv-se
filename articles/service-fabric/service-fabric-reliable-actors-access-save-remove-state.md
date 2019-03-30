---
title: Hantera Azure Service Fabric tillstånd | Microsoft Docs
description: Lär dig att komma åt, spara och ta bort Service Fabric Reliable Actors-tillstånd.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 7c10d00916ef65767c98616c7337bfa444c339a9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664734"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Komma åt, spara och ta bort Reliable Actors-tillstånd
[Reliable Actors](service-fabric-reliable-actors-introduction.md) entrådiga objekt som kan kapsla in både logik och tillstånd och upprätthålla tillstånd på ett tillförlitligt sätt. Varje aktör instans har sin egen [tillståndshanterare](service-fabric-reliable-actors-state-management.md): en ordlista-liknande datastruktur som lagrar på ett tillförlitligt sätt nyckel/värde-par. Tillstånd-manager är en omslutning runt en tillståndsprovider. Du kan använda den för att lagra data oavsett vilket [persistence inställningen](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) används.

Tillståndshanteraren nycklar måste vara strängar. Värden är allmänna och kan vara valfri, inklusive anpassade typer. Värden som lagras i tillståndet manager måste vara datakontrakt serialiserbara eftersom de kan överföras via nätverket till andra noder vid replikering och kan skrivas till disk, beroende på en aktör tillståndsinställningen för persistence.

Tillstånd-manager visar vanliga ordlista metoder för att hantera tillstånd, liknar dem som finns i en tillförlitlig ordlista.

Mer information finns i [bästa praxis vid hantering av aktörstillstånd](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Åtkomstläge
Tillstånd nås via Hanteraren för tillstånd av nyckeln. Tillstånd manager metoder är asynkrona allt eftersom de kan kräva disk-i/o när aktörer beständiga tillstånd. Vid första åtkomst cachelagras tillstånd objekt i minnet. Upprepa åtkomst operations access-objekt direkt från minnet och returnera synkront klusteruppgradering diskens i/o- eller asynkron kontextberoende information. Ett tillstånd objekt tas bort från cachen i följande fall:

* En aktörsmetod kastar ett ohanterat undantag när den hämtar ett objekt från hanteraren för tillstånd.
* En aktör återaktiveras när de har inaktiverats eller efter fel.
* Tillstånd-providern sidor tillståndet till disk. Det här beteendet är beroende av implementeringen av databasleverantör som tillstånd. Standardprovidern för tillstånd för den `Persisted` har det här beteendet.

Du kan hämta tillstånd genom att använda en *hämta* åtgärden som genererar `KeyNotFoundException`(C#) eller `NoSuchElementException`(Java) om en post saknas för nyckeln:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Du kan också hämta tillstånd med hjälp av en *TryGet* metod som inget genereras om en post saknas för en nyckel:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Spara tillstånd
Tillstånd manager hämtning metoder returnerar en referens till ett objekt i lokalt minne. Ändra det här objektet i lokalt minne enbart innebär det inte att den sparas varaktigt. När ett objekt hämtas från hanteraren för tillstånd och ändras, måste infogas igen i tillståndshanterare sparas varaktigt.

Du kan infoga tillstånd med hjälp av en ovillkorlig *ange*, vilket är detsamma som det `dictionary["key"] = value` syntax:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Du kan lägga till tillstånd med hjälp av en *Lägg till* metod. Den här metoden utlöser `InvalidOperationException`(C#) eller `IllegalStateException`(Java) vid försök att lägga till en nyckel som redan finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Du kan också lägga till tillstånd med hjälp av en *TryAdd* metod. Den här metoden genereras vid försök att lägga till en nyckel som redan finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

I slutet av en aktörsmetod sparar tillstånd manager automatiskt alla värden som har lagts till eller ändras av en insert- eller update-åtgärd. ”Spara” kan innehålla beständig lagring till disk och replikering, beroende på vilka inställningar som används. Värden som inte har ändrats sparas eller replikeras inte. Om inga värden har ändrats, spara åtgärden gör ingenting. Om du sparar misslyckas ändringstillstånd ignoreras och det ursprungliga tillståndet läses.

Du kan också spara tillstånd manuellt genom att anropa den `SaveStateAsync` metoden utifrån aktör:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Ta bort tillstånd
Du kan ta bort tillstånd permanent från en aktör tillståndshanterare genom att anropa den *ta bort* metod. Den här metoden utlöser `KeyNotFoundException`(C#) eller `NoSuchElementException`(Java) vid försök att ta bort en nyckel som inte finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Du kan också ta bort tillstånd permanent med hjälp av den *TryRemove* metod. Den här metoden genereras vid försök att ta bort en nyckel som inte finns.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Nästa steg

Tillstånd som lagras i Reliable Actors måste serialiseras innan dess skrivs till och replikerade för hög tillgänglighet. Läs mer om [aktören typserialisering](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Därefter lär dig mer om [aktören diagnostik och övervakning av programprestanda](service-fabric-reliable-actors-diagnostics.md).
