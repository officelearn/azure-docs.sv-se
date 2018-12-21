---
title: Integrera med Apache Kafka Connect – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Apache Spark med Azure Event Hubs för Kafka.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: f405fe1c93ad17476ac47bc23951c65a5ea5db73
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091371"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Integrera stöd för Apache Kafka Connect stöd i Azure Event Hubs (förhandsversion)
När inmatning för företagsbehov ökar så ökas även kraven för inmatning av olika externa källor och kanalmottagare. [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) innehåller sådana ramverk för att ansluta och importera/exportera data från/till externa system såsom MySQL, HDFS och filsystem via ett Kafka-kluster. Den här självstudien beskriver hur du använder Kafka Connect-ramverket med Kafka-aktiverade händelsehubbar.

Den här självstudien vägleder dig genom integrering av Kafka Connect med en Kafka-aktiverade Azure-händelsehubb och distribution av grundläggande FileStreamSource- och FileStreamSink-anslutingsappar. Den här funktionen är för närvarande en förhandsversion. De här anslutningsapparna är inte avsedda för produktionsanvändning, men de demonstrerar ett Kafka Connect-scenario med slutpunkt till slutpunkt där Azure Event Hubs fungerar som asynkron Kafka-meddelandekö.

> [!NOTE]
Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Konfigurera Kafka Connect för Event Hubs
> * Köra Kafka Connect
> * Skapa anslutningsappar

## <a name="prerequisites"></a>Nödvändiga komponenter
För att kunna slutföra den här genomgången behöver du följande:

- En Azure-prenumeration. Om du inte har en [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka-version (version 1.1.1, Scala-version 2.11), som finns på [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Läs introduktionsartikeln [Event Hubs för Apache Kafka](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
En Event Hubs-namnrymd krävs för att skicka och ta emot från Event Hubs-tjänster. Instruktioner om hur du skaffar en Event Hubs Kafka-slutpunkt finns i avsnittet om att [skapa en Kafka-aktiverad händelsehubb](event-hubs-create.md). Hämta Event Hubs-anslutningssträngen och fullständigt domännamn (FQDN) för senare användning. Anvisningar finns i avsnittet om att [hämta en Event Hubs-anslutningssträng](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Klona exempelprojektet
Klona Azure Event Hubs-lagringsplatsen och navigera till undermappen tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurera Kafka Connect för Event Hubs
Minimal omkonfiguration krävs vid omdirigering av Kafka Connect-dataflöde från Kafka till Event Hubs.  Följande exempel med `connect-distributed.properties` illustrerar hur du konfigurerar Connect för att autentisera och kommunicera med Kafka-slutpunkten i Event Hubs:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Köra Kafka Connect

I det här steget startas en Kafka Connect-arbetare lokalt i distribuerat läge med hjälp av Event Hubs för att upprätthålla klustertillståndet.

1. Spara filen `connect-distributed.properties` ovan lokalt.  Se till att ersätta alla värden inom klammerparenteser.
2. Navigera till platsen för Kafka-versionen på datorn.
4. Kör `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  REST API för Connect-arbetaren är redo för interaktion när du ser `'INFO Finished starting connectors and tasks'`. 

> [!NOTE]
> Event Hubs har stöd för att Kafka-klienter skapar ämnen automatiskt. En snabb kontroll av namnrymden i Azure-portalen visar att Connect-arbetarens interna ämnen har skapats automatiskt.

### <a name="create-connectors"></a>Skapa anslutningsappar
Det här avsnittet vägleder dig genom att skapa anslutningsapparna FileStreamSource och FileStreamSink. 

1. Skapa en katalog för indata- och utdatafiler.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Skapa två filer: en fil med startdata som anslutningsappen FileStreamSource läser från och en annan som anslutningsappen FileStreamSink skriver till.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Skapa en FileStreamSource-anslutningsapp.  Se till att ersätta klamrarna med din startkatalogsökväg.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Du bör se Event Hub `connect-quickstart` på Event Hubs-instansen när du har kört kommandot ovan.
4. Kontrollera statusen för källanslutningsappen.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Du kan även välja att använda [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) för att verifiera att händelserna ankommer till ämnet `connect-quickstart`.

5. Skapa en FileStreamSink-anslutningsapp.  Se även denna gång till att ersätta klamrarna med din startkatalogsökväg.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Kontrollera statusen för kanalmottagarens anslutningsapp.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Kontrollera att data har replikerats mellan filer och att data är identiska i båda filerna.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Rensa
Kafka Connect skapar Event Hub-ämnen för att lagra konfigurationer, förskjutningar och status som består även när Connect-klustret har stängts. Såvida du inte vill att de ska bestå rekommenderas det att dessa ämnen tas bort. Du kan även ta bort händelsehubben `connect-quickstart`, som skapades under loppet av den här genomgången.

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Hubs och Event Hubs för Kafka i följande ämne:  

- [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Så skapar du Kafka-aktiverade händelsehubbar](event-hubs-create-kafka-enabled.md)
- [Strömma till Event Hubs från Kafka-program](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spegla en asynkron Kafka-meddelandekö i en Kafka-aktiverad händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en Kafka-aktiverad händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en Kafka-aktiverad händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Ansluta Akka Streams till en Kafka-aktiverad händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)