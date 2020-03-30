---
title: Använda Apache Flink för Apache Kafka - Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller information om hur du ansluter Apache Flink till en Apache Kafka-aktiverad Azure-händelsehubb
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: db877279bcfa7e132841e342cfc25b66bb3ec384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283607"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Använda Apache Flink med Azure Event Hubs för Apache Kafka
Den här självstudien visar hur du ansluter Apache Flink till en händelsehubb utan att ändra dina protokollklienter eller köra egna kluster. Azure Event Hubs stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html).

En av de viktigaste fördelarna med att använda Apache Kafka är ekosystemet av ramar som den kan ansluta till. Event Hubs kombinerar flexibiliteten i Kafka med skalbarhet, konsekvens och support för Azure-ekosystemet.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Flink producent 
> * Kör Flink-konsument

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, se till att du har följande förutsättningar:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1,7+](https://aka.ms/azure-jdks)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ner](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett Maven binärt arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett namnområde för eventhubbar krävs för att skicka eller ta emot från valfri eventhubbar-tjänst. Se [Skapa Kafka-aktiverade händelsehubbar](event-hubs-create.md) för information om hur du hämtar en Event Hubs Kafka-slutpunkt. Kontrollera att du kopierar anslutningssträngen för händelsehubbar för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har anslutningssträngen Event Hubs klonar du Azure Event `flink` Hubs för Kafka-databasen och navigerar till undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Kör Flink producent

Med hjälp av exemplet Flink-producent skickar du meddelanden till tjänsten Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en slutpunkt för Händelsehubbar Kafka

#### <a name="producerconfig"></a>producent.config

Uppdatera `bootstrap.servers` värdena `sasl.jaas.config` `producer/src/main/resources/producer.config` och värdena för att dirigera producenten till slutpunkten Event Hubs Kafka med rätt autentisering.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Kör producent från kommandoraden

Om du vill köra producenten från kommandoraden genererar du JAR:en och kör sedan inifrån Maven (eller genererar JAR:en med Maven och kör sedan i Java genom att lägga till nödvändiga Kafka JAR:ar i klassen(

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producenten kommer nu att börja skicka händelser till Kafka-aktiverade Event Hub på ämne `test` och skriva ut händelserna till stdout.

## <a name="run-flink-consumer"></a>Kör Flink-konsument

Med hjälp av det angivna konsumentexemplet får du meddelanden från Kafka-aktiverade eventhubbar.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en slutpunkt för Händelsehubbar Kafka

#### <a name="consumerconfig"></a>consumer.config (konsument.config)

Uppdatera `bootstrap.servers` värdena `sasl.jaas.config` `consumer/src/main/resources/consumer.config` och värdena för att dirigera konsumenten till slutpunkten Event Hubs Kafka med rätt autentisering.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Kör konsumenten från kommandoraden

Om du vill köra konsumenten från kommandoraden genererar du JAR:en och kör sedan inifrån Maven (eller genererar JAR:en med Maven och kör sedan i Java genom att lägga till nödvändiga Kafka JAR:ar i klassenpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Om händelsehubben har händelser (till exempel om din producent också körs) börjar `test`konsumenten nu ta emot händelser från ämnet .

Kolla in [Flink's Kafka Connector Guide](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) för mer detaljerad information om hur du ansluter Flink till Kafka.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du ansluter Apache Flink till eventhubbar utan att ändra dina protokollklienter eller köra egna kluster. Du har utfört följande steg som en del av den här självstudien: 

> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Flink producent 
> * Kör Flink-konsument

Läs mer om Event Hubs och Event Hubs för Kafka i följande ämne:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Så skapar du Kafka-aktiverade händelsehubbar](event-hubs-create.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Integrera Kafka Connect med ett händelsenav](event-hubs-kafka-connect-tutorial.md)
- [Ansluta Akka-strömmar till ett händelsenav](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
