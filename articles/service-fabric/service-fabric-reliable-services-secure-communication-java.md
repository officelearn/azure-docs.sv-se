---
title: Säker tjänstremoting kommunikation med Java
description: Lär dig hur du skyddar tjänståterbekreteringsbaserad kommunikation för Java-tillförlitliga tjänster som körs i ett Azure Service Fabric-kluster.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609646"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Säker tjänst som remoting kommunikation i en Java-tjänst
> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java i Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Säkerhet är en av de viktigaste aspekterna av kommunikation. Reliable Services-programramverket innehåller några fördefinierade kommunikationsstackar och verktyg som du kan använda för att förbättra säkerheten. I den här artikeln beskrivs hur du kan förbättra säkerheten när du använder tjänståterbetering i en Java-tjänst. Den bygger på ett befintligt [exempel](service-fabric-reliable-services-communication-remoting-java.md) som förklarar hur du ställer in ommotsättning för tillförlitliga tjänster skrivna i Java. 

Så här skyddar du en tjänst när du använder tjänståterbefordring med Java-tjänster:

1. Skapa ett `HelloWorldStateless`gränssnitt, som definierar de metoder som ska vara tillgängliga för ett fjärrproceduranrop på din tjänst. Tjänsten kommer `FabricTransportServiceRemotingListener`att använda , `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` som deklareras i paketet. Det här `CommunicationListener` är en implementering som tillhandahåller remoting-funktioner.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Lägg till lyssnarinställningar och säkerhetsreferenser.

    Kontrollera att certifikatet som du vill använda för att skydda tjänstkommunikationen är installerat på alla noder i klustret. För tjänster som körs på Linux måste certifikatet vara tillgängligt som en PEM-formulärad fil. Antingen `.pem` en fil som innehåller certifikatet `.crt` och den privata nyckeln `.key` eller en fil som innehåller certifikatet och en fil som innehåller den privata nyckeln. Mer information finns i [Plats och format för X.509-certifikat på Linux-noder](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Det finns två sätt att ange lyssnarinställningar och säkerhetsreferenser:

   1. Ge dem med hjälp av ett [config-paket:](service-fabric-application-and-service-manifests.md)

       Lägg till `TransportSettings` ett namngivet avsnitt i filen settings.xml.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       I det här `createServiceInstanceListeners` fallet kommer metoden att se ut så här:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Om du `TransportSettings` lägger till ett avsnitt i filen `FabricTransportListenerSettings` settings.xml utan prefix läser alla inställningar från det här avsnittet som standard in.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        I det här `CreateServiceInstanceListeners` fallet kommer metoden att se ut så här:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. När du anropar metoder för en säker tjänst med hjälp `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` av remotingstacken `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`använder du i stället för att använda klassen för att skapa en tjänstproxy .

    Om klientkoden körs som en del av `FabricTransportSettings` en tjänst kan du läsa in filen settings.xml. Skapa ett TransportSettings-avsnitt som liknar servicekoden, som tidigare visats. Gör följande ändringar i klientkoden:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
