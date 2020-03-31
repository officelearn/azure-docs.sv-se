---
title: Hantera tillståndet Azure Service Fabric
description: Lär dig mer om hur du använder, sparar och tar bort tillstånd för en tillförlitlig aktör för Azure Service Fabric och överväganden när du utformar ett program.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 788c337a37ec66c5aa1521c5cd9f2816ed7a8bf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645641"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Komma åt, spara och ta bort tillståndet För tillförlitliga aktörer
[Reliable Actors](service-fabric-reliable-actors-introduction.md) är entrådiga objekt som kan kapsla in både logik och tillstånd och underhålla tillstånd på ett tillförlitligt sätt. Varje aktörsinstans har sin egen [tillståndshanterare:](service-fabric-reliable-actors-state-management.md)en ordlisteliknande datastruktur som på ett tillförlitligt sätt lagrar nyckel-/värdepar. Tillståndshanteraren är ett omslag runt en tillståndsprovider. Du kan använda den för att lagra data oavsett vilken [beständighetsinställning](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) som används.

Tangenter för tillståndshanteraren måste vara strängar. Värden är generiska och kan vara vilken typ som helst, inklusive anpassade typer. Värden som lagras i tillståndshanteraren måste vara serialiserbara för datakontrakt eftersom de kan överföras över nätverket till andra noder under replikeringen och kan skrivas till disk, beroende på aktörens inställning för tillståndsbeständighet.

Tillståndshanteraren exponerar vanliga ordlistemetoder för att hantera tillstånd, liknande dem som finns i Tillförlitlig ordlista.

Mer information finns [i metodtips för hantering av aktörstillstånd](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Åtkomsttillstånd
Tillstånd nås via tillståndshanteraren efter nyckel. State manager metoder är alla asynkrona eftersom de kan kräva disk I / O när aktörer har beständigt tillstånd. Vid första åtkomsten cachelagras tillståndsobjekt i minnet. Upprepa åtkomståtgärder åtkomstobjekt direkt från minnet och returnera synkront utan att ådra sig disk-I/O eller asynkron kontextväxling. Ett tillståndsobjekt tas bort från cacheminnet i följande fall:

* En aktörsmetod genererar ett ohanterat undantag när den har hämtat ett objekt från tillståndshanteraren.
* En aktör återaktiveras, antingen efter att ha inaktiverats eller efter fel.
* Tillståndsproviderns sidor uppger disken. Detta beror på tillståndsproviderns implementering. Standardtillståndsprovidern `Persisted` för inställningen har detta beteende.

Du kan hämta tillstånd med hjälp av en *standardhämtningsåtgärd* som genererar `KeyNotFoundException`(C#) eller `NoSuchElementException`(Java) om det inte finns någon post för nyckeln:

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

Du kan också hämta tillstånd med hjälp av en *TryGet-metod* som inte genererar om en post inte finns för en nyckel:

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
Metoderna för hämtning av tillståndshanterare returnerar en referens till ett objekt i det lokala minnet. Om du ändrar det här objektet i det lokala minnet kan det inte sparas på ett sätt. När ett objekt hämtas från tillståndshanteraren och ändras måste det återinföras i tillståndshanteraren för att kunna sparas på ett sätt.

Du kan infoga tillstånd *Set*med hjälp av en `dictionary["key"] = value` ovillkorlig uppsättning , vilket motsvarar syntaxen:

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

Du kan lägga till tillstånd med hjälp av metoden *Lägg till.* Den här `InvalidOperationException`metoden genererar (C#) eller `IllegalStateException`(Java) när den försöker lägga till en nyckel som redan finns.

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

Du kan också lägga till tillstånd med hjälp av en *TryAdd-metod.* Den här metoden kastas inte när den försöker lägga till en nyckel som redan finns.

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

I slutet av en aktörsmetod sparar tillståndshanteraren automatiskt alla värden som har lagts till eller ändrats av en infoga eller uppdateringsåtgärd. En "spara" kan innehålla beständiga till disk och replikering, beroende på de inställningar som används. Värden som inte har ändrats sparas inte eller replikeras. Om inga värden har ändrats gör sparåtgärden ingenting. Om sparandet misslyckas ignoreras det ändrade tillståndet och det ursprungliga tillståndet läss in igen.

Du kan också spara tillstånd `SaveStateAsync` manuellt genom att anropa metoden på aktörsbasen:

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
Du kan ta bort tillståndet permanent från en aktörs tillståndshanterare genom att anropa metoden *Ta bort.* Den här `KeyNotFoundException`metoden genererar (C#) eller `NoSuchElementException`(Java) när den försöker ta bort en nyckel som inte finns.

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

Du kan också ta bort tillståndet permanent med hjälp av *Metoden TryRemove.* Den här metoden kastas inte när den försöker ta bort en nyckel som inte finns.

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

Tillstånd som lagras i Reliable Actors måste serialiseras innan det skrivs till disk och replikeras för hög tillgänglighet. Läs mer om [serialisering av aktörstyp](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Läs sedan mer om [skådespelares diagnostik och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md).
