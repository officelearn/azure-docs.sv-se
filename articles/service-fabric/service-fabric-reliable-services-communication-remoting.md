---
title: Tjänsten fjärrkommunikation i Service Fabric | Microsoft Docs
description: Service Fabric-fjärrkommunikation kan klienter och tjänster att kommunicera med tjänster med hjälp av ett fjärranrop.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: d9ba650549d313a4ecc9ceae5eb05e1cde727892
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="service-remoting-with-reliable-services"></a>Tjänsten fjärrkommunikation med Reliable Services
För tjänster som inte är knutna till en viss kommunikationsprotokoll eller stacken, till exempel WebAPI, Windows Communication Foundation (WCF) eller andra, gör Reliable Services framework fjärrkommunikation snabbt och enkelt konfigurera fjärrproceduranrop för tjänster.

## <a name="set-up-remoting-on-a-service"></a>Ställa in fjärrstyrning för en tjänst
Ställa in fjärrstyrning för en tjänst görs i två enkla steg:

1. Skapa ett gränssnitt för tjänsten att implementera. Det här gränssnittet definierar metoder som är tillgängliga för remote procedure call på din tjänst. Metoderna måste vara åtgärdsreturnerande asynkrona metoder. Gränssnittet måste implementera `Microsoft.ServiceFabric.Services.Remoting.IService` att signalera att tjänsten har ett gränssnitt för fjärrkommunikation.
2. Använd en lyssnare för fjärrkommunikation i din tjänst. RemotingListener är en `ICommunicationListener` implementering som tillhandahåller funktioner för fjärrkommunikation. Den `Microsoft.ServiceFabric.Services.Remoting.Runtime` namnområde innehåller en metod,`CreateServiceRemotingListener` för både tillståndslösa och tillståndskänsliga tjänster som kan användas för att skapa en lyssnare för fjärrkommunikation med transportprotokollet standard fjärrkommunikation.

>[!NOTE]
>Den `Remoting` namnområdet är tillgängligt som ett separat NuGet-paket som kallas `Microsoft.ServiceFabric.Services.Remoting`

