---
title: "Komma igång med Apache Kafka i HDInsight | Microsoft Docs"
description: "Lär dig grunderna för att skapa och arbeta med Kafka på HDInsight."
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
ms.date: 02/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1c7a95354358d91859ce9d2019cef93b6662811f
ms.lasthandoff: 03/25/2017

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Kom igång med Apache Kafka (förhandsgranskning) på HDInsight

[Apache Kafka](https://kafka.apache.org) är en distribuerad direktuppspelningsplattform med öppen källkod som är tillgänglig med HDInsight. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö. I det här dokumentet lär du dig att skapa en Kafka på HDInsight-kluster och skicka sedan och ta emot data från ett Java-program.

> [!NOTE]
> Det finns för närvarande två versioner av Kafka med HDInsight; 0.9.0 (HDInsight 3.4) och 0.10.0 (HDInsight 3.5). Stegen i det här dokumentet förutsätter vi att du använder Kafka på HDInsight 3.5.

## <a name="prerequisite"></a>Krav

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

För den här kursen om Apache Kafka krävs följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **kunskap om SSH och SCP**. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) eller motsvarande, till exempel OpenJDK.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>Åtkomstkontrollkrav

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Skapa ett Kafka-kluster

Använd följande steg om du vill skapa en Kafka i HDInsight-klustret:

