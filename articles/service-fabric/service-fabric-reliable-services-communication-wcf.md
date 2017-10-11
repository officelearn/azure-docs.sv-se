---
title: "Tillförlitliga WCF Services kommunikation stack | Microsoft Docs"
description: "Inbyggda WCF kommunikation stacken i Service Fabric ger-klienttjänsten WCF-kommunikation för Reliable Services."
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: 7037620ebdc26a9f18531064bf45d058f5060e39
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-baserad kommunikation stacken för Reliable Services
Reliable Services framework kan tjänsten författare välja stacken kommunikation som de vill använda för den här tjänsten. De kan ansluta kommunikation stacken valfri via den **ICommunicationListener** returnerades från den [CreateServiceReplicaListeners eller CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metoder. Ramen tillhandahåller en implementering av kommunikation stacken baserat på Windows Communication Foundation (WCF) för tjänsten programutvecklare som vill använda WCF-baserad kommunikation.

## <a name="wcf-communication-listener"></a>Lyssnare för WCF-kommunikation
WCF-specifika implementeringen av **ICommunicationListener** tillhandahålls av den **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** klass.

Att ange Säg har vi ett servicekontrakt av typen`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Vi kan skapa en lyssnare för WCF-kommunikation i tjänsten på följande sätt.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Skrivning av klienter för stacken för WCF-kommunikation
För att skriva klienter att kommunicera med tjänster med hjälp av WCF ramen innehåller **WcfClientCommunicationFactory**, vilket är WCF-specifika implementering av [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Kommunikationskanalen WCF kan nås från den **WcfCommunicationClient** skapas av den **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Klientkoden kan använda den **WcfCommunicationClientFactory** tillsammans med den **WcfCommunicationClient** som implementerar **ServicePartitionClient** att avgöra den tjänsten endpoint och kommunicera med tjänsten.

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
> Standard ServicePartitionResolver förutsätter att klienten körs i samma kluster som för tjänsten. Om det inte är fallet, skapa ett ServicePartitionResolver-objekt och ange klustret anslutningens slutpunkter.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Fjärrproceduranrop med Reliable Services fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API med OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Att säkra kommunikationen för Reliable Services](service-fabric-reliable-services-secure-communication.md)

