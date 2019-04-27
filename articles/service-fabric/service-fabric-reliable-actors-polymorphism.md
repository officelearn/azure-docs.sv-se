---
title: Polymorfism i Reliable Actors framework | Microsoft Docs
description: Skapa hierarkier av .NET-gränssnitt och typer inom ramen för Reliable Actors kan återanvända funktioner och API-definitioner.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c14b3006184f7bd6dcd1eb67be11bd0214957d72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725506"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfism i Reliable Actors framework
Reliable Actors-framework kan du skapa aktörer använder många av samma teknik som du vill använda i objektorienterad design. En av dessa tekniker är polymorfism, vilket gör att typer och gränssnitt för att ärva från mer generaliserad föräldrar. Arv inom ramen för Reliable Actors följer Allmänt .NET-modellen med några ytterligare begränsningar. Vid Java/Linux följer det Java-modellen.

## <a name="interfaces"></a>Gränssnitt
Reliable Actors-ramverket måste du definiera minst en gränssnittet implementeras av din typ av aktör. Det här gränssnittet används för att generera en proxyklass som kan användas av klienter för att kommunicera med dina aktörer. Gränssnitt kan ärvas från andra gränssnitt, förutsatt att varje gränssnitt som implementeras av en typ av aktör och alla dess överordnade slutligen drar av IActor (C#) eller Actor(Java). IActor (C#) och Actor(Java) är definierad av plattformen grundläggande gränssnitt för aktörerna i ramverk .NET och Java respektive. Därför i klassiska polymorfism exempel som använder former kan se ut ungefär så här:

![Hierarki för gränssnittet för form aktörer][shapes-interface-hierarchy]

## <a name="types"></a>Typer
Du kan också skapa en hierarki av aktör typer som härletts från aktören basklassen som tillhandahålls av plattformen. När det gäller former, kan du ha en base `Shape`(C#) eller `ShapeImpl`(Java) typ:

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

Subtypes av `Shape`(C#) eller `ShapeImpl`(Java) kan åsidosätta metoder från basklassen.

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

Obs den `ActorService` aktörstypen-attributet. Det här attributet anger Reliable Actor-ramverket att den automatiskt skapar en tjänst som värd för aktörer av den här typen. I vissa fall kanske du vill skapa en bastyp som endast är avsedd för att dela funktioner med undertyper och aldrig används för att skapa en instans av konkreta aktörer. I sådana fall bör du använda den `abstract` nyckelord att indikera att du aldrig skapar en aktör baserat på den typen.

## <a name="next-steps"></a>Nästa steg
* Se [hur ramen för Reliable Actors använder Service Fabric-plattformen](service-fabric-reliable-actors-platform.md) att tillhandahålla tillförlitlighet, skalbarhet och konsekvent tillstånd.
* Lär dig mer om den [aktören livscykel](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
