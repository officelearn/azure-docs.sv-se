---
title: Fjärrtjänst med C# i Service Fabric | Microsoft Docs
description: Fjärrkommunikation för Service Fabric kan klienter och tjänster att kommunicera med C#-tjänster med hjälp av en RPC-anrop.
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
ms.openlocfilehash: 7afa50484c3ebf258bbdd2b7f16c9cd051710d28
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437900"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Fjärrkommunikation för tjänster i C# med Reliable Services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java i Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

För tjänster som inte är kopplad till en viss kommunikationsprotokoll eller stack, till exempel WebAPI, Windows Communication Foundation (WCF) eller andra, tillhandahåller Reliable Services-ramverk en mekanism för fjärrkommunikation att snabbt och enkelt konfigurera RPC-anrop för tjänster. Den här artikeln beskrivs hur du ställer in RPC-anrop för tjänster som skrivits med C#.

## <a name="set-up-remoting-on-a-service"></a>Ställa in fjärrstyrning på en tjänst
Hur du konfigurerar fjärrstyrning för en tjänst görs i två enkla steg:

1. Skapa ett gränssnitt för tjänsten att implementera. Det här gränssnittet definierar metoder som är tillgängliga för ett RPC-anrop till din tjänst. Metoderna måste vara aktiviteten returnerar asynkrona metoder. Gränssnittet måste implementera `Microsoft.ServiceFabric.Services.Remoting.IService` att signalera att tjänsten har ett gränssnitt för fjärrkommunikation.
2. Använd en lyssnare för fjärrkommunikation i din tjänst. RemotingListener är en `ICommunicationListener` implementering som tillhandahåller funktioner för fjärrkommunikation. Den `Microsoft.ServiceFabric.Services.Remoting.Runtime` namnområdet innehåller en tilläggsmetod`CreateServiceRemotingListener` för både tillståndslösa och tillståndskänsliga tjänster som kan användas för att skapa en lyssnare för fjärrkommunikation med transportprotokollet standard fjärrkommunikation.

>[!NOTE]
>Den `Remoting` namnområdet är tillgängligt som ett separat NuGet-paket som heter `Microsoft.ServiceFabric.Services.Remoting`

Till exempel exponerar följande tillståndslös tjänst en enkel metod för att få ”Hello World” med ett RPC-anrop.

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

    public Task<string> HelloWorldAsync()
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
> Argumenten och returtyper i tjänstegränssnittet kan vara några enkla, komplexa eller anpassade typer, men de måste vara serialiserbara av .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Anropa fjärrtjänsten metoder
Anropa metoder på en tjänst med hjälp av fjärrkommunikation stack görs med hjälp av en lokal proxy till tjänsten via den `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klass. Den `ServiceProxy` metoden skapar en lokal proxy genom att använda samma gränssnitt som implementerar tjänsten. Med den proxyn kan du anropa metoder på gränssnittet via en fjärranslutning.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Fjärr-framework sprider undantag från tjänsten till klienten. Resultatet blir att, när du använder `ServiceProxy`, klienten är ansvarig för hantering av undantag från tjänsten.

## <a name="service-proxy-lifetime"></a>Tjänsten Proxylivslängden
Skapa en ServiceProxy är en enkel åtgärd så att du kan skapa så många som du behöver. Tjänstproxy instanser kan återanvändas så länge de behövs. Du kan återanvända samma proxy-instans även om ett RPC genererar ett undantag. Varje ServiceProxy innehåller en klient för kommunikation som används för att skicka meddelanden över nätverket. Samtidigt som du anropar fjärranrop utförs interna kontroller för att fastställa om kommunikation klienten är giltig. Baserat på resultatet av dessa kontroller, återskapas kommunikation klienten om det behövs. Därför, om ett undantag inträffar, du behöver inte återskapa `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory livslängd
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) är en fabrik som skapar proxy-instanser för olika fjärrkommunikation gränssnitt. Om du använder API: et `ServiceProxy.Create` för att skapa proxy, ramverket skapar sedan en singleton ServiceProxy.
Det är bra att skapa ett manuellt när du behöver åsidosätta [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) egenskaper.
Skapande av Factory är en kostsam åtgärd. ServiceProxyFactory underhåller internt cacheminne av klienten för kommunikation.
Det är bra att cachelagra ServiceProxyFactory så länge som möjligt.

