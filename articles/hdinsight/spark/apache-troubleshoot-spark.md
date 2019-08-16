---
title: Felsöka Spark i Azure HDInsight
description: Få svar på vanliga frågor om hur du arbetar med Apache Spark och Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: c88136fee7a75b8f3b8e504b1ff1e6673a31bcf7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543170"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Felsöka Apache Spark med Azure HDInsight

Lär dig mer om de viktigaste problemen och sina lösningar när du arbetar med [Apache Spark](https://spark.apache.org/) nyttolaster i [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av Apache Ambari på kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

Konfigurations värden för Spark kan justeras för att undvika ett Apache Spark program OutofMemoryError-undantag. Följande steg visar standard konfigurations värden för Spark i Azure HDInsight: 

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

[Jobböverföring för Apache Spark i HDInsight-kluster](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av en Jupyter-anteckningsbok i kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. För att avgöra vilka Spark-konfigurationer som måste anges och vilka värden, se vad som orsakar ett Apache Spark program OutofMemoryError undantag.

2. I den första cellen i Jupyter-anteckningsbok när den **%% konfigurera** direktiv, ange Spark-konfigurationer i ett giltigt JSON-format. Ändra de faktiska värdena efter behov:

    ![Lägg till en konfiguration](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Ytterligare resurser

[Jobböverföring för Apache Spark i HDInsight-kluster](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av Apache Livy på kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. För att avgöra vilka Spark-konfigurationer som måste anges och vilka värden, se vad som orsakar ett Apache Spark program OutofMemoryError undantag. 

2. Skicka Spark-programmet till Livy med hjälp av en REST-klient som cURL. Använda ett kommando som liknar följande. Ändra de faktiska värdena efter behov:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Ytterligare resurser

[Jobböverföring för Apache Spark i HDInsight-kluster](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hur konfigurerar jag ett Apache Spark program med hjälp av spark-submit på kluster?

### <a name="resolution-steps"></a>Lösningsanvisningar

1. För att avgöra vilka Spark-konfigurationer som måste anges och vilka värden, se vad som orsakar ett Apache Spark program OutofMemoryError undantag.

2. Starta spark-shell med hjälp av ett kommando som liknar följande. Ändra det faktiska värdet av konfigurationer efter behov: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Ytterligare resurser

[Jobböverföring för Apache Spark i HDInsight-kluster](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* [Översikt över Spark-minnes hantering](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Felsöka Spark-program i HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
