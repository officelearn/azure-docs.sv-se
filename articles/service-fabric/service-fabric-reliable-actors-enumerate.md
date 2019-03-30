---
title: Räkna upp aktörer på Azure Service Fabric | Microsoft Docs
description: Lär dig mer om att räkna upp tillförlitliga aktörer och deras metadata.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 04e2c32b18e6897d6443fea68587aba9ae294be5
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664509"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Räkna upp Service Fabric Reliable Actors
Reliable Actors-tjänst kan en klient att räkna upp metadata om aktörer som är värd för tjänsten. Eftersom aktörstjänsten är en partitionerad tillståndskänslig tjänst, utförs uppräkning per partition. Eftersom varje partition kan innehålla många aktörer, returneras uppräkningen av växlingsbara resultat. Sidorna upprepas över tills alla sidor är skrivskyddade. I följande exempel visar hur du skapar en lista över alla aktiva aktörer i en partition av en actor-tjänst:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Nästa steg
* [Aktören tillståndshantering](service-fabric-reliable-actors-state-management.md)
* [Aktör livscykel och skräpinsamling samling](service-fabric-reliable-actors-lifecycle.md)
* [Aktörer API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET-exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkoden](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
