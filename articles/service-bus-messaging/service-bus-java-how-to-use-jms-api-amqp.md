---
title: Använda AMQP med Java Message Service API & Azure Service Bus
description: Så här använder du Java Message Service (JMS) med Azure Service Bus och AMQP (Advanced Message Queuing Protocol) 1.0.
services: service-bus-messaging
documentationcenter: java
author: axisc
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: cd06838abbb69af5684fdea18c42f6a8f95ffe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371265"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Använda Java Message Service (JMS) med Azure Service Bus och AMQP 1.0
I den här artikeln beskrivs hur du använder Azure Service Bus-meddelandefunktioner (köer och publicerings-/prenumerationsämnen) från Java-program med den populära JAVA Message Service-API-standarden (Java Message Service). Det finns en [kompletterande artikel](service-bus-amqp-dotnet.md) som förklarar hur du gör samma sak med Azure Service Bus .NET API. Du kan använda dessa två guider tillsammans för att lära dig mer om plattformsoberoende meddelanden med AMQP 1.0.

Advanced Message Queuing Protocol (AMQP) 1.0 är ett effektivt, tillförlitligt meddelandeprotokoll på trådnivå som du kan använda för att skapa robusta, plattformsoberoende meddelandeprogram.

Stöd för AMQP 1.0 i Azure Service Bus innebär att du kan använda funktioner för kö- och publicering/prenumererande med förmedlade meddelandetjänster från en rad plattformar med hjälp av ett effektivt binärt protokoll. Dessutom kan du skapa program som består av komponenter byggda med en blandning av språk, ramverk och operativsystem.

