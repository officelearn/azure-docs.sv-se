---
title: Hur du använder AMQP 1.0 med Service Bus-API för Java | Microsoft Docs
description: Hur du använder Java Message Service (JMS) med Azure Service Bus och Message Queuing Protodol AMQP (Advanced) 1.0.
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
ms.openlocfilehash: a3274053e772cbdf120be15a385c84d5ae37d610
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392660"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Hur du använder Java Message Service (JMS) API med Service Bus och AMQP 1.0
AMQP Advanced Message Queuing Protocol () 1.0 är ett effektivt, pålitligt meddelandeprotokoll på trådnivå som du kan använda för att skapa robusta och plattformsöverskridande meddelandeprogram.

Stöd för AMQP 1.0 i Service Bus innebär att du kan använda den queuing och publicera/prenumerera på asynkrona meddelandefunktioner från flera olika plattformar med en effektiv binär-protokollet. Dessutom kan du skapa program som består av komponenter som skapats med en blandning av språk, ramverk och operativsystem.

Den här artikeln förklarar hur du använder Service Bus-meddelandefunktioner (köer och publicera/prenumerera på ämnen) från Java-program med den populära Java Message Service JMS () API som standard. Det finns en [tillhörande artikeln](service-bus-amqp-dotnet.md) som förklarar hur du gör samma sak med hjälp av Service Bus .NET API. Du kan använda guiderna två tillsammans att lära dig om plattformar med AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Kom igång med Service Bus
Den här guiden förutsätter att du redan har ett Service Bus-namnområde som innehåller en kö med namnet **Kö1**. Om du inte gör det, kan du [skapa namnområde och kö](service-bus-create-namespace-portal.md) med hjälp av den [Azure-portalen](https://portal.azure.com). Mer information om hur du skapar Service Bus-namnområden och -köer finns i [Kom igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partitionerade köer och ämnen också stöd för AMQP. Mer information finns i [partitionerade meddelandeentiteter](service-bus-partitioning.md) och [AMQP 1.0-stöd för Service Bus partitionerade köer och ämnen](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Hämta klientbiblioteket AMQP 1.0 JMS
Information om var du kan hämta den senaste versionen av Apache Qpid JMS AMQP 1.0-klientbiblioteket finns [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Du måste lägga till följande fyra JAR-filer från arkivet för Apache Qpid JMS AMQP 1.0-distribution Java-KLASSÖKVÄGEN när du bygger och kör JMS program med Service Bus:

* geronimo jms\_1.1\_spec 1.0.jar
* qpid-amqp-1-0-Client-[version].JAR
* qpid-amqp-1-0-Client-jms-[version].JAR
* qpid-amqp-1-0-Common-[version].JAR

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

#### <a name="configure-the-connectionfactory"></a>Konfigurera ConnectionFactory
Posten som används för att definiera en **ConnectionFactory** i filen Qpid egenskaper JNDI-provider är följande format:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Där **[jndi_name]** och **[ConnectionURL]** följande:

* **[jndi_name]** : Det logiska namnet för ConnectionFactory. Det här är det namn som kommer att lösas i Java-program med hjälp av metoden JNDI IntialContext.lookup().
* **[ConnectionURL]** : En URL som innehåller JMS-bibliotek med den information som krävs till AMQP meddelandekön.

Formatet för den **ConnectionURL** är följande:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Där **[namnutrymme]**, **[SASPolicyName]** och **[SASPolicyKey]** följande:

* **[namnutrymme]** : Service Bus-namnområde.
* **[SASPolicyName]** : Namn på den kö signatur för delad åtkomst.
* **[SASPolicyKey]** : Principnyckel för den kön signatur för delad åtkomst.

> [!NOTE]
> Du måste URL-koda lösenordet manuellt. Det finns ett bra verktyg för URL-kodning på [ http://www.w3schools.com/tags/ref_urlencode.asp ](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Konfigurera mål
Posten som används för att definiera ett mål i Qpid egenskaper filprovidern JNDI har följande format:

```
queue.[jndi_name] = [physical_name]
```

eller

```
topic.[jndi_name] = [physical_name]
```

Där **[jndi\_namn]** och **[fysiska\_namn]** följande:

* **[jndi_name]** : Det logiska namnet för målet. Det här är det namn som kommer att lösas i Java-program med hjälp av metoden JNDI IntialContext.lookup().
* **[physical_name]** : Namnet på Service Bus-entiteten som programmet skickar eller tar emot meddelanden.

> [!NOTE]
> När du tar emot från en ämnesprenumeration för Service Bus, bör fysiska namnet i JNDI vara namnet på ämnet. Prenumerationens namn tillhandahålls när varaktiga prenumeration skapas i programkoden JMS. Den [Service Bus AMQP 1.0 Developer's Guide](service-bus-amqp-dotnet.md) innehåller mer information om hur du arbetar med Service Bus-ämnen från JMS.
> 
> 

### <a name="write-the-jms-application"></a>Skriva JMS-program
Det finns inga särskilda API: er eller alternativ som krävs när du använder JMS med Service Bus. Det finns dock några begränsningar som beskrivs senare. Som med alla JMS-program är de nödvändiga först öppna konfiguration av JNDI miljön, för att kunna lösa en **ConnectionFactory** och mål.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurera JNDI InitialContext
JNDI miljön är konfigurerad genom att skicka en hashtabell med konfigurationsinformation till konstruktören av klassen javax.naming.InitialContext. Två obligatoriska element i hash-tabellen är klassnamnet för den inledande kontext fabriken och Provider-URL: en. Följande kod visar hur du konfigurerar JNDI miljö om du vill använda Qpid egenskaper för filbaserad JNDI Provider med en egenskapsfil med namnet **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Ett enkelt JMS program med hjälp av en Service Bus-kö
Följande exempelprogrammet skickar JMS TextMessages till en Service Bus-kö med det logiska namnet JNDI för KÖN och tar emot meddelanden igen.

```java
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}    
```

### <a name="run-the-application"></a>Köra programmet
Kör programmet ger utdata i formatet:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Plattformsoberoende meddelanden mellan JMS och .NET
Den här guiden visades hur du skickar och tar emot meddelanden till och från Service Bus med hjälp av JMS. En av de främsta fördelarna med AMQP 1.0 är dock att det gör att program kan byggas komponenter som skrivits i olika språk, med meddelanden som utbyts på ett tillförlitligt och fullständig återgivning.

Med hjälp av JMS exempelprogrammet som beskrivs ovan och ett liknande .NET-program som kommer från en tillhörande artikel [med hjälp av Service Bus från .NET med AMQP 1.0](service-bus-amqp-dotnet.md), du kan utbyta meddelanden mellan .NET och Java. Den här artikeln för mer information om av plattformsoberoende meddelanden med hjälp av Service Bus och AMQP 1.0.

### <a name="jms-to-net"></a>JMS till .NET
Att visa JMS för .NET-meddelanden:

* Starta .NET-exempelprogram utan kommandoradsargument.
* Starta Java-exempelprogram med kommandoradsargumentet ”sendonly”. I det här läget kan programmet inte tar emot meddelanden från kön, skickas bara.
* Tryck på **RETUR** några gånger i konsolen Java-program, vilket gör att meddelanden ska skickas.
* Dessa meddelanden tas emot av .NET-program.

#### <a name="output-from-jms-application"></a>Utdata från JMS-program
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Utdata från .NET-program
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET för att JMS
Att visa .NET för JMS meddelanden:

* Börja .NET-exempelprogram med kommandoradsargumentet ”sendonly”. I det här läget kan programmet inte tar emot meddelanden från kön, skickas bara.
* Starta Java-exempelprogrammet utan kommandoradsargument.
* Tryck på **RETUR** några gånger i konsolen för .NET-program, vilket gör att meddelanden ska skickas.
* Dessa meddelanden tas emot av Java-program.

#### <a name="output-from-net-application"></a>Utdata från .NET-program
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Utdata från JMS-program
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Funktioner som inte stöds och begränsningar
Följande begränsningar gäller när du använder JMS över AMQP 1.0 med Service Bus, nämligen:

* Endast en **MessageProducer** eller **MessageConsumer** tillåts per **Session**. Om du vill skapa flera **MessageProducers** eller **MessageConsumers** i ett program, skapa en dedikerad **Session** för respektive scenario.
* Föränderliga ämnesprenumerationer stöds inte för närvarande.
* **MessageSelectors** stöds inte för närvarande.
* Tillfällig mål; till exempel **TemporaryQueue**, **TemporaryTopic** stöds inte för närvarande, tillsammans med den **QueueRequestor** och **TopicRequestor**API: er som använder dem.
* Överförda sessioner och distribuerade transaktioner stöds inte.

## <a name="summary"></a>Sammanfattning
I den här guiden visar hur du använder tjänsten hanterade meddelandefunktioner Bus (köer och publicera/prenumerera på ämnen) från Java med hjälp av populära JMS API och AMQP 1.0.

Du kan också använda Service Bus AMQP 1.0 från andra språk, inklusive .NET, C, Python och PHP. Komponenter som skapats med hjälp av dessa olika språk kan utbyta meddelanden på ett tillförlitligt sätt och vid fullständig exakthet med hjälp av AMQP 1.0 stöder i Service Bus.

## <a name="next-steps"></a>Nästa steg
* [Stöd för AMQP 1.0 i Azure Service Bus](service-bus-amqp-overview.md)
* [Hur du använder AMQP 1.0 med Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 Guide för utvecklare](service-bus-amqp-dotnet.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Java-utvecklingscenter](https://azure.microsoft.com/develop/java/)

