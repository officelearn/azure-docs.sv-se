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
ms.openlocfilehash: 8931f9b09836d30f95e25cee245932475c3cf64c
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018413"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Felsöka Apache Spark med Azure HDInsight

Lär dig mer om de vanligaste problemen och deras lösningar när du arbetar med Apache Spark nytto laster i [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av Apache Ambari på kluster?

Konfigurations värden för Spark kan justeras för att undvika `OutofMemoryError` ett Apache Spark program undantag. Följande steg visar standard konfigurations värden för Spark i Azure HDInsight:

1. Logga in på Ambari på `https://CLUSTERNAME.azurehdidnsight.net` med dina autentiseringsuppgifter för klustret. Den första skärmen visar en översikts instrument panel. Det finns små kosmetiska skillnader mellan HDInsight 3,6 och 4,0.

1. Navigera till **Spark2** > -**konfigurationer**.

    ![Välj fliken konfigurationer](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. I listan med konfigurationer väljer och expanderar du **Custom-spark2-defaults**.

1. Leta efter värdeinställning som du behöver justera som **spark.executor.memory**. I det här fallet är värdet för **9728m** för högt.

    ![Ange standardinställningar för anpassade spark](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Ange värdet till den rekommenderade inställningen. Värdet **2 048 m** rekommenderas för den här inställningen.

1. Spara värdet och sedan spara konfigurationen. Välj **Spara**.

    ![Ändra värdet till 2 048 m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Skriv en anteckning om ändringar i konfigurationen och välj sedan **spara**.

    ![Ange en kommentar om de ändringar du gjort](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Du får ett meddelande om några konfigurationer som behöver åtgärdas. Observera objekt och välj sedan **fortsätter ändå**.

    ![Välj Fortsätt ändå](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. När en konfiguration sparas uppmanas du att starta om tjänsten. Välj **starta om**.

    ![Välj omstart](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Bekräfta omstarten.

    ![Välj bekräfta starta om alla](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Du kan granska de processer som körs.

    ![Granska processer som körs](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Du kan lägga till konfigurationer. Välj i listan över konfigurationer, **anpassad-spark2-standardvärden**, och välj sedan **Lägg till egenskap**.

    ![Välj Lägg till egenskap](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definiera en ny egenskap. Du kan definiera en enskild egenskap med en dialogruta för specifika inställningar, till exempel datatypen. Eller du kan definiera flera egenskaper med hjälp av en definition av per rad.

    I det här exemplet på **spark.driver.memory** egenskapen har definierats med värdet **4g**.

    ![Definiera nya egenskapen](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Spara konfigurationen och sedan starta om tjänsten enligt beskrivningen i steg 6 och 7.

Dessa ändringar är hela klustret, men kan åsidosättas när du skickar in Spark-jobbet.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av en Jupyter-anteckningsbok i kluster?

I den första cellen i Jupyter-anteckningsbok när den **%% konfigurera** direktiv, ange Spark-konfigurationer i ett giltigt JSON-format. Ändra de faktiska värdena efter behov:

![Lägg till en konfiguration](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hur konfigurerar jag ett Apache Spark-program med hjälp av Apache Livy på kluster?

Skicka Spark-programmet till Livy med hjälp av en REST-klient som cURL. Använda ett kommando som liknar följande. Ändra de faktiska värdena efter behov:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hur konfigurerar jag ett Apache Spark program med hjälp av spark-submit på kluster?

Starta spark-shell med hjälp av ett kommando som liknar följande. Ändra det faktiska värdet av konfigurationer efter behov:

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
