---
title: MapReduce med Apache Hadoop i HDInsight
description: Lär dig hur du kör MapReduce-jobb på Apache Hadoop i HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: c9a4a0944c51cb8c6f6d91cd153580a17a9b7898
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632670"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Använda MapReduce i Apache Hadoop på HDInsight

Lär dig hur du kör MapReduce-jobb på HDInsight-kluster. Använd följande tabell för att identifiera de olika sätt att du kan använda MapReduce med HDInsight:

| **Använd det här**... | **...Om du vill göra detta** | ...med detta **klustret operativsystem** | ...from detta **klientoperativsystem** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Använd kommandot Hadoop via **SSH** |Linux |Linux, Unix, Mac OS X eller Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |Skicka jobbet via en fjärranslutning med hjälp av **REST** (exempel använder vi cURL) |Linux eller Windows |Linux, Unix, Mac OS X eller Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Skicka jobbet via en fjärranslutning med hjälp av **Windows PowerShell** |Linux eller Windows |Windows |

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a id="whatis"></a>Vad är MapReduce

Apache Hadoop MapReduce är ett ramverk för programvara för att skriva jobb som bearbetar stora mängder data. Indata är indelat i oberoende segment. Varje segment bearbetas parallellt över noder i klustret. Ett MapReduce-jobb består av två funktioner:

* **Mapper**: förbrukar indata, analyserar dessa (vanligtvis med filter och sortering operations) och genererar tupplar (nyckel / värde-par)

* **Reducer**: förbrukar tupplar som orsakats av mappningen och utför en sammanfattning av åtgärd som skapar en mindre, kombinerade resultatet från Mapper-data

Ett grundläggande word antal MapReduce-jobb exempel illustreras i följande diagram:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

Utdata för jobbet är en uppräkning av hur många gånger varje ord uppstod i texten.

* Mappningen tar varje rad från indatatext som indata och delar upp den i orden. Den genererar en nyckel/värde-par varje gång ett ord som sker till Word följs av en 1. Resultatet sorteras innan de skickas till reducer.
* Reducer summerar dessa enskilda antalet för varje ord och genererar ett enda nyckel/värde-par som innehåller ordet följt av summan av dess förekomster.

MapReduce kan implementeras på olika språk. Java är den vanligaste implementeringen och används i demonstrationssyfte i det här dokumentet.

## <a name="development-languages"></a>Utvecklingsspråk

Språk eller ramverk som baseras på Java och Java Virtual Machine kan köra direkt som ett MapReduce-jobb. Exemplet i det här dokumentet är ett Java MapReduce-program. Icke-Java-språk, till exempel C#, Python eller fristående körbara filer, måste använda **Hadoop streaming**.

Hadoop streaming kommunicerar med mapper och reducer via STDIN och STDOUT. Mapper och reducer läsa data till en rad i taget från STDIN och skriva utdata till STDOUT. Varje rad läsa eller orsakats av mapper och reducer måste vara i formatet av en nyckel/värde-par, avgränsat med ett tabbtecken:

    [key]/t[value]

Mer information finns i [Hadoop Streaming](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Exempel på användning av Hadoop-strömmande med HDInsight finns i följande dokument:

* [Utveckla C#-MapReduce-jobb](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Utveckla Python MapReduce-jobb](apache-hadoop-streaming-python.md)

## <a id="data"></a>Exempeldata

HDInsight innehåller olika exempel datauppsättningar, som lagras i den `/example/data` och `/HdiSamples` directory. Dessa kataloger finns i standardlagringen för klustret. I det här dokumentet använder vi den `/example/data/gutenberg/davinci.txt` filen. Den här filen innehåller anteckningsböcker för Leonardo Da Vinci.

## <a id="job"></a>Exempel MapReduce

Ett exempel MapReduce word antal program ingår i ditt HDInsight-kluster. Det här exemplet finns på `/example/jars/hadoop-mapreduce-examples.jar` på standardlagringen för klustret.

Följande Java-kod är källan till MapReduce-program som ingår i den `hadoop-mapreduce-examples.jar` fil:

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

Anvisningar att skriva din egen MapReduce-program finns i följande dokument:

* [Utveckla Java MapReduce-program för HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Utveckla Python MapReduce-program för HDInsight](apache-hadoop-streaming-python.md)

## <a id="run"></a>Kör MapReduce

HDInsight kan köra HiveQL jobb med hjälp av olika metoder. Använd följande tabell för att bestämma vilken metod som passar dig och klicka sedan på länken för en genomgång.

| **Använd det här**... | **...Om du vill göra detta** | ...med detta **klustret operativsystem** | ...from detta **klientoperativsystem** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Använd kommandot Hadoop via **SSH** |Linux |Linux, Unix, Mac OS X eller Windows |
| [CURL](apache-hadoop-use-mapreduce-curl.md) |Skicka jobbet via en fjärranslutning med hjälp av **REST** |Linux eller Windows |Linux, Unix, Mac OS X eller Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Skicka jobbet via en fjärranslutning med hjälp av **Windows PowerShell** |Linux eller Windows |Windows |

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="nextsteps"></a>Nästa steg

Mer information om hur du arbetar med data i HDInsight finns i följande dokument:

* [Utveckla Java MapReduce-program för HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Utveckla Python-strömmande MapReduce-program för HDInsight](apache-hadoop-streaming-python.md)

* [Använda Hive med HDInsight][hdinsight-use-hive]

* [Använda Pig med HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
