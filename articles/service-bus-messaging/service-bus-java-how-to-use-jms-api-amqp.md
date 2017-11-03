---
title: "Hur du använder AMQP 1.0 med Service Bus-API för Java | Microsoft Docs"
description: "Hur du använder Java meddelandet Service (JMS) med Azure Service Bus och avancerade Message Queuing Protodol (AMQP) 1.0."
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: 
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 0848facd764c4fb0d7f95c1ae89ecb02a32257e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Hur du använder Java meddelandet Service (JMS) API med Service Bus och AMQP 1.0
Den avancerade Message Queuing-protokollet (AMQP) 1.0 är ett effektivt, tillförlitlig och överföring nivå meddelanden protokoll som du kan använda för att bygga robusta, plattformsoberoende meddelandeprogram.

Stöd för AMQP 1.0 i Service Bus innebär att du kan använda den queuing och publicera/prenumerera asynkrona meddelandetjänsten funktioner mellan olika plattformar med en effektiv binär-protokollet. Dessutom kan du skapa program består av komponenter som skapats med en blandning av språk och ramverk operativsystem.

Den här artikeln förklarar hur du använder Service Bus-funktioner (köer och ämnen för Publicera/prenumerera)-meddelanden från Java-program med hjälp av populära Java meddelandet Service (JMS) API som standard. Det finns en [tillhörande artikel](service-bus-amqp-dotnet.md) som förklarar hur du gör samma med Service Bus .NET-API. Du kan använda dessa två guider tillsammans för att lära dig om plattformar använder AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Kom igång med Service Bus
Den här handboken förutsätts att du redan har ett Service Bus-namnområde som innehåller en kö med namnet **Kö1**. Om du inte gör det, så kan du [skapa namnområdet och kön](service-bus-create-namespace-portal.md) med hjälp av den [Azure-portalen](https://portal.azure.com). Mer information om hur du skapar Service Bus-namnområden och köer finns [komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Stöder också AMQP partitionerade köer och ämnen. Mer information finns i [partitionerade meddelandeentiteter](service-bus-partitioning.md) och [AMQP 1.0-stöd för Service Bus partitionerad köer och ämnen](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Hämta klientbiblioteket AMQP 1.0 JMS
Information om var du kan hämta den senaste versionen av klientbiblioteket Apache Qpid JMS AMQP 1.0 finns [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Du måste lägga till följande fyra JAR-filer från arkivet för Apache Qpid JMS AMQP 1.0-distribution Java-KLASSÖKVÄGEN när du bygger och JMS program som körs med Service Bus:

* geronimo jms\_1.1\_spec 1.0.jar
* qpid-amqp-1-0-Client-[version].JAR
* qpid-amqp-1-0-Client-jms-[version].JAR
* qpid-amqp-1-0-Common-[version].JAR

## <a name="coding-java-applications"></a>Kodning Java-program
### <a name="java-naming-and-directory-interface-jndi"></a>Java Naming and Directory Interface JNDI)
JMS använder Java Naming and Directory Interface JNDI () för att skapa en åtskillnad mellan namn på logisk och fysisk. Två typer av JMS objekt löses med hjälp av JNDI: ConnectionFactory- och målservrar. JNDI använder en providermodell som du kan ansluta olika katalogtjänster för att hantera name resolution uppgifter. Apache Qpid JMS AMQP 1.0 biblioteket levereras med en enkel egenskaper filbaserade JNDI Provider som har konfigurerats med en egenskapsfil av följande format:

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
Transaktionen som används för att definiera en **ConnectionFactory** i filen Qpid egenskaper JNDI provider är följande format:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Där **[jndi_name]** och **[ConnectionURL]** följande:

* **[jndi_name]** : Det logiska namnet för ConnectionFactory. Detta är det namn som kommer att lösas i Java-program som använder JNDI IntialContext.lookup()-metoden.
* **[ConnectionURL]** : En URL som innehåller JMS-bibliotek med information som krävs för att AMQP Service broker.

Formatet på den **ConnectionURL** är följande:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Där **[namnutrymme]**, **[SASPolicyName]** och **[SASPolicyKey]** följande:

* **[namnutrymme]** : The Service Bus-namnrymd.
* **[SASPolicyName]** : Principnamn i kön signatur för delad åtkomst.
* **[SASPolicyKey]** : I kön signatur för delad åtkomst principnyckel.

> [!NOTE]
> Du måste URL-koda lösenordet manuellt. Det finns ett användbart URL-kodning verktyg på [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).
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

* **[jndi_name]** : Det logiska namnet på målet. Detta är det namn som kommer att lösas i Java-program som använder JNDI IntialContext.lookup()-metoden.
* **[physical_name]** : Namnet på Service Bus-entitet som programmet skickar eller tar emot meddelanden.

> [!NOTE]
> När du får från en prenumeration för Service Bus-avsnittet, ska fysiska namnet som angetts i JNDI vara namnet på avsnittet. Ange prenumerationsnamnet när varaktiga prenumerationen skapas i programkoden JMS. Den [Service Bus AMQP 1.0 Developer's Guide](service-bus-amqp-dotnet.md) innehåller mer information om hur du arbetar med Service Bus-ämnen från JMS.
> 
> 

### <a name="write-the-jms-application"></a>Skriva JMS-program
Det finns inga särskilda API: er eller alternativ som krävs när du använder JMS med Service Bus. Det finns dock några begränsningar som beskrivs senare. Som med alla JMS program är direkt nödvändiga konfigurationen av JNDI-miljön för att kunna lösa en **ConnectionFactory** och mål.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurera JNDI InitialContext
JNDI miljö konfigureras genom att skicka en hash-tabell konfigurationsinformationen till konstruktorn för klassen javax.naming.InitialContext. Två obligatoriska element i hash-tabellen är klassnamnet för inledande kontexten fabriken och webbadressen för providern. Följande kod visar hur du konfigurerar JNDI miljön om du vill använda Qpid egenskaper filbaserade JNDI providern med en egenskapsfil med namnet **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Ett enkelt JMS program med hjälp av en Service Bus-kö
Följande exempel programmet skickar JMS TextMessages till en Service Bus-kö med det logiska namnet JNDI för KÖN och tar emot meddelanden igen.

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
Den här guiden visades hur du skickar och tar emot meddelanden till och från Service Bus med hjälp av JMS. En av de främsta fördelarna med AMQP 1.0 är dock att du kan program som ska byggas från komponenter som skrivits i olika språk med meddelanden som utbyts tillförlitligt och fullständig återgivning.

Med hjälp av JMS exempelprogrammet som beskrivs ovan och ett liknande .NET-program som hämtas från en tillhörande artikel [med hjälp av Service Bus från .NET med AMQP 1.0](service-bus-amqp-dotnet.md), du kan utbyta meddelanden mellan .NET och Java. Den här artikeln för mer detaljerad information om av plattformsoberoende meddelanden med hjälp av Service Bus och AMQP 1.0.

### <a name="jms-to-net"></a>JMS till .NET
Att visa JMS för .NET-meddelanden:

* Starta .NET exempelprogrammet utan kommandoradsargument.
* Starta exempelprogrammet Java med ”sendonly” kommandoradsargument. I detta läge programmet ska inte ta emot meddelanden från kön, skickas bara.
* Tryck på **RETUR** några gånger i konsolen Java-program som gör att meddelanden ska skickas.
* Dessa meddelanden tas emot av .NET-program.

#### <a name="output-from-jms-application"></a>Utdata från JMS program
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

### <a name="net-to-jms"></a>.NET till JMS
Att visa .NET för JMS meddelanden:

* Starta exempelprogrammet .NET med ”sendonly” kommandoradsargument. I detta läge programmet ska inte ta emot meddelanden från kön, skickas bara.
* Starta Java exempelprogrammet utan kommandoradsargument.
* Tryck på **RETUR** några gånger i konsolen för .NET-program som gör att meddelanden ska skickas.
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

#### <a name="output-from-jms-application"></a>Utdata från JMS program
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Funktioner som inte stöds och begränsningar
Följande begränsningar gäller när du använder JMS via AMQP 1.0 med Service Bus, nämligen:

* Endast en **MessageProducer** eller **MessageConsumer** tillåts per **Session**. Om du behöver skapa flera **MessageProducers** eller **MessageConsumers** i ett program, skapa ett dedikerat **Session** för dem.
* Temporär ämnesprenumerationer stöds inte för närvarande.
* **MessageSelectors** stöds inte för närvarande.
* Tillfälligt mål; till exempel **TemporaryQueue**, **TemporaryTopic** stöds inte för närvarande, tillsammans med den **QueueRequestor** och **TopicRequestor** API: er som använder dem.
* Överförda sessioner och distribuerade transaktioner stöds inte.

## <a name="summary"></a>Sammanfattning
Den här instruktioner visades hur du använder Service Bus asynkrona meddelandetjänsten funktioner (köer och ämnen för Publicera/prenumerera) från Java med populära JMS API och AMQP 1.0.

Du kan också använda Service Bus AMQP 1.0 från andra språk, inklusive .NET, C, Python eller PHP. Komponenter som skapats med hjälp av dessa olika språk kan utbyta meddelanden på ett tillförlitligt sätt och vid fullständig återgivning använder AMQP 1.0 stöder i Service Bus.

## <a name="next-steps"></a>Nästa steg
* [AMQP 1.0-stöd i Azure Service Bus](service-bus-amqp-overview.md)
* [Hur du använder AMQP 1.0 med Service Bus .NET-API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0-Guide för utvecklare](service-bus-amqp-dotnet.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Java-utvecklingscenter](https://azure.microsoft.com/develop/java/)

