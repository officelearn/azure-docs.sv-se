---
title: Säkerhetskopiera och återställa Azure Service Fabric-aktörer
description: Lär dig hur du implementerar säkerhets kopiering och återställning i Azure Service Fabric-aktörer.
author: vturecek
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vturecek
ms.openlocfilehash: 41ba3f9c7d362756b800005d0c140c23dd96caa6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75370467"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Implementera Reliable Actors säkerhets kopiering och återställning

> [!NOTE]
> Microsoft rekommenderar att du använder [Periodisk säkerhets kopiering och återställning](service-fabric-backuprestoreservice-quickstart-azurecluster.md) för att konfigurera data säkerhets kopiering av pålitliga tillstånds känsliga tjänster och Reliable Actors. 
> 

I följande exempel visar en anpassad aktörs tjänst en metod för att säkerhetskopiera aktörs data genom att dra nytta av den lyssnare för fjärr kommunikation som `ActorService`redan finns i:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

I det här exemplet `IMyActorService` är ett fjärr kommunikations kontrakt som implementeras `IService` (C#) `Service` och (Java) och implementeras sedan av `MyActorService`. Genom att lägga till det här fjärr kommunikations `IMyActorService` kontraktet är metoderna på nu också tillgängliga för en klient genom att skapa `ActorServiceProxy`en fjärrproxy via:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

Mer information om Reliable Actors finns i följande artiklar:
* [Hantering av aktörs tillstånd](service-fabric-reliable-actors-state-management.md)
* [Aktörs livs cykel och skräp insamling](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentation om aktörers API-referens](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET-exempel kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempel kod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
