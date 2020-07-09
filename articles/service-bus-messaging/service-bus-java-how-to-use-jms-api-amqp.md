---
title: Använda AMQP med Java Message Service API & Azure Service Bus
description: Så här använder du JMS (Java Message Service) med Azure Service Bus och Advanced Message Queueing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119166"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Använda Java Message Service (JMS) med Azure Service Bus och AMQP 1,0

Stöd för **Advanced Message Queueing Protocol (AMQP) 1,0** -protokollet i Azure Service Bus innebär att du kan använda funktionerna för köer och publicera/prenumerera på asynkrona meddelanden från en uppsättning plattformar med hjälp av ett effektivt binärt protokoll. Dessutom kan du skapa program som består av komponenter som skapats med en blandning av språk, ramverk och operativ system.

Den här artikeln förklarar hur du använder Azure Service Bus meddelande funktioner (köer och publicera/prenumerera på ämnen) från Java-program med hjälp av JMS-API: t **(populära Java Message Service)** i AMQP-protokollet.

## <a name="get-started-with-service-bus"></a>Kom igång med Service Bus
Den här guiden förutsätter att du redan har ett Service Bus namnrum som innehåller en kö med namnet `basicqueue` . Om du inte gör det kan du [skapa namn området och kön](service-bus-create-namespace-portal.md) med hjälp av [Azure Portal](https://portal.azure.com). Mer information om hur du skapar Service Bus namnrymder och köer finns i [komma igång med Service Bus köer](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partitionerade köer och ämnen stöder också AMQP. Mer information finns i [partitionerade meddelande enheter](service-bus-partitioning.md) och [AMQP 1,0-stöd för Service Bus partitionerade köer och ämnen](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
>

## <a name="what-jms-features-are-supported"></a>Vilka JMS-funktioner stöds?

Här följer de JMS-funktioner som stöds i Azure Service Bus.

| Funktioner | Azure Service Bus standard-nivå – JMS 1,1 | Azure Service Bus Premium-nivå – JMS 2,0 (för hands version) |
|---|---|---|
| Skapa entiteter automatiskt över AMQP | Stöds inte | **Stöds** |
| Köer | **Stöds** | **Stöds** |
| Ämnen | **Stöds** | **Stöds** |
| Tillfälliga köer | Stöds inte <br/> (Skapa en vanlig kö med *AutoDeleteOnIdle* set i stället) | **Stöds** |
| Tillfälliga ämnen | Stöds inte | **Stöds** |
| Meddelande väljare | Stöds inte | **Stöds** |
| Köa webbläsare | Stöds inte <br/> (Använd *gransknings* funktionerna i Service Bus API) | **Stöds** |
| Delade varaktiga prenumerationer | **Stöds** | **Stöds**|
| Delade varaktiga prenumerationer | Stöds inte | **Stöds** |
| Delade icke-varaktiga prenumerationer | Stöds inte | **Stöds** |
| Ej delade icke-varaktiga prenumerationer | Stöds inte | **Stöds** |
| Avbryt prenumerationen för varaktiga prenumerationer | Stöds inte | **Stöds** |
| ReceiveNoWait | Stöds inte | **Stöds** |
| Distribuerade transaktioner | Stöds inte | Stöds inte |
| Varaktiga upphör | Stöds inte | Stöds inte |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Ytterligare varningar för Service Bus standard nivån
Endast en **MessageProducer** eller **MessageConsumer** tillåts per **session**. Om du behöver skapa flera **MessageProducers** eller **MessageConsumers** i ett program skapar du en särskild **session** för var och en av dem.

## <a name="downloading-the-java-message-service-jms-client-library"></a>Hämta klient biblioteket Java Message Service (JMS)

För att ansluta med Azure Service Bus och utnyttja API: t för Java-JMS (Java Message Service) över AMQP, måste du använda de här biblioteken. De måste läggas till i build-sökvägen med hjälp av det prioriterade beroende hanterings verktyget för ditt projekt.

Klient biblioteket som krävs beror på vilken pris nivå som används.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Premium-nivå – JMS 2,0 över AMQP (för hands version)

Om du vill använda alla förhands gransknings funktioner som är tillgängliga på Azure Service Bus Premium-nivån använder du [Azure-Service Bus-JMS-](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) biblioteket.

### <a name="standard-tier---jms-11-over-amqp"></a>Standard-nivå – JMS 1,1 över AMQP

Använd de JMS-funktioner som stöds av Service Bus standard-nivån (se [vilka JMS-funktioner som stöds?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)) Använd följande bibliotek:

* [Geronimo JMS 1,1-specifikation](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Qpid JMS-klient](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> JMS JAR-namn och-versioner kan ha ändrats. Mer information finns i [QPID JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Koda Java-program

När beroenden har importer ATS kan Java-programmen skrivas i en JMS-Provider oberoende-metod.

Eftersom Azure Service Bus standard och Premium skiljer sig i beroenden och antalet JMS-funktioner som de stöder, är programmerings modellen något annorlunda för de två.

> [!IMPORTANT]
> I guiden nedan visas hur du ansluter till Azure Service Bus med ett enkelt program.
>
> Med tanke på att de flesta företags program arkitekturer kan ha ett anpassat sätt att hantera beroenden och konfigurationer, använder du nedan som en vägledning för att förstå vad som behövs och hur du anpassar dig till programmet på lämpligt sätt.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Ansluta till Azure Service Bus med JMS

Om du vill ansluta med Azure Service Bus med JMS-klienter behöver du **ConnectionString** som är tillgänglig i "Shared Access policies" i [Azure Portal](https://portal.azure.com) under **primär anslutnings sträng**.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Ansluta till Azure Service Bus Premium över JMS 2,0 (för hands version)

1. Instansiera`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instansiera `ServiceBusJmsConnectionFactory` med lämplig `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Använd `ConnectionFactory` för att antingen skapa en `Connection` och sedan en`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    eller en `JMSContext` (för JMS 2,0-klienter)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Ansluta till Azure Service Bus standard över JMS 1,1

1. Infoga Azure Service Bus konfiguration i JNDI egenskaps fil med namnet **Service Bus. Properties**.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. Konfigurera JNDI-kontext och konfigurera ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Använd `ConnectionFactory` för att skapa en `Connection` och en `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>Skriv JMS-programmet

När `Session` eller `JMSContext` har instansierats kan ditt program använda välkända JMS-API: er för att utföra både hanterings-och data åtgärder.

Se listan över [JMS funktioner som stöds](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) för både standard-och Premium nivån för att se vilka API: er som stöds på varje nivå.

## <a name="summary"></a>Sammanfattning
Den här guiden demonstrerade hur Java-klientprogram som använder Java Message Service (JMS) över AMQP 1,0 kan interagera med Azure Service Bus.

Du kan också använda Service Bus AMQP 1,0 från andra språk, inklusive .NET, C, python och PHP. Komponenter som skapats med dessa olika språk kan utbyta meddelanden på ett tillförlitligt sätt och med fullständig åter givning med AMQP 1,0-stödet i Service Bus.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Service Bus och information om JMS-enheter (Java Message Service) finns i länkarna nedan – 
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java Message Service-entiteter](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [AMQP 1,0-stöd i Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 Developer ' s guide](service-bus-amqp-dotnet.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)

