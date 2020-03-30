---
title: Använda Akka-strömmar för Apache Kafka – Azure Event Hubs| Microsoft-dokument
description: Den här artikeln innehåller information om hur du ansluter Akka-strömmar till en Apache Kafka-aktiverad Azure-händelsehubb.
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
ms.openlocfilehash: 729e78959f93b1aa1563a049a64c553929c4f97e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283641"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Använda Akka Streams med Event Hubs för Apache Kafka
Den här självstudien visar hur du ansluter Akka Streams till en händelsehubb utan att ändra dina protokollklienter eller köra egna kluster. Azure Event Hubs för Kafka stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Akka Streams producent 
> * Kör Akka Streams konsument

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, se till att du har följande förutsättningar:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ner](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett Maven binärt arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett namnområde för eventhubbar krävs för att skicka eller ta emot från valfri eventhubbar-tjänst. Se [Skapa Kafka-aktiverade händelsehubbar](event-hubs-create.md) för information om hur du hämtar en Event Hubs Kafka-slutpunkt. Kontrollera att du kopierar anslutningssträngen för händelsehubbar för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har en anslutningssträng för eventhubner klonar du Azure `akka` Event Hubs för Kafka-databasen och navigerar till undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Kör Akka Streams producent

Med hjälp av det angivna exemplet Akka Streams-producent skickar du meddelanden till tjänsten Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en slutpunkt för Händelsehubbar Kafka

#### <a name="producer-applicationconf"></a>Producent application.conf

Uppdatera `bootstrap.servers` värdena `sasl.jaas.config` `producer/src/main/resources/application.conf` och värdena för att dirigera producenten till slutpunkten Event Hubs Kafka med rätt autentisering.

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

Om du vill köra producenten från kommandoraden genererar du JAR:en och kör sedan inifrån Maven (eller genererar JAR:en med Maven och kör sedan i Java genom att lägga till nödvändiga Kafka JAR:ar i klassen(

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producenten börjar skicka händelser till händelsehubben i ämnet `test`och skriver ut händelserna för att stdout.

## <a name="run-akka-streams-consumer"></a>Kör Akka Streams konsument

Ta emot meddelanden från händelsehubben med hjälp av det angivna konsumentexemplet.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en slutpunkt för Händelsehubbar Kafka

#### <a name="consumer-applicationconf"></a>Konsument application.conf

Uppdatera `bootstrap.servers` värdena `sasl.jaas.config` `consumer/src/main/resources/application.conf` och värdena för att dirigera konsumenten till slutpunkten Event Hubs Kafka med rätt autentisering.

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

Om du vill köra konsumenten från kommandoraden genererar du JAR:en och kör sedan inifrån Maven (eller genererar JAR:en med Maven och kör sedan i Java genom att lägga till nödvändiga Kafka JAR:ar i klassenpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Om händelsehubben har händelser (till exempel om din producent också körs) `test`börjar konsumenten ta emot händelser från ämnet . 

Kolla in [Akka Streams Kafka Guide](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) för mer detaljerad information om Akka Streams.

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig hur du ansluter Akka-strömmar till händelsehubben utan att ändra dina protokollklienter eller köra egna kluster. Azure Event Hubs för Kafka stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html). Du har gjort följande åtgärder som en del av den här självstudien: 

> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Akka Streams producent 
> * Kör Akka Streams konsument

Läs mer om Event Hubs och Event Hubs för Kafka i följande ämne:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Så här skapar du en händelsehubb](event-hubs-create.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka Connect med ett händelsenav](event-hubs-kafka-connect-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
