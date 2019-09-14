---
title: Använd AMQP 1,0 med Java Message Service API och Azure Service Bus
description: Så här använder du JMS (Java Message Service) med Azure Service Bus och Advanced Message Queueing Protocol (AMQP) 1,0.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9dff2cc11b71f314de81fd99ed3b72c6337d977f
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967969"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Använda Java Message Service (JMS) med Azure Service Bus och AMQP 1,0
Den här artikeln förklarar hur du använder Azure Service Bus meddelande funktioner (köer och publicera/prenumerera ämnen) från Java-program med hjälp av den populära API-standarden Java Message Service (JMS). Det finns en [medföljande artikel](service-bus-amqp-dotnet.md) som förklarar hur du gör detta med hjälp av Azure Service Bus .NET API. Du kan använda dessa två guider tillsammans för att lära dig mer om plattforms oberoende meddelanden med AMQP 1,0.

Advanced Message Queueing Protocol (AMQP) 1,0 är ett effektivt, tillförlitligt meddelande protokoll på trådnivå som du kan använda för att bygga robusta program för meddelanden mellan plattformar.

Stöd för AMQP 1,0 i Azure Service Bus innebär att du kan använda funktionerna för kö-och publicerings-och prenumerations tjänster från en uppsättning plattformar med hjälp av ett effektivt binärt protokoll. Dessutom kan du skapa program som består av komponenter som skapats med en blandning av språk, ramverk och operativ system.

