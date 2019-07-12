---
title: Spegling av Apache Kafka-avsnitt – Azure HDInsight
description: Lär dig hur du använder Apache Kafka databasspegling funktionen för att underhålla en replik av en Kafka på HDInsight-klustret genom att spegla ämnen till ett sekundärt kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657182"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Använd MirrorMaker för att replikera Apache Kafka-ämnen med Kafka på HDInsight

Lär dig hur du använder Apache Kafka databasspegling funktionen för att replikera ämnen till ett sekundärt kluster. Spegling kan kördes som en kontinuerlig process eller används ibland som en metod för att migrera data från ett kluster till en annan.

I det här exemplet används spegling för att replikera ämnen mellan två kluster med HDInsight. Båda klustren finns i olika virtuella nätverk i olika datacenter.

> [!WARNING]  
> Spegling ska inte betraktas som ett sätt att uppnå feltolerans. Förskjutningen till objekt i ett ämne skiljer sig mellan de primära och sekundära kluster, så att klienter inte kan använda två synonymt.
>
> Om du är orolig feltolerans, anger du replikering för ämnena i klustret. Mer information finns i [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Så här fungerar Apache Kafka spegling

Spegling fungerar med hjälp av den [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) verktyget (del av Apache Kafka) att använda poster ämnen på det primära klustret och sedan skapa en lokal kopia på det sekundära klustret. MirrorMaker använder en (eller flera) *konsumenter* som läser från primära klustret, och en *producent* som skriver till det lokala klustret för (sekundär).

Den mest användbara databasspegling installationsprogrammet för disaster recovery använder Kafka-kluster i olika Azure-regioner. För att uppnå detta virtuella nätverk där klustren finns är peer-kopplade tillsammans.

Följande diagram illustrerar processen för databasspegling och hur kommunikation flödar mellan kluster:

![Diagram över databasspegling processen](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

De primära och sekundära kluster kan vara annorlunda i antalet noder och partitioner och förskjutningar i avsnittet skiljer sig också. Spegling underhåller nyckelvärdet som används för partitionering, så att posten ordning bevaras på basis av per nyckel.

### <a name="mirroring-across-network-boundaries"></a>Spegling över nätverksgränser

Om du vill spegla mellan Kafka-kluster i olika nätverk, finns det ytterligare aspekter:

* **Gatewayer**: Nätverk måste kunna kommunicera på TCP/IP-nivå.

* **Server-adressering**: Du kan välja att åtgärda klusternoderna med hjälp av deras IP-adresser eller fullständiga domännamn.

    * **IP-adresser**: Om du konfigurerar dina Kafka-kluster för att använda IP-adress reklam, kan du fortsätta med databasspegling installationen med hjälp av IP-adresserna för meddelandenoder och zookeeper-noder.
    
    * **Domännamn**: Om du inte konfigurerar dina Kafka-kluster i IP-adress reklamsyfte måste klustren kunna ansluta till varandra med hjälp av fullständigt kvalificerat domännamn (FQDN). Detta kräver en Domain Name System (DNS)-server i varje nätverk som är konfigurerad för att vidarebefordra begäranden till andra nätverk. När du skapar ett virtuellt nätverk i Azure, istället för att använda automatisk DNS medföljer i nätverket, måste du ange en anpassad DNS-server och IP-adressen för servern. När du har skapat det virtuella nätverket, måste du sedan skapa en Azure-dator som använder den IP-adressen, och sedan installera och konfigurera DNS-programvara på den.

    > [!WARNING]  
    > Skapa och konfigurera anpassade DNS-servern innan du installerar HDInsight till det virtuella nätverket. Det finns ingen ytterligare konfiguration krävs för HDInsight att använda DNS-server som konfigurerats för det virtuella nätverket.

Mer information om hur du ansluter två virtuella Azure-nätverk finns i [konfigurera en VNet-till-VNet-anslutning](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Spegling arkitektur

Den här arkitekturen har två kluster i olika resursgrupper och virtuella nätverk: en **primära** och **sekundära**.

### <a name="creation-steps"></a>Separata steg

1. Skapa två nya resursgrupper:

    |Resursgrupp | Location |
    |---|---|
    | kafka-primary-rg | Centrala USA |
    | kafka-secondary-rg | Norra centrala USA |

1. Skapa ett nytt virtuellt nätverk **kafka-primary-vnet** i **kafka-primary-rg**. Lämna standardinställningarna.
1. Skapa ett nytt virtuellt nätverk **kafka-sekundär-vnet** i **kafka-sekundär-rg**, även med standardinställningar.

1. Skapa två nya Kafka-kluster:

    | Klusternamn | Resursgrupp | Virtuellt nätverk | Lagringskonto |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Skapa virtuella nätverk. Det här steget skapar två peerings: en från **kafka-primary-vnet** till **kafka-sekundär-vnet** och en tillbaka från **kafka-sekundär-vnet** till  **kafka-primary-vnet**.
    1. Välj den **kafka-primary-vnet** virtuellt nätverk.
    1. Klicka på **Peerings** under **inställningar**.
    1. Klicka på **Lägg till**.
    1. På den **lägga till peering** skärmen, anger du informationen som visas i skärmbilden nedan.

        ![Lägg till vnet-peering](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. Konfigurera IP-annonsering:
    1. Gå till Ambari-instrumentpanelen för primära klustret: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. Klicka på **Services** > **Kafka**. Klicka på den **Peeringkonfigurationer** fliken.
    1. Lägg till följande rader i config längst ned **kafka-env mallen** avsnittet. Klicka på **Spara**.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Ange en kommentar den **Spara konfiguration** skärmen och klicka på **spara**.
    1. Om du får en uppmaning med Konfigurationsvarning klickar du på **fortsätter ändå**.
    1. Klicka på **Ok** på den **spara konfigurationsändringar**.
    1. Klicka på **starta om** > **starta om alla påverkade** i den **omstart krävs** meddelande. Klicka på **bekräfta omstart alla**.

        ![Starta om kafka-noder](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Konfigurera Kafka för att lyssna på alla nätverksgränssnitt.
    1. Stanna kvar på den **Peeringkonfigurationer** fliken **Services** > **Kafka**. I den **Kafka-Meddelandeköer** avsnittet set den **lyssnare** egenskap `PLAINTEXT://0.0.0.0:9092`.
    1. Klicka på **Spara**.
    1. Klicka på **starta om**, och **bekräfta omstart alla**.

1. Registrera Broker och Zookeeper-adresser för primära kluster.
    1. Klicka på **värdar** på Ambari-instrumentpanelen.
    1. Anteckna IP-adresser för asynkrona meddelandeköer och Zookeeper. Broker noder har **flytta** som de två första bokstäverna i värdnamnet och zookeeper-noder har **zk** som de två första bokstäverna i värdnamnet.

        ![Visa ip-adresser](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Upprepa föregående tre steg för det andra klustret **kafka-sekundär-kluster**: konfigurera IP-reklam, ange lyssnare och anteckna Broker och Zookeeper-IP-adresser.

## <a name="create-topics"></a>Skapa ämnen

1. Ansluta till den **primära** klustret med hjälp av SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med SSH-användarnamnet som används när klustret skapas. Ersätt **BASENAME** med det grundläggande namnet som används när klustret skapas.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommando för att skapa en variabel med Apache Zookeeper-värdar för primära klustret. Strängarna som `ZOOKEEPER_IP_ADDRESS1` måste ersättas med de faktiska IP-adresser som registreras tidigare, till exempel `10.23.0.11` och `10.23.0.7`. Om du använder FQDN-lösning med en anpassad DNS-server, följer du [här](apache-kafka-get-started.md#getkafkainfo) att hämta broker och zookeeper namnen.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Skapa ett ämne med namnet `testtopic`, använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. Använd följande kommando för att verifiera att ämnet har skapats:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Svaret innehåller `testtopic`.

4. Använd följande för att visa Zookeeper-värdens information för detta (den **primära**) kluster:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Detta returnerar information liknande följande text:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Spara den här informationen. Den används i nästa avsnitt.

## <a name="configure-mirroring"></a>Konfigurera spegling

1. Ansluta till den **sekundära** klustret med hjälp av en annan SSH-session:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Ersätt **sshuser** med SSH-användarnamnet som används när klustret skapas. Ersätt **SECONDARYCLUSTER** med det namn som används när klustret skapas.

    Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. En `consumer.properties` används för att konfigurera kommunikation med den **primära** kluster. Använd följande kommando för att skapa filen:

    ```bash
    nano consumer.properties
    ```

    Använd följande text som innehållet i den `consumer.properties` fil:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Ersätt **PRIMARY_ZKHOSTS** med Zookeeper IP-adresser från den **primära** kluster.

    Den här filen beskriver konsumentinformation för användning vid läsning från det primära Kafka-klustret. Mer information konsument konfiguration finns i [konsument Peeringkonfigurationer](https://kafka.apache.org/documentation#consumerconfigs) på kafka.apache.org.

    Om du vill spara filen, Använd **Ctrl + X**, **Y**, och sedan **RETUR**.

3. Innan du konfigurerar producenten som kommunicerar med sekundära klustret bör du konfigurera en variabel för broker IP-adresserna för de **sekundära** kluster. Använd följande kommandon för att skapa den här variabeln:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Kommandot `echo $SECONDARY_BROKERHOSTS` ska returnera information liknande följande text:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. En `producer.properties` används för att kommunicera i **sekundära** kluster. Använd följande kommando för att skapa filen:

    ```bash
    nano producer.properties
    ```

    Använd följande text som innehållet i den `producer.properties` fil:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Ersätt **SECONDARY_BROKERHOSTS** med broker IP-adresser som används i föregående steg.

    Mer information producent konfiguration finns i [producent Peeringkonfigurationer](https://kafka.apache.org/documentation#producerconfigs) på kafka.apache.org.

5. Använd följande kommandon för att skapa en miljövariabel med Zookeeper-värdar för sekundära klustret IP-adresser:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. Standardkonfigurationen för Kafka på HDInsight tillåter inte automatisk generering av ämnen. Du måste använda en av följande alternativ innan du börjar spegling:

    * **Skapa ämnena i det sekundära klustret**: Det här alternativet kan du ange hur många partitioner och replikeringsfaktorn.

        Du kan skapa ämnen förbereds i förväg med hjälp av följande kommando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Ersätt `testtopic` med namnet på ämnet för att skapa.

    * **Konfigurera klustret för att skapa automatiska ämne**: Det här alternativet kan MirrorMaker att automatiskt skapa ämnen, men det kan skapa dem med ett annat antal partitioner eller replikeringsfaktor än det primära avsnittet.

        Utför de här stegen för att konfigurera sekundära klustret för att automatiskt skapa ämnen:

        1. Gå till Ambari-instrumentpanelen för sekundära klustret: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Klicka på **Services** > **Kafka**. Klicka på den **Peeringkonfigurationer** fliken.
        5. I den __Filter__ fältet, anger du värdet `auto.create`. Detta filtrerar listan över egenskaper och visar den `auto.create.topics.enable` inställningen.
        6. Ändra värdet för `auto.create.topics.enable` true och välj sedan __spara__. Lägga till en anteckning och välj sedan __spara__ igen.
        7. Välj den __Kafka__ tjänsten, väljer __starta om__, och välj sedan __starta om alla berörda__. När du uppmanas, väljer __bekräfta starta om alla__.

        ![Konfigurera automatiskt skapa-ämne](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Starta MirrorMaker

1. Från SSH-anslutningen till den **sekundära** kluster, Använd följande kommando för att starta MirrorMaker-processen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    De parametrar som används i det här exemplet är:

    * **--consumer.config**: Anger den fil som innehåller egenskaper för konsument. Dessa egenskaper används för att skapa en konsument läser från den *primära* Kafka-kluster.

    * **--producer.config**: Anger den fil som innehåller egenskaper för producenten. Dessa egenskaper används för att skapa en producent som skriver till den *sekundära* Kafka-kluster.

    * **--whitelist**: En lista över ämnen som MirrorMaker som replikerar från det primära klustret till sekundärt.

    * **--num.streams**: Antal trådar som konsument ska skapa.

    Konsumenten på den sekundära noden väntar nu på att ta emot meddelanden.

2. Från SSH-anslutningen till den **primära** kluster, Använd följande kommando för att starta en producent och skicka meddelanden till ämnet:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     När du kommer till en tom rad med en markör ange några textmeddelanden. Meddelandet har skickats till ämnet den **primära** kluster. När du är klar kan du använda **Ctrl + C** att avsluta processen producenten.

3. Från SSH-anslutningen till den **sekundära** kluster, använda **Ctrl + C** att avsluta processen MirrorMaker. Det kan ta flera sekunder att avsluta processen. Om du vill kontrollera att meddelanden har replikerats till sekundärt, använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Listan med avsnitt innehåller nu `testtopic`, som skapas när MirrorMaster speglar ämne från det primära klustret till sekundärt. Meddelanden som har hämtats från ämnet är desamma som de som du angav på det primära klustret.

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Stegen i det här dokumentet Skapa kluster i olika Azure-resursgrupper. Om du vill ta bort alla resurser som har skapats, kan du ta bort de två resursgrupperna som skapats: **kafka-primary-rg** och **kafka secondary_rg**. Tar bort resursgrupperna tar bort alla resurser som har skapats genom att följa det här dokumentet, inklusive kluster, virtuella nätverk och lagringskonton.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) att skapa en replik av en [Apache Kafka](https://kafka.apache.org/) kluster. Använd följande länkar för att upptäcka andra sätt att arbeta med Kafka:

* [Dokumentation om Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) på cwiki.apache.org.
* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)
* [Använda Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Anslut till Apache Kafka via ett Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
