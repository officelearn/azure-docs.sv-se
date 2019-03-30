---
title: Kommunikationsöversikt för tillförlitlig tjänster | Microsoft Docs
description: Översikt över Reliable Services kommunikation modellen, inklusive att öppna lyssnare på tjänster, åtgärda slutpunkter och kommunikation mellan tjänster.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 15b45cadc69830827952d87ffc2315b06b07b02c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663408"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Hur du använder API: er för Reliable Services-kommunikation
Azure Service Fabric som en plattform är helt oberoende om kommunikation mellan tjänster. Alla protokoll och stackar accepteras, från UDP till HTTP. Det är upp till tjänsten utvecklaren kan välja hur tjänster ska kommunicera. Reliable Services application framework tillhandahåller inbyggd kommunikation stackar samt API: er som du kan använda för att skapa dina anpassade kommunikationskomponenter.

## <a name="set-up-service-communication"></a>Konfigurera kommunikation
Reliable Services-API använder ett enkelt gränssnitt för kommunikation. Om du vill öppna en slutpunkt för din tjänst, helt enkelt implementera det här gränssnittet:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Du kan sedan lägga till implementeringen kommunikation lyssnare returneras i en tjänst-baserad klass metoden åsidosättning.

För tillståndslösa tjänster:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

För tillståndskänsliga tjänster:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

I båda fallen kan returnera en samling av avlyssning. På så sätt kan din tjänst att lyssna på flera slutpunkter kan potentiellt med olika protokoll, med hjälp av flera lyssnare. Du kan exempelvis ha en HTTP-lyssnare och en separat WebSocket-lyssnare. Varje lyssnare hämtar ett namn och den resulterande samlingen av *namn: adressen* par representeras som en JSON-objekt när en klient begär de lyssnande adresserna för en tjänstinstans eller en partition.

I en tillståndslös tjänst returnerar åsidosättningen en uppsättning ServiceInstanceListeners. En `ServiceInstanceListener` innehåller en funktion för att skapa en `ICommunicationListener(C#) / CommunicationListener(Java)` och ger den ett namn. Åsidosättningen returnerar en uppsättning ServiceReplicaListeners för tillståndskänsliga tjänster. Detta skiljer sig från motparten tillståndslösa, eftersom en `ServiceReplicaListener` har ett alternativ för att öppna en `ICommunicationListener` på sekundära repliker. Inte bara kan du använda flera kommunikationslyssnarna i en tjänst, men du kan också ange vilka lyssnare godkänner begäranden på sekundära repliker och vilka som lyssnar endast på primära repliker.

Exempelvis kan du ha en ServiceRemotingListener som tar RPC-anrop endast på primära repliker och en andra, anpassade lyssnare som tar Läs-begäranden på sekundära repliker över HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> När du skapar flera lyssnare för en tjänst, varje lyssnare **måste** ges ett unikt namn.
>
>

Slutligen beskriver de slutpunkter som krävs för tjänsten i den [tjänstmanifestet](service-fabric-application-and-service-manifests.md) under avsnittet på slutpunkter.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Kommunikationslyssnaren kan komma åt resurserna för slutpunkten för den från den `CodePackageActivationContext` i den `ServiceContext`. Lyssnaren kan sedan starta lyssna efter begäranden när den öppnas.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Slutpunkt för resurser som är gemensamma för hela tjänstpaketet och de tilldelas av Service Fabric när tjänstepaketet är aktiverad. Flera service repliker på samma ServiceHost kan dela samma port. Det innebär att kommunikationslyssnaren ska ha stöd för delning av port. Det rekommenderade sättet att göra detta avser kommunikationslyssnaren att använda partitions-ID: T och replik/instans-ID när den skapar listen-adress.
>
>

### <a name="service-address-registration"></a>Registrering av tjänst-adress
En system-tjänsten kallas den *Namngivningstjänsten* körs på Service Fabric-kluster. Namngivningstjänsten är en registrator för tjänster och sina adresser som varje instans eller en replik av tjänsten lyssnar på. När den `OpenAsync(C#) / openAsync(Java)` -metoden för en `ICommunicationListener(C#) / CommunicationListener(Java)` har slutförts, dess returnera värdet registreras i namngivning av tjänsten. Det returnera värdet som publiceras i Naming Service är en sträng vars värde kan vara något alls. Strängvärdet är klienter ser när de frågar efter en adress för tjänsten från namngivning av tjänsten.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric tillhandahåller API: er som gör att klienter och andra tjänster för att sedan ber du för den här adressen efter tjänstnamn. Detta är viktigt eftersom tjänstadressen inte är statiska. Tjänster flyttas i klustret för resursen belastningsutjämning och tillgänglighet. Det här är den mekanism som tillåter klienterna att lösa lyssnaradress för en tjänst.

> [!NOTE]
> En fullständig genomgång av hur du skriver en kommunikationslyssnaren Se [webb-API för Service Fabric-tjänster med egen värd för OWIN](service-fabric-reliable-services-communication-webapi.md) för C#, medan du kan skriva egna HTTP-serverimplementering för Java Se EchoServer exempel på https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Kommunicera med en tjänst
Reliable Services-API ger följande bibliotek för att skriva klienter som kommunicerar med tjänster.

### <a name="service-endpoint-resolution"></a>Service-slutpunktsmappning
Det första steget att kommunicera med en tjänst är att lösa en slutpunktsadress för den partition eller en instans av tjänsten som du vill kommunicera med. Den `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` verktyget klassen är en grundläggande primitiv hämtas som hjälper klienterna att fastställa slutpunkten för en tjänst vid körning. I Service Fabric-terminologi processen att avgöra slutpunkten för en tjänst kallas den *tjänsten slutpunktsmappning*.

