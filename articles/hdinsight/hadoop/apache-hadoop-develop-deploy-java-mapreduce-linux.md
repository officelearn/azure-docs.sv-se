---
title: Skapa Java MapReduce för Apache Hadoop - Azure HDInsight
description: Lär dig hur du använder Apache Maven för att skapa ett Java-baserat MapReduce-program och kör det sedan med Hadoop på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/16/2020
ms.openlocfilehash: a37a8bb45c11d5b74f3059a153806e3d083cf452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311962"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Utveckla Java MapReduce-program för Apache Hadoop i HDInsight

Lär dig hur du använder Apache Maven för att skapa ett Java-baserat MapReduce-program och kör det sedan med Apache Hadoop på Azure HDInsight.

## <a name="prerequisites"></a>Krav

* [Java Developer Kit (JDK) version 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) korrekt [installerad](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projektbyggsystem för Java-projekt.

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

Miljön som användes för den här artikeln var en dator som kör Windows 10. Kommandona utfördes i en kommandotolk och de olika filerna redigerades med Anteckningar. Ändra i enlighet med detta för din miljö.

Från en kommandotolk anger du kommandona nedan för att skapa en arbetsmiljö:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Ange följande kommando för att skapa ett Maven-projekt med namnet **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Med det här kommandot skapas en `artifactID` katalog med det namn som anges av parametern (**wordcountjava** i det här exemplet.) Den här katalogen innehåller följande objekt:

    * `pom.xml`- [Projektobjektmodellen (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) som innehåller information och konfigurationsinformation som används för att bygga projektet.
    * src\main\java\org\apache\hadoop\examples: Innehåller din programkod.
    * src\test\java\org\apache\hadoop\examples: Innehåller tester för ditt program.

1. Ta bort den genererade exempelkoden. Ta bort de genererade `AppTest.java`test- och programfilerna och `App.java` genom att ange kommandona nedan:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Uppdatera projektobjektmodellen

En fullständig referens för filen pom.xml finns i https://maven.apache.org/pom.html. Öppna `pom.xml` genom att ange kommandot nedan:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Lägga till beroenden

Lägg `pom.xml`till följande text `<dependencies>` i avsnittet i:

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

Detta definierar obligatoriska bibliotek &lt;(listade\>i artifactId) med en &lt;\>specifik version (listad i version ). Vid kompilering hämtas dessa beroenden från standarddatabasen Maven. Du kan använda [Maven-databassökningen](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) för att visa mer.

Talar `<scope>provided</scope>` maven att dessa beroenden inte bör paketeras med programmet, eftersom de tillhandahålls av HDInsight-klustret vid körning.

> [!IMPORTANT]
> Den version som används ska matcha den version av Hadoop som finns i klustret. Mer information om versioner finns i [HDInsight-komponentversionsdokumentet.](../hdinsight-component-versioning.md)

### <a name="build-configuration"></a>Skapa konfiguration

Maven plug-ins kan du anpassa bygga stadier av projektet. Det här avsnittet används för att lägga till plugin-program, resurser och andra konfigurationsalternativ för versioner.

Lägg till följande `pom.xml` kod i filen och spara och stäng filen. Den här texten `<project>...</project>` måste finnas i taggarna `</dependencies>` i `</project>`filen, till exempel mellan och .

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

Det här avsnittet konfigurerar Plugin-programmet Apache Maven Compiler och Apache Maven Shade Plugin. Plugin-programmet kompilator används för att kompilera topologin. Skärmplug-in används för att förhindra licensdubbning i JAR-paketet som är byggt av Maven. Denna plugin används för att förhindra en "dubblett licensfiler" fel vid körning på HDInsight klustret. Använda maven-skugga-plugin `ApacheLicenseResourceTransformer` med genomförandet förhindrar felet.

Den maven-skugga-plugin producerar också en uber burk som innehåller alla beroenden som krävs av programmet.

Spara filen `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Skapa MapReduce-programmet

1. Ange kommandot nedan för att skapa `WordCount.java`och öppna en ny fil . Välj **Ja** vid uppmaningen om du vill skapa en ny fil.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Kopiera och klistra in java-koden nedan i den nya filen. Stäng sedan filen.

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

    Lägg märke till `org.apache.hadoop.examples` att paketnamnet `WordCount`är och klassnamnet är . Du använder dessa namn när du skickar mapreduce-jobbet.

## <a name="build-and-package-the-application"></a>Skapa och paketera programmet

Från `wordcountjava` katalogen använder du följande kommando för att skapa en JAR-fil som innehåller programmet:

```cmd
mvn clean package
```

Det här kommandot rensar alla tidigare byggartefakter, hämtar alla beroenden som inte redan har installerats och sedan skapar och paketerar programmet.

När kommandot är klart `wordcountjava/target` innehåller katalogen `wordcountjava-1.0-SNAPSHOT.jar`en fil med namnet .

> [!NOTE]
> Filen `wordcountjava-1.0-SNAPSHOT.jar` är en uberjar, som inte bara innehåller WordCount-jobbet, utan även beroenden som jobbet kräver vid körning.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Ladda upp JAR och kör jobb (SSH)

Följande steg `scp` används för att kopiera JAR till den primära huvudnoden för apache HBase i HDInsight-klustret. Kommandot `ssh` används sedan för att ansluta till klustret och köra exemplet direkt på huvudnoden.

1. Ladda upp burken till klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-klusternamn och ange sedan följande kommando:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Anslut till klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-klusternamn och ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från SSH-sessionen använder du följande kommando för att köra MapReduce-programmet:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Det här kommandot startar WordCount MapReduce-programmet. Indatafilen `/example/data/gutenberg/davinci.txt`är och utdatakatalogen är `/example/data/wordcountout`. Både indatafilen och utdata lagras i standardlagringen för klustret.

1. När jobbet är klart använder du följande kommando för att visa resultaten:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Du bör få en lista med ord och antal, med värden som liknar följande text:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig att utveckla ett Java MapReduce-jobb. Se följande dokument för andra sätt att arbeta med HDInsight.

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använd MapReduce med HDInsight](hdinsight-use-mapreduce.md)
* [Java-utvecklingscenter](https://azure.microsoft.com/develop/java/)
