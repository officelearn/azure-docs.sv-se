---
title: Använda ett interaktivt Spark-skal i Azure HDInsight
description: Ett interaktivt Spark Shell ger en läs-kör-print process för att köra Spark kommandon en i taget och se resultaten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/10/2020
ms.openlocfilehash: f8737f645df2aefbf9ce544199f0cc45ce6a3d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162811"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Kör Apache Spark från Spark Shell

En interaktiv [Apache Spark](https://spark.apache.org/) Shell ger en REPL -miljö (läs-kör-print loop) för att köra Spark-kommandon en i taget och se resultatet. Den här processen är användbar för utveckling och felsökning. Spark ger ett skal för var och en av dess språk som stöds: Scala, Python och R.

## <a name="run-an-apache-spark-shell"></a>Kör en Apache Spark Shell

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark tillhandahåller skal för Scala (spark-shell) och Python (pyspark). I SSH-sessionen *anger du något* av följande kommandon:

    ```bash
    spark-shell

    # Optional configurations
    # spark-shell --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    ```bash
    pyspark

    # Optional configurations
    # pyspark --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4
    ```

    Om du tänker använda valfri konfiguration, se till att du först granska [OutOfMemoryError undantag för Apache Spark](./apache-spark-troubleshoot-outofmemory.md).

1. Några grundläggande exempelkommandon. Välj relevant språk:

    ```spark-shell
    val textFile = spark.read.textFile("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(line => line.contains("apple")).show()
    ```

    ```pyspark
    textFile = spark.read.text("/example/data/fruits.txt")
    textFile.first()
    textFile.filter(textFile.value.contains("apple")).show()
    ```

1. Fråga en CSV-fil. Notera språket nedan `spark-shell` fungerar `pyspark`för och .

    ```scala
    spark.read.csv("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv").show()
    ```

1. Fråga en CSV-fil och lagra resultat i variabel:

    ```spark-shell
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

    ```pyspark
    data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")
    ```

1. Visa resultat:

    ```spark-shell
    data.show()
    data.select($"BuildingID", $"Country").show(10)
    ```

    ```pyspark
    data.show()
    data.select("BuildingID", "Country").show(10)
    ```

1. Avsluta

    ```spark-shell
    :q
    ```

    ```pyspark
    exit()
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession- och SparkContext-instanser

Som standard när du kör Spark Shell instanserar förekomster av SparkSession och SparkContext automatiskt för dig.

Om du vill komma `spark`åt SparkSession-instansen anger du . Om du vill komma åt `sc`SparkContext-instansen anger du .

## <a name="important-shell-parameters"></a>Viktiga skalparametrar

Kommandot Spark Shell`spark-shell`( `pyspark`eller ) stöder många kommandoradsparametrar. Om du vill se en fullständig lista över `--help`parametrar startar du Spark Shell med växeln . Vissa av dessa parametrar `spark-submit`kan bara gälla , som Spark Shell radbryts.

| växla | description | Exempel |
| --- | --- | --- |
| --master MASTER_URL | Anger huvud-URL:en. I HDInsight är det `yarn`här värdet alltid . | `--master yarn`|
| --burkar JAR_LIST | Kommaavgränsad lista över lokala burkar som ska inkluderas på drivrutins- och executorklassbanorna. I HDInsight består den här listan av sökvägar till standardfilsystemet i Azure Storage eller Data Lake Storage. | `--jars /path/to/examples.jar` |
| --paket MAVEN_COORDS | Kommaavskiljad lista över maven-koordinater för burkar som ska inkluderas på föraren och körkretsklassbanorna. Söker i den lokala maven repo, sedan maven central, sedan `--repositories`några ytterligare fjärrarkiv som anges med . Formatet för koordinaterna är *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-filer LISTA | Endast för Python visas en kommaavgränsad lista över .zip-, .egg- eller .py-filer som ska placeras på PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Nästa steg

- Se [Introduktion till Apache Spark på Azure HDInsight](apache-spark-overview.md) för en översikt.
- Se [Skapa ett Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md) för att arbeta med Spark-kluster och SparkSQL.
- Se [Vad är Apache Spark Structured Streaming?](apache-spark-streaming-overview.md)
