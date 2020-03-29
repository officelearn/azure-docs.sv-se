---
title: Operationalisera Spark-byggda maskininlärningsmodeller - Team Data Science Process
description: Så här läser du in och poängar utbildningsmodeller som lagras i Azure Blob Storage (WASB) med Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3f02690d7c54581ed80b521e8222d1bd5964c878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718556"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operationalisera Spark-byggda maskininlärningsmodeller

Det här avsnittet visar hur du kan operationalisera en sparad maskininlärningsmodell (ML) med Python på HDInsight Spark-kluster. Den beskriver hur du läser in maskininlärningsmodeller som har byggts med Spark MLlib och lagras i Azure Blob Storage (WASB) och hur du poängar dem med datauppsättningar som också har lagrats i WASB. Den visar hur du förbehandlar indata, omvandlar funktioner med hjälp av indexerings- och kodningsfunktionerna i MLlib-verktygslådan och hur du skapar ett märkt punktdataobjekt som kan användas som indata för bedömning med ML-modellerna. Modellerna som används för bedömning är linjär regression, logistisk regression, slumpmässiga skogsmodeller och gradienthöjande trädmodeller.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark kluster och Jupyter bärbara datorer
Installationssteg och koden för att operationalize en ML-modell finns i den här genomgången för att använda ett HDInsight Spark 1.6-kluster samt ett Spark 2.0-kluster. Koden för dessa procedurer finns också i Jupyter-anteckningsböcker.

