---
title: 'Självstudie: Använda Apache Kafka-producent- och konsument-API:er – Azure HDInsight '
description: Lär dig att använda Apache Kafka-producenten och konsument-API:er med Kafka i HDInsight. I självstudien får du lära dig att använda dessa API:er med Kafka i HDInsight från ett Java-program.
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/02/2019
ms.openlocfilehash: 3dead1bdedb75a1b6fafb947da9c88094f0c4de9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115017"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Självstudie: Använda Apache Kafka-producent- och konsument-API:er

Lär dig att använda Apache Kafka-producenten och konsument-API:er med Kafka i HDInsight.

Kafka-producentens API tillåter att program skickar dataströmmar till Kafka-klustret. Kafka-konsumentens API tillåter att program läser dataströmmar från klustret.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Nödvändiga komponenter
> * Förstå koden
> * Skapa och distribuera programmet
> * Köra programmet på klustret

Mer information om API:er finns i Apache-dokumentationen i [Producent-API](https://kafka.apache.org/documentation/#producerapi) och [Konsument-API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Apache Kafka på HDInsight 3.6. Läs hur du skapar en Kafka på HDInsight-kluster i [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md).

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks) eller motsvarande, till exempel OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt buildsystemet för Java-projekt.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Förstå koden

Exempelprogrammet finns på [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) i underkatalogen `Producer-Consumer`. Programmet består i huvudsak av fyra filer:

* `pom.xml`: Den här filen definierar projektberoenden, Java-version och paketeringsmetoder.
* `Producer.java`: Den här filen skickar slumpmässiga meningar till Kafka med hjälp av producent-API:et.
* `Consumer.java`: Den här filen använder konsument-API:et till att läsa data från Kafka och generera dem till STDOUT.
* `Run.java`: Kommandoradsgränssnittet används för att köra producent- och konsumentkoden.

### <a name="pomxml"></a>Pom.xml

Viktiga saker att förstå i `pom.xml`-filen är:

* Beroenden: Det här projektet använder producent- och konsument-API:er i Kafka, som tillhandahålls av `kafka-clients`-paketet. Följande XML-kod definierar detta beroende:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]  
    > `${kafka.version}`-posten har deklarerats i `<properties>..</properties>`-avsnittet i `pom.xml` och är konfigurerad till Kafka-versionen av HDInsight-klustret.

* Plugin-program: Maven-plugin-program tillhandahåller flera olika funktioner. I det här projektet används följande plugin-program:

    * `maven-compiler-plugin`: Används för att ange att den Java-version som används av projektet är 8. Detta är den version av Java som används av HDInsight 3.6.
    * `maven-shade-plugin`: Används för att generera en uber-jar som innehåller det här programmet samt eventuella beroenden. Den används också för att ange startpunkten för programmet, så att du kan köra Jar-filen direkt utan att behöva ange huvudklassen.

### <a name="producerjava"></a>Producer.java

Producenten kommunicerar med värdar för Kafka-meddelandeköer (arbetarnoder) och skickar data till ett Kafka-ämne. Följande kodavsnitt kommer från den [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) fil från den [GitHub-lagringsplatsen](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) och visar hur du ställer in egenskaperna producent:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Konsumenten kommunicerar med värdar för Kafka-meddelandeköer (arbetarnoder) och läser posterna i en loop. Följande kodavsnitt från filen [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) anger konsumentegenskaperna:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

I den här koden är konsumenten konfigurerad att läsa från början av ämnet (`auto.offset.reset` är inställd på `earliest`.)

### <a name="runjava"></a>Run.java

Den [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) filen tillhandahåller ett kommandoradsgränssnitt som kör antingen konsumenten eller producenten kod. Du måste ange värdinformationen om Kafka-meddelandeköerna som en parameter. Du kan också inkludera ett grupp-ID: T värde, som används av konsument-processen. Om du skapar flera instanser av konsumenten med samma grupp-ID, kommer de belastningsutjämna läsa från ämnet.

## <a name="build-and-deploy-the-example"></a>Skapa och distribuera exemplet

