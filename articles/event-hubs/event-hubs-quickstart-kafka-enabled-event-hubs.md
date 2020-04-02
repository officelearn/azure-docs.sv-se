---
title: 'Snabbstart: Datastreaming med Azure Event Hubs med Kafka-protokollet'
description: 'Snabbstart: Den här artikeln innehåller information om hur du streamar till Azure Event Hubs med Kafka-protokollet och API:er.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: d196611dcf9f29e981977b8506ad9623f4629b53
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521639"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Snabbstart: Datastreaming med eventhubbar med Kafka-protokollet
Den här snabbstarten visar hur du streamar till eventhubbar utan att ändra dina protokollklienter eller köra egna kluster. Du lär dig hur du använder dina producenter och konsumenter för att prata med Event Hubs med bara en konfigurationsändring i dina program. Azure Event Hubs stöder [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här snabbstarten behöver du följande:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Ladda ned](https://maven.apache.org/download.cgi) och [installera](https://maven.apache.org/install.html) ett Maven-binärarkiv.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
När du skapar **ett** standardnivånamnområde för händelsehubbar aktiveras Kafka-slutpunkten för namnområdet automatiskt. Du kan strömma händelser från dina program som använder Kafka-protokollet till standardnivåhändelsehubbar. Följ steg-för-steg-instruktioner i [skapa en händelsehubb med Azure-portalen](event-hubs-create.md) för att skapa ett **standardnivånamnområde** för eventhubbar. 

> [!NOTE]
> Event Hubs för Kafka är endast tillgängligt på **standard-** och **dedikerade** nivåer. Den **grundläggande** nivån stöder inte Kafka på Event Hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Skicka och ta emot meddelanden med Kafka i Event Hubs

1. Klona [Azure Event Hubs för Kafka-lagringsplats](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navigera till `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Uppdatera konfigurationsinformationen för tillverkare i `src/main/resources/producer.config` på följande sätt:

    **Ssl:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    Du hittar källkoden för exempelhanterarklassen CustomAuthenticateCallbackHandler på GitHub [här](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Kör producentkoden och strömma händelser till Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navigera till `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Uppdatera konfigurationsinformationen för konsument i `src/main/resources/consumer.config` på följande sätt:
   
    **Ssl:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    Du hittar källkoden för exempelhanterarklassen CustomAuthenticateCallbackHandler på GitHub [här](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    Du hittar alla OAuth-exempel för Event Hubs för Kafka [här](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).
7. Kör konsumentkoden och bearbeta händelser från händelsehubben med kafka-klienterna:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Om dina Kafka-kluster för Event Hubs har händelser börjar du nu att få dem från konsumenten.

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att strömma till eventhubbar utan att ändra dina protokollklienter eller köra egna kluster. Mer information finns i följande artiklar och exempel:

- [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Snabbstarter för eventhubbar för Kafka på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [Självstudier för eventhubbar för Kafka på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att [strömma händelser från Kafka lokalt till Event Hubs i molnet.](event-hubs-kafka-mirror-maker-tutorial.md)
- Lär dig hur du streamar till eventhubbar med [Apache Flink-](event-hubs-kafka-flink-tutorial.md) eller [Akka-strömmar](event-hubs-kafka-akka-streams-tutorial.md)
