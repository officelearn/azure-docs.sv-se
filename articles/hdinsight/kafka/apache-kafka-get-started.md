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
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 24133adc6e6b16c69a8b124f13e684fce26b115f
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Kom igång med Apache Kafka i HDInsight

Lär dig hur du skapar och använder ett [Apache Kafka](https://kafka.apache.org)-kluster i Azure HDInsight. Kafka är en distribuerad direktuppspelningsplattform med öppen källkod som är tillgänglig i HDInsight. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö.

> [!NOTE]
> Det finns för närvarande två versioner av Kafka med HDInsight; 0.9.0 (HDInsight 3.4) och 0.10.0 (HDInsight 3.5 och 3.6). Stegen i det här dokumentet förutsätter att du använder Kafka på HDInsight 3.6.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Skapa ett Kafka-kluster

Använd följande steg om du vill skapa en Kafka i HDInsight-klustret:

1. Från [Azure Portal](https://portal.azure.com) väljer du **+ NY**, **Intelligence + Analytics** och väljer sedan **HDInsight**.
   
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

    * **Klusternivå**: Standard
     
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
    PASSWORD='your cluster password'
    export KAFKAZKHOSTS=`curl -sS -u admin:$PASSWORD -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin:$PASSWORD -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > Ange `CLUSTERNAME=` till namnet på Kafka-klustret. Ange `PASSWORD=` med inloggningslösenordet (för administratör) som du använde när klustret skapas.

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
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
    ```
   
    Med det här kommandot hämtar du posterna från ämnet och visar dem. Med hjälp av `--from-beginning` anges att konsumenten ska starta från början av direktuppspelningen så att alla poster hämtas.

3. Använd __Ctrl + C__ om du vill stoppa konsumenten.

## <a name="producer-and-consumer-api"></a>Producent- och konsument-API

Du kan även programmässigt producera och använda poster med hjälp av [Kafka-API: er](http://kafka.apache.org/documentation#api). Använd följande steg från din utvecklingsmiljö för att skapa en Java-producent och -konsument.

> [!IMPORTANT]
> Du måste ha följande komponenter installerade i utvecklingsmiljön:
>
> * [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) eller motsvarande, till exempel OpenJDK.
>
> * [Apache Maven](http://maven.apache.org/)
>
> * En SSH-klient och kommandot `scp` . Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ladda ned exempel från [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Använd projektet i katalogen `Producer-Consumer` för exemplet med producent/konsument. Det här exemplet innehåller följande klasser:
   
    * **Kör** – Startar antingen konsumenten eller producenten.

    * **Producent** – Sparar 1 000 000 poster i ämnet.

    * **Konsument** – Läser poster från ämnet.

2. Skapa ett jar-paket genom att navigera till sökvägen för katalogen `Producer-Consumer` och sedan använda följande kommando:

    ```
    mvn clean package
    ```

    Det här kommandot skapar en katalog med namnet `target`, som innehåller en fil med namnet `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Använd följande kommandon för att kopiera filen `kafka-producer-consumer-1.0-SNAPSHOT.jar` till ditt HDInsight-kluster:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Ersätt **SSHUSER** med SSH-användare för klustret och ersätt **CLUSTERNAME** med namnet på klustret. Ange lösenordet för SSH-användaren när du uppmanas till det.

4. När filen kopierats med kommandot `scp` ansluter du till klustret via SSH. Skriv posterna till testämnet med följande kommando:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

5. När processen är klar kan du använda följande kommando för att läsa från ämnet:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    De lästa posterna, tillsammans med antalet poster, visas. Du kan se några fler än 1 000 000 loggade om du har skickat flera poster till ämnet med hjälp av ett skript i ett tidigare steg.

6. Använd __Ctrl + C__ om du vill avsluta konsumenten.

### <a name="multiple-consumers"></a>Flera konsumenter

Kafka-konsumenter använder en konsumentgrupp vid läsning av poster. Att använda samma grupp med flera konsumenter resulterar i belastningsutjämnaravläsningar från ett ämne. Varje konsument i gruppen tar emot en del av posterna. Använd följande steg om du vill se hur det fungerar:

1. Öppna en ny SSH-session i klustret, så att du har två av dessa. Använd följande för att starta en konsument med samma konsumentgrupps-ID i varje session:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    Detta kommando startar en konsument med grupp-ID `mygroup`.

    > [!NOTE]
    > Använd kommandona i avsnittet [Hämta värdinformation för Zookeeper och Broker](#getkafkainfo) till att ange `$KAFKABROKERS` för den här SSH-sessionen.

2. Titta på när varje session räknar posterna som tas emot från ämnet. Summan av båda sessionerna ska vara identisk med den som du tidigare har tagit emot från en konsument.

Förbrukning av klienter i samma grupp hanteras via partitionerna för ämnet. För det `test`-ämne som skapades tidigare har den åtta partitioner. Om du öppnar åtta SSH-sessioner och startar en konsument i alla sessioner, läser varje konsument poster från en partition i ämnet.

> [!IMPORTANT]
> Det får inte finnas flera instanser av konsumenten i en konsumentgrupp än partitioner. I det här exemplet kan en konsumentgrupp innehålla upp till åtta konsumenter, eftersom det är antalet partitioner i ämnet. Du kan även ha flera konsumentgrupper med högst åtta konsumenter vardera.

Poster som lagras i Kafka lagras i den ordning som de tas emot inom en partition. För att uppnå sorterad leverans av poster *inom en partition* skapar du en konsumentgrupp där antalet konsumentinstanser matchar antalet partitioner. För att uppnå sorterad leverans av poster *i ämnet* skapar du en konsumentgrupp med bara en konsumentinstans.

## <a name="streaming-api"></a>Strömmande API

Strömmande API lades till Kafka i version 0.10.0; tidigare versioner är beroende av Apache Spark eller Storm för bearbetning av dataströmmen.

1. Om du inte redan gjort det kan du hämta exemplen från [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) till din utvecklingsmiljö. För direktuppspelningsexemplet använder du projektet i katalogen `streaming`.
   
    Det här projektet innehåller endast en klass `Stream`, som läser poster från ämnet `test` som har skapats tidigare. Den räknar antalet lästa ord och genererar varje ord och antalet till ett ämne som heter `wordcounts`. Ämnet `wordcounts` skapas i ett senare steg i det här avsnittet.

2. Från kommandoraden i din utvecklingsmiljö ändrar du katalogerna till platsen för katalogen `Streaming` och använder sedan följande kommando för att skapa ett jar-paket:

    ```bash
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
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. Starta sedan den direktuppspelade processen med följande kommando:
   
    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    Detta kommando startar den direktuppspelade processen i bakgrunden.

6. Använd följande kommando för att skicka meddelanden till ämnet `test`. Meddelandena behandlas med direktuppspelade exempel:
   
    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. Använd följande kommando för att visa utdata som skrivs till ämnet `wordcounts` genom den direktuppspelade processen:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
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
* [Använda Apache Spark-strömning (DStream) med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Spark Structured Streaming med Kafka på HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ansluta till Kafka via ett trådlöst Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
