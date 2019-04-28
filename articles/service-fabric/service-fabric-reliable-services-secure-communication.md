---
title: Säkra kommunikationer för fjärrkommunikation med C# i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du skyddar fjärrkommunikation baserat tjänstkommunikation för C# tillförlitliga tjänster som körs i ett Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: f247142f26490e1899256917b64fbec7308fb281
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107678"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Säkra kommunikationer för fjärrkommunikation i en C# service
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java i Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Säkerhet är en av de viktigaste aspekterna av kommunikation. Reliable Services application framework innehåller några fördefinierade kommunikation stackar och verktyg som du kan använda för att förbättra säkerheten. Den här artikeln beskrivs hur du förbättrar säkerheten när du använder fjärrkommunikation för tjänster i en C# service. Den bygger på en befintlig [exempel](service-fabric-reliable-services-communication-remoting.md) som förklarar hur du ställer in fjärrstyrning för tillförlitliga tjänster som skrivits i C#. 

För att skydda en tjänst när du använder en fjärrtjänst med C# tjänster, genom att följa dessa steg:

1. Skapa ett gränssnitt `IHelloWorldStateful`, som definierar de metoder som är tillgängliga för ett RPC-anrop till din tjänst. Tjänsten kommer att använda `FabricTransportServiceRemotingListener`, som är deklarerad i den `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` namnområde. Det här är en `ICommunicationListener` implementering som tillhandahåller funktioner för fjärrkommunikation.

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
2. Lägg till lyssnaren inställningar och autentiseringsuppgifter.

    Kontrollera att det certifikat som du vill använda för att skydda din kommunikation är installerad på alla noder i klustret. 
    
    > [!NOTE]
    > På Linux-noder certifikatet måste finnas som PEM-formaterade filer i den */var/lib/sfcerts* directory. Mer information finns i [plats och format för X.509-certifikat på Linux-noder](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Det finns två sätt att du kan ange lyssnaren inställningar och autentiseringsuppgifter:

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
   2. Ange dem med hjälp av en [konfigurationspaket](service-fabric-application-and-service-manifests.md):

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

        Om du lägger till en `TransportSettings` avsnitt i filen settings.xml `FabricTransportRemotingListenerSettings` laddas alla inställningar från det här avsnittet som standard.

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
3. När du anropar metoder på en säker tjänst med hjälp av fjärrkommunikation-stacken, istället för att använda den `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klassen för att skapa en Tjänstproxy, Använd `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Skicka in `FabricTransportRemotingSettings`, som innehåller `SecurityCredentials`.

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

    Om klientkoden körs som en del av en tjänst, kan du läsa in `FabricTransportRemotingSettings` från filen settings.xml. Skapa ett HelloWorldClientTransportSettings avsnitt som liknar kod, som visades tidigare. Göra följande ändringar i klientkoden:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Om klienten inte körs som en del av en tjänst, kan du skapa en client_name.settings.xml-fil på samma plats där client_name.exe är. Skapa sedan en TransportSettings-avsnitt i filen.

    Liknande till tjänsten, om du lägger till en `TransportSettings` avsnittet i klienten settings.xml/client_name.settings.xml `FabricTransportRemotingSettings` läser in alla inställningar från det här avsnittet som standard.

    I så fall kan förenklas ytterligare tidigare koden:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


I nästa steg, läsa [webb-API med OWIN Reliable Services](service-fabric-reliable-services-communication-webapi.md).
