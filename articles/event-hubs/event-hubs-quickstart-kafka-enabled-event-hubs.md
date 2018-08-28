---
title: Strömma till Azure Event Hubs för Apache Kafka | Microsoft Docs
description: Strömma till Event Hubs med Kafka-protokoll och API:er.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: bahariri
ms.openlocfilehash: 90d9f3620f954da42add08a0aebf779a95c7e7a3
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42024274"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Strömma till Event Hubs för Apache Kafka
Den här snabbstarten visar hur du strömmar till Kafka-aktiverade Event Hubs utan att ändra protokoll-klienter eller köra dina egna kluster. Du lär dig att använda dina tillverkare och konsumenter för att kommunicera med Kafka-aktiverade Event Hubs med bara en konfigurationsändring i dina program. Azure Event Hubs stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs)

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra den här snabbstarten behöver du följande:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Ladda ned](http://maven.apache.org/download.cgi) och [installera](http://maven.apache.org/install.html) ett Maven-binärarkiv.
* [Git](https://www.git-scm.com/)
* [Ett namnområde för Kafka-aktiverade Event Hubs](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Skapa ett Kafka-aktiverat Event Hubs-namnområde

1. Logga in på [Azure Portal][Azure Portal] och klicka på **Skapa en resurs** längst upp till vänster på skärmen.

2. Sök efter Event Hubs och välj de alternativ som visas här:
    
    ![Sök efter Event Hubs på portalen](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Ange ett unikt namn och aktivera Kafka för namnområdet. Klicka på **Skapa**.
    
    ![Skapa ett namnområde](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. När namnområdet har skapats går du till fliken **Inställningar** och klickar på **Policyer för delad åtkomst** för att hämta anslutningssträngen.

    ![Klicka på Policyer för delad åtkomst](./media/event-hubs-create/create-event-hub7.png)

5. Du kan välja standardprincipen **RootManageSharedAccessKey** eller lägga till en ny princip. Klicka på principnamnet och kopiera anslutningssträngen. 
    
    ![Välj en princip](./media/event-hubs-create/create-event-hub8.png)
 
6. Lägg till den här anslutningssträngen i Kafka-programkonfigurationen.

Nu kan du strömma händelser från program som använder Kafka-protokollet till Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Skicka och ta emot meddelanden med Kafka i Event Hubs

1. Klona [Azure Event Hubs-databasen](https://github.com/Azure/azure-event-hubs).

2. Navigera till `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Uppdatera konfigurationsinformationen för tillverkare i `src/main/resources/producer.config` på följande sätt:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Kör tillverkarkoden och strömma till Kafka-aktiverade Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Navigera till `azure-event-hubs/samples/kafka/quickstart/consumer`.

6. Uppdatera konfigurationsinformationen för konsument i `src/main/resources/consumer.config` på följande sätt:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Kör konsumentkoden och bearbeta från Kafka-aktiverade Event Hubs med dina Kafka-klienter:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Om dina Kafka-kluster för Event Hubs har händelser börjar du nu att få dem från konsumenten.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du strömmar till Kafka-aktiverade Event Hubs utan att ändra dina protokollklienter och utan att köra dina egna kluster. Fortsätt med följande självstudie om du vill veta mer:

> [!div class="nextstepaction"]
> [Använda Kafka MirrorMaker med Event Hubs](event-hubs-kafka-mirror-maker-tutorial.md)
