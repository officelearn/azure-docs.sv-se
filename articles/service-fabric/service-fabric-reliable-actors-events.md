---
title: Händelser i aktörsbaserade Azure Service Fabric-aktörer
description: Lär dig mer om händelser för Service Fabric Reliable Actors, ett effektivt sätt att kommunicera mellan skådespelare och klient.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 73c149a0d0992fecd1acf633891057570285df64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639674"
---
# <a name="actor-events"></a>Skådespelare händelser
Aktörshändelser är ett sätt att skicka meddelanden om bästa möjliga insats från aktören till klienterna. Skådespelare händelser är utformade för skådespelare-till-klient kommunikation och bör inte användas för skådespelare-till-skådespelare kommunikation.

Följande kodavsnitt visar hur du använder aktörshändelser i programmet.

Definiera ett gränssnitt som beskriver de händelser som publiceras av aktören. Det här gränssnittet måste `IActorEvents` härledas från gränssnittet. Argumenten för metoderna måste vara [datakontrakt serialiserbara](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metoderna måste returnera ogiltiga, eftersom händelsemeddelanden är ett sätt och bästa möjliga ansträngning.

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
Deklarera de händelser som publiceras av aktören i aktörsgränssnittet.

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

Skapa en proxy till aktören som publicerar händelsen och prenumerera på dess händelser på klienten.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

I händelse av redundans kan aktören växla över till en annan process eller nod. Aktörsproxyn hanterar aktiva prenumerationer och prenumererar automatiskt på dem igen. Du kan styra omprenumerationsintervallet via API:et. `ActorProxyEventExtensions.SubscribeAsync<TEvent>` Om du vill avsluta `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` prenumerationen använder du API:et.

På skådespelaren, publicera händelserna när de inträffar. Om det finns prenumeranter på händelsen skickar skådespelarna körningen dem till meddelandet.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Nästa steg
* [Skådespelare reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Skådespelare diagnostik och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentation för aktörs-API-referens](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET-kärnasprovkod](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-exempelkod](https://github.com/Azure-Samples/service-fabric-java-getting-started)
