---
title: Apache Storm-exempel Java-topologi - Azure HDInsight
description: Lär dig mer om att skapa Apache Storm-topologier i Java genom att skapa en exempel-ordräkningstopologi.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache storm, apache storm-exempel, storm java, storm-topologi-exempel
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 5f07f462fc33761f7d29944594491a72f283cd31
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582572"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Skapa ett Apache Storm-topologi i Java

Lär dig hur du skapar en Java-baserad topologi för [Apache Storm](http://storm.apache.org/). Du skapar en Storm-topologi som implementerar ett word-count-program. Du använder [Apache Maven](https://maven.apache.org/) att bygga och paketera projektet. Sedan får du lära dig hur du definierar topologin med hjälp av som framework.

När du har slutfört stegen i det här dokumentet, kan du distribuera topologin till Apache Storm på HDInsight.

> [!NOTE]
> En fullständig version av Storm-topologi exemplen som skapas i det här dokumentet finns på [ https://github.com/Azure-Samples/hdinsight-java-storm-wordcount ](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

## <a name="prerequisites"></a>Förutsättningar

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks)

* [Apache Maven (https://maven.apache.org/download.cgi)](https://maven.apache.org/download.cgi): Maven är ett projekt build-system för Java-projekt.

* En textredigerare eller IDE.

## <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Följande miljövariabler kan anges när du installerar Java och JDK-Paketet. Du bör dock kontrollera att de finns och att de innehåller rätt värden för ditt system.

* **JAVA_HOME** -måste peka på den katalog där med Java runtime environment (JRE) har installerats. Till exempel en Unix- eller Linux-distribution, den inte innehålla ett värde som liknar `/usr/lib/jvm/java-8-oracle`. I Windows, skulle den ha ett värde som liknar `c:\Program Files (x86)\Java\jre1.8`

* **SÖKVÄGEN** -bör innehålla följande sökvägar:

  * **JAVA_HOME** (eller motsvarande sökväg)

  * **JAVA_HOME\bin** (eller motsvarande sökväg)

  * Den katalog där Maven har installerats

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

Från kommandoraden använder du följande kommando för att skapa ett Maven-projekt med namnet **WordCount**:

```bash
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false
```

> [!NOTE]
> Om du använder PowerShell måste du omge det`-D` parametrar med dubbla citattecken.
>
> `mvn archetype:generate "-DarchetypeArtifactId=maven-archetype-quickstart" "-DgroupId=com.microsoft.example" "-DartifactId=WordCount" "-DinteractiveMode=false"`

Det här kommandot skapar en katalog med namnet `WordCount` på den aktuella platsen som innehåller ett grundläggande Maven-projekt. Den `WordCount` katalogen innehåller följande objekt:

* `pom.xml`: Innehåller inställningar för Maven-projekt.
* `src\main\java\com\microsoft\example`: Innehåller din programkod.
* `src\test\java\com\microsoft\example`: Innehåller tester för ditt program. 

### <a name="remove-the-generated-example-code"></a>Ta bort den genererade exempelkoden

Ta bort det genererade testet- och programfilerna:

* **src\test\java\com\microsoft\example\AppTest.java**
* **src\main\java\com\microsoft\example\App.java**

## <a name="add-maven-repositories"></a>Lägg till Maven-databaser

HDInsight bygger på Hortonworks Data Platform (HDP) och vi rekommenderar att du använder Hortonworks-databasen för att hämta beroenden för Apache Storm-projekt. I den __pom.xml__ fil, Lägg till följande XML-filen när den `<url> http://maven.apache.org</url>` rad:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>http://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>http://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Lägg till egenskaper

Maven kan du definiera projekt-värdena kallas egenskaper. I den __pom.xml__, Lägg till följande text efter den `</repositories>` rad:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Du kan nu använda det här värdet i andra avsnitt i den `pom.xml`. Till exempel när du anger versionen av Storm-komponenter, du kan använda `${storm.version}` i stället för att hårdkoda ett värde.

## <a name="add-dependencies"></a>Lägga till beroenden

Lägg till ett beroende för Storm-komponenter. Öppna den `pom.xml` filen och Lägg till följande kod i den `<dependencies>` avsnittet:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Vid kompilering, Maven använder denna information för att leta upp `storm-core` i Maven-centrallagret. Det verkar först i databasen på den lokala datorn. Om filerna inte är det Maven hämtar dem från den offentliga Maven-databasen och lagrar dem i den lokala lagringsplatsen.

> [!NOTE]
> Observera den `<scope>provided</scope>` raden i det här avsnittet. Den här inställningen instruerar Maven att undanta **storm kärnor** från valfri JAR-fil som skapas, eftersom den tillhandahålls av systemet.

## <a name="build-configuration"></a>Skapa konfiguration

Maven-plugin-program kan du anpassa build-faser av projektet. Till exempel hur kompileras projektet eller hur du paketera den i en JAR-fil. Öppna den `pom.xml` filen och Lägg till följande kod direkt ovan den `</project>` rad.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Det här avsnittet används för att lägga till plugin-program, resurser och andra build-konfigurationsalternativ. Fullständiga referenser av den **pom.xml** fil, se [ http://maven.apache.org/pom.html ](http://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Lägga till plugin-program

För Apache Storm-topologier som implementerats i Java, den [Exec Maven-pluginprogrammet](http://www.mojohaus.org/exec-maven-plugin/) är användbart eftersom det kan du enkelt köra topologin lokalt i din utvecklingsmiljö. Lägg till följande till den `<plugins>` delen av den `pom.xml` filen för att inkludera Exec Maven-pluginprogrammet:

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.5.0</version>
    <executions>
        <execution>
        <goals>
            <goal>exec</goal>
        </goals>
        </execution>
    </executions>
    <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
        <cleanupDaemonThreads>false</cleanupDaemonThreads> 
    </configuration>
</plugin>
```

En annan användbar plugin-programmet är den [plugin-programmet för Apache Maven-kompilatorn](http://maven.apache.org/plugins/maven-compiler-plugin/), som används för att ändra alternativ för kompilering. Ändringarna Java-version som Maven använder för källa och mål för ditt program.

* För HDInsight __3.4 eller tidigare__, ange källa och mål Java-versionen till __1.7__.

* För HDInsight __3.5__, ange källa och mål Java-versionen till __1.8__.

Lägg till följande text i den `<plugins>` delen av den `pom.xml` filen för att inkludera Apache Maven-kompilatorn plugin-programmet. Det här exemplet anger 1.8, så att HDInsight målversionen är 3.5.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.3</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

### <a name="configure-resources"></a>Konfigurera resurser

Resursavsnittet kan du inkludera icke kodbaserat resurser, till exempel configuration-filer som krävs av komponenterna i topologin. Lägg till följande text i det här exemplet den `<resources>` delen av den ”pom.xml-filen.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

Det här exemplet lägger till resurser som katalogen i roten av projektet (`${basedir}`) som en plats som innehåller resurser, och som innehåller filen `log4j2.xml`. Den här filen används för att konfigurera vilken information som loggas av topologin.

## <a name="create-the-topology"></a>Skapa topologin

En Java-baserade Apache Storm-topologi som består av tre komponenter som du måste skapa (eller referens) som ett beroende.

* **Spouts**: läser data från externa datakällor och genererar dataströmmar till topologin.

* **Bolts**: utför bearbetning på strömmar som av kanaler eller andra bultar och genererar en eller flera strömmar.

* **Topologi**: definierar hur kanaler och bultar ordnas och ger en startpunkt för topologin.

### <a name="create-the-spout"></a>Skapa kanal

För att minska kraven för att konfigurera externa datakällor, genererar följande spout bara meningar slumpmässigt. Det är en modifierad version av en kanal som medföljer den [Storm-starterexempel](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [!NOTE]
> Ett exempel på en kanal som läser från en extern datakälla finns i följande exempel:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): en exempel-kanal som läser från Twitter
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): en kanal som läser från Kafka

Skapa en fil med namnet för spout `RandomSentenceSpout.java` i den `src\main\java\com\microsoft\example` katalogen och Använd följande Java-kod som innehåll:

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]
> Även om den här topologin använder bara en kanal kan kan andra ha flera som mata in data från olika källor i topologin.

### <a name="create-the-bolts"></a>Skapa bultarna

Bultar hantera databearbetningen. Den här topologin använder två bultar:

* **SplitSentence**: delar upp meningar som orsakats av **RandomSentenceSpout** till enskilda ord.

* **WordCount**: räknar hur många gånger varje ord har inträffat.

> [!NOTE]
> Bultar kan göra något, till exempel beräkning, persistence eller prata externa komponenter.

Skapa två nya filer, `SplitSentence.java` och `WordCount.java` i den `src\main\java\com\microsoft\example` directory. Använd följande text som innehållet för filer:

#### <a name="splitsentence"></a>SplitSentence

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Definiera topologin

Topologin binda spouts och bolts tillsammans i ett diagram som definierar hur data flödar mellan komponenterna. Det ger också parallellitet tips som Storm använder när du skapar instanser av komponenter i klustret.

Följande bild är ett grundläggande diagram i diagrammet med komponenter för den här topologin.

![diagram som visar hur kanaler och bultar](./media/apache-storm-develop-java-topology/wordcount-topology.png)

Skapa en fil med namnet om du vill implementera topologin `WordCountTopology.java` i den `src\main\java\com\microsoft\example` directory. Använd följande kod för Java som innehållet i filen:

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Konfigurera loggning

Storm använder [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) att logga information. Om du inte konfigurerar loggning genererar topologin diagnostisk information. Skapa en fil med namnet för att styra vad loggas `log4j2.xml` i den `resources` directory. Använd följande XML som innehållet i filen.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

Den här XML konfigurerar en ny loggare för den `com.microsoft.example` klass som innehåller komponenter i den här exempel-topologin. Nivån har angetts att spåra för den här loggaren som samlar in alla loggningsinformation som orsakats av komponenter i den här topologin.

Den `<Root level="error">` avsnittet konfigurerar loggning rotnivå (allt inte i `com.microsoft.example`) att endast logga information om felet.

Läs mer om hur du konfigurerar loggning för Log4j 2 [ http://logging.apache.org/log4j/2.x/manual/configuration.html ](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]
> Storm-version 0.10.0 och högre användning Log4j 2.x. Används för äldre versioner av storm Log4j 1.x, som används av ett annat format för konfigurationen av loggen. Information om konfigurationen av äldre finns [ http://wiki.apache.org/logging-log4j/Log4jXmlFormat ](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

## <a name="test-the-topology-locally"></a>Testa topologin lokalt

När du har sparat filer kan du använda kommandona nedan för att testa topologin lokalt.

```bash
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

När den körs, visar topologin startinformation. Följande text är ett exempel på utdata som antal ord:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Den här exempel-loggen indikerar att ordet ”och” släpptes 113 gånger. Antalet fortsätter att öka så länge topologin körs eftersom spout kontinuerligt genererar samma meningarna.

Det finns ett 5-sekundersintervall intervall mellan utsläpp av ord och antal. Den **WordCount** -komponenten har konfigurerats för att generera information endast när en tidstuppel tas emot. Begär att skalstreck tupplar levereras endast var femte sekund.

## <a name="convert-the-topology-to-flux"></a>Konvertera topologin till som

[Som](http://storm.apache.org/releases/2.0.0-SNAPSHOT/flux.html) är ett nytt ramverk tillgänglig med Storm 0.10.0 och högre, där du kan avgränsa konfigurationen från implementering. Dina komponenter definieras fortfarande i Java, men topologin definieras med hjälp av en YAML-fil. Du kan paketera en topologi standarddefinition med ditt projekt eller använda en fristående fil när du skickar in topologin. När du skickar in topologin till Storm, kan du använda miljövariabler eller konfigurationsfiler för att fylla i värden i topologidefinitionen YAML.

YAML-fil som definierar vilka komponenter som ska användas för topologin och data kan flöda mellan dem. Du kan inkludera en YAML-fil som en del av jar-filen eller du kan använda en extern YAML-fil.

Mer information om som finns i [som framework (https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html).

> [!WARNING]
> På grund av en [bugg (https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) med Storm 1.0.1, kan du behöva installera en [Storm utvecklingsmiljö](http://storm.apache.org/releases/current/Setting-up-development-environment.html) att köra som topologier lokalt.

1. Flytta den `WordCountTopology.java` filen utanför projektet. Tidigare den här filen definierats topologin, men behövs inte med som.

2. I den `resources` katalogen, skapa en fil med namnet `topology.yaml`. Använd följande text som innehållet i den här filen.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. Gör följande ändringar till den `pom.xml` filen.
   
   * Lägg till följande nya beroende på den `<dependencies>` avsnittet:
     
        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```
   * Lägg till följande plugin-programmet till den `<plugins>` avsnittet. Det här plugin-programmet hanterar skapandet av ett paket (jar-fil) för projektet och gäller vissa transformationer som är specifika för som när du skapar paketet.
     
        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   * I den **exec-maven-plugin-programmet** `<configuration>` ändrar värdet för `<mainClass>` till `org.apache.storm.flux.Flux`. Den här inställningen som ska hanteras kör topologin lokalt under utveckling.

   * I den `<resources>` lägger du till följande för att den `<includes>`. Den här XML-filen innehåller YAML-fil som definierar topologin som en del av projektet.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testa topologin som lokalt

1. Använd följande för att kompilera och kör som-topologi med Maven:

    ```bash
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    Om du använder PowerShell använder du följande kommando:

    ```bash
    mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"
    ```

    > [!WARNING]
    > Om topologin använder Storm 1.0.1 bits, misslyckas kommandot. Det här felet beror på [ https://issues.apache.org/jira/browse/STORM-2055 ](https://issues.apache.org/jira/browse/STORM-2055). I stället [installera Storm i utvecklingsmiljön](http://storm.apache.org/releases/current/Setting-up-development-environment.html) och Använd följande steg:
    >
    > Om du har [installerat Storm i utvecklingsmiljön](http://storm.apache.org/releases/current/Setting-up-development-environment.html), du kan använda följande kommandon i stället:
    >
    > ```bash
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    Den `--local` parametern körs topologin i lokalt läge på din utvecklingsmiljö. Den `-R /topology.yaml` parametern använder den `topology.yaml` filen resursen från jar-filen för att definiera topologin.

    När den körs, visar topologin startinformation. Följande text är ett exempel på utdata:

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    Det finns en fördröjning på 10 sekunder mellan batchar av information som loggas.

2. Skapa en kopia av den `topology.yaml` filen från projektet. Ge den nya filen namnet `newtopology.yaml`. I den `newtopology.yaml` filen, leta upp följande avsnitt och ändra värdet för `10` till `5`. Den här ändringen ändrar intervallet mellan sändande batchar av word antalen från 10 sekunder till 5.

    ```yaml
    - id: "counter-bolt"
    className: "com.microsoft.example.WordCount"
    constructorArgs:
    - 5
    parallelism: 1
    ```yaml

3. To run the topology, use the following command:

    ```bash
    mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"
    ```

    Eller, om du har Storm i utvecklingsmiljön:

    ```bash
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml
    ```

    Ändra den `/path/to/newtopology.yaml` sökvägen till filen newtopology.yaml som du skapade i föregående steg. Det här kommandot använder newtopology.yaml som definitionen för nätverkstopologin. Eftersom vi skickat den `compile` parametern Maven använder en version av att projektet har skapats i föregående steg.

    När topologin startar, bör du märka att tiden mellan utgivna batchar har ändrats för att återspegla värdet i newtopology.yaml. Så att du kan se att du kan ändra din konfiguration via en YAML-fil utan att kompilera om topologin.

Mer information om dessa och andra funktioner i ramen som finns i [som (http://storm.apache.org/releases/current/flux.html)](http://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](http://storm.apache.org/releases/current/Trident-API-Overview.html) är en abstraktion på hög nivå som tillhandahålls av Storm. Det stöder tillståndskänsliga bearbetning. Viktig fördel med Trident är att det garanterar att varje meddelande som anger topologin bearbetas bara en gång. Utan att använda Trident garanterar topologin endast att meddelanden bearbetas minst en gång. Det finns även andra skillnader, till exempel inbyggda komponenter som kan användas istället för att skapa bultar. I själva verket ersätts bultar med mindre-generisk komponenter, till exempel filter, projektioner och funktioner.

Trident program kan skapas med hjälp av Maven-projekt. Du använder samma grundläggande steg som visas tidigare i den här artikeln – endast koden är olika. Heller Trident kan inte (för närvarande) användas som Framework.

Mer information om Trident finns i den [Trident API-översikt](http://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du skapar ett Apache Storm-topologi med hjälp av Java. Nu lära dig hur du:

* [Distribuera och hantera Apache Storm-topologier i HDInsight](apache-storm-deploy-monitor-topology.md)

* [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Du hittar flera exempel Apache Storm-topologier genom att besöka [exempeltopologier för Apache Storm på HDInsight](apache-storm-example-topology.md).

