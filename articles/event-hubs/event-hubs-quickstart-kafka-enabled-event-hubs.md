---
title: Strömma till Azure Event Hubs för Kafka Ecosystem | Microsoft Docs
description: Strömma till Event Hubs med Kafka-protokoll och API:er.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2018
ms.author: bahariri
ms.openlocfilehash: cabbb7ed6157a6c68530ab6b5f405aa67b31a1b2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="stream-into-event-hubs-for-kafka-ecosystem"></a>Strömma till Event Hubs för Kafka Ecosystem

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs)

Den här snabbstarten visar hur du strömmar till Kafka-aktiverade Event Hubs utan att ändra protokoll-klienter eller köra dina egna kluster. Du lär dig hur du använder dina tillverkare och konsumenter kan kommunicera med Kafka-aktiverade Event Hubs med bara en konfigurationsändring i dina program. Azure Event Hubs för Kafka-ekosystem har stöd för [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande för genomföra den här snabbstarten:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett Maven-binärarkiv.
* [Git](https://www.git-scm.com/)
* [Ett namnområde för Kafka-aktiverade Event Hubs](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Skicka och ta emot meddelanden med Kafka i Event Hubs

1. Klona [Azure Event Hubs-databasen](https://github.com/Azure/azure-event-hubs).

2. Navigera till `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Uppdatera konfigurationsinformationen för tillverkare i src/main/resources/producer.config såsom visas här.

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Kör tillverkarkoden och strömma till Kafka-aktiverade Event Hubs.
   
    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Gå till azure-event-hubs/samples/kafka/quickstart/consumer.

6. Uppdatera konfigurationsinformationen för konsument i src/main/resources/consumer.config såsom visas här.
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Kör konsumentkoden och bearbeta från Kafka-aktiverade Event Hubs med dina Kafka-klienter.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Om dina Kafka-kluster för Event Hubs har händelser köade från tillverkaren bör du nu börja att få dem från konsumenten.

## <a name="next-steps"></a>Nästa steg

* [Ta reda på mer om Event Hubs för Kafka Ecosystem](event-hubs-for-kafka-ecosystem-overview.md)
* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
* Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att strömma händelser från Kafka lokalt till Kafka-aktiverade Event Hubs i molnet.] (event-hubs-kafka-mirror-maker-tutorial.md)
