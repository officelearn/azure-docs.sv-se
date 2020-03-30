---
title: Bästa metoder för Apache Spark på Azure HDInsight
description: Lär dig metodtips för att använda Apache Spark i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 20033e52e862f086d1491c06d38cdf4f2c57ba8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71106130"
---
# <a name="apache-spark-best-practices"></a>Bästa metoder för Apache Spark

Den här artikeln innehåller olika metodtips för hur du använder Apache Spark på Azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hur kör eller skickar jag Spark-jobb?

| Alternativ | Dokument |
|---|---|
| VSCode | [Använda Spark & Hive-verktyg för Visual Studio-kod](../hdinsight-for-vscode.md) |
| Jupyter Notebook | [Självstudie: Läsa in data och köra frågor i ett Apache Spark-kluster i Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Självstudiekurs: Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Självstudie: Skapa ett Scala Maven-program för Apache Spark i HDInsight med hjälp av IntelliJ](./apache-spark-create-standalone-application.md) |
| Zeppelin Notebook | [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Fjärrjobb inlämning med Livy | [Använda REST-API:et för Apache Spark för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster](./apache-spark-livy-rest-interface.md) |

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hur övervakar och felsöker jag Spark-jobb?

| Alternativ | Dokument |
|---|---|
| Azure Toolkit for IntelliJ | [Fel spark jobb felsökning med Azure Toolkit för IntelliJ (förhandsvisning)](apache-spark-intellij-tool-failure-debug.md) |
| Azure Toolkit för IntelliJ via SSH | [Felsöka Apache Spark-program lokalt eller via fjärranslutning i ett HDInsight-kluster med Azure Toolkit for IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure Toolkit för IntelliJ via VPN | [Använd Azure Toolkit för IntelliJ för att felsöka Apache Spark-program på distans i HDInsight via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Jobbdiagram på Apache Spark History Server | [Använda utökad Apache Spark-historikserver för att felsöka och diagnostisera Apache Spark-program](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hur får jag mina Spark-jobb att fungera mer effektivt?

| Alternativ | Dokument |
|---|---|
| I/O-cache | [Förbättra prestanda för Apache Spark-arbetsbelastningar med Azure HDInsight IO-cache (förhandsversion)](./apache-spark-improve-performance-iocache.md) |
| Konfigurationsalternativ | [Optimera Apache Spark-jobb](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hur ansluter jag till andra Azure-tjänster?

| Alternativ | Dokument |
|---|---|
| Apache Hive på HDInsight | [Integrera Apache Spark och Apache Hive med Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase på HDInsight | [Använda Apache Spark för att läsa och skriva Apache HBase-data](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka på HDInsight | [Självstudie: Använda Apache Spark Structured Streaming med Apache Kafka i HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Cosmos DB: Implementera en lambda-arkitektur på Azure-plattformen](../../cosmos-db/lambda-architecture.md) |

## <a name="what-are-my-storage-options"></a>Vilka är mina lagringsalternativ?

| Alternativ | Dokument |
|---|---|
| Data Lake Storage Gen2 | [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Data Lake Storage Gen1 | [Använda DataSjölagringgend1 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-store.md) |
| Azure Blob Storage | [Använda Azure-lagring med Azure HDInsight-kluster](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache Spark-inställningar](apache-spark-settings.md)
* [Optimera Apache Spark-jobb i HDInsight](apache-spark-perf.md)
