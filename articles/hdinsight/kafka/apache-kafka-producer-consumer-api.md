---
title: "Använd Apache Kafka producenten och konsumenten API: er – Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Apache Kafka producenten och konsumenten API: er med Kafka på HDInsight. Dessa API: er kan du utveckla program som kan skriva till och läsa från Apache Kafka."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: b57745d6bd993a993e923c964327d9071e745413
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Apache Kafka producenten och konsumenten API: er

Lär dig hur du skapar ett program som använder Kafka producenten och konsumenten API: er med Kafka på HDInsight.

Dokumentation om API: erna finns [producenten API](https://kafka.apache.org/documentation/#producerapi) och [konsumenten API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Ställ in din utvecklingsmiljö

Du måste ha följande komponenter installerade i utvecklingsmiljön:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) eller motsvarande, till exempel OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* En SSH-klient och kommandot `scp` . Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Konfigurera din distributionsmiljö

Det här exemplet kräver Kafka på HDInsight 3,6. Information om hur du skapar en Kafka på HDInsight-kluster finns i [börjar med Kafka på HDInsight](apache-kafka-get-started.md) dokumentet.

## <a name="build-and-deploy-the-example"></a>Skapa och distribuera exemplet

1. Ladda ned exempel från [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Ändra kataloger till platsen för den `Producer-Consumer` katalogen och Använd följande kommando:

    ```
    mvn clean package
    ```

    Det här kommandot skapar en katalog med namnet `target`, som innehåller en fil med namnet `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Använd följande kommandon för att kopiera filen `kafka-producer-consumer-1.0-SNAPSHOT.jar` till ditt HDInsight-kluster:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Ange lösenordet för SSH-användaren när du uppmanas till det.

## <a id="run"></a>Kör exemplet

1. Om du vill öppna en SSH-anslutning till klustret använder du följande kommando:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Om du uppmanas du ange lösenordet för SSH-användarkontot. Mer information om hur du använder `scp` med HDInsight, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande kommandon för att skapa Kafka ämnen som används av det här exemplet:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Ersätt __klusternamnet__ med namnet på ditt HDInsight-kluster. När du uppmanas, ange lösenordet för inloggningskontot för HDInsight-kluster.

    > [!NOTE]
    > Om din klusterinloggning skiljer sig från standardvärdet `admin`, ersätter den `admin` värdet i de föregående kommandona med ditt inloggningsnamn för klustret.

3. Om du vill köra producenten och skriva data till ämnet, använder du följande kommando:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. När producenten har slutförts använder du följande kommando för att läsa från avsnittet:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    De lästa posterna, tillsammans med antalet poster, visas.

5. Använd __Ctrl + C__ om du vill avsluta konsumenten.

### <a name="multiple-consumers"></a>Flera konsumenter

Kafka-konsumenter använder en konsumentgrupp vid läsning av poster. Att använda samma grupp med flera konsumenter resulterar i belastningsutjämnaravläsningar från ett ämne. Varje konsument i gruppen tar emot en del av posterna.

Konsumenten programmet accepterar en parameter som används som grupp-ID. Exempelvis följande kommando startar konsumenter med hjälp av ett grupp-ID i `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Använd följande steg om du vill se hur det fungerar

1. Upprepa steg 1 och 2 i den [kör exemplet](#run) avsnitt för att öppna en ny SSH-session.

2. I båda SSH-sessioner, anger du följande kommando:

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Ange både kommandon samtidigt, så att de körs båda på samma gång.

    Observera att antalet meddelanden läser inte är samma som i föregående avsnitt, där du hade endast en konsument. I stället delas meddelanden Läs mellan instanser.

Förbrukning av klienter i samma grupp hanteras via partitionerna för ämnet. För det `test`-ämne som skapades tidigare har den åtta partitioner. Om du öppnar åtta SSH-sessioner och startar en konsument i alla sessioner, läser varje konsument poster från en partition i ämnet.

> [!IMPORTANT]
> Det får inte finnas flera instanser av konsumenten i en konsumentgrupp än partitioner. I det här exemplet kan en konsumentgrupp innehålla upp till åtta konsumenter, eftersom det är antalet partitioner i ämnet. Du kan även ha flera konsumentgrupper med högst åtta konsumenter vardera.

Poster som lagras i Kafka lagras i den ordning som de tas emot inom en partition. För att uppnå sorterad leverans av poster *inom en partition* skapar du en konsumentgrupp där antalet konsumentinstanser matchar antalet partitioner. För att uppnå sorterad leverans av poster *i ämnet* skapar du en konsumentgrupp med bara en konsumentinstans.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet beskrivs hur du använder Kafka producenten och konsumenten API med Kafka på HDInsight. Använd följande för att lära dig mer om att arbeta med Kafka:

* [Analysera Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Kafka-kluster](apache-kafka-mirroring.md)
* [Kafka dataströmmar API med HDInsight](apache-kafka-streams-api.md)
* [Använda Apache Spark-strömning (DStream) med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Spark Structured Streaming med Kafka på HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Använda Apache Spark strukturerade strömning för att flytta data från Kafka på HDInsight Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ansluta till Kafka via ett trådlöst Azure-nätverk](apache-kafka-connect-vpn-gateway.md)