---
title: "Tjänsten fjärrkommunikation i Service Fabric | Microsoft Docs"
description: "Service Fabric-fjärrkommunikation kan klienter och tjänster att kommunicera med tjänster med hjälp av ett fjärranrop."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 655bc3dd3735a35fbe7437e8dda92b2adf15f7bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-remoting-with-reliable-services"></a>Tjänsten fjärrkommunikation med Reliable Services
För tjänster som inte är knutna till en viss kommunikationsprotokoll eller stacken, till exempel WebAPI, Windows Communication Foundation (WCF) eller andra, gör Reliable Services framework fjärrkommunikation snabbt och enkelt konfigurera fjärrproceduranrop för tjänster.

## <a name="set-up-remoting-on-a-service"></a>Ställa in fjärrstyrning för en tjänst
Ställa in fjärrstyrning för en tjänst görs i två enkla steg:

1. Skapa ett gränssnitt för tjänsten att implementera. Det här gränssnittet definierar metoder som är tillgängliga för remote procedure call på din tjänst. Metoderna måste vara åtgärdsreturnerande asynkrona metoder. Gränssnittet måste implementera `Microsoft.ServiceFabric.Services.Remoting.IService` att signalera att tjänsten har ett gränssnitt för fjärrkommunikation.
2. Använd en lyssnare för fjärrkommunikation i din tjänst. RemotingListener är en `ICommunicationListener` implementering som tillhandahåller funktioner för fjärrkommunikation. Den `Microsoft.ServiceFabric.Services.Remoting.Runtime` namnområde innehåller en metod,`CreateServiceRemotingListener` för både tillståndslösa och tillståndskänsliga tjänster som kan användas för att skapa en lyssnare för fjärrkommunikation med transportprotokollet standard fjärrkommunikation.

>[!NOTE]
>Den `Remoting` namnområdet är tillgängligt som ett separat NuGet-paket som kallas`Microsoft.ServiceFabric.Services.Remoting`

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
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
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

Fjärr-framework sprider undantag på tjänsten till klienten. Så undantagshantering logik på klienten med hjälp av `ServiceProxy` direkt kan hantera undantag som genereras av tjänsten.

## <a name="service-proxy-lifetime"></a>Tjänsten Proxylivslängden
ServiceProxy skapas en enkel åtgärd, så att användarna kan skapa så många som de behöver den. Tjänstproxy kan återanvändas som användarna behöver den. Om fjärråtkomst Api utlöser undantag, kan användarna fortfarande återanvända till samma proxy. Varje ServiceProxy innehåller kommunikation klient används för att skicka meddelanden via kabel. När du anropar API: T har vi interna kontrollerar om kommunikation klienten använde är giltig. Baserat på resultatet återskapar vi klienten för kommunikation. Därför om undantag inträffar kan behöver användarna inte återskapa serviceproxy.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory livslängd
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) är en fabrik som skapar proxy för olika fjärrkommunikation gränssnitt. Om du använder API ServiceProxy.Create för att skapa proxy skapar framework singleton ServiceProxyFactory.
Det är praktiskt att skapa ett manuellt när du måste åsidosätta [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) egenskaper.
Fabriken är en kostsam åtgärd. ServiceProxyFactory underhåller cacheminnet på klienten för kommunikation.
Det är bra att cachelagra ServiceProxyFactory så länge som möjligt.

## <a name="remoting-exception-handling"></a>Fjärrkommunikation undantagshantering
Alla fjärranslutna undantag som uppstått i tjänsten API, skickas tillbaka till klienten som AggregateException. RemoteExceptions ska kunna serialiseras DataContract annars [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) genereras till proxyservern API med serialiseringsfel i den.

ServiceProxy hanterar alla Failover-undantag för service-partition som skapats för. Slutpunkterna löser igen om det finns Failover Exceptions(Non-Transient Exceptions) och försöker anropa med korrekt slutpunkt. Antal återförsök för redundans undantag är obegränsad.
Om tillfälligt undantag inträffar återförsök proxy anropet.

Försök standardparametrarna är etablerar av [OperationRetrySettings]. (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) Användare kan konfigurera dessa värden genom att skicka OperationRetrySettings objekt till ServiceProxyFactory konstruktor.
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

2. Använd [fjärrkommunikation V2Listener](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingistener?view=azure-dotnet). Standardnamn som tjänsten Endpoint-resursen används måste definieras i Service Manifest är ”ServiceEndpointV2”.

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

3. Använd V2 [Klientfabrik](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
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

  class JsonMessageFactory: IServiceRemotingMessageBodyFactory
  {
      public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
          int numberOfParameters)
      {
          return new JsonRemotingRequestBody(new JObject());
      }

      public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
      {
          return  new JsonRemotingResponseBody();
      }
   }

  class ServiceRemotingRequestJsonMessageBodySerializer: IServiceRemotingRequestMessageBodySerializer
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

          var json = serviceRemotingRequestMessageBody.ToString();
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var segments = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(segments);
      }

      public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (JsonReader reader = new JsonTextReader(sr))
          {
              var serializer = new JsonSerializer();
              var ob = serializer.Deserialize<JObject>(reader);
              var ob2 = new JsonRemotingRequestBody(ob);
              return ob2;
          }
      }
  }

  class ServiceRemotingResponseJsonMessageBodySerializer: IServiceRemotingResponseMessageBodySerializer
  {

      public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
      {
          var json = JsonConvert.SerializeObject(responseMessageBody,new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
          });
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var list = new List<ArraySegment<byte>> {segment};
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
  internal class JsonRemotingResponseBody: IServiceRemotingResponseMessageBody
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

  class JsonRemotingRequestBody: IServiceRemotingRequestMessageBody
    {
      private readonly JObject jobject;

        public JsonRemotingRequestBody(JObject ob)
        {
            this.jobject = ob;
        }

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.jobject.Add(parameName, JToken.FromObject(parameter));
        }

        public object GetParameter(int position, string parameName, Type paramType)
       {
           var ob = this.jobject[parameName];
           return ob.ToObject(paramType);
       }

       public override string ToString()
        {
            return this.jobject.ToString();
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
