---
title: 'Självstudier: Läsa in data och köra frågor i ett Apache Spark-kluster i Azure HDInsight '
description: Lär dig mer om att läsa in data och köra interaktiva frågor på Spark-kluster i Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 04/03/2019
ms.openlocfilehash: f480aeb7e126cb6ab8286bbfbfb8441fefeb07ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097127"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Självstudier: Läsa in data och köra frågor i ett Apache Spark-kluster i Azure HDInsight

I den här självstudien lär du dig hur du skapar en dataram från en csv-fil och kör interaktiva Spark SQL-frågor mot ett [Apache Spark](https://spark.apache.org/)-kluster i Azure HDInsight. I Spark är en dataram en distribuerad datasamling som har ordnats i namngivna kolumner. Begreppsmässigt motsvarar dataramen en tabell i en relationsdatabas eller en dataram i R/Python.
 
I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en dataram från en csv-fil
> * Köra frågor i dataramen

## <a name="prerequisites"></a>Nödvändiga komponenter

* Slutför [Skapa ett Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Skapa en dataram från en csv-fil

Program kan skapa dataramar direkt från filer eller mappar via fjärrlagring, till exempel Azure Storage eller Azure Data Lake Storage, från en Hive-tabell eller från andra datakällor som stöds av Spark, till exempel Cosmos DB, Azure SQL DB, DW osv. Följande skärmbild visar en ögonblicksbild av den HVAC.csv-fil som används i självstudien. Csv-filen finns i alla HDInsight Spark-kluster. Datan visar temperaturvariationer i vissa byggnader.
    
![Ögonblicksbild av data för en interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Ögonblicksbild av data för en interaktiv Spark SQL-fråga")


1. Öppna Jupyter-anteckningsboken som du skapade i kravavsnittet och skapa en ny anteckningsbok med PySpark.

    > [!NOTE]  
    > Genom att använda PySpark-kärnan för att skapa en anteckningsbok skapas automatiskt sessionen `spark` för dig när du kör den första kodcellen. Du behöver inte uttryckligen skapa sessionen.

2. Klistra in följande kod i en tom cell i anteckningsboken och tryck sedan på **SKIFT+RETUR** för att köra koden. Koden importerar de typer som krävs för det här scenariot:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    När du kör en interaktiv fråga i Jupyter visar fönstret i webbläsaren eller fliktiteln statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras detta till en tom cirkel.

    ![Status för interaktiv Spark SQL-fråga](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status för interaktiv Spark SQL-fråga")

3. Kör följande kod för att skapa en dataram och en tillfällig tabell (**hvac**). 

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

     ![Tabellutdata från interaktivt Spark-frågeresultat](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabellutdata från interaktivt Spark-frågeresultat")

2. Du kan också visa resultaten i andra visualiseringar. Om du vill se ett ytdiagram för samma utdata väljer du **Yta** och anger sedan de andra värden som visas.

    ![Områdesdiagram över interaktivt Spark-frågeresultat](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Områdesdiagram över interaktivt Spark-frågeresultat")

3. Från menyraden notebook navigerar du till **filen** > **spara och kontrollpunkt**.

4. Om du ska starta [nästa självstudie](apache-spark-use-bi-tools.md) direkt kan du lämna anteckningsboken öppen. Om den inte stänga ned anteckningsboken för att frigöra klusterresurserna: från menyraden notebook navigerar du till **filen** >  **Stäng och stoppa**.

## <a name="clean-up-resources"></a>Rensa resurser

I HDInsight lagras dina data och Jupyter Notebooks i Azure Storage eller Azure Data Lake Store för att du på ett säkert sätt ska kunna ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används. Om du tänker arbeta med nästa självstudie direkt kan du behålla klustret.

Öppna klustret i Azure Portal och välj **Ta bort**.

![Ta bort HDInsight-kluster](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. När resursgruppen tas bort, tas även HDInsight Spark-klustret och standardkontot för lagring bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> * Skapa en Apache Spark-dataram.
> * Kör Spark SQL mot dataramen.

Gå vidare till nästa artikel för att se hur de data som du har registrerat i Apache Spark kan hämtas till ett BI-analysverktyg såsom Power BI. 
> [!div class="nextstepaction"]
> [Analysera data med BI-verktyg](apache-spark-use-bi-tools.md)
