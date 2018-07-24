---
title: Implementering av funktioner i Azure Service Fabric actors | Microsoft Docs
description: Beskriver hur du skriver din egen actor-tjänst som implementerar funktioner på servicenivå på samma sätt som vid arv StatefulService.
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
ms.openlocfilehash: 6aff9e9599d31942f994f3cb4e5e9219f33dc7e1
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205528"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementera funktioner på servicenivå i actor-tjänst
Mer information finns i [service lagren](service-fabric-reliable-actors-platform.md#service-layering), aktörstjänsten själva är en tillförlitlig tjänst.  Du kan skriva din egen tjänst som härleds från `ActorService` och implementera funktioner på servicenivå på samma sätt som vid arv StatefulService, till exempel:

- Tjänsten säkerhetskopiering och återställning.
- Delade funktioner för alla aktörer, till exempel en kretsbrytare.
- RPC-anrop på aktörstjänsten själva och på varje enskild skådespelare.

## <a name="using-the-actor-service"></a>Med hjälp av aktörstjänsten
Aktörsinstanser har åtkomst till aktörstjänsten där de körs. Aktörsinstanser kan programmässigt hämta tjänstkontext via actor-tjänst. Tjänstkontext har partitions-ID, namn, programnamn och annan information för Service Fabric-plattformsspecifika:

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

Precis som alla tillförlitliga tjänster måste aktörstjänsten registreras med en tjänsttyp i Service Fabric-körningen. För aktörstjänsten ska kunna köra dina aktörsinstanser måste din aktörstypen registreras hos aktörstjänsten. `ActorRuntime`-registreringsmetoden gör det här jobbet för aktörerna. Du kan bara registrera din typ av aktör i det enklaste fallet och aktörstjänsten med standardinställningar används implicit:

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

Du kan också använda en lambda som tillhandahålls av metoden för registrering för att konstruera aktörstjänsten själv. Du kan sedan konfigurera aktörstjänsten och uttryckligen skapa dina aktörsinstanser, där du kan mata in beroenden aktören via dess konstruktorn:

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
Aktören tjänsten implementerar `IActorService` (C#) eller `ActorService` (Java), vilket i sin tur implementerar `IService` (C#) eller `Service` (Java). Detta är det gränssnitt som används av Reliable Services-fjärrkommunikation, vilket gör att RPC-anrop på Webbtjänstmetoder. Den innehåller tjänstnivå metoder som kan anropas via fjärranslutning via fjärrtjänst så att du kan [räkna upp](service-fabric-reliable-actors-enumerate.md) och [ta bort](service-fabric-reliable-actors-delete-actors.md) aktörer.


## <a name="custom-actor-service"></a>Anpassade actor-tjänst
Med hjälp av aktör registrering lambda, kan du registrera dina egna anpassade actor-tjänst som härleds från `ActorService` (C#) och `FabricActorService` (Java). I den här anpassade actor-tjänst du kan implementera dina egna tjänstnivå funktioner genom att skriva en tjänsteklass som ärver `ActorService` (C#) eller `FabricActorService` (Java). En anpassad aktörstjänsten ärver alla actor runtime funktioner från `ActorService` (C#) eller `FabricActorService` (Java) och kan användas för att implementera en egen tjänstmetoder.

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
En anpassad actor-tjänst kan exponera en metod för att säkerhetskopiera aktören data genom att utnyttja fjärrkommunikation lyssnaren finns redan i `ActorService`.  Ett exempel finns i [säkerhetskopiering och återställning aktörer](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2interfacecompatible-stack"></a>Använda fjärrkommunikation V2(InterfaceCompatible) Stack aktör
Remoting V2 (InterfaceCompatible aka V2_1) stack har alla funktioner i V2-fjärrkommunikation stack utöver det är gränssnittet kompatibla stack till fjärrkommunikation V1 stack men är inte bakåtkompatibla med V2 och V1. För att kunna genomföra uppgraderingen från V1 till V2_1 utan att påverka tjänsttillgängligheten, Följ nedanstående [artikeln](#actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability).

Följande ändringar krävs för att använda fjärrkommunikation V2_1 Stack.
 1. Lägg till följande attribut i sammansättningen på Aktörsgränssnitt.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

 2. Bygg- och uppgradera ActorService och aktören klienten projekt att börja använda V2-stacken.

#### <a name="actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability"></a>Aktören uppgradera tjänsten till fjärrkommunikation V2(InterfaceCompatible) Stack utan att påverka tjänsttillgängligheten.
Den här ändringen kommer att vara en 2-steg-uppgradering. Följ stegen i samma ordning som anges.

1.  Lägg till följande attribut i sammansättningen på Aktörsgränssnitt. Det här attributet startar två lyssnare för ActorService, V1 (befintlig) och V2_1 lyssnare. Uppgradera ActorService med den här ändringen.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
  ```

2. Uppgradera ActorClients när du har slutfört ovanstående uppgraderingen.
Det här steget gör att aktören-proxyn använder fjärrkommunikation V2_1 Stack.

3. Det här steget är valfritt. Ändra attributet ovan för att ta bort V1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-using-remoting-v2-stack"></a>Använda fjärrkommunikation V2 Stack aktör
Användare kan nu använda Remoting V2 stack, som är bättre och innehåller funktioner som anpassade serialisering 2,8 nuget-paketet. Remoting V2 är inte bakåtkompatibla med befintliga fjärrkommunikation stack (vi ringer upp dig nu den som V1-fjärrkommunikation stack).

Följande ändringar krävs för att använda Remoting V2-stacken.
 1. Lägg till följande attribut i sammansättningen på Aktörsgränssnitt.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

 2. Bygg- och uppgradera ActorService och aktören klienten projekt att börja använda V2-stacken.

#### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Aktören uppgradera tjänsten till Remoting V2 Stack utan att påverka tjänsttillgängligheten.
Den här ändringen kommer att vara en 2-steg-uppgradering. Följ stegen i samma ordning som anges.

1.  Lägg till följande attribut i sammansättningen på Aktörsgränssnitt. Det här attributet startar två lyssnare för ActorService, V1 (befintlig) och V2-lyssnare. Uppgradera ActorService med den här ändringen.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
  ```

2. Uppgradera ActorClients när du har slutfört ovanstående uppgraderingen.
Det här steget gör att aktören-proxyn använder Remoting V2 Stack.

3. Det här steget är valfritt. Ändra attributet ovan för att ta bort V1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Nästa steg
* [Aktören tillståndshantering](service-fabric-reliable-actors-state-management.md)
* [Aktör livscykel och skräpinsamling samling](service-fabric-reliable-actors-lifecycle.md)
* [Aktörer API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET-exempelkod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-exempelkoden](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
