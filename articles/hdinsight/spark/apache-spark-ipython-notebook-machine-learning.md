---
title: 'Självstudier: Skapa ett Spark-maskininlärningsprogram i Azure HDInsight'
description: Stegvisa anvisningar om att skapa Apache Spark machine learning-program i HDInsight Spark-kluster med Jupyter Notebook.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 54ef9ac72ffeebbf228a85768f6c2eb4b64862c9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433407"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-hdinsight"></a>Självstudier: Skapa ett Apache Spark-maskininlärningsprogram i HDInsight 

I den här självstudien lär du dig att använda [Jupyter Notebook](https://jupyter.org/) för att skapa ett [Apache Spark](https://spark.apache.org/)-maskininlärningsprogram för Azure HDInsight. 

[MLib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) är Sparks skalbara Machine Learning-bibliotek som består av vanliga algoritmer och verktyg, inklusive klassificering, regression, klustring, samarbetsfilter, dimensionsminskning samt underliggande optimeringsprimitiver.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Utveckla ett Apache Spark-maskininlärningsprogram

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav:

Du måste ha följande objekt:

* Slutför [Skapa ett Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="understand-the-data-set"></a>Förstå datauppsättningen

Programmet använder HVAC.csv-exempeldata som är tillgängligt på alla kluster som standard. Filen finns på **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Data visar måltemperaturen och den faktiska temperaturen för några byggnader som har installerade HVAC-system. Kolumnen **System** representerar system-ID:t, och kolumnen **SystemAge** representerar antalet år som HVAC-systemet har funnits i byggnaden. Med dessa data kan du förutsäga om en byggnad kommer att bli varmare eller kallare baserat på måltemperaturen baserat på ett system-ID och systemets ålder.

![Ögonblicksbild av data som används för Spark machine learning-exempel](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Ögonblicksbild av data som används för Spark machine learning-exempel")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Utveckla ett Spark-maskininlärningsprogram med Spark MLlib

I det här programmet använder du en Spark [ML-pipeline](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) för att utföra en dokumentklassificering. ML-pipelines tillhandahåller en enhetlig uppsättning högnivå-API:er som har skapats på DataFrames som hjälper användare att skapa och justera praktiska machine learning-pipelines. I pipelinen delar du upp dokumentet i ord, konverterar orden till en numerisk funktionsvektor och slutligen skapa en förutsägelsemodell med funktionsvektorer och etiketter. Skapa programmet genom att utföra stegen nedan.

1. Skapa en Jupyter-anteckningsbok med PySpark-kerneln. Instruktioner finns i [Skapa en Jupyter-anteckningsbok](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Importera de typer som krävs för det här scenariot. Klistra in följande kodfragment i en tom cell och tryck sedan på **SKIFT+RETUR**. 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```
3. Läs in data (hvac.csv), parsa dem och använd dem för att träna modellen. 

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0        

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    I kodfragmentet definierar du en funktion som jämför den faktiska temperaturen med måltemperaturen. Om den faktiska temperaturen är högre är byggnaden varm, vilket markeras med värdet **1.0**. Annars är byggnaden kall, vilket visas av värdet **0.0**. 

4. Konfigurera Spark machine learning-pipelinen som består av tre steg: tokenizer, hashingTF och lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Mer information om pipeline och hur det fungerar finns i avsnittet om <a href="https://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Apache Spark-maskininlärnings-pipeline</a>.

5. Anpassa pipelinen till utbildningsdokumentet.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Verifiera utbildningsdokumentet för att kontrollera din utveckling med programmet.
   
    ```PySpark
    training.show()
    ```
   
    Utdatan liknar följande:

    ```
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Jämföra utdata med CSV-råfilen. Den första raden i CSV-filen har dessa data till exempel:

    ![Ögonblicksbild av utdata för Spark machine learning-exempel](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Ögonblicksbild av utdata för Spark machine learning-exempel")

    Lägg märke till att den faktiska temperaturen är lägre än måltemperaturen, vilket indikerar att byggnaden är kall. I utbildningsutdata är därför värdet för **etikett** i den första raden **0.0**, vilket betyder att byggnaden inte är varm.

7. Förbered en datauppsättning att köra träningsmodellen mot. För att göra det skickar du ett system-ID och en systemålder (betecknas som **SystemInfo** i utbildningsutdata). Modellen förutsäger om byggnaden med det system-ID:t och den systemåldern kommer att bli varmare (betecknas av 1.0) eller kallare (betecknas med 0.0).
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. Gör slutligen förutsägelser på testdata. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Utdatan liknar följande:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   Från den första raden i förutsägelsen kan du se att för ett HVAC-system med ID 20 och en systemålder på 25 år är byggnaden varm (**förutsägelse=1.0**). Det första värdet för DenseVector (0,49999) motsvarar förutsägelsen 0.0 och det andra värdet (0,5001) motsvarar förutsägelsen 1.0. I utdata, trots att det andra värdet bara är marginellt högre, visar modellen **förutsägelse=1.0**.
10. Stäng anteckningsboken för att frigöra resurser. Du gör det genom att välja **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Åtgärden stänger anteckningsboken.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Använda Anaconda scikit-bibliotek för Spark machine learning
Apache Spark-kluster i HDInsight innehåller Anaconda-bibliotek. Det innehåller också **scikit-learn**-bibliotek för machine learning. Biblioteket innehåller också olika datauppsättningar som du kan använda för att skapa exempelprogram direkt från en Jupyter Notebook. För exempel på hur du använder scikit-learn-biblioteket, se [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Utveckla ett Apache Spark-maskininlärningsprogram

Gå vidare till nästa självstudie om du vill lära dig hur du använder IntelliJ IDEA för Spark-jobb. 

> [!div class="nextstepaction"]
> [Skapa ett Scala Maven-program med IntelliJ](./apache-spark-create-standalone-application.md)

