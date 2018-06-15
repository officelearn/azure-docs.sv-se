---
title: Tillförlitlig kommunikation översikt över | Microsoft Docs
description: Översikt över Reliable Services kommunikation modellen, inklusive ingående lyssnare på tjänster, åtgärda slutpunkter och kommunikation mellan tjänster.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 62c81368b8a3129b42262cb99cf23a5021744c1b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210767"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Hur du använder Reliable Services kommunikationen API: er
Azure Service Fabric en plattform som är helt oberoende om kommunikation mellan tjänster. Alla protokoll och stackar är acceptabla, från UDP till HTTP. Är det upp till tjänsten utvecklaren kan välja hur tjänster ska kommunicera. Application framework Reliable Services tillhandahåller inbyggd kommunikation stackar samt API: er som du kan använda för att skapa anpassade kommunikationskomponenter.

## <a name="set-up-service-communication"></a>Konfigurera service-kommunikation
Reliable Services API används ett enkelt gränssnitt för kommunikation. Om du vill öppna en slutpunkt för din tjänst helt enkelt implementera gränssnittet:

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

Du kan sedan lägga till din kommunikation lyssnare implementering returneras i en åsidosättning för service-baserad klass-metoden.

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

I båda fallen måste returnera en samling av avlyssning. Detta gör tjänsten att lyssna på flera slutpunkter kan potentiellt med olika protokoll, med hjälp av flera lyssnare. Du kan till exempel ha en HTTP-lyssnare och en separat WebSocket-lyssnare. Varje lyssnare hämtar ett namn och den resulterande insamlingen av *name: adressen* par representeras som ett JSON-objekt när en klient begär lyssnande-adresser för en tjänstinstans eller en partition.

Åsidosättningen som returnerar en mängd ServiceInstanceListeners i en tillståndslös tjänst. En `ServiceInstanceListener` innehåller en funktion för att skapa en `ICommunicationListener(C#) / CommunicationListener(Java)` och ger den ett namn. Åsidosättningen som returnerar en mängd ServiceReplicaListeners för tillståndskänsliga tjänster. Detta är skiljer sig från motparten tillståndslös, eftersom en `ServiceReplicaListener` har ett alternativ för att öppna en `ICommunicationListener` på sekundära repliker. Inte bara kan du använda flera kommunikationslyssnarna i en tjänst, men du kan också ange vilka lyssnare acceptera begäranden på sekundära repliker och vilka som bara lyssna på primära repliker.

Till exempel kan du ha en ServiceRemotingListener som tar RPC-anrop endast på primära repliker och en andra, anpassade lyssnare som tar skrivskyddade begäranden på sekundära repliker över HTTP:

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

Slutligen beskrivs de slutpunkter som krävs för tjänsten i den [tjänstmanifestet](service-fabric-application-and-service-manifests.md) under avsnittet på slutpunkter.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Kommunikation-lyssnaren kan komma åt slutpunkten resurserna för den från den `CodePackageActivationContext` i den `ServiceContext`. Lyssnaren kan sedan starta lyssna efter begäranden när den öppnas.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Slutpunkten resurser som är gemensamma för hela tjänstepaketet och de tilldelas av Service Fabric när tjänstepaketet är aktiverad. Flera service repliker i samma ServiceHost kan dela samma port. Det innebär att kommunikation lyssnaren ska ha stöd för delning av port. Det rekommenderade sättet att göra detta är för kommunikation lyssnaren att använda partitions-ID och repliken/instansen när den skapar lyssna-adress.
>
>

### <a name="service-address-registration"></a>Adressen för tjänstregistrering
En systemtjänst kallas den *namngivningstjänst* körs på Service Fabric-kluster. Naming Service är en register för tjänster och de adresser som varje instans eller en replik av tjänsten lyssnar på. När den `OpenAsync(C#) / openAsync(Java)` metod för ett `ICommunicationListener(C#) / CommunicationListener(Java)` har slutförts, dess returnera värdet hämtar registrerade i Naming Service. Värdet som publiceras i Naming Service är en sträng vars värde kan vara något alls. Strängvärdet är klienter ser när de frågar efter en adress för tjänsten från Naming Service.

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

Service Fabric innehåller API: er som gör att klienter och andra tjänster kan sedan ber för den här adressen av tjänstnamn. Detta är viktigt eftersom serviceadressen inte är statisk. Tjänster flyttas i klustret för resursen belastningsutjämning och tillgänglighet. Det här är den mekanism som tillåter klienterna att lösa den lyssnande adressen för en tjänst.

> [!NOTE]
> En fullständig genomgång av hur du skriver en lyssnare för kommunikation finns [Service Fabric Web API-tjänster med OWIN värd själv](service-fabric-reliable-services-communication-webapi.md) för C#, men du kan skriva egna HTTP-serverimplementering för Java, se EchoServer program exempel på https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>Kommunicerar med en tjänst
API: et för tillförlitlig Services ger följande bibliotek om du vill skriva klienter som kommunicerar med tjänster.

### <a name="service-endpoint-resolution"></a>Tjänsten slutpunktsmappning
Det första steget att kommunikation med en tjänst är att lösa en slutpunktsadress av partition eller instans av tjänsten som du vill kommunicera. Den `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` verktyget klassen är en grundläggande primitiv som hjälper klienterna att fastställa slutpunkten för en tjänst vid körning. Med Service Fabric-terminologi process för att fastställa slutpunkten för en tjänst kallas den *tjänsten slutpunktsmappning*.

