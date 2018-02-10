---
title: "Hantera resurser för Apache Spark-kluster i Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder hantera resurser för Spark-kluster i Azure HDInsight för bättre prestanda."
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 4acaac52ec86f902a7971459ed4b19c1e5992a28
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Hantera resurser för Apache Spark-kluster i Azure HDInsight 

Lär dig hur du kommer åt gränssnitt som Ambari UI, YARN-Användargränssnittet och Spark historik servern som associeras med Spark-kluster och hur du ställer in klusterkonfigurationen för optimala prestanda.

**Krav:**

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Öppna Ambari-webbgränssnittet

Apache Ambari används för att övervaka klustret och gör ändringar i konfigurationen. Mer information finns i [hantera Hadoop-kluster i HDInsight med hjälp av Azure portal](../hdinsight-administer-use-portal-linux.md#open-the-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Öppna Spark historik Server

Spark historik Server är webbgränssnittet för slutfördes och en körs Spark-program. Det är en förlängning av Sparks Webbgränssnittet.

**Öppna Webbgränssnittet Spark historik Server**

1. Från den [Azure-portalen](https://portal.azure.com/), öppna Spark-klustret. Mer information finns i [listan och visa](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Från **snabblänkar**, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **Spark historik Server**

    ![Spark History Server](./media/apache-spark-resource-manager/launch-history-server.png "Spark History Server")

    När du uppmanas ange administratörsautentiseringsuppgifterna för Spark-klustret. Du kan också öppna Spark historik servern genom att bläddra till följande URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Ersätt <ClusterName> med Spark klusternamnet.

Spark historik Server web UI ser ut:

![HDInsight Spark historik Server](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Öppna Yarn-Användargränssnittet
Du kan använda YARN-Användargränssnittet för att övervaka program som körs i Spark-klustret.

1. Från den [Azure-portalen](https://portal.azure.com/), öppna Spark-klustret. Mer information finns i [listan och visa](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Från **snabblänkar**, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **YARN**.

    ![Starta YARN-Användargränssnittet](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Alternativt kan du starta YARN-Användargränssnittet från Ambari UI. Om du vill starta Ambari UI, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **instrumentpanelen för HDInsight-klustret**. Ambari UI, klicka på **YARN**, klickar du på **snabblänkar**, klicka på den aktiva Resource Manager och klicka sedan på **Resource Manager UI**.
   >
   >

## <a name="optimize-clusters-for-spark-applications"></a>Optimera kluster för Spark-program
De tre viktiga parametrar som kan användas för konfiguration av Spark beroende på kraven för application är `spark.executor.instances`, `spark.executor.cores`, och `spark.executor.memory`. En utförare är en process startas för ett Spark-program. Körs på arbetsnoden och ansvarar för att utföra uppgifter för programmet. Standardantalet executors och utföraren storleken för varje kluster beräknas baserat på antalet arbetarnoder och nodstorlek worker. Den här informationen lagras i `spark-defaults.conf` head noderna i klustret.

De tre konfigurationsparametrarna kan konfigureras på klusternivå (för alla program som körs på klustret) eller kan anges för varje enskilt program.

### <a name="change-the-parameters-using-ambari-ui"></a>Ändra parametrarna med Ambari UI
1. Ambari UI klickar du på **Spark**, klickar du på **konfigurationerna**, och expandera sedan **anpassad spark-standarder**.

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Standardvärdena är bra att ha fyra Spark-program som körs samtidigt på klustret. Du kan ändra dessa värden i användargränssnittet, som visas i följande skärmbild:

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Klicka på **spara** att spara ändringar i konfigurationen. Överst på sidan uppmanas du att starta om alla påverkade tjänster. Klicka på **starta om**.

    ![Starta om tjänsterna](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrarna för ett program som körs i Jupyter-anteckningsbok
Du kan använda för program som körs i Jupyter-anteckningsbok den `%%configure` Magiskt tal för att göra ändringar i konfigurationen. Vi rekommenderar måste du göra dessa ändringar i början av programmet innan du kör din första kodcellen. Detta säkerställer att konfigurationen tillämpas Livius-sessionen när den skapas. Om du vill ändra konfigurationen senare i programmet, måste du använda den `-f` parameter. På så sätt är dock alla förlopp i programmet går förlorade.

Följande utdrag visar hur du ändrar konfigurationen för ett program som körs i Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurationsparametrar som måste överföras i som en JSON-sträng och måste vara på nästa rad efter magic, som visas i Exempelkolumnen.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Ändra parametrarna för ett program som skickas med spark-skicka
Följande kommando är ett exempel på hur du ändrar konfigurationsparametrar för ett batch-program som har skickats med `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Ändra parametrarna för ett program som skickas med cURL
Följande kommando är ett exempel på hur du ändrar konfigurationsparametrar för ett batch-program som har skickats med cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>De här parametrarna på en Spark Thrift-Server
Spark Thrift-servern tillhandahåller JDBC/ODBC-åtkomst till ett Spark-kluster och används för att tjänsten Spark SQL-frågor. Verktyg som Power BI Tableau osv. använda ODBC-protokollet för att kommunicera med Spark Thrift-servern för att köra Spark SQL-frågor som ett Spark-program. När ett Spark-kluster skapas två instanser av Spark Thrift-servern är igång, en på varje huvudnod. Varje Spark Thrift-servern visas som ett Spark-program i YARN-Användargränssnittet.

Spark Thrift-servern använder Spark dynamiska utförare allokering och därför den `spark.executor.instances` används inte. Spark Thrift-servern används i stället `spark.dynamicAllocation.minExecutors` och `spark.dynamicAllocation.maxExecutors` att ange antalet utförare. Konfigurationsparametrarna `spark.executor.cores` och `spark.executor.memory` används för att ändra storlek på utförare. Du kan ändra parametrarna som visas i följande steg:

* Expandera den **avancerade spark-thrift-sparkconf** kategori att uppdatera parametrarna `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, och `spark.executor.memory`.

    ![Konfigurera Spark thrift-servern](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Expandera den **anpassad spark-thrift-sparkconf** kategori att uppdatera parametern `spark.executor.cores`.

    ![Konfigurera Spark thrift-servern](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Ändra drivrutinen minnet på Spark Thrift-servern
Spark Thrift-servern drivrutinsminne är konfigurerad att 25% av huvudnod ram-storlek, under förutsättning att den totala storleken för RAM-minne på Huvudnoden är större än 14 GB. Du kan använda Ambari UI ändra minneskonfigurationen drivrutin som visas i följande skärmbild:

* Ambari UI klickar du på **Spark**, klickar du på **konfigurationerna**, expandera **avancerade spark env**, och ange värdet för **spark_thrift_cmd_opts**.

    ![Konfigurera Spark thrift-servern RAM-minne](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Frigöra resurser för Spark-kluster
Endast resurserna som förbrukas av thrift-servern är på grund av Spark dynamisk allokering, resurser för två program huvudservrar. Om du vill frigöra dessa resurser, måste du stoppa tjänsterna Thrift-servern körs i klustret.

1. Ambari UI, i den vänstra rutan klickar du på **Spark**.
2. Klicka på nästa sida **Spark Thrift servrar**.

    ![Starta om thrift-servern](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Du bör se två headnodes som Spark Thrift-servern körs. Klicka på någon av headnodes.

    ![Starta om thrift-servern](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. Nästa sida visar alla tjänster som körs på den headnode. Klicka på listrutan bredvid Spark Thrift-servern i listan och klicka sedan på **stoppa**.

    ![Starta om thrift-servern](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Upprepa dessa steg på andra headnode samt.

## <a name="restart-the-jupyter-service"></a>Starta om tjänsten Jupyter
Starta Ambari-Webbgränssnittet som visas i början av artikeln. I det vänstra navigeringsfönstret klickar du på **Jupyter**, klickar du på **tjänståtgärder**, och klicka sedan på **starta om alla**. Detta startar tjänsten Jupyter på alla headnodes.

![Starta om Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "starta om Jupyter")

## <a name="monitor-resources"></a>Övervaka resurser
Starta Yarn-Användargränssnittet som visas i början av artikeln. Kontrollera värdena för i klustret mått tabell på skärmen, **minne används** och **minne totalt** kolumner. Om de två värdena är nära kanske det inte finns tillräckligt med resurser för att starta programmet nästa. Samma gäller den **VCores används** och **VCores totalt** kolumner. Även i vyn huvudsakliga om det finns ett program svaret inte i **godkända** tillstånd och inte att omvandla till **kör** eller **misslyckades** tillstånd, detta kan också vara ett tecken som det inte får tillräckligt med resurser för att starta.

![Resursgräns](./media/apache-spark-resource-manager/resource-limit.png "resursgräns")

## <a name="kill-running-applications"></a>Avsluta program som körs
1. I Yarn-Användargränssnittet från den vänstra rutan klickar du på **kör**. Listan över program som körs och att bestämma programmet avslutas och klicka på den **ID**.

    ![Avsluta App1](./media/apache-spark-resource-manager/kill-app1.png "Kill App1")

2. Klicka på **avsluta programmet** i övre högra hörnet, klicka sedan på **OK**.

    ![Avsluta App2](./media/apache-spark-resource-manager/kill-app2.png "Kill App2")

## <a name="see-also"></a>Se också
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
