---
title: Spegling av Apache Kafka-avsnitt – Azure HDInsight
description: Lär dig hur du använder Apache Kafka databasspegling funktionen för att underhålla en replik av en Kafka på HDInsight-klustret genom att spegla ämnen till ett sekundärt kluster.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 0c37ad6de867c4abe4ebf0e6c7a40b5cf27c4541
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079647"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Använd MirrorMaker för att replikera Apache Kafka-ämnen med Kafka på HDInsight

Lär dig hur du använder Apache Kafka databasspegling funktionen för att replikera ämnen till ett sekundärt kluster. Spegling kan kördes som en kontinuerlig process eller används ibland som en metod för att migrera data från ett kluster till en annan.

I det här exemplet används spegling för att replikera ämnen mellan två kluster med HDInsight. Båda klustren finns i ett virtuellt Azure-nätverk i samma region.

> [!WARNING]  
> Spegling ska inte betraktas som ett sätt att uppnå feltolerans. Förskjutningen till objekt i ett ämne skiljer sig mellan käll- och -kluster, så att klienter inte kan använda två synonymt.
>
> Om du är orolig feltolerans, anger du replikering för ämnena i klustret. Mer information finns i [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Så här fungerar Apache Kafka spegling

Spegling fungerar med hjälp av den [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) verktyget (del av Apache Kafka) att använda poster ämnen på källklustret och sedan skapa en lokal kopia på målklustret. MirrorMaker använder en (eller flera) *konsumenter* som läser från källklustret, och en *producent* som skriver till klustret för lokal (mål).

Följande diagram illustrerar processen spegling:

![Diagram över databasspegling processen](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka på HDInsight ger inte tillgång till Kafka-tjänsten via det offentliga internet. Kafka producenter och konsumenter måste vara i samma Azure-nätverk som noder i Kafka-klustret. I det här exemplet finns både Kafka-käll- och kluster i Azure-nätverk. Följande diagram visar hur kommunikation flödar mellan kluster:

![Diagram över käll- och Kafka-kluster i Azure-nätverk](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Käll- och -kluster kan vara annorlunda i antalet noder och partitioner och förskjutningar i avsnittet skiljer sig också. Spegling underhåller nyckelvärdet som används för partitionering, så att posten ordning bevaras på basis av per nyckel.

### <a name="mirroring-across-network-boundaries"></a>Spegling över nätverksgränser

Om du vill spegla mellan Kafka-kluster i olika nätverk, finns det ytterligare aspekter:

* **Gatewayer**: Nätverk måste kunna kommunicera på TCPIP-nivå.

* **Namnmatchning**: Kafka-kluster i varje nätverk måste kunna ansluta till varandra med hjälp av värdnamn. Du kan behöva en Domain Name System (DNS)-server i varje nätverk som är konfigurerad för att vidarebefordra begäranden till andra nätverk.

    När du skapar ett virtuellt nätverk i Azure, istället för att använda automatisk DNS medföljer i nätverket, måste du ange en anpassad DNS-server och IP-adressen för servern. När du har skapat det virtuella nätverket, måste du sedan skapa en Azure-dator som använder den IP-adressen, och sedan installera och konfigurera DNS-programvara på den.

    > [!WARNING]  
    > Skapa och konfigurera anpassade DNS-servern innan du installerar HDInsight till det virtuella nätverket. Det finns ingen ytterligare konfiguration krävs för HDInsight att använda DNS-server som konfigurerats för det virtuella nätverket.

Mer information om hur du ansluter två virtuella Azure-nätverk finns i [konfigurera en VNet-till-VNet-anslutning](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-apache-kafka-clusters"></a>Skapa Apache Kafka-kluster

Du kan skapa ett Azure-nätverk och Kafka-kluster manuellt, men det är enklare att använda en Azure Resource Manager-mall. Använd följande steg för att distribuera ett Azure-nätverk och två Kafka-kluster på Azure-prenumerationen.

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager-mallen finns i **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]  
    > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka-kluster som innehåller tre arbetarnoder.

2. Använd följande information för att fylla i posterna i den **anpassad distribution** bladet:
    
    ![Anpassade HDInsight-distribution](./media/apache-kafka-mirroring/parameters.png)
    
    * **Resursgrupp**: Skapa en grupp eller välj en befintlig. Den här gruppen innehåller HDInsight-klustret.

    * **Plats**: Välj en plats geografiskt nära dig.
     
    * **Basera klusternamnet**: Det här värdet används som det grundläggande namnet på Kafka-kluster. Till exempel innebär **hdi** skapar kluster med namnet **käll-hdi** och **dest hdi**.

    * **Kluster-användarnamnet för inloggningen**: Administratörsanvändarnamn för källan och målet Kafka-kluster.

    * **Kluster inloggningslösenordet**: Administratörslösenord för källan och målet Kafka-kluster.

    * **SSH-användarnamn**: SSH-användaren skapa för källan och målet Kafka-kluster.

    * **SSH lösenord**: Lösenordet för SSH-användare för källan och målet Kafka-kluster.

3. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

4. Markera slutligen **Fäst på instrumentpanelen** och välj sedan **Inköp**. Det tar cirka 20 minuter för att skapa kluster.

> [!IMPORTANT]  
> Namnet på HDInsight-kluster är **källa BASENAME** och **dest BASENAME**, där BASENAME är det namn du angav i mallen. Du kan använda dessa namn i senare steg när du ansluter till klustren.

## <a name="create-topics"></a>Skapa ämnen

1. Ansluta till den **källa** klustret med hjälp av SSH:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med SSH-användarnamnet som används när klustret skapas. Ersätt **BASENAME** med det grundläggande namnet som används när klustret skapas.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommandon för att hitta Apache Zookeeper-värdar för källklustret:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ersätt `$CLUSTERNAME` med namnet på källklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

3. Skapa ett ämne med namnet `testtopic`, använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Använd följande kommando för att verifiera att ämnet har skapats:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    Svaret innehåller `testtopic`.

4. Använd följande för att visa Zookeeper-värdens information för detta (den **källa**) kluster:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Detta returnerar information liknande följande text:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Spara den här informationen. Den används i nästa avsnitt.

## <a name="configure-mirroring"></a>Konfigurera spegling

1. Ansluta till den **mål** klustret med hjälp av en annan SSH-session:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med SSH-användarnamnet som används när klustret skapas. Ersätt **BASENAME** med det grundläggande namnet som används när klustret skapas.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. En `consumer.properties` används för att konfigurera kommunikation med den **källa** kluster. Använd följande kommando för att skapa filen:

    ```bash
    nano consumer.properties
    ```

    Använd följande text som innehållet i den `consumer.properties` fil:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Ersätt **SOURCE_ZKHOSTS** med värdinformation för Zookeeper från den **källa** kluster.

    Den här filen beskriver konsumentinformation för användning vid läsning från källan Kafka-kluster. Mer information konsument konfiguration finns i [konsument Peeringkonfigurationer](https://kafka.apache.org/documentation#consumerconfigs) på kafka.apache.org.

    Om du vill spara filen, Använd **Ctrl + X**, **Y**, och sedan **RETUR**.

3. Innan du konfigurerar producenten som kommunicerar med målklustret, måste du söka efter den asynkrona meddelandekön värdar för den **mål** kluster. Använd följande kommandon för att hämta den här informationen:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Ersätt `$CLUSTERNAME` med namnet på målklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

    Den `echo` kommandot returnerar information liknande följande text:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. En `producer.properties` används för att kommunicera i __mål__ kluster. Använd följande kommando för att skapa filen:

    ```bash
    nano producer.properties
    ```

    Använd följande text som innehållet i den `producer.properties` fil:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Ersätt **DEST_BROKERS** med broker information från föregående steg.

    Mer information producent konfiguration finns i [producent Peeringkonfigurationer](https://kafka.apache.org/documentation#producerconfigs) på kafka.apache.org.

5. Använd följande kommandon för att hitta Zookeeper-värdar för målklustret:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ersätt `$CLUSTERNAME` med namnet på målklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

7. Standardkonfigurationen för Kafka på HDInsight tillåter inte automatisk generering av ämnen. Du måste använda en av följande alternativ innan du börjar spegling:

    * **Skapa i avsnitt på målklustret**: Det här alternativet kan du ange hur många partitioner och replikeringsfaktorn.

        Du kan skapa ämnen förbereds i förväg med hjälp av följande kommando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Ersätt `testtopic` med namnet på ämnet för att skapa.

    * **Konfigurera klustret för att skapa automatiska ämne**: Det här alternativet kan MirrorMaker att automatiskt skapa ämnen, men det kan skapa dem med ett annat antal partitioner eller replikeringsfaktor än käll-avsnittet.

        Utför de här stegen för att konfigurera målklustret för att automatiskt skapa ämnen:

        1. Från den [Azure-portalen](https://portal.azure.com), Välj mål Kafka-kluster.
        2. Översikt över kluster väljer du __klusterinstrumentpanel__. Välj sedan __HDInsight-klusterinstrumentpanel__. När du uppmanas, kan du autentisera med autentiseringsuppgifterna för inloggning (admin) för klustret.
        3. Välj den __Kafka__ tjänsten i listan till vänster på sidan.
        4. Välj __Peeringkonfigurationer__ mitt på sidan.
        5. I den __Filter__ fältet, anger du värdet `auto.create`. Detta filtrerar listan över egenskaper och visar den `auto.create.topics.enable` inställningen.
        6. Ändra värdet för `auto.create.topics.enable` true och välj sedan __spara__. Lägga till en anteckning och välj sedan __spara__ igen.
        7. Välj den __Kafka__ tjänsten, väljer __starta om__, och välj sedan __starta om alla berörda__. När du uppmanas, väljer __bekräfta starta om alla__.

## <a name="start-mirrormaker"></a>Starta MirrorMaker

1. Från SSH-anslutningen till den **mål** kluster, Använd följande kommando för att starta MirrorMaker-processen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    De parametrar som används i det här exemplet är:

    * **--consumer.config**: Anger den fil som innehåller egenskaper för konsument. Dessa egenskaper används för att skapa en konsument läser från den *källa* Kafka-kluster.

    * **--producer.config**: Anger den fil som innehåller egenskaper för producenten. Dessa egenskaper används för att skapa en producent som skriver till den *mål* Kafka-kluster.

    * **--whitelist**: En lista över ämnen som MirrorMaker som replikerar från källklustret till målet.

    * **--num.streams**: Antal trådar som konsument ska skapa.

   Vid start, kan MirrorMaker returnerar information liknande följande text:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Från SSH-anslutningen till den **källa** kluster, Använd följande kommando för att starta en producent och skicka meddelanden till ämnet:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Ersätt `$CLUSTERNAME` med namnet på källklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

     När du kommer till en tom rad med en markör ange några textmeddelanden. Meddelandet har skickats till ämnet den **källa** kluster. När du är klar kan du använda **Ctrl + C** att avsluta processen producenten.

3. Från SSH-anslutningen till den **mål** kluster, använda **Ctrl + C** att avsluta processen MirrorMaker. Det kan ta flera sekunder att avsluta processen. Om du vill kontrollera att meddelanden har replikerats till målet, använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Ersätt `$CLUSTERNAME` med namnet på målklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

    Listan med avsnitt innehåller nu `testtopic`, som skapas när MirrorMaster speglar avsnittet från källklustret till målet. Meddelanden som har hämtats från ämnet har samma värde som anges på källklustret.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Eftersom stegen i det här dokumentet skapar båda klustren i samma Azure-resursgruppen kan du ta bort resursgruppen på Azure-portalen. Ta bort resursgruppen tar bort alla resurser som skapats genom att följa det här dokumentet, Azure Virtual Network och storage-konto som används av klustren.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) att skapa en replik av en [Apache Kafka](https://kafka.apache.org/) kluster. Använd följande länkar för att upptäcka andra sätt att arbeta med Kafka:

* [Dokumentation om Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) på cwiki.apache.org.
* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)
* [Använda Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Anslut till Apache Kafka via ett Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
