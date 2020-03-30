---
title: Spegla Apache Kafka-ämnen - Azure HDInsight
description: Lär dig hur du använder Apache Kafkas speglingsfunktion för att underhålla en replik av en Kafka på HDInsight-kluster genom att spegla ämnen till ett sekundärt kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425892"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Använd MirrorMaker för att replikera Apache Kafka-ämnen med Kafka i HDInsight

Lär dig hur du använder Apache Kafkas speglingsfunktion för att replikera ämnen till ett sekundärt kluster. Spegling kan kördes som en kontinuerlig process eller användas periodvis som en metod för att migrera data från ett kluster till ett annat.

I det här exemplet används spegling för att replikera ämnen mellan två HDInsight-kluster. Båda klustren finns i olika virtuella nätverk i olika datacenter.

> [!WARNING]  
> Spegling bör inte betraktas som ett sätt att uppnå feltolerans. Förskjutningen till objekt i ett ämne skiljer sig mellan de primära och sekundära klustren, så klienter kan inte använda de två omväxlande.
>
> Om du är orolig för feltolerans bör du ange replikering för ämnena i klustret. Mer information finns i [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Hur Apache Kafka spegling fungerar

Spegling fungerar med verktyget [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (en del av Apache Kafka) för att använda poster från ämnen i det primära klustret och sedan skapa en lokal kopia i det sekundära klustret. MirrorMaker använder en (eller flera) *konsumenter* som läser från det primära klustret och en *producent* som skriver till det lokala (sekundära) klustret.

Den mest användbara speglingsinställningen för haveriberedskap använder Kafka-kluster i olika Azure-regioner. För att uppnå detta är de virtuella nätverk där klustren finns peered tillsammans.

Följande diagram illustrerar speglingsprocessen och hur kommunikationen flödar mellan kluster:

![Diagram över speglingsprocessen](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

De primära och sekundära klustren kan vara olika i antalet noder och partitioner, och förskjutningar inom ämnena är olika också. Spegling upprätthåller nyckelvärdet som används för partitionering, så postordningen bevaras per nyckel.

### <a name="mirroring-across-network-boundaries"></a>Spegling över nätverksgränser

Om du behöver spegla mellan Kafka-kluster i olika nätverk finns det följande ytterligare överväganden:

* **Gateways**: Nätverken måste kunna kommunicera på TCP/IP-nivå.

* **Serveradressering:** Du kan välja att adressera klusternoderna med hjälp av deras IP-adresser eller fullständigt kvalificerade domännamn.

    * **IP-adresser:** Om du konfigurerar kafka-kluster för att använda IP-adressannonsering kan du fortsätta med speglingsinställningen med hjälp av IP-adresserna för mäklarenoderna och zookeepernoderna.
    
    * **Domännamn:** Om du inte konfigurerar Kafka-kluster för IP-adressannonsering måste klustret kunna ansluta till varandra med hjälp av fullständigt kvalificerade domännamn (FQDN). Detta kräver en DNS-server (Domain Name System) i varje nätverk som är konfigurerat för att vidarebefordra begäranden till andra nätverk. När du skapar ett virtuellt Azure-nätverk måste du ange en anpassad DNS-server och IP-adressen för servern i stället för att använda den automatiska DNS som medföljer nätverket. När det virtuella nätverket har skapats måste du sedan skapa en Virtuell Azure-dator som använder ip-adressen och sedan installera och konfigurera DNS-programvara på den.

    > [!WARNING]  
    > Skapa och konfigurera den anpassade DNS-servern innan du installerar HDInsight i det virtuella nätverket. Det krävs ingen ytterligare konfiguration för att HDInsight ska kunna använda DEN DNS-server som konfigurerats för det virtuella nätverket.

Mer information om hur du ansluter två Virtuella Azure-nätverk finns i [Konfigurera en VNet-till-VNet-anslutning](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Speglingsarkitektur

Den här arkitekturen har två kluster i olika resursgrupper och virtuella nätverk: en **primär** och **sekundär**.

### <a name="creation-steps"></a>Skapa steg

1. Skapa två nya resursgrupper:

    |Resursgrupp | Location |
    |---|---|
    | kafka-primär-rg | USA, centrala |
    | kafka-sekundär-rg | USA, norra centrala |

1. Skapa ett nytt virtuellt nätverk **kafka-primary-vnet** i **kafka-primary-rg**. Lämna standardinställningarna.
1. Skapa ett nytt virtuellt nätverk **kafka-secondary-vnet** i **kafka-secondary-rg**, även med standardinställningar.

1. Skapa två nya Kafka-kluster:

    | Klusternamn | Resursgrupp | Virtual Network | Lagringskonto |
    |---|---|---|---|
    | kafka-primär-kluster | kafka-primär-rg | kafka-primär-vnet | kafkaprimarystorage |
    | kafka-sekundär-kluster | kafka-sekundär-rg | kafka-sekundär-vnet | kafkasecondarystorage |

1. Skapa virtuella nätverks peerings. Detta steg kommer att skapa två peerings: en från **kafka-primär-vnet** till **kafka-secondary-vnet** och en tillbaka från **kafka-secondary-vnet** till **kafka-primary-vnet**.
    1. Välj det virtuella nätverket **kafka-primary-vnet.**
    1. Välj **Peerings** under **Inställningar**.
    1. Välj **Lägg till**.
    1. På skärmen **Lägg till peering** anger du detaljerna som visas på skärmbilden nedan.

        ![HDInsight Kafka lägga till vnet peering](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Konfigurera IP-annonsering

Konfigurera IP-annonsering så att en klient kan ansluta med hjälp av mäklar-IP-adresser i stället för domännamn.

1. Gå till Ambari-instrumentpanelen `https://PRIMARYCLUSTERNAME.azurehdinsight.net`för det primära klustret: .
1. Välj **Tjänster** > **Kafka**. CliSelectck fliken **Configs.**
1. Lägg till följande config-rader i det nedre **mallavsnittet för kafka-env.** Välj **Spara**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Ange en anteckning på skärmen **Spara konfiguration** och klicka på **Spara**.
1. Om du uppmanas att ange konfigurationsvarning klickar du på **Fortsätt ändå**.
1. Välj **Ok** på **spara konfigurationsändringar**.
1. Välj **Starta om** > **starta om alla som påverkas** i meddelandet **Omstart.** Välj **Bekräfta starta om alla**.

    ![Apache Ambari starta om alla drabbade](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Konfigurera Kafka för att lyssna på alla nätverksgränssnitt.
    
1. Stanna på **fliken Configs** under **Tjänster** > **Kafka**. I avsnittet **Kafka Broker** ställs **lyssnare** åt `PLAINTEXT://0.0.0.0:9092`.
1. Välj **Spara**.
1. Välj **Starta om**och Bekräfta Starta om **alla**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Registrera mäklar-IP-adresser och Zookeeper-adresser för primära kluster.

1. Välj **Värdar** på Ambari-instrumentpanelen.
1. Anteckna IP-adresserna för mäklare och zookeepers. Mäklarenoderna har **wn** som de två första bokstäverna i värdnamnet, och zookeeper noder har **zk** som de två första bokstäverna i värdnamnet.

    ![Apache Ambari visa nod IP-adresser](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Upprepa de föregående tre stegen för det andra klustret **kafka-sekundär-kluster:** konfigurera IP-annonsering, ange lyssnare och anteckna mäklar- och Zookeeper IP-adresser.

## <a name="create-topics"></a>Skapa ämnen

1. Anslut till det **primära** klustret med SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med det SSH-användarnamn som används när klustret skapas. Ersätt **PRIMARYCLUSTER** med det basnamn som används när klustret skapas.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

1. Använd följande kommando för att skapa en variabel med Apache Zookeeper värdar för det primära klustret. Strängarna som `ZOOKEEPER_IP_ADDRESS1` måste ersättas med de faktiska IP-adresser som registrerats tidigare, till exempel `10.23.0.11` och `10.23.0.7`. Om du använder FQDN-upplösning med en anpassad DNS-server följer du [dessa steg](apache-kafka-get-started.md#getkafkainfo) för att få namn på mäklare och djurhållare.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Om du vill `testtopic`skapa ett ämne med namnet använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Använd följande kommando för att kontrollera att ämnet har skapats:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Svaret innehåller `testtopic`.

1. Använd följande om du vill visa värdinformationen för Zookeeper för det här (det **primära)** klustret:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Detta returnerar information som liknar följande text:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Spara den här informationen. Det används i nästa avsnitt.

## <a name="configure-mirroring"></a>Konfigurera spegling

1. Anslut till det **sekundära** klustret med en annan SSH-session:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med det SSH-användarnamn som används när klustret skapas. Ersätt **SECONDARYCLUSTER** med det namn som används när klustret skapas.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

1. En `consumer.properties` fil används för att konfigurera kommunikation med det **primära** klustret. Så här skapar du filen:

    ```bash
    nano consumer.properties
    ```

    Använd följande text som innehållet `consumer.properties` i filen:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Ersätt **PRIMARY_ZKHOSTS** med Zookeeper IP-adresser från det **primära** klustret.

    Den här filen beskriver konsumentinformation som ska användas när du läser från det primära Kafka-klustret. Mer information om konsumentkonfiguration finns i [Konsumentkonfiguration på](https://kafka.apache.org/documentation#consumerconfigs) kafka.apache.org.

    Om du vill spara filen använder du **Ctrl + X**, **Y**och sedan **enter**.

1. Innan du konfigurerar producenten som kommunicerar med det sekundära klustret ställer du in en variabel för mäklar-IP-adresserna för det **sekundära** klustret. Använd följande kommandon för att skapa den här variabeln:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Kommandot `echo $SECONDARY_BROKERHOSTS` bör returnera information som liknar följande text:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. En `producer.properties` fil används för att kommunicera det **sekundära** klustret. Så här skapar du filen:

    ```bash
    nano producer.properties
    ```

    Använd följande text som innehållet `producer.properties` i filen:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Ersätt **SECONDARY_BROKERHOSTS** med de mäklar-IP-adresser som användes i föregående steg.

    Mer information producentkonfiguration finns i [Producent configs](https://kafka.apache.org/documentation#producerconfigs) på kafka.apache.org.

1. Använd följande kommandon för att skapa en miljövariabel med IP-adresserna för Zookeeper-värdarna för det sekundära klustret:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Standardkonfigurationen för Kafka på HDInsight tillåter inte automatiskt skapande av ämnen. Du måste använda något av följande alternativ innan du startar speglingsprocessen:

    * **Skapa ämnena i det sekundära klustret**: Med det här alternativet kan du också ange antalet partitioner och replikeringsfaktorn.

        Du kan skapa ämnen i förväg med följande kommando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Ersätt `testtopic` med namnet på det ämne som ska skapas.

    * **Konfigurera klustret för automatisk skapande**av ämnen: Med det här alternativet kan MirrorMaker automatiskt skapa ämnen, men det kan skapa dem med ett annat antal partitioner eller replikeringsfaktor än det primära ämnet.

        Så här konfigurerar du det sekundära klustret så att de automatiskt skapar ämnen:

        1. Gå till Ambari-instrumentpanelen `https://SECONDARYCLUSTERNAME.azurehdinsight.net`för det sekundära klustret: .
        1. Klicka på **Tjänster** > **Kafka**. Klicka på fliken **Configs.**
        1. Ange värdet __.__ `auto.create` Detta filtrerar listan över `auto.create.topics.enable` egenskaper och visar inställningen.
        1. Ändra värdet `auto.create.topics.enable` för till sant och välj sedan __Spara__. Lägg till en anteckning och välj sedan __Spara__ igen.
        1. Välj __Kafka-tjänsten,__ välj __Starta om__och välj sedan __Starta om alla berörda__. När du uppmanas till det väljer du __Bekräfta omstart av alla__.

        ![kafka aktivera automatiska skapa ämnen](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Starta MirrorMaker

1. Från SSH-anslutningen till det **sekundära** klustret använder du följande kommando för att starta MirrorMaker-processen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    De parametrar som används i det här exemplet är:

    |Parameter |Beskrivning |
    |---|---|
    |--consumer.config|Anger filen som innehåller konsumentegenskaper. Dessa egenskaper används för att skapa en konsument som läser från det *primära* Kafka-klustret.|
    |--producent.config|Anger filen som innehåller producentegenskaper. Dessa egenskaper används för att skapa en producent som skriver till det *sekundära* Kafka-klustret.|
    |--vitlista|En lista med ämnen som MirrorMaker replikerar från det primära klustret till det sekundära.|
    |--num.streams|Antalet konsumenttrådar att skapa.|

    Konsumenten på den sekundära noden väntar nu på att få meddelanden.

2. Från SSH-anslutningen till det **primära** klustret använder du följande kommando för att starta en producent och skicka meddelanden till ämnet:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     När du kommer till en tom rad med en markör skriver du in några sms. Meddelandena skickas till ämnet i det **primära** klustret. När du är klar använder du **Ctrl + C** för att avsluta producentprocessen.

3. Från SSH-anslutningen till det **sekundära** klustret använder du **Ctrl + C** för att avsluta MirrorMaker-processen. Det kan ta flera sekunder att avsluta processen. Om du vill kontrollera att meddelandena har replikerats till den sekundära använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Listan över ämnen `testtopic`innehåller nu , som skapas när MirrorMaster speglar ämnet från det primära klustret till det sekundära. De meddelanden som hämtas från ämnet är desamma som de som du angav i det primära klustret.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Stegen i det här dokumentet skapade kluster i olika Azure-resursgrupper. Om du vill ta bort alla resurser som har skapats kan du ta bort de två resursgrupper som skapats: **kafka-primary-rg** och **kafka-secondary_rg**. Om du tar bort resursgrupperna tas alla resurser som skapas bort genom att följa det här dokumentet, inklusive kluster, virtuella nätverk och lagringskonton.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet lärde du dig hur du använder [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att skapa en replik av ett [Apache Kafka-kluster.](https://kafka.apache.org/) Använd följande länkar för att upptäcka andra sätt att arbeta med Kafka:

* [Apache Kafka MirrorMaker dokumentation](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) på cwiki.apache.org.
* [Kafka Mirror Maker bästa praxis](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)
* [Använd Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Ansluta till Apache Kafka via ett virtuellt Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
