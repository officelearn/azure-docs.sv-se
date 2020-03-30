---
title: MapReduce med Apache Hadoop på HDInsight
description: Lär dig hur du kör Apache MapReduce-jobb på Apache Hadoop i HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 238f37cc1e8cbc8d3260693181d6e54ac5f592f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435707"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Använda MapReduce i Apache Hadoop på HDInsight

Lär dig hur du kör MapReduce-jobb på HDInsight-kluster.

## <a name="example-data"></a>Exempeldata

HDInsight ger olika exempel datauppsättningar, `/example/data` `/HdiSamples` som lagras i och katalogen. Dessa kataloger finns i standardlagringen för klustret. I det här dokumentet `/example/data/gutenberg/davinci.txt` använder vi filen. Denna fil innehåller anteckningsböcker av Leonardo da Vinci.

## <a name="example-mapreduce"></a>Exempel mapreduce

Ett exempel MapReduce ordräkningsprogram ingår i ditt HDInsight-kluster. Det här exemplet `/example/jars/hadoop-mapreduce-examples.jar` finns på standardlagringen för klustret.

Följande Java-kod är källan till MapReduce-programmet `hadoop-mapreduce-examples.jar` som finns i filen:

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

Instruktioner för att skriva dina egna MapReduce-program finns i [Utveckla Java MapReduce-program för HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Kör MapReduce

HDInsight kan köra HiveQL-jobb med hjälp av olika metoder. Använd följande tabell för att bestämma vilken metod som är rätt för dig och följ sedan länken för en genomgång.

| **Använd denna**... | **... för att göra detta** |  ... från det här **klientoperativsystemet** |
|:--- |:--- |:--- |:--- |
| [Ssh](apache-hadoop-use-mapreduce-ssh.md) |Använd kommandot Hadoop via **SSH** |Linux, Unix, Mac OS X eller Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Skicka jobbet på distans med **REST** |Linux, Unix, Mac OS X eller Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Skicka jobbet på distans med hjälp av **Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med data i HDInsight finns i följande dokument:

* [Utveckla Java MapReduce-program för HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Använda Apache Hive med HDInsight](./hdinsight-use-hive.md)