1. Från [Azure Portal](https://portal.azure.com) väljer du **+ NY**, **Intelligence + Analytics** och väljer sedan **HDInsight**.
   
    ![Skapa ett HDInsight-kluster](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. Ange följande information i bladet **Grundläggande inställningar**:

    * **Klusternamn**: Namnet på HDInsight-klustret.
    * **Prenumeration**: Välj den prenumeration som du vill använda.
    * **Användarnamn för klusterinloggning** och **Lösenord för klusterinloggning**: Inloggningen vid åtkomst till klustret via HTTPS. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till tjänster som Ambari-webbgränssnittet eller REST API.
    * **Secure Shell-användarnamn (SSH)**: Den inloggning som används vid åtkomst till klustret via SSH. Som standard är lösenordet detsamma som lösenordet för klusterinloggning.
    * **Resursgrupp**: Resursgruppen som klustret ska skapas i.
    * **Plats**: Azure-region som klustret ska skapas i.
   
    ![Välj en prenumeration](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Välj **Klustertyp** och ange följande värden på bladet **Klusterkonfiguration**:
   
    * **Klustertyp**: Kafka

    * **Version**: Kafka 0.10.0 (HDI 3.5)

    * **Klusternivå**: Standard
     
    Slutligen kan spara inställningarna med kommandot **Välj**.
     
    ![Välj klustertyp](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

    > [!NOTE]
    > Om din Azure-prenumeration inte har åtkomst till Kafka-förhandsversionen visas instruktioner om hur du får tillgång till förhandsversionen. De instruktioner som visas liknar följande bild:
    >
    > ![förhandsgranskningsmeddelande: Om du vill distribuera ett hanterat Apache Kafka-kluster på HDInsight skickar du e-post till oss och begär åtkomst för förhandsgranskningen](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. När du har valt klustertypen anger du klustertypen med hjälp av knappen __Välj__. Använd sedan knappen __Nästa__ och slutföra den grundläggande konfigurationen.

5. Gå till bladet **Lagring** och välj eller skapa ett lagringskonto. Lämna övriga fält på det här bladet på standardvärden för stegen i det här dokumentet. Spara lagringskonfigurationen genom att klicka på __Nästa__.

    ![Ange inställningarna för lagringskontot för HDInsight](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. Gå till bladet **Sammanfattning** och granska konfigurationen för klustret. Använd länkarna __Redigera__ om du behöver ändra eventuella inställningar som är felaktiga. Till sist skapar du klustret genom att klicka på Skapa.
   
    ![Sammanfattning av klusterkonfiguration](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Det kan ta upp till 20 minuter att skapa klustret.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Anslut till klustret via SSH från klienten. Om du använder en Linux, Unix, MacOS eller Bash i Windows 10 ska du använda följande kommando:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Ersätt **SSHUSER** med det SSH-användarnamn som du angav när klustret skapades. Ersätt **CLUSTERNAME** med namnet på klustret.

När du uppmanas till det anger du det lösenord som du använde för SSH-kontot.

Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

##<a id="getkafkainfo"></a>Hämta information om värden i Zookeeper och Broker

När du arbetar med Kafka, måste du känna två värdvärden: *Zookeeper*-värdarna och *Broker*-värdarna. Dessa värdar används med Kafka-API och många av de verktyg som levereras med Kafka.

Skapa miljövariabler som innehåller värdinformationen med hjälp av följande steg. Dessa miljövariabler används i stegen i det här dokumentet.

1. Använd följande kommando från en SSH-anslutning till klustret för att installera verktyget `jq`. Det här verktyget används för att parsa JSON-dokument och är användbart vid hämtning av värdinformation för asynkron meddelandekö:
   
    ```bash
    sudo apt -y install jq
    ```

2. använd följande kommandon för att ställa in miljövariabler med information som hämtas från Ambari. Ersätt __KAFKANAME__ med namnet på Kafka-klustret. Ersätt __PASSWORD__ med inloggningslösenordet (för administratör) som du använde när klustret skapas.

    ```bash
    export KAFKAZKHOSTS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

    export KAFKABROKERS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Följande text är ett exempel på innehållet i `$KAFKAZKHOSTS`:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    Följande text är ett exempel på innehållet i `$KAFKABROKERS`:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
    > [!WARNING]
    > Förlita dig inte på att den information som returneras från den här sessionen alltid är korrekt. Om du skalar klustret läggs nya asynkrona meddelandeköer till respektive tas bort. Om ett fel uppstår och en nod ersätts kan värdnamnet för noden ändras. 
    > 
    > Du bör hämta värdinformation om Zookeeper och asynkron meddelandekö strax innan du använder den för att kontrollera att du har giltig information.

## <a name="create-a-topic"></a>Skapa ett ämne

Kafka lagrar dataströmmar i kategorier som kallas *ämnen*. Från en SSH-anslutning till en klusterhuvudnod använder du ett skript som medföljer Kafka för att skapa ett ämne:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Det här kommandot ansluter till Zookeeper med hjälp av värdinformationen som lagras i `$KAFKAZKHOSTS`, och skapar sedan ett Kafka-ämne med namnet **test**. Du kan verifiera att ämnet har skapats med hjälp av följande skript för att lista ämnen:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

Utdata från det här kommandot visar Kafka-avsnitt som innehåller ämnet **test**.

## <a name="produce-and-consume-records"></a>Skapa och använda poster

Kafka lagrar *poster* i ämnen. Poster produceras av *producenter*, och används av *konsumenter*. Producenter hämtar poster från asynkrona Kafka-*meddelandeköer*. Varje arbetsnod i HDInsight-klustret är en asynkron Kafka-meddelandekö.

Använd följande steg för att lagra poster i det testämne som du skapade tidigare och läs dem sedan med en konsument:

1. Använd ett skript som medföljer Kafka för att skriva poster till ämnet från SSH-sessionen:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Du kommer inte tillbaka till uppmaningen efter det här kommandot. Skriv i stället ange några textmeddelanden och använd sedan **Ctrl + C** för att sluta skicka till ämnet. Varje rad skickas som en separat post.

2. Använd ett skript som medföljer Kafka för att läsa poster från ämnet:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
    ```
   
    Detta hämtar posterna från ämnena och visar dem. Med hjälp av `--from-beginning` anges att konsumenten ska starta från början av direktuppspelningen så att alla poster hämtas.

3. Använd __Ctrl + C__ om du vill stoppa konsumenten.

## <a name="producer-and-consumer-api"></a>Producent- och konsument-API

Du kan även programmässigt producera och använda poster med hjälp av [Kafka-API: er](http://kafka.apache.org/documentation#api). Använd följande steg för att hämta, skapa en Java-baserad producent och konsument:

1. Ladda ned exempel från [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Använd projektet i katalogen `Producer-Consumer` för exemplet med producent/konsument. Det här exemplet innehåller följande klasser:
   
    * **Kör** – Startar antingen konsumenten eller producenten.

    * **Producent** – Sparar 1 000 000 poster i ämnet.

    * **Konsument** – Läser poster från ämnet.

2. Från kommandoraden i din utvecklingsmiljö ändrar du sökvägarna till platsen för katalogen `Producer-Consumer` med exempel och använder sedan följande kommando för att skapa ett jar-paket:
   
    ```
    mvn clean package
    ```
   
    Det här kommandot skapar en katalog med namnet `target`, som innehåller en fil med namnet `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Använd följande kommandon för att kopiera filen `kafka-producer-consumer-1.0-SNAPSHOT.jar` till ditt HDInsight-kluster:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Ange lösenordet för SSH-användaren när du uppmanas till det.

4. När kommandot `scp` har slutfört kopieringen av filen ansluter du till klustret med SSH och använder sedan följande för att skriva poster till testämnet som du skapade tidigare.
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS
    ```
   
    Detta kommando startar producenten och skriver poster. En räknare visas så att du kan se hur många poster som har skrivits.

    > [!NOTE]
    > Om du ser ett behörighet nekad-fel använder du följande kommando för att göra filen körbar: ```chmod +x kafka-producer-consumer.jar```

5. När processen är klar kan du använda följande kommando för att läsa från ämnet:
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    De lästa posterna, tillsammans med antalet poster, visas. Du kan se några fler än 1 000 000 loggade om du har skickat flera poster till ämnet med hjälp av ett skript i ett tidigare steg.

6. Använd __Ctrl + C__ om du vill avsluta konsumenten.

### <a name="multiple-consumers"></a>Flera konsumenter

Ett viktigt begrepp i Kafka är att konsumenterna använder en konsumentgrupp (definieras med ett grupp-id) när posterna läses. Att använda samma grupp med flera konsumenter resulterar i belastningsutjämnaravläsningar från ett ämne. Varje konsument i gruppen tar emot en del av posterna. Använd följande steg om du vill se hur det fungerar:

1. Öppna en ny SSH-session i klustret, så att du har två av dessa. Använd följande för att starta en konsument med samma konsumentgrupps-ID i varje session:
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    > [!NOTE]
    > Eftersom detta är en ny SSH-session måste du använda kommandona i avsnittet [Hämta värdinformation för Zookeeper och Broker](#getkafkainfo) för att ange `$KAFKABROKERS`.

2. Titta på när varje session räknar posterna som tas emot från ämnet. Summan av båda sessionerna ska vara identisk med den som du tidigare har tagit emot från en konsument.

Förbrukning av klienter i samma grupp hanteras via partitionerna för ämnet. För det `test`-ämne som skapades tidigare har den åtta partitioner. Om du öppnar åtta SSH-sessioner och startar en konsument i alla sessioner, läser varje konsument poster från en partition i ämnet.

> [!IMPORTANT]
> Det får inte finnas flera instanser av konsumenten i en konsumentgrupp än partitioner. I det här exemplet kan en konsumentgrupp innehålla upp till 8 konsumenter, eftersom det är antalet partitioner i ämnet. Du kan även ha flera konsumentgrupper med högst 8 konsumenter vardera.

Poster som lagras i Kafka lagras i den ordning som de tas emot inom en partition. För att uppnå sorterad leverans av poster *inom en partition* skapar du en konsumentgrupp där antalet konsumentinstanser matchar antalet partitioner. För att uppnå sorterad leverans av poster *i ämnet* skapar du en konsumentgrupp med bara en konsumentinstans.

## <a name="streaming-api"></a>Strömmande API

Strömmande API lades till Kafka i version 0.10.0; tidigare versioner är beroende av Apache Spark eller Storm för bearbetning av dataströmmen.

1. Om du inte redan gjort det kan du hämta exemplen från [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) till din utvecklingsmiljö. För direktuppspelningsexemplet använder du projektet i katalogen `streaming`.
   
    Det här projektet innehåller endast en klass `Stream`, som läser poster från ämnet `test` som har skapats tidigare. Den räknar antalet lästa ord och genererar varje ord och antalet till ett ämne som heter `wordcounts`. Ämnet `wordcounts` skapas i ett senare steg i det här avsnittet.

2. Från kommandoraden i din utvecklingsmiljö ändrar du katalogerna till platsen för katalogen `Streaming` och använder sedan följande kommando för att skapa ett jar-paket:
   
    ```
    mvn clean package
    ```
   
    Det här kommandot skapar en katalog med namnet `target`, som innehåller en fil med namnet `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Använd följande kommandon för att kopiera filen `kafka-streaming-1.0-SNAPSHOT.jar` till ditt HDInsight-kluster:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Ange lösenordet för SSH-användaren när du uppmanas till det.

4. När kommandot `scp` har slutfört kopieringen av filen ansluter du till klustret med SSH och använder sedan följande kommando för att skapa ämnet `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. Starta sedan den direktuppspelade processen med följande kommando:
   
    ```bash
    ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    Detta kommando startar den direktuppspelade processen i bakgrunden.

6. Använd följande kommando för att skicka meddelanden till ämnet `test`. Meddelandena behandlas med direktuppspelade exempel:
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. Använd följande kommando för att visa utdata som skrivs till ämnet `wordcounts` genom den direktuppspelade processen:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > Om du vill visa data måste du beordra konsumenten att skriva ut nyckeln och ange vilken funktion för avserialisering som ska användas för nyckeln och värdet. Nyckelnamnet är ordet, och nyckelvärdet innehåller antalet.
   
    De utdata som genereras liknar följande text:
   
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
        a       13805
        snow    13637
   
    > [!NOTE]
    > Antalet ökar varje gång ett ord påträffas.

7. Använd __Ctrl + C__ om du vill avsluta konsumenten och använd sedan kommandot `fg` för att återställa den direktuppspelade bakgrundsaktiviteten till förgrunden igen. Använd __Ctrl + C__ för att avsluta den också.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nästa steg

I detta dokument har du lärt dig grunderna för att arbeta med Apache Kafka på HDInsight. Använd följande för att lära dig mer om att arbeta med Kafka:

* [Apache Kafka-dokumentation](http://kafka.apache.org/documentation.html) på kafka.apache.org.
* [Använd MirrorMaker för att skapa en replik av Kafka på HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Använda Apache Storm med Kafka på HDInsight](hdinsight-apache-storm-with-kafka.md)
* [Använda Apache Spark med Kafka på HDInsight](hdinsight-apache-spark-with-kafka.md)