## <a name="remoting-exception-handling"></a>Fjärrkommunikation undantagshantering
Alla fjärranslutna undantag som skickats av tjänsten API skickas tillbaka till klienten som AggregateException. RemoteExceptions ska vara DataContract serialiserbara; Om inte proxy API utlöser [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) med serialiseringsfel i den.

ServiceProxy hanterar alla redundans undantag för service-partition som den har skapats för. Det matchar slutpunkterna igen om det finns redundans undantag (icke tillfälliga undantag) och försöker anropet med rätt slutpunkt. Antal återförsök för redundans undantag är obestämd.
Om tillfälliga undantag uppstår försöker proxyn anropet.

Återförsök standardparametrarna är etablerar av [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Du kan konfigurera dessa värden genom att skicka OperationRetrySettings objekt till ServiceProxyFactory konstruktorn.

## <a name="how-to-use-the-remoting-v2-stack"></a>Hur du använder Remoting V2-stack

Från och med NuGet-fjärrkommunikation paketets version 2.8 har du möjlighet att använder Remoting V2-stacken. Remoting V2-stacken är bättre och tillhandahåller funktioner som anpassade serialisering och mer modulära API: er.
Mallkod fortsätter att använda fjärrkommunikation V1-stacken.
Remoting V2 är inte kompatibel med V1 (tidigare fjärrkommunikation-stacken), så Följ anvisningarna nedan på [hur du uppgraderar från V1 till V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) utan att påverka tjänsttillgängligheten.

Följande metoder är tillgängliga för att aktivera V2-stack.

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>Med hjälp av ett attribut för sammansättningen för att använda V2-stack

De här stegen ändra mallkod för att använda V2-Stack med hjälp av ett attribut i sammansättningen.

1. Ändra Slutpunktsresurs från `"ServiceEndpoint"` till `"ServiceEndpointV2"` i tjänstmanifestet.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Använd den `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` tilläggsmetod skapa fjärrkommunikation lyssnare (lika för både V1 och V2).

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. Markera den sammansättning som innehåller fjärrkommunikation gränssnitt med en `FabricTransportServiceRemotingProvider` attribut.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```

Inga kodändringar krävs i klientprojektet.
Skapa klientsammansättningen med gränssnittet sammansättningen att se till att sammansättningen-attributet som visas ovan används.

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>Använde explicit V2-klasser för att använda V2-stack

Som ett alternativ till ett attribut för sammansättningen aktiveras V2-stack också med hjälp av explicita V2-klasser.

De här stegen ändra mallkod för att använda V2-Stack med explicita V2-klasser.

1. Ändra Slutpunktsresurs från `"ServiceEndpoint"` till `"ServiceEndpointV2"` i tjänstmanifestet.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. Använd den [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) från den `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` namnområde.

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

3. Använd den [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) från den `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` namnområdet för att skapa klienter.

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Hur du uppgraderar från fjärrkommunikation V1 till V2 för fjärrkommunikation.
Steg 2 uppgraderingar krävs för att uppgradera från V1 till V2. Följande steg för att köras i vilken ordning som visas.

1. Uppgradera tjänsten V1 till V2-tjänsten med hjälp av CompactListener attribut.
Den här ändringen ser till att tjänsten lyssnar på V1 och V2-lyssnare.

    (a) Lägg till en Slutpunktsresurs med namn som ”ServiceEndpointV2” i tjänstmanifestet.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) använda följande tilläggsmetod för att skapa fjärrkommunikation lyssnare.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) lägga till sammansättningen attribut på fjärrkommunikation gränssnitt för att använda CompatListener och V2-klienten.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Uppgradera klienten V1 till V2-klienten med V2 klienten attributet.
Det här steget ser till att klienten använder V2-stacken.
Det krävs ingen ändring i projekt/klienttjänsten. Skapa klienten projekt med uppdaterade gränssnittet sammansättningen räcker.

3. Det här steget är valfritt. Använd V2Listener attributet och sedan uppgradera V2-tjänsten.
Det här steget ser till att tjänsten lyssnar endast på V2-lyssnaren.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Hur du använder anpassad serialisering med Remoting V2.
Följande exempel använder Json-serialisering med Remoting V2.
1. Implementera IServiceRemotingMessageSerializationProvider gränssnittet för att ge implementering för anpassad serialisering.
    Här är ett kodfragment på att implementeringen ser ut som.

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

2.    Åsidosätta standardprovidern för serialisering med JsonSerializationProvider för fjärrkommunikation lyssnaren.

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
* [Webb-API med OWIN Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikation med Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Säker kommunikation för Reliable Services](service-fabric-reliable-services-secure-communication.md)

