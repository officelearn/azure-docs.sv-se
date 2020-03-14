---
title: Felsöka YARN i Azure HDInsight
description: Få svar på vanliga frågor om hur du arbetar med Apache Hadoop YARN och Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272206"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Felsöka Apache Hadoop garn med hjälp av Azure HDInsight

Läs mer om de viktigaste problemen och sina lösningar när du arbetar med Apache Hadoop YARN-nyttolaster i Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hur skapar jag en ny YARN-kö på ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

Använd följande steg i Ambari för att skapa en ny YARN-kö och för att balansera kapacitetstilldelning bland alla köer.

I det här exemplet ändras två befintliga köer (**standard** -och **thriftsvr**) från 50% kapacitet till 25% kapacitet, vilket ger den nya kön (Spark) 50% kapacitet.

| Kö | Kapacitet | Maximal kapacitet |
| --- | --- | --- |
| standard | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Välj ikonen **Ambari views** och välj sedan rutnäts mönstret. Välj sedan **garn Queue Manager**.

    ![Apache Ambari Dashboard garn Queue Manager](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Välj **standard** kön.

    ![Apache Ambari-garn Välj standard kön](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. Ändra **kapaciteten** från 50% till 25% för **standard** kön. Ändra **kapaciteten** till 25% för **thriftsvr** -kön.

    ![Ändra kapaciteten till 25% för standard och thriftsvr köer](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Om du vill skapa en ny kö väljer du **Lägg till kö**.

    ![Apache Ambari garn Dashboard Lägg till kö](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Namnge den nya kön.

    ![Apache Ambari garn instrument panel, namn kö](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Lämna **kapacitets** värden på 50% och välj sedan knappen **åtgärder** .

    ![Apache Ambari-garn Välj åtgärd](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Välj **Spara och uppdatera köer**.

    ![Välj Spara och uppdatera köer](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Dessa ändringar visas direkt på Scheduler YARN-Användargränssnittet.

### <a name="additional-reading"></a>Ytterligare resurser

- [Apache Hadoop garn CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hur jag för att hämta YARN-loggar från ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Anslut till HDInsight-kluster med hjälp av en Secure Shell (SSH)-klient. Mer information finns i [Ytterligare läsning](#additional-reading-2).

1. Om du vill visa alla program-ID för YARN-program som körs, kör du följande kommando:

    ```apache
    yarn top
    ```

    ID: na visas i kolumnen **APPLICATIONID** . Du kan hämta loggar från kolumnen **APPLICATIONID** .

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Om du vill hämta loggar för YARN-behållare för alla program huvudservrar, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet amlogs.txt.

1. Om du vill hämta loggar för YARN-behållare för endast det senaste programmet master, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet latestamlogs.txt.

1. Om du vill hämta loggar för YARN-behållare för de första två program huvudservrarna, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet first2amlogs.txt.

1. Om du vill hämta alla loggar för YARN-behållare, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Det här kommandot skapar en loggfil med namnet logs.txt.

1. För att hämta YARN-logg för behållare för en specifik behållare, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet containerlogs.txt.

### <a name="additional-reading-2"></a>Ytterligare läsning

- [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop garn koncept och program](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
