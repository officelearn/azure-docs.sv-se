---
title: Skydda fjärrkommunikation servicemeddelanden med C# i Azure Service Fabric | Microsoft Docs
description: Lär dig att skydda fjärrkommunikation baserat tjänstkommunikation för C# tillförlitliga tjänster som körs i ett Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: be5dab7b9714f13a4bd30e6ab33a5a0e2016212d
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020027"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Skydda fjärrkommunikation servicemeddelanden i en C#-tjänst
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java i Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Säkerhet är en av de viktigaste aspekterna av kommunikation. Application framework Reliable Services innehåller några fördefinierade kommunikation stackar och verktyg som du kan använda för att förbättra säkerheten. Den här artikeln beskriver hur du förbättrar säkerheten när du använder tjänsten fjärrkommunikation i C#-tjänsten. Den bygger på en befintlig [exempel](service-fabric-reliable-services-communication-remoting.md) som förklarar hur du ställer in fjärrstyrning för tillförlitlig tjänster som skrivits i C#. 

Följ dessa steg om du vill skydda en tjänst när du använder tjänsten fjärrkommunikation med C#-tjänster:

1. Skapa ett gränssnitt `IHelloWorldStateful`, som definierar metoder som är tillgängliga för remote procedure call på din tjänst. Tjänsten använder `FabricTransportServiceRemotingListener`, som har deklarerats i den `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` namnområde. Det här är en `ICommunicationListener` implementering som tillhandahåller funktioner för fjärrkommunikation.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Lägga till lyssnarinställningarna och säkerhetsreferenser.

    Kontrollera att det certifikat som du vill använda för att skydda din kommunikation är installerad på alla noder i klustret. 
    
    > [!NOTE]
    > För Linux-noder, certifikatet måste finnas som PEM-formaterade filer i den */var/lib/sfcerts* directory. Läs mer i [plats och format för X.509-certifikat på Linux-noder](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Det finns två sätt att du kan ange inställningar för lyssnare och säkerhetsreferenser:

   1. Ge dem direkt i kod som:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Ge dem med hjälp av en [konfigurationspaketet](service-fabric-application-and-service-manifests.md):

       Lägg till en namngiven `TransportSettings` -avsnittet i settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       I det här fallet den `CreateServiceReplicaListeners` metoden ser ut så här:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Om du lägger till en `TransportSettings` avsnitt i filen settings.xml `FabricTransportRemotingListenerSettings ` kommer att läsa in alla inställningar från det här avsnittet som standard.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        I det här fallet den `CreateServiceReplicaListeners` metoden ser ut så här:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. När du anropar metoder på en skyddad tjänst med hjälp av fjärrkommunikation stacken, istället för att använda den `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klassen för att skapa en Tjänstproxy använder `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Skicka in `FabricTransportRemotingSettings`, som innehåller `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Om klientkoden körs som en del av en tjänst, kan du läsa in `FabricTransportRemotingSettings` från filen settings.xml. Skapa ett HelloWorldClientTransportSettings avsnitt som liknar kod, enligt tidigare. Gör följande ändringar klientkoden:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Om klienten inte körs som en del av en tjänst, kan du skapa en client_name.settings.xml-fil på samma plats där client_name.exe är. Skapa sedan en TransportSettings avsnitt i filen.

    Liknande tjänsten, om du lägger till en `TransportSettings` avsnitt i klienten settings.xml/client_name.settings.xml `FabricTransportRemotingSettings` läser in alla inställningar från det här avsnittet som standard.

    I så fall förenklas ytterligare tidigare koden:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Som ett nästa steg läsa [Web API med OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md).
