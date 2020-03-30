---
title: 'Säker tjänstremoting kommunikation med C #'
description: Lär dig hur du skyddar tjänståterbeklädnad kommunikation för C#-tillförlitliga tjänster som körs i ett Azure Service Fabric-kluster.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ee2f1d70f4094ccc7d80edbfaf16509b5124f607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609629"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Säker tjänståterbekännande kommunikation i en C#-tjänst
> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java i Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Säkerhet är en av de viktigaste aspekterna av kommunikation. Reliable Services-programramverket innehåller några fördefinierade kommunikationsstackar och verktyg som du kan använda för att förbättra säkerheten. I den här artikeln beskrivs hur du kan förbättra säkerheten när du använder tjänståterbetering i en C#-tjänst. Den bygger på ett befintligt [exempel](service-fabric-reliable-services-communication-remoting.md) som förklarar hur du ställer in ommotsättning för tillförlitliga tjänster skrivna i C#. 

Så här skyddar du en tjänst när du använder tjänståterbefordring med C#-tjänster:

1. Skapa ett `IHelloWorldStateful`gränssnitt, som definierar de metoder som ska vara tillgängliga för ett fjärrproceduranrop på din tjänst. Tjänsten kommer `FabricTransportServiceRemotingListener`att använda , `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` som deklareras i namnområdet. Det här `ICommunicationListener` är en implementering som tillhandahåller remoting-funktioner.

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
2. Lägg till lyssnarinställningar och säkerhetsreferenser.

    Kontrollera att certifikatet som du vill använda för att skydda tjänstkommunikationen är installerat på alla noder i klustret. 
    
    > [!NOTE]
    > På Linux-noder måste certifikatet finnas som PEM-formaterade filer i katalogen */var/lib/sfcerts.* Mer information finns i [Plats och format för X.509-certifikat på Linux-noder](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Det finns två sätt att ange lyssnarinställningar och säkerhetsreferenser:

   1. Ge dem direkt i servicekoden:

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
   2. Ge dem med hjälp av ett [config-paket:](service-fabric-application-and-service-manifests.md)

       Lägg till `TransportSettings` ett namngivet avsnitt i filen settings.xml.

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

       I det här `CreateServiceReplicaListeners` fallet kommer metoden att se ut så här:

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

        Om du `TransportSettings` lägger till ett avsnitt `FabricTransportRemotingListenerSettings` i filen settings.xml läser alla inställningar från det här avsnittet som standard in.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        I det här `CreateServiceReplicaListeners` fallet kommer metoden att se ut så här:

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
3. När du anropar metoder för en säker tjänst med hjälp `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` av remotingstacken `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`använder du i stället för att använda klassen för att skapa en tjänstproxy . Skicka `FabricTransportRemotingSettings`in , `SecurityCredentials`som innehåller .

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

    Om klientkoden körs som en del av `FabricTransportRemotingSettings` en tjänst kan du läsa in filen settings.xml. Skapa ett HelloWorldClientTransportSettings-avsnitt som liknar servicekoden, som tidigare visats. Gör följande ändringar i klientkoden:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Om klienten inte körs som en del av en tjänst kan du skapa en client_name.settings.xml-fil på samma plats där client_name.exe finns. Skapa sedan avsnittet TransportSettings i filen.

    I likhet med tjänsten `TransportSettings` `FabricTransportRemotingSettings` läser du in alla inställningar från det här avsnittet som standard om du lägger till ett avsnitt i klientinställningarna.xml/client_name.settings.xml.

    I så fall förenklas den tidigare koden ytterligare:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


Som ett nästa steg kan du läsa [webb-API med OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md).
