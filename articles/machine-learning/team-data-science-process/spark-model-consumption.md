---
title: Operationalisera Spark-byggda machine learning-modeller | Microsoft Docs
description: Så här att läsa in och bedöma learning-modeller som lagras i Azure Blob Storage (WASB) med Python.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 6ffe1dd960b6fd09539d093d8a632efc99452c00
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442513"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operationalisera Spark-byggda machine learning-modeller

Det här avsnittet visar hur du operationalisera en sparad maskininlärningsmodell (ML) med hjälp av Python i HDInsight Spark-kluster. Den beskriver hur du läser in machine learning-modeller som skapats med Spark MLlib och lagras i Azure Blob Storage (WASB) och hur du kan bedöma dem med datauppsättningar som också har lagrats i WASB. Den visar hur du Förbearbeta indata, omvandla funktioner med hjälp av funktionerna indexering och kodning i MLlib toolkit och hur du skapar ett taggade point data-objekt som kan användas som indata för bedömning med ML-modeller. Modeller som används för bedömning är linjär Regression, Logistic Regression, slumpmässigt skog modeller och Gradient Boosting trädet modeller.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark-kluster och Jupyter notebooks
Steg för konfiguration och koden för att operationalisera en ML-modellen tillhandahålls i den här genomgången för att använda ett HDInsight Spark 1.6-kluster, samt ett Spark 2.0-kluster. Koden för de här procedurerna ges också i Jupyter-anteckningsböcker.

