---
title: Apache Storm med Python - komponenter i Azure HDInsight
description: Lär dig hur du skapar ett Apache Storm-topologi som använder Python-komponenter.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache storm python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: a5cbd54dd07143688b676c063133bb1a73bed01a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694395"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Utveckla Apache Storm-topologier med Python på HDInsight

Lär dig hur du skapar en [Apache Storm](https://storm.apache.org/) topologi som använder Python-komponenter. Apache Storm har stöd för flera språk, även så att du kan kombinera komponenter på flera språk i en topologi. Den [som](https://storm.apache.org/releases/current/flux.html) framework (presenteras med Storm 0.10.0) kan du enkelt kan skapa lösningar som använder Python-komponenter.

> [!IMPORTANT]  
> Informationen i det här dokumentet har testats med Storm på HDInsight 3.6. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Koden för det här projektet är tillgänglig på [ https://github.com/Azure-Samples/hdinsight-python-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Python 2.7 eller senare

* Java JDK 1.8 eller senare

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* (Valfritt) En lokal utvecklingsmiljö för Storm. En lokal miljö för Storm krävs endast om du vill köra topologin lokalt. Mer information finns i [konfigurera en utvecklingsmiljö](https://storm.apache.org/releases/1.1.2/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Stöd för flera språk för storm

Apache Storm har utformats för att arbeta med komponenter som skrivits med alla programmeringsspråk. Komponenterna måste förstå hur du arbetar med den [Thrift-definition för Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). För Python tillhandahåller en modul som en del av Apache Storm-projekt där du kan enkelt gränssnitt med Storm. Du hittar den här modulen på [ https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py ](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm är en Java-process som körs på Java Virtual Machine (JVM). Komponenter som skrivits på andra språk körs som delprocesser. Storm kommunicerar med dessa delprocesser med hjälp av JSON-meddelanden som skickas över stdin/stdout. Mer information om kommunikation mellan komponenter finns i den [Multi-lang protokollet](https://storm.apache.org/documentation/Multilang-protocol.html) dokumentation.

## <a name="python-with-the-flux-framework"></a>Python med som framework

Ramverket som kan du definiera Storm-topologier separat från komponenterna. Ramverket som använder YAML för att definiera Storm-topologi. Följande text är ett exempel på hur du kan referera till en Python-komponent i YAML-dokumentet:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

Klassen `FluxShellSpout` används för att starta den `sentencespout.py` skript som implementerar kanal.

Som förväntar sig Python-skript i den `/resources` katalogen i den jar-filen som innehåller topologin. Så det här exemplet lagrar Python-skript i den `/multilang/resources` directory. Den `pom.xml` innehåller den här filen med följande XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Som tidigare nämnts är det en `storm.py` fil som implementerar Thrift-definition för Storm. Ramverket som innehåller `storm.py` automatiskt när projektet har skapats, så att du inte behöver bekymra dig om att inkludera den.

## <a name="build-the-project"></a>Bygga projektet

Använd följande kommando från roten av projektet:

```bash
mvn clean compile package
```

Det här kommandot skapar en `target/WordCount-1.0-SNAPSHOT.jar` -fil som innehåller den kompilerade topologin.

## <a name="run-the-topology-locally"></a>Kör topologin lokalt

Om du vill köra topologin lokalt använder du följande kommando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Det här kommandot kräver en lokal utvecklingsmiljö för Storm. Mer information finns i [konfigurera en utvecklingsmiljö](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

När topologi-startar den skickar information till den lokala konsolen som liknar följande text:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Stoppa topologin genom att använda __Ctrl + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Kör topologi för Storm på HDInsight

1. Använd följande kommando för att kopiera den `WordCount-1.0-SNAPSHOT.jar` filen till ditt Storm på HDInsight-kluster:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Ersätt `sshuser` med SSH-användare för klustret. Ersätt `mycluster` med klustrets namn. Du kan uppmanas att ange lösenordet för SSH-användaren.

    Läs mer om hur du använder SSH och SCP [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. När filen har överförts, ansluta till klustret med SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Från SSH-sessionen använder du följande kommando för att starta topologin i klustret:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Du kan använda Storm-Användargränssnittet för att visa topologi i klustret. Storm-Användargränssnittet finns på https://mycluster.azurehdinsight.net/stormui. Ersätt `mycluster` med klusternamnet på ditt.

> [!NOTE]  
> När startats körs en Storm-topologi tills de stoppas. Stoppa topologin genom att använda någon av följande metoder:
>
> * Den `storm kill TOPOLOGYNAME` från kommandoraden
> * Den **Kill** knappen i Storm-Användargränssnittet.


## <a name="next-steps"></a>Nästa steg

Se följande dokument för andra sätt att använda Python med HDInsight:

* [Hur du använder Python användaren användardefinierade funktioner (UDF) i Apache Pig- och Apache Hive](../hadoop/python-udf-hdinsight.md)
