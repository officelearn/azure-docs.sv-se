---
title: Hantera resurser för Apache Spark-kluster i Azure HDInsight
description: Lär dig hur du hanterar resurser för Spark-kluster på Azure HDInsight för bättre prestanda.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932095"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Hantera resurser för Apache Spark-kluster i Azure HDInsight

Lär dig hur du kommer åt gränssnitten som [Apache Ambari](https://ambari.apache.org/) UI, [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI och Spark History [Server](./apache-azure-spark-history-server.md) som är associerade med [Apache Spark-klustret](https://spark.apache.org/) och hur du justerar klusterkonfigurationen för optimal prestanda.

## <a name="open-the-spark-history-server"></a>Öppna Spark History Server

Spark History Server är webbgränssnittet för färdiga och köra Spark-program. Det är ett tillägg av Spark's Web UI. Fullständig information finns i [Spark History Server](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Öppna garngränssnittet

Du kan använda YARN-användargränssnittet för att övervaka program som körs på Spark-klustret.

1. Öppna Spark-klustret från [Azure-portalen.](https://portal.azure.com/) Mer information finns i [Lista och visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Välj **Garn** **från klusterinstrumentpaneler**. Ange administratörsautentiseringsuppgifterna för Spark-klustret när du uppmanas att göra det.

    ![Starta YARN UI](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Alternativt kan du också starta YARN UI från Ambari UI. Från Ambari UI navigerar du till **YARN** > **Quick Links** > **Active** > **Resource Manager UI**.

## <a name="optimize-clusters-for-spark-applications"></a>Optimera kluster för Spark-program

De tre nyckelparametrar som kan användas för `spark.executor.instances` `spark.executor.cores`Spark-konfiguration beroende på programkrav är , och `spark.executor.memory`. En Executor är en process som startas för ett Spark-program. Den körs på arbetarnoden och är ansvarig för att utföra uppgifterna för programmet. Standardantalet köror och körorstorlekar för varje kluster beräknas baserat på antalet arbetsnoder och arbetarnodstorleken. Den här informationen `spark-defaults.conf` lagras i på klusterhuvudnoderna.

De tre konfigurationsparametrarna kan konfigureras på klusternivå (för alla program som körs i klustret) eller kan också anges för varje enskilt program.

### <a name="change-the-parameters-using-ambari-ui"></a>Ändra parametrarna med hjälp av Ambari UI

1. Från Ambari UI navigera till **Spark2** > **Configs** > **Custom spark2-standardvärden**.

    ![Ange parametrar med Ambari custom](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Ange parametrar med Ambari custom")

1. Standardvärdena är bra att fyra Spark-program körs samtidigt i klustret. Du kan ändra dessa värden från användargränssnittet, vilket visas i följande skärmbild:

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Ange parametrar med Ambari")

1. Välj **Spara** om du vill spara konfigurationsändringarna. Högst upp på sidan uppmanas du att starta om alla berörda tjänster. Välj **Starta om**.

    ![Starta om tjänster](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrarna för ett program som körs i Jupyter-anteckningsbok

För program som körs i den bärbara `%%configure` Jupyter-anteckningsboken kan du använda magin för att göra konfigurationsändringarna. Helst måste du göra sådana ändringar i början av programmet innan du kör din första kodcell. Detta säkerställer att konfigurationen tillämpas på Livy-sessionen när den skapas. Om du vill ändra konfigurationen i ett senare skede `-f` i programmet måste du använda parametern. Genom att göra detta går dock alla framsteg i programmet förlorade.

Följande kodavsnitt visar hur du ändrar konfigurationen för ett program som körs i Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurationsparametrar måste skickas in som en JSON-sträng och måste finnas på nästa rad efter magin, som visas i exempelkolumnen.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Ändra parametrarna för en ansökan som skickas in med hjälp av spark-submit

Följande kommando är ett exempel på hur du ändrar konfigurationsparametrarna för ett batchprogram som skickas med `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Ändra parametrarna för en ansökan som skickas in med hjälp av cURL

Följande kommando är ett exempel på hur du ändrar konfigurationsparametrarna för ett batchprogram som skickas med cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Ändra dessa parametrar på en Spark Thrift Server

Spark Thrift Server ger JDBC/ODBC åtkomst till ett Spark-kluster och används för att betjäna Spark SQL-frågor. Verktyg som Power BI, Tableau och så vidare använder ODBC-protokollet för att kommunicera med Spark Thrift Server för att köra Spark SQL-frågor som ett Spark-program. När ett Spark-kluster skapas startas två instanser av Spark Thrift Server, en på varje huvudnod. Varje Spark Sparsamhet server visas som ett Spark-program i YARN-användargränssnittet.

Spark Thrift Server använder Spark dynamisk `spark.executor.instances` executor allokering och därmed inte används. Spark Thrift Server `spark.dynamicAllocation.maxExecutors` använder `spark.dynamicAllocation.minExecutors` och anger i stället antalet köror. Konfigurationsparametrarna `spark.executor.cores`och `spark.executor.memory` används för att ändra körordningsstorleken. Du kan ändra dessa parametrar enligt följande steg:

* Expandera kategorin **Avancerad spark2-sparsamhet-sparkconf** för `spark.dynamicAllocation.maxExecutors`att `spark.dynamicAllocation.minExecutors`uppdatera parametrarna och .

    ![Konfigurera Spark-sparsamhetsserver](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Konfigurera Spark-sparsamhetsserver")

* Expandera kategorin **Anpassad spark2-sparsamhet-sparkconf** för `spark.executor.cores`att `spark.executor.memory`uppdatera parametrarna och .

    ![Konfigurera parametern För spark-sparsamhetserver](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Konfigurera parametern För spark-sparsamhetserver")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Ändra drivrutinsminnet för Spark Thrift Server

Spark Thrift Server-drivrutinsminnet är konfigurerat till 25 % av huvudnodens RAM-storlek, förutsatt att den totala RAM-storleken på huvudnoden är större än 14 GB. Du kan använda användargränssnittet i Ambari för att ändra drivrutinens minneskonfiguration, som visas i följande skärmbild:

Från Ambari UI navigerar du till **Spark2** > **Configs** > **Advanced spark2-env**. Ange sedan värdet för **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Återta Spark-klusterresurser

På grund av den dynamiska sparkallokeringen är de enda resurser som förbrukas av sparsamhetsservern resurserna för de två programmallarna. Om du vill återkräva dessa resurser måste du stoppa sparsamhetsservertjänsterna som körs i klustret.

1. Välj **Spark2**från Ambari-användargränssnittet i den vänstra rutan .

2. På nästa sida väljer du **Spark2 Thrift-servrar**.

    ![Starta om sparsamhetsserver1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Starta om sparsamhetsserver1")

3. Du bör se de två headnodes som Spark2 Thrift Server körs på. Välj en av headnodes.

    ![Starta om sparsamhetsserver2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Starta om sparsamhetsserver2")

4. På nästa sida visas alla tjänster som körs på den headnoden. Välj listrutan bredvid Spark2 Thrift Server i listan och välj sedan **Stoppa**.

    ![Starta om sparsamhetsserver3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Starta om sparsamhetsserver3")
5. Upprepa dessa steg på den andra headnode också.

## <a name="restart-the-jupyter-service"></a>Starta om Tjänsten Jupyter

Starta Ambari Web UI som visas i början av artikeln. Välj **Jupyter**i det vänstra navigeringsfönstret , välj **Serviceåtgärder**och välj sedan **Starta om alla**. Detta startar Jupyter tjänsten på alla headnodes.

![Starta om Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Starta om Jupyter")

## <a name="monitor-resources"></a>Övervaka resurser

Starta Garngränssnittet som visas i början av artikeln. I tabellen Klustermått överst på skärmen kontrollerar du värdena **för kolumnerna Minne som används** och Totalt **minne.** Om de två värdena är nära kanske det inte finns tillräckligt med resurser för att starta nästa program. Detsamma gäller för **kolumnerna VCores Used** och **VCores Total.** Dessutom, i huvudvyn, om det finns ett program stannade i **accepterat** tillstånd och inte övergår till **kör** eller **misslyckades** tillstånd, kan detta också vara en indikation på att det inte får tillräckligt med resurser för att starta.

![Resursgräns](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Resursgräns")

## <a name="kill-running-applications"></a>Döda program som körs

1. Välj **Kör**från den vänstra panelen i garngränssnittet . I listan över program som körs bestämmer du vilket program som ska dödas och väljer **ID.**

    ![Döda App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Döda App1")

2. Välj **Döda program** längst upp till höger och välj sedan **OK**.

    ![Döda App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Döda App2")

## <a name="see-also"></a>Se även

* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>För dataanalytiker

* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Program Insight telemetri dataanalys med Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>För Apache Spark-utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight Tools Plugin för IntelliJ IDEA för att felsöka Apache Spark-program på distans](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
