---
title: Spegling Apache Kafka - avsnitt i Azure HDInsight | Microsoft Docs
description: Lär dig använda Apache Kafka spegling för att underhålla en replik av en Kafka på HDInsight-kluster genom att spegla avsnitt till ett sekundärt kluster.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2018
ms.author: larryfr
ms.openlocfilehash: 39d7eeaf565a6c58bac162d110e6fd1cd96ad1f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Använd MirrorMaker för att replikera Apache Kafka avsnitt med Kafka på HDInsight

Lär dig använda Apache Kafka databasspegling funktionen för att replikera avsnitt till sekundära kluster. Spegling kan kördes som en kontinuerlig process eller används ibland som en metod för att migrera data från ett kluster.

I det här exemplet används spegling för att replikera information mellan två HDInsight-kluster. Båda klustren finns i ett Azure Virtual Network i samma region.

> [!WARNING]
> Spegling ska inte ses som ett sätt att uppnå feltolerans. Förskjutning av objekt inom ett ämne skiljer sig mellan käll- och kluster, så att klienter inte kan använda två synonymt.
>
> Om du är orolig feltolerans, anger du replikering för avsnitten i klustret. Mer information finns i [Kom igång med Kafka på HDInsight](apache-kafka-get-started.md).

## <a name="how-kafka-mirroring-works"></a>Så här fungerar Kafka spegling

Spegling fungerar med hjälp av verktyget MirrorMaker (del av Apache Kafka) att använda poster ämnen på källklustret och sedan skapa en lokal kopia på målklustret. MirrorMaker använder (minst) *konsumenter* som läses från källklustret och en *producenten* som skriver till lokala (mål) klustret.

Följande diagram illustrerar processen spegling:

