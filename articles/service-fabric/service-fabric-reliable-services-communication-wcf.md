---
title: Reliable Services WCF-kommunikationsstack | Microsoft Docs
description: Den inbyggda WCF-kommunikationsstack i Service Fabric ger-klienttjänsten WCF-kommunikation för Reliable Services.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: ae8a0ab0382083ebfca0834d2238403668efa71d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670584"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-baserade kommunikationsstack för Reliable Services
Reliable Services-framework kan tjänsten författare till väljer kommunikationsstack som de vill använda för sina tjänster. De kan ansluta kommunikation stack för valfri via den **ICommunicationListener** returnerades från den [CreateServiceReplicaListeners eller CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) metoder. Ramverket innehåller en implementering av kommunikationsstack baserat på Windows Communication Foundation (WCF) för tjänsten författare som vill använda WCF-baserad kommunikation.

## <a name="wcf-communication-listener"></a>WCF Communication Listener
WCF-specifika implementeringen av **ICommunicationListener** tillhandahålls av den **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** klass.

Att ange anta att vi har ett tjänstekontrakt av typen `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Vi kan skapa en WCF-kommunikationslyssnaren i tjänsten på följande sätt.

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

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Skriva klienter för WCF-kommunikationsstack
För att skriva klienter att kommunicera med tjänster med hjälp av WCF ramverket ger **WcfClientCommunicationFactory**, som är den WCF-specifik implementeringen av [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Kommunikationskanalen WCF kan nås från den **WcfCommunicationClient** skapats av den **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Klientkoden kan använda den **WcfCommunicationClientFactory** tillsammans med den **WcfCommunicationClient** som implementerar **ServicePartitionClient** att fastställa den tjänsten endpoint och kommunicera med tjänsten.

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
> Standard ServicePartitionResolver förutsätter att klienten körs i samma kluster som tjänsten. Om det inte är fallet, kan du skapa ett ServicePartitionResolver-objekt och skicka in klusterslutpunkter för anslutningen.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Fjärrproceduranrop med Reliable Services-fjärrkommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API med OWIN Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Säker kommunikation för Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)

