---
title: Ta bort Azure Service Fabric aktörer | Microsoft Docs
description: Lär dig hur du manuellt ta bort Service Fabric Reliable Actors och deras tillstånd.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: fa4fe018a9e6b32158f5bbd13c44ff57069cb1cf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="delete-reliable-actors-and-their-state"></a>Ta bort Reliable Actors och deras tillstånd
Skräpinsamling inaktiverade aktörer endast rensar aktören objektet, men det tar inte bort data som lagras i en aktör tillstånd Manager. När en aktör återaktiveras få data igen ska tillgång till den via hanteraren tillstånd. I fall där aktörer lagra data i Tillståndshanterare och är inaktiverad men aldrig återaktivera kan det vara nödvändigt att rensa sina data.

Den [aktören Service](service-fabric-reliable-actors-platform.md) innehåller en funktion för att ta bort aktörer från en fjärransluten anropare:

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

Om du tar bort en aktör ger följande effekter beroende på om huruvida aktören är för närvarande är aktiva:

* **Aktiva aktören**
  * Aktören tas bort från listan med aktiva aktörer och inaktiveras.
  * Dess tillstånd tas bort permanent.
* **Inaktiva aktören**
  * Dess tillstånd tas bort permanent.

Det går inte att anropa en aktör ta bort på sig själv från någon av dess metoder aktören eftersom aktören inte kan tas bort körs inom en aktören anrop-kontext där körningsmiljön har fått ett lås runt aktören anropet att tillämpa Enkeltrådig åtkomst.

Mer information om Reliable Actors läsa följande:
* [Aktören timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Aktören händelser](service-fabric-reliable-actors-events.md)
* [Aktören återinträde](service-fabric-reliable-actors-reentrancy.md)
* [Aktören diagnostik- och prestandaövervakning](service-fabric-reliable-actors-diagnostics.md)
* [Aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-kodexempel](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
