---
title: Polymorfism inom ramen för reliable actors
description: Skapa hierarkier för .NET-gränssnitt och typer i ramen för reliable actors för återanvändning av funktioner och API-definitioner.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4e485463f41cdfbadeb166ecbb3a86d4a32c1589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348927"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfism inom ramen för reliable actors
Med reliable actors-ramverket kan du bygga aktörer med många av samma tekniker som du skulle använda i objektorienterad design. En av dessa tekniker är polymorfism, som gör att typer och gränssnitt att ärva från mer generaliserade föräldrar. Arv i ramen för Reliable Actors följer i allmänhet .NET-modellen med några ytterligare begränsningar. Vid Java/Linux följer den Java-modellen.

## <a name="interfaces"></a>Gränssnitt
Reliable Actors-ramverket kräver att du definierar minst ett gränssnitt som ska implementeras av din aktörstyp. Det här gränssnittet används för att generera en proxyklass som kan användas av klienter för att kommunicera med dina aktörer. Gränssnitt kan ärva från andra gränssnitt så länge varje gränssnitt som genomförs av en aktör typ och alla dess föräldrar i slutändan härrör från IActor (C #) eller Actor (Java) . IActor(C#) och Actor(Java) är de plattformsdefinierade basgränssnitten för aktörer i ramverken .NET respektive Java. Således kan det klassiska polymorfismexemplet med former se ut ungefär så här:

![Gränssnittshierarki för formaktörer][shapes-interface-hierarchy]

## <a name="types"></a>Typer
Du kan också skapa en hierarki av aktörstyper som härleds från klassen basskådespelaraktör som tillhandahålls av plattformen. När det gäller former kan du `Shape`ha en `ShapeImpl`bastyp (C#) eller (Java) typ:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Undertyper av `Shape`(C#) `ShapeImpl`eller (Java) kan åsidosätta metoder från basen.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Observera `ActorService` attributet på aktörstypen. Det här attributet talar om för reliable actor-ramverket att det automatiskt ska skapa en tjänst för att vara värd för aktörer av den här typen. I vissa fall kanske du vill skapa en bastyp som endast är avsedd för delning av funktioner med undertyper och kommer aldrig att användas för att instansiera betongaktörer. I sådana fall bör `abstract` du använda nyckelordet för att ange att du aldrig kommer att skapa en aktör baserat på den typen.

## <a name="next-steps"></a>Nästa steg
* Se [hur reliable actors-ramverket utnyttjar Service Fabric-plattformen](service-fabric-reliable-actors-platform.md) för att ge tillförlitlighet, skalbarhet och konsekvent tillstånd.
* Läs mer om [skådespelarens livscykel](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
