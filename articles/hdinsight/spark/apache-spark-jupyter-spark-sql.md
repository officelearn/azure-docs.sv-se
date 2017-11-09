---
title: Skapa ett Apache Spark-kluster i Azure HDInsight | Microsoft Docs
description: HDInsight Spark-snabbstart om hur du skapar ett Apache Spark-kluster i HDInsight.
keywords: "spark-snabbstart,interaktiv spark,interaktiv fråga,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: maxluk
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
ms.date: 09/07/2017
ms.author: maxluk
ms.openlocfilehash: aa5ba691833cc5c1f256971a3eb1ab8240293fc4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Skapa ett Apache Spark-kluster i Azure HDInsight

I den här artikeln får du lära dig hur du skapar ett Apache Spark-kluster i Azure HDInsight och sedan kör en Spark SQL-fråga på en Hive-tabell. Mer information om Spark på HDInsight finns i [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md).

   ![Snabbstartsdiagram som beskriver steg för att skapa ett Apache Spark-kluster på Azure HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Spark-snabbstart med Apache Spark i HDInsight. Illustrerade steg: skapa ett kluster, kör interaktiv Spark-fråga")

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Se [Skapa ett kostnadsfritt Azure-konto i dag](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Skapa HDInsight Spark-kluster

I det här avsnittet skapar du ett Spark-kluster i HDInsight med en [Azure Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Information om andra metoder för att skapa kluster finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

1. Klicka på följande bild för att öppna mallen i Azure Portal.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Ange följande värden:

    ![Skapa HDInsight Spark-kluster med en Azure Resource Manager-mall](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Skapa Spark-kluster i HDInsight med en Azure Resource Manager-mall")

    * **Prenumeration**: Välj din Azure-prenumeration för det här klustret.
    * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig. Resursgrupp används för att hantera Azure-resurser till dina projekt.
    * **Plats**: Välj en plats för resursgruppen. Mallen använder den här platsen för att skapa klustret samt standardklusterlagringen.
    * **Klusternamn**: Ange ett namn på det HDInsight-kluster som du vill skapa.
    * **Spark-version**: Välj **2.0** som den version som ska installeras i klustret.
    * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard ”admin”.
    * **SSH-användarnamn och lösenord**.

   Anteckna dessa värden.  Du behöver dem senare under kursen.

3. Välj **Jag godkänner villkoren som anges ovan**, välj **Fäst på instrumentpanelen** och klicka sedan på **Köp**. En ny panel visas med rubriken Skicka distribution för malldistribution. Det tar cirka 20 minuter att skapa klustret.

Om du stöter på ett problem med att skapa HDInsight-kluster kan det bero på att du inte har rätt behörighet för att göra det. Mer information finns i [åtkomstkravkontrollen](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> Den här artikeln skapar ett Spark-kluster som använder [Azure Storage-blobar som klusterlagring](../hdinsight-hadoop-use-blob-storage.md). Du kan även skapa ett Spark-kluster som använder [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) som standardlagringsutrymme. Instruktioner finns i [Skapa ett HDInsight-kluster med Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Köra Spark SQL-instruktioner på en Hive-tabell

SQL (Structured Query Language) är det vanligaste språket för frågor och definition av data. Grundarna till Spark ville dra nytta av kunskapen och tillgängliggöra det välkända datafrågespråket till en bredare målgrupp med analytiker som ville arbeta med data som finns på Hadoop Distributed File System (HDFS). Spark SQL är det erbjudandet. Det fungerar som ett tillägg till Apache Spark för bearbetning av strukturerade data med den välkända SQL-syntaxen.

Spark SQL stöder både SQL och HiveQL som frågespråk. Funktionerna inkluderar bindning i Python, Scala och Java. Med det kan du fråga data som lagras på många platser, som externa databaser, strukturerade datafiler (t.ex. JSON) och Hive-tabeller.

### <a name="running-spark-sql-on-an-hdinsight-cluster"></a>Köra Spark SQL på ett HDInsight-kluster

När du använder en Jupyter-anteckningsbok som har konfigurerats för HDInsight Spark-klustret kan det visas en förinställning `sqlContext` som du kan använda för att köra Hive-frågor med hjälp av Spark SQL. I det här avsnittet får du lära dig att starta en Jupyter Notebook och sedan köra en grundläggande Spark SQL-fråga på en befintlig Hive-tabell (**hivesampletable**) som är tillgänglig på alla HDInsight-kluster.

1. Öppna [Azure-portalen](https://portal.azure.com/).

2. Om du har valt att fästa klustret på instrumentpanelen klickar du på klusterikonen på instrumentpanelen för att öppna klusterbladet.

    Om du inte har fäst klustret på instrumentpanelen går du till den vänstra rutan och klickar på **HDInsight-kluster**. Klicka sedan på det kluster du har skapat.

3. I **Snabblänkar** klickar du på **Klusterinstrumentpaneler** och sedan på **Jupyter Notebook**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

   ![Öppna Jupyter-anteckningsboken för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Öppna Jupyter-anteckningsboken för att köra interaktiv Spark SQL-fråga")

   > [!NOTE]
   > Du kan också nå Jupyter-anteckningsboken för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Skapa en anteckningsbok. Klicka på **Ny** och sedan på **PySpark**.

   ![Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga")

   En ny anteckningsbok skapas och öppnas med namnet Untitled(Untitled.pynb).

4. Klicka på anteckningsbokens namn högst upp och ange ett beskrivande namn om du vill.

    ![Ange ett namn för Jupyter-anteckningsboken för att köra en interaktiv Spark-fråga från](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för Jupyter-anteckningsboken för att köra en interaktiv Spark-fråga från")

5.  Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR** för att köra koden. I följande kodexempel anger `%%sql` (kallas sql magic) att Jupyter-anteckningsboken ska använda förinställningen `sqlContext` för att köra Hive-frågan. Frågan hämtar de översta 10 raderna från en Hive-tabell (**hivesampletable**) som är tillgängliga som standard i alla HDInsight-kluster.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![Hive-fråga i HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-fråga i HDInsight Spark")

    Mer information om `%%sql` och förinställda kontexter finns i [Jupyter-kernlar som är tillgängliga för ett HDInsight-kluster](apache-spark-jupyter-notebook-kernels.md).

    > [!NOTE]
    > Varje gång du kör en fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras detta till en tom cirkel.
    >
    >
    
6. Skärmen bör uppdateras så att frågeresultatet visas.

    ![Hive-frågeresultatet i HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive-frågeresultatet i HDInsight Spark")

7. När du har kört programmet stänger du anteckningsboken för att frigöra klusterresurserna. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny.

8. Om du planerar att utföra nästa steg vid ett senare tillfälle är det viktigt att du tar bort HDInsight-klustret som du skapade i den här artikeln. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Nästa steg 

I den här artikeln beskrivs hur du skapar ett HDInsight Spark-kluster och kör en grundläggande Spark SQL-fråga. Gå vidare till nästa artikel om du vill lära dig hur du använder ett HDInsight Spark-kluster för att köra interaktiva frågor på exempeldata.

> [!div class="nextstepaction"]
>[Köra interaktiva frågor på ett HDInsight Spark-kluster](apache-spark-load-data-run-query.md)



