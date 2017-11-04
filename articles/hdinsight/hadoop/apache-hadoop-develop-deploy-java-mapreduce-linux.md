---
title: "Skapa Java MapReduce för Hadoop - Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Apache Maven för att skapa ett Java-baserad MapReduce-program och sedan köra den med Hadoop på Azure HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: larryfr
ms.openlocfilehash: c8871dd59dd6974d5263f925d0638beb28847f21
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>Utveckla Java-MapReduce-program för Hadoop i HDInsight

Lär dig hur du använder Apache Maven för att skapa ett Java-baserad MapReduce-program och sedan köra den med Hadoop på Azure HDInsight.

> [!NOTE]
> Det här exemplet har senast har testats på HDInsight 3,6.

## <a name="prerequisites"></a>Förhandskrav

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 eller senare (eller en motsvarande, till exempel OpenJDK).
    
    > [!NOTE]
    > HDInsight-version 3.4 och tidigare använda Java 7. HDInsight 3.5 och senare använder Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

Följande miljövariabler kan anges när du installerar Java och JDK. Dock bör du kontrollera att de finns och att de innehåller rätt värden för ditt system.

* `JAVA_HOME`-måste peka på den katalog där med Java runtime environment (JRE) har installerats. På en OS X-, Unix- eller Linux-system, bör det till exempel ha ett värde som liknar `/usr/lib/jvm/java-7-oracle`. I Windows, skulle det ha ett värde som liknar`c:\Program Files (x86)\Java\jre1.7`

* `PATH`-bör innehålla följande sökvägar:
  
  * `JAVA_HOME`(eller motsvarande sökväg)

  * `JAVA_HOME\bin`(eller motsvarande sökväg)

  * Katalogen där Maven är installerat

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Från en terminalsession eller Kommandotolken i din utvecklingsmiljö, ändra kataloger till platsen som du vill lagra det här projektet.

2. Använd den `mvn` kommandot, som installeras med Maven för att generera scaffold-teknik för projektet.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Om du använder PowerShell kan du ange den `-D` parametrar inom dubbla citattecken.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Det här kommandot skapar en katalog med det namn som anges av den `artifactID` parameter (**wordcountjava** i det här exemplet.) Den här katalogen innehåller följande:

   * `pom.xml`- [Projekt Object Model (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) som innehåller information och konfiguration information som används för att skapa projektet.

   * `src`-Katalogen som innehåller programmet.

3. Ta bort den `src/test/java/org/apache/hadoop/examples/apptest.java` filen. Den används inte i det här exemplet.

## <a name="add-dependencies"></a>Lägga till beroenden

1. Redigera den `pom.xml` och Lägg till följande text i det `<dependencies>` avsnitt:
   
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

    Detta definierar bibliotek som krävs (anges i &lt;artefakt-ID\>) med en specifik version (listade i &lt;version\>). Dessa beroenden hämtas vid kompileringen, från Maven standarddatabas. Du kan använda den [Maven databasen Sök](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) vill se mer.
   
    Den `<scope>provided</scope>` talar om Maven dessa beroenden inte paketeras med programmet som de som tillhandahålls av HDInsight-kluster under körning.

    > [!IMPORTANT]
    > Den version som används ska matcha versionen av Hadoop finns på ditt kluster. Mer information om versioner finns i [HDInsight component-versioning](../hdinsight-component-versioning.md) dokumentet.

2. Lägg till följande för att den `pom.xml` filen. Den här texten måste finnas i den `<project>...</project>` taggar i filen, till exempel mellan `</dependencies>` och `</project>`.

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

    Första plugin-programmet konfigurerar den [Maven skugga Plugin](http://maven.apache.org/plugins/maven-shade-plugin/), som används för att skapa en uberjar (kallas ibland ett fatjar) som innehåller beroenden som krävs för programmet. Den också förhindrar duplicering av licenser i jar-paketet, vilket kan orsaka problem på vissa datorer.

    Andra plugin-programmet konfigurerar Java målversionen.

    > [!NOTE]
    > HDInsight 3.4 och tidigare användning Java 7. HDInsight 3.5 och senare använder Java 8.

3. Spara den `pom.xml` filen.

## <a name="create-the-mapreduce-application"></a>Skapa MapReduce-program

1. Gå till den `wordcountjava/src/main/java/org/apache/hadoop/examples` katalog och Byt namn på den `App.java` filen till `WordCount.java`.

2. Öppna den `WordCount.java` filen i en textredigerare och Ersätt det med följande text:
   
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
   
    Lägg märke till paketnamnet `org.apache.hadoop.examples` och klassnamnet har `WordCount`. Du använder dessa namn när du skickar MapReduce-jobb.

3. Spara filen.

## <a name="build-the-application"></a>Skapa programmet

1. Ändra till den `wordcountjava` directory om du inte redan har det.

2. Använd följande kommando för att skapa en JAR-fil som innehåller programmet:

   ```
   mvn clean package
   ```

    Det här kommandot rensar alla tidigare build-artefakter, laddar ned alla beroenden som inte redan installerats, och sedan versioner och paketet programmet.

3. När kommandot har slutförts i `wordcountjava/target` katalogen innehåller en fil med namnet `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > Den `wordcountjava-1.0-SNAPSHOT.jar` filen är en uberjar som innehåller inte bara den WordCount jobb, utan även beroenden som krävs vid körning.

## <a id="upload"></a>Överför jar

Använd följande kommando för att överföra jar-filen till HDInsight-headnode:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Det här kommandot kopieras filerna från det lokala systemet till huvudnod. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="run"></a>Kör MapReduce-jobb på Hadoop

1. Anslut till HDInsight med hjälp av SSH. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommando för att köra programmet MapReduce från SSH-session:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Detta kommando startar WordCount MapReduce-programmet. Indatafilen är `/example/data/gutenberg/davinci.txt`, och den angivna katalogen är `/example/data/wordcountout`. Både filen med indata och utdata lagras till standardlagring för klustret.

3. När jobbet har slutförts använder du följande kommando för att visa resultatet:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Du bör få en lista över ord och antal med värden som liknar följande:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du utvecklar ett Java-MapReduce-jobb. Finns i följande dokument för andra sätt att arbeta med HDInsight.

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