![Diagram över databasspegling processen](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka på HDInsight ger inte tillgång till tjänsten Kafka via det offentliga internet. Kafka producenter och konsumenter måste vara i samma virtuella Azure-nätverket som noder i klustret Kafka. I det här exemplet finns både Kafka käll- och kluster i Azure-nätverk. Följande diagram visar hur kommunikation som flödar mellan kluster:

![Diagram över käll- och Kafka-kluster i Azure-nätverk](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Käll- och kluster kan skilja sig i antal noder och partitioner och förskjutningar i avsnittet skiljer sig också. Spegling underhåller värdet för nyckeln som används för partitionering, så poster ordning bevaras på grundval av per nyckel.

### <a name="mirroring-across-network-boundaries"></a>Spegling över nätverksgränser

Om du behöver spegling mellan Kafka kluster i olika nätverk finns följande ytterligare överväganden:

* **Gateways**: nätverk måste kunna kommunicera på TCPIP-nivå.

* **Namnmatchning**: den Kafka kluster i varje nätverk måste kunna ansluta till varandra med hjälp av värdnamn. Det här kan kräva en Domain Name System (DNS)-server i varje nätverk som har konfigurerats som vidarebefordrar begäranden till andra nätverk.

    När du skapar ett virtuellt Azure-nätverk istället för att använda automatisk DNS som ingår i nätverket, måste du ange en anpassad DNS-server och IP-adressen för servern. När det virtuella nätverket har skapats, måste du skapa en virtuell Azure-dator som använder den IP-adressen och sedan installera och konfigurera DNS-programvaran på den.

    > [!WARNING]
    > Skapa och konfigurera anpassade DNS-servern innan du installerar HDInsight till det virtuella nätverket. Det finns ingen ytterligare konfiguration krävs för HDInsight för att använda DNS-server som konfigurerats för det virtuella nätverket.

Mer information om hur du ansluter två virtuella Azure-nätverk finns [konfigurera VNet-till-VNet-anslutningen](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Skapa Kafka kluster

Du kan skapa ett virtuellt Azure-nätverk och Kafka kluster manuellt, men det är enklare att använda en Azure Resource Manager-mall. Använd följande steg för att distribuera ett virtuellt Azure-nätverk och två kluster med Kafka till din Azure-prenumeration.

1. Använd knappen följande för att logga in på Azure och öppna mallen i Azure-portalen.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager-mallen finns på **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka kluster som innehåller tre arbetsnoderna.

2. Använd följande information för att fylla i posterna på den **anpassad distribution** bladet:
    
    ![HDInsight anpassad distribution](./media/apache-kafka-mirroring/parameters.png)
    
    * **Resursgruppen**: skapa en grupp eller välj en befintlig. Den här gruppen innehåller HDInsight-klustret.

    * **Plats**: Välj en plats geografiskt nära dig.
     
    * **Basera klusternamnet**: det här värdet används som det grundläggande namnet för Kafka-kluster. Ange till exempel **hdi** skapar kluster med namnet **källa hdi** och **dest hdi**.

    * **Klustrets inloggningsnamn**: admin användarnamn för käll- och Kafka-kluster.

    * **Klustret inloggningslösenordet**: administratörslösenord för käll- och Kafka-kluster.

    * **SSH-användarnamn**: SSH-användare för käll- och Kafka Skapa kluster.

    * **SSH-lösenordet**: lösenord för SSH-användare för käll- och Kafka-kluster.

3. Läs den **villkor**, och välj sedan **jag samtycker till villkoren som anges ovan**.

4. Kontrollera slutligen **fäst på instrumentpanelen** och välj sedan **inköp**. Det tar ungefär 20 minuter för att skapa kluster.

> [!IMPORTANT]
> Namnet på HDInsight-kluster är **källa BASENAME** och **dest BASENAME**, där BASENAME är det namn du angav i mallen. Du kan använda dessa namn i senare steg när du ansluter till kluster.

## <a name="create-topics"></a>Skapa avsnitt

1. Ansluta till den **källa** kluster med SSH:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med SSH-användarnamn som används när du skapar klustret. Ersätt **BASENAME** med det grundläggande namnet som används när du skapar klustret.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommandon för att hitta Zookeeper-värdar för källklustret:

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

3. Använder du följande kommando för att verifiera att avsnittet skapades:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    Svaret innehåller `testtopic`.

4. Använd följande för att visa information om Zookeeper värden för den här (den **källa**) klustret:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Information returneras liknar följande:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Spara den här informationen. Den används i nästa avsnitt.

## <a name="configure-mirroring"></a>Konfigurera spegling

1. Ansluta till den **mål** kluster med hjälp av en annan SSH-session:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med SSH-användarnamn som används när du skapar klustret. Ersätt **BASENAME** med det grundläggande namnet som används när du skapar klustret.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. En `consumer.properties` används för att konfigurera kommunikation med den **källa** klustret. Om du vill skapa filen använder du följande kommando:

    ```bash
    nano consumer.properties
    ```

    Använd följande text som innehållet i den `consumer.properties` filen:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Ersätt **SOURCE_ZKHOSTS** med Zookeeper värdar information från den **källa** klustret.

    Den här filen beskriver konsumenten ska användas vid läsning från källan Kafka klustret. Mer information konsumenten konfiguration finns i [konsumenten konfigurationerna](https://kafka.apache.org/documentation#consumerconfigs) på kafka.apache.org.

    Om du vill spara filen, Använd **Ctrl + X**, **Y**, och sedan **RETUR**.

3. Innan du konfigurerar producenten som kommunicerar med målklustret bör du hitta Service broker-värdar för den **mål** klustret. Använd följande kommandon för att hämta den här informationen:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Ersätt `$CLUSTERNAME` med namnet på målklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

    Den `echo` kommandot returnerar informationen som liknar följande:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. En `producer.properties` används för att kommunicera på __mål__ klustret. Om du vill skapa filen använder du följande kommando:

    ```bash
    nano producer.properties
    ```

    Använd följande text som innehållet i den `producer.properties` filen:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Ersätt **DEST_BROKERS** med broker information från föregående steg.

    Mer information producenten konfiguration finns i [producenten konfigurationerna](https://kafka.apache.org/documentation#producerconfigs) på kafka.apache.org.

5. Använd följande kommandon för att hitta Zookeeper-värdar för målklustret:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ersätt `$CLUSTERNAME` med namnet på målklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

7. Standardkonfigurationen för Kafka på HDInsight tillåter inte automatisk generering av avsnitt. Du måste använda en av följande alternativ innan du börjar spegling:

    * **Skapa ämnena på målklustret**: det här alternativet kan du ange antalet partitioner och replikering faktorn.

        Du kan skapa avsnitt i förväg med hjälp av följande kommando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Ersätt `testtopic` med namnet på avsnittet om du vill skapa.

    * **Konfigurera klustret för att skapa en automatisk avsnittet**: det här alternativet kan MirrorMaker att automatiskt skapa ämnen, men det kan skapa dem med ett annat antal partitioner eller replikering faktor än käll-avsnittet.

        Utför de här stegen för att konfigurera målklustret för att automatiskt skapa avsnitt:

        1. Från den [Azure-portalen](https://portal.azure.com), Välj mål Kafka klustret.
        2. Översikt över kluster Välj __klusterinstrumentpanel__. Välj sedan __instrumentpanelen för HDInsight-klustret__. När du uppmanas du autentisera med inloggningsuppgifterna (admin) för klustret.
        3. Välj den __Kafka__ tjänsten i listan till vänster på sidan.
        4. Välj __konfigurationerna__ i mitten på sidan.
        5. I den __Filter__ , ange ett värde för `auto.create`. Detta filtrerar listan över egenskaper och visar den `auto.create.topics.enable` inställningen.
        6. Ändra värdet för `auto.create.topics.enable` till true, och välj sedan __spara__. Lägga till en anteckning och välj sedan __spara__ igen.
        7. Välj den __Kafka__ tjänsten, Välj __starta om__, och välj sedan __starta om alla berörda__. När du uppmanas, Välj __bekräfta starta om alla__.

## <a name="start-mirrormaker"></a>Starta MirrorMaker

1. Från SSH-anslutning till den **mål** bör du använda följande kommando för att starta processen MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    De parametrar som används i det här exemplet är:

    * **--consumer.config**: Anger den fil som innehåller konsumenten egenskaper. De här egenskaperna används för att skapa en konsument läser från den *källa* Kafka klustret.

    * **--producer.config**: Anger den fil som innehåller producenten egenskaper. De här egenskaperna används för att skapa en producent som skriver till den *mål* Kafka klustret.

    * **--godkända**: en lista över ämnen som MirrorMaker replikeras från källklustret till målet.

    * **--num.streams**: antalet trådar som konsumenten att skapa.

 Vid start returnerar MirrorMaker information som är liknar följande:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Från SSH-anslutning till den **källa** bör du använda följande kommando för att starta en producent och skicka meddelanden till avsnittet:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Ersätt `$CLUSTERNAME` med namnet på källklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

     När du kommer till en tom rad med en markör Skriv i några textmeddelanden. Meddelandet har skickats till ämnet den **källa** klustret. När du är klar kan du använda **Ctrl + C** att avsluta processen producenten.

3. Från SSH-anslutning till den **mål** klustret använder **Ctrl + C** att avsluta processen MirrorMaker. Det kan ta flera sekunder att avsluta processen. Om du vill kontrollera att meddelandena som har replikerats till målet, använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Ersätt `$CLUSTERNAME` med namnet på målklustret. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

    I listan med avsnitt innehåller nu `testtopic`, som skapas när MirrorMaster speglar avsnittet från källklustret till målet. Meddelanden som hämtas från avsnittet är samma som har angetts på källklustret.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Eftersom stegen i det här dokumentet skapa båda klustren i samma Azure resursgrupp måste du ta bort resursgruppen i Azure-portalen. Ta bort resursgruppen tar bort alla resurser som har skapats genom att följa det här dokumentet, Azure Virtual Network och storage-konto som används av kluster.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet du har lärt dig hur du använder MirrorMaker för att skapa en replik av en Kafka-klustret. Använd följande länkar för att identifiera andra sätt att arbeta med Kafka:

* [Apache Kafka MirrorMaker dokumentationen](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) på cwiki.apache.org.
* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)
* [Använda Apache Spark med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ansluta till Kafka via ett trådlöst Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
