---
title: Använda Akka strömmar för Apache Kafka-Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du ansluter Akka-strömmar till en Apache Kafka aktive rad Azure Event Hub.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: ba81ce88bcdf039d020dcd945e45a11cf603c114
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555750"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Använda Akka strömmar med Event Hubs för Apache Kafka
Den här självstudien visar hur du ansluter Akka-strömmar till Kafka-aktiverade Event Hub utan att ändra protokoll klienter eller köra egna kluster. Azure Event Hubs för Kafka har stöd för [Apache Kafka version 1,0.](https://kafka.apache.org/10/documentation.html)

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Akka Streams-tillverkare 
> * Köra Akka strömmar konsument

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Krav

Kontrol lera att du har följande krav för att slutföra den här självstudien:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Hämta](https://maven.apache.org/download.cgi) och [Installera](https://maven.apache.org/install.html) ett maven-binärt Arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett Event Hubs-namnområde krävs för att skicka eller ta emot från en Event Hubs-tjänst. Information om hur du hämtar en Event Hubs Kafka-slutpunkt finns i [skapa Kafka-aktiverade Event Hubs](event-hubs-create-kafka-enabled.md) . Se till att kopiera Event Hubs anslutnings strängen för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har en Kafka-aktiverad Event Hubs anslutnings sträng, klona Azure-Event Hubs för Kafka-lagringsplatsen och navigera till undermappen `akka`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Kör Akka Streams-tillverkare

Med hjälp av det tillhandahållna Akka Streams-exemplet skickar du meddelanden till tjänsten Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka-slutpunkt

#### <a name="producer-applicationconf"></a>Producent program. conf

Uppdatera `bootstrap.servers` och `sasl.jaas.config` värden i `producer/src/main/resources/application.conf` för att dirigera producenten till Event Hubs Kafka-slutpunkten med rätt autentisering.

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

### <a name="run-producer-from-the-command-line"></a>Kör Producer från kommando raden

Om du vill köra producenten från kommando raden genererar du BURKen och kör sedan inifrån maven (eller genererar JAR med Maven och kör sedan i Java genom att lägga till de nödvändiga Kafka-JAR: erna i classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producenten börjar skicka händelser till Kafka-aktiverad händelsehubben i avsnittet `test` och skriver ut händelserna till stdout.

## <a name="run-akka-streams-consumer"></a>Köra Akka strömmar konsument

Med hjälp av det tillhandahållna konsument exemplet tar du emot meddelanden från Kafka-aktiverade Event Hub.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka-slutpunkt

#### <a name="consumer-applicationconf"></a>Konsument program. conf

Uppdatera `bootstrap.servers` och `sasl.jaas.config` värden i `consumer/src/main/resources/application.conf` för att dirigera konsumenten till Event Hubs Kafka-slutpunkten med rätt autentisering.

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

### <a name="run-consumer-from-the-command-line"></a>Kör konsument från kommando raden

Om du vill köra konsumenten från kommando raden genererar du BURKen och kör sedan inifrån maven (eller genererar JAR med Maven och kör sedan i Java genom att lägga till de nödvändiga Kafka-JAR: erna i classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Om Kafka-aktiverade händelsehubben har händelser (till exempel om din producent också körs) börjar klienten att ta emot händelser från ämnes `test`. 

Mer detaljerad information om Akka-strömmar finns i [Akka Streams Kafka-guiden](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) .

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du ansluter Akka strömmar till Kafka-aktiverade Event Hub utan att ändra protokoll klienter eller köra egna kluster. Azure Event Hubs för Kafka har stöd för [Apache Kafka version 1,0.](https://kafka.apache.org/10/documentation.html). Du har följande åtgärder som en del av den här självstudien: 

> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Akka Streams-tillverkare 
> * Köra Akka strömmar konsument

Läs mer om Event Hubs och Event Hubs för Kafka i följande ämne:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Så skapar du Kafka-aktiverade händelsehubbar](event-hubs-create-kafka-enabled.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en asynkron Kafka-meddelandekö i en Kafka-aktiverad händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en Kafka-aktiverad händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en Kafka-aktiverad händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med en Kafka-aktiverad händelsehubb](event-hubs-kafka-connect-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
