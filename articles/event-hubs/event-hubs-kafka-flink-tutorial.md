---
title: Använda Apache Flink med Azure Event Hubs för Apache Kafka | Microsoft Docs
description: Den anslutande Apache Flink till ett Kafka aktiverat händelsehubb
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: ce1665c3cfd58d0d5aa8e253b5db317505b1959e
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284585"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Använda Apache Flink med Azure Event Hubs för Apache Kafka

En av de främsta fördelarna med att använda Apache Kafka är ekosystem med ramverk som den kan ansluta till. Kafka aktiverat Händelsehubbar kombinerar flexibiliteten i Kafka med skalbarhet, konsekvens och stöd för Azure-ekosystemet.

Den här självstudien visar hur du ansluter Apache Flink till Kafka-aktiverade event hubs utan att ändra protokoll-klienter eller ditt eget kluster som körs. Azure Event Hubs stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du kontrollera att du har följande krav:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett Maven-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett namnområde för Event Hubs krävs för att skicka eller ta emot från alla Event Hubs-tjänsten. Se [skapa Kafka aktiverat Händelsehubbar](event-hubs-create-kafka-enabled.md) information om hur du får en Event Hubs Kafka-slutpunkt. Se till att kopiera anslutningssträngen Event Hubs för senare användning.

## <a name="clone-the-example-project"></a>Klona exemplet-projektet

Nu när du har en Kafka-aktiverade Event Hubs anslutningssträng klona databasen för Azure Event Hubs och navigera till den `flink` undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/flink
```

## <a name="flink-producer"></a>Flink producent

Med hjälp av angivna Flink producent exempel, skicka meddelanden till Event Hubs-tjänsten.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka-slutpunkt

#### <a name="producerconfig"></a>Producer.config

Uppdatera den `bootstrap.servers` och `sasl.jaas.config` värdena i `producer/src/main/resources/producer.config` att dirigera producenten till Event Hubs Kafka-slutpunkten med korrekt autentisering.

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

Skapa JAR för att köra producenten från kommandoraden, och sedan köra inifrån Maven (eller generera JAR-filen med hjälp av Maven, kör sedan i Java genom att lägga till nödvändiga Kafka JAR(s) klassökvägen):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producenten kommer nu att börja skicka händelser till Kafka aktiverat Event Hub vid avsnittet `test` och skriva ut händelserna som bör stdout.

## <a name="flink-consumer"></a>Flink konsument

Med hjälp av angivna konsument exempelvis ta emot meddelanden från Kafka aktiverat Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka-slutpunkt

#### <a name="consumerconfig"></a>Consumer.config

Uppdatera den `bootstrap.servers` och `sasl.jaas.config` värdena i `consumer/src/main/resources/consumer.config` till direktkonsumenten till Event Hubs Kafka-slutpunkten med korrekt autentisering.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Kör konsument från kommandoraden

Skapa JAR för att köra konsumenten från kommandoraden, och sedan köra inifrån Maven (eller generera JAR-filen med hjälp av Maven, kör sedan i Java genom att lägga till nödvändiga Kafka JAR(s) klassökvägen):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Om Kafka-aktiverade event hub har händelser (till exempel, om din producent också körs), sedan konsumenten nu börjar ta emot händelser från ämnet `test`.

Kolla in [Flink's Kafka Connector Guide](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) mer detaljerad information om hur du ansluter Flink till Kafka.

## <a name="next-steps"></a>Nästa steg

* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
* [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att [strömma händelser från Kafka lokalt till Kafka-aktiverade Event Hubs i molnet.](event-hubs-kafka-mirror-maker-tutorial.md)
* Lär dig att strömma till Kafka aktiverade Event Hubs med hjälp av [interna Kafka program](event-hubs-quickstart-kafka-enabled-event-hubs.md) eller [Akka strömmar](event-hubs-kafka-akka-streams-tutorial.md).
