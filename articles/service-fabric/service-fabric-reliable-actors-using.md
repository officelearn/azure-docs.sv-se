---
title: Implementera funktioner i Azure Service Fabric aktörer | Microsoft Docs
description: Beskriver hur du skriver egna aktören-tjänst som implementerar servicenivå funktioner på samma sätt som vid arv StatefulService.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 41548c3395fa0c8f56e62cfcfb7338a2d53f040f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212899"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementering av servicenivå funktioner i din aktören-tjänst
Enligt beskrivningen i [service Skikta](service-fabric-reliable-actors-platform.md#service-layering), tjänsten aktören är en tillförlitlig tjänst.  Du kan skriva en egen tjänst som härleds från `ActorService` och implementera servicenivå funktioner på samma sätt som vid arv StatefulService, som:

- Tjänsten säkerhetskopiering och återställning.
- Delade funktioner för alla aktörer, till exempel strömbrytare.
- RPC-anrop på tjänsten aktören och på varje enskild aktören.

## <a name="using-the-actor-service"></a>Med hjälp av tjänsten aktören
Aktören instanser har åtkomst till tjänsten aktören de körs. Via tjänsten aktören hämta aktören instanser programmässigt kontext för tjänster. Tjänsten kontexten har partitions-ID, namn, programnamn och andra plattformsspecifika Service Fabric-information:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Precis som alla Reliable Services måste tjänsten aktören registreras med en typ i Service Fabric-körningsmiljön. För tjänsten aktören att köra aktören-instanser måste aktören-typen registreras med tjänsten aktören. `ActorRuntime`-registreringsmetoden gör det här jobbet för aktörerna. Du kan registrera aktörstyp i det enklaste fallet och aktören tjänsten med standardinställningarna används implicit:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Du kan också använda ett lambda-uttryck som tillhandahålls av metoden för registrering för att konstruera tjänsten aktören själv. Du kan konfigurera tjänsten aktören och uttryckligen skapa dina aktören instanser, där du kan mata in beroenden din aktören via sin konstruktor:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Aktören Webbtjänstmetoder
Aktören tjänsten implementerar `IActorService` (C#) eller `ActorService` (Java), som i sin tur implementerar `IService` (C#) eller `Service` (Java). Detta är det gränssnitt som används av Reliable Services fjärrkommunikation, vilket gör att RPC-anrop på Webbtjänstmetoder. Den innehåller servicenivå metoder som kan anropas via fjärranslutning via tjänsten fjärrkommunikation och du kan [räkna upp](service-fabric-reliable-actors-enumerate.md) och [ta bort](service-fabric-reliable-actors-delete-actors.md) aktörer.


## <a name="custom-actor-service"></a>Anpassade aktören service
Du kan registrera dina egna anpassade aktören-tjänst som härleds från med hjälp av aktören registrering lambda `ActorService` (C#) och `FabricActorService` (Java). I den här anpassade aktören-tjänsten du kan implementera din egen servicenivå funktioner genom att skriva en service-klass som ärver `ActorService` (C#) eller `FabricActorService` (Java). En anpassad aktören tjänst ärver alla aktören runtime funktioner från `ActorService` (C#) eller `FabricActorService` (Java) och kan användas för att implementera din egen Webbtjänstmetoder.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>Implementera aktören säkerhetskopiering och återställning
En anpassad aktören tjänst kan exponera en metod för att säkerhetskopiera aktören data genom att utnyttja fjärrkommunikation lyssnaren finns redan i `ActorService`.  Ett exempel finns [säkerhetskopiering och återställning aktörer](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2-stack"></a>Aktören med fjärrkommunikation V2 Stack
Användare kan nu använda fjärrkommunikation V2-stacken, vilket är mer performant och ger funktioner som anpassad serialisering med 2,8 nuget-paketet. Fjärrkommunikation V2 är inte bakåtkompatibla med befintliga fjärrkommunikation stack (vi ringer upp dig nu den som V1 fjärrkommunikation stacken).

Följande ändringar krävs för att använda fjärrkommunikation V2-stacken.
 1. Lägg till följande attribut för sammansättningen på aktören gränssnitt.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Bygg- och uppgradera ActorService aktören klienten och projekt att använda V2-stacken.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Aktören uppgradera tjänsten till fjärrkommunikation V2 stacken utan att påverka tillgängligheten för tjänsten.
Den här ändringen kommer att vara en 2-steg-uppgradering. Följ stegen i samma ordning som anges.

1.  Lägg till följande attribut för sammansättningen på aktören gränssnitt. Detta attribut kommer att starta två lyssnare för ActorService V1 (befintlig) och V2-lyssnare. Uppgradera ActorService med den här ändringen.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Uppgradera ActorClients när du har uppgraderat ovan.
Det här steget säkerställer aktören Proxy använder fjärrkommunikation V2-stacken.

3. Det här steget är valfritt. Ändra attributet ovan om du vill ta bort V1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Nästa steg
* [Aktören tillståndshantering](service-fabric-reliable-actors-state-management.md)
* [Aktören livscykel och skräp samling](service-fabric-reliable-actors-lifecycle.md)
* [Aktörer API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exempelkod för .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkod](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
