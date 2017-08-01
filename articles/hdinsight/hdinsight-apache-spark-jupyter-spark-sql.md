---
title: Skapa ett Apache Spark-kluster i Azure HDInsight | Microsoft Docs
description: HDInsight Spark-snabbstart om hur du skapar ett Apache Spark-kluster i HDInsight.
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
ms.date: 07/20/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 0625984bf10588fe50a2632285f565eb79b66ab7
ms.contentlocale: sv-se
ms.lasthandoff: 07/22/2017

---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Skapa ett Apache Spark-kluster i Azure HDInsight

I den här artikeln får du lära dig hur du skapar ett Apache Spark-kluster i Azure HDInsight.

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
    * **Plats**: Välj en plats för resursgruppen. Mallen använder den här platsen för att skapa klustret samt standardklusterlagringen.
    * **Klusternamn**: Ange ett namn på det HDInsight-kluster som du vill skapa.
    * **Spark-version**: Välj **2.0** som den version som ska installeras i klustret.
    * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard ”admin”.
    * **SSH-användarnamn och lösenord**.

   Anteckna dessa värden.  Du behöver dem senare under kursen.

3. Välj **Jag godkänner villkoren som anges ovan**, välj **Fäst på instrumentpanelen** och klicka sedan på **Köp**. En ny panel visas med rubriken Skicka distribution för malldistribution. Det tar cirka 20 minuter att skapa klustret.

Om du stöter på ett problem med att skapa HDInsight-kluster kan det bero på att du inte har rätt behörighet för att göra det. Mer information finns i [åtkomstkravkontrollen](hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> Den här artikeln skapar ett Spark-kluster som använder [Azure Storage-blobar som klusterlagring](hdinsight-hadoop-use-blob-storage.md). Du kan även skapa ett Spark-kluster som använder [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) som standardlagringsutrymme. Instruktioner finns i [Skapa ett HDInsight-kluster med Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-a-hive-query-using-spark-sql"></a>Köra en Hive-fråga med Spark SQL

När du använder en Jupyter-anteckningsbok som har konfigurerats för HDInsight Spark-klustret kan det visas en förinställning `sqlContext` som du kan använda för att köra Hive-frågor med hjälp av Spark SQL. I det här avsnittet lär du dig att starta en Jupyter-anteckningsbok och sedan köra en grundläggande Hive-fråga.

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

5.  Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR** för att köra koden. I följande kodexempel anger `%%sql` (kallas sql magic) att Jupyter-anteckningsboken ska använda förinställningen `sqlContext` för att köra Hive-frågan. Frågan hämtar de översta 10 raderna från en Hive-tabell (**hivesampletable**) som är tillgängliga som standard i alla HDInsight-kluster.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![Hive-fråga i HDInsight Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-fråga i HDInsight Spark")

    Mer information om `%%sql` och förinställda kontexter finns i [Jupyter-kernlar som är tillgängliga för ett HDInsight-kluster](hdinsight-apache-spark-jupyter-notebook-kernels.md).

    > [!NOTE]
    > Varje gång du kör en fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras detta till en tom cirkel.
    >
    >
    
6. Skärmen bör uppdateras så att frågeresultatet visas.

    ![Hive-frågeresultatet i HDInsight Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive-frågeresultatet i HDInsight Spark")

7. När du har kört programmet stänger du anteckningsboken för att frigöra klusterresurserna. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny.

## <a name="next-step"></a>Nästa steg

I den här artikeln beskrivs hur du skapar ett HDInsight Spark-kluster och kör en grundläggande Spark SQL-fråga. Lär dig att [läsa in data till HDInsight Spark och köra en interaktiv fråga](hdinsight-apache-spark-load-data-run-query.md).

Om du planerar att utföra nästa steg vid ett senare tillfälle är det viktigt att du tar bort HDInsight-klustret. 

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
