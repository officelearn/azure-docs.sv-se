---
title: Felsöka Hive med hjälp av Azure HDInsight
description: Få svar på vanliga frågor om hur du arbetar med Apache Hive och Azure HDInsight.
keywords: Azure HDInsight, Hive, Vanliga frågor och svar, felsökningsguide, vanliga frågor
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895224"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Felsöka Apache Hive med Azure HDInsight

Lär dig mer om de vanligaste frågorna och deras resolutioner när du arbetar med Apache Hive-nyttolaster i Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hur exporterar jag en Hive-metabutik och importerar den på ett annat kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Anslut till HDInsight-klustret med hjälp av en SSH-klient (Secure Shell). Mer information finns i [Ytterligare läsning](#additional-reading-end).

2. Kör följande kommando i HDInsight-klustret som du vill exportera metabutiken från:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Det här kommandot genererar en fil med namnet allatables.sql.

3. Kopiera filen alltables.sql till det nya HDInsight-klustret och kör sedan följande kommando:

    ```apache
    hive -f alltables.sql
    ```

Koden i lösningsstegen förutsätter att datasökvägar i det nya klustret är samma som datasökvägarna i det gamla klustret. Om datasökvägarna är olika kan du `alltables.sql` redigera den genererade filen manuellt så att den återspeglar eventuella ändringar.

### <a name="additional-reading"></a>Ytterligare läsning

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Hur hittar jag Hive-loggar i ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Anslut till HDInsight-klustret med hjälp av SSH. Mer information finns i **Ytterligare läsning**.

2. Så här visar du Hive-klientloggar:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Så här visar du Hive-metalager loggar:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Så här visar du Hive-serverloggar:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Ytterligare läsning

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Hur startar jag Hive-skalet med specifika konfigurationer i ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Ange ett konfigurationsnyckelvärdespar när du startar Hive-skalet. Mer information finns i [Ytterligare läsning](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Om du vill visa en lista över alla effektiva konfigurationer på Hive-skalet använder du följande kommando:

   ```apache
   hive> set;
   ```

   Använd till exempel följande kommando för att starta Hive-skalet med felsökningsloggning aktiverat på konsolen:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Ytterligare läsning

- [Egenskaper för Hive-konfiguration](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Hur analyserar jag Apache Tez DAG-data på en klusterkritisk linje?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Om du vill analysera en Apache Tez-riktad acyklisk graf (DAG) i ett klusterkritiskt diagram ansluter du till HDInsight-klustret med hjälp av SSH. Mer information finns i [Ytterligare läsning](#additional-reading-end).

2. Kör följande kommando i en kommandotolk:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Om du vill lista andra analysatorer som kan användas för att analysera Tez DAG använder du följande kommando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Du måste ange ett exempelprogram som det första argumentet.

   Giltiga programnamn inkluderar:
    - **ContainerReuseAnalyzer**: Återanvänd information om återanvändning av behållare i en DAG
    - **CriticalPath**: Hitta den kritiska linjen för en DAG
    - **LocalityAnalyzer**: Skriv ut information om orten i en DAG
    - **ShuffleTimeAnalyzer:** analysera shuffle tid detaljer i en dag
    - **SkewAnalyzer**: Analysera skeva detaljer i en DAG
    - **SlowNodeAnalyzer**: Skriv ut noddedetaljer i en DAG
    - **SlowTaskIdentifier**: Skriv ut långsam uppgift detaljer i en DAG
    - **SlowestVertexAnalyzer:** Skriv ut långsammaste hörndetaljer i en DAG
    - **SpillAnalyzer**: Skriv ut spill detaljer i en DAG
    - **TaskConcurrencyAnalyzer**: Skriv ut information om aktivitetssam samtidighet i en DAG
    - **VertexLevelCriticalPathAnalyzer**: Hitta den kritiska linjen på hörnnivå i en DAG

### <a name="additional-reading"></a>Ytterligare läsning

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Hur laddar jag ner Tez DAG-data från ett kluster?

#### <a name="resolution-steps"></a>Lösningsanvisningar

Det finns två sätt att samla in Tez DAG-data:

- Från kommandoraden:

    Anslut till HDInsight-klustret med hjälp av SSH. Kör följande kommando i kommandotolken:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Använd vyn Ambari Tez:

  1. Gå till Ambari.
  2. Gå till Tez-vyn (under panelikonen i det övre högra hörnet).
  3. Välj den DAG som du vill visa.
  4. Välj **Hämta data**.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Ytterligare läsning

[Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

- Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