För att ansluta till tjänster i ett kluster, kan ServicePartitionResolver skapas med standardinställningar. Det här är den rekommendera användningen av de flesta situationer:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

För att ansluta till tjänster i ett annat kluster, kan en ServicePartitionResolver skapas med en uppsättning gateway-klusterslutpunkter. Observera att gateway-slutpunkter är bara olika slutpunkter för att ansluta till samma kluster. Exempel:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Du kan också `ServicePartitionResolver` kan få en funktion för att skapa en `FabricClient` för intern användning:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` är det objekt som används för att kommunicera med Service Fabric-kluster för olika hanteringsåtgärder i klustret. Detta är användbart när du vill ha mer kontroll över hur en partition service-matchare interagerar med klustret. `FabricClient` Utför cachelagring internt och är vanligtvis dyra att skapa, så det är viktigt att återanvända `FabricClient` instanser så mycket som möjligt.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

En Lös metod används sedan för att hämta adressen för en tjänst eller en tjänstpartition för tjänster som är partitionerad.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

En tjänstadress kan lösas enkelt med hjälp av en ServicePartitionResolver, men mer arbete krävs för att se till att matcha adressen kan användas korrekt. Din klient behöver identifiera om anslutningsförsöket misslyckades på grund av ett tillfälligt fel och kan göras (t.ex. tjänsten flyttas eller är tillfälligt otillgänglig) eller ett permanent fel (t.ex. tjänsten har tagits bort eller den begärda resursen finns inte längre). En tjänstinstans eller repliker kan flytta runt från nod till nod när som helst av flera skäl. Tjänstadressen matchas via ServicePartitionResolver kan vara inaktuella med tiden klientkoden försöker ansluta. I så fall igen måste klienten matcha adressen igen. Att tillhandahålla den tidigare `ResolvedServicePartition` innebär att matcharen måste du försöka igen i stället för att bara hämta en cachelagrad adress.

Normalt behöver klientkoden inte fungerar med ServicePartitionResolver direkt. Den skapas och skickas till kommunikation klienten fabriker i Reliable Services-API. Matcharen använder internt i fabriker för att generera ett klientobjekt som kan användas för att kommunicera med tjänster.

### <a name="communication-clients-and-factories"></a>Klienter för kommunikation och fabriker
Kommunikation factory biblioteket implementerar en typisk återförsöksmönstret för hantering av fel som underlättar omförsök anslutningar till löst tjänstslutpunkter. Factory biblioteket innehåller återförsöksmekanismen medan du anger felhanterare.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` Definierar grundläggande gränssnittet implementeras av en klientfabrik för kommunikation som ger klienter som kan kommunicera med en Service Fabric-tjänst. Implementeringen av CommunicationClientFactory beror på kommunikationsstack som används av Service Fabric-tjänst där klienten vill kommunicera. API: et för Reliable Services tillhandahåller en `CommunicationClientFactoryBase<TCommunicationClient>`. Detta ger en grundläggande implementering av gränssnittet CommunicationClientFactory och utför uppgifter som är gemensamma för alla staplar för kommunikation. (Dessa kan till exempel använder en ServicePartitionResolver för att se tjänstslutpunkten). Klienter implementera vanligtvis den abstrakta klassen CommunicationClientFactoryBase för att hantera logik som är specifik för kommunikationsstack.

Kommunikation klienten bara tar emot en adress och används för att ansluta till en tjänst. Klienten kan använda de protokoll som företaget.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

Klientfabrik är primärt ansvarig för att skapa kommunikation klienter. För klienter som inte underhåller en beständig anslutning, till exempel en HTTP-klient, behöver fabriken bara skapa och returnera klienten. Andra protokoll som underhåller en beständig anslutning, till exempel vissa binära protokoll, bör också valideras av factory att avgöra om anslutningen måste skapas på nytt.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Slutligen ansvarar en undantagshanterare för att fastställa vilken åtgärd som ska vidtas när ett undantag inträffar. Undantag är indelade i **återförsökbart** och **icke-återförsöksbar**.

* **Icke-återförsöksbar** undantag bara hämta rethrown tillbaka till anroparen.
* **återförsökbart** undantag är ytterligare indelade i **tillfälliga** och **icke tillfälliga**.
  * **Tillfälliga** undantag är de som bara kan göras utan att lösa slutpunktsadress tjänsten igen. Dessa omfattar tillfälliga problem med nätverket eller tjänsten felsvar än de som indikerar slutpunktsadress tjänsten inte finns.
  * **Icke tillfälliga** undantagen är de som kräver tjänsten slutpunktsadress lösas på nytt. Dessa omfattar undantag som anger tjänsteslutpunkt inte kunde nås, har som anger tjänsten flyttats till en annan nod.

Den `TryHandleException` fattar ett beslut om ett visst undantag. Om den **inte vet** vilka beslut att göra om ett undantag, bör den returnera **FALSKT**. Om den **känner** vilka beslut att fatta, det måste ställa in resultatet i enlighet med detta och returnera **SANT**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Färdigställa allt
Med en `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, och `IExceptionHandler(C#) / ExceptionHandler(Java)` uppbyggd kring ett kommunikationsprotokoll en `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` omsluter det allt på samma plats och ger hantering av fel och tjänsten partition adress upplösning loopen omkring dessa komponenter.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Nästa steg
* [ASP.NET Core med Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [RPC-anrop med Reliable Services-fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md)
* [WCF-kommunikation med Reliable Services](service-fabric-reliable-services-communication-wcf.md)
