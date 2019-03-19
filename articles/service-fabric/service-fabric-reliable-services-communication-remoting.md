---
title: Fjärrtjänst med hjälp av C# i Service Fabric | Microsoft Docs
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
ms.openlocfilehash: 366ab452d1693315e7e71301d953248008c1eac8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079136"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Fjärrkommunikation för tjänster i C# med Reliable Services

> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java i Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

För tjänster som inte är kopplad till en viss kommunikationsprotokoll eller stack, till exempel ett webb-API, Windows Communication Foundation eller andra, tillhandahåller Reliable Services-ramverk en mekanism för fjärrkommunikation att snabbt och enkelt konfigurera RPC-anrop för tjänster. Den här artikeln beskrivs hur du ställer in RPC-anrop för tjänster som skrivits med C#.

## <a name="set-up-remoting-on-a-service"></a>Ställa in fjärrstyrning på en tjänst

Du kan ställa in fjärrstyrning för en tjänst i två enkla steg:

1. Skapa ett gränssnitt för tjänsten att implementera. Det här gränssnittet definierar metoder som är tillgängliga för ett RPC-anrop till din tjänst. Metoderna måste vara aktiviteten returnerar asynkrona metoder. Gränssnittet måste implementera `Microsoft.ServiceFabric.Services.Remoting.IService` att signalera att tjänsten har ett gränssnitt för fjärrkommunikation.
2. Använd en lyssnare för fjärrkommunikation i din tjänst. En lyssnare för fjärrkommunikation är en `ICommunicationListener` implementering som tillhandahåller funktioner för fjärrkommunikation. Den `Microsoft.ServiceFabric.Services.Remoting.Runtime` namnområdet innehåller tilläggsmetod `CreateServiceRemotingListener` för både tillståndslösa och tillståndskänsliga tjänster som kan användas för att skapa en lyssnare för fjärrkommunikation med hjälp av standard-fjärrkommunikation transport-protokoll.

>[!NOTE]
>Den `Remoting` namnområdet är tillgängligt som ett separat NuGet-paket som heter `Microsoft.ServiceFabric.Services.Remoting`.

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
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Argumenten och returtyper i tjänstegränssnittet kan vara några enkla, komplexa eller anpassade typer, men de måste kunna serialiseras av .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Anropa fjärrtjänsten metoder

