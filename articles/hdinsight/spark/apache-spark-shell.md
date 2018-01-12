---
title: "Använd interaktiva Spark-gränssnittet i Azure HDInsight | Microsoft Docs"
description: "En interaktiva Spark-gränssnittet innehåller en Läs köra utskrifts-process för att köra Spark kommandon ett i taget och se resultaten."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: maxluk
ms.openlocfilehash: 55ff40165a1b25949cb202d9a5174e1f52fe4630
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="run-spark-from-the-spark-shell"></a>Köra Spark från Spark-gränssnittet

En interaktiva Spark-gränssnittet tillhandahåller en REPL-miljö (Läs köra utskrifts-loop) för att köra Spark kommandon ett i taget och se resultaten. Detta är användbart för utveckling och felsökning. Spark tillhandahåller ett gränssnitt för var och en av dess språk som stöds: Scala, Python och R.

## <a name="get-to-a-spark-shell-with-ssh"></a>Komma åt ett Spark-gränssnitt med SSH

Åtkomst till ett gränssnitt för Spark på HDInsight genom att ansluta till klustret med SSH primära huvudnod:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Du kan få fullständig SSH-kommandot för klustret i Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till fönstret för HDInsight Spark-kluster.
3. Välj SSH (Secure Shell).

    ![HDInsight-rutan i Azure-portalen](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Kopiera visas SSH-kommandot och kör det i terminalen.

    ![HDInsight SSH rutan i Azure-portalen](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Mer information om hur du använder SSH för att ansluta till HDInsight finns [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-a-spark-shell"></a>Kör ett Spark-gränssnitt

Spark tillhandahåller gränssnitt för Scala (spark-shell), Python (pyspark) och R (sparkR). Ange något av följande kommandon i SSH-sessionen på huvudnod ditt HDInsight-kluster:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Nu kan du ange Spark kommandona i det aktuella språket.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession och SparkContext instanser

När du kör Spark-gränssnittet som standard instansieras instanser av SparkSession och SparkContext automatiskt åt dig.

För att komma åt SparkSession-instans, ange `spark`. För att komma åt SparkContext-instans, ange `sc`.

## <a name="important-shell-parameters"></a>Viktiga shell-parametrar

Spark Shell-kommando (`spark-shell`, `pyspark`, eller `sparkR`) stöder många kommandoradsparametrar. Om du vill se en fullständig lista över parametrar startar Spark-gränssnittet med växeln `--help`. Observera att vissa av dessa parametrar kan bara gäller `spark-submit`, som radbryts Spark-gränssnittet.

| växel | description | Exempel |
| --- | --- | --- |
| --master MASTER_URL | Anger URL som master. I HDInsight, det här värdet är alltid `yarn`. | `--master yarn`|
| --JAR: er JAR_LIST | Kommaavgränsad lista över lokala burkar ska inkluderas i drivrutinen och utföraren klassökvägar. I HDInsight består denna lista av sökvägar till standard filsystemet i Azure Storage eller Azure Data Lake Store. | `--jars /path/to/examples.jar` |
| --MAVEN_COORDS-paket | Kommaavgränsad lista över maven koordinaterna för burkar ska inkluderas i drivrutinen och utföraren klassökvägar. Söker lokala maven lagringsplatsen och sedan maven central och eventuella ytterligare remote databaser anges med `--repositories`. Formatet för koordinaterna är *groupId*:*artefakt-ID*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| listan över--kopiera filer | För Python, en kommaavgränsad lista över .zip, .egg eller .py filer att placera på PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Nästa steg

- Se [introduktion till Spark på Azure HDInsight](apache-spark-overview.md) en översikt.
- Se [skapar ett Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md) att arbeta med Spark-kluster och SparkSQL.
- Se [vad är strukturerade Spark-direktuppspelning?](apache-spark-streaming-overview.md) att skriva program som bearbetar strömmande data med Spark.

