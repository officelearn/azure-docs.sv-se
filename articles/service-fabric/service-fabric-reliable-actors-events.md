---
title: Händelser i aktören-baserad Azure mikrotjänster | Microsoft Docs
description: Introduktion till händelser för Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: ed920c8d4ff7254b19c6eef8f5961593bb56bacf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="actor-events"></a>Aktören händelser
Aktören händelser är ett sätt att skicka meddelanden för bästa prestanda från aktören till klienterna. Aktören händelser är utformade för aktören till klientkommunikation och bör inte användas för aktören – aktören kommunikation.

Följande kodavsnitt visar hur du använder aktören händelser i ditt program.

Definiera ett gränssnitt som beskrivs de händelser som publicerats av den. Det här gränssnittet måste härledas från den `IActorEvents` gränssnitt. Argument av metoder måste vara [data minimera serialiserbara](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metoderna måste returnera void, som händelse-meddelanden är ett sätt och bästa prestanda.

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
Deklarera händelser som publicerats av aktören i gränssnittet aktören.

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
Implementera händelsehanteraren på klienten.

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

Skapa en proxy för aktören som publicerar händelsen och prenumerera på händelser på klienten.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Vid växling vid fel, kan aktören växlas över till en annan process eller en nod. Aktören proxy hanterar de aktiva prenumerationerna och igen prenumererar automatiskt dem. Du kan styra intervallet för ny prenumerationen via den `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Om du vill avbryta prenumerationen, använder den `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Publicera händelser på aktören, när de görs. Om det finns prenumeranter att händelsen, skickas aktörer runtime meddelandet.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Nästa steg
* [Aktören återinträde](service-fabric-reliable-actors-reentrancy.md)
* [Aktören diagnostik- och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md)
* [Aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-kodexempel](http://github.com/Azure-Samples/service-fabric-java-getting-started)
