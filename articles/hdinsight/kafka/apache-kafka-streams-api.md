---
title: Använd Apache Kafka dataströmmar API - Azure HDInsight | Microsoft Docs
description: 'Lär dig hur du använder API: et för Apache Kafka dataströmmar med Kafka på HDInsight. Detta API kan du utföra bearbetar mellan ämnen i Kafka.'
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 36d67cdb99871f3948db1f6497b1a4638df4f3f1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-streams-api"></a>Apache Kafka dataströmmar API

Lär dig hur du skapar ett program som använder API: et Kafka dataströmmar och köra den med Kafka på HDInsight.

När du arbetar med Apache Kafka dataströmmen bearbetning görs ofta använda Apache Spark eller Storm. Kafka version 0.10.0 (i HDInsight 3.5 och 3,6) introducerades Kafka dataströmmar API. Detta API kan du omvandla dataströmmar mellan inkommande och utgående ämnen, med hjälp av ett program som körs på Kafka. I vissa fall kan kan detta vara ett alternativ till att skapa en Spark eller Storm strömning lösning. Mer information om Kafka dataströmmar finns i [introduktion till dataströmmar](https://kafka.apache.org/10/documentation/streams/) dokumentation på Apache.org.

## <a name="set-up-your-development-environment"></a>Ställ in din utvecklingsmiljö

Du måste ha följande komponenter installerade i utvecklingsmiljön:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) eller motsvarande, till exempel OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* En SSH-klient och kommandot `scp` . Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Konfigurera din distributionsmiljö

Det här exemplet kräver Kafka på HDInsight 3,6. Information om hur du skapar en Kafka på HDInsight-kluster finns i [börjar med Kafka på HDInsight](apache-kafka-get-started.md) dokumentet.

## <a name="build-and-deploy-the-example"></a>Skapa och distribuera exemplet

Använd följande steg för att skapa och distribuera projektet till din Kafka på HDInsight-kluster.

1. Hämta exempel från [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Ändra sökvägen till den `Streaming` katalogen och Använd sedan följande kommando för att skapa ett jar-paket:

    ```bash
    mvn clean package
    ```

    Det här kommandot skapar en katalog med namnet `target`, som innehåller en fil med namnet `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Använd följande kommando för att kopiera den `kafka-streaming-1.0-SNAPSHOT.jar` filen till ditt HDInsight-kluster:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Om du uppmanas du ange lösenordet för SSH-användarkontot. Mer information om hur du använder `scp` med HDInsight, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-the-example"></a>Kör exemplet

1. Om du vill öppna en SSH-anslutning till klustret använder du följande kommando:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Om du uppmanas du ange lösenordet för SSH-användarkontot. Mer information om hur du använder `scp` med HDInsight, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Använd följande kommandon för att skapa Kafka ämnen som används av det här exemplet:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Ersätt __klusternamnet__ med namnet på ditt HDInsight-kluster. När du uppmanas, ange lösenordet för inloggningskontot för HDInsight-kluster.

    > [!NOTE]
    > Om din klusterinloggning skiljer sig från standardvärdet `admin`, ersätter den `admin` värdet i de föregående kommandona med ditt inloggningsnamn för klustret.

5. Om du vill köra det här exemplet måste du göra tre saker:

    * Starta dataströmmar lösningen finns i `kafka-streaming.jar`.
    * Starta en producent som skriver till den `test` avsnittet.
    * Starta en konsument så att du kan se utdata skrivs till den `wordcounts` avsnittet

    > [!NOTE]
    > Du måste kontrollera att den `auto.create.topics.enable` egenskap är inställd på `true` i konfigurationsfilen Kafka Broker. Den här egenskapen kan visa och ändra i filen avancerade Kafka Broker-konfigurationen genom att använda Ambari-Webbgränssnittet. I annat fall måste du skapa avsnittet mellanliggande `RekeyedIntermediateTopic` manuellt innan du kör det här exemplet använder du följande kommando:
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    Du kan utföra dessa åtgärder genom att öppna tre SSH-sessioner. Men du måste sedan ange `$KAFKABROKERS` och `$KAFKAZKHOSTS` för varje genom att köra steg 4 från det här avsnittet i varje SSH-session. En enklare lösning är att använda den `tmux` verktyg som kan dela den aktuella SSH visningen i flera delar. Att starta stream, tillverkare och konsumenter med hjälp av `tmux`, använder du följande kommando:

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    Det här kommandot delar SSH visningen i tre delar:

    * Den vänstra sidan kör konsolen konsumenter, vilka läser meddelanden från den `wordcounts` avsnittet: `/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > Den `--property` parametrar berätta konsolen konsumenten att skriva ut nyckel (word) tillsammans med antalet (värde). Den här parametern konfigurerar även funktionen för avserialisering ska användas vid läsning av dessa värden från Kafka.

    * Den övre högra delen körs dataströmmar API-lösningen: `java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * Det nedre högra avsnittet kör konsolen producenten och väntar på att ange meddelanden ska skickas till den `test` avsnittet: `/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. Efter den `tmux` kommandot delar visningen, markören är i det nedre högra avsnittet. Börja registrera meningar. Det vänstra fönstret uppdateras efter varje mening för att visa antalet unika ord. De utdata som genereras liknar följande text:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > Antalet ökar varje gång ett ord påträffas.

7. Använd den __Ctrl + C__ avsluta producenten. Fortsätta att använda __Ctrl + C__ avsluta programmet och konsumenter.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet du har lärt dig hur du använder API: et Kafka dataströmmar med Kafka på HDInsight. Använd följande för att lära dig mer om att arbeta med Kafka:

* [Analysera Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Kafka-kluster](apache-kafka-mirroring.md)
* [Kafka-producent och konsument-API med HDInsight](apache-kafka-producer-consumer-api.md)
* [Använda Apache Spark-strömning (DStream) med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Spark Structured Streaming med Kafka på HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Använda Apache Spark Structured Streaming för att flytta data från Kafka på HDInsight till Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ansluta till Kafka via ett trådlöst Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
