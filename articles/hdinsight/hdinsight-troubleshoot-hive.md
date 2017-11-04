---
title: "Felsöka Hive med Azure HDInsight | Microsoft Docs"
description: "Få svar på vanliga frågor om hur du arbetar med Apache Hive och Azure HDInsight."
keywords: "Azure HDInsight Hive, vanliga frågor och svar, felsökningsguide för vanliga frågor"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: dharmeshkakadia
ms.openlocfilehash: 398cc7cd0fa815f12ba5b503a1328688e1159eee
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-hive-by-using-azure-hdinsight"></a>Felsöka Hive med Azure HDInsight

Läs mer om de vanligaste frågorna och sina lösningar när du arbetar med Apache Hive nyttolaster i Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hur gör exportera en Hive metastore och importera det till ett annat kluster?


### <a name="resolution-steps"></a>Lösningssteg

1. Ansluta till HDInsight-kluster med hjälp av en klient med SSH (Secure Shell). Mer information finns i [ytterligare resurser](#additional-reading-end).

2. Kör följande kommando på HDInsight-klustret som du vill exportera metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

  Det här kommandot genererar en fil med namnet allatables.sql.

3. Kopiera filen alltables.sql till det nya HDInsight-klustret och kör sedan följande kommando:

  ```apache
  hive -f alltables.sql
  ```

Koden i Lösningssteg förutsätter att datasökvägar på det nya klustret är samma som datasökvägar på det gamla klustret. Om datasökvägar är olika, kan du redigera genererade alltables.sql-filen för att återspegla ändringar manuellt.

### <a name="additional-reading"></a>Ytterligare resurser

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Hur jag för att hitta Hive loggar på ett kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. Anslut till HDInsight-kluster med hjälp av SSH. Mer information finns i **ytterligare resurser**.

2. Om du vill visa Hive Klientloggfiler, använder du följande kommando:

  ```apache
  /tmp/<username>/hive.log 
  ```

3. Om du vill visa Hive metastore loggarna använder du följande kommando:

  ```apache
  /var/log/hive/hivemetastore.log 
  ```

4. Om du vill visa Hiveserver loggarna använder du följande kommando:

  ```apache
  /var/log/hive/hiveserver2.log 
  ```

### <a name="additional-reading"></a>Ytterligare resurser

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Hur jag för att starta Hive-gränssnittet med specifika konfigurationer i ett kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. Ange ett nyckel / värde-par för konfiguration när du startar Hive-gränssnittet. Mer information finns i [ytterligare resurser](#additional-reading-end).

  ```apache
  hive -hiveconf a=b 
  ```

2. Om du vill visa en lista med alla effektiva konfigurationer på Hive-gränssnittet, använder du följande kommando:

  ```apache
  hive> set;
  ```

  Till exempel använda följande kommando för att starta Hive-gränssnittet med felsökningsloggning aktiverats på konsolen:

  ```apache
  hive -hiveconf hive.root.logger=ALL,console 
  ```

### <a name="additional-reading"></a>Ytterligare resurser

- [Egenskaper för hive-konfiguration](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Hur jag för att analysera Tez DAG data på ett kluster kritiska?


### <a name="resolution-steps"></a>Lösningssteg
 
1. Anslut till HDInsight-kluster med hjälp av SSH för att analysera en Apache Tez riktat acykliskt diagram (DAG) i ett kluster-kritiskt diagram. Mer information finns i [ytterligare resurser](#additional-reading-end).

2. Kör följande kommando vid en kommandotolk:
   
  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
  ```

3. Om du vill visa en lista med andra analyzers som kan användas för att analysera Tez DAG, använder du följande kommando:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
  ```

  Du måste ange ett exempelprogram som första argument.

  Giltigt programnamn inkluderar:
    - **ContainerReuseAnalyzer**: Skriv ut behållaren återanvändning i DAG
    - **CriticalPath**: hitta kritiska i DAG
    - **LocalityAnalyzer**: Skriv ut ort i DAG
    - **ShuffleTimeAnalyzer**: analysera blanda tidsinformationen i DAG
    - **SkewAnalyzer**: analysera skeva informationen i DAG
    - **SlowNodeAnalyzer**: skriva noden information i DAG
    - **SlowTaskIdentifier**: Skriv ut långsam aktivitetsinformation i DAG
    - **SlowestVertexAnalyzer**: Skriv ut långsammaste hörn i DAG
    - **SpillAnalyzer**: Skriv ut oljesanering detaljer i DAG
    - **TaskConcurrencyAnalyzer**: Skriv ut samtidighet aktivitetsinformation i DAG
    - **VertexLevelCriticalPathAnalyzer**: hitta kritiska nivån hörn i DAG


### <a name="additional-reading"></a>Ytterligare resurser

- [Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Hur hämta Tez DAG data från ett kluster?


#### <a name="resolution-steps"></a>Lösningssteg

Det finns två sätt att samla in data för Tez DAG:

- Från kommandoraden:
 
    Anslut till HDInsight-kluster med hjälp av SSH. Kör följande kommando i Kommandotolken:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Använda Ambari Tez vy:
   
  1. Gå till Ambari. 
  2. Gå till vyn Tez (under ikonen paneler i det övre högra hörnet). 
  3. Välj den DAG du vill visa.
  4. Välj **ladda ned data**.

### <a name="additional-reading-end"></a>Ytterligare resurser

[Ansluta till ett HDInsight-kluster med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](hdinsight-troubleshoot-guide.md)




