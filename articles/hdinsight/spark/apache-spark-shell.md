---
title: Använd en interaktiv Spark Shell i Azure HDInsight
description: En interaktiv Spark Shell tillhandahåller en Läs köra utskrifts-process för att köra Spark kommandon ett i taget och se resultaten.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 9044ed3ad9cf9ffa2f54d130bb50b37df121b86f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116081"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Kör Apache Spark från Spark Shell

En interaktiv [Apache Spark](https://spark.apache.org/) Shell tillhandahåller en REPL (read-köra-print-loop)-miljö för att köra Spark kommandon ett i taget och se resultaten. Detta är användbart för utveckling och felsökning. Spark tillhandahåller ett gränssnitt för var och en av dess språk som stöds: Scala, Python och R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Gå till en Apache Spark-Shell med SSH

Komma åt en Apache Spark-Shell på HDInsight genom att ansluta till den primära huvudnoden av klustret med SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Du kan få fullständig SSH-kommandot för ditt kluster från Azure portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till fönstret för ditt HDInsight Spark-kluster.
3. Välj SSH (Secure Shell).

    ![HDInsight-fönstret i Azure-portalen](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Kopiera visas SSH-kommandot och kör den i terminalen.

    ![HDInsight SSH fönstret i Azure-portalen](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Mer information om hur du använder SSH för att ansluta till HDInsight finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Kör ett Apache Spark-gränssnittet

Spark tillhandahåller gränssnitt för Scala (spark-shell), Python (pyspark) och R (sparkR). Ange något av följande kommandon i SSH-sessionen på huvudnoden för ditt HDInsight-kluster:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Du kan nu ange Spark-kommandon i det aktuella språket.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession och SparkContext instanser

Som standard när du kör Spark-Shell instansieras instanser av SparkSession och SparkContext automatiskt åt dig.

För att komma åt SparkSession-instans, ange `spark`. För att komma åt SparkContext-instans, ange `sc`.

## <a name="important-shell-parameters"></a>Viktiga shell-parametrar

Spark-Shell-kommando (`spark-shell`, `pyspark`, eller `sparkR`) har stöd för många kommandoradsparametrar. Om du vill se en fullständig lista över parametrar, startar du Spark-Shell med växeln `--help`. Observera att vissa av dessa parametrar kan bara gäller `spark-submit`, som omsluter Spark-Shell.

| Växel | description | Exempel |
| --- | --- | --- |
| --MASTER_URL-huvudnod | Anger den överordnade URL. I HDInsight, det här värdet är alltid `yarn`. | `--master yarn`|
| --jars JAR_LIST | Kommaavgränsad lista över lokala JAR-filer till på drivrutinen och executor klassökvägar. I HDInsight består den här listan av sökvägar till standard-filsystem i Azure Storage eller Data Lake Storage. | `--jars /path/to/examples.jar` |
| --MAVEN_COORDS-paket | Kommaavgränsad lista med maven-koordinaterna för JAR-filer till på drivrutinen och executor klassökvägar. Söker lokala maven-lagringsplatsen och sedan maven central, och sedan alla ytterligare remote databaser som har angetts med `--repositories`. Formatet för koordinaterna är *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| listan över--py-filer | För Python, en kommaavgränsad lista över .zip eller .egg .py filer att placera på PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Nästa steg

- Se [introduktion till Apache Spark på Azure HDInsight](apache-spark-overview.md) en översikt.
- Se [skapar ett Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md) att arbeta med Spark-kluster och SparkSQL.
- Se [vad är Apache Spark Structured Streaming?](apache-spark-streaming-overview.md) att skriva program som bearbetar strömmande data med Spark.
