---
title: Felsöka Spark i Azure HDInsight
description: Få svar på vanliga frågor om hur du arbetar med Apache Spark och Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 2108de8988e973644e0622a4b76a04408949298e
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635161"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Felsöka Apache Spark med Azure HDInsight

Lär dig mer om de viktigaste problemen och sina lösningar när du arbetar med [Apache Spark](https://spark.apache.org/) nyttolaster i [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av Apache Ambari på kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

Konfigurationsvärden för den här proceduren har tidigare angetts i HDInsight. För att fastställa vilka Spark konfigurationer måste anges och vilka värden, se [vad som orsakar ett Apache Spark-program OutofMemoryError undantag](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. Välj i listan över kluster, **Spark2**.

    ![Välj klustret från listan](./media/apache-troubleshoot-spark/update-config-1.png)

2. Välj den **Peeringkonfigurationer** fliken.

    ![Välj fliken konfigurationer](./media/apache-troubleshoot-spark/update-config-2.png)

3. Välj i listan över konfigurationer, **anpassad-spark2-standardvärden**.

    ![Ange standardinställningar för anpassade spark](./media/apache-troubleshoot-spark/update-config-3.png)

4. Leta efter värdeinställning som du behöver justera som **spark.executor.memory**. I det här fallet, värdet för **4608m** högt.

    ![Välj fältet spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Ange värdet till den rekommenderade inställningen. Värdet **2 048 m** rekommenderas för den här inställningen.

    ![Ändra värdet till 2 048 m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Spara värdet och sedan spara konfigurationen. I verktygsfältet väljer **spara**.

    ![Spara inställningen och konfiguration](./media/apache-troubleshoot-spark/update-config-6a.png)

    Du får ett meddelande om några konfigurationer som behöver åtgärdas. Observera objekt och välj sedan **fortsätter ändå**. 

    ![Välj Fortsätt ändå](./media/apache-troubleshoot-spark/update-config-6b.png)

    Skriv en anteckning om ändringar i konfigurationen och välj sedan **spara**.

    ![Ange en kommentar om de ändringar du gjort](./media/apache-troubleshoot-spark/update-config-6c.png)

7. När en konfiguration sparas uppmanas du att starta om tjänsten. Välj **starta om**.

    ![Välj omstart](./media/apache-troubleshoot-spark/update-config-7a.png)

    Bekräfta omstarten.

    ![Välj bekräfta starta om alla](./media/apache-troubleshoot-spark/update-config-7b.png)

    Du kan granska de processer som körs.

    ![Granska processer som körs](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Du kan lägga till konfigurationer. Välj i listan över konfigurationer, **anpassad-spark2-standardvärden**, och välj sedan **Lägg till egenskap**.

    ![Välj Lägg till egenskap](./media/apache-troubleshoot-spark/update-config-8.png)

9. Definiera en ny egenskap. Du kan definiera en enskild egenskap med en dialogruta för specifika inställningar, till exempel datatypen. Eller du kan definiera flera egenskaper med hjälp av en definition av per rad. 

    I det här exemplet på **spark.driver.memory** egenskapen har definierats med värdet **4g**.

    ![Definiera nya egenskapen](./media/apache-troubleshoot-spark/update-config-9.png)

10. Spara konfigurationen och sedan starta om tjänsten enligt beskrivningen i steg 6 och 7.

Dessa ändringar är hela klustret, men kan åsidosättas när du skickar in Spark-jobbet.

### <a name="additional-reading"></a>Ytterligare resurser

[Jobböverföring för Apache Spark i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av en Jupyter-anteckningsbok i kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. För att fastställa vilka Spark konfigurationer måste anges och vilka värden, se [vad som orsakar ett Apache Spark-program OutofMemoryError undantag](#what-causes-a-spark-application-outofmemoryerror-exception).

2. I den första cellen i Jupyter-anteckningsbok när den **%% konfigurera** direktiv, ange Spark-konfigurationer i ett giltigt JSON-format. Ändra de faktiska värdena efter behov:

    ![Lägg till en konfiguration](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Ytterligare resurser

[Jobböverföring för Apache Spark i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av Apache Livy på kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. För att fastställa vilka Spark konfigurationer måste anges och vilka värden, se [vad som orsakar ett Apache Spark-program OutofMemoryError undantag](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Skicka Spark-programmet till Livy med hjälp av en REST-klient som cURL. Använda ett kommando som liknar följande. Ändra de faktiska värdena efter behov:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Ytterligare resurser

[Jobböverföring för Apache Spark i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hur konfigurerar jag ett Apache Spark program med hjälp av spark-submit på kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. För att fastställa vilka Spark konfigurationer måste anges och vilka värden, se [vad som orsakar ett Apache Spark-program OutofMemoryError undantag](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Starta spark-shell med hjälp av ett kommando som liknar följande. Ändra det faktiska värdet av konfigurationer efter behov: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Ytterligare resurser

[Jobböverföring för Apache Spark i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-an-apache-spark-application-outofmemoryerror-exception"></a>Vad som orsakar en OutofMemoryError programundantag Apache Spark?

### <a name="detailed-description"></a>Detaljerad beskrivning

Spark-programmet misslyckas med följande typer av undantag utan felhantering:

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Möjlig orsak

Den mest troliga orsaken till det här undantaget är att det finns inte tillräckligt med heap-minne har allokerats till Java-datorer (JVMs). Dessa JVMs startas som utförare eller drivrutiner som en del av Spark-programmet. 

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Avgöra den maximala storleken för data Spark program hanterar. Du kan göra en gissning baserat på den maximala storleken för indata, mellanliggande data som produceras av omvandlar indata och utdata som skapas när programmet är ytterligare transformering av mellanliggande data. Den här processen kan vara en iterativ om du inte göra en formell uppskattning inledningsvis. 

2. Se till att HDInsight-kluster som du ska använda har tillräckligt med resurser när det gäller minne och kärnor för Spark-programmet. Du kan fastställa det här genom att visa avsnittet cluster metrics i YARN-Användargränssnittet för värdena för **använt minne** vs. **Det totala minnet**, och **virtuella kärnor som används** vs. **Totala antalet virtuella kärnor**.

3. Ange följande Spark-konfigurationer till lämpliga värden som får innehålla högst 90% av det tillgängliga minnet och kärnor. Värdena bör vara väl inom minneskraven för Spark-program: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Att beräkna den totala mängden minne som används av alla executors: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Att beräkna den totala mängden minne som används av drivrutinen:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Ytterligare resurser

- [Översikt över Apache Spark minne](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Felsöka ett Apache Spark-program på ett HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)

