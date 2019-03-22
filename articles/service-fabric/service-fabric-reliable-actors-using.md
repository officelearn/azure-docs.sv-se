---
title: Implementera funktioner i Azure Service Fabric actors | Microsoft Docs
description: Beskriver hur du skriver din egen actor-tjänst som implementerar funktioner på servicenivå på samma sätt som när du tar över StatefulService.
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
ms.openlocfilehash: 5ab967cbd630447132300b22da5c5deb31fd50e9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852373"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementera funktioner på servicenivå i actor-tjänst

Mer information finns i [service lagren](service-fabric-reliable-actors-platform.md#service-layering), aktörstjänsten själva är en tillförlitlig tjänst. Du kan skriva din egen tjänst som härleds från `ActorService`. Du kan också implementera funktioner på servicenivå på samma sätt som när du tar en tillståndskänslig tjänst, till exempel:

- Tjänsten säkerhetskopiering och återställning.
- Delade funktioner för alla aktörer, till exempel en kretsbrytare.
- RPC-anrop på aktörstjänsten själva och på varje enskild skådespelare.

## <a name="use-the-actor-service"></a>Använd actor-tjänst

Aktörsinstanser har åtkomst till aktörstjänsten där de körs. Aktörsinstanser kan programmässigt hämta tjänstkontext via actor-tjänst. Tjänstkontext har partitions-ID, namn, programnamn och annan information för Azure Service Fabric-plattformsspecifika.

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

Precis som alla tillförlitliga tjänster måste aktörstjänsten registreras med en tjänsttyp i Service Fabric-körningen. För aktörstjänsten ska kunna köra dina aktörsinstanser måste din aktörstypen också registreras hos aktörstjänsten. `ActorRuntime`-registreringsmetoden gör det här jobbet för aktörerna. Du kan registrera din typ av aktör i det enklaste fallet och aktörstjänsten sedan använder standardinställningarna.

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

Du kan också använda en lambda som tillhandahålls av metoden för registrering för att konstruera aktörstjänsten själv. Sedan kan du konfigurera aktörstjänsten och uttryckligen skapa dina aktörsinstanser. Du kan mata in beroenden till aktören via dess konstruktor.

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

## <a name="actor-service-methods"></a>Aktören service-metoder

Aktören tjänsten implementerar `IActorService` (C#) eller `ActorService` (Java), vilket i sin tur implementerar `IService` (C#) eller `Service` (Java). Det här gränssnittet används av Reliable Services-fjärrkommunikation, vilket gör att RPC-anrop på Webbtjänstmetoder. Den innehåller tjänstnivå metoder som kan anropas via fjärranslutning via fjärrtjänst. Du kan använda den för att [räkna upp](service-fabric-reliable-actors-enumerate.md) och [ta bort](service-fabric-reliable-actors-delete-actors.md) aktörer.


## <a name="custom-actor-service"></a>Anpassade actor-tjänst

Med hjälp av aktör registrering lambda, kan du registrera dina egna anpassade actor-tjänst som härleds från `ActorService` (C#) och `FabricActorService` (Java). Du kan sedan implementera dina egna tjänstnivå funktioner genom att skriva en tjänsteklass som ärver `ActorService` (C#) eller `FabricActorService` (Java). En anpassad aktörstjänsten ärver alla actor runtime funktioner från `ActorService` (C#) eller `FabricActorService` (Java). Det kan användas för att implementera en egen tjänstmetoder.

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

## <a name="implement-actor-backup-and-restore"></a>Implementera aktören säkerhetskopiering och återställning

En anpassad actor-tjänst kan exponera en metod för att säkerhetskopiera aktören data genom att utnyttja fjärrkommunikation lyssnaren finns redan i `ActorService`. Ett exempel finns i [säkerhetskopiering och återställning aktörer](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Aktör som använder en stack för fjärrkommunikation V2 (interface-kompatibelt)

Remoting V2 (gränssnitt kompatibel, kallas V2_1) stack har alla funktioner i V2 fjärrkommunikation stack. Dess gränssnitt är kompatibel med V1-stack fjärrkommunikation, men det är inte bakåtkompatibla med V2 och V1. Om du vill uppgradera från V1 till V2_1 med ingen inverkan på tjänstens tillgänglighet, följer du stegen i nästa avsnitt.

Följande ändringar krävs för att använda fjärrkommunikation V2_1 stack:

1. Lägg till följande attribut i sammansättningen på aktörsgränssnitt.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Bygg och uppgradera actor-tjänst och aktörsprojekt för klienten att börja använda V2-stack.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Aktören tjänsteuppgraderingen till remoting V2 (interface-kompatibelt) stack utan att påverka tjänsttillgängligheten

Den här ändringen är en uppgradering för tvåstegsverifiering. Följ stegen i sekvensen.

1. Lägg till följande attribut i sammansättningen på aktörsgränssnitt. Det här attributet startar två lyssnare för aktörstjänsten V1 (befintlig) och V2_1 lyssnaren. Uppgradera aktörstjänsten med den här ändringen.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Uppgradera klienterna aktören när du har slutfört den tidigare uppgraderingen.
   Det här steget säkerställer att aktören-proxyn använder fjärrkommunikation V2_1 stack.

3. Det här steget är valfritt. Ändra attributet tidigare för att ta bort V1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Aktör som använder remoting V2-stack

Användare kan nu använda remoting V2 stack, som utför bättre och tillhandahåller funktioner som anpassad serialisering med version 2.8 NuGet-paketet. Remoting V2 är inte bakåtkompatibla med befintliga fjärrkommunikation stack (kallas nu V1-fjärrkommunikation stack).

Följande ändringar krävs för att använda remoting V2-stacken.

1. Lägg till följande attribut i sammansättningen på aktörsgränssnitt.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Bygg och uppgradera actor-tjänst och aktörsprojekt för klienten att börja använda V2-stack.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Uppgradera aktörstjänsten till remoting V2-stacken utan att påverka tjänsttillgängligheten

Den här ändringen är en uppgradering för tvåstegsverifiering. Följ stegen i sekvensen.

1. Lägg till följande attribut i sammansättningen på aktörsgränssnitt. Det här attributet startar två lyssnare för aktörstjänsten V1 (befintlig) och V2-lyssnare. Uppgradera aktörstjänsten med den här ändringen.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Uppgradera klienterna aktören när du har slutfört den tidigare uppgraderingen.
   Det här steget säkerställer att aktören-proxyn använder remoting V2-stack.

3. Det här steget är valfritt. Ändra attributet tidigare för att ta bort V1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
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