## <a name="get-started-with-service-bus"></a>Kom igång med Service Bus
Den här guiden förutsätter att du redan har ett Service Bus namnrum som innehåller en kö med namnet **basicqueue**. Om du inte gör det kan du [skapa namn området och kön](service-bus-create-namespace-portal.md) med hjälp av [Azure Portal](https://portal.azure.com). Mer information om hur du skapar Service Bus namnrymder och köer finns i [komma igång med Service Bus köer](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partitionerade köer och ämnen stöder också AMQP. Mer information finns i [partitionerade meddelande enheter](service-bus-partitioning.md) och [AMQP 1,0-stöd för Service Bus partitionerade köer och ämnen](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Hämta klient biblioteket AMQP 1,0 JMS
Information om var du hämtar den senaste versionen av Apache qpid JMS AMQP 1,0-klient biblioteket finns på [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Du måste lägga till följande fyra JAR-filer från Apache qpid JMS AMQP 1,0 distribution Archive till Java-CLASSPATH när du skapar och kör JMS-program med Service Bus:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS JAR-namn och-versioner kan ha ändrats. Mer information finns i [QPID JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Koda Java-program
### <a name="java-naming-and-directory-interface-jndi"></a>Java-namngivning och katalog gränssnitt (JNDI)
JMS använder Java-namn och katalog gränssnitt (JNDI) för att skapa en separation mellan logiska namn och fysiska namn. Två typer av JMS-objekt löses med JNDI: ConnectionFactory och mål. JNDI använder en leverantörs modell där du kan koppla olika katalog tjänster för att hantera namn matchnings uppgifter. Apache qpid JMS AMQP 1,0-biblioteket innehåller en filbaserad JNDI-Provider med enkel egenskap som har kon figurer ATS med hjälp av en egenskaps fil med följande format:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Konfigurera JNDI-kontext och konfigurera ConnectionFactory

**ConnectionString** som refereras till i den som är tillgänglig i "Shared Access policies" i [Azure-portalen](https://portal.azure.com) under **primär anslutnings sträng**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurera producent-och konsument målköer
Posten som används för att definiera ett mål i qpid-JNDI provider har följande format:

Så här skapar du målkön för producenten – 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Så här skapar du en målkö för konsumenten – 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Skriv JMS-programmet
Det finns inga särskilda API: er eller alternativ som krävs när du använder JMS med Service Bus. Det finns dock några begränsningar som kommer att täckas senare. Precis som med alla JMS-program är det första som krävs konfiguration av JNDI-miljön för att kunna matcha en **ConnectionFactory** och mål.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurera JNDI-InitialContext
JNDI-miljön konfigureras genom att skicka en hash av konfigurations information till konstruktorn för klassen javax. Naming. InitialContext. De två nödvändiga elementen i hash-tabellen är klass namnet för den inledande kontext fabriken och providerns URL. Följande kod visar hur du konfigurerar JNDI-miljön så att den använder qpid Properties-JNDI providern med en egenskaps fil med namnet **Service Bus. Properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Ett enkelt JMS-program med hjälp av en Service Bus kö
I följande exempel program skickas JMS-TextMessages till en Service Bus kö med JNDI logiska namn för kön och tar emot meddelanden tillbaka.

Du kan all åtkomst till all käll kod och konfigurations information från [Azure Service Bus samples JMS Queue Snabbstart](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Köra programmet
Skicka **anslutnings strängen** från principerna för delad åtkomst för att köra programmet.
Nedan visas utdata från formuläret genom att köra programmet:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP disposition och Service Bus åtgärds mappning
Här följer hur en AMQP disposition översätts till en Service Bus-åtgärd:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS ämnen jämfört med Avsnitt om Service Bus
Med hjälp av Azure Service Bus ämnen och prenumerationer via API: t Java Message Service (JMS) får du grundläggande funktioner för att skicka och ta emot. Det är ett bekvämt val när du ska Porta program från andra meddelande hanterare med JMS-kompatibla API: er, även om Service Bus ämnen skiljer sig från JMS ämnen och kräver några justeringar. 

Azure Service Bus ämnen dirigerar meddelanden till namngivna, delade, varaktiga prenumerationer som hanteras via Azure Resource Management-gränssnittet, Azures kommando rads verktyg eller via Azure Portal. Varje prenumeration tillåter upp till 2000 urvals regler, som var och en kan ha ett filter villkor och, för SQL-filter, även en åtgärd för metadata-omvandling. Varje filter villkors matchning väljer det indatameddelande som ska kopieras till tehj-prenumerationen.  

Att ta emot meddelanden från prenumerationer är identiskt med att ta emot meddelanden från köer. Varje prenumeration har en associerad kö för obeställbara meddelanden samt möjligheten att automatiskt vidarebefordra meddelanden till en annan kö eller ämnen. 

Med JMS ämnen kan klienter dynamiskt skapa invaraktiga och varaktiga prenumeranter som kan filtrera meddelanden med meddelande väljare. Dessa delade entiteter stöds inte av Service Bus. Syntaxen för SQL filter-regeln för Service Bus är dock mycket lik syntaxen för meddelande väljaren som stöds av JMS. 

JMS-avsnittets Publisher-sida är kompatibel med Service Bus, vilket visas i det här exemplet, men dynamiska prenumeranter är inte det. Följande topologi-relaterade JMS-API: er stöds inte med Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Funktioner och begränsningar som inte stöds
Följande begränsningar föreligger när du använder JMS över AMQP 1,0 med Service Bus, nämligen:

* Endast en **MessageProducer** eller **MessageConsumer** tillåts per **session**. Om du behöver skapa flera **MessageProducers** eller **MessageConsumers** i ett program skapar du en särskild **session** för var och en av dem.
* Beständiga ämnes prenumerationer stöds inte för närvarande.
* **MessageSelectors** stöds inte för närvarande.
* Överförda sessioner och distribuerade transaktioner stöds inte.

Dessutom, Azure Service Bus delar kontroll planet från data planet och stöder därför inte flera av JMS funktioner i dynamisk topologi:

| Metoden stöds inte          | Ersätt med                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | skapa en ämnes prenumerations port för meddelande väljaren                                 |
| createDurableConsumer       | skapa en ämnes prenumerations port för meddelande väljaren                                 |
| createSharedConsumer        | Service Bus ämnen kan alltid delas, se ovan                                       |
| createSharedDurableConsumer | Service Bus ämnen kan alltid delas, se ovan                                       |
| createTemporaryTopic        | skapa ett ämne via hanterings-API/verktyg/Portal med *AutoDeleteOnIdle* inställd på en förfallo period |
| createTopic                 | skapa ett ämne via hanterings-API/verktyg/Portal                                           |
| avbryta prenumerationen                 | ta bort API/verktyg/Portal för ämnes hantering                                             |
| createBrowser               | Som inte stöds. Använd funktionerna Peek () i Service Bus-API: et                         |
| createQueue                 | skapa en kö via hanterings-API/verktyg/Portal                                           | 
| createTemporaryQueue        | skapa en kö via hanterings-API/verktyg/Portal med *AutoDeleteOnIdle* inställd på en förfallo period |
| receiveNoWait               | Använd metoden receive () som tillhandahålls av Service Bus SDK och ange en väldigt låg eller noll tids gräns |

## <a name="summary"></a>Sammanfattning
Den här instruktions guiden visar hur du använder Service Bus Brokered Messaging-funktioner (köer och publicera/prenumerera ämnen) från Java med hjälp av det populära JMS-API: et och AMQP 1,0.

Du kan också använda Service Bus AMQP 1,0 från andra språk, inklusive .NET, C, python och PHP. Komponenter som skapats med dessa olika språk kan utbyta meddelanden på ett tillförlitligt sätt och med fullständig åter givning med AMQP 1,0-stödet i Service Bus.

## <a name="next-steps"></a>Nästa steg
* [AMQP 1,0-stöd i Azure Service Bus](service-bus-amqp-overview.md)
* [Använda AMQP 1,0 med Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1,0 Developer ' s guide](service-bus-amqp-dotnet.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Java-utvecklingscenter](https://azure.microsoft.com/develop/java/)