Anropa metoder på en tjänst med hjälp av fjärrkommunikation stack görs med hjälp av en lokal proxy till tjänsten via den `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klass. Den `ServiceProxy` metoden skapar en lokal proxy genom att använda samma gränssnitt som implementerar tjänsten. Med den proxyn kan du anropa metoder på gränssnittet via en fjärranslutning.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Fjärr-framework sprider undantag från tjänsten till klienten. Som ett resultat när `ServiceProxy`är används, klienten är ansvarig för hantering av undantag från tjänsten.

## <a name="service-proxy-lifetime"></a>Tjänsten proxylivslängden

Tjänstgenereringen proxy är en enkel åtgärd, så att du kan skapa så många som du behöver. Proxy-tjänstinstanser kan återanvändas för så länge de behövs. Du kan återanvända samma proxy-instans även om ett RPC genererar ett undantag. Varje Tjänstproxy innehåller en klient för kommunikation som används för att skicka meddelanden över nätverket. Samtidigt som du anropar fjärranrop utförs interna kontroller för att fastställa om kommunikation klienten är giltig. Baserat på resultatet av dessa kontroller, skapas klienten kommunikation nytt om det behövs. Därför, om ett undantag inträffar, du behöver inte återskapa `ServiceProxy`.

### <a name="service-proxy-factory-lifetime"></a>Tjänsten proxylivslängden factory

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) är en fabrik som skapar proxy-instanser för olika fjärrkommunikation gränssnitt. Om du använder API: et `ServiceProxyFactory.CreateServiceProxy` för att skapa en proxy, ramverket skapar en singleton-tjänstproxy.
Det är bra att skapa ett manuellt när du behöver åsidosätta [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) egenskaper.
Skapande av Factory är en kostsam åtgärd. En service proxy fabrik underhåller internt cacheminne av klienten för kommunikation.
Det är bra att cachelagra service proxy factory så länge som möjligt.

## <a name="remoting-exception-handling"></a>Fjärrkommunikation undantagshantering

Alla fjärranslutna undantag som skickats av tjänsten API skickas tillbaka till klienten som AggregateException. Remote undantag ska kunna serialiseras med DataContract. Om inte proxy API utlöser [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) med serialiseringsfel i den.

Tjänstproxy hanterar alla redundans undantag för service-partition som den har skapats för. Det matchar slutpunkterna igen om det finns redundans undantag (icke tillfälliga undantag) och försöker anropet med rätt slutpunkt. Antal återförsök för redundans undantag är obestämd.
Om tillfälliga undantag uppstår försöker proxyn anropet.

Standardparametrar för återförsök tillhandahålls av [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

En användare kan konfigurera dessa värden genom att skicka OperationRetrySettings-objekt till konstruktorn ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Använda fjärrkommunikation V2-stack

Från och med version 2.8 av fjärrkommunikation NuGet-paketet har möjlighet att använda remoting V2 stack. Remoting V2-stack som presterar bäst. Det ger också funktioner som anpassade serialisering och mer modulära API: er.
Mallkod fortsätter att använda fjärrkommunikation V1-stacken.
Remoting V2 är inte kompatibel med V1 (tidigare fjärrkommunikation-stacken). Följ instruktionerna i artikeln [uppgradera från V1 till V2](#upgrade-from-remoting-v1-to-remoting-v2) att undvika effekter på tjänstetillgänglighet.

Följande metoder är tillgängliga för att aktivera V2-stack.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Använd ett sammansättningen attribut för att använda V2-stack

De här stegen ändra mallkod för att använda V2-stack med hjälp av ett attribut i sammansättningen.

1. Ändra slutpunktsresurs från `"ServiceEndpoint"` till `"ServiceEndpointV2"` i tjänstmanifestet.

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
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Inga kodändringar krävs i klientprojektet.
Skapa klientsammansättningen med gränssnittet sammansättningen att se till att sammansättningen attributet visats tidigare används.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Använd explicit V2-klasser för att använda V2-stack

Som ett alternativ till ett attribut för sammansättningen kan V2-stack också aktiveras med hjälp av explicita V2-klasser.

De här stegen ändra mallkod för att använda V2-stack med hjälp av explicita V2-klasser.

1. Ändra slutpunktsresurs från `"ServiceEndpoint"` till `"ServiceEndpointV2"` i tjänstmanifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Använd [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) från den `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` namnområde.

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

3. Använd [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) från den `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` namnområdet för att skapa klienter.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Uppgradera från fjärrkommunikation V1 till remoting V2

Om du vill uppgradera från V1 till V2 krävs två uppgraderingar. Följ stegen i sekvensen.

1. Uppgradera tjänsten V1 till V2-tjänsten med hjälp av det här attributet.
Den här ändringen ser till att tjänsten lyssnar på V1 och V2-lyssnaren.

    a. Lägg till en slutpunktsresurs med namnet ”ServiceEndpointV2” i tjänstmanifestet.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Använd följande tilläggsmetod för att skapa en lyssnare för fjärrkommunikation.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Lägga till en sammansättning attribut på fjärrkommunikation gränssnitt för att använda V1 och V2-lyssnare och V2-klienten.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Uppgradera V1-klienten till en V2-klient med hjälp av attributet V2-klienten.
Det här steget ser till att klienten använder V2-stack.
Det krävs ingen ändring i projekt /-klienttjänsten. Skapa klienten projekt med uppdaterade gränssnittet sammansättningen räcker.

3. Det här steget är valfritt. Använda attribut för V2-lyssnare och sedan uppgradera V2-tjänsten.
Det här steget ser till att tjänsten lyssnar endast på V2-lyssnaren.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Använda fjärrkommunikation V2 (interface-kompatibelt) stack

 Remoting V2 (gränssnitt kompatibel, kallas V2_1) stack har alla funktioner i V2 fjärrkommunikation stack. Dess gränssnitt-stacken är kompatibel med V1-stack fjärrkommunikation, men det är inte bakåtkompatibla med V2 och V1. För att uppgradera från V1 till V2_1 utan att påverka tjänsttillgängligheten, följer du stegen i artikeln uppgradering från V1 till V2 (kompatibel-gränssnitt).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Använd ett sammansättningen attribut för att använda remoting V2 (interface-kompatibelt)-stack

Följ dessa steg för att ändra till en V2_1 stack.

1. Lägg till en slutpunktsresurs med namnet ”ServiceEndpointV2_1” i tjänstmanifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Använda fjärrkommunikation tilläggsmetod för att skapa en lyssnare för fjärrkommunikation.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Lägg till en [sammansättningen attributet](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) på fjärrkommunikation gränssnitt.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Det krävs inga ändringar i klientprojektet.
Skapa klientsammansättningen med gränssnittet sammansättningen att se till att attributet tidigare sammansättningen används.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Använd explicit fjärrkommunikation klasser för att skapa en lyssnare/klientfabrik för version 2 (interface-kompatibelt)

Följ de här stegen:

1. Lägg till en slutpunktsresurs med namnet ”ServiceEndpointV2_1” i tjänstmanifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Använd den [remoting V2-lyssnaren](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Standard service endpoint resursnamnet som används är ”ServiceEndpointV2_1”. Den måste ha definierats i tjänstmanifestet.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Använd V2 [klientfabrik](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Uppgradera från fjärrkommunikation V1 till remoting V2 (kompatibel-gränssnitt)

Uppgradera från V1 till V2 (gränssnitt kompatibel, kallas även V2_1), tvåstegsverifiering uppgraderingar krävs. Följ stegen i sekvensen.

1. Uppgradera tjänsten V1 till V2_1-tjänsten med hjälp av följande attribut.
Den här ändringen ser till att tjänsten lyssnar på V1 och V2_1 lyssnaren.

    a. Lägg till en slutpunktsresurs med namnet ”ServiceEndpointV2_1” i tjänstmanifestet.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Använd följande tilläggsmetod för att skapa en lyssnare för fjärrkommunikation.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Lägga till en sammansättning attribut på fjärrkommunikation gränssnitt ska användas i V1, V2_1 lyssnare och V2_1 klienten.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Uppgradera klienten V1 till V2_1 klienten med hjälp av attributet V2_1 klienten.
Det här steget ser till att klienten använder V2_1 stack.
Det krävs ingen ändring i projekt /-klienttjänsten. Skapa klienten projekt med uppdaterade gränssnittet sammansättningen räcker.

3. Det här steget är valfritt. Ta bort V1-lyssnaren version från attributet och sedan uppgradera V2-tjänsten.
Det här steget ser till att tjänsten lyssnar endast på V2-lyssnaren.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Använd anpassad serialisering med meddelandet fjärrkommunikation omslutna

För ett meddelande med fjärrkommunikation omslutna skapa vi ett enda omslutna objekt med alla parametrar som ett fält i den.
Följ de här stegen:

1. Implementera de `IServiceRemotingMessageSerializationProvider` gränssnittet för att ge implementering för anpassad serialisering.
    Det här kodstycket visar hur implementeringen ser ut.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Åsidosätt standardprovidern för serialisering med `JsonSerializationProvider` för en lyssnare för fjärrkommunikation.

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

3. Åsidosätt standardprovidern för serialisering med `JsonSerializationProvider` för en klientfabrik för fjärrkommunikation.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Nästa steg

* [Webb-API med OWIN Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Windows Communication Foundation kommunikation med Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Säker kommunikation för Reliable Services](service-fabric-reliable-services-secure-communication.md)
