---
title: 'Självstudie: Använda Apache Kafka-producenten och konsument-API:er – Azure HDInsight '
description: Lär dig att använda Apache Kafka-producenten och konsument-API:er med Kafka i HDInsight. I självstudien får du lära dig att använda dessa API:er med Kafka i HDInsight från ett Java-program.
services: hdinsight
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.openlocfilehash: 2a441e3cd90eba8fc2b1201671047cfcd9d277a6
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277740"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Självstudie: Använda Apache Kafka-producenten och konsument-API:er

Lär dig att använda Kafka-producenten och konsument-API:er med Kafka i HDInsight.

Kafka-producentens API tillåter att program skickar dataströmmar till Kafka-klustret. Kafka-konsumentens API tillåter att program läser dataströmmar från klustret.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ställ in din utvecklingsmiljö
> * Konfigurera din distributionsmiljö
> * Förstå koden
> * Skapa och distribuera programmet
> * Köra programmet på klustret

Mer information om API:er finns i Apache-dokumentationen i [Producent-API](https://kafka.apache.org/documentation/#producerapi) och [Konsument-API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Ställ in din utvecklingsmiljö

Du måste ha följande komponenter installerade i utvecklingsmiljön:

* [Java JDK 8](https://aka.ms/azure-jdks) eller motsvarande, till exempel OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* En SSH-klient och kommandot `scp` . Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Ett redigeringsprogram eller Java IDE.

Följande miljövariabler kan konfigureras när du installerar Java och JDK på utvecklingsdatorn. Du bör dock kontrollera att de finns och att de innehåller rätt värden för ditt system.

* `JAVA_HOME` – ska peka på den katalog där JDK har installerats.
* `PATH` – ska innehålla följande sökvägar:
  
    * `JAVA_HOME` (eller motsvarande sökväg).
    * `JAVA_HOME\bin` (eller motsvarande sökväg).
    * Den katalog där Maven är installerat.

## <a name="set-up-your-deployment-environment"></a>Konfigurera din distributionsmiljö

Den här självstudien kräver att Apache Kafka finns i HDInsight 3.6. Information om hur du skapar en Kafka på ett HDInsight-kluster finns i dokumentet [Starta med Kafka i HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Förstå koden

Exempelprogrammet finns på [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) i underkatalogen `Producer-Consumer`. Programmet består i huvudsak av fyra filer:

* `pom.xml`: Den här filen definierar projektberoenden, Java-version och paketeringsmetoder.
* `Producer.java`: Den här filen skickar slumpmässiga meningar till Kafka med producent-API:et.
* `Consumer.java`: Den här filen använder konsument-API:n till att läsa data från Kafka och generera den till STDOUT.
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

* Plugin-program: Plugin-programmet Maven innehåller olika funktioner. I det här projektet används följande plugin-program:

    * `maven-compiler-plugin`: Används för att ange att den Java-version som används av projektet är 8. Detta är den version av Java som används av HDInsight 3.6.
    * `maven-shade-plugin`: Används för att generera en Uber-jar som både innehåller det här programmet och eventuella beroenden. Den används också för att ange startpunkten för programmet, så att du kan köra Jar-filen direkt utan att behöva ange huvudklassen.

### <a name="producerjava"></a>Producer.java

Producenten kommunicerar med värdar för Kafka-meddelandeköer (arbetarnoder) och skickar data till ett Kafka-ämne. Följande kodavsnitt är från filen [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) på [github-lagringsplatsen](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) och visar hur du anger producentegenskaperna:

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

[Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java)-filen innehåller ett kommandoradsgränssnitt som antingen kör producent- eller konsumentkoden. Du måste ange värdinformationen om Kafka-meddelandeköerna som en parameter. Du kan också inkludera ett grupp-ID som används av konsumentprocessen. Om du skapar flera konsumentinstanser med samma grupp-ID, kommer de belastningsutjämna läsningen från ämnet.

## <a name="build-and-deploy-the-example"></a>Skapa och distribuera exemplet

1. Hämta exempel från [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Ändra kataloger till platsen för katalogen `Producer-Consumer` och använd följande kommando:

    ```
    mvn clean package
    ```

    Det här kommandot skapar en katalog med namnet `target`, som innehåller en fil med namnet `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Använd följande kommandon för att kopiera filen `kafka-producer-consumer-1.0-SNAPSHOT.jar` till ditt HDInsight-kluster:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Ange lösenordet för SSH-användaren när du uppmanas till det.

## <a id="run"></a> Köra exemplet

1. Använd följande kommando för att öppna en SSH-anslutning till klustret:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Ange lösenordet för SSH-användarkontot om du uppmanas till det. Mer information om hur du använder `scp` med HDInsight finns i [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande steg för att skapa de Kafka-ämnen som används i exemplet:

    1. Spara klustrets namn som en variabel och installera ett JSON-parsningsverktyg (`jq`) med följande kommandon. Ange Kafka-klustrets namn när du uppmanas till detta:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Använd följande kommandon för att hämta värdar för Kafka-meddelandeköer och Zookeeper. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).
    
        ```bash
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Om du vill skapa ämnet `test` använder du följande kommando:

        ```bash
        java -jar kafka-producer-consumer.jar create test $KAFKABROKERS
        ```

3. Om du vill köra producenten och skriva data till ämnet, använder du följande kommando:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. När producenten är klar kan du använda följande kommando för att läsa från ämnet:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```
   
    De lästa posterna, tillsammans med antalet poster, visas.

5. Använd __Ctrl + C__ om du vill avsluta konsumenten.

### <a name="multiple-consumers"></a>Flera konsumenter

Kafka-konsumenter använder en konsumentgrupp vid läsning av poster. Att använda samma grupp med flera konsumenter resulterar i belastningsutjämnaravläsningar från ett ämne. Varje konsument i gruppen tar emot en del av posterna.

Konsumentprogrammet accepterar en parameter som används som grupp-ID. Exempelvis startar följande kommando en konsument med hjälp av ett grupp-ID i `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

Använd följande kommando om du vill se hur det fungerar:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

Detta kommando använder `tmux` till att dela terminalen i två kolumner. En konsument startas i varje kolumn med samma ID-värde för gruppen. När konsumenterna har läst färdigt kan du se att de bara läst en del av posterna. Använd __Ctrl + C __ två gånger för att avsluta `tmux`.

Förbrukning av klienter i samma grupp hanteras via partitionerna för ämnet. I det här kodexemplet har `test`-ämnet som skapades tidigare åtta partitioner. Om du startar åtta konsumenter läser varje konsument poster från en enda partition i ämnet.

> [!IMPORTANT]
> Det får inte finnas flera instanser av konsumenten i en konsumentgrupp än partitioner. I det här exemplet kan en konsumentgrupp innehålla upp till åtta konsumenter, eftersom det är antalet partitioner i ämnet. Du kan även ha flera konsumentgrupper med högst åtta konsumenter vardera.

Poster som lagras i Kafka lagras i den ordning som de tas emot inom en partition. För att uppnå sorterad leverans av poster *inom en partition* skapar du en konsumentgrupp där antalet konsumentinstanser matchar antalet partitioner. För att uppnå sorterad leverans av poster *i ämnet* skapar du en konsumentgrupp med bara en konsumentinstans.

## <a name="next-steps"></a>Nästa steg

I dokumentet har du lärt dig att använda Kafka-producenten och konsument-API:n med Kafka i HDInsight. Använd följande för att lära dig mer om att arbeta med Kafka:

* [Analysera Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Kafka-kluster](apache-kafka-mirroring.md)
* [Kafka Streams-API med HDInsight](apache-kafka-streams-api.md)
