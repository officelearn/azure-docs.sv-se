---
title: Säkra kommunikationer för fjärrkommunikation med Java i Azure Service Fabric | Microsoft Docs
description: Lär dig hur du skyddar fjärrkommunikation baserad kommunikation för Java reliable services som körs i ett Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667012"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Säkra kommunikationer för fjärrkommunikation i en Java-tjänst
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java i Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Säkerhet är en av de viktigaste aspekterna av kommunikation. Reliable Services application framework innehåller några fördefinierade kommunikation stackar och verktyg som du kan använda för att förbättra säkerheten. Den här artikeln beskriver hur du förbättrar säkerheten när du använder fjärrtjänst i en Java-tjänst. Den bygger på en befintlig [exempel](service-fabric-reliable-services-communication-remoting-java.md) som förklarar hur du konfigurerar fjärrstyrning för reliable services som är skrivna i Java. 

Följ dessa steg om du vill skydda en tjänst när du använder fjärrtjänst med Java-tjänster:

1. Skapa ett gränssnitt `HelloWorldStateless`, som definierar de metoder som är tillgängliga för ett RPC-anrop till din tjänst. Tjänsten kommer att använda `FabricTransportServiceRemotingListener`, som är deklarerad i den `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` paketet. Det här är en `CommunicationListener` implementering som tillhandahåller funktioner för fjärrkommunikation.

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
2. Lägg till lyssnaren inställningar och autentiseringsuppgifter.

    Kontrollera att det certifikat som du vill använda för att skydda din kommunikation är installerad på alla noder i klustret. För tjänster som körs på Linux, måste certifikatet vara tillgänglig som en PEM-formmatted-fil. antingen en `.pem` -fil som innehåller certifikatet och den privata nyckeln eller en `.crt` -fil som innehåller certifikatet och en `.key` -fil som innehåller den privata nyckeln. Mer information finns i [plats och format för X.509-certifikat på Linux-noder](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Det finns två sätt att du kan ange lyssnaren inställningar och autentiseringsuppgifter:

   1. Ange dem med hjälp av en [konfigurationspaket](service-fabric-application-and-service-manifests.md):

       Lägg till en namngiven `TransportSettings` -avsnittet i settings.xml.

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

       I det här fallet den `createServiceInstanceListeners` metoden ser ut så här:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Om du lägger till en `TransportSettings` -avsnittet i settings.xml utan att alla prefix `FabricTransportListenerSettings` laddas alla inställningar från det här avsnittet som standard.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        I det här fallet den `CreateServiceInstanceListeners` metoden ser ut så här:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. När du anropar metoder på en säker tjänst med hjälp av fjärrkommunikation-stacken, istället för att använda den `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klassen för att skapa en Tjänstproxy, Använd `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`.

    Om klientkoden körs som en del av en tjänst, kan du läsa in `FabricTransportSettings` från filen settings.xml. Skapa ett TransportSettings avsnitt som liknar kod, som visades tidigare. Göra följande ändringar i klientkoden:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
