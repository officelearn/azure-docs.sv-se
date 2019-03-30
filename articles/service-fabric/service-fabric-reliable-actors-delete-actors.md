---
title: Ta bort Azure Service Fabric actors | Microsoft Docs
description: Lär dig hur du manuellt ta bort Service Fabric tillförlitliga aktörer och deras tillstånd.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: e297a6f42774f29e2eca4a410b695d5bbb636300
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666978"
---
# <a name="delete-reliable-actors-and-their-state"></a>Ta bort tillförlitliga aktörer och deras tillstånd
Skräpinsamling för inaktiverad aktörer rensar endast aktörobjekt, men tas inte bort data som lagras i en aktör State Manager. När en aktör återaktiveras få dess data igen tillgång till den via State Manager. I fall där aktörer lagra data i State Manager och är inaktiverad men aldrig återaktiverats, kan det vara nödvändigt att rensa sina data.

Den [Aktörstjänsten](service-fabric-reliable-actors-platform.md) innehåller en funktion för att ta bort aktörer från en fjärransluten anropare:

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

Tar bort en aktör ger följande effekter beroende på om huruvida aktören för närvarande är aktiv:

* **Active Actor**
  * Aktören tas bort från listan active aktörer och har inaktiverats.
  * Tillståndet tas bort permanent.
* **Inaktiva aktör**
  * Tillståndet tas bort permanent.

Det går inte att anropa en aktör ta bort på själva från någon av dess aktören metoder eftersom aktören inte kan tas bort vid körning i en aktör anrop-kontext där körningen har erhållit ett lås runt aktören anropet till tillämpa entrådiga åtkomst.

Mer information om Reliable Actors, läsa följande:
* [Aktören timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
* [Aktören händelser](service-fabric-reliable-actors-events.md)
* [Återinträde av aktör](service-fabric-reliable-actors-reentrancy.md)
* [Aktören diagnostik och övervakning av programprestanda](service-fabric-reliable-actors-diagnostics.md)
* [Aktören API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkoden](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
