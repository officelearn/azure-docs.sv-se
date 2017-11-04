---
title: "Polymorfism inom ramen för Reliable Actors | Microsoft Docs"
description: "Skapa hierarkier av .NET-gränssnitt och typer i Reliable Actors framework återanvända funktioner och API-definitioner."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 36a59a41b2261369a2062c76ef90aebf7e24a221
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfism inom ramen för Reliable Actors
Reliable Actors framework kan du skapa aktörer med många av samma teknik som du vill använda i objektorienterad design. En av dessa tekniker är polymorfism som tillåter typer och gränssnitt för att ärva från flera generaliserad överordnade. Arv inom ramen för Reliable Actors följer vanligtvis .NET modellen med några ytterligare begränsningar. Vid Java-/ Linux följer Java-modellen.

## <a name="interfaces"></a>Gränssnitt
Reliable Actors framework måste du definiera minst ett gränssnitt implementeras av aktören-typen. Det här gränssnittet används för att generera en proxyklass som kan användas av klienter för att kommunicera med dina aktörer. Gränssnitt kan ärvas från andra gränssnitt, förutsatt att varje gränssnitt som implementeras av en aktörstyp av och alla dess överordnade slutligen härledas från IActor(C#) eller Actor(Java). IActor(C#) och Actor(Java) är definierad på plattform basgränssnitt för aktörer i ramverk .NET och Java. Därför klassiska polymorfism exemplet med hjälp av former kan se ut ungefär så här:

![Gränssnittet hierarki för formen aktörer][shapes-interface-hierarchy]

## <a name="types"></a>Typer
Du kan också skapa en hierarki av aktören typer som härletts från aktören basklass som tillhandahålls av plattformen. Du kanske har en bas för former, `Shape`(C#) eller `ShapeImpl`(Java) typ:

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

Observera den `ActorService` -attributet i aktörstyp. Det här attributet anger tillförlitliga aktören framework att den automatiskt skapar en tjänst som värd för aktörer av den här typen. I vissa fall kanske du vill skapa en bastyp som endast är avsedd för att dela funktioner med undertyper och aldrig används för att skapa en instans av konkreta aktörer. I sådana fall kan du använda den `abstract` nyckelord för att indikera att du aldrig skapar en aktör baserat på den typen.

## <a name="next-steps"></a>Nästa steg
* Se [hur Reliable Actors framework utnyttjar Service Fabric-plattformen](service-fabric-reliable-actors-platform.md) att tillhandahålla tillförlitlighet, skalbarhet och konsekvent tillstånd.
* Lär dig mer om den [aktören livscykel](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
