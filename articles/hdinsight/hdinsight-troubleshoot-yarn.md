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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272206"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Felsöka Apache Hadoop YARN med Azure HDInsight

Lär dig mer om de vanligaste problemen och deras lösningar när du arbetar med Apache Hadoop YARN nyttolaster i Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hur skapar jag en ny YARN-kö i ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

Använd följande steg i Ambari för att skapa en ny YARN-kö och balansera sedan kapacitetsallokeringen mellan alla köer.

I det här exemplet ändras båda två befintliga köer **(standard** och **sparsamhetsvr)** från 50 % kapacitet till 25 % kapacitet, vilket ger den nya kön (gnista) 50 % kapacitet.

| Kö | Kapacitet | Maximal kapacitet |
| --- | --- | --- |
| standard | 25 % | 50 % |
| thrftsvr (thrftsvr) | 25 % | 50 % |
| spark | 50 % | 50 % |

1. Markera ikonen **Ambari-vyer** och välj sedan rutnätsmönstret. Välj sedan **YARN Queue Manager**.

    ![Apache Ambari instrumentpanel YARN Köhanterare](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Välj **standardkön.**

    ![Apache Ambari YARN välj standardkö](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. För **standardkön** ändrar du **kapaciteten** från 50 % till 25 %. För **sparsamhetsvrkön** ändrar du **kapaciteten** till 25 %.

    ![Ändra kapaciteten till 25 % för standard- och sparsamhetsvekar](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Om du vill skapa en ny kö väljer du **Lägg till kö**.

    ![Apache Ambari YARN instrumentpanel Lägg till kö](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Ge den nya kön ett namn.

    ![Namnkö för Apache Ambari YARN-instrumentpanelen](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Lämna **kapacitetsvärdena** på 50 % och välj sedan knappen **Åtgärder.**

    ![Apache Ambari YARN välj åtgärd](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Välj **Spara och uppdatera köer**.

    ![Välj Spara och uppdatera köer](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Dessa ändringar visas omedelbart på YARN Scheduler UI.

### <a name="additional-reading"></a>Ytterligare läsning

- [Apache Hadoop GARN KapacitetPlanerare](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hur laddar jag ner YARN-loggar från ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Anslut till HDInsight-klustret med hjälp av en SSH-klient (Secure Shell). Mer information finns i [Ytterligare läsning](#additional-reading-2).

1. Om du vill visa en lista över alla program-ID:er för YARN-programmen som körs kör du följande kommando:

    ```apache
    yarn top
    ```

    ID:na visas i kolumnen **APPLICATIONID.** Du kan hämta loggar från **applicationid-kolumnen.**

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

1. Om du vill hämta YARN-behållarloggar för alla programhanterare använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Med det här kommandot skapas en loggfil med namnet amlogs.txt.

1. Om du bara vill hämta YARN-behållarloggar för den senaste programbakgrunden använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Med det här kommandot skapas en loggfil med namnet latestamlogs.txt.

1. Om du vill hämta YARN-behållarloggar för de två första programbakgrunderna använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet first2amlogs.txt.

1. Så här hämtar du alla YARN-behållarloggar:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Med det här kommandot skapas en loggfil med namnet logs.txt.

1. Om du vill hämta YARN-behållarloggen för en viss behållare använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Med det här kommandot skapas en loggfil med namnet containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Ytterligare läsning

- [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN koncept och applikationer](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

- Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
