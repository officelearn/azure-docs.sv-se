---
title: "Hjälpa säker kommunikation för tjänster i Azure Service Fabric | Microsoft Docs"
description: "Översikt över hur du kan säker kommunikation för tillförlitlig tjänster som körs i ett Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: 0804e43c3f1bb13bea92ebd661ca52c799ff2332
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Hjälp för säker kommunikation för tjänster i Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java i Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Säkerhet är en av de viktigaste aspekterna av kommunikation. Application framework Reliable Services innehåller några fördefinierade kommunikation stackar och verktyg som du kan använda för att förbättra säkerheten. Den här artikeln handlar om hur du förbättrar säkerheten när du använder tjänsten fjärrkommunikation och Windows Communication Foundation (WCF) kommunikation stacken.

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Skydda en tjänst när du använder tjänsten fjärrkommunikation
Vi använder en befintlig [exempel](service-fabric-reliable-services-communication-remoting.md) som förklarar hur du ställer in fjärrstyrning för tillförlitlig tjänster. Följ dessa steg om du vill skydda en tjänst när du använder tjänsten fjärrkommunikation:

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

    Kontrollera att det certifikat som du vill använda för att skydda din kommunikation är installerad på alla noder i klustret. Det finns två sätt att du kan ange inställningar för lyssnare och säkerhetsreferenser:

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

       Lägg till en `TransportSettings` -avsnittet i settings.xml.

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

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Skydda en tjänst när du använder en WCF-baserad kommunikation stack
Vi använder en befintlig [exempel](service-fabric-reliable-services-communication-wcf.md) som förklarar hur du ställer in en WCF-baserad kommunikation stack för tillförlitlig tjänster. Följ dessa steg om du vill skydda en tjänst när du använder en WCF-baserad kommunikation stack:

1. För tjänsten, måste du skydda lyssnaren för WCF-kommunikation (`WcfCommunicationListener`) som du skapar. Det gör du genom att ändra den `CreateServiceReplicaListeners` metoden.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. I klienten den `WcfCommunicationClient` klass som skapades i föregående [exempel](service-fabric-reliable-services-communication-wcf.md) förblir oförändrad. Men du måste åsidosätta den `CreateClientAsync` metod för `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Använd `SecureWcfCommunicationClientFactory` att skapa en WCF-klient för kommunikation (`WcfCommunicationClient`). Använd klienten för att anropa tjänstmetoder.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Nästa steg
* [Webb-API med OWIN i Reliable Services](service-fabric-reliable-services-communication-webapi.md)
