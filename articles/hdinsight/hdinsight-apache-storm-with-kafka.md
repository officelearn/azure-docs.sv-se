---
title: "Använda Apache Kafka med Storm på HDInsight - Azure | Microsoft Docs"
description: "Apache Kafka har installerats med Apache Storm på HDInsight. Lär dig mer om att skriva till Kafka och läses sedan från den, använder de KafkaBolt och KafkaSpout med Storm. Också lära dig hur du använder som framework att definiera och skicka Storm-topologier."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2018
ms.author: larryfr
ms.openlocfilehash: 9e714b1dc97fb12c2994537d33cefd98b5c9cf99
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>Använda Apache Kafka med Storm på HDInsight

Lär dig hur du använder Apache Storm att läsa från och skriva till Apache Kafka. Det här exemplet visar även hur du sparar data från en Storm-topologi till HDFS-kompatibla filsystem som används av HDInsight.

> [!NOTE]
> Stegen i det här dokumentet skapa ett Azure-resursgrupp som innehåller både ett Storm på HDInsight och en Kafka på HDInsight-kluster. Dessa kluster finns både i ett Azure Virtual Network, vilket innebär att Storm-klustret kan kommunicera direkt med Kafka kluster.
> 
> Kom ihåg att ta bort kluster för att undvika överdriven avgifter när du är klar med steg i det här dokumentet.

## <a name="get-the-code"></a>Hämta koden

Koden för exemplet i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

För att kompilera det här projektet, behöver du följande konfiguration för din utvecklingsmiljö:

