---
title: Felsöka Hive med Azure HDInsight
description: Få svar på vanliga frågor om hur du arbetar med Apache Hive och Azure HDInsight.
keywords: Azure HDInsight, Hive, vanliga frågor och svar, felsökningsguide för vanliga frågor
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dharmeshkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 43886a132f2f3cf75f0ec7a0b2dc0680a0f69589
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712477"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Felsöka Apache Hive med Azure HDInsight

Läs mer om på viktiga frågor och deras lösningar när du arbetar med Apache Hive nyttolaster i Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hur jag för att exportera en Hive-metaarkiv och importera dem på ett annat kluster?


### <a name="resolution-steps"></a>Lösningsanvisningar

1. Anslut till HDInsight-kluster med hjälp av en Secure Shell (SSH)-klient. Mer information finns i [mer att läsa](#additional-reading-end).

2. Kör följande kommando på HDInsight-klustret som du vill exportera metaarkiv:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Det här kommandot genererar en fil med namnet allatables.sql.

3. Kopiera filen alltables.sql till det nya HDInsight-klustret och kör sedan följande kommando:

   ```apache
   hive -f alltables.sql
   ```

Koden i Lösningssteg förutsätter att datasökvägar för det nya klustret är samma som datasökvägar för det gamla klustret. Om datasökvägarna är olika, kan du manuellt redigera den genererade alltables.sql-filen för att återspegla ändringar.

### <a name="additional-reading"></a>Ytterligare resurser

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Hur hittar jag Hive loggar i ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Anslut till HDInsight-kluster med hjälp av SSH. Mer information finns i **mer att läsa**.

2. Om du vill visa Hive klientloggar, använder du följande kommando:

   ```apache
   /tmp/<username>/hive.log 
   ```

3. Om du vill visa Hive-metaarkiv loggar, använder du följande kommando:

   ```apache
   /var/log/hive/hivemetastore.log 
   ```

4. Om du vill visa Hiveserver loggar, använder du följande kommando:

   ```apache
   /var/log/hive/hiveserver2.log 
   ```

### <a name="additional-reading"></a>Ytterligare resurser

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Hur jag för att starta Hive-gränssnittet med specifika konfigurationer i ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Ange ett nyckel / värde-par för konfiguration när du startar Hive-gränssnittet. Mer information finns i [mer att läsa](#additional-reading-end).

   ```apache
   hive -hiveconf a=b 
   ```

2. Om du vill visa alla effektiva konfigurationer på Hive-gränssnittet, använder du följande kommando:

   ```apache
   hive> set;
   ```

   Exempelvis kan du använda följande kommando för att starta Hive-gränssnittet utan felsökningsloggning aktiverats på konsolen:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console 
   ```

### <a name="additional-reading"></a>Ytterligare resurser

- [Egenskaper för hive-konfiguration](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Hur jag för att analysera data i Apache Tez DAG på en kluster-kritisk väg?


### <a name="resolution-steps"></a>Lösningsanvisningar
 
1. Ansluta till HDInsight-kluster med hjälp av SSH för att analysera en Apache Tez riktad Acyklisk graf (DAG) i ett kluster-kritiska diagram. Mer information finns i [mer att läsa](#additional-reading-end).

2. Kör följande kommando i en kommandotolk:
   
   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Om du vill visa andra analysverktyg som kan användas för att analysera Tez DAG, använder du följande kommando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Du måste ange ett exempelprogram som det första argumentet.

   Giltigt programnamn inkluderar:
    - **ContainerReuseAnalyzer**: Skriva ut behållare återanvändning information i en grupp för Databastillgänglighet
    - **CriticalPath**: Hitta den kritiska vägen i DAG
    - **LocalityAnalyzer**: Skriva ut ort information finns i en grupp för Databastillgänglighet
    - **ShuffleTimeAnalyzer**: Analysera shuffle tidsinformation i en grupp för Databastillgänglighet
    - **SkewAnalyzer**: Analysera skeva informationen i en grupp för Databastillgänglighet
    - **SlowNodeAnalyzer**: Skriva ut nodinformation i en grupp för Databastillgänglighet
    - **SlowTaskIdentifier**: Skriva ut långsam delar av uppgifterna i en grupp för Databastillgänglighet
    - **SlowestVertexAnalyzer**: Skriva ut den långsammaste hörn information i en grupp för Databastillgänglighet
    - **SpillAnalyzer**: Skriva ut oljesanering information finns i en grupp för Databastillgänglighet
    - **TaskConcurrencyAnalyzer**: Skriva ut samtidighet aktivitetsinformation i en grupp för Databastillgänglighet
    - **VertexLevelCriticalPathAnalyzer**: Hitta den kritiska vägen på hörn nivå i en grupp för Databastillgänglighet


### <a name="additional-reading"></a>Ytterligare resurser

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Hur jag för att hämta Tez DAG data från ett kluster?


#### <a name="resolution-steps"></a>Lösningsanvisningar

Det finns två sätt att samla in data för Tez DAG:

- Från kommandoraden:
 
    Anslut till HDInsight-kluster med hjälp av SSH. Kör följande kommando i Kommandotolken:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Använda Ambari Tez-vy:
   
  1. Gå till Ambari. 
  2. Gå till Tez-vy (under ikonen paneler i det övre högra hörnet). 
  3. Välj den DAG som du vill visa.
  4. Välj **ladda ned data**.

### <a name="additional-reading-end"></a>Ytterligare resurser

[Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](hdinsight-troubleshoot-guide.md)




