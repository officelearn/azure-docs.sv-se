---
title: Pålitliga tjänster WCF kommunikation stack
description: Den inbyggda WCF-kommunikationsstacken i Service Fabric tillhandahåller WCF-kommunikation för tillförlitliga tjänster för klienttjänster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7f3b3974893316a488270f755b8f8822080658d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433865"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-baserad kommunikationsstack för reliable services
Reliable Services-ramverket gör det möjligt för tjänstförfattare att välja den kommunikationsstack som de vill använda för sin tjänst. De kan koppla in kommunikationsstacken som de själva väljer via **metoderna ICommunicationListener** som returneras från [metoderna CreateServiceReplicaListeners eller CreateServiceInstanceListeners.](service-fabric-reliable-services-communication.md) Ramverket tillhandahåller en implementering av kommunikationsstacken baserat på Windows Communication Foundation (WCF) för tjänstförfattare som vill använda WCF-baserad kommunikation.

## <a name="wcf-communication-listener"></a>WCF-kommunikationslyssnare
Den WCF-specifika **implementeringen av ICommunicationListener** tillhandahålls av klassen **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener.**

Så att inte säga att vi har ett serviceavtal av typ`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Vi kan skapa en WCF-kommunikationslyssnare i tjänsten på följande sätt.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Skriva klienter för WCF-kommunikationsstacken
För att skriva klienter att kommunicera med tjänster med hjälp av WCF, ger ramen **WcfClientCommunicationFactory**, vilket är WCF-specifika genomförandet av [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

WCF-kommunikationskanalen kan nås från **WcfCommunicationClient** som skapats av **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Klientkod kan använda **WcfCommunicationClientFactory** tillsammans med **WcfCommunicationClient** som implementerar **ServicePartitionClient** för att bestämma tjänstens slutpunkt och kommunicera med tjänsten.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> StandardtjänstenPartitionResolver förutsätter att klienten körs i samma kluster som tjänsten. Om så inte är fallet skapar du ett ServicePartitionResolver-objekt och skickar i slutpunkterna för klusteranslutningen.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Fjärrproceduranrop med reliable services-ommotning](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API med OWIN i reliable services](service-fabric-reliable-services-communication-webapi.md)
* [Säkra kommunikation för tillförlitliga tjänster](service-fabric-reliable-services-secure-communication-wcf.md)

