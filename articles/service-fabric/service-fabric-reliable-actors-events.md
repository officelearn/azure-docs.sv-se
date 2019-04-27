---
title: Händelser i aktörbaserade Azure Service Fabric actors | Microsoft Docs
description: Introduktion till händelser för Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 9075fc8391e8afa21e3963c1eff6a630c586d647
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726408"
---
# <a name="actor-events"></a>Aktören händelser
Aktören händelser är ett sätt att skicka meddelanden för bästa prestanda från aktören till klienterna. Aktören händelser är utformade för aktören till klientkommunikation och bör inte användas för aktören till aktören kommunikation.

I följande kodavsnitt visar hur du använder aktören händelser i ditt program.

Definiera ett gränssnitt som beskriver de händelser som publicerats av aktören. Det här gränssnittet måste härledas från den `IActorEvents` gränssnitt. Argument av metoder måste vara [data kontrakt serialiserbara](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metoderna måste returnera void, som händelse-meddelanden är ett sätt och bästa prestanda.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Deklarera de händelser som publicerats av aktör i aktörsgränssnittet.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Implementera händelsehanteraren på klientsidan.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Skapa en proxy till aktören som publicerar händelsen och prenumerera på händelser på klienten.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

I händelse av redundans, kan aktören redundansväxla till en annan process eller en nod. Aktören-proxyn hanterar de aktiva prenumerationerna och igen prenumererar automatiskt dem. Du kan styra intervallet för ny prenumeration via den `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Om du vill avbryta prenumerationen, använder de `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Publicera händelser på aktören, när de visar. Om det finns prenumeranter på händelsen, skickar aktörer runtime dem meddelandet.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Nästa steg
* [Återinträde av aktör](service-fabric-reliable-actors-reentrancy.md)
* [Aktören diagnostik och övervakning av programprestanda](service-fabric-reliable-actors-diagnostics.md)
* [Aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-exempelkoden](https://github.com/Azure-Samples/service-fabric-java-getting-started)