## <a name="get-started-with-service-bus"></a>Kom igång med Service Bus
Den här guiden förutsätter att du redan har `basicqueue`ett servicebussnamnområde som innehåller en kö med namnet . Om du inte gör det kan du [skapa namnområdet och kön](service-bus-create-namespace-portal.md) med [Azure-portalen](https://portal.azure.com). Mer information om hur du skapar servicebussnamnområden och köer finns i [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partitionerade köer och ämnen stöder också AMQP. Mer information finns i [Partitionerade meddelandeentiteter](service-bus-partitioning.md) och [AMQP 1.0-stöd för delade köer och ämnen för Service Bus](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Hämta JMS-klientbiblioteket AMQP 1.0
Information om var du kan hämta den senaste versionen av Apache Qpid JMS [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)AMQP 1.0-klientbiblioteket finns i .

Du måste lägga till följande fyra JAR-filer från Apache Qpid JMS AMQP 1.0-distributionsarkivet i Java CLASSPATH när du skapar och kör JMS-program med Service Bus:

* geronimo-jms\_1,1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS JAR namn och versioner kan ha ändrats. Mer information finns i [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kodning Java-program
### <a name="java-naming-and-directory-interface-jndi"></a>Java-namngivning och kataloggränssnitt (JNDI)
JMS använder JNDI (Java Naming and Directory Interface) för att skapa en separation mellan logiska namn och fysiska namn. Två typer av JMS-objekt matchas med JNDI: ConnectionFactory och Destination. JNDI använder en leverantörsmodell där du kan ansluta olika katalogtjänster för att hantera namnmatchningsuppgifter. Apache Qpid JMS AMQP 1.0-biblioteket levereras med en enkel egenskapsfilbaserad JNDI-provider som är konfigurerad med hjälp av en egenskapsfil med följande format:

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

**ConnectionString** refererade i den som är tillgänglig i principerna för delad åtkomst i [Azure-portalen](https://portal.azure.com) under **Primär anslutningssträng**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Konfigurera köer för producent- och konsumentmål
Den post som används för att definiera ett mål i Qpid-egenskapsfilen JNDI-providern är av följande format:

Så här skapar du destinationskön för producent - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Så här skapar du en destinationskö för Konsument- 
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
Det finns inga särskilda API:er eller alternativ när du använder JMS med Service Bus. Det finns dock några begränsningar som kommer att täckas senare. Som med alla JMS-program är det första som krävs konfiguration av JNDI-miljön, för att kunna lösa en **ConnectionFactory** och destination.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurera JNDI InitialContext
JNDI-miljön konfigureras genom att skicka en hashtable av konfigurationsinformation till konstruktorn för klassen javax.naming.InitialContext. De två obligatoriska elementen i hashtable är klassnamnet för den ursprungliga kontextfabriken och provider-URL:en. Följande kod visar hur du konfigurerar JNDI-miljön för att använda Qpid-egenskapsfilen som är baserad på JNDI Provider med en egenskapsfil med namnet **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Ett enkelt JMS-program med hjälp av en servicebusskö
Följande exempelprogram skickar JMS-textmessages till en servicebusskö med JNDI logiska namnet PÅ QUEUE och tar emot meddelandena tillbaka.

Du kan alla komma åt all källkod och konfigurationsinformation från [snabbstarten för Azure Service Bus Samples JMS-kö](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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

### <a name="run-the-application"></a>Köra appen
Skicka **anslutningssträngen** från principerna för delad åtkomst för att köra programmet.
Nedan visas formulärets utdata genom att köra programmet:

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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP-disposition och servicebussoperationsmappning
Så här översätter en AMQP-disposition till en Service Bus-åtgärd:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS-ämnen kontra servicebussavsnitt
Med hjälp av Azure Service Bus ämnen och prenumerationer via Java Message Service (JMS) API ger grundläggande skicka och ta emot funktioner. Det är ett bekvämt val när du porterar program från andra meddelandemäklare med JMS-kompatibla API:er, även om Service Bus-ämnen skiljer sig från JMS-ämnen och kräver några justeringar. 

Azure Service Bus-ämnen dirigerar meddelanden till namngivna, delade, varaktiga prenumerationer som hanteras via Azure Resource Management-gränssnittet, Azure-kommandoradsverktygen eller via Azure-portalen. Varje prenumeration tillåter upp till 2000 urvalsregler, som var och en kan ha ett filtervillkor och, för SQL-filter, även en metadataomvandlingsåtgärd. Varje filtervillkorsmatchning väljer det indatameddelande som ska kopieras till prenumerationen.  

Att ta emot meddelanden från prenumerationer är identiskt med att ta emot meddelanden från köer. Varje prenumeration har en associerad kö för obeställbara meddelanden och möjligheten att automatiskt vidarebefordra meddelanden till en annan kö eller ämnen. 

Med JMS-ämnen kan klienter dynamiskt skapa icke-varaktiga och varaktiga prenumeranter som eventuellt tillåter filtrering av meddelanden med meddelandeväljare. Dessa odelade entiteter stöds inte av Service Bus. Syntaxen för SQL-filterregeln för Service Bus liknar dock den meddelandeväljaren som stöds av JMS. 

Utgivningssidan för JMS-ämne är kompatibel med Service Bus, som visas i det här exemplet, men inte dynamiska prenumeranter. Följande topologi-relaterade JMS API:er stöds inte med Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Funktioner och begränsningar som inte stöds
Följande begränsningar finns när JMS använders över AMQP 1.0 med Service Bus, nämligen:

* Endast en **MessageProducer** eller **MessageConsumer tillåts** per **session**. Om du behöver skapa flera **MessageProducers** eller **MessageConsumers** i ett program skapar du en dedikerad **session** för var och en av dem.
* Flyktiga ämnesprenumerationer stöds för närvarande inte.
* **MessageSelectors** stöds för närvarande inte.
* Distribuerade transaktioner stöds inte (men genomförda sessioner stöds).

Dessutom delar Azure Service Bus kontrollplanet från dataplanet och stöder därför inte flera av JMS dynamiska topologifunktioner:

| Metod som inte stöds          | Ersätt med                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| skapaDurableSubscriber     | skapa en ämnesprenumeration som porterar meddelandeväljaren                                 |
| skapaDurableConsumer       | skapa en ämnesprenumeration som porterar meddelandeväljaren                                 |
| skapaDeldConsumer        | Service Bus ämnen är alltid delbara, se ovan                                       |
| skapaDeladVarligKonsumer | Service Bus ämnen är alltid delbara, se ovan                                       |
| skapaTemporaryTopic        | skapa ett ämne via hantering API / verktyg / portal med *AutoDeleteOnIdle* inställd på en förfalloperiod |
| createTopic                 | skapa ett ämne via hantering API / verktyg / portal                                           |
| Unsubscribe                 | ta bort API:et för ämneshantering/verktyg/portal                                             |
| skapaBrowser               | Unsupported. Använda peek()-funktionen i Service Bus API                         |
| skapaQueue                 | skapa en kö via hantering API /tools/portal                                           | 
| skapaTemporaryQueue        | skapa en kö via hanterings-API/verktyg/portal med *AutoDeleteOnIdle* inställd på en förfalloperiod |
| ta emotNoWait               | använda metoden receive() som tillhandahålls av Service Bus SDK och ange en mycket låg eller noll timeout |

## <a name="summary"></a>Sammanfattning
Den här guiden visade hur du använder servicebussmässade meddelandefunktioner (köer och publicering/prenumerera ämnen) från Java med det populära JMS API och AMQP 1.0.

Du kan också använda Service Bus AMQP 1.0 från andra språk, inklusive .NET, C, Python och PHP. Komponenter som skapats med dessa olika språk kan utbyta meddelanden på ett tillförlitligt sätt och med full återgivning med hjälp av AMQP 1.0-stödet i Service Bus.

## <a name="next-steps"></a>Nästa steg
* [AMQP 1.0-stöd i Azure Service Bus](service-bus-amqp-overview.md)
* [Så här använder du AMQP 1.0 med Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 Utvecklarguide](service-bus-amqp-dotnet.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Java-utvecklingscenter](https://azure.microsoft.com/develop/java/)

