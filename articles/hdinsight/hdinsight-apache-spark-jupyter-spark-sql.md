---
title: "Kom igång – Apache Spark och interaktiva Spark SQL-frågor – Azure HDInsight | Microsoft Doc"
description: "HDInsight Spark-snabbstart om hur du skapar ett Apache Spark-kluster i HDInsight och kör interaktiva frågor med Jupyter-anteckningsböcker."
keywords: "spark-snabbstart,interaktiv spark,interaktiv fråga,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/25/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 925dbf5e595941da58e7d705175d0cc63bbf6a16
ms.contentlocale: sv-se
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-create-an-apache-spark-cluster-in-hdinsight-and-run-interactive-spark-sql-queries"></a>Kom igång: Skapa ett Apache Spark-kluster i HDInsight och köra interaktiva frågor med Spark SQL

Lär dig hur du skapar ett [Apache Spark](hdinsight-apache-spark-overview.md)-kluster i HDInsight och kör interaktiva Spark SQL-frågor med [Jupyter](https://jupyter.org)-anteckningsboken.

   ![Snabbstartsdiagram som beskriver steg för att skapa ett Apache Spark-kluster på Azure HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Spark-snabbstart med Apache Spark i HDInsight. Illustrerade steg: skapa ett kluster, kör interaktiv Spark-fråga")

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [Skapa ett kostnadsfritt Azure-konto i dag](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Skapa HDInsight Spark-kluster

I det här avsnittet skapar du ett Spark-kluster i HDInsight med en [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Information om andra metoder för att skapa kluster finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicka på följande bild för att öppna mallen i Azure Portal.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Ange följande värden:

    ![Skapa HDInsight Spark-kluster med en Azure Resource Manager-mall](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Skapa Spark-kluster i HDInsight med en Azure Resource Manager-mall")

    * **Prenumeration**: Välj din Azure-prenumeration för det här klustret.
    * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig. Resursgrupp används för att hantera Azure-resurser till dina projekt.
    * **Plats**: Välj en plats för resursgruppen.  Den här platsen används också för standardklusterlagringen och HDInsight-klustret.
    * **Klusternamn**: Ange ett namn för det Hadoop-kluster du skapar.
    * **Spark-version**: Välj den Spark-version som du vill installera på klustret.
    * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard ”admin”.
    * **SSH-användarnamn och lösenord**.

   Anteckna dessa värden.  Du behöver dem senare under kursen.

3. Välj **Jag godkänner villkoren som anges ovan**, välj **Fäst på instrumentpanelen** och klicka sedan på **Köp**. En ny panel visas med rubriken Skicka distribution för malldistribution. Det tar cirka 20 minuter att skapa klustret.

> [!NOTE]
> Den här artikeln skapar ett Spark-kluster som använder [Azure Storage-blobar som klusterlagring](hdinsight-hadoop-use-blob-storage.md). Du kan även skapa ett Spark-kluster som använder [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) som ytterligare lagring, utöver Azure Storage-blobar som standardlagring. Instruktioner finns i [Skapa ett HDInsight-kluster med Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-an-interactive-spark-sql-query"></a>Köra en interaktiv Spark SQL-fråga

I det här avsnittet använder du Jupyter-anteckningsboken för att köra interaktiva Spark SQL-frågor mot Spark-klustret du skapade tidigare. HDInsight Spark-kluster tillhandahåller tre kernels som du kan använda med Jupyter-anteckningsboken. Dessa är:

* **PySpark** (för appar som skrivits i Python)
* **PySpark3** (för appar som skrivits i Python3)
* **Spark** (för appar som skrivits i Scala)

I den här artikeln använder du **PySpark**-kerneln i anteckningsboken som du kör den interaktiva Spark SQL-frågan från. Mer information om dessa kernlar finns i [Använda kernlar i Jupyter-anteckningsböcker med Apache Spark-kluster i HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md). Några av de främsta fördelarna med att använda PySpark-kerneln är:

* Kontexter för Spark och Hive anges automatiskt.
* Du kan använda cellfunktioner som `%%sql` för att köra dina interaktiva SQL- eller Hive-frågor direkt utan några föregående kodfragment.
* Utdata för interaktiva frågor visualiseras automatiskt.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Skapa en Jupyter-anteckningsbok med PySpark-kernel

1. Öppna [Azure-portalen](https://portal.azure.com/).

2. Om du har valt att fästa klustret på instrumentpanelen klickar du på klusterikonen på instrumentpanelen för att öppna klusterbladet.

    Om du inte har fäst klustret på instrumentpanelen går du till den vänstra rutan och klickar på **HDInsight-kluster**. Klicka sedan på det kluster du har skapat.

3. I **Snabblänkar** klickar du på **Klusterinstrumentpaneler** och sedan på **Jupyter Notebook**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

   ![Öppna Jupyter-anteckningsboken för att köra interaktiv Spark SQL-fråga](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Öppna Jupyter-anteckningsboken för att köra interaktiv Spark SQL-fråga")

   > [!NOTE]
   > Du kan också nå Jupyter-anteckningsboken för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Skapa en anteckningsbok. Klicka på **Ny** och sedan på **PySpark**.

   ![Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga")

   En ny anteckningsbok skapas och öppnas med namnet Untitled(Untitled.pynb).

4. Klicka på anteckningsbokens namn högst upp och ange ett beskrivande namn om du vill.

    ![Ange ett namn för Jupter-anteckningsboken för att köra en interaktiv Spark-fråga från](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för Jupter-anteckningsboken för att köra en interaktiv Spark-fråga från")

5. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR** för att köra koden. Koden importerar de typer som krävs för det här scenariot:

        from pyspark.sql.types import *

    Du behöver inte uttryckligen skapa några kontexter eftersom du har skapat anteckningsboken med hjälp av PySpark-kerneln. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen.

    ![Status för interaktiv Spark SQL-fråga](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-interactive-spark-query-status.png "Status för interaktiv Spark SQL-fråga")

    Varje gång du kör en interaktiv fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras detta till en tom cirkel.

6. Registrera en exempeldatauppsättning som en tillfällig tabell (**hvac**) genom att köra följande kod.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data frame as a table to run queries against
        hvacdf.registerTempTable("hvac")

    Spark-kluster i HDInsight har en exempeldatafil, **hvac.csv**, under **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

7. Använd följande kod för att köra den interaktiva frågan på data.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Eftersom du använder en PySpark-kernel kan du nu direkt köra en interaktiv SQL-fråga för den tillfälliga tabellen **hvac** som du skapade med den användbara `%%sql`-funktionen. Mer information om `%%sql`-funktionen, samt andra användbara funktioner hos PySpark-kerneln, finns i [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med HDInsight Spark-kluster](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   Följande tabellutdata visas som standard.

     ![Tabellutdata från interaktivt Spark-frågeresultat](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result.png "Tabellutdata från interaktivt Spark-frågeresultat")

    Du kan också visa resultaten i andra visualiseringar. Ett områdesdiagram för samma utdata skulle som exempel se ut enligt nedan.

    ![Områdesdiagram över interaktivt Spark-frågeresultat](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-interactive-spark-query-result-area-chart.png "Områdesdiagram över interaktivt Spark-frågeresultat")

9. När du har kört programmet stänger du anteckningsboken för att frigöra klusterresurserna. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny.

## <a name="delete-the-cluster"></a>Ta bort klustret
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot-access-control"></a>Felsöka åtkomstkontroll

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="see-also"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](hdinsight-apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](hdinsight-apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Application Insight-telemetridataanalys i HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: storage-create-storage-account.md

