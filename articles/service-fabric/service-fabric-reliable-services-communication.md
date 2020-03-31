---
title: Översikt över tillförlitlig kommunikation för tjänster
description: Översikt över kommunikationsmodellen för tillförlitliga tjänster, inklusive att öppna lyssnare på tjänster, lösa slutpunkter och kommunicera mellan tjänster.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 3c1a6cfa5227369bf1cde4af087019727c22c0c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462949"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Så här använder du kommunikations-API:erna för tillförlitliga tjänster
Azure Service Fabric som en plattform är helt agnostiker om kommunikation mellan tjänster. Alla protokoll och stackar är acceptabla, från UDP till HTTP. Det är upp till tjänstutvecklaren att välja hur tjänster ska kommunicera. Reliable Services-programramverket tillhandahåller inbyggda kommunikationsstackar samt API:er som du kan använda för att skapa dina anpassade kommunikationskomponenter.

## <a name="set-up-service-communication"></a>Konfigurera servicekommunikation
API:et för reliable services använder ett enkelt gränssnitt för tjänstkommunikation. Om du vill öppna en slutpunkt för tjänsten implementerar du bara det här gränssnittet:

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

Du kan sedan lägga till implementeringen av kommunikationsavlyssningsavlyssningsaren genom att returnera den i en tjänstbaserad klassmetod åsidosättning.

För statslösa tjänster:

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

I båda fallen returnerar du en samling lyssnare. På så sätt kan tjänsten lyssna på flera slutpunkter, eventuellt med hjälp av olika protokoll, med hjälp av flera lyssnare. Du kan till exempel ha en HTTP-lyssnare och en separat WebSocket-lyssnare. Varje lyssnare får ett namn och den resulterande *namnsamlingen: adresspar* representeras som ett JSON-objekt när en klient begär lyssningsadresser för en tjänstinstans eller en partition.

I en tillståndslös tjänst returnerar åsidosättningen en samling ServiceInstanceListeners. A `ServiceInstanceListener` innehåller en funktion `ICommunicationListener(C#) / CommunicationListener(Java)` för att skapa en och ger den ett namn. För tillståndskänsliga tjänster returnerar åsidosättningen en samling ServiceReplicaListeners. Detta skiljer sig något från dess `ServiceReplicaListener` tillståndslösa motsvarighet, `ICommunicationListener` eftersom en har en möjlighet att öppna en på sekundära repliker. Du kan inte bara använda flera kommunikationsavlyssnare i en tjänst, utan du kan också ange vilka lyssnare som accepterar begäranden på sekundära repliker och vilka som bara lyssnar på primära repliker.

Du kan till exempel ha en ServiceRemotingListener som endast tar RPC-anrop på primära repliker och en andra anpassad lyssnare som tar läsbegäranden på sekundära repliker via HTTP:

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
> När du skapar flera lyssnare för en tjänst **måste** varje lyssnare ges ett unikt namn.
>
>

Beskriv slutligen de slutpunkter som krävs för tjänsten i [tjänstmanifestet](service-fabric-application-and-service-manifests.md) under avsnittet på slutpunkter.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Kommunikationsavlyssnaren kan komma åt de `CodePackageActivationContext` slutpunktsresurser som tilldelats den från i `ServiceContext`. Lyssnaren kan sedan börja lyssna efter förfrågningar när den öppnas.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Slutpunktsresurser är gemensamma för hela servicepaketet och de allokeras av Service Fabric när servicepaketet aktiveras. Flera tjänstrepliker som finns i samma ServiceHost kan dela samma port. Detta innebär att kommunikationslyssnaren bör stödja hamndelning. Det rekommenderade sättet att göra detta är att kommunikationsavlyssnaren använder partitions-ID och replik-/instans-ID när den genererar lyssningsadressen.
>
>

