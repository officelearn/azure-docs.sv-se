---
title: 'Självstudiekurs: HDInsight Apache Storm till lagring - Azure/Data Lake'
description: Självstudiekurs – Lär dig hur du använder Apache Storm för att skriva till HDFS-kompatibelt lagringsutrymme för Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 579163180f6c7ba19927ca66d20bd92d1b2de52e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73241205"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Självstudiekurs: Skriv till Apache Hadoop HDFS från Apache Storm på Azure HDInsight

Den här självstudien visar hur du använder Apache Storm för att skriva data till hdfs-kompatibel lagring som används av Apache Storm på HDInsight. HDInsight kan använda både Azure Storage och Azure Data Lake Storage som HDFS-kompatibel lagring. Storm tillhandahåller en [HDfsBolt-komponent](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) som skriver data till HDFS. Det här dokumentet innehåller information om hur du skriver till någon av typerna av lagringsutrymme från HdfsBolt.

Exempeltopologin som används i det här dokumentet är beroende av komponenter som ingår i Storm på HDInsight. Det kan kräva ändringar för att fungera med Azure Data Lake Storage när det används med andra Apache Storm-kluster.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera klustret med skriptåtgärd
> * Bygg och paketera topologin
> * Distribuera och köra topologin
> * Visa utdata
> * Stoppa topologin

## <a name="prerequisites"></a>Krav

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerad](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projektbyggsystem för Java-projekt.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta skulle `wasb://` vara för `abfs://` Azure Storage, för `adl://` Azure Data Lake Storage Gen2 eller för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Azure `wasbs://`Storage, skulle URI vara .  Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Exempelkonfiguration

Följande YAML är ett `resources/writetohdfs.yaml` utdrag från filen som ingår i exemplet. Den här filen definierar Storm-topologin med [Flux-ramverket](https://storm.apache.org/releases/current/flux.html) för Apache Storm.

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

Den här YAML definierar följande objekt:

* `syncPolicy`: Definierar när filer synkroniseras/rensas till filsystemet. I det här exemplet var 1000 tupplar.
* `fileNameFormat`: Definierar sökvägen och filnamnsmönstret som ska användas när filer skrivs. I det här exemplet anges sökvägen vid körning med hjälp `.txt`av ett filter och filtillägget är .
* `recordFormat`: Definierar det interna formatet för de skrivna filerna. I det här exemplet avgränsas fälten `|` av tecknet.
* `rotationPolicy`: Definierar när filer ska roteras. I det här exemplet utförs ingen rotation.
* `hdfs-bolt`: Använder de tidigare komponenterna `HdfsBolt` som konfigurationsparametrar för klassen.

Mer information om Flux-ramverket finns i [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Konfigurera klustret

Som standard innehåller Storm på HDInsight `HdfsBolt` inte de komponenter som används för att kommunicera med Azure Storage eller Data Lake Storage i Storms klassväg. Använd följande skriptåtgärd för att `extlib` lägga till dessa komponenter i katalogen för Storm i klustret:

| Egenskap | Värde |
|---|---|
|Skripttyp |- Anpassad|
|Bash skript URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Nodtyper |Nimbus, Handledare|
|Parametrar |Inget|

Information om hur du använder skriptet med klustret finns i [Anpassa HDInsight-kluster med skriptåtgärder.](./../hdinsight-hadoop-customize-cluster-linux.md)

## <a name="build-and-package-the-topology"></a>Bygg och paketera topologin

1. Ladda ner exempelprojektet från [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) till din utvecklingsmiljö.

2. Från en kommandotolk, terminal eller skalsession byter du kataloger till roten för det hämtade projektet. Om du vill skapa och paketera topologin använder du följande kommando:

    ```cmd
    mvn compile package
    ```

    När bygget och förpackningen är klar finns `target`det en ny `StormToHdfs-1.0-SNAPSHOT.jar`katalog med namnet , som innehåller en fil med namnet . Den här filen innehåller den kompilerade topologin.

## <a name="deploy-and-run-the-topology"></a>Distribuera och köra topologin

1. Använd följande kommando för att kopiera topologin till HDInsight-klustret. Ersätt `CLUSTERNAME` med namnet på klustret.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. När överföringen är klar använder du följande för att ansluta till HDInsight-klustret med SSH. Ersätt `CLUSTERNAME` med namnet på klustret.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. När du har anslutit använder du `dev.properties`följande kommando för att skapa en fil med namnet:

    ```bash
    nano dev.properties
    ```

1. Använd följande text som innehållet `dev.properties` i filen. Revidera efter behov baserat på [ditt URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Om du vill spara filen använder du __Ctrl + X__, sedan __Y__och slutligen __Retur__. Värdena i den här filen anger lagrings-URL:en och det katalognamn som data skrivs till.

1. Använd följande kommando för att starta topologin:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Det här kommandot startar topologin med hjälp av Flux-ramverket genom att skicka den till Nimbus-noden i klustret. Topologin definieras av `writetohdfs.yaml` filen som ingår i burken. Filen `dev.properties` skickas som ett filter och värdena i filen läss av topologin.

## <a name="view-output-data"></a>Visa utdata

Om du vill visa data använder du följande kommando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

En lista över de filer som skapats av den här topologin visas. Följande lista är ett exempel på de data som returneras av de tidigare kommandona:

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

Stormtopologier körs tills de stoppas, eller så tas klustret bort. Om du vill stoppa topologin använder du följande kommando:

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

I den här självstudien lärde du dig hur du använder Apache Storm för att skriva data till hdfs-kompatibel lagring som används av Apache Storm på HDInsight.

> [!div class="nextstepaction"]
> Upptäck andra [Apache Storm-exempel för HDInsight](apache-storm-example-topology.md)