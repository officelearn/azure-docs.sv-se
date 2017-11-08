---
title: "Köra interaktiva frågor i ett Azure HDInsight Spark-kluster | Microsoft Docs"
description: HDInsight Spark-snabbstart om hur du skapar ett Apache Spark-kluster i HDInsight.
keywords: "spark-snabbstart,interaktiv spark,interaktiv fråga,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.openlocfilehash: 0d93e261121f11d2a1082b9672e6d979955d3bee
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>Köra interaktiva frågor på ett HDInsight Spark-kluster

I den här artikeln använder du en Jupyter-anteckningsbok för att köra interaktiva Spark SQL-frågor mot Spark-kluster. Jupyter-anteckningsbok är ett webbaserat program som utökar konsolbaserad interaktiva upplevelse på webben. Mer information finns i [i Jupyter-anteckningsbok](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html).

Den här kursen kan du använda den **PySpark** kernel i Jupyter-anteckningsboken för att köra en interaktiva Spark SQL-fråga. Jupyter-anteckningsböcker på HDInsight-kluster stöder också två andra kernel - **PySpark3** och **Spark**. Mer information om kärnor och fördelarna med att använda **PySpark**, se [Använd Jupyter-anteckningsbok kärnor med Apache Spark-kluster i HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Krav

* **Ett Azure HDInsight Spark-kluster**. Instruktioner finns i [skapar ett Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>Skapa en Jupyter-anteckningsboken för att köra interaktiva frågor

Om du vill köra frågor som vi använder exempeldata är som standard tillgängligt i lagringen som är associerade med klustret. Du måste dock först hämta dessa data till Spark som en dataframe. När du har dataframe kan köra du frågor i det med Jupyter-anteckningsboken. I den här artikeln titta på hur du:

* Registrera data exempeldata som ett Spark-dataframe.
* Köra frågor på dataframe.

Nu sätter vi igång.

1. Öppna [Azure-portalen](https://portal.azure.com/). Om du har valt att fästa klustret på instrumentpanelen klickar du på klusterikonen på instrumentpanelen för att öppna klusterbladet.

    Om du inte har fäst klustret på instrumentpanelen går du till den vänstra rutan och klickar på **HDInsight-kluster**. Klicka sedan på det kluster du har skapat.

3. I **Snabblänkar** klickar du på **Klusterinstrumentpaneler** och sedan på **Jupyter Notebook**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

   ![Öppna Jupyter-anteckningsboken för att köra interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Öppna Jupyter-anteckningsboken för att köra interaktiv Spark SQL-fråga")

   > [!NOTE]
   > Du kan också nå Jupyter-anteckningsboken för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Skapa en anteckningsbok. Klicka på **Ny** och sedan på **PySpark**.

   ![Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga")

   En ny anteckningsbok skapas och öppnas med namnet Untitled(Untitled.pynb).

4. Klicka på anteckningsbokens namn högst upp och ange ett beskrivande namn om du vill.

    ![Ange ett namn för Jupter-anteckningsboken för att köra en interaktiv Spark-fråga från](./media/apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "Ange ett namn för Jupter-anteckningsboken för att köra en interaktiv Spark-fråga från")

5. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR** för att köra koden. Koden importerar de typer som krävs för det här scenariot:

        from pyspark.sql import *
        from pyspark.sql.types import *

    Du behöver inte uttryckligen skapa några kontexter eftersom du har skapat anteckningsboken med hjälp av PySpark-kerneln. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen.

    ![Status för interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status för interaktiv Spark SQL-fråga")

    Varje gång du kör en interaktiv fråga i Jupyter visar fönsterrubriken i webbläsaren statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras detta till en tom cirkel.

6. Låt oss se ut en ögonblicksbild av den innan du läser in data i ett Spark-kluster. Exempeldata används i den här kursen är tillgängliga som en CSV-fil på alla HDInsight Spark-kluster på **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Informationen som avbildar temperaturvariationer av en byggnad. Här är de första raderna i data.

    ![Ögonblicksbild av data för interaktiva Spark SQL-frågan](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "ögonblicksbild av data för interaktiva Spark SQL-fråga")

6. Skapa en dataframe och en tillfällig tabell (**hvac**) genom att köra följande kod. Den här självstudiekursen kommer skapar vi inte alla kolumner som är tillgängliga i CSV-filen. 

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

7. När tabellen har skapats kan köra en interaktiv fråga på data, kan du använda följande kod.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Eftersom du använder en PySpark-kernel kan du nu direkt köra en interaktiv SQL-fråga för den tillfälliga tabellen **hvac** som du skapade med den användbara `%%sql`-funktionen. Mer information om `%%sql`-funktionen, samt andra användbara funktioner hos PySpark-kerneln, finns i [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med HDInsight Spark-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   Följande tabellutdata visas som standard.

     ![Tabellutdata från interaktivt Spark-frågeresultat](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabellutdata från interaktivt Spark-frågeresultat")

9. Du kan också visa resultaten i andra visualiseringar. Om du vill se ett Områdesdiagram för samma utdata, Välj **område** sedan ange andra värden som visas.

    ![Områdesdiagram över interaktivt Spark-frågeresultat](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Områdesdiagram över interaktivt Spark-frågeresultat")

10. Från den **filen** Klicka på menyn i anteckningsbokens **kontrollpunkten och spara**. 

11. Om du startar den [nästa kurs](apache-spark-use-bi-tools.md) nu lämna anteckningsboken öppen. Om inte, stänga ned anteckningsboken för att frigöra resurserna i klustret: från den **filen** Klicka på menyn i anteckningsbokens **Stäng och stoppa**.

## <a name="next-step"></a>Nästa steg

I den här artikeln beskrivs hur du köra interaktiva frågor i Spark med Jupyter-anteckningsbok. Gå vidare till nästa artikel för att se hur de data som du har registrerat i Spark kan användas i en BI analytics verktyg som till exempel Power BI och Tableau. 

> [!div class="nextstepaction"]
>[Spark BI med hjälp av verktyg för visualisering av data med Azure HDInsight](apache-spark-use-bi-tools.md)




