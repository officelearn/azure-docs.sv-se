---
title: Fel söknings guider för Azure HDInsight
description: Felsök Apache Hadoop-arbetsbelastningar med Azure HDInsight. Steg för steg-dokumentationen visar hur du använder HDInsight för att lösa vanliga problem med Apache Hive, Apache Spark, Apache-garn, Apache HBase, HDFS och Apache Storm.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 6c0dc61cac96037406b165d7ebee693bfe709c5d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811746"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Felsöka med hjälp av Azure HDInsight

| Apache-arbetsbelastning | Vanliga frågor |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Felsöka Apache HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Otilldelade regioner](hbase/hbase-troubleshoot-unassigned-regions.md#scenario-unassigned-regions)<br><br>[Timeout med kommandot "HBase hbck" i Azure HDInsight](hbase/hbase-troubleshoot-timeouts-hbase-hbck.md)<br><br>[Apache Phoenix anslutnings problem i Azure HDInsight](hbase/hbase-troubleshoot-phoenix-connectivity.md)<br><br>[Vad gör det inte att starta en huvud server?](hbase/hbase-troubleshoot-start-fails.md)<br><br>[BindException-adressen används redan](hbase/hbase-troubleshoot-bindexception-address-use.md)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Felsöka Apache Hadoop HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Hur gör jag för att åtkomst till en lokal HDFS inifrån ett kluster?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Lokal HDFS fastnar i fel säkert läge på Azure HDInsight-kluster](hadoop/hdinsight-hdfs-troubleshoot-safe-mode.md)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Felsöka Apache Hive](hdinsight-troubleshoot-hive.md)|<br>[Hur gör jag för att exportera ett Hive-metaarkiv och importera det till ett annat kluster?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Hur gör jag för att hitta Apache Hive loggar i ett kluster?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Hur gör jag för att startar du Apache Hive gränssnittet med vissa konfigurationer i ett kluster?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Hur gör jag för att analysera Apache Tez DAG-data på en kluster kritisk väg?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Hur gör jag för att hämta Apache Tez DAG-data från ett kluster?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Felsöka Apache Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Hur gör jag för att konfigurera ett Apache Spark-program med Apache Ambari i kluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[Hur gör jag för att konfigurera ett Apache Spark-program med hjälp av en Jupyter Notebook i kluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Hur gör jag för att konfigurera ett Apache Spark-program med Apache Livy i kluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[Hur gör jag för att konfigurera ett Apache Spark-program med spark-submit i kluster?](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[Hur gör jag för att konfigurerar du ett Apache Spark program genom att använda IntelliJ?](spark/apache-spark-intellij-tool-plugin.md)<br><br>[Hur gör jag för att konfigurera ett Apache Spark program med hjälp av Sol förmörkelse?](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[Hur gör jag för att konfigurerar du ett Apache Spark program genom att använda VSCode?](hdinsight-for-vscode.md)<br><br>[OutOfMemoryError-undantag för Apache Spark](spark/apache-spark-troubleshoot-outofmemory.md#scenario-outofmemoryerror-exception-for-apache-spark)|
|![Stort](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Felsöka Apache Storm](hdinsight-troubleshoot-STORM.md)|<br>[Hur gör jag för att åtkomst till Apache Storm användar gränssnittet i ett kluster?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Hur gör jag för att överföra Apache Storm Event Hub-kanalen från en topologi till en annan?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Hur gör jag för att hittar du Storm-binärfiler i ett kluster?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Hur gör jag för att du bestämma distributions sto pol Ogin för ett Storm-kluster?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Hur gör jag för att hitta Apache Storm Event Hub kanalen-binärfiler för utveckling?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![VIKT](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Felsöka Apache Hadoop garn](hdinsight-troubleshoot-YARN.md)|<br>[Hur gör jag för att skapa en ny Apache Hadoop garn kö i ett kluster?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Hur gör jag för att hämta Apache Hadoop garn loggar från ett kluster?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Fel söknings resurser för HDInsight

| För information om | Läs följande artiklar |
| --- | --- |
| HDInsight på Linux och optimering | - [Information om hur du använder HDInsight på Linux](hdinsight-hadoop-linux-information.md)<br>- [Apache Hadoop fel sökning av minne och prestanda](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Apache Hive fråga om prestanda](https://web.archive.org/web/20190217214250/https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Loggar och dumpar | - [Åtkomst Apache Hadoop garn program loggar på Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Aktivera heap-dum par för Apache Hadoop tjänster i Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analysera HDInsight-loggar](hdinsight-debug-jobs.md)|
| Fel | - [Förstå och lösa WebHCat-fel](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Apache Hive inställningar för att åtgärda OutofMemory-fel](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Verktyg | - [Optimera Apache Hive-frågor](hdinsight-hadoop-optimize-hive-query.md)<br>- [Verktyget HDInsight IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)<br>- [Verktyg för HDInsight-Sol förmörkelse](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [Verktyget HDInsight VSCode](hdinsight-for-vscode.md)<br>- [Verktyget HDInsight Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).