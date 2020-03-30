---
title: Implementera funktioner i Azure Service Fabric-aktörer
description: Beskriver hur du skriver en egen aktörstjänst som implementerar funktioner på tjänstnivå på samma sätt som när du ärver StatefulService.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 55ee4c7498dcda3060d4e4221711793b80132bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502279"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementera funktioner på tjänstnivå i aktörstjänsten

Som beskrivs i [tjänsten skiktning](service-fabric-reliable-actors-platform.md#service-layering), aktören tjänsten själv är en pålitlig tjänst. Du kan skriva din egen `ActorService`tjänst som härstammar från . Du kan också implementera funktioner på tjänstnivå på samma sätt som när du ärver en tillståndskänslig tjänst, till exempel:

- Säkerhetskopiering och återställning av tjänsten.
- Delad funktionalitet för alla aktörer, till exempel en kretsbrytare.
- Fjärrproceduranrop på aktörstjänsten själv och på varje enskild aktör.

## <a name="use-the-actor-service"></a>Använda aktörstjänsten

Aktörsinstanser har åtkomst till aktörstjänsten där de körs. Genom aktörstjänsten kan aktörsinstanser programmässigt hämta servicekontexten. Tjänstkontexten har partitions-ID, tjänstnamn, programnamn och annan Azure Service Fabric-plattformsspecifik information.

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

Liksom alla tillförlitliga tjänster måste aktörstjänsten vara registrerad med en tjänsttyp i Service Fabric-körningen. För att aktörstjänsten ska kunna köra aktörsinstanserna måste aktörstypen också vara registrerad hos aktörstjänsten. `ActorRuntime`-registreringsmetoden gör det här jobbet för aktörerna. I det enklaste fallet kan du registrera aktörstypen och aktörstjänsten använder sedan standardinställningarna.

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

Alternativt kan du använda en lambda som tillhandahålls av registreringsmetoden för att konstruera aktörstjänsten själv. Du kan sedan konfigurera aktörstjänsten och uttryckligen konstruera aktörsinstanserna. Du kan injicera beroenden till din aktör genom dess konstruktor.

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

## <a name="actor-service-methods"></a>Metoder för aktörstjänst

`IActorService` Aktörstjänsten implementerar (C#) `ActorService` eller (Java), `IService` som i sin `Service` tur implementerar (C#) eller (Java). Det här gränssnittet används av Reliable Services remoting, vilket möjliggör fjärrproceduranrop på tjänstmetoder. Den innehåller metoder på servicenivå som kan anropas på distans via tjänståterbes här. Du kan använda den för att [räkna upp](service-fabric-reliable-actors-enumerate.md) och [ta bort](service-fabric-reliable-actors-delete-actors.md) aktörer.


## <a name="custom-actor-service"></a>Anpassad aktörstjänst

Genom att använda aktörsregistreringen lambda kan du registrera `ActorService` din egen anpassade `FabricActorService` aktörstjänst som kommer från (C#) och (Java). Du kan sedan implementera din egen servicenivåfunktion genom att `ActorService` skriva en `FabricActorService` tjänstklass som ärver (C#) eller (Java). En anpassad aktörstjänst ärver alla aktörskörningsfunktioner `ActorService` `FabricActorService` från (C#) eller (Java). Den kan användas för att implementera dina egna servicemetoder.

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

## <a name="implement-actor-backup-and-restore"></a>Implementera säkerhetskopiering och återställning av aktör

En anpassad aktörstjänst kan visa en metod för att säkerhetskopiera aktörsdata `ActorService`genom att dra nytta av den remoting-lyssnare som redan finns i . Ett exempel finns i [Skådespelarier För säkerhetskopiering och återställning](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Aktör som använder en remoting V2 (gränssnittskompatibel) stack

Den remoting V2 (gränssnitt kompatibel, känd som V2_1) stacken har alla funktioner i V2 remoting stack. Dess gränssnitt är kompatibelt med remoting V1 stacken, men det är inte bakåtkompatibel med V2 och V1. Om du vill uppgradera från V1 till V2_1 utan effekter på tjänstens tillgänglighet följer du stegen i nästa avsnitt.

Följande ändringar krävs för att använda remoting V2_1 stacken:

1. Lägg till följande sammansättningsattribut på aktörsgränssnitt.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Skapa och uppgradera aktörstjänst- och aktörsklientprojekt för att börja använda V2-stacken.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Uppgradering av aktörstjänsten till ommoting V2-stack (gränssnittskompatibel) utan att påverka tjänstens tillgänglighet

Den här ändringen är en uppgradering i två steg. Följ stegen i den här sekvensen.

1. Lägg till följande sammansättningsattribut på aktörsgränssnitt. Det här attributet startar två lyssnare för aktörstjänsten, V1 (befintlig) och V2_1 lyssnaren. Uppgradera aktörstjänsten med den här ändringen.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Uppgradera aktörsklienterna när du har slutfört den tidigare uppgraderingen.
   Det här steget ser till att aktörsproxyn använder den remoting V2_1 stacken.

3. Det här steget är valfritt. Ändra föregående attribut för att ta bort V1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Aktör som använder v2-stacken för remoting

Med version 2.8 NuGet paketet kan användarna nu använda remoting V2 stacken, som presterar bättre och ger funktioner som anpassad serialisering. Remoting V2 är inte bakåtkompatibel med den befintliga remoting stacken (kallas nu V1 remoting stack).

Följande ändringar krävs för att använda remoting V2 stacken.

1. Lägg till följande sammansättningsattribut på aktörsgränssnitt.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Skapa och uppgradera aktörstjänsten och aktörsklientprojekten för att börja använda V2-stacken.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Uppgradera aktörstjänsten till v2-stacken för remoting utan att påverka tjänstens tillgänglighet

Den här ändringen är en uppgradering i två steg. Följ stegen i den här sekvensen.

1. Lägg till följande sammansättningsattribut på aktörsgränssnitt. Det här attributet startar två lyssnare för aktörstjänsten, V1 (befintlig) och V2-lyssnaren. Uppgradera aktörstjänsten med den här ändringen.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Uppgradera aktörsklienterna när du har slutfört den tidigare uppgraderingen.
   Det här steget ser till att aktörsproxyn använder v2-stacken för remoting.

3. Det här steget är valfritt. Ändra föregående attribut för att ta bort V1-lyssnaren.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Nästa steg

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