Till exempel exponerar följande tillståndslösa tjänsten en metod för att få ”Hello World” över ett fjärranrop.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Argumenten och returtyper i gränssnittet service kan vara enkla, komplexa eller anpassade typer, men de måste kunna serialiseras av .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Anropa fjärrtjänsten metoder
Anropar metoder för en tjänst med hjälp av fjärrkommunikation stacken görs med hjälp av en lokal proxyserver till tjänsten via den `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klass. Den `ServiceProxy` metoden skapar en lokal proxyserver genom att använda samma gränssnitt som tjänsten implementerar. Med denna proxy, kan du anropa metoder i gränssnittet via fjärranslutning.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Fjärr-framework sprider undantag från tjänsten till klienten. Som ett resultat när du använder `ServiceProxy`, klienten ansvarar för hantering av undantag från tjänsten.

## <a name="service-proxy-lifetime"></a>Tjänsten Proxylivslängden
ServiceProxy skapas en enkel åtgärd, så att användarna kan skapa så många som de behöver. Tjänstproxy instanser kan återanvändas som användarna behöver den. Om ett fjärranrop genererar ett undantag, kan användarna fortfarande återanvända samma instans som proxy. Varje ServiceProxy innehåller en klient för kommunikation som används för att skicka meddelanden via kabel. Vid anrop fjärranrop Kontrollera vi internt om kommunikation klienten är giltig. Baserat på resultatet återskapa vi kommunikation klienten om det behövs. Därför om ett undantag inträffar kan användarna inte behöver återskapa `ServiceProxy` eftersom det är gjort transparent.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory livslängd
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) är en fabrik som skapar instanser av proxy för olika fjärrkommunikation gränssnitt. Om du använder API: et `ServiceProxy.Create` för att skapa proxy ramen skapar sedan en singleton ServiceProxy.
Det är praktiskt att skapa ett manuellt när du måste åsidosätta [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) egenskaper.
Generering av är en kostsam åtgärd. ServiceProxyFactory upprätthåller en interna cachelagringen av klienten för kommunikation.
Det är bra att cachelagra ServiceProxyFactory så länge som möjligt.

## <a name="remoting-exception-handling"></a>Fjärrkommunikation undantagshantering
Alla undantag för remote från service API skickas tillbaka till klienten som AggregateException. RemoteExceptions ska vara DataContract serialiserbara; Om inte proxy API utlöser [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) med serialiseringsfel i den.

ServiceProxy hanterar alla failover-undantag för service-partition som skapats för. Den matchar slutpunkterna igen om det finns undantag för växling vid fel (icke-tillfälligt undantag) och försöker anropa med korrekt slutpunkt. Antal försök för växling vid fel undantag är obestämd.
Om tillfälligt undantag inträffar återförsök proxyn anropet.

Försök standardparametrarna är etablerar av [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Användare kan konfigurera dessa värden genom att skicka OperationRetrySettings objekt till ServiceProxyFactory konstruktor.
## <a name="how-to-use-remoting-v2-stack"></a>Hur du använder fjärrkommunikation V2 stack
Med 2,8 fjärrkommunikation med NuGet-paketet har du alternativet att använda fjärrkommunikation V2-stacken. Fjärrkommunikation V2-stacken är mer performant och ger funktioner som serialiserbar egna och mer pluggable Api.
Som standard om du inte gör följande ändringar, fortsätter den att använda fjärrkommunikation V1-stacken.
Fjärrkommunikation V2 är inte kompatibel med V1 (tidigare fjärrkommunikation stack), så Följ nedan artikel om hur du uppgraderar från V1 till V2 utan att påverka tjänsttillgängligheten.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Använda Sammansättningsattributet för att använda V2-stacken.

Här följer stegen för att ändra till V2-stacken.

1. Lägg till en resurs för slutpunkten med namn som ”ServiceEndpointV2” i service manifest.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Använd fjärrkommunikation metod för att skapa lyssnare för fjärrkommunikation.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Lägg till Sammansättningsattributet på fjärrkommunikation gränssnitt.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
Inga ändringar som krävs i Klientprojekt.
Skapa klientsammansättningen med gränssnittet sammansättning, att du säkerställer att ovan sammansättningen attributet används.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Med Explicit V2-klasser för att skapa lyssnare / ClientFactory
Här följer stegen för att följa.
1.  Lägg till en resurs för slutpunkten med namn som ”ServiceEndpointV2” i service manifest.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Använd [fjärrkommunikation V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet). Standardnamn som tjänsten Endpoint-resursen används måste definieras i Service Manifest är ”ServiceEndpointV2”.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Använd V2 [Klientfabrik](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Hur du uppgraderar från fjärrkommunikation V1 till fjärrkommunikation V2.
Steg 2 uppgraderingar krävs för att uppgradera från V1 till V2. Följande steg ska utföras i den ordning som visas.

1. Uppgradera tjänsten V1 V2-tjänsten genom att använda attributet CompactListener.
Den här ändringen ser till att tjänsten lyssnar på V1 och V2-lyssnaren.

    a) Lägg till en resurs för slutpunkten med namn som ”ServiceEndpointV2” i service manifest.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) använder du följande metod för att skapa lyssnare för fjärrkommunikation.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Lägg till Sammansättningsattributet på fjärrkommunikation gränssnitt för att använda CompatListener och V2-klienten.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Uppgradera klienten V1 till V2-klienten genom att använda attributet för V2-klienten.
Det här steget ser till att klienten använder V2-stacken.
Det krävs ingen ändring i projektet/klienttjänsten. Skapa klienten projekt med uppdaterade gränssnittet sammansättningen är tillräckligt.

3. Det här steget är valfritt. Använda V2Listener attribut och sedan uppgradera V2-tjänsten.
Det här steget ser till att tjänsten lyssnar bara på V2-lyssnaren.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Hur du använder anpassad serialisering med fjärrkommunikation V2.
Följande exempel använder Json-serialisering med fjärrkommunikation V2.
1. Implementera IServiceRemotingMessageSerializationProvider-gränssnittet för att ge implementering för anpassad serialisering.
    Här är ett kodfragment på hur implementeringen ser ut så.

 ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> requestBodyTypes)
        {
            return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> responseBodyTypes)
        {
            return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
        }

        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
            return new JsonMessageFactory();
        }
    }

    class JsonMessageFactory : IServiceRemotingMessageBodyFactory
    {
        public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
            int numberOfParameters)
        {
            return new JsonRemotingRequestBody();
        }

        public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
        {
            return new JsonRemotingResponseBody();
        }
    }

    class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
    {
        public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
        {
            if (serviceRemotingRequestMessageBody == null)
            {
                return null;
            }

            var writeStream = new MemoryStream();
            var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream));

            var serializer = JsonSerializer.Create(new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);

            jsonWriter.Flush();
            var segment = new ArraySegment<byte>(writeStream.ToArray());
            var segments = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(segments);
        }

        public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (JsonReader reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingRequestBody>(reader);
            }
        }
    }

    class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
    {
        public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
        {
            var json = JsonConvert.SerializeObject(responseMessageBody, new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            var bytes = Encoding.UTF8.GetBytes(json);
            var segment = new ArraySegment<byte>(bytes);
            var list = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(list);
        }

        public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (var reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingResponseBody>(reader);
            }
        }
    }

    internal class JsonRemotingResponseBody : IServiceRemotingResponseMessageBody
    {
        public object Value;

        public void Set(object response)
        {
            this.Value = response;
        }

        public object Get(Type paramType)
        {
            return this.Value;
        }
    }

    class JsonRemotingRequestBody : IServiceRemotingRequestMessageBody
    {
        public readonly Dictionary<string, object> parameters = new Dictionary<string, object>();        

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.parameters[parameName] = parameter;
        }

        public object GetParameter(int position, string parameName, Type paramType)
        {
            return this.parameters[parameName];
        }
    }
 ```

2.    Åsidosätta standardprovidern för serialisering med JsonSerializationProvider för fjärrkommunikation lyssnare.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Åsidosätta standardprovidern för serialisering med JsonSerializationProvider för fjärrkommunikation Klientfabrik.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>Nästa steg
* [Webb-API med OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikation med Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Att säkra kommunikationen för Reliable Services](service-fabric-reliable-services-secure-communication.md)
