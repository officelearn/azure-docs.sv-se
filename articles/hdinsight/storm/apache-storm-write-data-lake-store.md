---
title: Apache Storm skrivning till Storage-Data Lake Store - Azure HDInsight | Microsoft Docs
description: "Lär dig hur du använder Apache Storm för att skriva till HDFS-kompatibla lagringen för HDInsight. Azure Storage eller Azure Data Lake Store ger HDFS comptabile lagring för HDInsight. Detta dokument och associerad exemplet visar hur komponenten HdfsBolt kan användas för att skriva till standardlagring av ett Storm på HDInsight-kluster."
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 908a1130171cc091b4d95d4532cc3bb95edb44f9
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Skriva till HDFS från Apache Storm på HDInsight

Lär dig hur du använder Storm för att skriva data till HDFS-kompatibla lagring som används av Apache Storm på HDInsight. HDInsight kan använda båda Azure Storage- och Azure Data Lake lagra som HDFS comptabile lagring. Storm tillhandahåller en [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) komponent som skriver data till HDFS. Det här dokumentet innehåller information om hur du skriver till båda typerna av lagring från HdfsBolt. 

> [!IMPORTANT]
> Exempel topologin som används i det här dokumentet är beroende av komponenter som ingår i Storm på HDInsight. Det kan behöva ändras för att arbeta med Azure Data Lake Store tillsammans med andra Apache Storm-kluster.

## <a name="get-the-code"></a>Hämta koden

