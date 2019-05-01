---
title: Felsöka YARN i Azure HDInsight
description: Få svar på vanliga frågor om hur du arbetar med Apache Hadoop YARN och Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 4ca3dddb2b5ea4897ff82f1d91e88dc656305453
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721574"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Felsöka Apache Hadoop YARN med Azure HDInsight

Läs mer om de viktigaste problemen och sina lösningar när du arbetar med Apache Hadoop YARN-nyttolaster i Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hur skapar jag en ny YARN-kö på ett kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar 

Använd följande steg i Ambari för att skapa en ny YARN-kö och för att balansera kapacitetstilldelning bland alla köer. 

I det här exemplet, två befintliga köer (**standard** och **thriftsvr**) både ändras från 50% kapacitet till 25% kapacitet som får den nya kön (spark) 50% kapaciteten.

| Kö | Kapacitet | Maximal kapacitet |
| --- | --- | --- |
| standard | 25 % | 50% |
| thrftsvr | 25 % | 50% |
| Spark | 50% | 50% |

1. Välj den **Ambari-vyer** ikonen och välj sedan mönstret rutnätet. Välj sedan **YARN köhanteraren**.

    ![Välj ikonen Ambari-vyer](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Välj den **standard** kö.

    ![Välj standardkö](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. För den **standard** kö, ändra den **kapacitet** från 50% till 25%. För den **thriftsvr** kö, ändra den **kapacitet** till 25%.

    ![Ändra kapaciteten till 25% för standard och thriftsvr köer](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Om du vill skapa en ny kö, Välj **Lägg till kö**.

    ![Välj Lägg till kö](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Namnge den nya kön.

    ![Namn på kön Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Lämna den **kapacitet** värden på 50% och välj sedan den **åtgärder** knappen.

    ![Välj knappen åtgärder](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Välj **spara och uppdatera köer**.

    ![Välj Spara och uppdatera köer](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Dessa ändringar visas direkt på Scheduler YARN-Användargränssnittet.

### <a name="additional-reading"></a>Ytterligare resurser

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hur jag för att hämta YARN-loggar från ett kluster?


### <a name="resolution-steps"></a>Lösningsanvisningar 

1. Anslut till HDInsight-kluster med hjälp av en Secure Shell (SSH)-klient. Mer information finns i [mer att läsa](#additional-reading-2).

2. Om du vill visa alla program-ID för YARN-program som körs, kör du följande kommando:

    ```apache
    yarn top
    ```
    ID: N visas i den **APPLICATIONID** kolumn. Du kan hämta loggar från den **APPLICATIONID** kolumn.

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

3. Om du vill hämta loggar för YARN-behållare för alla program huvudservrar, använder du följande kommando:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet amlogs.txt. 

4. Om du vill hämta loggar för YARN-behållare för endast det senaste programmet master, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Det här kommandot skapar en loggfil med namnet latestamlogs.txt. 

4. Om du vill hämta loggar för YARN-behållare för de första två program huvudservrarna, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Det här kommandot skapar en loggfil med namnet first2amlogs.txt. 

5. Om du vill hämta alla loggar för YARN-behållare, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Det här kommandot skapar en loggfil med namnet logs.txt. 

6. För att hämta YARN-logg för behållare för en specifik behållare, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Det här kommandot skapar en loggfil med namnet containerlogs.txt.

### <a name="additional-reading-2"></a>Ytterligare resurser

- [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN-begrepp och program](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](hdinsight-troubleshoot-guide.md)
