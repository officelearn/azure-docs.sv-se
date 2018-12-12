---
title: Använda Apache Flink för Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du ansluter Apache Flink till ett Apache Kafka aktiverat Azure-händelsehubb
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: cb964451d25bb381ee9cca84628852a61e4be9b6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090915"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Använda Apache Flink med Azure Event Hubs för Apache Kafka
Den här självstudien visar hur du ansluter Apache Flink till Kafka-aktiverade event hubs utan att ändra protokoll-klienter eller ditt eget kluster som körs. Azure Event Hubs stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html).

En av de främsta fördelarna med att använda Apache Kafka är ekosystem med ramverk som den kan ansluta till. Kafka aktiverat Händelsehubbar kombinerar flexibiliteten i Kafka med skalbarhet, konsekvens och stöd för Azure-ekosystemet.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Flink producent 
> * Kör Flink konsument

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du kontrollera att du har följande krav:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett Maven-binärarkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett namnområde för Event Hubs krävs för att skicka eller ta emot från alla Event Hubs-tjänsten. Se [skapa Kafka aktiverat Händelsehubbar](event-hubs-create-kafka-enabled.md) information om hur du får en Event Hubs Kafka-slutpunkt. Se till att kopiera anslutningssträngen Event Hubs för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har en Kafka-aktiverade Event Hubs anslutningssträng klona Azure Event Hubs för Kafka-lagringsplats och navigera till den `flink` undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Kör Flink producent

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

## <a name="run-flink-consumer"></a>Kör Flink konsument

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
I de här självstudierna din inlärda ansluta Apache Flink till Kafka-aktiverade händelsehubbar utan att ändra protokoll-klienter eller ditt eget kluster som körs. Du har utfört följande steg som en del av den här självstudien: 

> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Flink producent 
> * Kör Flink konsument

Läs mer om Event Hubs och Event Hubs för Kafka i följande ämne:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Så skapar du Kafka-aktiverade händelsehubbar](event-hubs-create-kafka-enabled.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en asynkron Kafka-meddelandekö i en Kafka-aktiverad händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en Kafka-aktiverad händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Integrera Kafka Connect med en Kafka-aktiverad händelsehubb](event-hubs-kafka-connect-tutorial.md)
- [Ansluta Akka Streams till en Kafka-aktiverad händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
