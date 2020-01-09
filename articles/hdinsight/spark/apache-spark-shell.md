---
title: Använda ett interaktivt Spark-gränssnitt i Azure HDInsight
description: Ett interaktivt Spark-gränssnitt ger en Read-EXECUTE-utskrift för att köra Spark-kommandon en i taget och visa resultaten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435215"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Köra Apache Spark från Spark-gränssnittet

Ett interaktivt [Apache Spark](https://spark.apache.org/) -gränssnitt ger en repl-miljö (Read-EXECUTE-Print) för att köra Spark-kommandon en i taget och visa resultaten. Den här processen är användbar för utveckling och fel sökning. Spark tillhandahåller ett gränssnitt för vart och ett av de språk som stöds: Scala, python och R.

## <a name="run-an-apache-spark-shell"></a>Köra ett Apache Spark-gränssnitt

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark tillhandahåller gränssnitt för Scala (Spark-Shell) och python (pyspark). I SSH-sessionen anger du något av följande kommandon:

    ```bash
    spark-shell
    pyspark
    ```

    Nu kan du ange Spark-kommandon på det aktuella språket.

1. Några grundläggande exempel kommandon:

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession-och SparkContext-instanser

Som standard när du kör Spark-gränssnittet instansieras instanser av SparkSession och SparkContext automatiskt åt dig.

För att få åtkomst till SparkSession-instansen anger du `spark`. För att få åtkomst till SparkContext-instansen anger du `sc`.

## <a name="important-shell-parameters"></a>Viktiga Shell-parametrar

Spark Shell-kommandot (`spark-shell`eller `pyspark`) stöder många kommando rads parametrar. Om du vill se en fullständig lista över parametrar startar du Spark-gränssnittet med växeln `--help`. En del av dessa parametrar kan endast tillämpas på `spark-submit`, vilket Spark-gränssnittet radbryts.

| växla | description | Exempel |
| --- | --- | --- |
| --huvud MASTER_URL | Anger huvud-URL: en. I HDInsight är det här värdet alltid `yarn`. | `--master yarn`|
| --jar v7 JAR_LIST | Kommaavgränsad lista över lokala jar v7 som ska ingå i classpath-driv rutiner och utförar. I HDInsight består den här listan av sökvägar till standard fil systemet i Azure Storage eller Data Lake Storage. | `--jars /path/to/examples.jar` |
| – paket MAVEN_COORDS | Kommaavgränsad lista över maven-koordinaterna för JAR v7 som ska ingå på driv rutins-och utförar-classpath. Söker i den lokala maven-lagrings platsen, sedan maven Central och sedan alla ytterligare fjärrdatabaser som anges med `--repositories`. Formatet för koordinaterna är*artifactId* *:* *version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --PY-fillista | Endast för python, en kommaavgränsad lista med zip-,. ägg-eller. py-filer som ska placeras på PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Introduktion till Apache Spark på Azure HDInsight](apache-spark-overview.md) .
- Se [skapa ett Apache Spark kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md) för att arbeta med Spark-kluster och SparkSQL.
- Se [Vad är Apache Spark strukturerad strömning?](apache-spark-streaming-overview.md) om du vill skriva program som bearbetar strömmande data med Spark.
