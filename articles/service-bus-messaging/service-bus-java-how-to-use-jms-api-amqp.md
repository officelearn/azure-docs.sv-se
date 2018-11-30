---
title: Hur du använder AMQP 1.0 med Service Bus-API för Java | Microsoft Docs
description: Hur du använder Java Message Service (JMS) med Azure Service Bus och AMQP Advanced Message Queuing Protocol () 1.0.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2018
ms.author: spelluru
ms.openlocfilehash: fbd74b227afd2191616100d74c7864eacf015add
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308078"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Hur du använder Java Message Service (JMS) API med Service Bus och AMQP 1.0
AMQP Advanced Message Queuing Protocol () 1.0 är ett effektivt, pålitligt meddelandeprotokoll på trådnivå som du kan använda för att skapa robusta och plattformsöverskridande meddelandeprogram.

Stöd för AMQP 1.0 i Service Bus innebär att du kan använda den queuing och publicera/prenumerera på asynkrona meddelandefunktioner från flera olika plattformar med en effektiv binär-protokollet. Dessutom kan du skapa program som består av komponenter som skapats med en blandning av språk, ramverk och operativsystem.

Den här artikeln förklarar hur du använder Service Bus-meddelandefunktioner (köer och publicera/prenumerera på ämnen) från Java-program med den populära Java Message Service JMS () API som standard. Det finns en [tillhörande artikeln](service-bus-amqp-dotnet.md) som förklarar hur du gör samma sak med hjälp av Service Bus .NET API. Du kan använda guiderna två tillsammans att lära dig om plattformar med AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Kom igång med Service Bus
Den här guiden förutsätter att du redan har ett Service Bus-namnområde som innehåller en kö med namnet **basicqueue**. Om du inte gör det, kan du [skapa namnområde och kö](service-bus-create-namespace-portal.md) med hjälp av den [Azure-portalen](https://portal.azure.com). Mer information om hur du skapar Service Bus-namnområden och -köer finns i [Kom igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partitionerade köer och ämnen också stöd för AMQP. Mer information finns i [partitionerade meddelandeentiteter](service-bus-partitioning.md) och [AMQP 1.0-stöd för Service Bus partitionerade köer och ämnen](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Hämta klientbiblioteket AMQP 1.0 JMS
Information om var du kan hämta den senaste versionen av Apache Qpid JMS AMQP 1.0-klientbiblioteket finns [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Du måste lägga till följande fyra JAR-filer från arkivet för Apache Qpid JMS AMQP 1.0-distribution Java-KLASSÖKVÄGEN när du bygger och kör JMS program med Service Bus:

* geronimo jms\_1.1\_spec 1.0.jar
* qpid-jms - client-[version] .jar

> ! [OBS] JMS JAR namn och versioner kan ha ändrats. Mer information finns i [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Koda i Java-program
### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directory Interface JNDI)
JMS använder Java Naming and Directory Interface JNDI () för att skapa en åtskillnad mellan namn på logisk och fysisk. Två typer av JMS objekt löses med hjälp av JNDI: ConnectionFactory och mål. JNDI använder en providermodell där du ansluter olika katalogtjänster för att hantera name resolution uppgifter. Apache Qpid JMS AMQP 1.0 biblioteket innehåller en enkel egenskaper filbaserade JNDI providern som har konfigurerats med en egenskapsfil med följande format:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Konfigurera JNDI kontext och konfigurera ConnectionFactory

Den **ConnectionString** refereras till i version som är tillgänglig i den ”delade åtkomstprinciper” i den [Azure-portalen](https://portal.azure.com) under **primär anslutningssträng**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurera producent och konsument målköer
Posten som används för att definiera ett mål i Qpid egenskaper filprovidern JNDI har följande format:

Skapa målkön för producenten- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Skapa en målkö för konsument- 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Skriva JMS-program
Det finns inga särskilda API: er eller alternativ som krävs när du använder JMS med Service Bus. Det finns dock några begränsningar som beskrivs senare. Som med alla JMS-program är de nödvändiga först öppna konfiguration av JNDI miljön, för att kunna lösa en **ConnectionFactory** och mål.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurera JNDI InitialContext
JNDI miljön är konfigurerad genom att skicka en hashtabell med konfigurationsinformation till konstruktören av klassen javax.naming.InitialContext. Två obligatoriska element i hash-tabellen är klassnamnet för den inledande kontext fabriken och Provider-URL: en. Följande kod visar hur du konfigurerar JNDI miljö om du vill använda Qpid egenskaper för filbaserad JNDI Provider med en egenskapsfil med namnet **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Ett enkelt JMS program med hjälp av en Service Bus-kö
Följande exempelprogrammet skickar JMS TextMessages till en Service Bus-kö med det logiska namnet JNDI för KÖN och tar emot meddelanden igen.

Du hittar alla att all källkod kod och konfiguration information från den [Azure Service Bus-exempel JMS kö Snabbstart](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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
Skicka den **Connection String** från den delade åtkomstprinciper för att köra programmet.
Nedan visas resultatet av formuläret genom att köra programmet:

```
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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP disposition och Service Bus åtgärden mappning
Här är hur en AMQP-disposition översätts till en Service Bus-åtgärd:

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```


## <a name="unsupported-features-and-restrictions"></a>Funktioner som inte stöds och begränsningar
Följande begränsningar gäller när du använder JMS över AMQP 1.0 med Service Bus, nämligen:

* Endast en **MessageProducer** eller **MessageConsumer** tillåts per **Session**. Om du vill skapa flera **MessageProducers** eller **MessageConsumers** i ett program, skapa en dedikerad **Session** för respektive scenario.
* Föränderliga ämnesprenumerationer stöds inte för närvarande.
* **MessageSelectors** stöds inte för närvarande.
* Överförda sessioner och distribuerade transaktioner stöds inte.

Dessutom Azure Service Bus delar upp kontrollplanet från dataplanet och därför stöd inte för flera JMSS topologi för dynamiska funktioner:

| Metod som inte stöds          | Ersätt med                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Skapa en ämnesprenumeration porta meddelande-väljare                                 |
| createDurableConsumer       | Skapa en ämnesprenumeration porta meddelande-väljare                                 |
| createSharedConsumer        | Service Bus-ämnen är alltid delbart, se ovan                                       |
| createSharedDurableConsumer | Service Bus-ämnen är alltid delbart, se ovan                                       |
| createTemporaryTopic        | Skapa ett ämne via hantering av API/tools/portalen med *AutoDeleteOnIdle* inställd på en förfalloperiod |
| createTopic                 | Skapa ett ämne via hantering av API-tools-portalen                                           |
| Avbryt prenumeration                 | ta bort avsnittet management API-tools-portalen                                             |
| createBrowser               | som inte stöds. Använd Peek() funktionerna i Service Bus-API                         |
| createQueue                 | Skapa en kö via hantering av API-tools-portalen                                           | 
| createTemporaryQueue        | Skapa en kö via hantering av API/tools/portalen med *AutoDeleteOnIdle* inställd på en förfalloperiod |

## <a name="summary"></a>Sammanfattning
I den här guiden visar hur du använder tjänsten hanterade meddelandefunktioner Bus (köer och publicera/prenumerera på ämnen) från Java med hjälp av populära JMS API och AMQP 1.0.

Du kan också använda Service Bus AMQP 1.0 från andra språk, inklusive .NET, C, Python och PHP. Komponenter som skapats med hjälp av dessa olika språk kan utbyta meddelanden på ett tillförlitligt sätt och vid fullständig exakthet med hjälp av AMQP 1.0 stöder i Service Bus.

## <a name="next-steps"></a>Nästa steg
* [Stöd för AMQP 1.0 i Azure Service Bus](service-bus-amqp-overview.md)
* [Hur du använder AMQP 1.0 med Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 Guide för utvecklare](service-bus-amqp-dotnet.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Java-utvecklingscenter](https://azure.microsoft.com/develop/java/)

