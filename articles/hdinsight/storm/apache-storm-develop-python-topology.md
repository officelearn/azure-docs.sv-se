---
title: Apache Storm med Python-komponenter - Azure HDInsight
description: Lär dig hur du skapar en Apache Storm-topologi som använder Python-komponenter i Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460032"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Utveckla Apache Storm-topologier med Python på HDInsight

Lär dig hur du skapar en [Apache Storm-topologi](https://storm.apache.org/) som använder Python-komponenter. Apache Storm stöder flera språk, även så att du kan kombinera komponenter från flera språk i en topologi. [Flux-ramverket](https://storm.apache.org/releases/current/flux.html) (som introducerades med Storm 0.10.0) gör att du enkelt kan skapa lösningar som använder Python-komponenter.

> [!IMPORTANT]  
> Informationen i det här dokumentet testades med Storm på HDInsight 3.6.

## <a name="prerequisites"></a>Krav

* Ett Apache Storm-kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **klustertyp**.

* En lokal stormutvecklingsmiljö (valfritt). En lokal Storm-miljö behövs bara om du vill köra topologin lokalt. Mer information finns i [Konfigurera en utvecklingsmiljö](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2.7 eller högre](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerad](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projektbyggsystem för Java-projekt.

## <a name="storm-multi-language-support"></a>Stöd för flera språk för storm

Apache Storm har utformats för att fungera med komponenter skrivna med valfritt programmeringsspråk. Komponenterna måste förstå hur du arbetar med sparsamhetsdefinitionen för Storm. För Python tillhandahålls en modul som en del av Apache Storm-projektet som gör att du enkelt kan samverka med Storm. Du hittar den [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)här modulen på .

Storm är en Java-process som körs på Java Virtual Machine (JVM). Komponenter skrivna på andra språk körs som underprocesser. Stormen kommunicerar med dessa underprocesser med JSON-meddelanden som skickas över stdin/stdout. Mer information om kommunikation mellan komponenter finns i [multi-lang-protokollets](https://storm.apache.org/releases/current/Multilang-protocol.html) dokumentation.

## <a name="python-with-the-flux-framework"></a>Python med Flux-ramverket

Flux-ramverket gör att du kan definiera Stormtopologier separat från komponenterna. Flux-ramverket använder YAML för att definiera Storm-topologin. Följande text är ett exempel på hur du refererar till en Python-komponent i YAML-dokumentet:

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

Klassen `FluxShellSpout` används för att `sentencespout.py` starta skriptet som implementerar pipen.

Flux förväntar sig att Python-skripten ska finnas i katalogen `/resources` i jar-filen som innehåller topologin. Så det här exemplet lagrar `/multilang/resources` Python-skripten i katalogen. Den `pom.xml` här filen innehåller följande XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Som tidigare nämnts finns `storm.py` det en fil som implementerar sparsamhetsdefinitionen för Storm. Flux-ramverket `storm.py` innehåller automatiskt när projektet byggs, så du behöver inte oroa dig för att inkludera det.

## <a name="build-the-project"></a>Bygga projektet

1. Ladda ner [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)projektet från .

1. Öppna en kommandotolk och navigera `hdinsight-python-storm-wordcount-master`till projektroten: . Ange följande kommando:

    ```cmd
    mvn clean compile package
    ```

    Med det här `target/WordCount-1.0-SNAPSHOT.jar` kommandot skapas en fil som innehåller den kompilerade topologin.

## <a name="run-the-storm-topology-on-hdinsight"></a>Kör Storm-topologin på HDInsight

1. Använd [kommandot ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) `WordCount-1.0-SNAPSHOT.jar` för att kopiera filen till stormen på HDInsight-klustret. Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. När filen har laddats upp ansluter du till klustret med SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från SSH-sessionen använder du följande kommando för att starta topologin i klustret:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    En gång startat, en Storm topologi löper tills stoppas.

1. Använd användargränssnittet Storm för att visa topologin i klustret. Storm-användargränssnittet finns `https://CLUSTERNAME.azurehdinsight.net/stormui`på . Ersätt `CLUSTERNAME` med klusternamnet.

1. Stoppa Storm-ursäkten. Använd följande kommando för att stoppa topologin i klustret:

    ```bash
    storm kill wordcount
    ```

    Du kan också använda användargränssnittet Storm. Under **Topologi åtgärder** för topologin väljer du **Döda**.

## <a name="run-the-topology-locally"></a>Kör topologin lokalt

Om du vill köra topologin lokalt använder du följande kommando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Det här kommandot kräver en lokal stormutvecklingsmiljö. Mer information finns i [Konfigurera en utvecklingsmiljö](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

När topologin startar avger den information till den lokala konsolen som liknar följande text:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Om du vill stoppa topologin använder du __Ctrl + C__.

## <a name="next-steps"></a>Nästa steg

Se följande dokument för andra sätt att använda Python med HDInsight: [Så här använder du UDF (Python User Defined Functions) i Apache Pig och Apache Hive](../hadoop/python-udf-hdinsight.md).
