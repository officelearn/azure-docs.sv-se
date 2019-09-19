---
title: Spegla Apache Kafka ämnen – Azure HDInsight
description: Lär dig hur du använder Apache Kafkas speglings funktion för att underhålla en replik av en Kafka på HDInsight-kluster genom att spegla ämnen till ett sekundärt kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: 270bc5401e58f4e5c99cae3c5ab06b4f03ae9543
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123245"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Använda MirrorMaker för att replikera Apache Kafka ämnen med Kafka på HDInsight

Lär dig hur du använder Apache Kafkas speglings funktion för att replikera ämnen till ett sekundärt kluster. Spegling kan köras som en kontinuerlig process eller användas tillfälligt som en metod för att migrera data från ett kluster till ett annat.

I det här exemplet används spegling för att replikera ämnen mellan två HDInsight-kluster. Båda klustren finns i olika virtuella nätverk i olika data Center.

> [!WARNING]  
> Spegling bör inte betraktas som ett sätt att uppnå fel tolerans. Förskjutningen till objekt i ett ämne skiljer sig åt mellan de primära och sekundära klustren, så att klienterna inte kan använda den två utbytbara informationen.
>
> Om du är orolig över fel toleransen bör du ställa in replikering för ämnena i klustret. Mer information finns i [Kom igång med Apache Kafka i HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Så här fungerar Apache Kafka spegling

Spegling fungerar med hjälp av [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) -verktyget (en del av Apache Kafka) för att använda poster från ämnen i det primära klustret och sedan skapa en lokal kopia på det sekundära klustret. MirrorMaker använder en (eller flera) *konsumenter* som läser från det primära klustret och en *tillverkare* som skriver till det lokala (sekundära) klustret.

Den mest användbara speglings inställningen för haveri beredskap använder Kafka-kluster i olika Azure-regioner. För att uppnå detta är de virtuella nätverken där klustren finns, peer-kopplas tillsammans.

Följande diagram illustrerar speglings processen och hur kommunikationen flödar mellan kluster:

![Diagram över speglings processen](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

De primära och sekundära klustren kan vara olika i antalet noder och partitioner, och förskjutningarna i ämnena skiljer sig också. Spegling upprätthåller det nyckel värde som används för partitionering, så post ordningen bevaras per nyckel.

### <a name="mirroring-across-network-boundaries"></a>Spegling över nätverks gränser

Om du behöver spegla mellan Kafka-kluster i olika nätverk finns det följande ytterligare överväganden:

* **Gatewayer**: Nätverken måste kunna kommunicera på TCP/IP-nivån.

* **Server adressering**: Du kan välja att adressera klusternoderna med deras IP-adresser eller fullständiga domän namn.

    * **IP-adresser**: Om du konfigurerar Kafka-kluster att använda annonsering av IP-adresser kan du fortsätta med speglings installationen med hjälp av IP-adresserna för Broker-noderna och Zookeeper-noderna.
    
    * **Domän namn**: Om du inte konfigurerar dina Kafka-kluster för annonsering av IP-adresser måste klustren kunna ansluta till varandra med hjälp av fullständigt kvalificerade domän namn (FQDN). Detta kräver en Domain Name System-Server (DNS) i varje nätverk som har kon figurer ATS för att vidarebefordra begär anden till andra nätverk. När du skapar en Azure-Virtual Network, i stället för att använda den automatiska DNS som medföljer nätverket, måste du ange en anpassad DNS-server och IP-adressen för servern. När Virtual Network har skapats måste du skapa en virtuell Azure-dator som använder den IP-adressen och sedan installera och konfigurera DNS-programvaran på den.

    > [!WARNING]  
    > Skapa och konfigurera den anpassade DNS-servern innan du installerar HDInsight i Virtual Network. Det krävs ingen ytterligare konfiguration för att HDInsight ska kunna använda den DNS-server som kon figurer ATS för Virtual Network.

Mer information om hur du ansluter två virtuella Azure-nätverk finns i [Konfigurera en VNet-till-VNET-anslutning](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Speglings arkitektur

Den här arkitekturen innehåller två kluster i olika resurs grupper och virtuella nätverk: en **primär** och en **sekundär**.

### <a name="creation-steps"></a>Skapande steg

1. Skapa två nya resurs grupper:

    |Resursgrupp | Location |
    |---|---|
    | kafka-primary-rg | Centrala USA |
    | kafka-secondary-rg | Norra centrala USA |

1. Skapa ett nytt virtuellt nätverk **Kafka-Primary-VNet** i **Kafka-Primary-RG**. Låt standardinställningarna vara kvar.
1. Skapa ett nytt virtuellt nätverk **Kafka-sekundärt VNet** i **Kafka-Secondary-RG**, även med standardinställningar.

1. Skapa två nya Kafka-kluster:

    | Klusternamn | Resursgrupp | Virtuellt nätverk | Lagringskonto |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Skapa peer-kopplingar för virtuella nätverk. Det här steget skapar två peer-kopplingar: en från **Kafka-Primary-VNet** till **Kafka-Secondary-VNet** och en tillbaka från **Kafka-Secondary-VNet** till **Kafka-Primary-VNet**.
    1. Välj det virtuella nätverket **Kafka-VNet** .
    1. Klicka på **peer** -kopplingar under **Inställningar**.
    1. Klicka på **Lägg till**.
    1. På sidan **Lägg till peering (Lägg till peering** ) anger du informationen som visas på skärm bilden nedan.

        ![HDInsight-Kafka Lägg till VNet-peering](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

1. Konfigurera IP-annonsering:
    1. Gå till Ambari-instrumentpanelen för det primära klustret `https://PRIMARYCLUSTERNAME.azurehdinsight.net`:.
    1. Klicka på **Services** > **Kafka**. Klicka på fliken **konfigurationer** .
    1. Lägg till följande konfigurations rader i det nedre **Kafka-avsnittet mall** . Klicka på **Spara**.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Skriv en anteckning på sidan **Spara konfiguration** och klicka på **Spara**.
    1. Om du uppmanas att ange en konfigurations varning klickar du på **Fortsätt ändå**.
    1. Klicka på **OK** i **Spara konfigurations ändringarna**.
    1. Klicka på **Starta** > om omstart**allt påverkat** i meddelandet **omstart krävs** . Klicka på **Bekräfta omstart av alla**.

        ![Apache Ambari-omstart alla påverkade](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Konfigurera Kafka för att lyssna på alla nätverks gränssnitt.
    1. Stanna kvar på fliken **configs** under **Services** > **Kafka**. I **Kafka Broker** -avsnittet anger du egenskapen **Listeners** till `PLAINTEXT://0.0.0.0:9092`.
    1. Klicka på **Spara**.
    1. Klicka på **starta om**och **Bekräfta omstart av alla**.

1. Post Broker IP-adresser och Zookeeper-adresser för det primära klustret.
    1. Klicka på **värdar** på Ambari-instrumentpanelen.
    1. Anteckna IP-adresserna för utjämnare och Zookeepers. Service Broker-noderna har dubbelt **så många** de två första bokstäverna i värd namnet och Zookeeper-noderna har **ZK** som de första två bokstäverna i värd namnet.

        ![Apache Ambari View Node IP-adresser](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Upprepa föregående tre steg för det andra klustret **Kafka – sekundärt kluster**: Konfigurera IP-annonsering, ange lyssnare och anteckna IP-adresserna för Broker och Zookeeper.

## <a name="create-topics"></a>Skapa ämnen

1. Anslut till det **primära** klustret med hjälp av SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med SSH-användarnamnet som används när du skapar klustret. Ersätt **BASENAME** med bas namnet som används när du skapar klustret.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommando för att skapa en variabel med Apache Zookeeper-värdar för det primära klustret. Strängarna som `ZOOKEEPER_IP_ADDRESS1` måste ersättas med de faktiska IP-adresser som registrerats tidigare, `10.23.0.11` till `10.23.0.7`exempel och. Om du använder FQDN-matchning med en anpassad DNS-Server följer du [dessa steg](apache-kafka-get-started.md#getkafkainfo) för att hämta Broker-och Zookeeper-namn.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Använd följande kommando för att `testtopic`skapa ett ämne med namnet:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. Använd följande kommando för att kontrol lera att ämnet har skapats:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Svaret innehåller `testtopic`.

4. Använd följande för att Visa Zookeeper-värd information för det här (det **primära**) klustret:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Detta returnerar information som liknar följande text:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Spara den här informationen. Den används i nästa avsnitt.

## <a name="configure-mirroring"></a>Konfigurera spegling

1. Ansluta till det **sekundära** klustret med en annan SSH-session:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med SSH-användarnamnet som används när du skapar klustret. Ersätt **SECONDARYCLUSTER** med det namn som användes när du skapade klustret.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. En `consumer.properties` fil används för att konfigurera kommunikation med det **primära** klustret. Använd följande kommando för att skapa filen:

    ```bash
    nano consumer.properties
    ```

    Använd följande text som `consumer.properties` filens innehåll:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Ersätt **PRIMARY_ZKHOSTS** med IP-adresserna för Zookeeper från det **primära** klustret.

    Den här filen beskriver den konsument information som ska användas vid läsning från det primära Kafka-klustret. Mer information konsument konfiguration finns i [konsument konfigurationerna](https://kafka.apache.org/documentation#consumerconfigs) på Kafka.apache.org.

    Om du vill spara filen använder du **CTRL + X**, **Y**och **anger**sedan.

3. Innan du konfigurerar producenten som kommunicerar med det sekundära klustret, måste du konfigurera en variabel för Service Broker-IP-adresserna för det **sekundära** klustret. Använd följande kommandon för att skapa den här variabeln:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Kommandot `echo $SECONDARY_BROKERHOSTS` bör returnera information som liknar följande text:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. En `producer.properties` fil används för att kommunicera det **sekundära** klustret. Använd följande kommando för att skapa filen:

    ```bash
    nano producer.properties
    ```

    Använd följande text som `producer.properties` filens innehåll:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Ersätt **SECONDARY_BROKERHOSTS** med IP-adresserna för Service Broker som användes i föregående steg.

    Mer information om Producer-konfiguration finns i [Producer](https://kafka.apache.org/documentation#producerconfigs) -konfigurationer på Kafka.apache.org.

5. Använd följande kommandon för att skapa en miljö variabel med IP-adresserna för Zookeeper-värdarna för det sekundära klustret:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. Standard konfigurationen för Kafka i HDInsight tillåter inte att ämnen skapas automatiskt. Du måste använda något av följande alternativ innan du startar speglings processen:

    * **Skapa ämnena i det sekundära klustret**: Med det här alternativet kan du också ange antalet partitioner och replikeringslänken.

        Du kan skapa ämnen i förväg genom att använda följande kommando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Ersätt `testtopic` med namnet på det ämne som ska skapas.

    * **Konfigurera klustret för att skapa automatiskt ämne**: Med det här alternativet kan MirrorMaker automatiskt skapa ämnen, men det kan skapa ett annat antal partitioner eller en replikeringsrelation än det primära ämnet.

        Gör så här för att konfigurera det sekundära klustret att automatiskt skapa ämnen:

        1. Gå till Ambari-instrumentpanelen för det sekundära klustret `https://SECONDARYCLUSTERNAME.azurehdinsight.net`:.
        1. Klicka på **Services** > **Kafka**. Klicka på fliken **konfigurationer** .
        5. Ange ett värde `auto.create`i fältet filter. Detta filtrerar listan över egenskaper och visar `auto.create.topics.enable` inställningen.
        6. Ändra värdet `auto.create.topics.enable` till sant och välj sedan __Spara__. Lägg till en anteckning och välj sedan __Spara__ igen.
        7. Välj __Kafka__ -tjänsten, Välj __starta om__och välj sedan __starta om alla berörda__. När du uppmanas väljer du __Bekräfta omstart av alla__.

        ![Kafka aktivera automatisk skapande av ämnen](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Starta MirrorMaker

1. Från SSH-anslutningen till det **sekundära** klustret använder du följande kommando för att starta MirrorMaker-processen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    De parametrar som används i det här exemplet är:

    * **--consumer.config**: Anger den fil som innehåller konsument egenskaper. Dessa egenskaper används för att skapa en konsument som läser från det *primära* Kafka-klustret.

    * **--producer.config**: Anger den fil som innehåller producent egenskaper. Dessa egenskaper används för att skapa en producent som skriver till det *sekundära* Kafka-klustret.

    * **--vitlista**: En lista med avsnitt som MirrorMaker replikerar från det primära klustret till den sekundära.

    * **--antal. strömmar**: Antalet konsument trådar som ska skapas.

    Konsumenten på den sekundära noden väntar nu på att ta emot meddelanden.

2. Från SSH-anslutningen till det **primära** klustret använder du följande kommando för att starta en producent och skicka meddelanden till ämnet:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     När du kommer till en tom rad med en markör skriver du några text meddelanden. Meddelandena skickas till avsnittet i det **primära** klustret. När du är klar använder du **CTRL + C** för att avsluta producent processen.

3. Från SSH-anslutningen till det **sekundära** klustret använder du **CTRL + C** för att avsluta MirrorMaker-processen. Det kan ta flera sekunder att avsluta processen. Använd följande kommando för att kontrol lera att meddelandena har repliker ATS till den sekundära:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Listan med ämnen innehåller `testtopic`nu, som skapas när MirrorMaster speglar ämnet från det primära klustret till den sekundära. Meddelanden som hämtas från avsnittet är desamma som de som du angav i det primära klustret.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Stegen i det här dokumentet skapade kluster i olika Azure-resurs grupper. Om du vill ta bort alla resurser som har skapats kan du ta bort de två resurs grupper som skapats: **Kafka-Primary-RG** och **Kafka-secondary_rg**. Om du tar bort resurs grupperna tas alla resurser som skapats i följande dokument bort, inklusive kluster, virtuella nätverk och lagrings konton.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att skapa en replik av ett [Apache Kafka](https://kafka.apache.org/) -kluster. Använd följande länkar för att identifiera andra sätt att arbeta med Kafka:

* [Apache Kafka MirrorMaker-dokumentation](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) på cwiki.apache.org.
* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)
* [Använda Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Ansluta till Apache Kafka via en Azure-Virtual Network](apache-kafka-connect-vpn-gateway.md)
