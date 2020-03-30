---
title: Säkerhetskopiera och återställa Azure Service Fabric-aktörer
description: Lär dig hur du implementerar säkerhetskopiering och återställning i dina Azure Service Fabric-aktörer.
author: vturecek
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vturecek
ms.openlocfilehash: 41ba3f9c7d362756b800005d0c140c23dd96caa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370467"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Implementera reliable actors-säkerhetskopiering och återställning

> [!NOTE]
> Microsoft rekommenderar att du använder [Periodisk säkerhetskopiering och återställning](service-fabric-backuprestoreservice-quickstart-azurecluster.md) för att konfigurera säkerhetskopiering av tillförlitliga tillståndstjänster och tillförlitliga aktörer. 
> 

I följande exempel exponerar en anpassad aktörstjänst en metod för att säkerhetskopiera aktörsdata `ActorService`genom att dra nytta av den remoting-lyssnare som redan finns i :

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

I det `IMyActorService` här exemplet är ett remoting-kontrakt som implementerar `IService` (C#) och `Service` (Java) och sedan implementeras av `MyActorService`. Genom att lägga till detta `IMyActorService` remoting kontrakt, metoder på är nu `ActorServiceProxy`också tillgängliga för en klient genom att skapa en remoting proxy via:

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

För mer information om Reliable Actors, läs följande artiklar:
* [Hantering av aktörstillstånd](service-fabric-reliable-actors-state-management.md)
* [Skådespelarens livscykel och skräpinsamling](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentation för ACTORS API-referens](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [EXEMPELkod för .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkod](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