* [Java JDK 1.8](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) eller högre. HDInsight 3.5 eller högre krävs Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* En SSH-klient (du behöver den `ssh` och `scp` kommandon) – information finns i [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* En textredigerare eller IDE.

Följande miljövariabler kan anges när du installerar Java och JDK på utvecklingsdatorn. Dock bör du kontrollera att de finns och att de innehåller rätt värden för ditt system.

* `JAVA_HOME` -måste peka på den katalog där JDK har installerats.
* `PATH` -bör innehålla följande sökvägar:
  
    * `JAVA_HOME` (eller motsvarande sökväg).
    * `JAVA_HOME\bin` (eller motsvarande sökväg).
    * Den katalog där Maven är installerat.

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till Kafka mäklare via det offentliga internet. Allt som kommunicerar Kafka måste finnas i samma virtuella Azure-nätverket som noder i klustret Kafka. I det här exemplet finns både Kafka och Storm-kluster i Azure-nätverk. Följande diagram visar hur kommunikation som flödar mellan kluster:

![Diagram över Storm och Kafka kluster i Azure-nätverk](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Andra tjänster på klustret, till exempel SSH och Ambari kan nås via internet. Mer information om de offentliga portarna som är tillgängliga med HDInsight finns [portar och URI: er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Du kan skapa en Azure-nätverk, Kafka, och Storm-kluster manuellt, men det är enklare att använda en Azure Resource Manager-mall. Använd följande steg för att distribuera ett Azure-nätverk, Kafka, och Storm-kluster till Azure-prenumeration.

1. Använd knappen följande för att logga in på Azure och öppna mallen i Azure-portalen.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager-mallen finns på **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v2.json**. Den skapar följande resurser:
    
    * Azure-resursgrupp
    * Azure Virtual Network
    * Azure-lagringskonto
    * Kafka på HDInsight version 3,6 (tre arbetarnoder)
    * Storm på HDInsight version 3,6 (tre arbetarnoder)

  > [!WARNING]
  > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka kluster som innehåller tre arbetsnoderna.

2. Använd följande riktlinjer för att fylla posterna på den **anpassad distribution** avsnitt:
   
    ![HDInsight anpassad distribution](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Resursgruppen**: skapa en grupp eller välj en befintlig. Den här gruppen innehåller HDInsight-klustret.
   
    * **Plats**: Välj en plats geografiskt nära dig.

    * **Basera klusternamnet**: det här värdet används som huvudnamnet för Storm och Kafka kluster. Ange till exempel **hdi** skapar en Storm-kluster med namnet **storm-hdi** och ett Kafka kluster med namnet **kafka hdi**.
   
    * **Klustrets inloggningsnamn**: admin användarnamn Storm och Kafka kluster.
   
    * **Klustret inloggningslösenordet**: administratörslösenord för Storm och Kafka kluster.
    
    * **SSH-användarnamn**: SSH-användare skapa för Storm och Kafka kluster.
    
    * **SSH-lösenordet**: lösenord för SSH-användare för Storm och Kafka kluster.

3. Läs den **villkor**, och välj sedan **jag samtycker till villkoren som anges ovan**.

4. Kontrollera slutligen **fäst på instrumentpanelen** och välj sedan **inköp**. Det tar ungefär 20 minuter för att skapa kluster.

När resurserna som har skapats visas i avsnittet för resursgruppen.

![Avsnittet resursgrupp för virtuella nätverk och kluster](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Lägg märke till att namnen på HDInsight-kluster **storm-BASENAME** och **kafka BASENAME**, där BASENAME är det namn du angav i mallen. Du kan använda dessa namn i senare steg när du ansluter till kluster.

## <a name="understanding-the-code"></a>Förstå koden

Det här projektet innehåller två topologier:

* **KafkaWriter**: definieras av den **writer.yaml** filen, den här topologin skriver slumpmässiga meningar till Kafka med KafkaBolt med Apache Storm.

    Den här topologin använder en anpassad **SentenceSpout** komponenten som ska generera en slumpmässig meningar.

* **KafkaReader**: definieras av den **reader.yaml** filen, den här topologin läser data från Kafka med KafkaSpout med Apache Storm och sedan loggar data STDOUT.

    Den här topologin använder Storm-HdfsBolt för att skriva data till standardlagring för Storm-kluster.
### <a name="flux"></a>Flux

Topologierna definieras med hjälp av [som](https://storm.apache.org/releases/1.1.2/flux.html). Som introducerades i Storm-0.10.x och du kan avgränsa topologi konfigurationen från koden. Topologier som använder ramverket som definieras topologin i en YAML-fil. YAML-filen kan vara ingår i topologin. Det kan också vara en fristående fil som används när du skickar in topologin. Som stöder också variabeln ersättning vid körning, som används i det här exemplet.

Följande parametrar anges vid körning för dessa topologier:

* `${kafka.topic}`: Namnet på avsnittet Kafka som den topologier läsning och skrivning till.

* `${kafka.broker.hosts}`: De värdar som Kafka förmedlar köras på. Service broker-informationen används av KafkaBolt vid skrivning till Kafka.

* `${kafka.zookeeper.hosts}`: De värdar som Zookeeper körs på i Kafka-klustret.

Mer information om topologier som finns [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

## <a name="download-and-compile-the-project"></a>Hämta och kompileras projektet

1. Hämta projektet från på din utvecklingsmiljö [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), öppna en kommandorad och ändra kataloger till platsen som du hämtade projektet.

2. Från den **hdinsight-storm-java-kafka** directory, Använd följande kommando för att kompilera projektet och skapa ett paket för distribution:

  ```bash
  mvn clean package
  ```

    Paketet skapas en fil med namnet `KafkaTopology-1.0-SNAPSHOT.jar` i den `target` directory.

3. Använd följande kommandon för att kopiera paketet till ditt Storm på HDInsight-kluster. Ersätt **användarnamn** med SSH-användarnamn för klustret. Ersätt **BASENAME** med det grundläggande namnet som du använde när du skapar klustret.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    När du uppmanas, ange lösenordet som du använde när du skapar kluster.

## <a name="configure-the-topology"></a>Konfigurera topologin

1. Använd någon av följande metoder för att identifiera Kafka broker värdar:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > Bash-exemplet förutsätter att `$CLUSTERNAME` innehåller namnet på HDInsight-klustret. Det förutsätts även att som [jq](https://stedolan.github.io/jq/) är installerad. När du uppmanas, ange lösenordet för inloggningskontot för klustret.

    Det värde som returneras liknar följande:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Det kan finnas fler än två broker värdar för klustret, behöver du inte ange en fullständig lista över alla värdar till klienter. En eller två är tillräckligt.

2. Använd någon av följande metoder för att identifiera Kafka Zookeeper-värdar:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > Bash-exemplet förutsätter att `$CLUSTERNAME` innehåller namnet på HDInsight-klustret. Det förutsätts även att som [jq](https://stedolan.github.io/jq/) är installerad. När du uppmanas, ange lösenordet för inloggningskontot för klustret.

    Det värde som returneras liknar följande:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Det finns fler än två Zookeeper-noder, behöver du inte ange en fullständig lista över alla värdar till klienter. En eller två är tillräckligt.

    Spara det här värdet eftersom det används senare.

3. Redigera den `dev.properties` filen i roten av projektet. Lägga till den Service Broker och Zookeeper värdar i matchande rader i den här filen. I följande exempel konfigureras med hjälp av exempelvärden från föregående steg:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Spara den `dev.properties` filen och sedan använda följande kommando för att överföra den till Storm-kluster:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
    ```

    Ersätt **användarnamn** med SSH-användarnamn för klustret. Ersätt **BASENAME** med det grundläggande namnet som du använde när du skapar klustret.

## <a name="start-the-writer"></a>Starta skrivaren

1. Använd följande för att ansluta till Storm-kluster med SSH. Ersätt **användarnamn** med SSH-användarnamn som används när du skapar klustret. Ersätt **BASENAME** med det grundläggande namnet som används när du skapar klustret.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    När du uppmanas, ange lösenordet som du använde när du skapar kluster.
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använder du följande kommando från SSH-anslutning för att skapa avsnittet Kafka används av topologi:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Ersätt `$KAFKAZKHOSTS` med Zookeeper värd för information som du hämtade i föregående avsnitt.

2. Från SSH-anslutning till Storm-kluster använder du följande kommando för att starta writer topologi:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    De parametrar som används med det här kommandot är:

    * `org.apache.storm.flux.Flux`: Använd som du konfigurerar och kör den här topologin.

    * `--remote`: Skicka topologi och Nimbus. Topologi distribueras över arbetarnoder i klustret.

    * `-R /writer.yaml`: Använd den `writer.yaml` filen om du vill konfigurera topologin. `-R` Anger att den här resursen ingår i jar-filen. Det är i roten av jar så `/writer.yaml` är sökvägen till den.

    * `--filter`: Fylla poster i den `writer.yaml` topologi med värdena i den `dev.properties` filen. Exempelvis värdet för den `kafka.topic` post i filen används för att ersätta den `${kafka.topic}` post i topologin definition.

5. När topologin har börjat använder du följande kommando för att kontrollera att den data skrivs till avsnittet Kafka:

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    Ersätt `$KAFKAZKHOSTS` med Zookeeper värd för information som du hämtade i föregående avsnitt.

    Det här kommandot använder ett skript som medföljer Kafka för att övervaka avsnittet. Efter ett tag och bör börja returnera slumpmässiga meningar som har skrivits till ämnet. Utdata ser ut ungefär så här:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Använd Ctrl + c för att stoppa skriptet.

## <a name="start-the-reader"></a>Starta läsaren

1. Från SSH-session till Storm-kluster använder du följande kommando för att starta reader-topologi:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. När topologin startar öppna Storm-Användargränssnittet. Det här webbinnehållet Användargränssnittet finns på `https://storm-BASENAME.azurehdinsight.net/stormui`. Ersätt __BASENAME__ med det grundläggande namnet som används när klustret skapades. 

    När du uppmanas, Använd inloggningsnamnet admin (standard `admin`) och lösenord som används när klustret skapades. Du ser en webbsida som liknar följande bild:

    ![Storm UI](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Storm-Användargränssnittet, Välj den __kafka läsare__ länken i den __Topology Summary__ avsnitt för att visa information om den __kafka läsare__ topologi.

    ![Topologi sammanfattningen i Storm webbgränssnittet](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Om du vill visa information om instanser av komponenten loggaren bult, Välj den __loggaren bult__ länken i den __bultar (hela tiden)__ avsnitt.

    ![Loggaren bult länk i avsnittet bultar](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. I den __Executors__ väljer du en länk i den __Port__ kolumn som ska visas logga information om den här instansen av komponenten.

    ![Executors länk](./media/hdinsight-apache-storm-with-kafka/executors.png)

    Loggen innehåller en logg över data läses från avsnittet Kafka. Informationen i loggen liknar följande:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Stoppa topologierna

Använd följande kommandon från en SSH-session till Storm-kluster för att stoppa Storm-topologier:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Eftersom stegen i det här dokumentet skapa båda klustren i samma Azure resursgrupp måste du ta bort resursgruppen i Azure-portalen. Ta bort resursgruppen tar bort alla resurser som har skapats genom att följa det här dokumentet.

## <a name="next-steps"></a>Nästa steg

Flera exempeltopologier som kan användas med Storm på HDInsight finns [exempel Storm-topologier och komponenter](storm/apache-storm-example-topology.md).

Mer information om distribuera och övervaka topologier på Linux-baserat HDInsight finns [distribuera och hantera Apache Storm-topologier på Linux-baserat HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)