---
title: Ommotsättning av tjänsten med C# i serviceinfrastruktur
description: 'Service Fabric remoting tillåter klienter och tjänster att kommunicera med C # tjänster med hjälp av en fjärrprocedur samtal.'
author: vturecek
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 0d59275f25931a11b2d551a2e9eb019838e4c1b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433874"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Serviceåterbetering i C# med tillförlitliga tjänster

> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java i Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

För tjänster som inte är kopplade till ett visst kommunikationsprotokoll eller en viss stack, till exempel ett webb-API, Windows Communication Foundation eller andra, tillhandahåller ramverket för tillförlitliga tjänster en remoting-mekanism för att snabbt och enkelt konfigurera fjärrprocedursamtal för Tjänster. I den här artikeln beskrivs hur du ställer in fjärrproceduranrop för tjänster som är skrivna med C#.

## <a name="set-up-remoting-on-a-service"></a>Konfigurera ommotsättning på en tjänst

Du kan ställa in ommotning för en tjänst i två enkla steg:

1. Skapa ett gränssnitt som tjänsten kan implementera. Det här gränssnittet definierar de metoder som är tillgängliga för ett fjärrproceduranrop på din tjänst. Metoderna måste vara asynkrona metoder som returneras av uppgift. Gränssnittet måste `Microsoft.ServiceFabric.Services.Remoting.IService` implementeras för att signalera att tjänsten har ett remoting-gränssnitt.
2. Använd en remoting lyssnare i din tjänst. En remoting lyssnare `ICommunicationListener` är en implementering som ger remoting kapacitet. Namnområdet `Microsoft.ServiceFabric.Services.Remoting.Runtime` innehåller tilläggsmetoden `CreateServiceRemotingInstanceListeners` för både tillståndslösa och tillståndskänsliga tjänster som kan användas för att skapa en remoting-lyssnare med hjälp av standardtransportprotokollet för ommoting.

>[!NOTE]
>Namnområdet `Remoting` är tillgängligt som ett separat `Microsoft.ServiceFabric.Services.Remoting`NuGet-paket som heter .

Följande tillståndslösa tjänst exponerar till exempel en enda metod för att få "Hello World" över ett fjärrproceduranrop.

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
> Argumenten och returtyperna i tjänstgränssnittet kan vara enkla, komplexa eller anpassade typer, men de måste kunna serialiseras av .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Anropa fjärrtjänstmetoder

Anropa metoder för en tjänst med hjälp av remoting stacken görs `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` med hjälp av en lokal proxy till tjänsten via klassen. Metoden `ServiceProxy` skapar en lokal proxy med samma gränssnitt som tjänsten implementerar. Med den proxyn kan du anropa metoder på gränssnittet på distans.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Remoting-ramverket sprider undantag som genereras av tjänsten till klienten. När klienten `ServiceProxy`används är klienten därför ansvarig för att hantera de undantag som genereras av tjänsten.

## <a name="service-proxy-lifetime"></a>Livslängd för tjänstproxy

Skapa tjänstproxy är en lättåtgärd, så du kan skapa så många du behöver. Tjänstproxyinstanser kan återanvändas så länge de behövs. Om ett fjärrproceduranrop genererar ett undantag kan du fortfarande återanvända samma proxyinstans. Varje tjänstproxy innehåller en kommunikationsklient som används för att skicka meddelanden via kabeln. När fjärranrop anropas utförs interna kontroller för att avgöra om kommunikationsklienten är giltig. Baserat på resultaten av dessa kontroller återskapas kommunikationsklienten om det behövs. Om ett undantag inträffar behöver du därför `ServiceProxy`inte återskapa .

### <a name="service-proxy-factory-lifetime"></a>Livslängden för tjänstens proxyfabrik

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) är en fabrik som skapar proxyinstanser för olika remoting-gränssnitt. Om du använder `ServiceProxyFactory.CreateServiceProxy` API:et för att skapa en proxy skapar ramverket en singleton-tjänstproxy.
Det är användbart att skapa en manuellt när du behöver åsidosätta [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) egenskaper.
Fabriksskapande är en dyr operation. En tjänstproxyfabrik har en intern cache för kommunikationsklienten.
En bra metod är att cachelagra tjänstproxyfabriken så länge som möjligt.

