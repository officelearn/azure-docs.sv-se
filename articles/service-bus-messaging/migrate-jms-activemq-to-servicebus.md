---
title: Migrera JMS-program (Java Message Service) från ActiveMQ till Azure Service Bus | Microsoft Docs
description: Den här artikeln förklarar hur du migrerar befintliga JMS-program som interagerar med aktiva MQ för att interagera med Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122412"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Migrera befintliga JMS (Java Message Service) 2,0-program från Active MQ till Azure Service Bus

Azure Service Bus stöder Java/J2EE-och våren-arbetsbelastningar som använder API: t Java Message Service (JMS) 2,0 via protokollet Advanced Message Queueing Protocol (AMQP).

Den här guiden beskriver vad du bör känna till när du vill ändra ett befintligt JMS 2,0-program (Java Message Service) som interagerar med en JMS-Broker (särskilt Apache ActiveMQ eller Amazon MQ) för att interagera med Azure Service Bus.

## <a name="before-you-start"></a>Innan du börjar

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Skillnader mellan Azure Service Bus-och Apache-ActiveMQ

Azure Service Bus-och Apache-ActiveMQ är både meddelande hanterare som fungerar som JMS-leverantörer för klient program för att skicka meddelanden till och ta emot meddelanden från. De aktiverar både punkt-till-punkt-semantik med **köer** och publicera-prenumerera-semantik med **ämnen** och **prenumerationer**. 

Dessutom finns det några skillnader i de två.

| Kategori | Aktiv MQ | Azure Service Bus |
| --- | --- | --- |
| Program skiktning | Klustrad monolit | Två nivåer <br> (Gateway + server del) |
| Protokollstöd | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Etableringsläge | <ul> <li> IaaS (lokalt) </li> <li> Amazon MQ (hanterad PaaS) </li> | Hanterade PaaS |
| Meddelandestorlek | Kunde konfigureras | 1 MB (Premium-nivå) |
| Hög tillgänglighet | Kund hantering | Plattform hanterad |
| Haveriberedskap | Kund hantering | Plattform hanterad | 

### <a name="current-supported-and-unsupported-features"></a>Aktuella funktioner som stöds och som inte stöds

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Varningar

Azure Service Bus med två nivåer i ger olika funktioner för affärs kontinuitet (hög tillgänglighet och haveri beredskap). Det finns dock några saker att tänka på när du använder JMS-funktioner.

#### <a name="service-upgrades"></a>Tjänst uppgraderingar

Om du uppgraderar och startar om Service Bus tas temporära köer eller ämnen bort.

Om programmet är känsligt för data förlust i temporära köer eller ämnen rekommenderar vi att du **inte** använder tillfälliga köer eller ämnen och använder varaktiga köer, ämnen och prenumerationer i stället.

#### <a name="data-migration"></a>Datamigrering

Som en del av migreringen/ändringen av klient programmen för att interagera med Azure Service Bus migreras inte data som lagras i ActiveMQ till Service Bus.

Ett anpassat program kan behövas för att tömma ActiveMQ-köer, ämnen och prenumerationer och spela upp meddelanden till Service Bus köer, ämnen och prenumerationer.

#### <a name="authentication-and-authorization"></a>Autentisering och auktorisering

Rollbaserad Access Control (RBAC) som stöds av Azure ActiveDirectory är den föredragna autentiseringsmetoden för Azure Service Bus.

Eftersom RBAC inte stöds för närvarande på grund av brist på anspråk baserat stöd för autentisering av Apache QPID JMS.

För närvarande stöds endast autentisering med SAS-nycklar.

## <a name="pre-migration"></a>Före migrering

### <a name="version-check"></a>Versions kontroll

Nedan används de komponenter som används vid skrivning av JMS-program och de olika versioner som stöds. 

| Komponenter | Version |
|---|---|
| API för Java-meddelande tjänst (JMS) | 1,1 eller senare |
| AMQP-protokoll | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Se till att AMQP-portarna är öppna

Azure Service Bus stöder kommunikation via AMQP-protokollet. I detta syfte måste kommunikation över portarna 5671 (AMQP) och 443 (TCP) aktive ras. Beroende på var klient programmen finns kan du behöva ett support ärende för att tillåta kommunikation över dessa portar.

> [!IMPORTANT]
> Azure Service Bus stöder **endast** AMQP 1,0-protokoll.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Konfigurera Enterprise-konfigurationer (VNET, brand vägg, privat slut punkt osv.)

