---
title: Med Azure Event Hubs Akka strömmar för Apache Kafka | Microsoft Docs
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
ms.openlocfilehash: f6671e05c53b8be2e06eb969adc8a4d5736aff37
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284470"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Med hjälp av Akka dataströmmar med Event Hubs för Apache Kafka

En av de främsta fördelarna med att använda Apache Kafka är ekosystem med ramverk som den kan ansluta till. Kafka-aktiverade Händelsehubbar kombinerar flexibiliteten i Kafka med skalbarhet, konsekvens och stöd för Azure-ekosystemet.

Den här självstudien visar hur du ansluter Akka strömmar till Kafka-aktiverade event hubs utan att ändra protokoll-klienter eller ditt eget kluster som körs. Har stöd för Azure Event Hubs för Kafka [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du kontrollera att du har följande krav:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK 1.8 +)](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett Maven-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett namnområde för Event Hubs krävs för att skicka eller ta emot från alla Event Hubs-tjänsten. Se [skapa Kafka aktiverat Händelsehubbar](event-hubs-create-kafka-enabled.md) information om hur du får en Event Hubs Kafka-slutpunkt. Se till att kopiera anslutningssträngen Event Hubs för senare användning.

## <a name="clone-the-example-project"></a>Klona exemplet-projektet

Nu när du har en Kafka-aktiverade Event Hubs anslutningssträng klona databasen för Azure Event Hubs och navigera till den `akka` undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Akka strömmar producent

Med hjälp av angivna Akka strömmar producent exempel, skicka meddelanden till Event Hubs-tjänsten.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka-slutpunkt

#### <a name="producer-applicationconf"></a>Producent application.conf

Uppdatera den `bootstrap.servers` och `sasl.jaas.config` värdena i `producer/src/main/resources/application.conf` att dirigera producenten till Event Hubs Kafka-slutpunkten med korrekt autentisering.

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

### <a name="run-producer-from-the-command-line"></a>Kör producent från kommandoraden

Skapa JAR för att köra producenten från kommandoraden, och sedan köra inifrån Maven (eller generera JAR-filen med hjälp av Maven, kör sedan i Java genom att lägga till nödvändiga Kafka JAR(s) klassökvägen):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producenten börjar skicka händelser till Kafka-aktiverade händelsehubben i avsnittet `test`, och skriver ut händelser till stdout.

## <a name="akka-streams-consumer"></a>Akka strömmar konsument

Med hjälp av angivna konsument-exempel, ta emot meddelanden från Kafka-aktiverade event hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka-slutpunkt

#### <a name="consumer-applicationconf"></a>Konsumenten application.conf

Uppdatera den `bootstrap.servers` och `sasl.jaas.config` värdena i `consumer/src/main/resources/application.conf` till direktkonsumenten till Event Hubs Kafka-slutpunkten med korrekt autentisering.

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

### <a name="run-consumer-from-the-command-line"></a>Kör konsument från kommandoraden

Skapa JAR för att köra konsumenten från kommandoraden, och sedan köra inifrån Maven (eller generera JAR-filen med hjälp av Maven, kör sedan i Java genom att lägga till nödvändiga Kafka JAR(s) klassökvägen):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Om Kafka-aktiverade event hub har händelser (till exempel om ditt producent körs också), och sedan konsumenten börjar ta emot händelser från avsnittet `test`. 

Kolla in den [Akka strömmar Kafka Guide](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) mer detaljerad information om Akka strömmar.

## <a name="next-steps"></a>Nästa steg

* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
* [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att [strömma händelser från Kafka lokalt till Kafka-aktiverade Event Hubs i molnet.](event-hubs-kafka-mirror-maker-tutorial.md)
* Lär dig att strömma till Kafka aktiverade Event Hubs med hjälp av [interna Kafka program](event-hubs-quickstart-kafka-enabled-event-hubs.md) eller [Apache Flink](event-hubs-kafka-flink-tutorial.md)
