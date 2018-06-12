---
title: Med Azure Event Hubs Akka dataströmmar för Kafka ekosystem | Microsoft Docs
description: Ansluta Akka strömmar till en Kafka aktiverat Event Hub
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: 9db27340a2210ea0be0564b15241952477e592ba
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304134"
---
# <a name="using-akka-streams-with-event-hubs-for-kafka-ecosystem"></a>Med Händelsehubbar Akka dataströmmar för Kafka ekosystem

En av de främsta fördelarna med att använda Apache Kafka är ekosystem med ramverk som den kan ansluta till. Kafka-aktiverade Händelsehubbar kombinerar Kafka flexibilitet med skalbarhet, konsekvens och stöd för Azure-ekosystemet.

Den här kursen visar hur du ansluter Akka dataströmmar till Kafka-aktiverade händelsehubbar utan att ändra protokoll-klienter eller ditt eget kluster som körs. Händelsehubbar i Azure för Kafka ekosystem stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Förutsättningar

Den här kursen, kontrollera att du har följande krav:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.8 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Hämta](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett binärt Maven-Arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett namnområde för Händelsehubbar

Ett namnområde för Händelsehubbar krävs för att skicka eller ta emot från alla händelsehubbtjänsten. Se [skapa Kafka aktiverat Händelsehubbar](event-hubs-create-kafka-enabled.md) information om hur du får en Event Hubs Kafka slutpunkt. Se till att kopiera anslutningssträngen Händelsehubbar för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har en aktiverad Kafka Händelsehubbar anslutningssträng klona lagringsplatsen för Azure Event Hubs och navigera till den `akka` undermapp:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Akka dataströmmar producenten

Med hjälp av angivna Akka dataströmmar producenten exempel, skicka meddelanden till händelsehubbtjänsten.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka slutpunkt

#### <a name="producer-applicationconf"></a>Producenten application.conf

Uppdatering av `bootstrap.servers` och `sasl.jaas.config` värdena i `producer/src/main/resources/application.conf` att dirigera producenten Event Hubs Kafka slutpunkten med korrekt autentisering.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Kör producenten från kommandoraden

Generera JAR för att köra producenten från kommandoraden, och sedan köras från ett Maven (eller generera JAR med Maven, kör i Java genom att lägga till nödvändiga Kafka JAR(s) klassökvägen):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producenten börjar skicka händelser till Kafka-aktiverade händelsehubben på avsnittet `test`, skrivs händelser till stdout.

## <a name="akka-streams-consumer"></a>Akka dataströmmar konsumenten

I exemplet med angivna konsumenten ta emot meddelanden från Kafka-aktiverade händelsehubbar.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka slutpunkt

#### <a name="consumer-applicationconf"></a>Konsumenten application.conf

Uppdatering av `bootstrap.servers` och `sasl.jaas.config` värdena i `consumer/src/main/resources/application.conf` till direktkonsumenten till Event Hubs Kafka slutpunkten med korrekt autentisering.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Kör konsumenten från kommandoraden

Generera JAR för att köra konsumenten från kommandoraden, och sedan köras från ett Maven (eller generera JAR med Maven, kör i Java genom att lägga till nödvändiga Kafka JAR(s) klassökvägen):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Om aktiverad Kafka händelsehubben har händelser (t.ex, om din producenten körs även), och sedan konsumenten börjar ta emot händelser från avsnittet `test`. 

Kolla in den [Akka dataströmmar Kafka Guide](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) mer detaljerad information om Akka dataströmmar.

## <a name="next-steps"></a>Nästa steg

* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
* [Ta reda på mer om Event Hubs för Kafka Ecosystem](event-hubs-for-kafka-ecosystem-overview.md)
* Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) till [dataströmmen händelser från Kafka lokal till Kafka aktiverat Händelsehubbar i molnet.](event-hubs-kafka-mirror-maker-tutorial.md)
* Lär dig att strömma till Kafka aktiverade Händelsehubbar med hjälp av [interna Kafka program](event-hubs-quickstart-kafka-enabled-event-hubs.md) eller [Apache Flink](event-hubs-kafka-flink-tutorial.md)
