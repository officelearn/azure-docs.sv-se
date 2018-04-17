---
title: Felsöka YARN med Azure HDInsight | Microsoft Docs
description: Få svar på vanliga frågor om hur du arbetar med Apache Hadoop YARN och Azure HDInsight.
keywords: Azure HDInsight, YARN, vanliga frågor och svar, felsökningsguide för vanliga frågor
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: ''
editor: ''
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 338d678fb31a86046b8bc6424d0e8aac2de1a0c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>Felsöka YARN med Azure HDInsight

Läs mer om de vanligaste problemen och sina lösningar när du arbetar med Apache Hadoop YARN nyttolaster i Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Hur skapar jag en ny YARN-kö på ett kluster?


### <a name="resolution-steps"></a>Lösningsanvisningar 

Använd följande steg i Ambari för att skapa en ny YARN-kö och balansera kapacitet fördelas mellan alla köer. 

I det här exemplet två befintliga köer (**standard** och **thriftsvr**) både ändras från 50% kapacitet till 25% kapacitet, som ger den nya kön (spark) 50% kapaciteten.
| Kö | Kapacitet | Maximal kapacitet |
| --- | --- | --- | --- |
| standard | 25 % | 50% |
| thrftsvr | 25 % | 50% |
| Spark | 50% | 50% |

1. Välj den **Ambari Views** ikonen och välj sedan rutnät. Välj därefter **YARN Queue Manager**.

    ![Välj ikonen Ambari-vyer](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Välj den **standard** kön.

    ![Välj standardkön](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. För den **standard** kö, ändra den **kapacitet** från 50% 25%. För den **thriftsvr** kö, ändra den **kapacitet** 25%.

    ![Ändra kapacitet till 25% för standard- och thriftsvr köer](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Om du vill skapa en ny kö **lägga till kön**.

    ![Välj Lägg till kön](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Namnge den nya kön.

    ![Namnet kön Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Lämna den **kapacitet** värdena vid 50% och välj sedan den **åtgärder** knappen.

    ![Välj knappen åtgärder](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Välj **spara och uppdatera köer**.

    ![Välj Spara och uppdatera köer](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Dessa ändringar visas omedelbart på YARN Scheduler-Användargränssnittet.

### <a name="additional-reading"></a>Ytterligare resurser

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Hur hämta YARN-loggar från ett kluster?


### <a name="resolution-steps"></a>Lösningsanvisningar 

1. Ansluta till HDInsight-kluster med hjälp av en klient med SSH (Secure Shell). Mer information finns i [ytterligare resurser](#additional-reading-2).

2. Om du vill visa en lista över alla program-ID YARN-program som körs för närvarande, kör du följande kommando:

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

3. Om du vill hämta YARN-loggar för behållaren för alla program original, använder du följande kommando:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Detta kommando skapar en loggfil med namnet amlogs.txt. 

4. Hämta YARN-loggar för behållaren för endast det senaste programmet master, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Detta kommando skapar en loggfil med namnet latestamlogs.txt. 

4. Hämta YARN-loggar för behållaren för de första två program original, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    Detta kommando skapar en loggfil med namnet first2amlogs.txt. 

5. Om du vill hämta alla loggar i YARN-behållare, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Detta kommando skapar en loggfil med namnet logs.txt. 

6. Om du vill hämta YARN-logg för behållare för en specifik behållare, använder du följande kommando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    Detta kommando skapar en loggfil med namnet containerlogs.txt.

### <a name="additional-reading-2"></a>Ytterligare resurser

- [Ansluta till HDInsight (Hadoop) med hjälp av SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN begrepp och program](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](hdinsight-troubleshoot-guide.md)







