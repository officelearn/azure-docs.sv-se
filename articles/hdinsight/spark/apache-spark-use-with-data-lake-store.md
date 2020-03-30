---
title: Analysera Azure Data Lake Storage Gen1 med HDInsight Apache Spark
description: Kör Apache Spark-jobb för att analysera data som lagras i Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818170"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>Använd HDInsight Spark-kluster för att analysera data i Data Lake Storage Gen1

I den här artikeln använder du [Jupyter Notebook](https://jupyter.org/) som är tillgänglig med HDInsight Spark-kluster för att köra ett jobb som läser data från ett DataSjölagringskonto.

## <a name="prerequisites"></a>Krav

* Azure Data Lake Storage Gen1-konto. Följ instruktionerna på [Kom igång med Azure Data Lake Storage Gen1 med Hjälp av Azure-portalen](../../data-lake-store/data-lake-store-get-started-portal.md).

* Azure HDInsight Spark kluster med Data Lake Storage Gen1 som lagring. Följ instruktionerna vid [Snabbstart: Konfigurera kluster i HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

## <a name="prepare-the-data"></a>Förbereda data

> [!NOTE]  
> Du behöver inte utföra det här steget om du har skapat HDInsight-klustret med Data Lake Storage som standardlagring. Processen för att skapa kluster lägger till några exempeldata i datasjölagringskontot som du anger när du skapar klustret. Gå till avsnittet Använd HDInsight Spark-kluster med DataSjölagring.

Om du har skapat ett HDInsight-kluster med Data Lake Storage som ytterligare lagring och Azure Storage Blob som standardlagring bör du först kopiera över vissa exempeldata till datasjölagringskontot. Du kan använda exempeldata från Azure Storage Blob som är associerad med HDInsight-klustret. Du kan använda [ADLCopy-verktyget](https://www.microsoft.com/download/details.aspx?id=50358) för att göra det. Ladda ner och installera verktyget från länken.

1. Öppna en kommandotolk och navigera till katalogen `%HOMEPATH%\Documents\adlcopy`där AdlCopy är installerat, vanligtvis .

2. Kör följande kommando om du vill kopiera en specifik blob från källbehållaren till DataSjölagring:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Kopiera **exempeldatafilen HVAC.csv** på **/HdiSamples/HdiSamples/SensorSampleData/hvac/** till Azure Data Lake Storage-kontot. Kodavsnittet ska se ut så här:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Kontrollera att fil- och sökvägsnamnen använder rätt versaler.

3. Du uppmanas att ange autentiseringsuppgifterna för Azure-prenumerationen som du har ditt Data Lake Storage-konto under. Du bör se utdata som liknar följande fragment:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Datafilen (**HVAC.csv**) kopieras under en mapp **/vvs** i datasjölagringskontot.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Använda ett HDInsight Spark-kluster med Data Lake Storage Gen1

1. Från [Azure-portalen](https://portal.azure.com/), från startbordet, klicka på panelen för ditt Apache Spark-kluster (om du fäst den på startbordet). Du kan också navigera till klustret under **Bläddra bland alla** > **HDInsight-kluster**.

2. Klicka på **Snabblänkar** på Spark-klusterbladet och sedan på **Jupyter Notebook** på **Klusterinstrumentpanel**-bladet. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

   > [!NOTE]  
   > Du kan också nå Jupyter Notebook för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Skapa en ny anteckningsbok. Klicka på **Ny** och sedan på **PySpark**.

    ![Skapa en ny Jupyter-anteckningsbok](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Skapa en ny Jupyter-anteckningsbok")

4. Du behöver inte uttryckligen skapa några kontexter eftersom du har skapat anteckningsboken med hjälp av PySpark-kerneln. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen. Du kan börja med att importera de typer som krävs för det här scenariot. Det gör du genom att klistra in följande kodfragment i en cell och trycka på **SKIFT + RETUR**.

        from pyspark.sql.types import *

    Varje gång du kör ett jobb i Jupyter kommer fönsterrubriken i din webbläsare att visa statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du kan även se en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras denna till en tom cirkel.

     ![Status för ett Jupyter-anteckningsboksjobb](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Status för ett Jupyter-anteckningsboksjobb")

5. Läs in exempeldata i en temporär tabell med filen **HVAC.csv** som du kopierade till datasjölagringsgenm1-kontot. Du kan komma åt data i datasjölagringskontot med hjälp av följande URL-mönster.

   * Om du har Data Lake Storage Gen1 som standardlagring kommer HVAC.csv att vara på sökvägen som liknar följande WEBBADRESS:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Du kan också använda ett förkortat format som följande:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Om du har Data Lake Storage som ytterligare lagring kommer HVAC.csv att vara på den plats där du kopierade den, till exempel:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     I en tom cell klistrar du in följande kodexempel, ersätter **MYDATALAKESTORE** med ditt datasjölagringskontonamn och trycker på **SKIFT + RETUR**. Den här kodexemplet registrerar data i en tillfällig tabell som kallas **hvac**.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Eftersom du använder en PySpark-kernel kan du nu direkt köra en SQL-fråga för den tillfälliga tabellen **hvac** som du just skapade med den användbara `%%sql`-funktionen. Mer information om `%%sql` magin, liksom andra magier som finns med PySpark-kärnan, finns [i Kärnor som finns tillgängliga på Jupyter-anteckningsböcker med Apache Spark HDInsight-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. När jobbet har slutförts visas följande tabellutdata som standard.

      ![Tabellutdata från frågeresultat](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabellutdata från frågeresultat")

     Du kan också visa resultaten i andra visualiseringar. Ett områdesdiagram för samma utdata skulle som exempel se ut enligt nedan.

     ![Områdesdiagram över frågeresultat](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Områdesdiagram över frågeresultat")

8. När du har kört appen bör du stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Då avslutas anteckningsboken och stängs ned.


## <a name="next-steps"></a>Nästa steg

* [Skapa ett fristående Scala-program som ska köras i Apache Spark-kluster](apache-spark-create-standalone-application.md)
* [Använd HDInsight Tools i Azure Toolkit för IntelliJ för att skapa Apache Spark-program för HDInsight Spark Linux-kluster](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight Tools i Azure Toolkit for Eclipse för att skapa Apache Spark-program för HDInsight Spark Linux-kluster](apache-spark-eclipse-tool-plugin.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
