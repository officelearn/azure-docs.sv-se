---
title: Använda Apache Kafka på HDInsight med Azure IoT Hub
description: Lär dig hur du använder Apache Kafka på HDInsight med Azure IoT Hub. Projektet Kafka ansluta Azure IoT Hub tillhandahåller en käll- och mottagar-anslutningsapp för Kafka. Käll-koppling kan läsa data från IoT Hub och mottagare anslutningen skriver till IoT Hub.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 8941a7332c19b1a9d5c04abb0e4b03ae83e98016
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260490"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Använda Apache Kafka på HDInsight med Azure IoT Hub

Lär dig hur du använder den [Kafka ansluta Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) -anslutningen för att flytta data mellan Apache Kafka på HDInsight och Azure IoT Hub. I det här dokumentet har du lära dig hur du kör IoT Hub-kopplingen från en edge-nod i klustret.

Kafka ansluta API: et kan du implementera kopplingar som kontinuerligt hämta data till Kafka eller skicka data från Kafka till ett annat system. Den [Kafka ansluta Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) är en koppling som hämtar data från Azure IoT Hub i Kafka. Det kan också skicka data från Kafka till IoT Hub. 

När du hämtar från IoT-hubben som du använder en __källa__ connector. När du skickar till IoT Hub, använder du en __mottagare__ connector. IoT Hub-anslutningen tillhandahåller både käll- och mottagar-kopplingar.

I följande diagram visas dataflödet mellan Azure IoT Hub och Kafka på HDInsight när du använder anslutningen.