1. Ladda ned och extrahera exemplen från [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Ange din aktuella katalog till platsen för den `hdinsight-kafka-java-get-started\Producer-Consumer` katalogen och Använd följande kommando:

    ```cmd
    mvn clean package
    ```

    Det här kommandot skapar en katalog med namnet `target`, som innehåller en fil med namnet `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Ersätt `sshuser` med SSH-användare för klustret och ersätt `CLUSTERNAME` med namnet på klustret. Ange följande kommando för att kopiera den `kafka-producer-consumer-1.0-SNAPSHOT.jar` filen till ditt HDInsight-kluster. Ange lösenordet för SSH-användaren när du uppmanas till det.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> Köra exemplet

1. Ersätt `sshuser` med SSH-användare för klustret och ersätt `CLUSTERNAME` med namnet på klustret. Öppna en SSH-anslutning i klustret, genom att ange följande kommando. Ange lösenordet för SSH-användarkontot om du uppmanas till det.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Installera [jq](https://stedolan.github.io/jq/), en kommandorad JSON-processor. Från den öppna SSH-anslutningen anger du följande kommando för att installera `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Konfigurera miljövariabler. Ersätt `PASSWORD` och `CLUSTERNAME` med inloggningslösenordet för klustret och klustret namnge respektive genom att ange sedan kommandot:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Extrahera korrekt alltid i klustrets namn. Faktiska versaler och gemener i klustrets namn kan skilja sig än förväntat, beroende på hur klustret har skapats. Det här kommandot ska hämta faktiska gemener och versaler, lagra det i en variabel och sedan visa korrekt cased namn och det namn du angav tidigare. Ange följande kommando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Om du vill ha Kafka-Meddelandeköer värdar och Apache Zookeeper-värdar, använder du följande kommando:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

6. Skapa Kafka-ämne `myTest`, genom att ange följande kommando:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

7. Om du vill köra producenten och skriva data till ämnet, använder du följande kommando:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

8. När producenten är klar kan du använda följande kommando för att läsa från ämnet:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    De lästa posterna, tillsammans med antalet poster, visas.

9. Använd __Ctrl + C__ om du vill avsluta konsumenten.

### <a name="multiple-consumers"></a>Flera konsumenter

Kafka-konsumenter använder en konsumentgrupp vid läsning av poster. Att använda samma grupp med flera konsumenter resulterar i belastningsutjämnaravläsningar från ett ämne. Varje konsument i gruppen tar emot en del av posterna.

Konsumentprogrammet accepterar en parameter som används som grupp-ID. Exempelvis startar följande kommando en konsument med hjälp av ett grupp-ID i `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Använd __Ctrl + C__ om du vill avsluta konsumenten.

Använd följande kommando om du vill se hur det fungerar:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Detta kommando använder `tmux` till att dela terminalen i två kolumner. En konsument startas i varje kolumn med samma ID-värde för gruppen. När konsumenterna har läst färdigt kan du se att de bara läst en del av posterna. Använd __Ctrl + C__ två gånger för att avsluta `tmux`.

Förbrukning av klienter i samma grupp hanteras via partitionerna för ämnet. I det här kodexemplet har `test`-ämnet som skapades tidigare åtta partitioner. Om du startar åtta konsumenter läser varje konsument poster från en enda partition i ämnet.

> [!IMPORTANT]  
> Det får inte finnas flera instanser av konsumenten i en konsumentgrupp än partitioner. I det här exemplet kan en konsumentgrupp innehålla upp till åtta konsumenter, eftersom det är antalet partitioner i ämnet. Du kan även ha flera konsumentgrupper med högst åtta konsumenter vardera.

Poster som lagras i Kafka lagras i den ordning som de tas emot inom en partition. För att uppnå sorterad leverans av poster *inom en partition* skapar du en konsumentgrupp där antalet konsumentinstanser matchar antalet partitioner. För att uppnå sorterad leverans av poster *i ämnet* skapar du en konsumentgrupp med bara en konsumentinstans.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig att använda Apache Kafka-producent- och konsument-API:et med Kafka i HDInsight. Använd följande för att lära dig mer om att arbeta med Kafka:

* [Analysera Apache Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Apache Kafka-kluster](apache-kafka-mirroring.md)
* [Apache Kafka Streams-API med HDInsight](apache-kafka-streams-api.md)
