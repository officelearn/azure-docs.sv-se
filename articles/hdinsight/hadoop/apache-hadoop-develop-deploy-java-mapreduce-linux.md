---
title: Skapa Java MapReduce för Apache Hadoop - Azure HDInsight
description: Lär dig hur du använder Apache Maven för att skapa ett Java-baserade MapReduce-program och sedan köra den med Hadoop på Azure HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 3c16d85479399d7988be18f9b852563617bb824b
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632664"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Utveckla Java MapReduce-program för Apache Hadoop på HDInsight

Lär dig hur du använder Apache Maven för att skapa ett Java-baserade MapReduce-program och sedan köra den med Apache Hadoop på Azure HDInsight.

> [!NOTE]
> Det här exemplet har nyligen testats på HDInsight 3.6.

## <a name="prerequisites"></a>Förhandskrav

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 eller senare (eller ett motsvarande, till exempel OpenJDK).
    
    > [!NOTE]
    > HDInsight version 3.4 och tidigare använda Java 7. HDInsight 3.5 och senare använder Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

Följande miljövariabler kan anges när du installerar Java och JDK-Paketet. Du bör dock kontrollera att de finns och att de innehåller rätt värden för ditt system.

* `JAVA_HOME` -måste peka på den katalog där med Java runtime environment (JRE) har installerats. På en OS X-, Unix- eller Linux-system, bör det till exempel ha ett värde som liknar `/usr/lib/jvm/java-7-oracle`. I Windows, skulle den ha ett värde som liknar `c:\Program Files (x86)\Java\jre1.7`

* `PATH` – ska innehålla följande sökvägar:
  
  * `JAVA_HOME` (eller motsvarande sökväg)

  * `JAVA_HOME\bin` (eller motsvarande sökväg)

  * Den katalog där Maven har installerats

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Från en terminalsession, eller från kommandoraden i din utvecklingsmiljö, byter du katalog till den plats som du vill lagra det här projektet.

2. Använd den `mvn` kommando, som installeras med Maven för att generera ställningarna för projektet.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Om du använder PowerShell måste du omge det `-D` parametrar inom dubbla citattecken.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Det här kommandot skapar en katalog med det namnet som anges av den `artifactID` parametern (**wordcountjava** i det här exemplet.) Den här katalogen innehåller följande objekt:

   * `pom.xml` – [Projekt Object Model (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) som innehåller information om och konfiguration information som används för att skapa projektet.

   * `src` -Katalogen som innehåller programmet.

3. Ta bort den `src/test/java/org/apache/hadoop/examples/apptest.java` filen. Den används inte i det här exemplet.

## <a name="add-dependencies"></a>Lägga till beroenden

1. Redigera den `pom.xml` filen och Lägg till följande text inuti den `<dependencies>` avsnittet:
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    Detta definierar vilka bibliotek som behövs (visas i &lt;artifactId\>) med en specifik version (visas i &lt;version\>). Vid kompilering laddas dessa beroenden från Maven-centrallagret standard. Du kan använda den [Maven-sökning för databasen](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) vill se mer.
   
    Den `<scope>provided</scope>` meddelar Maven dessa beroenden inte paketeras med programmet som de tillhandahålls av HDInsight-klustret vid körning.

    > [!IMPORTANT]
    > Den version som används måste matcha versionen av Hadoop som finns på ditt kluster. Mer information om versioner finns i den [versionshantering för HDInsight](../hdinsight-component-versioning.md) dokumentet.

2. Lägg till följande i filen `pom.xml`. Den här texten måste ingå i den `<project>...</project>` taggar i filen, till exempel mellan `</dependencies>` och `</project>`.

   ```xml
    <build>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
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
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    Plugin-programmet första konfigurerar den [Maven nyans-pluginprogrammet](http://maven.apache.org/plugins/maven-shade-plugin/), som används för att skapa en uberjar (kallas ibland en fatjar) som innehåller beroenden som krävs av programmet. Det förhindrar även att duplicering av licenser inom jar-paket, vilket kan orsaka problem i vissa system.

    Andra plugin-programmet konfigurerar för målets version av Java.

    > [!NOTE]
    > HDInsight 3.4 och tidigare använda Java 7. HDInsight 3.5 och senare använder Java 8.

3. Spara filen `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Skapa MapReduce-program

1. Gå till den `wordcountjava/src/main/java/org/apache/hadoop/examples` directory och Byt namn på den `App.java` filen till `WordCount.java`.

2. Öppna den `WordCount.java` i en textredigerare och Ersätt innehållet med följande text:
   
    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```
   
    Lägg märke till paketnamnet är `org.apache.hadoop.examples` och klassnamnet har `WordCount`. Du använder dessa namn när du skickar MapReduce-jobb.

3. Spara filen.

## <a name="build-the-application"></a>Skapa programmet

1. Ändra till den `wordcountjava` directory om du inte redan har det.

2. Använd följande kommando för att skapa ett JAR-filen som innehåller programmet:

   ```
   mvn clean package
   ```

    Det här kommandot rensar alla tidigare byggartefakter, laddar ned alla beroenden som inte redan installerats, och sedan byggen och paketet programmet.

3. När kommandot har slutförts, den `wordcountjava/target` katalogen innehåller en fil med namnet `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > Den `wordcountjava-1.0-SNAPSHOT.jar` filen är en uberjar som innehåller inte bara den WordCount jobbet, men även beroenden som krävs för jobbet vid körning.

## <a id="upload"></a>Ladda upp jar

Använd följande kommando för att överföra jar-filen till HDInsight-huvudnoden:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

Ersätt __användarnamn__ med ditt SSH-användarnamn för klustret. Ersätt __CLUSTERNAME__ med HDInsight-klusternamnet.

Det här kommandot kopierar filerna från det lokala systemet till huvudnoden. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="run"></a>Kör MapReduce-jobb på Hadoop

1. Ansluta till HDInsight med hjälp av SSH. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Från SSH-sessionen använder du följande kommando för att köra MapReduce-program:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Detta kommando startar WordCount MapReduce-program. Indatafilen är `/example/data/gutenberg/davinci.txt`, och katalogen är `/example/data/wordcountout`. Både filen med indata och utdata lagras till standardlagringen för klustret.

3. När jobbet har slutförts, använder du följande kommando för att se resultat:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Du bör få en lista över ord och antalet med värden som liknar följande text:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du utvecklar ett Java MapReduce-jobb. Se följande dokument för andra sätt att arbeta med HDInsight.

* [Använda Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Pig med HDInsight](hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)

Mer information finns också i [Java-Utvecklingscenter](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