### <a name="notebook-for-spark-16"></a>Bärbar dator för Spark 1.6
Den [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter anteckningsbok visar hur man operationalize en sparad modell med Python på HDInsight kluster. 

### <a name="notebook-for-spark-20"></a>Bärbar dator för Spark 2.0
Om du vill ändra den Jupyter-anteckningsboken som Spark 1.6 ska använda med ett HDInsight Spark 2.0-kluster ersätter du Python-kodfilen med den [här filen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Den här koden visar hur du använder de modeller som skapats i Spark 2.0.


## <a name="prerequisites"></a>Krav

1. Du behöver ett Azure-konto och ett Spark 1.6 -(eller Spark 2.0) HDInsight-kluster för att slutföra den här genomgången. Se [översikten över datavetenskap med Spark på Azure HDInsight](spark-overview.md) för instruktioner om hur du uppfyller dessa krav. Det avsnittet innehåller också en beskrivning av NYC 2013 Taxi data som används här och instruktioner om hur man kör kod från en Jupyter anteckningsbok på Spark klustret. 
2. Skapa de maskininlärningsmodeller som ska poängsättas här genom att arbeta igenom [datautforskning och modellering med Spark-avsnittet](spark-data-exploration-modeling.md) för Spark 1.6-klustret eller Spark 2.0-anteckningsböckerna. 
3. Spark 2.0-anteckningsböckerna använder ytterligare en datauppsättning för klassificeringsuppgiften, den välkända avgångsdatauppsättningen för flygbolagsavgång från 2011 och 2012. En beskrivning av anteckningsböckerna och länkarna till dem finns i [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom är koden här och i de länkade anteckningsböckerna allmän och bör fungera på alla Spark-kluster. Om du inte använder HDInsight Spark kan stegen för klusterinställningar och hantering skilja sig något från vad som visas här. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Inställningar: lagringsplatser, bibliotek och den förinställda Spark-kontexten
Spark kan läsa och skriva till en Azure Storage Blob (WASB). Så alla dina befintliga data som lagras där kan bearbetas med Spark och resultaten lagras igen i WASB.

Om du vill spara modeller eller filer i WASB måste sökvägen anges korrekt. Standardbehållaren som är kopplad till Spark-klustret kan refereras med hjälp av en sökväg som börjar med: *"wasb///".* Följande kodexempel anger platsen för de data som ska läsas och sökvägen för den modelllagringskatalog som modellutdata sparas till. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ange katalogsökvägar för lagringsplatser i WASB
Modeller sparas i: "wasb:///user/remoteuser/NYCTaxi/Models". Om den här sökvägen inte är korrekt inställd läses inte modeller in för bedömning.

De poängsatta resultaten har sparats i: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Om sökvägen till mappen är felaktig sparas inte resultaten i den mappen.   

> [!NOTE]
> Filsökvägsplatserna kan kopieras och klistras in i platshållarna i den här koden från utdata från den sista cellen i anteckningsboken **machine-learning-data-science-spark-data-exploration.ipynb.**   
> 
> 

Här är koden för att ställa in katalogsökvägar: 

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

**Produktionen:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importera bibliotek
Ange gnistkontext och importera nödvändiga bibliotek med följande kod

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Preset Spark sammanhang och PySpark magi
PySpark-kärnorna som medföljer Jupyter-anteckningsböcker har en förinställd kontext. Därför behöver du inte ange Spark- eller Hive-kontexterna uttryckligen innan du börjar arbeta med det program du utvecklar. Dessa sammanhang är tillgängliga som standard:

* sc - för Spark 
* sqlContext - för Hive

PySpark-kärnan tillhandahåller några fördefinierade "magi", som är speciella kommandon som du kan anropa med %%. Det finns två sådana kommandon som används i dessa kodexempel.

* **%%lokal** Anger att koden i efterföljande rader körs lokalt. Koden måste vara giltig Python-kod.
* **%%sql -o \<variabelnamn>** 
* Kör en Hive-fråga mot sqlContext. Om parametern -o skickas sparas resultatet av frågan i kontexten %%lokal Python som en Pandas-dataram.

Mer information om kärnorna för Jupyter-anteckningsböcker och de fördefinierade "magi" som de tillhandahåller finns i [Kärnor som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster på HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Ta data och skapa en rensad dataram
Det här avsnittet innehåller koden för en serie uppgifter som krävs för att ange de data som ska poängsättas. Läs i ett sammanfogat 0,1% exempel på taxiresa och biljettfil (lagras som en TSV-fil), formatera data och skapa sedan en ren dataram.

Taxi-rese- och biljettfilerna anslöts baserat på proceduren i: [Team Data Science Process i aktion: med hjälp av HDInsight Hadoop-klusterämnet.](hive-walkthrough.md)

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

**Produktionen:**

Tid det tar att köra över cellen: 46,37 sekunder

## <a name="prepare-data-for-scoring-in-spark"></a>Förbereda data för bedömning i Spark
Det här avsnittet visar hur du indexerar, kodar och skalar kategoriska funktioner för att förbereda dem för användning i MLlib-övervakade inlärningsalgoritmer för klassificering och regression.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funktionsomvandling: index och koda kategoriska funktioner för indata till modeller för bedömning
Det här avsnittet visar hur du indexerar kategoriska data med hjälp av en `StringIndexer` och kodar funktioner med `OneHotEncoder` indata i modellerna.

[StringIndexer kodar](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) en strängkolumn med etiketter till en kolumn med etikettindex. Indexen sorteras efter etikettfrekvenser. 

[OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mappar en kolumn med etikettindex till en kolumn med binära vektorer, med högst ett enda värde. Med den här kodningen kan algoritmer som förväntar sig kontinuerliga värdefunktioner, till exempel logistisk regression, tillämpas på kategoriska funktioner.

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

**Produktionen:**

Tid det tar att köra över cellen: 5,37 sekunder

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Skapa RDD-objekt med funktionsmatriser för indata i modeller
Det här avsnittet innehåller kod som visar hur du indexerar kategoriska textdata som ett RDD-objekt och en-het koda den så att den kan användas för att träna och testa MLlib logistisk regression och trädbaserade modeller. Indexerade data lagras i [RDD-objekt (Resilient Distributed Dataset).](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) RDD:erna är den grundläggande abstraktionen i Spark. Ett RDD-objekt representerar en oföränderlig, partitionerad samling element som kan användas parallellt med Spark.

Den innehåller också kod som visar hur `StandardScalar` man skalar data med den som tillhandahålls av MLlib för användning i linjär regression med Stokastisk Gradient Descent (SGD), en populär algoritm för utbildning av ett brett spektrum av maskininlärningsmodeller. [Standardscaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) används för att skala funktionerna till enhetsavvikelse. Funktionsskalning, även känd som datanormalisering, försäkrar att funktioner med allmänt utbetalda värden inte ges överdriven vägning i målfunktionen. 

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
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

**Produktionen:**

Tid det tar att köra över cellen: 11,72 sekunder

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Poäng med logistikregressionsmodellen och spara utdata till blob
Koden i det här avsnittet visar hur du läser in en logistisk regressionsmodell som har sparats i Azure blob storage och använder den för att förutsäga om ett tips betalas på en taxiresa, poängsätta den med standardklassificeringsmått och sedan spara och rita resultaten för att blob-lagring . De poängsatta resultaten lagras i RDD-objekt. 

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

**Produktionen:**

Tid det tar att köra över cellen: 19,22 sekunder

## <a name="score-a-linear-regression-model"></a>Betyg en linjär regressionsmodell
Vi använde [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) för att träna en linjär regressionsmodell med stokastisk gradient descent (SGD) för optimering för att förutsäga mängden tips som betalas. 

Koden i det här avsnittet visar hur du läser in en linjär regressionsmodell från Azure blob storage, poäng med hjälp av skalade variabler och sedan spara resultaten tillbaka till blob.

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


**Produktionen:**

Tid det tar att köra över cellen: 16,63 sekunder

## <a name="score-classification-and-regression-random-forest-models"></a>Poängklassificering och regression Slumpmässiga skogsmodeller
Koden i det här avsnittet visar hur du läser in den sparade klassificeringen och regressionen Random Forest Models som sparats i Azure blob storage, poängar deras prestanda med standardklassificerare och regressionsmått och sparar sedan tillbaka resultaten till blob-lagring.

[Slumpmässiga skogar](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler av beslutsträd.  De kombinerar många beslutsträd för att minska risken för övermontering. Slumpmässiga skogar kan hantera kategoriska funktioner, utöka till klassificeringsinställningen för fleraklasser, inte kräver funktionskalning och kan fånga icke-linjäriteter och funktionsinteraktioner. Slumpmässiga skogar är en av de mest framgångsrika maskininlärningsmodellerna för klassificering och regression.

[spark.mllib](https://spark.apache.org/mllib/) stöder slumpmässiga skogar för binär och multiklassklassificering och för regression, med hjälp av både kontinuerliga och kategoriska funktioner. 

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

**Produktionen:**

Tid det tar att köra över cellen: 31,07 sekunder

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Poängklassificering och regressionsövergrading öka trädmodeller
Koden i det här avsnittet visar hur du läser in klassificering och regressionsövertoningshöjande trädmodeller från Azure blob storage, poängar deras prestanda med standardklassificerare och regressionsmått och sparar sedan resultaten tillbaka till blob-lagring. 

**spark.mllib** stöder GBTS för binär klassificering och regression, med hjälp av både kontinuerliga och kategoriska funktioner. 

[Gradient Öka träd](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) är ensembler av beslut träd. GBTS tåg beslut träd iterativt för att minimera en förlust funktion. GBTS kan hantera kategoriska funktioner, kräver inte funktionsskalning och kan fånga icke-linjäriteter och funktionsinteraktioner. Den här algoritmen kan också användas i en multiklassklassificeringsinställning.

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

**Produktionen:**

Tid det tar att köra över cellen: 14,6 sekunder

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Rensa objekt från minnet och skriva ut poängsatta filplatser
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


**Produktionen:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linjärRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Förbruka Spark-modeller via ett webbgränssnitt
Spark tillhandahåller en mekanism för att fjärrse skicka batchjobb eller interaktiva frågor via ett REST-gränssnitt med en komponent som heter Livy. Livy är aktiverat som standard i ditt HDInsight Spark-kluster. Mer information om Livy finns i: [Skicka Spark-jobb på distans med Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Du kan använda Livy för att fjärrsända ett jobb som batchpoängar en fil som lagras i en Azure-blob och sedan skriver resultaten till en annan blob. För att göra detta laddar du upp Python-skriptet från  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) till bloben för Spark-klustret. Du kan använda ett verktyg som **Microsoft Azure Storage Explorer** eller **AzCopy** för att kopiera skriptet till klusterbloben. I vårt fall laddade vi upp skriptet till ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> De åtkomstnycklar som du behöver finns på portalen för lagringskontot som är associerat med Spark-klustret. 
> 
> 

När det här skriptet har överförts till den här platsen körs det i Spark-klustret i en distribuerad kontext. Den laddar modellen och kör förutsägelser på indatafiler baserat på modellen.  

Du kan anropa skriptet på distans genom att göra en enkel HTTPS/REST-begäran på Livy.  Här är ett curl-kommando för att konstruera HTTP-begäran om att anropa Python-skriptet på distans. Ersätt CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME med lämpliga värden för Spark-klustret.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Du kan använda vilket språk som helst på fjärrsystemet för att anropa Spark-jobbet via Livy genom att ringa ett enkelt HTTPS-samtal med grundläggande autentisering.   

> [!NOTE]
> Det skulle vara praktiskt att använda Python Requests-biblioteket när du ringer det här HTTP-anropet, men det är för närvarande inte installerat som standard i Azure Functions. Så äldre HTTP-bibliotek används i stället.   
> 
> 

Här är Python-koden för HTTP-anropet:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
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


Du kan också lägga till den här Python-koden i [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) för att utlösa en Spark-jobböverföring som får en blob baserat på olika händelser som en timer, skapande eller uppdatering av en blob. 

Om du föredrar en kodfri klientupplevelse använder du [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) för att anropa Spark-batchbedömningen genom att definiera en HTTP-åtgärd på Logic Apps **Designer** och ange dess parametrar. 

* Skapa en ny Logikapp från Azure-portalen genom att välja **+Ny** -> **webb + Mobile** -> **Logic App**. 
* Om du vill visa **Logic Apps Designer**anger du namnet på Logic App och App Service Plan.
* Välj en HTTP-åtgärd och ange de parametrar som visas i följande bild:

![Logikappdesigner](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Nästa steg
**Korsvalidering och hyperparametersvepning:** Se [Avancerad datautforskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med korsvalidering och hyperparametersvepning.

