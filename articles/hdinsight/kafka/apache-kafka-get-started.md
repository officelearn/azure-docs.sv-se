---
title: "Kom igång med Apache Kafka – Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du skapar ett Apache Kafka-kluster i Azure HDInsight. Lär dig hur du skapar ämnen, prenumeranter och konsumenter."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: 6191d81d6b55f5ffe943f800be542d7ea4614eaf
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Kom igång med Apache Kafka i HDInsight

Lär dig hur du skapar och använder ett [Apache Kafka](https://kafka.apache.org)-kluster i Azure HDInsight. Kafka är en distribuerad direktuppspelningsplattform med öppen källkod som är tillgänglig i HDInsight. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö. Kafka används ofta med Apache Spark och Apache Storm.

> [!NOTE]
> Det finns för närvarande två versioner av Kafka med HDInsight; 0.9.0 (HDInsight 3.4) och 0.10.0 (HDInsight 3.5 och 3.6). Stegen i det här dokumentet förutsätter att du använder Kafka på HDInsight 3.6.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Skapa ett Kafka-kluster

Använd följande steg om du vill skapa en Kafka i HDInsight-klustret:

1. Från [Azure-portalen](https://portal.azure.com) väljer du **+ Skapa en resurs**, **Data och analys** och sedan **HDInsight**.
   
    ![Skapa ett HDInsight-kluster](./media/apache-kafka-get-started/create-hdinsight.png)

2. Från **Grundläggande**, ange följande information:

    * **Klusternamn**: Namnet på HDInsight-klustret.
    * **Prenumeration**: Välj den prenumeration som du vill använda.
    * **Användarnamn för klusterinloggning** och **Lösenord för klusterinloggning**: Inloggningen vid åtkomst till klustret via HTTPS. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till tjänster som Ambari-webbgränssnittet eller REST API.
    * **Secure Shell-användarnamn (SSH)**: Den inloggning som används vid åtkomst till klustret via SSH. Som standard är lösenordet detsamma som lösenordet för klusterinloggning.
    * **Resursgrupp**: Resursgruppen som klustret ska skapas i.
    * **Plats**: Azure-region som klustret ska skapas i.

        > [!IMPORTANT]
        > För hög tillgänglighet för data rekommenderar vi att du väljer en plats (region) som innehåller __tre feldomäner__. Mer information finns i avsnittet [Hög tillgänglighet för data](#data-high-availability).
   
 ![Välj en prenumeration](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Välj **Klustertyp** och ange följande värden från **Klusterkonfiguration**:
   
    * **Klustertyp**: Kafka
    * **Version**: Kafka 0.10.0 (HDI 3.6)

    Slutligen kan spara inställningarna med kommandot **Välj**.
     
 ![Välj klustertyp](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. När du har valt klustertypen anger du klustertypen med hjälp av knappen __Välj__. Använd sedan knappen __Nästa__ och slutföra den grundläggande konfigurationen.

5. Från **Lagring** ska du välja eller skapa ett lagringskonto. Lämna övriga fält på standardvärden för stegen i det här dokumentet. Spara lagringskonfigurationen genom att klicka på __Nästa__.

    ![Ange inställningarna för lagringskontot för HDInsight](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

6. Från __Program (valfritt)__ väljer du __Nästa__ för att fortsätta. Inga program krävs för det här exemplet.

7. Från __Klusterstorlek__ väljer du __Nästa__ för att fortsätta.

    > [!WARNING]
    > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Mer information finns i avsnittet [Hög tillgänglighet för data](#data-high-availability).

    ![Ange klusterstorlek för Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > Antalet **diskar per arbetsnod** anger hur skalbart Kafka är i HDInsight. Kafka på HDInsight använder den lokala disken för virtuella datorer i klustret. Kafka är i/o-stor, och därför används [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) för att tillhandahålla hög genomströmning och ge mer lagringsutrymme per nod. Typen av hanterade diskar kan vara antingen __Standard__ (HDD) eller __Premium__ (SSD). Premiumdiskar används med DS- och GS-serien virtuella datorer. Alla andra typer av virtuella dator använder standard.

8. Från __Avancerade inställningar__ väljer du __Nästa__ för att fortsätta.

9. Från **Sammanfattning** kan du granska konfigurationen för klustret. Använd länkarna __Redigera__ om du behöver ändra eventuella inställningar som är felaktiga. Till sist skapar du klustret genom att klicka på Skapa.
   
    ![Sammanfattning av klusterkonfiguration](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Det kan ta upp till 20 minuter att skapa klustret.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

> [!IMPORTANT]
> När du utför följande steg måste du använda en SSH-klient. Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Anslut till klustret via SSH från klienten:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Ersätt **SSHUSER** med det SSH-användarnamn som du angav när klustret skapades. Ersätt **CLUSTERNAME** med namnet på klustret.

När du uppmanas till det anger du det lösenord som du använde för SSH-kontot.

Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a id="getkafkainfo"></a>Hämta information om värden i Zookeeper och Broker

När du arbetar med Kafka, måste du känna två värdvärden: *Zookeeper*-värdarna och *Broker*-värdarna. Dessa värdar används med Kafka-API och många av de verktyg som levereras med Kafka.

Skapa miljövariabler som innehåller värdinformationen med hjälp av följande steg. Dessa miljövariabler används i stegen i det här dokumentet.

1. Använd följande kommando från en SSH-anslutning till klustret för att installera verktyget `jq`. Det här verktyget används för att parsa JSON-dokument och är användbart vid hämtning av värdinformation för asynkron meddelandekö:
   
    ```bash
    sudo apt -y install jq
    ```

2. Ange miljövariabler med information som hämtas från Ambari med hjälp av följande kommandon:

    ```bash
    CLUSTERNAME='your cluster name'
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > Ange `CLUSTERNAME=` till namnet på Kafka-klustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

    Följande text är ett exempel på innehållet i `$KAFKAZKHOSTS`:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    Följande text är ett exempel på innehållet i `$KAFKABROKERS`:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > Kommandot `cut` används för att trimma listan över värdar till två värdposter. Du behöver inte ange den fullständiga listan över värdar när du skapar en Kafka-konsument eller -producent.
   
    > [!WARNING]
    > Förlita dig inte på att den information som returneras från den här sessionen alltid är korrekt. Om du skalar klustret läggs nya asynkrona meddelandeköer till respektive tas bort. Om ett fel uppstår och en nod ersätts kan värdnamnet för noden ändras.
    >
    > Du bör hämta värdinformation om Zookeeper och asynkron meddelandekö strax innan du använder den för att kontrollera att du har giltig information.

## <a name="create-a-topic"></a>Skapa ett ämne

Kafka lagrar dataströmmar i kategorier som kallas *ämnen*. Från en SSH-anslutning till en klusterhuvudnod använder du ett skript som medföljer Kafka för att skapa ett ämne:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Det här kommandot ansluter till Zookeeper med hjälp av värdinformationen som lagras i `$KAFKAZKHOSTS`, och skapar sedan ett Kafka-ämne med namnet **test**. Du kan verifiera att ämnet har skapats med hjälp av följande skript för att lista ämnen:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

Utdata från det här kommandot visar Kafka-avsnitt som innehåller ämnet **test**.

## <a name="produce-and-consume-records"></a>Skapa och använda poster

Kafka lagrar *poster* i ämnen. Poster produceras av *producenter*, och används av *konsumenter*. Producenter producerar poster till Kafka-*utjämnare*. Varje arbetsnod i HDInsight-klustret är en asynkron Kafka-meddelandekö.

Använd följande steg för att lagra poster i det testämne som du skapade tidigare och läs dem sedan med en konsument:

1. Använd ett skript som medföljer Kafka för att skriva poster till ämnet från SSH-sessionen:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Du kommer inte tillbaka till uppmaningen efter det här kommandot. Skriv i stället ange några textmeddelanden och använd sedan **Ctrl + C** för att sluta skicka till ämnet. Varje rad skickas som en separat post.

2. Använd ett skript som medföljer Kafka för att läsa poster från ämnet:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Med det här kommandot hämtar du posterna från ämnet och visar dem. Med hjälp av `--from-beginning` anges att konsumenten ska starta från början av direktuppspelningen så att alla poster hämtas.

    > [!NOTE]
    > Om du använder en äldre version av Kafka kanske du måste ersätta `--bootstrap-server $KAFKABROKERS` med `--zookeeper $KAFKAZKHOSTS`.

3. Använd __Ctrl + C__ om du vill stoppa konsumenten.

Du kan också programmässigt skapa producenter och konsumenter. Om du vill se ett exempel på att använda denna API kan du läsa dokumentet [Kafka-producent och konsument-API med HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="data-high-availability"></a>Hög tillgänglighet för data

Varje Azure-region (plats) har _feldomäner_. En feldomän är en logisk gruppering av underliggande maskinvara i ett Azure-datacenter. Varje feldomän delar en gemensam strömkälla och nätverksbrytare. De virtuella datorer och hanterade diskar som implementerar noderna i ett HDInsight-kluster är fördelade mellan dessa feldomäner. Den här arkitekturen begränsar de potentiella problemen vid fysiska maskinvarufel.

Om du vill ha information om antalet feldomäner i en region läser du dokumentet [Availability of Linux virtual machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) (Tillgänglighet för virtuella Linux-datorer).

> [!IMPORTANT]
> Vi rekommenderar att du använder en Azure-region som innehåller tre feldomäner, och använder replikeringsfaktorn 3.

Om du måste använda en region som bara har två feldomäner ska du använda replikeringsfaktorn 4, så att replikerna fördelas jämnt mellan de två feldomänerna.

### <a name="kafka-and-fault-domains"></a>Kafka och feldomäner

Kafka har ingen information om feldomäner. När du skapar partitionsrepliker för ämnen kanske det inte distribueras repliker korrekt för hög tillgänglighet. Garantera hög tillgänglighet med [verktyget för ombalansering av Kafka-partitioner](https://github.com/hdinsight/hdinsight-kafka-tools). Du måste köra det här verktyget från en SSH-session till huvudnoden för ditt Kafka-kluster.

Du får bästa möjliga tillgänglighet för dina Kafka-data om du balanserar om partitionsreplikerna för ditt ämne vid följande tidpunkter:

* När du skapar ett nytt ämne eller en ny partition

* När du skalar upp ett kluster

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg

I detta dokument har du lärt dig grunderna för att arbeta med Apache Kafka i HDInsight. Använd följande för att lära dig mer om att arbeta med Kafka:

* [Analysera Kafka-loggar](apache-kafka-log-analytics-operations-management.md)
* [Replikera data mellan Kafka-kluster](apache-kafka-mirroring.md)
* [Kafka-producent och konsument-API med HDInsight](apache-kafka-producer-consumer-api.md)
* [Kafka Streams-API med HDInsight](apache-kafka-streams-api.md)
* [Använda Apache Spark-strömning (DStream) med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Spark Structured Streaming med Kafka på HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Använda Apache Spark Structured Streaming för att flytta data från Kafka på HDInsight till Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ansluta till Kafka via ett trådlöst Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