Projektet som innehåller den här topologin är tillgänglig för hämtning från [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

För att kompilera det här projektet, behöver du följande konfiguration för din utvecklingsmiljö:

* [Java JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) eller högre. HDInsight 3.5 eller högre krävs Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Följande miljövariabler kan anges när du installerar Java och JDK på utvecklingsdatorn. Dock bör du kontrollera att de finns och att de innehåller rätt värden för ditt system.

* `JAVA_HOME` -måste peka på den katalog där JDK har installerats.
* `PATH` -bör innehålla följande sökvägar:
  
    * `JAVA_HOME` (eller motsvarande sökväg).
    * `JAVA_HOME\bin` (eller motsvarande sökväg).
    * Den katalog där Maven är installerat.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Hur du använder HdfsBolt med HDInsight

> [!IMPORTANT]
> Innan du använder HdfsBolt med Storm på HDInsight, måste du först använda en skriptåtgärd kopiera krävs jar-filerna till den `extpath` för Storm. Mer information finns i [konfigurera klustret](#configure) avsnitt.

HdfsBolt använder schemat för filen som du anger för att förstå hur att skriva till HDFS. Använd någon av följande scheman med HDInsight:

* `wasb://`: Används med ett Azure Storage-konto.
* `adl://`: Används med Azure Data Lake Store.

Följande tabell innehåller exempel på användning av filen schemat för olika scenarier:

| schemat | Anteckningar |
| ----- | ----- |
| `wasb:///` | Standardkontot för lagring är en blobbbehållare i ett Azure Storage-konto |
| `adl:///` | Standardkontot för lagring är en katalog i Azure Data Lake Store. När klustret skapas Ange katalog i Data Lake Store är roten till klustrets HDFS. Till exempel den `/clusters/myclustername/` directory. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | En icke-standard (ytterligare) Azure storage-konto som associeras med klustret. |
| `adl://STORENAME/` | Roten på Data Lake Store som används av klustret. Det här schemat kan du komma åt data som finns utanför den katalog som innehåller klustret filsystemet. |

Mer information finns i [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) referens på Apache.org.

### <a name="example-configuration"></a>Exempel på konfiguration

Följande YAML är hämtad från den `resources/writetohdfs.yaml` -fil som ingår i exemplet. Den här filen definierar Storm-topologi med den [som](https://storm.apache.org/releases/1.1.0/flux.html) ramverk för Apache Storm.

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

* `syncPolicy`: Definierar när filer är synkroniserade/rensade till filsystemet. I det här exemplet var 1000 tupplar.
* `fileNameFormat`: Definierar namnmönstret sökvägen och filnamnet ska användas vid skrivning filer. I det här exemplet sökvägen under körning med ett filter, och filnamnstillägget `.txt`.
* `recordFormat`: Definierar de filer som skrivits internt format. I det här exemplet fälten avgränsas med den `|` tecken.
* `rotationPolicy`: Definierar när du roterar filer. I det här exemplet utförs ingen rotation.
* `hdfs-bolt`: Om du använder tidigare komponenter som konfigurationsparametrar för den `HdfsBolt` klass.

Läs mer på framework som [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Konfigurera klustret

Storm på HDInsight innehåller inte de komponenter som HdfsBolt använder för att kommunicera med Azure Storage eller Azure Data Lake Store i Storms klassökvägen som standard. Använd följande skriptåtgärden för att lägga till dessa komponenter i `extlib` katalogen för Storm på klustret:

* Skript-URI: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Noder som gäller för: Nimbus, chef
* Parametrar: ingen

Information om hur du använder det här skriptet med ditt kluster finns i [anpassa HDInsight-kluster med skriptåtgärder](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentet.

## <a name="build-and-package-the-topology"></a>Skapa och paket-topologi

1. Hämta exempelprojekt från [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) i din utvecklingsmiljö.

2. Från en kommandotolk terminal eller skal session, ändra kataloger till roten på det hämta projektet. För att bygga och paketera topologi, använder du följande kommando:
   
        mvn compile package
   
    När det är klar att bygga och paketering, det finns en ny katalog med namnet `target`, som innehåller en fil med namnet `StormToHdfs-1.0-SNAPSHOT.jar`. Den här filen innehåller den kompilerade topologin.

## <a name="deploy-and-run-the-topology"></a>Distribuera och köra topologin

1. Använd följande kommando för att kopiera topologi till HDInsight-klustret. Ersätt **användaren** med SSH-användarnamn som du använde när du skapar klustret. Ersätt **CLUSTERNAME** med namnet på klustret.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    När du uppmanas, anger du det lösenord som används när du skapar SSH-användare för klustret. Om du använder en offentlig nyckel i stället för ett lösenord, kan du behöva använda de `-i` parametern för att ange sökvägen till motsvarande privata nyckel.
   
   > [!NOTE]
   > Mer information om hur du använder `scp` med HDInsight, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. När överföringen är klar kan du använda följande för att ansluta till HDInsight-klustret via SSH. Ersätt **användaren** med SSH-användarnamn som du använde när du skapar klustret. Ersätt **CLUSTERNAME** med namnet på klustret.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    När du uppmanas, anger du det lösenord som används när du skapar SSH-användare för klustret. Om du använder en offentlig nyckel i stället för ett lösenord, kan du behöva använda de `-i` parametern för att ange sökvägen till motsvarande privata nyckel.
   
   Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

3. När du är ansluten, använder du följande kommando för att skapa en fil med namnet `dev.properties`:

        nano dev.properties

4. Använd följande text som innehållet i den `dev.properties` filen:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > Det här exemplet förutsätter att klustret använder ett Azure Storage-konto som standardlagring. Om klustret använder Azure Data Lake Store, Använd `hdfs.url: adl:///` i stället.
    
    Om du vill spara filen, Använd __Ctrl + X__, sedan __Y__, och slutligen __RETUR__. Värdena i den här filen ange Webbadressen för Data Lake store och katalognamnet data skrivs till.

3. Använd följande kommando för att starta topologin:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Detta kommando startar topologin med hjälp av framework som genom att skicka det till Nimbus-nod i klustret. Topologin definieras av den `writetohdfs.yaml` -fil som ingår i jar. Den `dev.properties` filen skickas som ett filter och de värden som finns i filen läses av topologin.

## <a name="view-output-data"></a>Visa utdata

Om du vill visa data använder du följande kommando:

    hdfs dfs -ls /stormdata/

En lista över filer som skapas av den här topologin visas.

I följande lista är ett exempel på data retuned av de föregående kommandona:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Stoppa topologin

Storm-topologier kör förrän stoppats eller ta bort klustret. Om du vill stoppa topologin, använder du följande kommando:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Storm för att skriva till Azure Storage- och Azure Data Lake Store kan identifiera andra [Storm-exempel för HDInsight](apache-storm-example-topology.md).

