---
title: Använda ett interaktivt Spark-gränssnitt i Azure HDInsight
description: Ett interaktivt Spark-gränssnitt ger en Read-EXECUTE-utskrift för att köra Spark-kommandon en i taget och visa resultaten.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 7aac2812787a7c14d99377754a4f85e699ef3f09
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441896"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Köra Apache Spark från Spark-gränssnittet

Ett interaktivt [Apache Spark](https://spark.apache.org/) -gränssnitt ger en repl-miljö (Read-EXECUTE-Print) för att köra Spark-kommandon en i taget och visa resultaten. Den här processen är användbar för utveckling och fel sökning. Spark tillhandahåller ett gränssnitt för vart och ett av de språk som stöds: Scala, python och R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Gå till ett Apache Spark-gränssnitt med SSH

Få åtkomst till ett Apache Spark-gränssnitt i HDInsight genom att ansluta till den primära huvudnoden i klustret med hjälp av SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Du kan hämta det fullständiga SSH-kommandot för klustret från Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till fönstret för ditt HDInsight Spark-kluster.
3. Välj Secure Shell (SSH).

    ![HDInsight-fönstret i Azure Portal](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Kopiera det visade SSH-kommandot och kör det i terminalen.

    ![HDInsight SSH-fönstret i Azure Portal](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Mer information om hur du använder SSH för att ansluta till HDInsight finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Köra ett Apache Spark-gränssnitt

Spark tillhandahåller gränssnitt för Scala (Spark-Shell), python (pyspark) och R (Spark). I SSH-sessionen på Head-noden i HDInsight-klustret anger du något av följande kommandon:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Nu kan du ange Spark-kommandon på det aktuella språket.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession-och SparkContext-instanser

Som standard när du kör Spark-gränssnittet instansieras instanser av SparkSession och SparkContext automatiskt åt dig.

För att få åtkomst till SparkSession- `spark`instansen anger du. För att få åtkomst till SparkContext- `sc`instansen anger du.

## <a name="important-shell-parameters"></a>Viktiga Shell-parametrar

Spark Shell-kommandot (`spark-shell`, `pyspark`eller `sparkR`) stöder många kommando rads parametrar. Om du vill se en fullständig lista över parametrar startar du Spark-gränssnittet med `--help`växeln. Observera att vissa av dessa parametrar endast kan tillämpas på `spark-submit`, vilket Spark-gränssnittet radbryts.

| byta | description | Exempel |
| --- | --- | --- |
| --huvud MASTER_URL | Anger huvud-URL: en. I HDInsight är det här värdet alltid `yarn`. | `--master yarn`|
| --jar v7 JAR_LIST | Kommaavgränsad lista över lokala jar v7 som ska ingå i classpath-driv rutiner och utförar. I HDInsight består den här listan av sökvägar till standard fil systemet i Azure Storage eller Data Lake Storage. | `--jars /path/to/examples.jar` |
| --paket MAVEN_COORDS | Kommaavgränsad lista över maven-koordinaterna för JAR v7 som ska ingå på driv rutins-och utförar-classpath. Söker i den lokala maven-lagrings platsen, sedan maven Central och sedan alla ytterligare fjärrdatabaser som anges med `--repositories`. Formatet för koordinaterna *är* *artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --PY-fillista | Endast för python, en kommaavgränsad lista med zip-,. ägg-eller. py-filer som ska placeras på PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Introduktion till Apache Spark på Azure HDInsight](apache-spark-overview.md) .
- Se [skapa ett Apache Spark kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md) för att arbeta med Spark-kluster och SparkSQL.
- Se [Vad är Apache Spark strukturerad strömning?](apache-spark-streaming-overview.md) om du vill skriva program som bearbetar strömmande data med Spark.
