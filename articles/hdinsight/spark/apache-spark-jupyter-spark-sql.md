---
title: Skapa ett Apache Spark-kluster i Azure HDInsight | Microsoft Docs
description: HDInsight Spark-snabbstart om hur du skapar ett Apache Spark-kluster i HDInsight.
keywords: "spark-snabbstart,interaktiv spark,interaktiv fråga,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2018
ms.author: jgao
ms.openlocfilehash: 1dbad36b7420791e70066263a566f1820823ad27
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Skapa ett Apache Spark-kluster i Azure HDInsight

Lär dig hur du skapar Apache Spark-kluster på Azure HDInsight och hur du kör Spark SQL-frågor mot Hive-tabeller. Mer information om Spark på HDInsight finns i [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* **En Azure-prenumeration**. Innan du börjar följa de här självstudierna måste du ha en Azure-prenumeration. Läs mer i [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Skapa HDInsight Spark-kluster

Skapa ett Spark-kluster i HDInsight med en [Azure Resource Manager-mall](../hdinsight-hadoop-create-linux-clusters-arm-templates.md). Du hittar mallen på [github](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Information om andra metoder för att skapa kluster finns i [Skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

1. Klicka på följande bild för att öppna mallen i Azure Portal.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Ange följande värden:

    ![Skapa HDInsight Spark-kluster med en Azure Resource Manager-mall](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Skapa Spark-kluster i HDInsight med en Azure Resource Manager-mall")

    * **Prenumeration**: Välj den Azure-prenumeration som ska användas till att skapa klustret.
    * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig. Resursgrupp används för att hantera Azure-resurser till dina projekt.
    * **Plats**: Välj en plats för resursgruppen. Mallen använder den här platsen för att skapa klustret samt standardklusterlagringen.
    * **Klusternamn**: Ange ett namn på det HDInsight-kluster som du vill skapa.
    * **Klustrets inloggningsnamn och lösenord**: Inloggningsnamnet är som standard ”admin”.
    * **SSH-användarnamn och lösenord**.

3. Välj **Jag godkänner villkoren som anges ovan**, välj **Fäst på instrumentpanelen** och klicka sedan på **Köp**. En ny panel visas med rubriken **Skicka malldistribution**. Det tar cirka 20 minuter att skapa klustret.

Om du stöter på ett problem med att skapa HDInsight-kluster kan det bero på att du inte har rätt behörighet för att göra det. Mer information finns i [åtkomstkravkontrollen](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> Den här artikeln skapar ett Spark-kluster som använder [Azure Storage-blobar som klusterlagring](../hdinsight-hadoop-use-blob-storage.md). Du kan även skapa ett Spark-kluster som använder [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) som standardlagringsutrymme. Instruktioner finns i [Skapa ett HDInsight-kluster med Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok

[Jupyter Notebook](http://jupyter.org) är en interaktiv miljö för anteckningsböcker som har stöd för olika programmeringsspråk så att du kan interagera med data, kombinera kod med markdowntext och utföra enkla visualiseringar. Spark i HDInsight innehåller också [Zeppelin Notebook](apache-spark-zeppelin-notebook.md). Jupyter Notebook används i den här självstudien.

**Så här skapar du en ny Jupyter-anteckningsbok**

1. Öppna [Azure-portalen](https://portal.azure.com/).

2. Öppna Spark-klustret du skapade. Instruktioner finns i avsnittet [Lista och visa kluster](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

3. I portalen klickar du på **Klusterinstrumentpaneler** och sedan på **Jupyter Notebook**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

   ![Öppna en Jupyter-anteckningsbok för att köra en interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Öppna en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga")

   > [!NOTE]
   > Du kan också nå Jupyter-anteckningsboken för ditt kluster genom att öppna följande URL-adress i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Klicka på **Ny** och sedan på **PySpark** för att skapa en anteckningsbok. Jupyter-anteckningsböcker på HDInsight-kluster har stöd för tre kernels – **PySpark**, **PySpark3** och **Spark**. Kerneln **PySpark** används i den här självstudien. Mer information om dessa kernlar, och om fördelarna med att använda **PySpark**, finns i [Använda kernlar i Jupyter-anteckningsböcker med Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

   ![Skapa en Jupyter-anteckningsbok för att köra en interaktiv Spark SQL-fråga](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Skapa en Jupyter-anteckningsbok för att köra en interaktiv Spark SQL-fråga")

   En ny anteckningsbok skapas och öppnas med namnet Untitled(Untitled.pynb).

4. Klicka på anteckningsbokens namn högst upp och ange ett beskrivande namn om du vill.

    ![Ange ett namn för Jupyter-anteckningsboken för att köra en interaktiv Spark-fråga från](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för Jupyter-anteckningsboken för att köra en interaktiv Spark-fråga från")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Köra Spark SQL-instruktioner på en Hive-tabell

SQL (Structured Query Language) är det vanligaste språket för frågor och definition av data. Spark SQL fungerar som ett tillägg till Apache Spark för bearbetning av strukturerade data med den välbekanta SQL-syntaxen.

Spark SQL stöder både SQL och HiveQL som frågespråk. Funktionerna inkluderar bindning i Python, Scala och Java. Med det kan du fråga data som lagras på många platser, som externa databaser, strukturerade datafiler (t.ex. JSON) och Hive-tabeller.

Ett exempel på läsning av data från en csv-fil i stället för en Hive-tabell finns i [Run interactive queries on Spark clusters in HDInsight](apache-spark-load-data-run-query.md) (Köra interaktiva frågor mot Spark-kluster i HDInsight).

**Att köra Spark SQL**

1. När du startar den bärbara datorn för första gången utför kerneln några uppgifter i bakgrunden. Vänta tills kerneln är klar. Kerneln är klar när du ser en tom cirkel bredvid kernelnamnet i den bärbara datorn. En fylld cirkel anger att kerneln är upptagen.

    ![Hive-fråga i HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive-fråga i HDInsight Spark")

2. När kerneln är klar klistrar du in följande kod i en tom cell och trycker sedan på **SKIFT+RETUR** för att köra koden. Resultatet bör innehålla en `hivesampletable` som är tillgänglig på klustret som standard.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    ![Hive-fråga i HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-fråga i HDInsight Spark")

    När du använder en Jupyter-anteckningsbok med ditt HDInsight Spark-kluster får du en förinställd `sqlContext` som du kan använda för att köra Hive-frågor med hjälp av Spark SQL. `%%sql` anger att Jupyter Notebook ska använda den förinställda `sqlContext` när Hive-frågan ska köras. Frågan hämtar de översta 10 raderna från en Hive-tabell (**hivesampletable**) som medföljer alla HDInsight-kluster som standard. Mer information om `%%sql` och förinställda kontexter finns i [Jupyter-kernlar som är tillgängliga för ett HDInsight-kluster](apache-spark-jupyter-notebook-kernels.md).

    Varje gång du kör en fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet.
    
2. Kör ytterligare en fråga för att visa data i `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    Skärmen bör uppdateras så att frågeresultatet visas.

    ![Hive-frågeresultatet i HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive-frågeresultatet i HDInsight Spark")

2. Öppna menyn **Arkiv** i anteckningsboken och klicka på **Stäng och stoppa**. När du stänger anteckningsboken frigörs klusterresurserna.

3. Om du planerar att utföra nästa steg vid ett senare tillfälle är det viktigt att du tar bort HDInsight-klustret som du skapade i den här artikeln. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nästa steg 

I den här artikeln beskrivs hur du skapar ett HDInsight Spark-kluster och kör en grundläggande Spark SQL-fråga. Gå vidare till nästa artikel om du vill lära dig hur du använder ett HDInsight Spark-kluster för att köra interaktiva frågor på exempeldata.

> [!div class="nextstepaction"]
>[Köra interaktiva frågor på ett HDInsight Spark-kluster](apache-spark-load-data-run-query.md)