För att ansluta till tjänster i ett kluster, skapas ServicePartitionResolver med standardinställningar. Detta är den rekommenderade användningen för de flesta situationer:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

En ServicePartitionResolver kan skapas med en uppsättning klusterslutpunkter för gateway för att ansluta till tjänster i ett annat kluster. Observera att gateway-slutpunkter är bara olika slutpunkter för att ansluta till samma kluster. Exempel:

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

`FabricClient` är det objekt som används för att kommunicera med Service Fabric-klustret för olika hanteringsåtgärder på klustret. Detta är användbart när du vill ha mer kontroll över hur en partition DNS-matchare för tjänsten samverkar med klustret. `FabricClient` Utför cachelagring internt och är vanligtvis dyrt att skapa, så det är viktigt att återanvända `FabricClient` instanser så mycket som möjligt.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

En Lös metod används sedan för att hämta adressen till en tjänst eller en tjänst partition för partitionerade tjänster.

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

En tjänstadress kan lösas med enkelt en ServicePartitionResolver, men mer arbete krävs för att se till att matcha adressen kan användas på rätt sätt. Din klient behöver identifiera om anslutningsförsöket misslyckades på grund av ett tillfälligt fel och kan göras (t.ex. tjänsten flyttas eller inte tillgänglig för tillfället) eller ett permanent fel (t.ex. tjänsten har tagits bort eller den begärda resursen finns inte längre). En tjänstinstans eller repliker kan flytta runt från nod till noden när som helst av flera skäl. Tjänsten adressen matchas via ServicePartitionResolver kan vara inaktuella av den tid som din klientkod försöker ansluta. I så fall igen måste klienten matcha adressen igen. Att tillhandahålla den tidigare `ResolvedServicePartition` innebär att matcharen måste försöka igen i stället för att hämta en cachelagrad adress.

Normalt behöver klientkoden inte fungerar med ServicePartitionResolver direkt. Det skapas och skickas till kommunikation klienten fabriker i Reliable Services API. Produktionsanläggningarna använder matcharen internt för att generera ett objekt som kan användas för att kommunicera med tjänster.

### <a name="communication-clients-and-factories"></a>Klienter för kommunikation och fabriker
Kommunikation factory biblioteket implementerar ett typiskt försök mönster för hantering av fel som underlättar sprider på nytt anslutningar till löst slutpunkter. Fabriken biblioteket kan försök igen när du anger felhanterare.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` Definierar grundläggande gränssnitt som implementerats av en kommunikation klientfabrik som ger klienter som kan kommunicera med Service Fabric-tjänsten. Implementeringen av CommunicationClientFactory beror på stacken för kommunikation som används av Service Fabric-tjänsten där klienten vill kommunicera. Reliable Services API ger en `CommunicationClientFactoryBase<TCommunicationClient>`. Detta ger en grundläggande implementering av gränssnittet CommunicationClientFactory och utför uppgifter som är gemensamma för alla staplar för kommunikation. (Dessa uppgifter omfattar med en ServicePartitionResolver för att avgöra tjänstslutpunkten). Klienter implementera vanligtvis den abstrakta klassen CommunicationClientFactoryBase för att hantera logik som är specifik för kommunikation stacken.

Kommunikation klienten bara tar emot en adress och används för att ansluta till en tjänst. Klienten kan använda det protokoll som den vill.

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

Klient-fabrik är främst ansvarar för att skapa klienter för kommunikation. För klienter som inte har en beständig anslutning, till exempel en klient för HTTP-behöver fabriken bara skapa och returnera klienten. Andra protokoll som underhåller en beständig anslutning, till exempel vissa binära protokoll ska också verifieras av fabriken att avgöra om anslutningen behöver skapas på nytt.  

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

Slutligen ansvarar en undantagshanterare för att fastställa åtgärd att vidta när ett undantag inträffar. Undantag är indelade i **återförsökbart** och **icke återförsökbart**.

* **Ej återförsökbart** undantag bara hämta igen tillbaka till anroparen.
* **återförsökbart** undantag kategoriseras i **tillfälligt** och **inte är tillfällig**.
  * **Tillfälligt** undantag är de som bara kan göras utan att återlösa slutpunktsadress service. Dessa omfattar tillfälliga nätverksproblem eller tjänsten felsvar förutom de som indikerar att slutpunktsadressen som tjänsten inte finns.
  * **Icke-tillfälligt** undantag är de som kräver tjänsten slutpunktsadress lösas på nytt. Dessa inkluderar undantag som anger tjänstslutpunkten inte kunde nås, har som anger tjänsten flyttats till en annan nod.

Den `TryHandleException` gör ett beslut om ett angivet undantag. Om den **inte vet** vilka beslut att göra om ett undantag som den ska returnera **FALSKT**. Om den **känner** vilka beslut att fatta, det måste ange resultatet i enlighet med detta och returnera **SANT**.

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
### <a name="putting-it-all-together"></a>Alltihop
Med en `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, och `IExceptionHandler(C#) / ExceptionHandler(Java)` uppbyggd kring ett kommunikationsprotokoll en `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` ska radbrytas samtidigt och ger hantering av fel och tjänsten partition adress upplösning cirkel runt dessa komponenter.

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
* [RPC-anrop med Reliable Services fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md)
* [WCF-kommunikation med hjälp av Reliable Services](service-fabric-reliable-services-communication-wcf.md)
