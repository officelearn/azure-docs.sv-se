---
title: "Hantera resurser för Apache Spark-kluster i Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder hantera resurser för Spark-kluster i Azure HDInsight för bättre prestanda."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 24f6ce9c22aff727ef597cd7bed0c15b260b8aa0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Hantera resurser för Apache Spark-kluster i Azure HDInsight 

I den här artikeln beskrivs hur du kan komma åt gränssnitt som Ambari UI, YARN-Användargränssnittet och servern Spark historik som är associerade med Spark-kluster. Du också information om hur du ställer in klusterkonfigurationen för optimala prestanda.

**Krav:**

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-launch-the-ambari-web-ui"></a>Hur jag för att starta Ambari-Webbgränssnittet?
1. På startsidan i [Azure Portal](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.
2. Spark-kluster, klickar du på **instrumentpanelen**. När du uppmanas ange administratörsautentiseringsuppgifterna för Spark-klustret.

    ![Starta Ambari](./media/apache-spark-resource-manager/hdinsight-launch-cluster-dashboard.png "starta hanteraren för filserverresurser")
3. Detta ska starta Ambari-Webbgränssnittet som visas i skärmbilden.

    ![Ambari-webbgränssnittet](./media/apache-spark-resource-manager/ambari-web-ui.png "Ambari-webbgränssnittet")   

## <a name="how-do-i-launch-the-spark-history-server"></a>Hur jag för att starta servern Spark historik?
1. På startsidan i [Azure Portal](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan).
2. I klusterbladet under **snabblänkar**, klickar du på **Klusterinstrumentpanel**. I den **Klusterinstrumentpanel** bladet, klickar du på **Spark historik Server**.

    ![Väck historik Server](./media/apache-spark-resource-manager/launch-history-server.png "Väck historik Server")

    När du uppmanas ange administratörsautentiseringsuppgifterna för Spark-klustret.

## <a name="how-do-i-launch-the-yarn-ui"></a>Hur jag för att starta Användargränssnittet för Yarn?
Du kan använda YARN-Användargränssnittet för att övervaka program som körs i Spark-klustret.

1. Klusterbladet klickar du på **Klusterinstrumentpanel**, och klicka sedan på **YARN**.

    ![Starta YARN-Användargränssnittet](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Alternativt kan du starta YARN-Användargränssnittet från Ambari UI. Om du vill starta Ambari UI, kluster-bladet klickar du på **Klusterinstrumentpanel**, och klicka sedan på **instrumentpanelen för HDInsight-klustret**. Ambari UI, klicka på **YARN**, klickar du på **snabblänkar**, klicka på den aktiva Resource Manager och klicka sedan på **Resource Manager UI**.
   >
   >

## <a name="what-is-the-optimum-cluster-configuration-to-run-spark-applications"></a>Vad är optimal klusterkonfigurationen att köra Spark-program?
De tre viktiga parametrar som kan användas för konfiguration av Spark beroende på kraven för application är `spark.executor.instances`, `spark.executor.cores`, och `spark.executor.memory`. En utförare är en process startas för ett Spark-program. Körs på arbetsnoden och ansvarar för att utföra uppgifter för programmet. Standardantalet executors och utföraren storleken för varje kluster beräknas baserat på antalet arbetarnoder och nodstorlek worker. Den här informationen lagras i `spark-defaults.conf` head noderna i klustret.

De tre konfigurationsparametrarna kan konfigureras på klusternivå (för alla program som körs på klustret) eller kan anges för varje enskilt program.

### <a name="change-the-parameters-using-ambari-ui"></a>Ändra parametrarna med Ambari UI
1. Ambari UI klickar du på **Spark**, klickar du på **Contigs**, och expandera sedan **anpassad spark-standarder**.

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Standardvärdena är bra att ha 4 Spark program körs samtidigt på klustret. Du kan ändra dessa värden i användargränssnittet, enligt nedan.

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Klicka på **spara** att spara ändringar i konfigurationen. Överst på sidan uppmanas du att starta om alla påverkade tjänster. Klicka på **starta om**.

    ![Starta om tjänsterna](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrarna för ett program som körs i Jupyter-anteckningsbok
Du kan använda för program som körs i Jupyter-anteckningsbok den `%%configure` Magiskt tal för att göra ändringar i konfigurationen. Vi rekommenderar måste du göra dessa ändringar i början av programmet innan du kör din första kodcellen. Detta säkerställer att konfigurationen tillämpas Livius-sessionen när den skapas. Om du vill ändra konfigurationen senare i programmet, måste du använda den `-f` parameter. Dock av du gör det försvinner alla förlopp i programmet.

Kodfragmentet nedan visar hur du ändrar konfigurationen för ett program som körs i Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurationsparametrar som måste överföras i som en JSON-sträng och måste vara på nästa rad efter magic, som visas i Exempelkolumnen.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Ändra parametrarna för ett program som skickas med spark-skicka
Följande kommando är ett exempel på hur du ändrar konfigurationsparametrar för ett batch-program som har skickats med `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Ändra parametrarna för ett program som skickas med cURL
Följande kommando är ett exempel på hur du ändrar konfigurationsparametrar för ett batch-program som har skickats med hjälp av cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>Hur ändrar parametrarna på en Spark Thrift-servern?
Spark Thrift-servern tillhandahåller JDBC/ODBC-åtkomst till ett Spark-kluster och används för att tjänsten Spark SQL-frågor. Verktyg som Power BI Tableau osv. använda ODBC-protokollet för att kommunicera med Spark Thrift-servern för att köra Spark SQL-frågor som ett Spark-program. När ett Spark-kluster skapas två instanser av Spark Thrift-servern är igång, en på varje huvudnod. Varje Spark Thrift-servern visas som ett Spark-program i YARN-Användargränssnittet.

Spark Thrift-servern använder Spark dynamiska utförare allokering och därför den `spark.executor.instances` används inte. Spark Thrift-servern används i stället `spark.dynamicAllocation.minExecutors` och `spark.dynamicAllocation.maxExecutors` att ange antalet utförare. Konfigurationsparametrarna `spark.executor.cores` och `spark.executor.memory` används för att ändra storlek på utförare. Du kan ändra parametrarna som visas i följande steg.

* Expandera den **avancerade spark-thrift-sparkconf** kategori att uppdatera parametrarna `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, och `spark.executor.memory`.

    ![Konfigurera Spark thrift-servern](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Expandera den **anpassad spark-thrift-sparkconf** kategori att uppdatera parametern `spark.executor.cores`.

    ![Konfigurera Spark thrift-servern](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>Hur ändrar jag drivrutinen minnet på Spark Thrift-servern?
Spark Thrift-servern drivrutinsminne är konfigurerad att 25% av huvudnod ram-storlek, under förutsättning att den totala storleken för RAM-minne på Huvudnoden är större än 14GB. Du kan använda Ambari UI ändra minneskonfigurationen drivrutinen enligt nedan.

* Ambari UI klickar du på **Spark**, klickar du på **konfigurationerna**, expandera **avancerade spark env**, och ange värdet för **spark_thrift_cmd_opts**.

    ![Konfigurera Spark thrift-servern RAM-minne](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>Jag använder inte BI med Spark-kluster. Hur jag för att ta resurserna tillbaka?
Eftersom vi använder Spark dynamisk allokering, är endast resurserna som förbrukas av thrift-servern resurser för två program huvudservrar. Om du vill frigöra dessa resurser måste du stoppa tjänsterna Thrift-servern körs i klustret.

1. Ambari UI, i den vänstra rutan klickar du på **Spark**.
2. Klicka på nästa sida **Spark Thrift servrar**.

    ![Starta om thrift-servern](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Du bör se två headnodes som Spark Thrift-servern körs. Klicka på någon av headnodes.

    ![Starta om thrift-servern](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. Nästa sida visar alla tjänster som körs på den headnode. Klicka på listrutan bredvid Spark Thrift-servern i listan och klicka sedan på **stoppa**.

    ![Starta om thrift-servern](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Upprepa dessa steg på andra headnode samt.

## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>Jupyter-anteckningsböcker körs inte som förväntat. Hur kan jag starta om tjänsten?
Starta Ambari-Webbgränssnittet som ovan. I det vänstra navigeringsfönstret klickar du på **Jupyter**, klickar du på **tjänståtgärder**, och klicka sedan på **starta om alla**. Detta startar tjänsten Jupyter på alla headnodes.

    ![Restart Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Restart Jupyter")

## <a name="how-do-i-know-if-i-am-running-out-of-resources"></a>Hur vet jag om jag har slut på resurser?
Starta Yarn-Användargränssnittet som du ser ovan. Kontrollera värdena för i klustret mått tabell på skärmen, **minne används** och **minne totalt** kolumner. Om värdena 2 ligger mycket nära kanske det inte finns tillräckligt med resurser för att starta programmet nästa. Samma gäller den **VCores används** och **VCores totalt** kolumner. Även i vyn huvudsakliga om det finns ett program svaret inte i **godkända** tillstånd och inte att omvandla till **kör** eller **misslyckades** tillstånd, detta kan också vara ett tecken som det inte får tillräckligt med resurser för att starta.

    ![Resource Limit](./media/apache-spark-resource-manager/resource-limit.png "Resource Limit")

## <a name="how-do-i-kill-a-running-application-to-free-up-resource"></a>Hur jag för att avsluta ett program som körs för att frigöra resurser?
1. I Yarn-Användargränssnittet från den vänstra rutan klickar du på **kör**. Listan över program som körs och att bestämma programmet avslutas och klicka på den **ID**.

    ![Avsluta App1](./media/apache-spark-resource-manager/kill-app1.png "Kill App1")

2. Klicka på **avsluta programmet** i övre högra hörnet, klicka sedan på **OK**.

    ![Avsluta App2](./media/apache-spark-resource-manager/kill-app2.png "Kill App2")

## <a name="see-also"></a>Se även
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>För dataanalytiker

* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight-telemetridataanalys i HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Använda Caffe för distribuerade djup learning på Azure HDInsight Spark](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>För Spark-utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](apache-spark-eventhub-streaming.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
