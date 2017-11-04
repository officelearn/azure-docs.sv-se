---
title: "Felsöka Spark med Azure HDInsight | Microsoft Docs"
description: "Få svar på vanliga frågor om hur du arbetar med Apache Spark och Azure HDInsight."
keywords: "Azure HDInsight Spark, vanliga frågor och svar, felsökning guide, vanliga problem, konfiguration, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: b75aca7d9f78cb425099cc33034b44a80d7b6b81
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Felsöka Spark med Azure HDInsight

Läs mer om de vanligaste problemen och sina lösningar när du arbetar med Apache Spark nyttolaster i Apache Ambari.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>Hur konfigurerar jag ett Spark-program genom att använda Ambari på kluster?

### <a name="resolution-steps"></a>Lösningssteg

Tidigare konfigurationsvärden för den här proceduren har ställts in i HDInsight. För att fastställa vilka Spark måste anges och vilka värden, se [vad som orsakar en Spark OutofMemoryError undantag](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. Välj i listan över kluster **Spark2**.

    ![Markera klustret från listan](./media/apache-troubleshoot-spark/update-config-1.png)

2. Välj den **konfigurationerna** fliken.

    ![Välj fliken konfigurationer](./media/apache-troubleshoot-spark/update-config-2.png)

3. Välj i listan över konfigurationer, **anpassad-spark2-standarder**.

    ![Ange standardinställningar för anpassad spark](./media/apache-troubleshoot-spark/update-config-3.png)

4. Leta efter inställningen värdet som du måste justera som **spark.executor.memory**. I det här fallet är värdet för **4608m** är för hög.

    ![Markera fältet spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Ange värdet till den rekommenderade inställningen. Värdet **2 048 m** rekommenderas för den här inställningen.

    ![Ändra värdet till 2 048 m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Spara värdet och spara sedan konfigurationen. I verktygsfältet väljer **spara**.

    ![Spara inställningen och konfiguration](./media/apache-troubleshoot-spark/update-config-6a.png)

    Du meddelas om några konfigurationer behöver åtgärdas. Observera objekt och välj sedan **fortsätta ändå**. 

    ![Välj fortsätter ändå](./media/apache-troubleshoot-spark/update-config-6b.png)

    Skriv en anteckning om ändringar i konfigurationen och välj sedan **spara**.

    ![Ange en notering om de ändringar du gjort](./media/apache-troubleshoot-spark/update-config-6c.png)

7. När en konfiguration sparas, uppmanas du att starta om tjänsten. Välj **starta om**.

    ![Välj omstart](./media/apache-troubleshoot-spark/update-config-7a.png)

    Bekräfta omstarten.

    ![Välj bekräfta starta om alla](./media/apache-troubleshoot-spark/update-config-7b.png)

    Du kan granska de processer som körs.

    ![Granska processer som körs](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Du kan lägga till konfigurationer. Välj i listan över konfigurationer, **anpassad-spark2-standarder**, och välj sedan **lägga till egenskapen**.

    ![Välj Lägg till egenskap](./media/apache-troubleshoot-spark/update-config-8.png)

9. Definiera en ny egenskap. Du kan definiera en egenskap med hjälp av en dialogruta för specifika inställningar, till exempel datatypen. Eller så du kan definiera flera egenskaper med hjälp av en definition av per rad. 

    I det här exemplet i **spark.driver.memory** egenskapen har definierats med värdet **4g**.

    ![Definiera nya egenskap](./media/apache-troubleshoot-spark/update-config-9.png)

10. Spara konfigurationen och sedan starta om tjänsten enligt beskrivningen i steg 6 och 7.

De här ändringarna kan åsidosättas när du har skickat jobbet Spark är klustret.

### <a name="additional-reading"></a>Ytterligare resurser

[Jobbet för Spark i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hur konfigurerar jag ett Spark-program med hjälp av en Jupyter-anteckningsbok på kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. För att fastställa vilka Spark måste anges och vilka värden, se [vad som orsakar en Spark OutofMemoryError undantag](#what-causes-a-spark-application-outofmemoryerror-exception).

2. I den första cellen i Jupyter-anteckningsbok när den **%% konfigurera** direktiv, ange Spark-konfigurationer i ett giltigt JSON-format. Ändra värden efter behov:

    ![Lägga till en konfiguration](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Ytterligare resurser

[Jobbet för Spark i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>Hur konfigurerar jag ett Spark-program med hjälp av Livius på kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. För att fastställa vilka Spark måste anges och vilka värden, se [vad som orsakar en Spark OutofMemoryError undantag](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Skicka Spark-program till Livius med hjälp av REST-klient som cURL. Använda ett kommando som liknar följande. Ändra värden efter behov:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Ytterligare resurser

[Jobbet för Spark i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>Hur konfigurerar ett program med hjälp av spark-skicka Spark på kluster?

### <a name="resolution-steps"></a>Lösningssteg

1. För att fastställa vilka Spark måste anges och vilka värden, se [vad som orsakar en Spark OutofMemoryError undantag](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Starta spark-gränssnittet med ett kommando som liknar följande. Ändra det faktiska värdet för konfigurationerna som behövs: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Ytterligare resurser

[Jobbet för Spark i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Vad som orsakar en Spark OutofMemoryError programundantag?

### <a name="detailed-description"></a>Detaljerad beskrivning

Spark-programmet misslyckas med följande typer av undantagsfel utan felhantering:

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

Den mest troliga orsaken till det här undantaget är att det finns inte tillräckligt med minne för heap tilldelas Java virtuella datorer (JVMs). Dessa JVMs startas som executors eller drivrutiner som en del av Spark-program. 

### <a name="resolution-steps"></a>Lösningssteg

1. Avgöra den maximala storleken på data i Spark programmet hanterar. Du kan göra en gissning baserat på den maximala storleken för indata, mellanliggande data som produceras av omvandla indata och utdata som skapas när programmet ytterligare förändrar mellanliggande data. Den här processen kan vara en iterativ om du inte göra en första formella gissning. 

2. Se till att HDInsight-klustret som du ska använda har tillräckligt med resurser när det gäller minne och kärnor för Spark-program. Du kan bestämma det genom att visa avsnittet kluster mått i YARN-Användargränssnittet för värden på **minne används** vs. **Minne totalt**, och **VCores används** vs. **Totalt antal VCores**.

3. Ange följande Spark konfigurationer till lämpliga värden som inte får överstiga 90% av det tillgängliga minnet och kärnor. Värdena bör vara bra i minneskrav för Spark-program: 

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

- [Översikt över Spark minne hantering](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Felsöka ett Spark-program på ett HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)

