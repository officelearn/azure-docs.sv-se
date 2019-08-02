---
title: Apache Storm med python-komponenter – Azure HDInsight
description: Lär dig hur du skapar en Apache Storm-topologi som använder python-komponenter.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache Storm python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: b9faf33734ba17e9912246fe9c5c2ac45c55ba44
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598470"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Utveckla Apache Storm topologier med python i HDInsight

Lär dig hur du skapar en [Apache Storm](https://storm.apache.org/) -topologi som använder python-komponenter. Apache Storm stöder flera språk, även för att du ska kunna kombinera komponenter från flera språk i en topologi. [Flödes](https://storm.apache.org/releases/current/flux.html) ramverket (lanserades med storm 0.10.0) gör att du enkelt kan skapa lösningar som använder python-komponenter.

> [!IMPORTANT]  
> Informationen i det här dokumentet har testats med storm på HDInsight 3,6. 

Koden för det här projektet är tillgänglig på [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Förutsättningar

* Python 2,7 eller högre

* Java JDK 1,8 eller högre

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* Valfritt En lokal Storm utvecklings miljö. En lokal Storm-miljö behövs bara om du vill köra topologin lokalt. Mer information finns i [Konfigurera en utvecklings miljö](http://storm.apache.org/releases/current/Setting-up-development-environment.html).

## <a name="storm-multi-language-support"></a>Storm stöd för flera språk

Apache Storm har utformats för att fungera med komponenter som skrivits med valfritt programmeringsspråk. Komponenterna måste förstå hur man arbetar med Thrift- [definitionen för Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). För python tillhandahålls en modul som en del av Apache Storm projektet som gör det enkelt att använda storm. Du hittar den här modulen på [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm är en Java-process som körs på Java Virtual Machine (JVM). Komponenter som skrivs på andra språk körs som under processer. Storm kommunicerar med dessa under processer med hjälp av JSON-meddelanden som skickats över STDIN/STDOUT. Mer information om kommunikation mellan komponenter finns i dokumentationen för [multi-lang-protokollet](https://storm.apache.org/documentation/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python med flödes ramverket

Med flödes ramverket kan du definiera Storm-topologier separat från komponenterna. Flödes ramverket använder YAML för att definiera Storm-topologin. Följande text är ett exempel på hur du refererar till en python-komponent i YAML-dokumentet:

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

Klassen `FluxShellSpout` används för att `sentencespout.py` starta skriptet som implementerar kanalen.

Sänd flödet förväntar sig att python-skripten ska finnas i `/resources` katalogen i JAR-filen som innehåller topologin. I det här exemplet lagras python-skripten `/multilang/resources` i katalogen. `pom.xml` Innehåller den här filen med följande XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Som tidigare nämnts finns det en `storm.py` fil som implementerar Thrift-definitionen för storm. Flödes ramverket `storm.py` innehåller automatiskt när projektet skapas, så du behöver inte oroa dig för att inkludera det.

## <a name="build-the-project"></a>Bygga projektet

Använd följande kommando från projektets rot:

```bash
mvn clean compile package
```

Det här kommandot skapar `target/WordCount-1.0-SNAPSHOT.jar` en fil som innehåller den kompilerade topologin.

## <a name="run-the-topology-locally"></a>Kör topologin lokalt

Använd följande kommando för att köra topologin lokalt:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Det här kommandot kräver en lokal Storm utvecklings miljö. Mer information finns i [Konfigurera en utvecklings miljö](https://storm.apache.org/releases/current/Setting-up-development-environment.html)

När topologin startar avger den information till den lokala konsolen som liknar följande text:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Om du vill stoppa topologin använder du __CTRL + C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Kör Storm-topologin på HDInsight

1. Använd följande kommando för att kopiera `WordCount-1.0-SNAPSHOT.jar` filen till din storm på HDInsight-kluster:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Ersätt `sshuser` med SSH-användaren för klustret. Ersätt `mycluster` med kluster namnet. Du kan uppmanas att ange lösen ordet för SSH-användaren.

    Mer information om hur du använder SSH och SCP finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. När filen har laddats upp ansluter du till klustret med hjälp av SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Från SSH-sessionen använder du följande kommando för att starta topologin i klustret:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Du kan använda Storm-ANVÄNDARGRÄNSSNITTET för att Visa topologin i klustret. Storm-ANVÄNDARGRÄNSSNITTET finns på https://mycluster.azurehdinsight.net/stormui. Ersätt `mycluster` med ditt kluster namn.

> [!NOTE]  
> När den har startats körs en Storm-topologi tills den har stoppats. Använd någon av följande metoder för att stoppa topologin:
>
> * `storm kill TOPOLOGYNAME` Kommandot från kommando raden
> * **Kill** -knappen i storm-användargränssnittet.


## <a name="next-steps"></a>Nästa steg

Se följande dokument för andra sätt att använda python med HDInsight:

* [Använda python-användardefinierade funktioner (UDF) i Apache gris och Apache Hive](../hadoop/python-udf-hdinsight.md)