![Bild som visar data som flödar från IoT Hub till Kafka via anslutningen](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Mer information om API: et ansluta finns i [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* En Kafka på HDInsight-klustret. Mer information finns i dokumentet [Snabbstart för Kafka i HDInsight](apache-kafka-get-started.md).

* En kantnod i Kafka-klustret. Mer information finns i den [använda kantnoder med HDInsight](../hdinsight-apps-use-edge-node.md) dokumentet.

* En Azure IoT-hubb. Den här självstudien rekommenderar jag den [Anslut Raspberry Pi onlinesimulator på Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) dokumentet.

* En SSH-klient. Stegen i det här dokumentet använder SSH för att ansluta till klustret. Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Installera connector

1. Ladda ned den senaste versionen av Kafka ansluta för Azure IoT Hub från [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Om du vill överföra .jar-filen till gränsnoden för dina Kafka på HDInsight-klustret använder du följande kommando:

    > [!NOTE]
    > Ersätt `sshuser` med SSH-användarkontot för ditt HDInsight-kluster. Ersätt `new-edgenode` med namnet på edge-noden. Ersätt `clustername` med klustrets namn. Mer information om SSH-slutpunkten för gränsnoden finns i den [används edge-noder med HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node) dokumentet.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. När filkopian är klar kan du ansluta till gränsnoden via SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Att installera anslutningen i Kafka `libs` directory, använder du följande kommando:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Om du stöter på problem med resten av stegen i det här dokumentet när du använder en färdiga .jar-fil kan du försöka med att skapa paket från källan.
>
> För att skapa anslutningen, måste du ha en Scala-utvecklingsmiljö med den [Scala skapa verktyget](http://www.scala-sbt.org/).
>
> 1. Hämtar källan för anslutningstjänsten från [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) till utvecklingsmiljön.
>
> 2. Från Kommandotolken i projektkatalogen, använder du följande kommando för att bygga och paketera projektet:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Det här kommandot skapar en fil med namnet `kafka-connect-iothub-assembly_2.11-0.6.jar` i den `target/scala-2.11` katalogen för projektet.

## <a name="configure-kafka"></a>Konfigurera Kafka

Använd följande steg för att konfigurera Kafka för att köra anslutningstjänsten i fristående läge från en SSH-anslutning till gränsnoden:

1. Spara klusternamnet i en variabel. Med hjälp av en variabel gör det lättare att kopiera och klistra in andra kommandona i det här avsnittet:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Installera den `jq` verktyget. Det här verktyget gör det enklare att bearbeta JSON-dokument som returneras från Ambari frågor:

    ```bash
    sudo apt -y install jq
    ```

3. Hämta adressen för Kafka asynkrona meddelandeköer. Det kan finnas många asynkrona meddelandeköer i klustret, men du behöver bara referera till en eller två. Om du vill hämta adressen för två broker värdar, använder du följande kommando:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin). Det värde som genereras liknar följande text:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Hämta adressen för Zookeeper-noder. Det finns flera Zookeeper-noder i klustret, men du behöver bara referera till en eller två. Om du vill hämta adressen för två Zookeeper-noder, använder du följande kommando:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. När du kör anslutningen i fristående läge, den `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` används för att kommunicera med asynkrona meddelandeköer i Kafka. Så här redigerar du den `connect-standalone.properties` fil ska du använda följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Hitta raden som börjar med för att konfigurera fristående konfiguration för gränsnoden att hitta Kafka mäklare, `bootstrap.servers=`. Ersätt den `localhost:9092` värde med broker-värdar från föregående steg.

    * Ändra den `key.converter=` och `value.converter=` rader på följande värden:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Den här ändringen kan du testa med hjälp av konsolen producenten som medföljer Kafka. Du kan behöva olika konverterare för andra producenter och konsumenter. Information om hur du använder andra converter värden finns i [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Lägg till en rad i slutet av filen med följande text:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Den här ändringen är att förhindra tidsgränser i mottagare connector genom att begränsa den till 10 poster i taget. Mer information finns på [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Om du vill spara filen, Använd __Ctrl + X__, __Y__, och sedan __RETUR__.

7. Använd följande kommandon för att skapa ämnen som används av kopplingen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Kontrollera att den `iotin` och `iotout` ämnen finns, använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Den `iotin` avsnittet används för att ta emot meddelanden från IoT Hub. Den `iotout` avsnittet används för att skicka meddelanden till IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Anslutningsinformation för IoT Hub

Använd följande steg för att hämta information om IoT hub som används av anslutningen:

1. Hämta Event Hub-kompatibla slutpunkten och Event Hubs-kompatibla slutpunktnamn för din IoT-hubb. Använd någon av följande metoder för att få den här informationen:

    * __Från den [Azure-portalen](https://portal.azure.com/)__, Använd följande steg:

        1. Gå till din IoT-hubb och välj __slutpunkter__.
        2. Från __inbyggda slutpunkter__väljer __händelser__.
        3. Från __egenskaper__, Kopiera värdet för följande fält:

            * __Event Hub-kompatibla namnet__
            * __Event Hubs-kompatibla slutpunkten__
            * __Partitioner__

        > [!IMPORTANT]
        > Slutpunktsvärdet från portalen kan innehålla extra text som inte behövs i det här exemplet. Extrahera text som matchar det här mönstret `sb://<randomnamespace>.servicebus.windows.net/`.

    * __Från den [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, använder du följande kommando:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Ersätt `myhubname` med namnet på din IoT-hubb. Svaret liknar följande text:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Hämta den __delad åtkomstprincip__ och __nyckeln__. Det här exemplet använder den __service__ nyckel. Använd någon av följande metoder för att få den här informationen:

    * __Från den [Azure-portalen](https://portal.azure.com/)__, Använd följande steg:

        1. Välj __principer för delad åtkomst__, och välj sedan __service__.
        2. Kopiera den __primärnyckel__ värde.
        3. Kopiera den __anslutningssträng – primärnyckel__ värde.

    * __Från den [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, använder du följande kommando:

        1. Om du vill hämta värdet för primära nyckeln, använder du följande kommando:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Ersätt `myhubname` med namnet på din IoT-hubb. Svaret är den primära nyckeln så att den `service` principen för den här hubben.

        2. Att hämta anslutningssträngen för den `service` princip, använder du följande kommando:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Ersätt `myhubname` med namnet på din IoT-hubb. Svaret är anslutningssträngen för den `service` principen.

## <a name="configure-the-source-connection"></a>Konfigurera anslutning datakälla

Utför följande åtgärder för att konfigurera källan för att arbeta med din IoT-hubb, från en SSH-anslutning till gränsnoden:

1. Skapa en kopia av den `connect-iot-source.properties` fil i den `/usr/hdp/current/kafka-broker/config/` directory. För att hämta filen från toketi-kafka-connect-iothub-projektet, använder du följande kommando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Så här redigerar du den `connect-iot-source.properties` filen och Lägg till IoT hub information, använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    I redigeraren, hitta och ändra följande poster:

    * `Kafka.Topic=PLACEHOLDER`: Ersätt `PLACEHOLDER` med `iotin`. Meddelanden som tas emot från IoT hub är placerade i den `iotin` avsnittet.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Ersätt `PLACEHOLDER` med Event Hubs-kompatibla namnet.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Ersätt `PLACEHOLDER` med Event Hubs-kompatibla slutpunkten.
    * `IotHub.Partitions=PLACEHOLDER`: Ersätt `PLACEHOLDER` med antalet partitioner från föregående steg.
    * `IotHub.AccessKeyName=PLACEHOLDER`: Ersätt `PLACEHOLDER` med `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`: Ersätt `PLACEHOLDER` med den primära nyckeln i den `service` principen.
    * `IotHub.StartType=PLACEHOLDER`: Ersätt `PLACEHOLDER` med UTC-datum. Det här datumet är när anslutningen startar söker efter meddelanden. Datumformatet är `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`: Ersätt `100` med `5`. Den här ändringen gör anslutningen kan läsa meddelanden i Kafka när det finns fem nya meddelanden i IoT hub.

    En exempelkonfiguration, se [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Spara ändringarna genom att använda __Ctrl + X__, __Y__, och sedan __RETUR__.

Läs mer om hur du konfigurerar anslutningen källan [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Konfigurera anslutningen till mottagare

För att konfigurera anslutningen mottagare att arbeta med din IoT-hubb, utför du följande åtgärder från en SSH-anslutning till gränsnoden:

1. Skapa en kopia av den `connect-iothub-sink.properties` fil i den `/usr/hdp/current/kafka-broker/config/` directory. För att hämta filen från toketi-kafka-connect-iothub-projektet, använder du följande kommando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Så här redigerar du den `connect-iothub-sink.properties` filen och Lägg till IoT hub information, använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    I redigeraren, hitta och ändra följande poster:

    * `topics=PLACEHOLDER`: Ersätt `PLACEHOLDER` med `iotout`. Meddelanden som skrivs till `iotout` avsnittet vidarebefordras till IoT hub.
    * `IotHub.ConnectionString=PLACEHOLDER`: Ersätt `PLACEHOLDER` med anslutningssträngen för den `service` principen.

    En exempelkonfiguration, se [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Spara ändringarna genom att använda __Ctrl + X__, __Y__, och sedan __RETUR__.

Mer information om hur du konfigurerar anslutningen mottagare finns i [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Starta käll-koppling

Starta käll-anslutningen med följande kommando från en SSH-anslutning till gränsnoden:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

När anslutningen startar, skicka meddelanden till IoT hub från dina enheter. När kopplingen läser meddelanden från IoT hub och lagrar dem i Kafka-ämne, loggar information till konsolen:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> Du kan se flera varningar som anslutningen startar. Dessa varningar orsakar inte problem med att ta emot meddelanden från IoT hub.

Om du vill stoppa anslutningen, använda __Ctrl + C__.

## <a name="start-the-sink-connector"></a>Starta anslutningen mottagare

Från en SSH-anslutning till gränsnoden, använder du följande kommando för att starta anslutningen mottagare i fristående läge:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

När anslutningen körs visas information liknande följande text:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> Du kanske ser flera varningar när anslutningen startar. Du kan ignorera dem.

Använd följande steg för att skicka meddelanden via anslutningen:

1. Öppna en annan SSH-session till Kafka-klustret:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Att skicka meddelanden till den `iotout` avsnittet använder du följande kommando:

    > [!WARNING]
    > Eftersom detta är en ny SSH-anslutning i `$KAFKABROKERS` variabeln innehåller inte någon information. Använd någon av följande metoder för att konfigurera den:
    >
    > * Använd de första tre stegen i den [konfigurera Kafka](#configure-kafka) avsnittet.
    > * Använd `echo $KAFKABROKERS` från föregående SSH-anslutningen till värden och Ersätt sedan `$KAFKABROKERS` i följande kommando med de faktiska värdena.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Det här kommandot returnerar du inte till normal Bash-Kommandotolken. I stället skickar den tangentbordstryckningar till den `iotout` avsnittet.

3. Skicka ett meddelande till din enhet genom att klistra in JSON-dokument i SSH-session för den `kafka-console-producer`.

    > [!IMPORTANT]
    > Du måste ange värdet för den `"deviceId"` post-ID: t för din enhet. I följande exempel heter enheten `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Schemat för det här JSON-dokumentet beskrivs i detalj i [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Om du använder den simulerade enheten Raspberry Pi och den körs, loggas följande meddelande av enheten:

    ```text
    Receive message: Turn On
    ```

    Skicka om JSON-dokumentet, men ändra värdet för den `"message"` posten. Det nya värdet loggas av enheten.

Mer information om hur du använder anslutningstjänsten mottagare finns i [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Kafka ansluta API för att starta Connector för IoT-Kafka på HDInsight. Använd följande länkar för att upptäcka andra sätt att arbeta med Kafka:

* [Använda Apache Spark med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
