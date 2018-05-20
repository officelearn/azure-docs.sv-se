---
title: Hantera Azure Service Fabric tillstånd | Microsoft Docs
description: Lär dig att komma åt, spara och ta bort Service Fabric Reliable Actors tillstånd.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: ac3afe144b9cf9e2fb307087edb175a603ffe4e9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Komma åt, spara och ta bort Reliable Actors tillstånd
[Reliable Actors](service-fabric-reliable-actors-introduction.md) är enkeltrådad objekt som kan kapsla in både logik och tillstånd och upprätthålla tillstånd på ett tillförlitligt sätt. Varje instans aktören har sin egen [tillståndshanterare](service-fabric-reliable-actors-state-management.md): en ordlista-liknande datastruktur som lagras på ett tillförlitligt sätt nyckel/värde-par. Tillståndshanterarens är en omslutning runt en tillståndsprovidern. Du kan använda för att lagra data oavsett vilket [beständiga inställningen](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) används.

Tillstånd manager nycklar måste vara strängar. Värden är generisk och kan ha olika typer, inklusive anpassade typer. Värden som lagras i tillståndshanterarens måste vara datakontrakt serialiserbara eftersom de kan överföras via nätverket till andra noder vid replikering och kan skrivas till disk, beroende på en aktör tillståndsinställningen persistence.

Tillståndshanterarens visar vanliga ordlista metoder för att hantera tillstånd, liknar de som finns i tillförlitliga ordlistan.

Mer information finns i [bästa praxis vid hantering av aktörstillstånd](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Åtkomstläge
Tillstånd kan nås via tillståndshanterarens av nyckeln. Tillstånd manager metoder är alla asynkrona eftersom de kan kräva disk-i/o när aktörer beständiga tillstånd. Vid första åtkomst cachelagras tillstånd objekt i minnet. Upprepa åtkomst operations access-objekt direkt från minnet och returnera synkront utan att det medför i/o eller asynkrona kontexten-växla omkostnader. Ett tillstånd objekt tas bort från cachen i följande fall:

* En aktörsmetod utlöser ett undantag när det hämtar ett objekt från hanteraren tillstånd.
* En aktör återaktiveras när de har inaktiverats eller efter fel.
* Tillstånd providern sidor status till disk. Det här problemet beror på tillstånd providerns implementering. Standardprovidern för tillstånd för den `Persisted` har problemet.

Du kan hämta tillstånd genom att använda ett standard- *hämta* åtgärden utlöser `KeyNotFoundException`(C#) eller `NoSuchElementException`(Java) om det inte finns en post för nyckeln:

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

Du kan också hämta tillstånd med hjälp av en *TryGet* metod som inte genereras om det inte finns en post för en nyckel:

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
Tillstånd manager hämtning metoder returnerar en referens till ett objekt i lokalt minne. Ändra det här objektet i lokala minnet enbart orsakar inte den sparas varaktigt. När ett objekt som hämtats från tillståndshanterarens och ändras, måste den igen i tillståndshanterarens sparas varaktigt.

Du kan infoga tillstånd med hjälp av en ovillkorlig *ange*, som motsvarar den `dictionary["key"] = value` syntax:

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

Du kan lägga till tillstånd med hjälp av en *Lägg till* metod. Den här metoden genererar `InvalidOperationException`(C#) eller `IllegalStateException`(Java) vid försök att lägga till en nyckel som redan finns.

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

Du kan också lägga till tillstånd med hjälp av en *TryAdd* metod. Den här metoden genereras inget när den försöker lägga till en nyckel som redan finns.

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

I slutet av en aktörsmetod sparar tillståndshanterarens automatiskt alla värden som har lagts till eller ändrats av en insert eller update-åtgärd. ”Spara” kan innehålla beständighet till disk och replikering, beroende på inställningarna som används. Värden som inte har ändrats beständiga eller replikeras inte. Om inga värden har ändrats, spara åtgärden händer ingenting. Om du sparar misslyckas ändringstillstånd ignoreras och det ursprungliga tillståndet läses.

Du kan också spara tillstånd manuellt genom att anropa den `SaveStateAsync` metod i basen aktören:

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
Du kan ta bort tillstånd permanent från en aktör tillstånd manager genom att anropa den *ta bort* metod. Den här metoden genererar `KeyNotFoundException`(C#) eller `NoSuchElementException`(Java) vid försök att ta bort en nyckel som inte finns.

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

Du kan också ta bort tillstånd permanent med hjälp av den *TryRemove* metod. Den här metoden genereras inget när den försöker ta bort en nyckel som inte finns.

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

Tillstånd som är lagrad i Reliable Actors måste serialiseras innan dess skrivits till disk och replikeras för hög tillgänglighet. Lär dig mer om [aktören typserialiseringen](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Därefter lär dig mer om [aktören diagnostik- och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md).