### <a name="service-address-registration"></a>Registrering av serviceadress
En systemtjänst som kallas *namngivningstjänsten* körs på Service Fabric-kluster. Namngivningstjänsten är en registrator för tjänster och deras adresser som varje instans eller replik av tjänsten lyssnar på. När `OpenAsync(C#) / openAsync(Java)` metoden för `ICommunicationListener(C#) / CommunicationListener(Java)` en färdigställs registreras dess returvärde i namngivningstjänsten. Det här returvärdet som publiceras i namngivningstjänsten är en sträng vars värde kan vara vad som helst. Det här strängvärdet är vad klienter ser när de ber om en adress för tjänsten från namngivningstjänsten.

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

Service Fabric tillhandahåller API:er som gör att klienter och andra tjänster sedan kan be om den här adressen efter tjänstnamn. Detta är viktigt eftersom serviceadressen inte är statisk. Tjänster flyttas runt i klustret för resursbalansering och tillgänglighet. Det här är den mekanism som gör det möjligt för klienter att lösa lyssningsadressen för en tjänst.

> [!NOTE]
> En fullständig genomgång av hur du skriver en kommunikationsavlyssnare finns i [Service Fabric Web API-tjänster med OWIN självhosting](service-fabric-reliable-services-communication-webapi.md) för https://github.com/Azure-Samples/service-fabric-java-getting-startedC#, medan du för Java kan skriva din egen HTTP-serverimplementering finns i EchoServer-programexempel på .
>
>

## <a name="communicating-with-a-service"></a>Kommunicera med en tjänst
Api:et för tillförlitliga tjänster tillhandahåller följande bibliotek för att skriva klienter som kommunicerar med tjänster.

### <a name="service-endpoint-resolution"></a>Lösning av tjänstens slutpunkt
Det första steget till kommunikation med en tjänst är att lösa en slutpunktsadress för partitionen eller instansen av tjänsten som du vill prata med. Verktygsklassen `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` är en grundläggande primitiv som hjälper klienter att bestämma slutpunkten för en tjänst vid körning. I terminologin för Service Fabric kallas processen för att bestämma slutpunkten för en tjänst som *tjänstslutpunktsmatchning*.

Om du vill ansluta till tjänster i ett kluster kan ServicePartitionResolver skapas med standardinställningarna. Detta är den rekommenderade användningen för de flesta situationer:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Om du vill ansluta till tjänster i ett annat kluster kan en ServicePartitionResolver skapas med en uppsättning slutpunkter för klustergateway. Observera att gatewayslutpunkter är bara olika slutpunkter för anslutning till samma kluster. Ett exempel:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternativt `ServicePartitionResolver` kan ges en funktion för `FabricClient` att skapa en att använda internt:

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

`FabricClient`är det objekt som används för att kommunicera med servicetygsklustret för olika hanteringsåtgärder i klustret. Detta är användbart när du vill ha mer kontroll över hur en tjänstpartitionsmatchningsanordning interagerar med klustret. `FabricClient`utför cachelagring internt och är i allmänhet dyrt att skapa, `FabricClient` så det är viktigt att återanvända instanser så mycket som möjligt.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

En lösningsmetod används sedan för att hämta adressen till en tjänst eller en tjänstpartition för partitionerade tjänster.

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

En serviceadress kan enkelt lösas med hjälp av en ServicePartitionResolver, men mer arbete krävs för att säkerställa att den lösta adressen kan användas korrekt. Klienten måste identifiera om anslutningsförsöket misslyckades på grund av ett tillfälligt fel och kan göras om (t.ex. tjänsten flyttas eller är tillfälligt otillgänglig) eller ett permanent fel (t.ex. har tjänsten tagits bort eller den begärda resursen inte längre finns). Tjänstinstanser eller repliker kan flyttas från nod till nod när som helst av flera skäl. Serviceadressen som löses via ServicePartitionResolver kan vara inaktuell när klientkoden försöker ansluta. I så fall igen klienten måste åter lösa adressen. Om du `ResolvedServicePartition` anger det föregående anger att matcharen måste försöka igen i stället för att bara hämta en cachelagrad adress.

