---
title: Självstudiekurs – använda Apache Storm för att skriva till lagring/Data Lake Storage - Azure HDInsight
description: Självstudie – Lär dig hur du använder Apache Storm för att skriva till det HDFS-kompatibla lagringsutrymmet för Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 5c1376c7d1afe9c9702cfb43a146ac1cd17d6e58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428350"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Självstudier: Skriva till Apache Hadoop HDFS från Apache Storm på Azure HDInsight

Den här självstudien visar hur du använder Apache Storm för att skriva data till det HDFS-kompatibla lagringsutrymmet som används av Apache Storm på HDInsight. HDInsight kan använda både Azure Storage och Azure Data Lake Storage som HDFS-kompatibla lagringsutrymmet. Storm innehåller en [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) komponent som skriver data till HDFS. Det här dokumentet innehåller information om skrivning till valfri typ av lagring från HdfsBolt.

Exempel-topologin som används i det här dokumentet är beroende av komponenter som ingår i Storm på HDInsight. Det kan kräva ändring av att arbeta med Azure Data Lake Storage när det används med andra Apache Storm-kluster.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera klustret med skriptåtgärd
> * Bygga och paketera topologin
> * Distribuera och köra topologin
> * Visa utdata
> * Stoppa topologin

## <a name="prerequisites"></a>Förutsättningar

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt buildsystemet för Java-projekt.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Den [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för ditt kluster primär lagring. Detta skulle vara `wasb://` för Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage Gen1. Om säker överföring har aktiverats för Azure Storage eller Data Lake Storage Gen2, URI: N blir `wasbs://` eller `abfss://`respektive Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Exempelkonfiguration

Följande YAML är hämtad från den `resources/writetohdfs.yaml` -filen som ingår i det här exemplet. Den här filen definierar Storm-topologi med den [som](https://storm.apache.org/releases/1.1.2/flux.html) ramverk för Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Den här YAML definierar följande:

* `syncPolicy`: Definierar när filer är synkroniserad/rensade till filsystemet. I det här exemplet var 1000 tupplar.
* `fileNameFormat`: Definierar namnmönstret sökvägen och filnamnet för att använda när du skriver filer. I det här exemplet tillhandahålls sökvägen vid körning med hjälp av ett filter och filnamnstillägget `.txt`.
* `recordFormat`: Definierar det interna formatet för filerna som skrivits. I det här exemplet fälten avgränsas med den `|` tecken.
* `rotationPolicy`: Definierar när du roterar filer. I det här exemplet utförs ingen rotation.
* `hdfs-bolt`: Använder de tidigare komponenterna som konfigurationsparametrar för den `HdfsBolt` klass.

Mer information om ramverket som finns i [ https://storm.apache.org/releases/current/flux.html ](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Konfigurera klustret

Som standard Storm på HDInsight inte innehåller komponenterna som `HdfsBolt` använder för att kommunicera med Azure Storage eller Data Lake Storage i Storms klassökväg. Använd följande skriptåtgärd för att lägga till dessa komponenter i `extlib` katalogen för Storm i klustret:

| Egenskap | Värde |
|---|---|
|Skripttyp |– Anpassad|
|Bash-skript-URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Nodtyper |Nimbus, Supervisor|
|Parametrar |Ingen|

Information om hur du använder det här skriptet med ditt kluster finns i den [anpassa HDInsight-kluster med skriptåtgärder](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentet.

## <a name="build-and-package-the-topology"></a>Bygga och paketera topologin

1. Ladda ned exempelprojektet från [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) till utvecklingsmiljön.

2. Från en kommandotolk och terminal eller skal-session, ändra kataloger till roten på det hämta projektet. Om du vill bygga och paketera topologin, använder du följande kommando:

    ```cmd
    mvn compile package
    ```

    När det är klart att skapa och paketering, det finns en ny katalog med namnet `target`, som innehåller en fil med namnet `StormToHdfs-1.0-SNAPSHOT.jar`. Den här filen innehåller den kompilerade topologin.

## <a name="deploy-and-run-the-topology"></a>Distribuera och köra topologin

1. Använd följande kommando för att kopiera topologi till HDInsight-klustret. Ersätt `CLUSTERNAME` med namnet på klustret.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. När överföringen är klar kan du använda följande för att ansluta till HDInsight-klustret med SSH. Ersätt `CLUSTERNAME` med namnet på klustret.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. När du är ansluten, använder du följande kommando för att skapa en fil med namnet `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Använd följande text som innehållet i den `dev.properties` filen. Ändra vid behov baserat på din [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Om du vill spara filen, Använd __Ctrl + X__, sedan __Y__, och slutligen __RETUR__. Värdet i den här filen in lagrings-URL och katalognamnet som data ska skrivas till.

1. Använd följande kommando för att starta topologin:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Detta kommando startar topologin med hjälp av ramverket som genom att skicka den till Nimbus-noden i klustret. Topologin definieras av den `writetohdfs.yaml` filen som ingår i den JAR-filen. Den `dev.properties` fil skickas som ett filter och de värden som finns i filen läses av topologin.

## <a name="view-output-data"></a>Visa utdata

Om du vill visa data, använder du följande kommando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

En lista över filer som skapas av den här topologin visas. I följande lista är ett exempel på data som returneras av de tidigare kommandona:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Stoppa topologin

Storm-topologier köra tills de stoppas eller klustret tas bort. Om du vill stoppa topologin, använder du följande kommando:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade HDInsight-klustret och eventuella andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure Portal:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer sedan __Resursgrupper__ för att visa listan med dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda Apache Storm för att skriva data till det HDFS-kompatibla lagringsutrymmet som används av Apache Storm på HDInsight.

> [!div class="nextstepaction"]
> Identifiera andra [Apache Storm-exempel för HDInsight](apache-storm-example-topology.md)