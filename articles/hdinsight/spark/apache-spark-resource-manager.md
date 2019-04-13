---
title: Hantera resurser för Apache Spark-kluster i Azure HDInsight
description: Lär dig hur du använder hantera resurser för Spark-kluster på Azure HDInsight får du bättre prestanda.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: 023fd8267a557fa57e98a6a57785fb9ebfcb12ab
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523978"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Hantera resurser för Apache Spark-kluster i Azure HDInsight 

Lär dig hur du kommer åt gränssnitt som [Apache Ambari](https://ambari.apache.org/) Användargränssnittet [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Användargränssnittet och Spark-Historikserver som är associerade med din [Apache Spark](https://spark.apache.org/) kluster, och hur du finjustera klusterkonfigurationen för optimala prestanda.

**Krav:**

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Öppna Ambari-webbgränssnittet

Apache Ambari används för att övervaka klustret och gör ändringar i konfigurationen. Mer information finns i [hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure portal](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Öppna Spark-Historikserver

Spark-Historikserver är webbgränssnittet för slutförda och körs Spark-program. Det är en utökning av Sparks Webbgränssnittet.

**Öppna Spark historik Server-Webbgränssnittet**

1. Från den [Azure-portalen](https://portal.azure.com/), öppna Spark-klustret. Mer information finns i [lista och visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Från **snabblänkar**, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **Spark-Historikserver**

    ![Spark History Server](./media/apache-spark-resource-manager/launch-history-server.png "Spark History Server")

    När du uppmanas, anger du autentiseringsuppgifter som administratör för Spark-klustret. Du kan också öppna Spark-Historikserver genom att bläddra till följande URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Ersätt `<ClusterName>` med Apache Spark-klusternamn.

Spark-Historikserver web UI som ser ut som:

![HDInsight Spark-Historikserver](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Öppna Användargränssnittet för Yarn
Du kan använda YARN-Användargränssnittet för att övervaka program som körs på Spark-kluster.

1. Från den [Azure-portalen](https://portal.azure.com/), öppna Spark-klustret. Mer information finns i [lista och visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Från **snabblänkar**, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **YARN**.

    ![Starta Användargränssnittet för YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]  
   > Alternativt kan du också starta YARN-Användargränssnittet från Ambari UI. Om du vill starta Ambari UI, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **HDInsight-Klusterinstrumentpanel**. Ambari UI, klickar du på **YARN**, klickar du på **snabblänkar**, klicka på den aktiva Resource Manager och klicka sedan på **Resource Manager UI**.

## <a name="optimize-clusters-for-spark-applications"></a>Optimera kluster för Spark-program
De tre viktiga parametrar som kan användas för konfiguration av Spark beroende på programkrav är `spark.executor.instances`, `spark.executor.cores`, och `spark.executor.memory`. En Executor är en process som lanserade för ett Spark-program. Körs på arbetsnoden och ansvarar för att utföra uppgifter för programmet. Standardvärdet för antal executors och executor storleken för varje kluster beräknas baserat på antalet arbetsnoder och nodstorlek worker. Den här informationen lagras i `spark-defaults.conf` på klustrets huvudnoder.

De tre konfigurationsparametrarna kan konfigureras på klusternivå (för alla program som körs i klustret) eller kan anges för varje enskilt program.

### <a name="change-the-parameters-using-ambari-ui"></a>Ändra parametrarna med hjälp av Ambari UI
1. Från Ambari UI-Klicka **Spark**, klickar du på **Peeringkonfigurationer**, och expandera sedan **anpassade spark-standardvärden**.

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Standardvärdena är bra om du har fyra Spark-program som körs samtidigt på klustret. Du kan ändra dessa värden från användargränssnittet, enligt följande skärmbild:

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Klicka på **spara** att spara konfigurationsändringarna. Högst upp på sidan uppmanas du att starta om alla tjänster som påverkas. Klicka på **Starta om**.

    ![Starta om tjänsterna](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrarna för ett program som körs i Jupyter-anteckningsbok
För program som körs i Jupyter-anteckningsboken, kan du använda den `%%configure` magic att göra ändringar i konfigurationen. Vi rekommenderar måste du göra ändringar i början av programmet, innan du kör ditt första kodcellen. Detta säkerställer att konfigurationen tillämpas i Livy-sessionen när den skapas. Om du vill ändra konfigurationen senare i programmet, måste du använda den `-f` parametern. På så sätt är dock alla ändringar i programmet gå förlorade.

Följande kodfragment visar hur du ändrar konfigurationen för ett program som körs i Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurationsparametrar måste skickas som en JSON-sträng och måste vara på nästa rad efter magic, som visas i Exempelkolumnen.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Ändra parametrarna för ett program som har skickats med hjälp av spark-submit
Följande kommando är ett exempel på hur du ändrar konfigurationsparametrar för ett batch-program som skickas med hjälp av `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Ändra parametrarna för ett program som har skickats med cURL
Följande kommando är ett exempel på hur du ändrar konfigurationsparametrar för ett batch-program som har skickats med cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Ändra dessa parametrar i en Spark Thrift-Server
Spark Thrift-Server ger JDBC/ODBC-åtkomst till ett Spark-kluster och används för att tjänsten Spark SQL-frågor. Verktyg som Power BI, Tableau osv. använda ODBC-protokollet för att kommunicera med Spark Thrift-Server för att köra Spark SQL-frågor som ett Spark-program. När ett Spark-kluster skapas, startas två instanser av Spark Thrift-Server, en på varje huvudnoden. Varje Spark Thrift-Server visas som ett Spark-program i YARN-Användargränssnittet.

Spark Thrift-Server använder Spark dynamisk executor allokering och den `spark.executor.instances` används inte. Spark Thrift-Server används i stället `spark.dynamicAllocation.minExecutors` och `spark.dynamicAllocation.maxExecutors` att ange antalet executor. Konfigurationsparametrarna `spark.executor.cores` och `spark.executor.memory` används för att ändra storlek på executor. Du kan ändra dessa parametrar som du ser i följande steg:

* Expandera den **avancerade spark-thrift-sparkconf** kategori om du vill uppdatera parametrarna `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, och `spark.executor.memory`.

    ![Konfigurera Spark thrift-server](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Expandera den **anpassade spark-thrift-sparkconf** kategori att uppdatera parametern `spark.executor.cores`.

    ![Konfigurera Spark thrift-server](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Ändra drivrutinen minnet för Spark Thrift-Server
Spark Thrift-Server-drivrutinsminne är konfigurerad för att 25% av huvudnod RAM-storleken, under förutsättning att totalt RAM-minne för Huvudnoden är större än 14 GB. Du kan använda Ambari UI ändra minneskonfigurationen drivrutin som du ser i följande skärmbild:

* Från Ambari UI-Klicka **Spark**, klickar du på **Peeringkonfigurationer**, expandera **avancerade spark env**, och ange sedan värdet för **spark_thrift_cmd_opts**.

    ![Konfigurera Spark thrift-server RAM-minne](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Frigöra Spark-klusterresurser
På grund av Spark dynamisk allokering är de enda resurserna som förbrukas av thrift-server med resurser för två program huvudservrar. Om du vill frigöra dessa resurser måste du stoppa tjänsterna Thrift-Server som körs i klustret.

1. Ambari UI, i den vänstra rutan klickar du på **Spark**.
2. På nästa sida klickar du på **Spark Thrift servrar**.

    ![Starta om thrift-server](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Du bör se två huvudnoder som Spark Thrift-Server körs. Klicka på någon av huvudnoderna.

    ![Starta om thrift-server](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. Nästa sida visar en lista över alla tjänster som körs på den huvudnoden. Klicka på listrutan bredvid Spark Thrift-Server i listan och klicka sedan på **stoppa**.

    ![Starta om thrift-server](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Upprepa dessa steg på den andra huvudnoden samt.

## <a name="restart-the-jupyter-service"></a>Starta om tjänsten Jupyter
Starta Ambari-Webbgränssnittet som visas i början av artikeln. I det vänstra navigeringsfönstret klickar du på **Jupyter**, klickar du på **tjänståtgärder**, och klicka sedan på **starta om alla**. Detta startar tjänsten Jupyter på alla huvudnoder.

![Starta om Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "starta om Jupyter")

## <a name="monitor-resources"></a>Övervaka resurser
Starta Användargränssnittet för Yarn som visas i början av artikeln. Klustermått tabellen på skärmen, Kontrollera värdena för **använt minne** och **minne totalt** kolumner. Om två värden ligger nära kanske det inte finns tillräckligt med resurser för att starta nästa programmet. Detsamma gäller för den **virtuella kärnor som används** och **totala antalet virtuella kärnor** kolumner. Dessutom i Huvudvy, om det finns ett program har stannat i **godkända** tillstånd och inte övergår i **kör** eller **misslyckades** tillstånd, detta kan också vara en indikation som Det får inte tillräckligt med resurser för att starta.

![Resursgränsen](./media/apache-spark-resource-manager/resource-limit.png "Resursgränsen")

## <a name="kill-running-applications"></a>Avsluta program som körs
1. I Yarn-Användargränssnittet i vänster panel, klickar du på **kör**. Från listan över program som körs, att avgöra att programmet avslutas och klicka på den **ID**.

    ![Avsluta App1](./media/apache-spark-resource-manager/kill-app1.png "Kill App1")

2. Klicka på **avsluta programmet** i övre högra hörnet, klicka sedan på **OK**.

    ![Avsluta App2](./media/apache-spark-resource-manager/kill-app2.png "Kill App2")

## <a name="see-also"></a>Se också
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>För dataanalytiker

* [Apache Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetri dataanalys med hjälp av Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Använda Caffe på Azure HDInsight Spark för distribuerade djupinlärning](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>För Apache Spark-utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-Plugin för IntelliJ IDEA till att felsöka Apache Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