### <a name="notebook-for-spark-16"></a>Anteckningsboken för Spark 1.6
Den [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter-anteckningsbok visar hur du operationalisera en sparad modell med hjälp av Python i HDInsight-kluster. 

### <a name="notebook-for-spark-20"></a>Anteckningsboken för Spark 2.0
Om du vill ändra Jupyter-anteckningsboken för Spark 1.6 ska användas med ett kluster i HDInsight Spark 2.0, Ersätt kodfilen Python med [filen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Den här koden visar hur du använder modeller som skapats i Spark 2.0.


## <a name="prerequisites"></a>Förutsättningar

1. Du behöver ett Azure-konto och en Spark 1.6 (eller Spark 2.0) HDInsight-kluster för att slutföra den här genomgången. Se den [översikt över datavetenskap med Spark på Azure HDInsight](spark-overview.md) för instruktioner om hur du uppfyller dessa krav. Avsnittet innehåller också en beskrivning av NYC 2013 Taxi-data som används här och instruktioner om hur du kör kod från en Jupyter notebook i Spark-klustret. 
2. Du måste också skapa maskininlärningsmodeller bedömas här genom att utföra den [datagranskning och modellering med Spark](spark-data-exploration-modeling.md) avsnittet för Spark 1.6 klustret eller Spark 2.0-anteckningsböcker. 
3. Spark 2.0 anteckningsböcker använder en annan datamängd för klassificering åtgärd, välkända flygbolag i tid avgång datauppsättningen från 2011 och 2012. En beskrivning av anteckningsböcker och länkar till dem finns i den [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-lagringsplatsen som innehåller dessa. Dessutom koden här och i länkade anteckningsböcker är generisk och bör fungera i ett Spark-kluster. Om du inte använder HDInsight Spark, konfiguration och hantering av steg kanske skiljer sig från vad som anges här. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Installationen: lagringsplatser, bibliotek och förinställda Spark-kontext
Spark kan läsa och skriva till ett Azure Storage Blob (WASB). Så att någon av dina befintliga data lagras kan det bearbetas med hjälp av Spark och resultatet lagras igen i WASB.

Om du vill spara modeller eller filer i WASB, måste sökvägen anges korrekt. Standardbehållaren kopplade till Spark-kluster kan refereras med en sökväg som börjar med: *”wasb / / /”*. Följande kodexempel anger platsen för data som ska läsas och sökvägen för modellen Arkivkatalog som utdata modellen sparas. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ange katalogsökvägar för lagringsplatser i WASB
Modeller har sparats i ”: wasb: / / / användare/remoteuser/NYCTaxi/modeller”. Om den här sökvägen inte är korrekt, laddas inte modeller för bedömning.

Poängsatta resultaten har sparats i ”: wasb: / / / användare/remoteuser/NYCTaxi/ScoredResults”. Om sökvägen till mappen är felaktigt, sparas inte resultaten i den mappen.   

> [!NOTE]
> Sökvägar för sökvägen kan kopieras och klistras in i platshållare i den här koden från utdata från den sista cellen i den **machine-learning-data-science-spark-data-exploration-modeling.ipynb** anteckningsboken.   
> 
> 

Här är koden för att ange directory sökvägar: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**UTDATA:**

datetime.datetime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importera bibliotek
Ange spark-kontext och importera nödvändiga bibliotek med följande kod

    #IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Förinställda Spark-kontexten och PySpark användbara funktioner
PySpark-kärnor som tillhandahålls med Jupyter-anteckningsböcker har en förinställd kontext. Så du behöver inte ange Spark eller Hive-kontexterna explicit innan du börjar arbeta med programmet du utvecklar. Dessa är tillgängliga för dig som standard. Dessa kontexter är:

* SC - för Spark 
* sqlContext - för Hive

PySpark-kerneln innehåller vissa fördefinierade ”användbara”, vilket är särskilt kommandon som du kan anropa med %%. Det finns två kommandon som används i följande kodexempel.

* **%% lokala** anges att koden i efterföljande rader körs lokalt. Koden måste vara giltig Python-kod.
* **%% sql -o <variable name>** 
* Kör en Hive-fråga mot sqlContext. Om parametern -o skickas resultatet av frågan sparas i den %% lokal Python-kontext som en Pandas-dataframe.

För mer information om kärnor för Jupyter notebooks och den fördefinierade ”magics” som ger, se [Kernlar som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Mata in data och skapa en rensad dataram
Det här avsnittet innehåller kod för en serie aktiviteter som krävs för att mata in data bedömas. Läsa en domänansluten 0,1% urval av taxi resa och avgiften filen (som lagras som en TSV-fil), format data och skapar sedan en ren dataram.

Taxi resa och avgiften filerna kopplas baserat på det sätt som anges i den: [Team Data Science Process i praktiken: med hjälp av HDInsight Hadoop-kluster](hive-walkthrough.md) avsnittet.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Åtgången tid att köra ovanför cellen: 46.37 sekunder

## <a name="prepare-data-for-scoring-in-spark"></a>Förbereda data för bedömning i Spark
Det här avsnittet visar hur du index, koda och skala kategoriska funktioner för att förbereda dem för användning i MLlib övervakat learning-algoritmer för klassificering och regression.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funktionen omvandling: indexera och koda kategoriska funktioner för mata in modeller för bedömning
Det här avsnittet visas hur du indexerar kategoriska data med hjälp av en `StringIndexer` och koda funktioner med `OneHotEncoder` indata i modeller.

Den [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) kodar en strängkolumn för etiketter till en kolumn etikett index skapas. Indexen ordnas efter frekvenser som etikett. 

Den [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mappar en kolumn med etiketten index till en kolumn med binära vektorer, med högst ett ett-värde. Den här kodningen kan algoritmer som förväntar sig kontinuerlig värdefull funktioner, till exempel logistic regression som ska tillämpas på kategoriska funktioner.

    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()

    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Åtgången tid att köra ovanför cellen: 5.37 sekunder

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Skapa RDD-objekt med funktionen matriser för mata in modeller
Det här avsnittet innehåller kod som visar hur du indexera kategoriska textdata som ett RDD-objekt och en frekvent koda den så att den kan användas för att träna och testa MLlib logistic regression och trädet-baserade modellen. Indexerade data lagras i [Resilient Distributed Dataset (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objekt. Det här är den grundläggande abstraktionen i Spark. En RDD-objektet representerar en oföränderlig, partitionerad samling element som kan användas på parallellt med Spark.

Den innehåller också kod som visar hur du skalar data med den `StandardScalar` tillhandahålls av MLlib för användning i linjär regression med Stokastisk brantaste Lutningsmetoden (Descent), en populär algoritm för att träna ett stort antal machine learning-modeller. Den [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) används för att skala funktioner till enhet avvikelse. Funktionen skalning, även kallat databasnormalisering försäkrar att funktioner med brett erläggas värden har inte gett överdriven väga i funktionen servicenivåmål. 

    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Åtgången tid att köra ovanför cellen: 11.72 sekunder

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Poängsätta med Logistic Regression-modellen och spara utdata till blob
Koden i det här avsnittet visar hur du läser in en Logistic Regression-modell som har sparats i Azure blob storage och använda den för att förutsäga om ett tips är betalas på en taxi-resa, bedöma med standardklassificeringen mått och spara och rita resultaten till blob stora ge. Poängsatta resultaten lagras i RDD-objekt. 

    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**UTDATA:**

Åtgången tid att köra ovanför cellen: 19.22 sekunder

## <a name="score-a-linear-regression-model"></a>Bedömningsmodell linjär Regression
Vi använde [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) att träna en linjär regressionsmodell med hjälp av Stokastisk brantaste Lutningsmetoden (Descent) för optimering för att förutsäga mängden tips som betalas. 

Koden i det här avsnittet visar hur du läser in en linjär regressionsmodell från Azure blob storage, poäng använda skalade variabler och spara resultaten tillbaka till blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**UTDATA:**

Åtgången tid att köra ovanför cellen: 16.63 sekunder

## <a name="score-classification-and-regression-random-forest-models"></a>Bedöma klassificerings- och regressionsmodeller slumpmässiga skog modeller
Koden i det här avsnittet visar hur du läser in sparade klassificeringen och regression slumpmässiga skog modeller sparas i Azure blob storage, poängsätta sina utföranden med standard klassificerare och regression mått och spara resultaten tillbaka till blob storage.

[Slumpmässig skogar](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler för beslutsträd.  De kombinera många beslutsträd för att minska risken för overfitting. Slumpmässig skogar kan hantera kategoriska funktioner, utöka till inställningen multiklass-baserad klassificering, kräver funktionen skalning och kan samla in icke-linjära och funktionen interaktioner. Slumpmässig skogar är en av de mest framgångsrika machine learning-modeller för klassificering och regression.

[Spark.mllib](http://spark.apache.org/mllib/) stöder slumpmässiga skogar för binära och multiklass-baserad klassificering och regression, med hjälp av både kontinuerlig och kategoriska funktioner. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES    
    from pyspark.mllib.tree import RandomForest, RandomForestModel


    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**UTDATA:**

Åtgången tid att köra ovanför cellen: 31.07 sekunder

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Bedöma klassificerings- och regressionsmodeller Gradient Boosting trädet modeller
Koden i det här avsnittet visar hur du läser in klassificering och regression Gradient Boosting trädet modeller från Azure blob storage, poängsätta sina utföranden med standard klassificerare och regression mått och spara resultaten tillbaka till blob storage. 

**Spark.mllib** stöder GBTs för binär klassificering och regression, med hjälp av både kontinuerlig och kategoriska funktioner. 

[Gradient Boosting träd](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) är ensembler för beslutsträd. GBTs träna beslutsträd upprepade gånger för att minimera en förlust-funktion. GBTs kan hantera kategoriska funktioner kräver funktionen skalning och kan samla in icke-linjära och funktionen interaktioner. De kan också användas i en inställning för multiclass-klassificering.

    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Åtgången tid att köra ovanför cellen: 14.6 sekunder

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Rensa objekt från minnet och skriva ut poängsatta sökvägar
    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**UTDATA:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Använd Spark-modeller via ett webbgränssnitt
Spark tillhandahåller en mekanism för att skicka via en fjärranslutning batchjobb eller interaktiva frågor via ett REST-gränssnitt med en komponent som kallas Livy. Livy är aktiverat som standard på ditt HDInsight Spark-kluster. Läs mer på Livy: [skicka Spark-jobb via fjärranslutning med Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Du kan använda Livy för att skicka ett jobb som batch resultat via en fjärranslutning en fil som lagras i en Azure-blob och skriver sedan resultaten till en annan blob. Om du vill göra detta måste du ladda upp Python-skriptet från  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) till bloben i Spark-kluster. Du kan använda ett verktyg som **Microsoft Azure Lagringsutforskaren** eller **AzCopy** att kopiera skriptet till kluster-blob. I vårt fall vi laddade upp skriptet till ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Åtkomstnycklarna som du behöver finns på portalen för storage-konto som är associerade med Spark-klustret. 
> 
> 

När du har överfört till den här platsen körs skriptet i Spark-kluster i en distribuerad kontext. Den läser in modellen och kör förutsägelser på indatafilerna baserat på modellen.  

Du kan anropa det här skriptet via fjärranslutning genom att göra en enkel HTTPS/REST-begäran på Livy.  Här är ett curl-kommando för att skapa HTTP-begäran för att anropa Python-skriptet via en fjärranslutning. Ersätt CLUSTERLOGIN, CLUSTERPASSWORD, KLUSTERNAMN med lämpliga värden för ditt Spark-kluster.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Du kan använda valfritt språk på fjärrsystemet för att anropa Spark-jobb via Livy genom att göra ett enkelt HTTPS-anrop med grundläggande autentisering.   

> [!NOTE]
> Det skulle vara praktiskt att använda Python-begäranden-biblioteket när du gör den här HTTP-anrop, men det är inte installerad som standard i Azure Functions. Så att äldre HTTP-biblioteken används i stället.   
> 
> 

Här är Python-kod för HTTP-anrop:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64

    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'

    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Du kan också lägga till den här Python-koden till [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) att utlösa en Spark-jobbet som poängsätter en blob baserat på olika händelser som en timer, skapande eller uppdatering av en blob. 

Om du föredrar en kostnadsfri klientmiljö kod kan använda den [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) att anropa Spark-batchbedömnings genom att definiera en HTTP-åtgärd på den **Logic Apps Designer** och ange dess parametrar. 

* Från Azure portal kan du skapa en ny Logikapp genom att välja **+ ny** -> **webb + mobilt** -> **Logikapp**. 
* Att ta fram den **Logic Apps Designer**, ange namnet på Logikappen och App Service-Plan.
* Välj en HTTP-åtgärd och ange de parametrar som visas i följande bild:

![Logikappdesigner](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Nästa steg
**Korsvalidering och finjustering oinskränkt**: se [avancerad datagranskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med hjälp av oinskränkt korsvalidering och hyper-parametern.

