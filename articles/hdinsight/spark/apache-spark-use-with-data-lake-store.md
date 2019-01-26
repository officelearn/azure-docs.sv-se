---
title: Använda Apache Spark för att analysera data i Azure Data Lake Storage
description: Köra Spark-jobb för att analysera data som lagras i Azure Data Lake Storage
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: 29fef9177e40bd89dd0f179b028d2f044af8addb
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913104"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage"></a>Använd HDInsight Spark-kluster för att analysera data i Data Lake Storage

I den här självstudien använder du [Jupyter Notebook](https://jupyter.org/) tillgängliga med HDInsight Spark-kluster till ett jobb som läser data från ett Data Lake Storage-konto.

## <a name="prerequisites"></a>Förutsättningar

* Azure Data Lake Storage-konto. Följ anvisningarna på [Kom igång med Azure Data Lake Storage med Azure portal](../../data-lake-store/data-lake-store-get-started-portal.md).

* Azure HDInsight Spark-kluster med Data Lake Storage som lagring. Följ anvisningarna i [snabbstarten: Konfigurera kluster i HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    
## <a name="prepare-the-data"></a>Förbereda data

> [!NOTE]  
> Du behöver inte utföra det här steget om du har skapat HDInsight-kluster med Data Lake Storage som standardlagringsutrymme. Klustret skapas lägger till lite exempeldata i Data Lake Storage-kontot som du anger när du skapade klustret. Hoppa till avsnittet [använda HDInsight Spark-kluster med Data Lake Storage](#use-an-hdinsight-spark-cluster-with-data-lake-store).

Om du har skapat ett HDInsight-kluster med Data Lake Storage som ytterligare lagringsutrymme och Azure Storage Blob som standardlagringsutrymme, bör du först kopiera över exempeldata till Data Lake Storage-kontot. Du kan använda exemplet data från Azure Storage Blob som är associerade med HDInsight-klustret. Du kan använda den [ADLCopy verktyget](https://aka.ms/downloadadlcopy) att göra detta. Hämta och installera verktyget från länken.

1. Öppna en kommandotolk och navigera till den katalog där AdlCopy är installerad, vanligtvis `%HOMEPATH%\Documents\adlcopy`.

2. Kör följande kommando för att kopiera en specifik blob från behållaren källa till Data Lake Storage:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Kopiera den **HVAC.csv** exempeldata filen på **/HdiSamples/HdiSamples/SensorSampleData/hvac/** till Azure Data Lake Storage-kontot. Kodfragmentet bör se ut:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Se till att du namnen på filen och sökvägen är i rätt skiftläge.

3. Du uppmanas att ange autentiseringsuppgifter för Azure-prenumerationen som du har ditt Data Lake Storage-konto. Du bör se utdata som liknar följande fragment:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Datafilen (**HVAC.csv**) kopieras under en mapp **/hvac** i Data Lake Storage-kontot.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage"></a>Använda ett HDInsight Spark-kluster med Data Lake Storage

1. Från den [Azure-portalen](https://portal.azure.com/), på startsidan klickar du på panelen för Apache Spark-kluster (om du har Fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.

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

5. Läs in exempeldata i en tillfällig tabell med hjälp av den **HVAC.csv** filen som du kopierade till Data Lake Storage-kontot. Du kan komma åt data i Data Lake Storage-konto med hjälp av följande URL-mönster.

    * Om du har Data Lake Storage som standardlagringsutrymme blir HVAC.csv vid sökvägen som liknar följande URL:

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Alternativt kan du också använda ett förkortat format till exempel följande:

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Om du har Data Lake Storage som ytterligare lagringsutrymme, blir HVAC.csv på den plats där du har kopierat, till exempel:

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Klistra in följande kodexempel i en tom cell och Ersätt **MYDATALAKESTORE** med ditt Data Lake Storage-kontonamn och tryck på **SKIFT + RETUR**. Den här kodexemplet registrerar data i en tillfällig tabell som kallas **hvac**.

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

6. Eftersom du använder en PySpark-kernel kan du nu direkt köra en SQL-fråga för den tillfälliga tabellen **hvac** som du just skapade med den användbara `%%sql`-funktionen. Mer information om den `%%sql` magic, samt andra användbara funktioner hos PySpark-kerneln, finns i [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med Apache Spark HDInsight-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. När jobbet har slutförts visas följande tabellutdata som standard.

      ![Tabellutdata från frågeresultat](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabellutdata från frågeresultat")

     Du kan också visa resultaten i andra visualiseringar. Ett områdesdiagram för samma utdata skulle som exempel se ut enligt nedan.

     ![Områdesdiagram över frågeresultat](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Områdesdiagram över frågeresultat")

8. När du har kört appen bör du stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Då avslutas anteckningsboken och stängs ned.


## <a name="next-steps"></a>Nästa steg

* [Skapa ett fristående Scala programmet ska köras på Apache Spark-kluster](apache-spark-create-standalone-application.md)
* [Använda HDInsight-verktyg i Azure Toolkit för IntelliJ för att skapa Apache Spark-program för HDInsight Spark Linux-kluster](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Apache Spark-program för HDInsight Spark Linux-kluster](apache-spark-eclipse-tool-plugin.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