Azure Service Bus aktiverar olika funktioner för företags säkerhet och hög tillgänglighet. Om du vill veta mer om dem följer du länken nedan.

  * [Virtual Network tjänst slut punkter](service-bus-service-endpoints.md)
  * [Brandvägg](service-bus-ip-filtering.md)
  * [Kryptering på tjänst sidan med kundhanterad nyckel (BYOK)](configure-customer-managed-key.md)
  * [Privata slut punkter](private-link-service.md)
  * [Autentisering och auktorisering](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Övervakning, aviseringar och spårning

Mått publiceras för varje Service Bus-namnrymd till Azure Monitor och kan användas för aviseringar och dynamisk skalning av resurser som har allokerats till namn området.

Läs mer om de olika måtten och hur du ställer in aviseringar på dem med [Service Bus mått i Azure Monitor](service-bus-metrics-azure-monitor.md).

Läs mer om spårning av [klient sidan för data åtgärder](service-bus-end-to-end-tracing.md) och [Operational/Diagnostic-loggning för hanterings åtgärder](service-bus-diagnostic-logs.md)

### <a name="metrics---newrelic"></a>Mått – NewRelic

Nedan visas en praktisk vägledning om vilka mått från ActiveMQ som mappar till vilka mått i Azure Service Bus. Nedan refereras från NewRelic.

  * [ActiveMQ/Amazon MQ NewRelic-mått](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus NewRelic mått](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> För närvarande har NewRelic inte någon sömlös integrering med ActiveMQ direkt, men de har mått som är tillgängliga för Amazon MQ.
> Eftersom Amazon MQ härleds från ActiveMQ, mappar nedanstående guide NewRelic-måtten från AmazonMQ till Azure Service Bus.
>

|Mått grupperingen| AmazonMQ/aktivt MQ-mått | Azure Service Bus mått |
|------------|---------------------------|--------------------------|
|Utjämning|`CpuUtilization`|`CPUXNS`|
|Utjämning|`MemoryUsage`|`WSXNS`|
|Utjämning|`CurrentConnectionsCount`|`activeConnections`|
|Utjämning|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Utjämning|`InactiveDurableTopicSubscribersCount`|Utnyttja prenumerations mått|
|Utjämning|`TotalMessageCount`|Dra nytta av kö/ämne/prenumerations nivå`activeMessages`|
|Kö/ämne|`EnqueueCount`|`incomingMessages`|
|Kö/ämne|`DequeueCount`|`outgoingMessages`|
|Kö|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migrering

För att migrera ditt befintliga JMS 2,0-program för att interagera med Azure Service Bus måste du utföra stegen nedan.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Exportera topologi från ActiveMQ och skapa entiteter i Azure Service Bus (valfritt)

För att säkerställa att klient programmen kan ansluta till Azure Service Bus kan topologin – som innehåller köer, ämnen och prenumerationer, behöva migreras från **Apache ActiveMQ** till **Azure Service Bus**.

> [!NOTE]
> För JMS-program (Java Message Service) skapar köer, ämnen och prenumerationer en körnings åtgärd. De flesta JMS-providers (Java Message Service) (meddelande utjämnare) erbjuder funktioner för att skapa *köer*, *ämnen* och *prenumerationer* vid körning.
>
> Därför är steget ovan valfritt.
>
> Säkerställ att ditt program har behörighet att skapa topologin vid körning genom att kontrol lera att behörigheterna för anslutnings strängen med ***SAS*** -behörighet används.

Gör så här: 
  * Använd [kommando rads verktygen för ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) för att exportera topologin
  * Återskapa samma topologi med en Azure Resource Manager- [mall](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Kör Azure Resource Manager-mallen.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importera maven-beroendet för Service Bus JMS-implementering

För att säkerställa sömlös anslutning med Azure Service Bus måste paketet ***Azure-Service Bus-JMS*** läggas till som ett beroende till maven- `pom.xml` filen enligt nedan.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Konfigurations ändringar för program Server

Den här delen är anpassad till den program server som är värd för dina klient program som ansluter till Active MQ.

#### <a name="tomcat"></a>Tomcat

Här börjar vi med konfigurationen som är speciell för Active MQ som det visas i `/META-INF/context.xml` filen.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

som kan anpassas så att de pekar på Azure Service Bus

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Våren-program

##### <a name="update-applicationproperties-file"></a>Uppdatera `application.properties` fil

Om du använder ett våren Boot-program för att ansluta till ActiveMQ.

Här är målet att ***ta bort*** de ActiveMQ-speciella egenskaperna från `application.properties` filen.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

***Lägg sedan till*** Service Bus vissa egenskaper i `application.properties` filen.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Ersätt ActiveMQConnectionFactory med ServiceBusJmsConnectionFactory

Nästa steg är att ersätta instansen av ActiveMQConnectionFactory med ServiceBusJmsConnectionFactory.

> [!NOTE] 
> De faktiska kod ändringarna är specifika för programmet och hur beroenden hanteras, men exemplet nedan ger vägledning om ***vad*** som ska ändras.
>

Tidigare kan du instansiera ett objekt av ActiveMQConnectionFactory enligt nedan.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Detta skulle ändras till instansiera ett objekt i ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Efter migreringen

Nu när du har ändrat programmet för att börja skicka och ta emot meddelanden från Azure Service Bus bör du kontrol lera att det fungerar som förväntat. När du är klar kan du fortsätta med att förfina och modernisera din programs tack.

## <a name="next-steps"></a>Nästa steg

Dra nytta av [våren Boot starter för Azure Service Bus JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) för sömlös integrering med Azure Service Bus.

Mer information om Service Bus Messaging och Java Message Service (JMS) finns i följande avsnitt:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