Klientkoden behöver vanligtvis inte fungera direkt med ServicePartitionResolver. Det skapas och vidarebefordras till kommunikation klientfabriker i Reliable Services API. Fabrikerna använder resolver internt för att generera ett klientobjekt som kan användas för att kommunicera med tjänster.

### <a name="communication-clients-and-factories"></a>Kommunikationskunder och fabriker
Kommunikationsfabriksbiblioteket implementerar ett typiskt mönster för felsökningsförsök som gör det enklare att försöka ansluta till lösta tjänstslutpunkter. Fabriksbiblioteket tillhandahåller återförsöksmekanismen medan du tillhandahåller felhanterare.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`definierar basgränssnittet som implementeras av en kommunikationsklientfabrik som producerar klienter som kan prata med en Service Fabric-tjänst. Implementeringen av CommunicationClientFactory beror på den kommunikationsstack som används av tjänsten Service Fabric där klienten vill kommunicera. API:et för `CommunicationClientFactoryBase<TCommunicationClient>`tillförlitliga tjänster tillhandahåller en . Detta ger en basimplementering av CommunicationClientFactory-gränssnittet och utför uppgifter som är gemensamma för alla kommunikationsstaplar. (Dessa uppgifter inkluderar att använda en ServicePartitionResolver för att bestämma tjänstens slutpunkt). Klienter implementerar vanligtvis klassen abstract CommunicationClientFactoryBase för att hantera logik som är specifik för kommunikationsstacken.

Kommunikationsklienten tar bara emot en adress och använder den för att ansluta till en tjänst. Klienten kan använda vilket protokoll den vill.

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

Kundfabriken är i första hand ansvarig för att skapa kommunikationsklienter. För klienter som inte har en beständig anslutning, till exempel en HTTP-klient, behöver fabriken bara skapa och returnera klienten. Andra protokoll som underhåller en beständig anslutning, till exempel vissa binära protokoll, bör också valideras av fabriken för att avgöra om anslutningen behöver återskapas.  

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

Slutligen är en undantagshanterare ansvarig för att avgöra vilka åtgärder som ska vidtas när ett undantag inträffar. Undantag kategoriseras i **återförsöksbara** och **kan inte återanvändas**.

* **Undantag som inte kan försökas** igen får helt enkelt återinbygas tillbaka till anroparen.
* undantag som kan **återinsättas** ytterligare i **tillfälliga** och **icke-övergående**.
  * **Tillfälliga** undantag är de som helt enkelt kan göras om utan att fela tjänstens slutpunktsadress. Dessa kommer att omfatta tillfälliga nätverksproblem eller andra servicefelsvar än de som anger att tjänstens slutpunktsadress inte finns.
  * **Icke-tillfälliga** undantag är de som kräver att tjänstslutpunktsadressen ska lösas igen. Dessa inkluderar undantag som anger att tjänstslutpunkten inte kunde nås, vilket indikerar att tjänsten har flyttats till en annan nod.

De `TryHandleException` fattar ett beslut om ett visst undantag. Om den **inte vet** vilka beslut som ska fattas om ett undantag, bör den returnera **falska**. Om den **vet** vilket beslut att fatta, bör det ställa in resultatet därefter och returnera **sant**.

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
Med `ICommunicationClient(C#) / CommunicationClient(Java)`en `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, `IExceptionHandler(C#) / ExceptionHandler(Java)` och uppbyggd `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` kring ett kommunikationsprotokoll sveper en ihop allt och tillhandahåller adresslösningsslingan för felhantering och tjänstpartitionen runt dessa komponenter.

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
* [ASP.NET core med tillförlitliga tjänster](service-fabric-reliable-services-communication-aspnetcore.md)
* [Fjärrproceduranrop med reliable services-ommotning](service-fabric-reliable-services-communication-remoting.md)
* [WCF-kommunikation med hjälp av Reliable Services](service-fabric-reliable-services-communication-wcf.md)
