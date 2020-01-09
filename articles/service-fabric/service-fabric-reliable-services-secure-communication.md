---
title: Säker kommunikation av fjärrkommunikation medC#
description: Lär dig hur du skyddar kommunikation mellan tjänster för C# pålitliga tjänster som körs i ett Azure Service Fabric-kluster.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ee2f1d70f4094ccc7d80edbfaf16509b5124f607
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609629"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Säker kommunikation mellan tjänster i en C# tjänst
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java i Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Säkerhet är en av de viktigaste aspekterna av kommunikationen. Reliable Services Application Framework innehåller några inbyggda kommunikations stackar och verktyg som du kan använda för att förbättra säkerheten. Den här artikeln beskriver hur du kan förbättra säkerheten när du använder tjänstens fjärrkommunikation i en C# tjänst. Det bygger på ett befintligt [exempel](service-fabric-reliable-services-communication-remoting.md) som förklarar hur du konfigurerar fjärr kommunikation för tillförlitliga tjänster som skrivits C#i. 

Följ dessa steg om du vill skydda en tjänst när du använder C# service Remoting med tjänster:

1. Skapa ett gränssnitt, `IHelloWorldStateful`, som definierar de metoder som ska vara tillgängliga för ett fjärran rop på tjänsten. Tjänsten använder `FabricTransportServiceRemotingListener`, som deklareras i namn området `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Detta är en `ICommunicationListener`-implementering som tillhandahåller funktioner för fjärr kommunikation.

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
2. Lägg till Listener-inställningar och säkerhets uppgifter.

    Kontrol lera att det certifikat som du vill använda för att skydda tjänst kommunikationen är installerat på alla noder i klustret. 
    
    > [!NOTE]
    > På Linux-noder måste certifikatet finnas som PEM-formaterade filer i katalogen */var/lib/sfcerts* . Mer information finns i [plats och format för X. 509-certifikat på Linux-noder](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Det finns två sätt som du kan ange inställningar för lyssnare och säkerhets uppgifter:

   1. Ange dem direkt i Service koden:

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
   2. Ange dem med hjälp av ett [konfigurations paket](service-fabric-application-and-service-manifests.md):

       Lägg till ett namngivet `TransportSettings`-avsnitt i filen Settings. xml.

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

       I det här fallet ser `CreateServiceReplicaListeners`-metoden ut så här:

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

        Om du lägger till en `TransportSettings`-sektion i filen Settings. xml kommer `FabricTransportRemotingListenerSettings` att läsa in alla inställningar från det här avsnittet som standard.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        I det här fallet ser `CreateServiceReplicaListeners`-metoden ut så här:

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
3. När du anropar metoder på en säker tjänst med hjälp av Remoting-stacken, i stället för att använda `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`-klassen för att skapa en Tjänstproxy, använder du `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Skicka in `FabricTransportRemotingSettings`, som innehåller `SecurityCredentials`.

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

    Om klient koden körs som en del av en tjänst kan du läsa in `FabricTransportRemotingSettings` från filen Settings. xml. Skapa ett HelloWorldClientTransportSettings-avsnitt som liknar tjänst koden, som du ser ovan. Gör följande ändringar i klient koden:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Om klienten inte körs som en del av en tjänst kan du skapa en client_name. Settings. XML-fil på samma plats som client_name. exe är. Skapa sedan en TransportSettings-sektion i filen.

    Om du lägger till en `TransportSettings`-sektion i klient inställningar. XML/client_name. Settings. XML, `FabricTransportRemotingSettings` läser in alla inställningar från det här avsnittet som standard.

    I så fall är den tidigare koden ännu enklare:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


I nästa steg ska du läsa [webb-API med OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md).
