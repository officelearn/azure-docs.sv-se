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
ms.date: 08/06/2018
ms.author: bahariri
ms.openlocfilehash: 973470a6964e04b84b814ecf8732b2b981ced44c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278964"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Med hjälp av Akka dataströmmar med Event Hubs för Apache Kafka
Den här självstudien visar hur du ansluter Akka strömmar till Kafka-aktiverade event hubs utan att ändra protokoll-klienter eller ditt eget kluster som körs. Har stöd för Azure Event Hubs för Kafka [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exemplet-projektet
> * Kör Akka strömmar producent 
> * Kör Akka strömmar konsument

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du kontrollera att du har följande krav:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett Maven-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett namnområde för Event Hubs krävs för att skicka eller ta emot från alla Event Hubs-tjänsten. Se [skapa Kafka aktiverat Händelsehubbar](event-hubs-create-kafka-enabled.md) information om hur du får en Event Hubs Kafka-slutpunkt. Se till att kopiera anslutningssträngen Event Hubs för senare användning.

## <a name="clone-the-example-project"></a>Klona exemplet-projektet

Nu när du har en Kafka-aktiverade Event Hubs anslutningssträng klona Azure Event Hubs för Kafka-lagringsplats och navigera till den `akka` undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Kör Akka strömmar producent

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

## <a name="run-akka-streams-consumer"></a>Kör Akka strömmar konsument

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
I den här självstudien lärde du dig att ansluta Akka strömmar till Kafka-aktiverade händelsehubbar utan att ändra protokoll-klienter eller ditt eget kluster som körs. Har stöd för Azure Event Hubs för Kafka [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html). Du gjorde följande åtgärder som en del av den här självstudien: 

> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exemplet-projektet
> * Kör Akka strömmar producent 
> * Kör Akka strömmar konsument

Läs mer om Event Hubs och Event Hubs för Kafka i följande avsnitt:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Så här skapar du Kafka aktiverat Event Hubs](event-hubs-create-kafka-enabled.md)
- [Stream till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en Kafka-meddelandeköer i ett Kafka-aktiverade händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en händelsehubb med Kafka-aktiverad](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till ett Kafka-aktiverade event hub](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka ansluta med en Kafka-aktiverade händelsehubb](event-hubs-kafka-connect-tutorial.md)
- [Utforska exempel på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
