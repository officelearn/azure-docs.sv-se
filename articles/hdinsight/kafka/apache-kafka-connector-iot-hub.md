---
title: Använda Apache Kafka på HDInsight med Azure IoT-hubb | Microsoft Docs
description: Lär dig använda Apache Kafka på HDInsight med Azure IoT-hubben. Kafka ansluta Azure IoT Hub-projektet innehåller en koppling för källa och mottagare för Kafka. Käll-kopplingen kan läsa data från IoT-hubb och sink anslutningen skriver till IoT-hubb.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/15/2018
ms.author: larryfr
ms.openlocfilehash: 33fdb5b099efc40fec94a860b21cda75ced44fe9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Använda Apache Kafka på HDInsight med Azure IoT-hubb

Lär dig hur du använder den [Kafka ansluta Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) connector för att flytta data mellan Apache Kafka i HDInsight och Azure IoT Hub. Lär dig hur du kör IoT-hubb-anslutningen från en kantnod i klustret i det här dokumentet.

Kafka ansluta API kan du implementera kopplingar som kontinuerligt hämta data till Kafka, eller skicka data från Kafka till ett annat system. Den [Kafka ansluta Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) är en koppling som hämtar data från Azure IoT-hubb i Kafka. Det kan också skicka data från Kafka till IoT-hubben. 

När du drar från IoT-hubben, använder du en __källa__ koppling. När push-installation till IoT-hubb, använder du en __sink__ koppling. IoT-hubb-kopplingen innehåller både källa och mottagare kopplingar.

Följande diagram visar dataflödet mellan Azure IoT Hub och Kafka på HDInsight när du använder anslutningen.

