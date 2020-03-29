---
title: Felsökningsguider för Azure HDInight
description: Felsöka Apache Hadoop-arbetsbelastningar med hjälp av Azure HDInsight. Steg-för-steg-dokumentation visar hur du använder HDInsight för att lösa vanliga problem med Apache Hive, Apache Spark, Apache YARN, Apache HBase, HDFS och Apache Storm.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 4460c24bb742bff72af47954363b1051f5d9b43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895278"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Felsöka med hjälp av Azure HDInsight

| Apache-arbetsbelastning | Vanligaste frågorna |
|---|---|
|![hdinsight apache HBase ikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hbase.png)<br>[Felsöka Apache HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Ej tilldelade regioner](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Överskridna tidsgränser med kommandot ”hbase hbck” i Azure HDInsight](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Apache Phoenix-anslutningsproblem i Azure HDInsight](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[Vad gör att en huvudserver inte startar?](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException - Adress som redan används](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![hdinsight apache hdfs ikonikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hdfs.png)<br>[Felsöka Apache Hadoop HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Hur kommer jag åt en lokal HDFS inifrån ett kluster?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Lokala HDFS som fastnat i felsäkert läge i Azure HDInsight-kluster](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![hdinsight apache Hive ikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-hive.png)<br>[Felsöka Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[Hur exporterar jag en Hive-metabutik och importerar den på ett annat kluster?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Hur hittar jag Apache Hive-loggar i ett kluster?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Hur startar jag Apache Hive-skalet med specifika konfigurationer i ett kluster?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Hur analyserar jag Apache Tez DAG-data på en klusterkritisk linje?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Hur laddar jag ner Apache Tez DAG-data från ett kluster?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![ikonikonen hdinsight apache Spark](./media/hdinsight-troubleshoot-guide/hdinsight-apache-spark.png)<br>[Felsöka Apache Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Hur gör jag för att konfigurera ett Apache Spark-program med Apache Ambari i kluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[Hur gör jag för att konfigurera ett Apache Spark-program med hjälp av en Jupyter Notebook i kluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Hur gör jag för att konfigurera ett Apache Spark-program med Apache Livy i kluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[Hur gör jag för att konfigurera ett Apache Spark-program med spark-submit i kluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[Hur konfigurerar jag ett Apache Spark-program med IntelliJ?](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Hur konfigurerar jag ett Apache Spark-program med Eclipse?](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[Hur konfigurerar jag ett Apache Spark-program med VSCode?](hdinsight-for-vscode.md)<br><br>[Undantag för OutOfMemoryError för Apache Spark](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![hdinsight apache Storm ikonikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-storm.png)<br>[Felsöka Apache Storm](hdinsight-troubleshoot-STORM.md)|<br>[Hur kommer jag åt Apache Storm-användargränssnittet i ett kluster?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Hur överför jag information om Apache Storm-händelsehubbens pipkontrollpunkt från en topologi till en annan?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Hur hittar jag Storm-binärfiler i ett kluster?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Hur avgör jag distributionstopologin för ett Storm-kluster?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Hur hittar jag Apache Storm-händelsehubbpisar för utveckling?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![hdinsight apache YARN ikonikon](./media/hdinsight-troubleshoot-guide/hdinsight-apache-yarn.png)<br>[Felsöka Apache Hadoop YARN](hdinsight-troubleshoot-YARN.md)|<br>[Hur skapar jag en ny Apache Hadoop YARN-kö i ett kluster?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Hur laddar jag ner Apache Hadoop YARN-loggar från ett kluster?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Felsökning av HDInsight-resurser

| För information om | Läs följande artiklar |
| --- | --- |
| HDInsight på Linux och optimering | - [Information om hur du använder HDInsight på Linux](hdinsight-hadoop-linux-information.md)<br>- [Apache Hadoop-felsökning av minne och prestanda](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Prestanda för Apache Hive-frågor](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Stockar och soptippar | - [Få tillgång till Apache Hadoop YARN-programloggar på Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Aktivera heap dumpar för Apache Hadoop tjänster på Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)|
| Fel | - [Förstå och lösa WebHCat-fel](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Apache Hive-inställningar för att åtgärda OutofMemory-fel](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Verktyg | - [Optimera Apache Hive-frågor](hdinsight-hadoop-optimize-hive-query.md)<br>- [HDInsight IntelliJ-verktyg](./spark/apache-spark-intellij-tool-plugin.md)<br>- [HDInsight Eclipse-verktyg](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [HDInsight VSCode-verktyg](hdinsight-for-vscode.md)<br>- [HdInsight Visual Studio-verktyg](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).