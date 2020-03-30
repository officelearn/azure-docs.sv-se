---
title: Ta bort Azure Service Fabric-aktörer
description: Lär dig hur du tar bort tillförlitliga aktörer och deras tillstånd manuellt och helt i ett Azure Service Fabric-program.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645624"
---
# <a name="delete-reliable-actors-and-their-state"></a>Ta bort Reliable Actors och deras tillstånd
Skräpsamling av inaktiverade aktörer rensar bara aktörsobjektet, men data som lagras i en aktörs tillståndshanterare tas inte bort. När en aktör återaktiveras görs dess data återigen tillgängliga för den via tillståndshanteraren. I de fall där aktörer lagrar data i State Manager och inaktiveras men aldrig återaktiveras kan det vara nödvändigt att rensa sina data.

[Aktörstjänsten](service-fabric-reliable-actors-platform.md) tillhandahåller en funktion för att ta bort aktörer från en fjärranrop:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Om du tar bort en aktör får du följande effekter beroende på om aktören för närvarande är aktiv eller inte:

* **Aktiv aktör**
  * Aktören tas bort från listan aktiva aktörer och inaktiveras.
  * Dess tillstånd tas bort permanent.
* **Inaktiv aktör**
  * Dess tillstånd tas bort permanent.

En aktör kan inte anropa ta bort på sig själv från en av dess aktörsmetoder eftersom aktören inte kan tas bort när den körs i en aktörsanropskontext, där körningen har fått ett lås runt aktörsanropet för att framtvinga åtkomst med en tråd.

Mer information om Reliable Actors finns i följande:
* [Aktörs timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Skådespelare händelser](service-fabric-reliable-actors-events.md)
* [Skådespelare reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Skådespelare diagnostik och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentation för aktörs-API-referens](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
