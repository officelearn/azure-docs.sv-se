---
title: Hantera resurser för Apache Spark kluster i Azure HDInsight
description: Lär dig hur du använder hantera resurser för Spark-kluster på Azure HDInsight för bättre prestanda.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: 0d97ca91466516b8722ecca77d19078399a258f7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814090"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Hantera resurser för Apache Spark kluster i Azure HDInsight 

Lär dig hur du kommer åt gränssnitten som [Apache Ambari](https://ambari.apache.org/) ui, [Apache Hadoop garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI och Spark historik servern som är kopplad till ditt [Apache Spark](https://spark.apache.org/) -kluster och hur du finjusterar kluster konfigurationen för optimala prestanda.

**Krav:**

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Öppna Ambari-webbgränssnittet

Apache Ambari används för att övervaka klustret och göra konfigurations ändringar. Mer information finns i [hantera Apache Hadoop kluster i HDInsight med hjälp av Azure Portal](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Öppna Spark-historik servern

Spark-historik Server är webb gränssnittet för slutförd och körning av Spark-program. Det är en utökning av Spark: s webb gränssnitt.

**Öppna webb gränssnittet för Spark-historikens Server**

1. Öppna Spark-klustret från [Azure Portal](https://portal.azure.com/). Mer information finns i [lista och Visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Från **snabb länkar**klickar du på **kluster instrument panel**och sedan på **Spark historik Server**

    ![Spark-historik Server](./media/apache-spark-resource-manager/launch-history-server.png "Spark-historik Server")

    När du uppmanas till det anger du administratörs behörighet för Spark-klustret. Du kan också öppna Spark historik servern genom att bläddra till följande URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Ersätt `<ClusterName>` med ditt Spark-kluster namn.

Spark historik serverns webb gränssnitt ser ut så här:

![HDInsight Spark historik Server](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Öppna garn gränssnittet
Du kan använda garn gränssnittet för att övervaka program som för närvarande körs i Spark-klustret.

1. Öppna Spark-klustret från [Azure Portal](https://portal.azure.com/). Mer information finns i [lista och Visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Från **snabb länkar**klickar du på **kluster instrument panel**och sedan på **garn**.

    ![Starta garn gränssnitt](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]  
   > Du kan också starta garn gränssnittet från Ambari-ANVÄNDARGRÄNSSNITTET. Starta Ambari-ANVÄNDARGRÄNSSNITTET genom att klicka på **kluster instrument panel**och sedan på **HDInsight-klustrets instrument panel**. Klicka på **garn**i AMBARI-användargränssnittet, klicka på **snabb länkar**, klicka på den aktiva Resource Manager och klicka sedan på **Resource Manager-användargränssnitt**.

## <a name="optimize-clusters-for-spark-applications"></a>Optimera kluster för Spark-program
De tre nyckel parametrarna som kan användas för Spark-konfiguration `spark.executor.instances`, beroende på program krav, `spark.executor.cores`, och `spark.executor.memory`. En utförar är en process som startas för ett Spark-program. Den körs på Worker-noden och ansvarar för att utföra uppgifterna för programmet. Standard antalet körningar och utförar storlekarna för varje kluster beräknas baserat på antalet arbetsnoder och storleken på arbets noden. Den här informationen lagras i `spark-defaults.conf` kluster huvudnoderna.

De tre konfigurations parametrarna kan konfigureras på kluster nivå (för alla program som körs i klustret) eller också kan de anges för varje enskilt program.

### <a name="change-the-parameters-using-ambari-ui"></a>Ändra parametrarna med Ambari-ANVÄNDARGRÄNSSNITTET
1. I Ambari-ANVÄNDARGRÄNSSNITTET klickar du på **Spark**, klickar på **config**och expanderar sedan **anpassade Spark-standardvärden**.

    ![Ange parametrar med anpassad Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png "Ange parametrar med anpassad Ambari")
2. Standardvärdena är lämpliga för att köra fyra Spark-program samtidigt i klustret. Du kan ändra dessa värden från användar gränssnittet, som visas på följande skärm bild:

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png "Ange parametrar med Ambari")

3. Klicka på **Spara** för att spara konfigurations ändringarna. Längst upp på sidan uppmanas du att starta om alla berörda tjänster. Klicka på **Starta om**.

    ![Starta om tjänster](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrarna för ett program som körs i Jupyter Notebook
För program som körs i Jupyter Notebook kan du använda `%%configure` Magic för att göra konfigurationen ändringar. Vi rekommenderar att du gör sådana ändringar i början av programmet innan du kör den första kod cellen. Detta säkerställer att konfigurationen tillämpas på livy-sessionen när den skapas. Om du vill ändra konfigurationen i ett senare skede i programmet måste du använda `-f` -parametern. Men genom att göra detta går all status i programmet förlorade.

Följande fragment visar hur du ändrar konfigurationen för ett program som körs i Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurations parametrar måste skickas in som en JSON-sträng och måste finnas på nästa rad efter Magic, som visas i kolumnen exempel.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Ändra parametrarna för ett program som har skickats med Spark-Submit
Följande kommando är ett exempel på hur du ändrar konfigurations parametrar för ett batch-program som skickas med `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Ändra parametrarna för ett program som skickas med hjälp av sväng
Följande kommando är ett exempel på hur du ändrar konfigurations parametrar för ett batch-program som skickas med hjälp av sväng.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Ändra dessa parametrar på en spark Thrift-Server
Spark Thrift-servern ger JDBC/ODBC-åtkomst till ett Spark-kluster och används för att betjäna Spark SQL-frågor. Verktyg som Power BI, Tableau osv. Använd ODBC-protokollet för att kommunicera med Spark Thrift-servern för att köra Spark SQL-frågor som ett Spark-program. När ett Spark-kluster skapas startas två instanser av Spark Thrift-servern, en på varje Head-nod. Varje Spark Thrift-Server visas som ett Spark-program i garn gränssnittet.

Spark Thrift-servern använder Spark Dynamic utförar-allokering och `spark.executor.instances` därför används den inte. I stället använder `spark.dynamicAllocation.minExecutors` Spark Thrift-servern `spark.dynamicAllocation.maxExecutors` och för att ange antalet utförar. Konfigurations parametrarna `spark.executor.cores` och `spark.executor.memory` används för att ändra storleken på utförar. Du kan ändra dessa parametrar så som visas i följande steg:

* Expandera kategorin **Avancerad Spark-Thrift-sparkconf** för att uppdatera parametrarna `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`och `spark.executor.memory`.

    ![Konfigurera Spark Thrift-Server](./media/apache-spark-resource-manager/spark-thrift-server-1.png "Konfigurera Spark Thrift-Server")
* Uppdatera parametern `spark.executor.cores`genom att expandera kategorin **Custom Spark-Thrift-sparkconf** .

    ![Konfigurera Spark Thrift Server parameter](./media/apache-spark-resource-manager/spark-thrift-server-2.png "Konfigurera Spark Thrift Server parameter")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Ändra driv rutins minnet för Spark Thrift-servern
Spark Thrift-serverns driv rutins minne har kon figurer ATS till 25% av Head-nodens RAM-storlek, förutsatt att Head-nodens totala RAM-storlek är större än 14 GB. Du kan använda Ambari-ANVÄNDARGRÄNSSNITTET för att ändra driv Rutinens minnes konfiguration, som visas på följande skärm bild:

* Från Ambari-ANVÄNDARGRÄNSSNITTET klickar du på **Spark**, klickar på **konfiguration**, expanderar **Avancerad Spark-miljö**och anger sedan värdet för **spark_thrift_cmd_opts**.

    ![Konfigurera Spark Thrift Server RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Frigör Spark kluster resurser
På grund av Spark Dynamic Allocation är de enda resurserna som används av Thrift-servern resurserna för de två program huvuden. Om du vill frigöra resurserna måste du stoppa de Thrift-Server tjänster som körs i klustret.

1. Klicka på **Spark**från det vänstra fönstret i AMBARI-användargränssnittet.
2. På nästa sida klickar du på **Spark Thrift-servrar**.

    ![Starta om Thrift server1](./media/apache-spark-resource-manager/restart-thrift-server-1.png "Starta om Thrift server1")
3. Du bör se de två huvudnoderna som Spark Thrift-servern körs på. Klicka på en av huvudnoderna.

    ![Starta om Thrift Server2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Starta om Thrift Server2")
4. Nästa sida visar en lista över alla tjänster som körs på den huvudnoden. I listan klickar du på den nedrullningsbara knappen bredvid Spark Thrift Server och klickar sedan på **stoppa**.

    ![Starta om Thrift Server3](./media/apache-spark-resource-manager/restart-thrift-server-3.png "Starta om Thrift Server3")
5. Upprepa de här stegen även på andra huvudnoden.

## <a name="restart-the-jupyter-service"></a>Starta om Jupyter-tjänsten
Starta Ambari-webbgränssnittet som visas i början av artikeln. Klicka på **Jupyter**i det vänstra navigerings fönstret, klicka på **service åtgärder**och klicka sedan på **starta om alla**. Detta startar Jupyter-tjänsten på alla huvudnoderna.

![Starta om Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Starta om Jupyter")

## <a name="monitor-resources"></a>Övervaka resurser
Starta det garn användar gränssnitt som visas i början av artikeln. I tabellen kluster mått överst på skärmen, kontrol lera värdena för **använt minne** och kolumner i **Total** mängd minne. Om de två värdena stängs kanske det inte finns tillräckligt med resurser för att starta nästa program. Detsamma gäller för de **virtuella kärnor som används** och **virtuella kärnor totala** kolumner. I huvudvyn kan det också vara en indikation på att det inte finns tillräckligt med resurser för att starta, om ett program finns i ett **godkänt** tillstånd och inte går över till att **köras** eller inte är i **fel** tillstånd.

![Resurs gräns](./media/apache-spark-resource-manager/resource-limit.png "Resurs gräns")

## <a name="kill-running-applications"></a>Avsluta program som körs
1. I garn gränssnittet klickar du på **Kör**i den vänstra panelen. I listan över program som körs anger du vilket program som ska avlivas och klickar på **ID: t**.

    ![Kill-APP1](./media/apache-spark-resource-manager/kill-app1.png "Kill-APP1")

2. Klicka på **Kill Application (avsluta program** ) i det övre högra hörnet och klicka sedan på **OK**.

    ![Kill-APP2](./media/apache-spark-resource-manager/kill-app2.png "Kill-APP2")

## <a name="see-also"></a>Se också
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>För data analyser

* [Apache Spark med Machine Learning: Använda spark i HDInsight för analys av bygg temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använd spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Program insiktering telemetri data analys med Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Använda Caffe på Azure HDInsight Spark för distribuerad djup inlärning](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>För Apache Spark utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels tillgängliga för Jupyter Notebook i Apache Spark kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
