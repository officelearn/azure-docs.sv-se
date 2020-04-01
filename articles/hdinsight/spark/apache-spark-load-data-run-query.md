---
title: 'Självstudiekurs: Ladda data & köra frågor med Apache Spark - Azure HDInsight'
description: Självstudiekurs - Lär dig hur du läser in data och kör interaktiva frågor på Spark-kluster i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: 5eb6788a558e4429296731f1693edd18bf92f98f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77198896"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Självstudie: Läsa in data och köra frågor i ett Apache Spark-kluster i Azure HDInsight

I den här självstudien får du lära dig hur du skapar en dataram från en csv-fil och hur du kör interaktiva Spark SQL-frågor mot ett [Apache Spark-kluster](https://spark.apache.org/) i Azure HDInsight. I Spark är en dataram en distribuerad datasamling som har ordnats i namngivna kolumner. Begreppsmässigt motsvarar dataramen en tabell i en relationsdatabas eller en dataram i R/Python.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Skapa en dataram från en csv-fil
> * Köra frågor i dataramen

## <a name="prerequisites"></a>Krav

Ett Apache Spark-kluster i HDInsight. Se [Skapa ett Apache Spark-kluster](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter-anteckningsbok

Jupyter Notebook är en interaktiv anteckningsboksmiljö som stöder flera olika datorspråk. Du kan använda anteckningsboken för att interagera med dina data, kombinera kod med markdown-text och utföra enkla visualiseringar.

1. Redigera `https://SPARKCLUSTER.azurehdinsight.net/jupyter` URL:en `SPARKCLUSTER` genom att ersätta med namnet på Spark-klustret. Ange sedan den redigerade webbadressen i en webbläsare. Ange autentiseringsuppgifterna för klustret om du uppmanas att göra det.

2. På Jupyters webbsida väljer du **Ny** > **PySpark** för att skapa en anteckningsbok.

   ![Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Skapa en Jupyter-anteckningsbok för att köra interaktiv Spark SQL-fråga")

   En ny anteckningsbok skapas och öppnas med`Untitled.ipynb`namnet Untitled( ).

    > [!NOTE]  
    > Genom att använda PySpark-kärnan för att skapa en anteckningsbok skapas automatiskt sessionen `spark` för dig när du kör den första kodcellen. Du behöver inte uttryckligen skapa sessionen.

## <a name="create-a-dataframe-from-a-csv-file"></a>Skapa en dataram från en csv-fil

Program kan skapa dataramar direkt från filer eller mappar i fjärrlagringen, till exempel Azure Storage eller Azure Data Lake Storage; från ett Hive-bord. eller från andra datakällor som stöds av Spark, till exempel Cosmos DB, Azure SQL DB, DW och så vidare. Följande skärmbild visar en ögonblicksbild av den HVAC.csv-fil som används i självstudien. Csv-filen finns i alla HDInsight Spark-kluster. Datan visar temperaturvariationer i vissa byggnader.

![Ögonblicksbild av data för interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Ögonblicksbild av data för interaktiv Spark SQL-fråga")

1. Klistra in följande kod i en tom cell i den jupyterda anteckningsboken och tryck sedan på **SKIFT + RETUR** för att köra koden. Koden importerar de typer som krävs för det här scenariot:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    När du kör en interaktiv fråga i Jupyter visar fönstret i webbläsaren eller fliktiteln statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras detta till en tom cirkel.

    ![Status för interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status för interaktiv Spark SQL-fråga")

1. Observera att sessions-ID:t returnerades. Från bilden ovan är sessions-ID 0. Om du vill kan du hämta sessionsinformationen genom att navigera till `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` den plats där CLUSTERNAME är namnet på Spark-klustret och ID:t är ditt sessions-ID-nummer.

1. Kör följande kod för att skapa en dataram och en tillfällig tabell (**hvac**).

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Köra frågor i dataramen

När tabellen har skapats kan du köra en interaktiv fråga på datan.

1. Kör följande kod i en tom cell i anteckningsboken:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Följande tabellutdata visas.

     ![Tabellutdata för interaktivt Spark-frågeresultat](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabellutdata för interaktivt Spark-frågeresultat")

2. Du kan också visa resultaten i andra visualiseringar. Om du vill se ett ytdiagram för samma utdata väljer du **Yta** och anger sedan de andra värden som visas.

    ![Områdesdiagram över interaktivt Spark-frågeresultat](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Områdesdiagram över interaktivt Spark-frågeresultat")

3. På menyraden för anteckningsbok navigerar du till**Spara och kontrollpunkt för** **Fil** > .

4. Om du ska starta [nästa självstudie](apache-spark-use-bi-tools.md) direkt kan du lämna anteckningsboken öppen. Om inte, stäng av anteckningsboken för att frigöra klusterresurserna: på menyraden för anteckningsbok navigerar du till Stäng**och stoppa** **fil** >  .

## <a name="clean-up-resources"></a>Rensa resurser

Med HDInsight lagras dina data- och Jupyter-anteckningsböcker i Azure Storage eller Azure Data Lake Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används. Om du tänker arbeta med nästa självstudie direkt kan du behålla klustret.

Öppna klustret i Azure Portal och välj **Ta bort**.

![Ta bort HDInsight-kluster](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. När resursgruppen tas bort, tas även HDInsight Spark-klustret och standardkontot för lagring bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar en dataram från en csv-fil och hur du kör interaktiva Spark SQL-frågor mot ett Apache Spark-kluster i Azure HDInsight. Gå vidare till nästa artikel för att se hur de data som du har registrerat i Apache Spark kan hämtas till ett BI-analysverktyg såsom Power BI.

> [!div class="nextstepaction"]
> [Analysera data med hjälp av BI-verktyg](apache-spark-use-bi-tools.md)
