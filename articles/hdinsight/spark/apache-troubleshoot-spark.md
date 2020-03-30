---
title: Felsöka Apache Spark i Azure HDInsight
description: Få svar på vanliga frågor om hur du arbetar med Apache Spark och Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271946"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Felsöka Apache Spark med Azure HDInsight

Lär dig mer om de vanligaste problemen och deras lösningar när du arbetar med Apache Spark-nyttolaster i [Apache Ambari.](https://ambari.apache.org/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hur gör jag för att konfigurera ett Apache Spark-program med Apache Ambari i kluster?

Spark-konfigurationsvärden kan justeras för `OutofMemoryError` att undvika ett apache spark-programundantag. Följande steg visar standardinställningar för Spark-konfiguration i Azure HDInsight:

1. Logga in på Ambari med `https://CLUSTERNAME.azurehdidnsight.net` dina klusterreferenser. Den första skärmen visar en översiktsinstrumentpanel. Det finns små kosmetiska skillnader mellan HDInsight 3,6 och 4,0.

1. Navigera till **Spark2** > **Configs**.

    ![Välj fliken Configs](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. I listan över konfigurationer väljer och expanderar **du Standardinställningar för anpassad gnista2**.

1. Leta efter den värdeinställning som du behöver justera, till exempel **spark.executor.memory**. I det här fallet är värdet **på 9728m** för högt.

    ![Välj standardvärden för anpassad gnista](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Ange värdet till den rekommenderade inställningen. Värdet **2048m** rekommenderas för den här inställningen.

1. Spara värdet och spara sedan konfigurationen. Välj **Spara**.

    ![Ändra värde till 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Skriv en anteckning om konfigurationsändringarna och välj sedan **Spara**.

    ![Ange en anteckning om de ändringar du har gjort](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Du meddelas om några konfigurationer behöver åtgärdas. Notera objekten och välj sedan **Fortsätt ändå**.

    ![Välj Fortsätt ändå](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. När en konfiguration sparas uppmanas du att starta om tjänsten. Välj **Starta om**.

    ![Välj omstart](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Bekräfta omstarten.

    ![Välj Bekräfta omstart alla](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Du kan granska de processer som körs.

    ![Granska processer som körs](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Du kan lägga till konfigurationer. I listan över konfigurationer väljer du Standardinställningar för **anpassad gnista2**och väljer sedan **Lägg till egenskap**.

    ![Välj lägg till egenskap](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definiera en ny egenskap. Du kan definiera en enskild egenskap genom att använda en dialogruta för specifika inställningar, till exempel datatypen. Du kan också definiera flera egenskaper med en definition per rad.

    I det här exemplet definieras egenskapen **spark.driver.memory** med värdet **4g**.

    ![Definiera ny egenskap](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Spara konfigurationen och starta sedan om tjänsten enligt beskrivningen i steg 6 och 7.

Dessa ändringar är klusteromfattande men kan åsidosättas när du skickar Spark-jobbet.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hur gör jag för att konfigurera ett Apache Spark-program med hjälp av en Jupyter Notebook i kluster?

I den första cellen i jupyter-anteckningsboken, efter att **%%konfigurerat** direktivet, anger du Spark-konfigurationerna i giltigt JSON-format. Ändra de faktiska värdena efter behov:

![Lägga till en konfiguration](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hur gör jag för att konfigurera ett Apache Spark-program med Apache Livy i kluster?

Skicka Spark-programmet till Livy med hjälp av en REST-klient som cURL. Använd ett kommando som liknar följande. Ändra de faktiska värdena efter behov:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hur gör jag för att konfigurera ett Apache Spark-program med spark-submit i kluster?

Starta spark-shell med hjälp av ett kommando som liknar följande. Ändra det faktiska värdet för konfigurationerna efter behov:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Ytterligare läsning

[Apache Spark-jobbinlämning på HDInsight-kluster](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* [Översikt över hantering av sparkminne](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Felsökning Spark-programmet på HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
