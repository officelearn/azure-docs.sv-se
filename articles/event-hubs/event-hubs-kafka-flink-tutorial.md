---
title: Använda Apache Flink med Händelsehubbar i Azure för Kafka ekosystem | Microsoft Docs
description: Den anslutande Apache Flink till en Kafka aktiverad händelsehubb
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: cb7ef0e9b6a612e3f4116cb626903770e4035368
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304139"
---
# <a name="apache-flink-with-event-hubs-for-the-kafka-ecosystem"></a>Apache Flink med Händelsehubbar för Kafka ekosystem

En av de främsta fördelarna med att använda Apache Kafka är ekosystem med ramverk som den kan ansluta till. Kafka aktiverad Händelsehubbar kombinerar Kafka flexibilitet med skalbarhet, konsekvens och stöd för Azure-ekosystemet.

Den här kursen visar hur du ansluter Apache Flink till Kafka-aktiverade händelsehubbar utan att ändra protokoll-klienter eller ditt eget kluster som körs. Händelsehubbar i Azure för Kafka ekosystem stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Förutsättningar

Den här kursen, kontrollera att du har följande krav:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Hämta](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett binärt Maven-Arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett namnområde för Händelsehubbar

Ett namnområde för Händelsehubbar krävs för att skicka eller ta emot från alla händelsehubbtjänsten. Se [skapa Kafka aktiverat Händelsehubbar](event-hubs-create-kafka-enabled.md) information om hur du får en Event Hubs Kafka slutpunkt. Se till att kopiera anslutningssträngen Händelsehubbar för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har en aktiverad Kafka Händelsehubbar anslutningssträng klona lagringsplatsen för Azure Event Hubs och navigera till den `flink` undermapp:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/flink
```

## <a name="flink-producer"></a>Flink producenten

Med hjälp av angivna Flink producenten exempel, skicka meddelanden till händelsehubbtjänsten.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka slutpunkt

#### <a name="producerconfig"></a>Producer.config

Uppdatering av `bootstrap.servers` och `sasl.jaas.config` värdena i `producer/src/main/resources/producer.config` att dirigera producenten Event Hubs Kafka slutpunkten med korrekt autentisering.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Kör producenten från kommandoraden

Generera JAR för att köra producenten från kommandoraden, och sedan köras från ett Maven (eller generera JAR med Maven, kör i Java genom att lägga till nödvändiga Kafka JAR(s) klassökvägen):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producenten kommer nu att börja skicka händelser till Kafka aktiverat Event Hub på avsnittet `test` och skriva ut händelser STDOUT.

## <a name="flink-consumer"></a>Flink konsumenten

Med hjälp av angivna konsumenten exempelvis ta emot meddelanden från Kafka aktiverat Händelsehubbar.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka slutpunkt

#### <a name="consumerconfig"></a>Consumer.config

Uppdatering av `bootstrap.servers` och `sasl.jaas.config` värdena i `consumer/src/main/resources/consumer.config` till direktkonsumenten till Event Hubs Kafka slutpunkten med korrekt autentisering.

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

Generera JAR för att köra konsumenten från kommandoraden, och sedan köras från ett Maven (eller generera JAR med Maven, kör i Java genom att lägga till nödvändiga Kafka JAR(s) klassökvägen):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Om aktiverad Kafka händelsehubben har händelser (t.ex, om din producenten också kör), sedan konsumenten nu börjar ta emot händelser från avsnittet `test`.

Checka ut [Flink's Kafka Connector guiden](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) mer detaljerad information om hur du ansluter Flink till Kafka.

## <a name="next-steps"></a>Nästa steg

* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
* [Ta reda på mer om Event Hubs för Kafka Ecosystem](event-hubs-for-kafka-ecosystem-overview.md)
* Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) till [dataströmmen händelser från Kafka lokal till Kafka aktiverat Händelsehubbar i molnet.](event-hubs-kafka-mirror-maker-tutorial.md)
* Lär dig att strömma till Kafka aktiverade Händelsehubbar med hjälp av [interna Kafka program](event-hubs-quickstart-kafka-enabled-event-hubs.md) eller [Akka dataströmmar](event-hubs-kafka-akka-streams-tutorial.md).
