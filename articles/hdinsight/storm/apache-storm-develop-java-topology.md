---
title: Apache Storm exempel Java-topologi - Azure HDInsight
description: Lär dig hur du skapar Apache Storm-topologier i Java genom att skapa ett exempel på tonologiska ordräkningstopologi.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 75100b47ddf8f36ed9a22ff3073c439f8ad9040b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083296"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Skapa en Apache Storm-topologi i Java

Lär dig hur du skapar en Java-baserad topologi för [Apache Storm](https://storm.apache.org/). Här skapar du en Storm-topologi som implementerar ett ordräkningsprogram. Du använder [Apache Maven](https://maven.apache.org/) för att bygga och paketera projektet. Sedan får du lära dig att definiera topologin med hjälp av [Apache Storm Flux-ramverket.](https://storm.apache.org/releases/2.0.0/flux.html)

När du har slutfört stegen i det här dokumentet kan du distribuera topologin till Apache Storm på HDInsight.

> [!NOTE]  
> En slutförd version av stormtopologiexemplen som [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)skapats i det här dokumentet finns på .

## <a name="prerequisites"></a>Krav

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerad](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projektbyggsystem för Java-projekt.

## <a name="test-environment"></a>Testmiljö

Miljön som användes för den här artikeln var en dator som kör Windows 10.  Kommandona utfördes i en kommandotolk och de olika filerna redigerades med Anteckningar.

Från en kommandotolk anger du kommandona nedan för att skapa en arbetsmiljö:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

Ange följande kommando för att skapa ett Maven-projekt med namnet **WordCount:**

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Det här kommandot skapar `WordCount` en katalog som namnges på den aktuella platsen, som innehåller ett grundläggande Maven-projekt. Det andra kommandot ändrar `WordCount`den aktuella arbetskatalogen till . Det tredje kommandot skapar en `resources`ny katalog, som kommer att användas senare.  Katalogen `WordCount` innehåller följande objekt:

* `pom.xml`: Innehåller inställningar för Maven-projektet.
* `src\main\java\com\microsoft\example`: Innehåller din programkod.
* `src\test\java\com\microsoft\example`: Innehåller tester för ditt program.  

### <a name="remove-the-generated-example-code"></a>Ta bort den genererade exempelkoden

Ta bort de genererade `AppTest.java`test- och programfilerna och `App.java` genom att ange kommandona nedan:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Lägg till Maven-databaser

HDInsight är baserat på Hortonworks Data Platform (HDP), så vi rekommenderar att du använder Hortonworks-arkivet för att hämta beroenden för dina Apache Storm-projekt.  

Öppna `pom.xml` genom att ange kommandot nedan:

```cmd
notepad pom.xml
```

Lägg sedan till följande `<url>https://maven.apache.org</url>` XML efter raden:

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
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
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
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Lägg till egenskaper

Med Maven kan du definiera värden på projektnivå som kallas egenskaper. Lägg `pom.xml`till följande text `</repositories>` efter raden i:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Du kan nu använda det här `pom.xml`värdet i andra avsnitt i . När du till exempel anger versionen av Storm-komponenter kan du använda `${storm.version}` i stället för hårdkodning av ett värde.

## <a name="add-dependencies"></a>Lägga till beroenden

Lägg till ett beroende för Storm-komponenter. Lägg `pom.xml`till följande text `<dependencies>` i avsnittet i:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Vid kompilering använder Maven den `storm-core` här informationen för att slå upp i Maven-databasen. Den tittar först i databasen på den lokala datorn. Om filerna inte finns där hämtar Maven dem från den offentliga Maven-databasen och lagrar dem i den lokala databasen.

> [!NOTE]  
> Lägg `<scope>provided</scope>` märke till raden i det här avsnittet. Den här inställningen talar om för Maven att utesluta stormkärnor från alla **JAR-filer** som skapas, eftersom det tillhandahålls av systemet.

## <a name="build-configuration"></a>Skapa konfiguration

Maven plug-ins kan du anpassa bygga stadier av projektet. Till exempel hur projektet kompileras eller hur du paketerar det i en JAR-fil. Lägg `pom.xml`till följande text direkt `</project>` ovanför raden i .

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Det här avsnittet används för att lägga till plugin-program, resurser och andra konfigurationsalternativ för versioner. En fullständig referens `pom.xml` av filen [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html)finns i .

### <a name="add-plug-ins"></a>Lägga till plugin-program

* **Exec Maven Plugin**

    För Apache Storm topologier genomförs i Java, [exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/) är användbart eftersom det låter dig enkelt köra topologi lokalt i din utvecklingsmiljö. Lägg till följande `<plugins>` i `pom.xml` avsnittet av filen för att inkludera Exec Maven plugin:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
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

* **Apache Maven Kompilator Plugin**

    En annan användbar plug-in är [Apache Maven Kompilator Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/), som används för att ändra alternativ sammanställning. Ändra javaversionen som Maven använder för källan och målet för ditt program.

  * För HDInsight __3.4 eller tidigare__anger du källan och rikta in Java-versionen på __1,7__.

  * För HDInsight __3.5__ställer du in källan och målet Java-versionen till __1,8__.

  Lägg till följande `<plugins>` text i `pom.xml` avsnittet av filen för att inkludera Apache Maven Kompilator plugin. Det här exemplet anger 1,8, så målet HDInsight-versionen är 3,5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Konfigurera resurser

Med resursavsnittet kan du inkludera icke-kodresurser, till exempel konfigurationsfiler som behövs av komponenter i topologin. I det här exemplet lägger `<resources>` du `pom.xml` till följande text i avsnittet i filen. Spara sedan och stäng filen.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

I det här exemplet läggs resurskatalogen`${basedir}`till i roten på projektet ( `log4j2.xml`) som en plats som innehåller resurser och filen med namnet . Den här filen används för att konfigurera vilken information som loggas av topologin.

## <a name="create-the-topology"></a>Skapa topologin

En Java-baserad Apache Storm-topologi består av tre komponenter som du måste skapa (eller referera till) som ett beroende.

* **Pipar**: Läser data från externa källor och avger dataströmmar i topologin.

* **Bultar:** Utför bearbetning på strömmar som avges av pipar eller andra bultar och avger en eller flera strömmar.

* **Topologi**: Definierar hur pipar och bultar är ordnade och tillhandahåller startpunkten för topologin.

### <a name="create-the-spout"></a>Skapa pipen

För att minska kraven för att ställa in externa datakällor avger följande pip helt enkelt slumpmässiga meningar. Det är en modifierad version av en pip som medföljer [Storm-Starter exempel](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Även om den här topologin bara använder en pip kan andra ha flera som matar in data från olika källor i topologin.

Ange kommandot nedan för att skapa `RandomSentenceSpout.java`och öppna en ny fil:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Kopiera och klistra in java-koden nedan i den nya filen.  Stäng sedan filen.

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
> Ett exempel på en pip som läser från en extern datakälla finns i något av följande exempel:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Ett exempel pip som läser från Twitter.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): En pip som läser från Kafka.

### <a name="create-the-bolts"></a>Skapa bultarna

Bultarna hanterar databehandlingen. Bultar kan göra vad som helst, till exempel beräkning, uthållighet eller prata med externa komponenter. Denna topologi använder två bultar:

* **SplitSentence**: Delar meningarna som avges av **RandomSentenceSpout** i enskilda ord.

* **WordCount**: Räknar hur många gånger varje ord har inträffat.

#### <a name="splitsentence"></a>SplitSentence

Ange kommandot nedan för att skapa `SplitSentence.java`och öppna en ny fil:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Kopiera och klistra in java-koden nedan i den nya filen.  Stäng sedan filen.

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

#### <a name="wordcount"></a>WordCount (Ort)

Ange kommandot nedan för att skapa `WordCount.java`och öppna en ny fil:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Kopiera och klistra in java-koden nedan i den nya filen.  Stäng sedan filen.

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

Topologin binder pipar och bultar till ett diagram, som definierar hur data flödar mellan komponenterna. Det ger också parallellism tips som Storm använder när du skapar instanser av komponenterna i klustret.

Följande bild är ett grundläggande diagram över diagrammet av komponenter för den här topologin.

![diagram som visar pipar och bultar arrangemang](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Om du vill implementera topologin anger du kommandot `WordCountTopology.java`nedan för att skapa och öppna en ny fil:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Kopiera och klistra in java-koden nedan i den nya filen.  Stäng sedan filen.

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

Storm använder [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) för att logga information. Om du inte konfigurerar loggning avger topologin diagnostikinformation. Om du vill styra vad som `log4j2.xml` loggas skapar du en fil som heter i katalogen `resources` genom att ange kommandot nedan:

```cmd
notepad resources\log4j2.xml
```

Kopiera och klistra sedan in XML-texten nedan i den nya filen.  Stäng sedan filen.

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

Den här XML-koden konfigurerar `com.microsoft.example` en ny logger för klassen, som innehåller komponenterna i den här exempeltopologin. Nivån är inställd på att spåra för den här loggern, som fångar all loggningsinformation som avges av komponenter i den här topologin.

Avsnittet `<Root level="error">` konfigurerar rotnivån för loggning (allt som inte finns i) `com.microsoft.example`för att bara logga felinformation.

Mer information om hur du konfigurerar loggning för [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html)Log4j 2 finns i .

> [!NOTE]  
> Storm version 0.10.0 och högre användning Log4j 2.x. Äldre versioner av stormen används Log4j 1.x, som använde ett annat format för loggkonfiguration. Information om den äldre [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)konfigurationen finns i .

## <a name="test-the-topology-locally"></a>Testa topologin lokalt

När du har sparat filerna använder du följande kommando för att testa topologin lokalt.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

När den körs visar topologin startinformation. Följande text är ett exempel på ordräkningsutdata:

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Den här exempelloggen anger att ordet "och" har avgetts 113 gånger. Antalet fortsätter att gå upp så länge topologin körs eftersom pipen kontinuerligt avger samma meningar.

Det finns en 5-sekunders intervall mellan utsläpp av ord och räknas. **WordCount-komponenten** är konfigurerad för att endast avge information när en fästetyppel anländer. Det begär att fästing tupplar endast levereras var femte sekund.

## <a name="convert-the-topology-to-flux"></a>Konvertera topologin till Flux

[Flux](https://storm.apache.org/releases/2.0.0/flux.html) är ett nytt ramverk som är tillgängligt med Storm 0.10.0 och högre, vilket gör att du kan separera konfigurationen från implementeringen. Komponenterna definieras fortfarande i Java, men topologin definieras med hjälp av en YAML-fil. Du kan paketera en standardtopologidefinition med projektet eller använda en fristående fil när du skickar topologin. När du skickar topologin till Storm kan du använda miljövariabler eller konfigurationsfiler för att fylla i värden i YAML-topologidefinitionen.

YAML-filen definierar de komponenter som ska användas för topologin och dataflödet mellan dem. Du kan inkludera en YAML-fil som en del av jar-filen eller använda en extern YAML-fil.

För mer information om Flux, se [Flux framework (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> På grund av en [bugg (https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) med Storm 1.0.1 kan du behöva installera en [stormutvecklingsmiljö](https://storm.apache.org/releases/current/Setting-up-development-environment.html) för att köra Flux-topologier lokalt.

1. Tidigare `WordCountTopology.java` definierade topologin, men behövs inte med Flux. Ta bort filen med följande kommando:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Ange kommandot nedan för att skapa `topology.yaml`och öppna en ny fil:

    ```cmd
    notepad resources\topology.yaml
    ```

    Kopiera och klistra sedan in texten nedan i den nya filen.  Stäng sedan filen.

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

1. Ange kommandot nedan `pom.xml` för att öppna för att göra de beskrivna ändringarna nedan:

    ```cmd
    notepad pom.xml
    ```

   1. Lägg till följande nya `<dependencies>` beroende i avsnittet:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Lägg till följande `<plugins>` plugin i avsnittet. Denna plugin hanterar skapandet av ett paket (jar-fil) för projektet, och tillämpar vissa omvandlingar som är specifika för Flux när du skapar paketet.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
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

   1. För avsnittet Exec Maven Plugin `<configuration>`  >  `<mainClass>` navigerar du till och byter `${storm.topology}` till `org.apache.storm.flux.Flux`. Med den här inställningen kan Flux hantera att köra topologin lokalt under utveckling.

   1. Lägg `<resources>` till följande i `<includes>`avsnittet . Den här XML-filen innehåller YAML-filen som definierar topologin som en del av projektet.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testa fluxtopologin lokalt

1. Ange följande kommando för att kompilera och köra Flux-topologin med Maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Om din topologi använder Storm 1.0.1 bitar, misslyckas det här kommandot. Det här felet [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055)orsakas av . Installera i stället [Storm i utvecklingsmiljön](https://storm.apache.org/releases/current/Setting-up-development-environment.html) och använd följande steg:
    >
    > Om du har [installerat Storm i utvecklingsmiljön](https://storm.apache.org/releases/current/Setting-up-development-environment.html)kan du använda följande kommandon i stället:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    Parametern `--local` kör topologin i lokalt läge i utvecklingsmiljön. Parametern `-R /topology.yaml` använder `topology.yaml` filresursen från jar-filen för att definiera topologin.

    När den körs visar topologin startinformation. Följande text är ett exempel på utdata:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Det finns en fördröjning på 10 sekunder mellan batchar med loggad information.

2. Skapa en ny topologi yaml från projektet.

    1. Ange kommandot nedan `topology.xml`för att öppna:

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Leta reda på följande avsnitt `10` `5`och ändra värdet på till . Den här ändringen ändrar intervallet mellan avgivande batchar av ord räknas från 10 sekunder till 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Spara filen `newtopology.yaml`som .

3. Om du vill köra topologin anger du följande kommando:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Eller, om du har Storm på din utvecklingsmiljö:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Det här `newtopology.yaml` kommandot använder som topologi definition. Eftersom vi inte inkluderade `compile` parametern använder Maven den version av projektet som skapats i tidigare steg.

    När topologin startar bör du märka att tiden mellan avgivna batchar har ändrats för att återspegla värdet i `newtopology.yaml`. Så du kan se att du kan ändra din konfiguration via en YAML-fil utan att behöva kompilera om topologin.

För mer information om dessa och andra funktioner i Flux-ramverket, se [Flux (https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) är en abstraktion på hög nivå som tillhandahålls av Storm. Det stöder tillståndskänslig bearbetning. Den främsta fördelen med Trident är att det kan garantera att varje meddelande som kommer in i topologin behandlas endast en gång. Utan att använda Trident, kan din topologi bara garantera att meddelanden behandlas minst en gång. Det finns också andra skillnader, till exempel inbyggda komponenter som kan användas istället för att skapa bultar. I själva verket, bultar ersätts med mindre generiska komponenter, såsom filter, projektioner och funktioner.

Trident applikationer kan skapas med hjälp av Maven projekt. Du använder samma grundläggande steg som tidigare i den här artikeln – bara koden är annorlunda. Trident kan inte heller (för närvarande) användas med Flux-ramverket.

Mer information om Trident finns i [översikten över Trident API](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Efterföljande moment

Du har lärt dig hur du skapar en Apache Storm-topologi med hjälp av Java. Lär dig nu hur du:

* [Distribuera och hantera Apache Storm-topologier på HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Utveckla C# topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

Du kan hitta fler exempel Apache Storm topologier genom att besöka [Exempel topologier för Apache Storm på HDInsight](apache-storm-example-topology.md).