![Bild som visar data som flödar från IoT-hubb till Kafka via kopplingen](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Mer information om API ansluta finns [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* En Kafka på HDInsight-kluster. Mer information finns i dokumentet [Snabbstart för Kafka i HDInsight](apache-kafka-get-started.md).

* En kantnod i Kafka-klustret. Mer information finns i [använder edge noder med HDInsight](../hdinsight-apps-use-edge-node.md) dokumentet.

* En Azure IoT-hubb. Den här kursen kan jag rekommenderar den [ansluta hallon Pi online simulator som Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) dokumentet.

* En SSH-klient. Stegen i det här dokumentet använder SSH för att ansluta till klustret. Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Installera connector

1. Hämta den senaste versionen av Kafka ansluta för Azure IoT-hubb från [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Om du vill överföra kantnod av din Kafka på HDInsight-kluster .jar-fil, använder du följande kommando:

    > [!NOTE]
    > Ersätt `sshuser` med SSH-användarkontot för ditt HDInsight-kluster. Ersätt `new-edgenode` med nodnamnet kant. Ersätt `clustername` med klustrets namn. Mer information om SSH-slutpunkten för kantnoden finns i [används edge noder med HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node) dokumentet.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. När filkopieringen är klar att ansluta till kantnoden med SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Så här installerar du anslutning till Kafka `libs` directory, använder du följande kommando:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Om du stöter på problem med resten av stegen i det här dokumentet när du använder en förskapad .jar-fil, försök att skapa paket från källan.
>
> Om du vill skapa kopplingen du har en Scala utvecklingsmiljö med den [Scala skapa verktyget](http://www.scala-sbt.org/).
>
> 1. Ladda ned källan för anslutningstjänsten från [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) i din utvecklingsmiljö.
>
> 2. Använder du följande kommando från en kommandotolk i projektkatalogen, att bygga och paket i projektet:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Det här kommandot skapar en fil med namnet `kafka-connect-iothub-assembly_2.11-0.6.jar` i den `target/scala-2.11` katalogen för projektet.

## <a name="configure-kafka"></a>Konfigurera Kafka

Använd följande steg från en SSH-anslutning till kantnoden för att konfigurera Kafka för att köra anslutningstjänsten i fristående läge:

1. Spara klustrets namn till en variabel. Använda en variabel gör det enklare att kopiera och klistra in kommandona i det här avsnittet:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Installera den `jq` verktyget. Det här verktyget gör det enklare att bearbeta JSON-dokument som returneras från Ambari frågor:

    ```bash
    sudo apt -y install jq
    ```

3. Hämta adressen till Kafka mäklare. Det kan finnas många mäklare i klustret, men du behöver bara att referera till en eller två. Om du vill hämta adressen till två broker värdar, använder du följande kommando:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin). Det värde som genereras liknar följande text:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Hämta adressen till Zookeeper-noder. Det finns flera Zookeeper-noder i klustret, men du behöver bara att referera till en eller två. Använd följande kommando för att hämta adressen till två Zookeeper-noder:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. När du kör kopplingen i fristående läge på `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` används för att kommunicera med Kafka mäklare. Så här redigerar du den `connect-standalone.properties` fil, använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Om du vill konfigurera en fristående konfiguration för kantnod att hitta Kafka mäklare, hitta den rad som börjar med `bootstrap.servers=`. Ersätt den `localhost:9092` värdet med broker-värdar från föregående steg.

    * Ändra den `key.converter=` och `value.converter=` rader till följande värden:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Den här ändringen kan du testa med hjälp av konsolen producenten ingår i Kafka. Du kan behöva olika konverterare för andra producenter och konsumenter. Information om hur du använder andra konverterare värden finns i [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Lägg till en rad i slutet av filen som innehåller följande text:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Den här ändringen är att förhindra tidsgränser för sink-kopplingen genom att begränsa den till 10 poster i taget. Mer information finns på [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Om du vill spara filen, Använd __Ctrl + X__, __Y__, och sedan __RETUR__.

7. Använd följande kommandon för att skapa de information som används av kopplingen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Kontrollera att den `iotin` och `iotout` avsnitt finns, använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Den `iotin` avsnittet används för att ta emot meddelanden från IoT-hubb. Den `iotout` avsnittet används för att skicka meddelanden till IoT-hubb.

## <a name="get-iot-hub-connection-information"></a>Hämta anslutningsinformationen för IoT-hubb

Använd följande steg för att hämta IoT-hubb information som används av anslutningen:

1. Hämta Event Hub-kompatibel slutpunkt och Event Hub-kompatibel namnet på slutpunkten för din IoT-hubb. Använd någon av följande metoder för att få den här informationen:

    * __Från den [Azure-portalen](https://portal.azure.com/)__, Använd följande steg:

        1. Navigera till din IoT-hubb och markera __slutpunkter__.
        2. Från __inbyggda slutpunkter__väljer __händelser__.
        3. Från __egenskaper__, Kopiera värdet för följande fält:

            * __Hubb-kompatibel händelsenamnet__
            * __Event Hub-kompatibel slutpunkt__
            * __Partitioner__

        > [!IMPORTANT]
        > Slutpunktsvärde från portalen kan innehålla extra text som inte behövs i det här exemplet. Extrahera text som matchar det här mönstret `sb://<randomnamespace>.servicebus.windows.net/`.

    * __Från den [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, använder du följande kommando:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Ersätt `myhubname` med namnet på din IoT-hubb. Svaret liknar följande:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Hämta den __delad åtkomstprincip__ och __nyckeln__. Det här exemplet använder den __service__ nyckel. Använd någon av följande metoder för att få den här informationen:

    * __Från den [Azure-portalen](https://portal.azure.com/)__, Använd följande steg:

        1. Välj __principer för delad åtkomst__, och välj sedan __tjänsten__.
        2. Kopiera den __primärnyckel__ värde.
        3. Kopiera den __anslutningssträngen--primärnyckel__ värde.

    * __Från den [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, använder du följande kommando:

        1. Om du vill hämta värdet för primära nyckeln, använder du följande kommando:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Ersätt `myhubname` med namnet på din IoT-hubb. Svaret är den primära nyckeln till den `service` principen för den här hubben.

        2. Att hämta anslutningssträngen för den `service` princip, använder du följande kommando:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Ersätt `myhubname` med namnet på din IoT-hubb. Svaret är anslutningssträngen för den `service` princip.

## <a name="configure-the-source-connection"></a>Konfigurera anslutning datakälla

Utför följande åtgärder för att konfigurera källan för att arbeta med din IoT-hubb från en SSH-anslutning till kantnoden:

1. Skapa en kopia av den `connect-iot-source.properties` filen i den `/usr/hdp/current/kafka-broker/config/` directory. För att hämta filen från toketi-kafka-ansluta-iothub-projektet, använder du följande kommando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Så här redigerar du den `connect-iot-source.properties` filen och Lägg till IoT-hubb information, använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    I redigeraren, söka efter och ersätta följande uppgifter:

    * `Kafka.Topic=PLACEHOLDER`: Ersätta `PLACEHOLDER` med `iotin`. Meddelanden som tagits emot från IoT-hubb placeras i den `iotin` avsnittet.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Ersätta `PLACEHOLDER` med Event Hub-kompatibelt namn.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Ersätta `PLACEHOLDER` med slutpunkten Event Hub-kompatibel.
    * `IotHub.Partitions=PLACEHOLDER`: Ersätta `PLACEHOLDER` med antalet partitioner från föregående steg.
    * `IotHub.AccessKeyName=PLACEHOLDER`: Ersätta `PLACEHOLDER` med `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`: Ersätta `PLACEHOLDER` med den primära nyckeln för den `service` princip.
    * `IotHub.StartType=PLACEHOLDER`: Ersätta `PLACEHOLDER` med UTC-datum. Detta är när kopplingen startar söker efter meddelanden. Datumformatet är `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`: Ersätta `100` med `5`. Den här ändringen gör kopplingen för att läsa meddelanden i Kafka när det finns fem nya meddelanden i IoT-hubb.

    En exempelkonfiguration, se [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Spara ändringarna genom att använda __Ctrl + X__, __Y__, och sedan __RETUR__.

Mer information om hur du konfigurerar anslutningen källan finns [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Konfigurera sink-anslutning

Utför följande åtgärder från en SSH-anslutning till kantnoden för att konfigurera sink-anslutningen ska fungera med din IoT-hubb:

1. Skapa en kopia av den `connect-iothub-sink.properties` filen i den `/usr/hdp/current/kafka-broker/config/` directory. För att hämta filen från toketi-kafka-ansluta-iothub-projektet, använder du följande kommando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Så här redigerar du den `connect-iothub-sink.properties` filen och Lägg till IoT-hubb information, använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    I redigeraren, söka efter och ersätta följande uppgifter:

    * `topics=PLACEHOLDER`: Ersätta `PLACEHOLDER` med `iotout`. Meddelanden som skrivs till `iotout` avsnittet vidarebefordras till IoT-hubben.
    * `IotHub.ConnectionString=PLACEHOLDER`: Ersätta `PLACEHOLDER` med anslutningssträngen för den `service` princip.

    En exempelkonfiguration, se [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Spara ändringarna genom att använda __Ctrl + X__, __Y__, och sedan __RETUR__.

Mer information om hur du konfigurerar anslutningen sink finns [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Starta käll-koppling

Starta käll-koppling, med följande kommando från en SSH-anslutning till kantnoden:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

När kopplingen startar skicka meddelanden till IoT-hubb från dina enheter. När kopplingen läser meddelanden från IoT-hubb och lagrar dem i avsnittet Kafka, loggar information till konsolen:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> Flera varningar kan visas när kopplingen startar. Dessa varningar orsakar inte problem med att ta emot meddelanden från IoT-hubb.

Om du vill avbryta anslutningen använder __Ctrl + C__.

## <a name="start-the-sink-connector"></a>Starta sink-koppling

Från en SSH-anslutning till kantnoden använder du följande kommando för att starta sink-kopplingen i fristående läge:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

När kopplingen körs, visas information som liknar följande:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> Du kan se flera varningar när kopplingen startar. Du kan ignorera dem.

Använd följande steg för att skicka meddelanden via kopplingen:

1. Öppna en annan SSH-session till klustret Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Att skicka meddelanden till den `iotout` avsnittet använder du följande kommando:

    > [!WARNING]
    > Eftersom det är en ny SSH-anslutning på `$KAFKABROKERS` variabel inte innehåller någon information. Använd någon av följande metoder för att konfigurera den:
    >
    > * Använd de första tre stegen i den [konfigurera Kafka](#configure-kafka) avsnitt.
    > * Använd `echo $KAFKABROKERS` från den tidigare SSH-anslutningen till värden och Ersätt `$KAFKABROKERS` i följande kommando med de faktiska värdena.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Det här kommandot returnerar du inte till normal Bash-prompten. I stället skickar den tangentbordstryckningar till den `iotout` avsnittet.

3. Skicka ett meddelande till din enhet genom att klistra in JSON-dokument i SSH-session för den `kafka-console-producer`.

    > [!IMPORTANT]
    > Du måste ange värdet för den `"deviceId"` post-ID: t för din enhet. I följande exempel heter enheten `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Schemat för den här JSON-dokumentet beskrivs mer i detalj i [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Om du använder den simulerade hallon Pi-enheten och den körs, loggas följande meddelande av enheten:

    ```text
    Receive message: Turn On
    ```

    Skicka JSON-dokumentet, men ändrar du värdet för den `"message"` posten. Det nya värdet loggas av enheten.

Mer information om hur du använder sink-koppling finns [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Nästa steg

I det här dokumentet beskrivs hur du använder Kafka ansluta API för att starta IoT Kafka Connector på HDInsight. Använd följande länkar för att identifiera andra sätt att arbeta med Kafka:

* [Använda Apache Spark med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
