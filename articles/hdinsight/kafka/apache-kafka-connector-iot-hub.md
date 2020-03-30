---
title: Använda Apache Kafka på HDInsight med Azure IoT Hub
description: Lär dig hur du använder Apache Kafka på HDInsight med Azure IoT Hub. Kafka Connect Azure IoT Hub-projektet tillhandahåller en käll- och sink-anslutning för Kafka. Källanslutningen kan läsa data från IoT Hub och sink-anslutningen skriver till IoT Hub.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 48a72b5ba3819712b9e1d2536ae2dd3a06eaf3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238822"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Använda Apache Kafka på HDInsight med Azure IoT Hub

Lär dig hur du använder [Apache Kafka Connect Azure IoT](https://github.com/Azure/toketi-kafka-connect-iothub) Hub-anslutningsappen för att flytta data mellan Apache Kafka på HDInsight och Azure IoT Hub. I det här dokumentet får du lära dig hur du kör IoT Hub-kopplingen från en kantnod i klustret.

Med Kafka Connect API kan du implementera kopplingar som kontinuerligt hämtar data till Kafka eller skickar data från Kafka till ett annat system. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) är en anslutningsapp som hämtar data från Azure IoT Hub till Kafka. Det kan också skicka data från Kafka till IoT Hub.

När du hämtar från IoT Hub använder du en __källkontakt.__ När du trycker till IoT Hub använder du en __handfatanslutning.__ IoT Hub-kontakten tillhandahåller både käll- och handfistanslutningar.

Följande diagram visar dataflödet mellan Azure IoT Hub och Kafka på HDInsight när du använder kopplingen.

