---
title: "Reliable Actors på Service Fabric | Microsoft Docs"
description: "Beskriver hur Reliable Actors ovanpå Reliable Services och använda funktioner i Service Fabric-plattformen."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: ee248cb656eeb54e259ff1adf45080a207b5a866
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hur Reliable Actors använder Service Fabric-plattformen
Den här artikeln förklarar hur Reliable Actors fungerar på Azure Service Fabric-plattformen. Reliable Actors som körs i ett ramverk som är värd för en implementering av en tillståndskänslig tillförlitlig tjänst kallas den *aktören tjänsten*. Tjänsten aktören innehåller alla komponenter som krävs för att hantera livscykeln och meddelandet sändning för din aktörer:

* Aktören Runtime hanterar livscykeln skräpinsamling, och tillämpar Enkeltrådig åtkomst.
* En aktören service fjärrkommunikation lyssnare accepterar fjärråtkomst anrop till aktörer och skickar dem till en dispatcher att dirigera till lämplig aktören-instans.
* Aktören Tillståndsprovidern radbryts tillståndsprovidrar (till exempel tillståndsprovidern tillförlitliga samlingar) och ger ett nätverkskort för hantering av aktören tillstånd.

De här komponenterna utgör tillsammans tillförlitliga aktören ramen.

## <a name="service-layering"></a>Tjänsten lager
Eftersom tjänsten aktören är en tillförlitlig tjänst alla de [programmodell](service-fabric-application-model.md), livscykel, [paketering](service-fabric-package-apps.md), [distribution](service-fabric-deploy-remove-applications.md), uppgradera och skalning begreppet Reliable Services tillämpas på samma sätt i aktörstjänster.

![Aktören service lager][1]

Föregående diagram visar relationen mellan Service Fabric-programramverk och användarkod. Blå element representerar Reliable Services application framework, orange representerar tillförlitliga aktören framework och grönt representerar användarkod.

I Reliable Services tjänsten ärver den `StatefulService` klass. Den här klassen är härledd från `StatefulServiceBase` (eller `StatelessService` för tillståndslösa tjänster). I Reliable Actors använder du tjänsten aktören. Aktören-tjänsten är en annan implementering av den `StatefulServiceBase` klassen som implementerar aktören mönstret där din aktörer kör. Eftersom tjänsten aktören är bara en implementering av `StatefulServiceBase`, du kan skriva en egen tjänst som härleds från `ActorService` och implementera servicenivå funktioner på samma sätt som vid arv `StatefulService`, som:

* Tjänsten säkerhetskopiering och återställning.
* Delade funktioner för alla aktörer, till exempel strömbrytare.
* RPC-anrop på tjänsten aktören och på varje enskild aktören.

### <a name="using-the-actor-service"></a>Med hjälp av tjänsten aktören
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

### <a name="actor-service-methods"></a>Aktören Webbtjänstmetoder
Aktören tjänsten implementerar `IActorService` (C#) eller `ActorService` (Java), som i sin tur implementerar `IService` (C#) eller `Service` (Java). Detta är det gränssnitt som används av Reliable Services fjärrkommunikation, vilket gör att RPC-anrop på Webbtjänstmetoder. Den innehåller servicenivå metoder som kan anropas via fjärranslutning via tjänsten fjärrkommunikation.

#### <a name="enumerating-actors"></a>Räknar upp aktörer
Tjänsten aktören kan en klient att räkna upp metadata om aktörer som är värd för tjänsten. Eftersom tjänsten aktören är en partitionerad tillståndskänslig service, utförs uppräkningen per partition. Eftersom varje partition kan innehålla många aktörer, returneras uppräkningen som en uppsättning växlingsbara resultat. Sidorna upprepas över tills alla sidor är skrivskyddade. I följande exempel visas hur du skapar en lista över alla aktiva aktörer i en partition av aktören tjänster:

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

#### <a name="deleting-actors"></a>Om du tar bort aktörer
Tjänsten aktören innehåller också en funktion för att ta bort aktörer:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Mer information om att radera aktörer och deras tillstånd finns det [aktören livscykel dokumentationen](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Anpassade aktören service
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

#### <a name="implementing-actor-backup-and-restore"></a>Implementera aktören säkerhetskopiering och återställning
 I följande exempel anpassade aktören tjänsten Exponerar en metod för att säkerhetskopiera aktören data genom att utnyttja fjärrkommunikation lyssnaren finns redan i `ActorService`:

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


I det här exemplet `IMyActorService` är ett kontrakt för fjärrkommunikation som implementerar `IService` (C#) och `Service` (Java) och sedan implementeras av `MyActorService`. Genom att lägga till avtalet remoting, metoder på `IMyActorService` är nu också tillgängliga för en klient genom att skapa en fjärrproxy via `ActorServiceProxy`:

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

## <a name="application-model"></a>Programmodell
Aktören tjänsterna är tillförlitliga, så att programmet modellen är samma. Dock generera aktören framework build-verktyg några av modellen programfilerna för dig.

### <a name="service-manifest"></a>Tjänstmanifestet
Aktören framework build-verktyg generera automatiskt innehållet i aktören tjänstens ServiceManifest.xml fil. Den här filen innehåller:

* Aktören tjänsttyp. Typnamnet genereras baserat på din aktören projektnamn. Baserat på attributet beständiga på din aktören ställs flaggan HasPersistedState också in därefter.
* Kodpaketet.
* Konfigurationspaketet.
* Resurser och slutpunkter.

### <a name="application-manifest"></a>Applikationsmanifestet
Aktören framework build-verktyg skapa automatiskt en standard service definition för aktören tjänsten. Build-verktyg fylla standardegenskaperna för tjänsten:

* Ange replikantalet bestäms av beständiga attribut i din aktören. Varje gång beständiga attribut i din aktören ändras återställs uppsättningen replikantalet i tjänstdefinitionen standard därför.
* Partitionsschemat och intervallet är inställda Uniform Int64 med fullständig viktiga Int64-intervall.

## <a name="service-fabric-partition-concepts-for-actors"></a>Service Fabric-partition begrepp för aktörer
Aktörstjänster är partitionerad tillståndskänsliga tjänster. Varje partition för en tjänst aktören innehåller en uppsättning aktörer. Tjänsten partitioner distribueras automatiskt över flera noder i Service Fabric. Aktören instanser distribueras som ett resultat.

![Aktören partitionering och distribution][5]

Reliable Services kan skapas med annan partitionsscheman och partition nyckelintervall. Tjänsten aktören använder Int64 partitioneringsschema med fullständig viktiga Int64-intervall för att mappa aktörer till partitioner.

### <a name="actor-id"></a>Aktörs-ID
Varje aktören som skapas i tjänsten har ett unikt ID som är associerade med den representeras av den `ActorId` klass. `ActorId` är ett täckande ID-värde som kan användas för jämn fördelning av aktörer över partitioner för tjänsten genom att generera slumpmässigt ID: N:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Varje `ActorId` hash-kodas till en Int64. Det är därför aktören tjänsten måste använda ett Int64 partitioneringsschema med fullständig viktiga Int64-intervall. Dock anpassade ID-värden kan användas för en `ActorID`, inklusive GUID/UUID: er, strängar och Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

När du använder GUID/UUID: er och strängar värdena-kodas till en Int64. Men när du uttryckligen ger Int64 till en `ActorId`, Int64 mappas direkt till en partition utan ytterligare hash. Du kan använda den här tekniken för att styra vilka partition aktörer placeras i.

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
