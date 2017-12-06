---
title: "Köra interaktiva frågor i ett Azure HDInsight Spark-kluster | Microsoft Docs"
description: HDInsight Spark-snabbstart om hur du skapar ett Apache Spark-kluster i HDInsight.
keywords: "spark-snabbstart,interaktiv spark,interaktiv fråga,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 78ab44a7afa6523e1e9e4082b3f45b1a28affe77
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>Köra interaktiva frågor i Spark-kluster i HDInsight

Lär dig hur du använder Jupyter-anteckningsboken för att köra interaktiva Spark SQL-frågor mot Spark-kluster. 

[Jupyter-anteckningsbok](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) är ett webbaserat program som utökar konsolbaserad interaktiva upplevelse på webben. Spark i HDInsight innehåller också [Zeppelin anteckningsboken](apache-spark-zeppelin-notebook.md). Jupyter-anteckningsbok används i den här kursen.

Jupyter-anteckningsböcker på HDInsight-kluster stöder tre kernel - **PySpark**, **PySpark3**, och **Spark**. Den **PySpark** kernel används i den här kursen. Mer information om kärnor och fördelarna med att använda **PySpark**, se [Använd Jupyter-anteckningsbok kärnor med Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md). Om du vill använda Zeppelin anteckningsboken finns [använda Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight](./apache-spark-zeppelin-notebook.md).

I kursen får fråga du efter data i en csv-fil. Du måste först läsa in data i Spark som en dataframe. Du kan köra frågor på dataframe med Jupyter-anteckningsbok. 

## <a name="prerequisites"></a>Krav

* **Ett Azure HDInsight Spark-kluster**. Instruktioner finns i avsnittet [skapar ett Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **En Jupyter-anteckningsbok med PySpark**. Instruktioner finns i avsnittet [skapa en Jupyter-anteckningsbok](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

## <a name="create-a-dataframe-from-a-csv-file"></a>Skapa en dataframe från en csv-fil

Med en SQLContext kan program skapa dataframes från en befintlig RDD, en Hive-tabell eller datakällor. 

**Skapa en dataframe från en csv-fil**

1. Skapa en Jupyter-anteckningsbok med PySpark om du inte har någon. Instruktioner finns i avsnittet [skapa en Jupyter-anteckningsbok](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Klistra in följande kod i en tom cell för anteckningsboken och tryck sedan på **SKIFT + RETUR** att köra koden. Koden importerar de typer som krävs för det här scenariot:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    Genom att använda PySpark-kerneln att skapa en bärbar dator på Spark och Hive skapas kontexter automatiskt åt dig när du kör den första kodcellen. Du behöver inte uttryckligen skapa några kontexter.

    När du kör en interaktiv fråga i Jupyter web webbläsare fönster eller flik beskrivningen visar en **(upptagen)** status tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras detta till en tom cirkel.

    ![Status för interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status för interaktiv Spark SQL-fråga")

3. Kör följande kod för att skapa en dataframe och en tillfällig tabell (**hvac**) genom att köra följande kod: koden inte extrahera alla kolumnerna som är tillgängliga i CSV-filen. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```
    Följande skärmbild visar en ögonblicksbild av HVAC.csv-filen. Csv-filen innehåller alla HDInsigt Spark-kluster. Informationen som avbildar temperaturvariationer av en byggnad.

    ![Ögonblicksbild av data för interaktiva Spark SQL-frågan](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "ögonblicksbild av data för interaktiva Spark SQL-fråga")

## <a name="run-queries-on-the-dataframe"></a>Köra frågor i dataframe

När tabellen har skapats kan du köra en interaktiv fråga på data.

**Att köra en fråga**

1. Kör följande kod i en tom cell för den bärbara datorn:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Eftersom PySpark-kerneln används i anteckningsboken, du kan nu direkt köra en interaktiv SQL-fråga på den temporära tabellen **hvac** som du skapade med hjälp av den `%%sql` Magiskt tal. Mer information om `%%sql`-funktionen, samt andra användbara funktioner hos PySpark-kerneln, finns i [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med HDInsight Spark-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   Följande tabellutdata visas som standard.

     ![Tabellutdata från interaktivt Spark-frågeresultat](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabellutdata från interaktivt Spark-frågeresultat")

3. Du kan också visa resultaten i andra visualiseringar. Om du vill se ett Områdesdiagram för samma utdata, Välj **område** sedan ange andra värden som visas.

    ![Områdesdiagram över interaktivt Spark-frågeresultat](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Områdesdiagram över interaktivt Spark-frågeresultat")

10. Från den **filen** Klicka på menyn i anteckningsbokens **kontrollpunkten och spara**. 

11. Om du startar den [nästa kurs](apache-spark-use-bi-tools.md) nu lämna anteckningsboken öppen. Om inte, stänga ned anteckningsboken för att frigöra resurserna i klustret: från den **filen** Klicka på menyn i anteckningsbokens **Stäng och stoppa**.

## <a name="next-step"></a>Nästa steg

I den här artikeln beskrivs hur du köra interaktiva frågor i Spark med Jupyter-anteckningsbok. Gå vidare till nästa artikel för att se hur de data som du har registrerat i Spark kan användas i en BI analytics verktyg som till exempel Power BI och Tableau. 

> [!div class="nextstepaction"]
>[Spark BI med hjälp av verktyg för visualisering av data med Azure HDInsight](apache-spark-use-bi-tools.md)