![Bild som visar data som flödar från IoT Hub till Kafka via kopplingen](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Mer information om Connect API [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)finns i .

## <a name="prerequisites"></a>Krav

* En Apache Kafka kluster på HDInsight. Mer information finns i dokumentet [Snabbstart för Kafka på HDInsight](apache-kafka-get-started.md).

* En kantnod i Kafka-klustret. Mer information finns i [användningskantnoderna med HDInsight-dokument.](../hdinsight-apps-use-edge-node.md)

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* En Azure IoT-hubb och enhet. I den här artikeln kan du använda [Connect Raspberry Pi online simulator till Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* [Scala bygga verktyg](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Skapa kopplingen

1. Hämta källan för anslutningen [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) från till din lokala miljö.

2. Navigera till katalogen från `toketi-kafka-connect-iothub-master` en kommandotolk. Använd sedan följande kommando för att skapa och paketera projektet:

    ```cmd
    sbt assembly
    ```

    Bygget tar några minuter att slutföra. Kommandot skapar en fil `kafka-connect-iothub-assembly_2.11-0.7.0.jar` som `toketi-kafka-connect-iothub-master\target\scala-2.11` namnges i katalogen för projektet.

## <a name="install-the-connector"></a>Installera kontakten

1. Ladda upp JAR-filen till kantnoden för kafka på HDInsight-klustret. Redigera kommandot nedan `CLUSTERNAME` genom att ersätta med det faktiska namnet på klustret. Standardvärdena för SSH-användarkontot och namnet på [kantnoden](../hdinsight-apps-use-edge-node.md#access-an-edge-node) används nedan, ändra efter behov.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. När filkopian är klar ansluter du till kantnoden med SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Så här installerar du anslutningen `libs` i Kafka-katalogen:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Håll SSH-anslutningen aktiv för de återstående stegen.

## <a name="configure-apache-kafka"></a>Konfigurera Apache Kafka

Från SSH-anslutningen till kantnoden gör du så här för att konfigurera Kafka så att den kör kopplingen i fristående läge:

1. Ställ in lösenordsvariabel. Ersätt LÖSENORD med lösenordet för klusterinloggning och ange sedan kommandot:

    ```bash
    export password='PASSWORD'
    ```

1. Installera [jq-verktyget.](https://stedolan.github.io/jq/) jq gör det enklare att bearbeta JSON-dokument som returneras från Ambari-frågor. Ange följande kommando:

    ```bash
    sudo apt -y install jq
    ```

1. Få adressen till Kafka mäklare. Det kan finnas många mäklare i klustret, men du behöver bara referera till en eller två. Använd följande kommando för att hämta adressen till två mäklarevärdar:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Kopiera värdena för senare användning. Det värde som genereras liknar följande text:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Hämta adressen till Apache Zookeeper-noderna. Det finns flera Zookeeper-noder i klustret, men du behöver bara referera till en eller två. Använd följande kommando för att lagra `KAFKAZKHOSTS`adresserna i variabeln :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. När du kör kopplingen i fristående `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` läge används filen för att kommunicera med Kafka-mäklarna. Så här `connect-standalone.properties` redigerar du filen:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Gör följande ändringar:

    |Aktuellt värde |Nytt värde | Kommentar |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Ersätta `localhost:9092` värdet med mäklarevärdarna från föregående steg|Konfigurerar den fristående konfigurationen för kantnoden för att hitta Kafka-mäklarna.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Med den här ändringen kan du testa med konsolen som ingår i Kafka. Du kan behöva olika omvandlare för andra producenter och konsumenter. Information om hur du använder [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)andra konverterarvärden finns i .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Samma som ovan.|
    |Ej tillämpligt|`consumer.max.poll.records=10`|Lägg till i slutet av filen. Den här ändringen är att förhindra timeout i sink-kopplingen genom att begränsa den till 10 poster åt gången. Mer information finns [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)i .|

1. Om du vill spara filen använder du __Ctrl + X__, __Y__och sedan __enter__.

1. Om du vill skapa de avsnitt som används av kopplingen använder du följande kommandon:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Om du `iotin` vill `iotout` kontrollera att det finns ämnen och ämnen använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Ämnet `iotin` används för att ta emot meddelanden från IoT Hub. Ämnet `iotout` används för att skicka meddelanden till IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Hämta information om IoT Hub-anslutning

Så här hämtar du IoT-hubbinformation som används av anslutningen:

1. Hämta slutpunktens slutpunkts- och Händelsenav-kompatibla slutpunktsnamn för din IoT-hubb. Använd någon av följande metoder för att hämta den här informationen:

   * Gör så här __på [Azure-portalen:](https://portal.azure.com/)__

     1. Navigera till IoT-hubben och välj __Slutpunkter__.
     2. Välj __Händelser__ __från inbyggda slutpunkter__.
     3. Kopiera värdet för följande fält från __Egenskaper:__

         * __Namn på händelsenavkompatibelt namn__
         * __Slutpunkt för händelsenavkompatibel__
         * __Partitioner__

        > [!IMPORTANT]  
        > Slutpunktsvärdet från portalen kan innehålla extra text som inte behövs i det här exemplet. Extrahera texten som matchar `sb://<randomnamespace>.servicebus.windows.net/`det här mönstret .

   * Använd följande kommando __i [Azure CLI:](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Ersätt `myhubname` med namnet på din IoT-hubb. Svaret liknar följande text:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Hämta __principen och__ __nyckeln__för delad åtkomst . Använd i det __service__ här exemplet tjänstnyckeln. Använd någon av följande metoder för att hämta den här informationen:

    * Gör så här __på [Azure-portalen:](https://portal.azure.com/)__

        1. Välj __Principer för delad åtkomst__och välj sedan __tjänst__.
        2. Kopiera värdet __för primärnyckeln.__
        3. Kopiera värdet __för anslutningssträngen – primärnyckel.__

    * Använd följande kommando __i [Azure CLI:](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__

        1. Använd följande kommando för att hämta primärnyckelvärdet:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Ersätt `myhubname` med namnet på din IoT-hubb. Svaret är den primära `service` nyckeln till principen för det här navet.

        2. Om du vill hämta `service` anslutningssträngen för principen använder du följande kommando:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Ersätt `myhubname` med namnet på din IoT-hubb. Svaret är anslutningssträngen `service` för principen.

## <a name="configure-the-source-connection"></a>Konfigurera källanslutningen

Om du vill konfigurera källan så att den fungerar med IoT Hub utför du följande åtgärder från en SSH-anslutning till kantnoden:

1. Skapa en kopia `connect-iot-source.properties` av `/usr/hdp/current/kafka-broker/config/` filen i katalogen. Om du vill hämta filen från toketi-kafka-connect-iothub-projektet använder du följande kommando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Om du `connect-iot-source.properties` vill redigera filen och lägga till IoT-hubbinformation använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. Sök efter och ändra följande poster i redigeraren:

    |Aktuellt värde |Redigera|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Ersätt `PLACEHOLDER` med `iotin`. Meddelanden som tas emot från IoT-hubben placeras i avsnittet. `iotin`|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Ersätt `PLACEHOLDER` med namnet Event Hub.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Ersätt `PLACEHOLDER` med slutpunkten för händelsehubben.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Ersätt `PLACEHOLDER` med `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Ersätt `PLACEHOLDER` med `service` principens huvudnyckel.|
    |`IotHub.Partitions=PLACEHOLDER`|Ersätt `PLACEHOLDER` med antalet partitioner från föregående steg.|
    |`IotHub.StartTime=PLACEHOLDER`|Ersätt `PLACEHOLDER` med ett UTC-datum. Det här datumet är när kopplingen börjar söka efter meddelanden. Datumformatet `yyyy-mm-ddThh:mm:ssZ`är .|
    |`BatchSize=100`|Ersätt `100` med `5`. Den här ändringen gör att kopplingen läser meddelanden i Kafka när det finns fem nya meddelanden i IoT-hubben.|

    En exempelkonfiguration finns i [Kafka Connect Source Connector för Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Om du vill spara ändringar använder du __Ctrl + X__, __Y__och sedan __enter__.

Mer information om hur du konfigurerar [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)anslutningskällan finns i .

## <a name="configure-the-sink-connection"></a>Konfigurera sink-anslutningen

Om du vill konfigurera sink-anslutningen så att den fungerar med IoT Hub utför du följande åtgärder från en SSH-anslutning till kantnoden:

1. Skapa en kopia `connect-iothub-sink.properties` av `/usr/hdp/current/kafka-broker/config/` filen i katalogen. Om du vill hämta filen från toketi-kafka-connect-iothub-projektet använder du följande kommando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Om du `connect-iothub-sink.properties` vill redigera filen och lägga till IoT-hubbinformation använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. Sök efter och ändra följande poster i redigeraren:

    |Aktuellt värde |Redigera|
    |---|---|
    |`topics=PLACEHOLDER`|Ersätt `PLACEHOLDER` med `iotout`. Meddelanden som `iotout` skrivits till ämnet vidarebefordras till IoT-hubben.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Ersätt `PLACEHOLDER` med anslutningssträngen `service` för principen.|

    En exempelkonfiguration finns i [Kafka Connect Sink Connector för Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Om du vill spara ändringar använder du __Ctrl + X__, __Y__och sedan __enter__.

Mer information om hur du konfigurerar [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)kopplingsmottagaren finns i .

## <a name="start-the-source-connector"></a>Starta källkontakten

1. Om du vill starta källanslutningen använder du följande kommando från en SSH-anslutning till kantnoden:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    När anslutningen startar skickar du meddelanden till IoT-hubben från enheten/enheten. När kopplingen läser meddelanden från IoT-hubben och lagrar dem i Kafka-avsnittet loggar den information till konsolen:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Du kan se flera varningar när kopplingen startar. Dessa varningar orsakar inga problem med att ta emot meddelanden från IoT-hubb.

1. Stoppa kopplingen efter några minuter med **Ctrl + C** två gånger. Det tar några minuter innan kontakten stannar.

## <a name="start-the-sink-connector"></a>Starta diskbänkskontakten

Från en SSH-anslutning till kantnoden använder du följande kommando för att starta sink-kopplingen i fristående läge:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

När kopplingen körs visas information som liknar följande text:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Du kan märka flera varningar när kopplingen startar. Du kan ignorera dem.

## <a name="send-messages"></a>Skicka meddelanden

Så här skickar du meddelanden via kopplingen:

1. Öppna *en andra* SSH-session i Kafka-klustret:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Få adressen till Kafka mäklare för den nya ssh session. Ersätt LÖSENORD med lösenordet för klusterinloggning och ange sedan kommandot:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Om du vill `iotout` skicka meddelanden till ämnet använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Det här kommandot återgår inte till den vanliga Bash-prompten. I stället skickas tangentbordsinmatning till ämnet. `iotout`

1. Om du vill skicka ett meddelande till enheten klistrar du `kafka-console-producer`in ett JSON-dokument i SSH-sessionen för .

    > [!IMPORTANT]  
    > Du måste ange värdet `"deviceId"` för posten till enhetens ID. I följande exempel heter `myDeviceId`enheten:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Schemat för det här JSON-dokumentet [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)beskrivs mer i detalj på .

    Om du använder den simulerade Raspberry Pi-enheten och den körs loggas följande meddelande av enheten:

    ```output
    Receive message: Turn On
    ```

    Skicka om JSON-dokumentet igen, men `"message"` ändra värdet på transaktionen. Det nya värdet loggas av enheten.

Mer information om hur du [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)använder handflänsanslutningen finns i .

## <a name="next-steps"></a>Nästa steg

I det här dokumentet lärde du dig hur du använder Apache Kafka Connect API för att starta IoT Kafka Connector på HDInsight. Använd följande länkar för att upptäcka andra sätt att arbeta med Kafka:

* [Använd Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använd Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