## <a name="remoting-exception-handling"></a>Hantering av ommotsättningsundantag

Alla fjärrundantag som genereras av tjänst-API:et skickas tillbaka till klienten som AggregateException. Fjärrundantag ska kunna serialiseras av DataContract. Om de inte är det, genererar proxy-API:et [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) med serialiseringsfelet i det.

Tjänstproxyn hanterar alla redundansundantag för den tjänstpartition som skapas för. Den löser slutpunkterna igen om det finns undantag för redundans (icke-tillfälliga undantag) och försöker igen samtalet med rätt slutpunkt. Antalet återförsök för undantag för redundans är obegränsat.
Om tillfälliga undantag inträffar försöker proxyn samtalet.

Standardparametrar för återförsök tillhandahålls av [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

En användare kan konfigurera dessa värden genom att skicka OperationRetrySettings-objektet till ServiceProxyFactory-konstruktorn.

## <a name="use-the-remoting-v2-stack"></a>Använda remoting V2 stacken

Från och med version 2.8 av NuGet remoting-paketet har du möjlighet att använda den remoting V2-stacken. Den remoting V2 stacken presterar bättre. Det ger också funktioner som anpassad serialisering och mer pluggbara API:er.
Mallkoden fortsätter att använda v1-stacken för remoting.
Remoting V2 är inte kompatibelt med V1 (föregående remoting stack). Följ instruktionerna i artikeln [Uppgradera från V1 till V2](#upgrade-from-remoting-v1-to-remoting-v2) för att undvika effekter på tjänstens tillgänglighet.

Följande metoder är tillgängliga för att aktivera V2-stacken.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Använda ett sammansättningsattribut för att använda V2-stacken

De här stegen ändrar mallkoden så att V2-stacken används med hjälp av ett sammansättningsattribut.

1. Ändra slutpunktsresursen från `"ServiceEndpoint"` till `"ServiceEndpointV2"` i tjänstmanifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Använd `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` tilläggsmetoden för att skapa remoting-lyssnare (lika för både V1 och V2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Markera sammansättningen som innehåller remoting-gränssnitten med ett `FabricTransportServiceRemotingProvider` attribut.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Inga kodändringar krävs i klientprojektet.
Skapa klientsammansättningen med gränssnittssammansättningen för att se till att sammansättningsattributet som tidigare visats används.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Använd explicita V2-klasser för att använda V2-stacken

Som ett alternativ till att använda ett sammansättningsattribut kan V2-stacken också aktiveras med hjälp av explicita V2-klasser.

Dessa steg ändrar mallkoden för att använda V2-stacken med hjälp av explicita V2-klasser.

1. Ändra slutpunktsresursen från `"ServiceEndpoint"` till `"ServiceEndpointV2"` i tjänstmanifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Använd [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` från namnområdet.

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

3. Använd [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` från namnområdet för att skapa klienter.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Uppgradera från remoting V1 till remoting V2

För att uppgradera från V1 till V2 krävs tvåstegsuppgraderingar. Följ stegen i den här sekvensen.

1. Uppgradera V1-tjänsten till V2-tjänsten med hjälp av det här attributet.
Den här ändringen säkerställer att tjänsten lyssnar på V1- och V2-lyssnaren.

    a. Lägg till en slutpunktsresurs med namnet "ServiceEndpointV2" i tjänstmanifestet.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Använd följande tilläggsmetod för att skapa en remoting-lyssnare.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Lägg till ett sammansättningsattribut på remoting-gränssnitt för att använda V1- och V2-lyssnaren och V2-klienten.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Uppgradera V1-klienten till en V2-klient med hjälp av V2-klientattributet.
Det här steget ser till att klienten använder V2-stacken.
Ingen ändring i klientprojektet/tjänsten krävs. Det räcker med att bygga klientprojekt med uppdaterad gränssnittssammansättning.

3. Det här steget är valfritt. Använd V2-lyssnare attributet och uppgradera sedan V2-tjänsten.
Det här steget ser till att tjänsten bara lyssnar på V2-lyssnaren.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Använd den remoting V2 (gränssnittskompatibla) stacken

 Den remoting V2 (gränssnitt kompatibel, känd som V2_1) stacken har alla funktioner i V2 remoting stack. Dess gränssnitt stack är kompatibel med remoting V1 stack, men det är inte bakåtkompatibel med V2 och V1. Om du vill uppgradera från V1 till V2_1 utan att påverka tjänstens tillgänglighet följer du stegen i artikeln Uppgradera från V1 till V2 (gränssnittskompatibel).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Använda ett sammansättningsattribut för att använda den remoting V2 -stapeln (gränssnittskompatibel)

Följ dessa steg för att ändra till en V2_1 stack.

1. Lägg till en slutpunktsresurs med namnet "ServiceEndpointV2_1" i tjänstmanifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Använd metoden remoting extension för att skapa en remoting lyssnare.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Lägg till ett [sammansättningsattribut](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) för remoting-gränssnitt.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Inga ändringar krävs i klientprojektet.
Skapa klientsammansättningen med gränssnittssammansättningen för att se till att det tidigare sammansättningsattributet används.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Använd explicita remoting klasser för att skapa en lyssnare / klient fabrik för V2 (gränssnitt kompatibel) version

Följ de här stegen:

1. Lägg till en slutpunktsresurs med namnet "ServiceEndpointV2_1" i tjänstmanifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Använd [den remoting V2-lyssnaren](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Standardresursnamnet för tjänstslutpunkt som används är "ServiceEndpointV2_1". Det måste definieras i tjänstmanifestet.

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

3. Använd [V2-klientfabriken](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Uppgradera från remoting V1 till remoting V2 (gränssnittskompatibel)

För att uppgradera från V1 till V2 (gränssnittskompatibel, så kallad V2_1), krävs tvåstegsuppgraderingar. Följ stegen i den här sekvensen.

> [!NOTE]
> När du uppgraderar från V1 `Remoting` till V2 kontrollerar du att namnområdet uppdateras för att använda V2. Exempel: 'Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client'
>
>

1. Uppgradera V1-tjänsten till V2_1-tjänsten med hjälp av följande attribut.
Denna förändring ser till att tjänsten lyssnar på V1 och V2_1 lyssnaren.

    a. Lägg till en slutpunktsresurs med namnet "ServiceEndpointV2_1" i tjänstmanifestet.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Använd följande tilläggsmetod för att skapa en remoting-lyssnare.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Lägg till ett sammansättningsattribut på remoting-gränssnitt för att använda V1-, V2_1-lyssnaren och V2_1 klienten.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Uppgradera V1-klienten till V2_1 klient med hjälp av attributet V2_1 client.
Det här steget ser till att klienten använder V2_1 stacken.
Ingen ändring i klientprojektet/tjänsten krävs. Det räcker med att bygga klientprojekt med uppdaterad gränssnittssammansättning.

3. Det här steget är valfritt. Ta bort V1-lyssnarversionen från attributet och uppgradera sedan V2-tjänsten.
Det här steget ser till att tjänsten bara lyssnar på V2-lyssnaren.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Använda anpassad serialisering med ett omfördämningsreprat meddelande

För ett ommotningsreperat meddelande skapar vi ett enda radbrutet objekt med alla parametrar som ett fält i det.
Följ de här stegen:

1. Implementera `IServiceRemotingMessageSerializationProvider` gränssnittet för att tillhandahålla implementering för anpassad serialisering.
    Det här kodavsnittet visar hur implementeringen ser ut.

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

2. Åsidosätt standardärualiseringsprovidern med `JsonSerializationProvider` för en remoting-lyssnare.

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

3. Åsidosätt standardärualiseringsprovidern med `JsonSerializationProvider` för en remoting-klientfabrik.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Nästa steg

* [Webb-API med OWIN i reliable services](service-fabric-reliable-services-communication-webapi.md)
* [Windows Communication Foundation kommunikation med tillförlitliga tjänster](service-fabric-reliable-services-communication-wcf.md)
* [Säker kommunikation för tillförlitliga tjänster](service-fabric-reliable-services-secure-communication.md)
