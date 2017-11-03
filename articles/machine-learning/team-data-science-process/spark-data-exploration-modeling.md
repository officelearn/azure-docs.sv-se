---
title: Datagranskning och modellering med Spark | Microsoft Docs
description: "Visar data från kartläggning av naturresurser och modellering funktioner i Spark MLlib toolkit på Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b989b918-5ba5-4696-b8d0-76ae510a23f4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: e9c7ae58825d640a33c7d76eb5016faeb3de2849
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="data-exploration-and-modeling-with-spark"></a>Datagranskning och modellering med Spark
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Den här genomgången använder HDInsight Spark för att utföra datagranskning och binär klassificering och regressionen modeling uppgifter på ett sampel från NYC Taxitransport resa och färdavgiften 2013 dataset.  Den vägleder dig genom stegen för den [datavetenskap Process](http://aka.ms/datascienceprocess), slutpunkt-till-slutpunkt, använder ett HDInsight Spark-kluster för bearbetning och Azure BLOB för att lagra data och modeller. Processen utforskar visualizes data som tillkommit från ett Azure Storage Blob och förbereder data för att skapa förutsägelsemodeller. Dessa modeller är versionen med Spark MLlib toolkit till binär klassificering och regressionen modeling aktiviteter.

* Den **binär klassificering** uppgift är att förutsäga huruvida ett tips är betald för resan. 
* Den **regression** uppgift är att förutsäga mängden tips baserat på andra tips funktioner. 

Modeller som vi använder inkluderar logistic och linjär regression, slumpmässiga skogar och toning ökat träd:

* [Linjär regression med SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) är en linjär regressionsmodell som använder en Stokastisk toning härstammar (SGD)-metod och skalning för att förutsäga tips belopp betald för optimering och funktion. 
* [Logistic regression med LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) eller ”logit” regression är en regressionsmodell som kan användas när den beroende variabeln är kategoriska att göra dataklassificering. LBFGS är en kvasi Karlsson optimering algoritm som uppskattar algoritmen Broyden – Fletcher – Goldfarb – Shanno (BFGS) med en begränsad mängd ledigt diskutrymme på datorn och som ofta används i machine learning.
* [Slumpmässiga skogar](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler av beslutsträd.  De kombinera många beslutsträd för att minska risken för overfitting. Slumpmässiga skogar används för regression och klassificering kan hantera kategoriska funktioner och kan utökas till inställningen för multiklass-baserad klassificering. De kräver inte funktionen skalning och kan avbilda icke-linjärt och funktion interaktioner. Slumpmässiga skogar är en av de mest framgångsrika modeller för klassificering och regression för maskininlärning.
* [Toningen ökat träd](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) är ensembler av beslutsträd. GBTs träna beslutsträd upprepade gånger för att minimera dataförlust funktionen. GBTs för regression och klassificering kan hantera kategoriska funktioner, kräver inte funktionen skalning och kan avbilda icke-linjärt och funktion interaktioner. De kan också användas i en multiclass klassificering.

Modellering stegen innehåller också kod visar hur du träna, utvärdera och spara varje typ av modellen. Python har använts kod lösningen och för att visa relevanta områden.   

> [!NOTE]
> Även om Spark MLlib toolkit är utformat för att arbeta med stora datauppsättningar, används ett relativt litet exempel (cirka 30 Mb med 170K rader, om 0,1% av den ursprungliga datauppsättningen NYC) här i informationssyfte. Övning som anges här körs effektivt (i 10 minuter) på ett HDInsight-kluster med 2 arbetsnoderna. Samma kod, med mindre ändringar kan användas för att bearbeta större-datamängder med lämpliga ändringar för cachelagring av data i minnet och ändrar storlek på klustret.
> 
> 

## <a name="prerequisites"></a>Krav
Du behöver ett Azure-konto och en Spark 1.6 (eller Spark 2.0) HDInsight-klustret för att slutföra den här genomgången. Finns det [översikt av datavetenskap med Spark på Azure HDInsight](spark-overview.md) för instruktioner om hur du uppfyller dessa krav. Avsnittet innehåller också en beskrivning av NYC 2013 Taxi data används här och instruktioner om hur du kör kod från en Jupyter notebook i Spark-klustret. 

## <a name="spark-clusters-and-notebooks"></a>Spark-kluster och bärbara datorer
Konfigurationsstegen och kod finns i den här genomgången för att använda ett HDInsight Spark 1.6. Men Jupyter-anteckningsböcker som har angetts för både HDInsight Spark 1.6 och 2.0 Spark-kluster. En beskrivning av bärbara datorer och länkar till dem finns i den [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-lagringsplats som innehåller dessa. Dessutom koden här länkade anteckningsböcker är generisk och bör fungera på Spark-kluster. Om du inte använder HDInsight Spark kanske klustret installation och hantering av stegen skiljer sig från vad som anges här. För enkelhetens skull är här länkar till Jupyter-anteckningsböcker för Spark 1.6 (för att köras i pySpark-kerneln Jupyter Notebook-Server) och Spark 2.0 (för att köras i kernelns pySpark3 Jupyter-anteckningsbok Server):

### <a name="spark-16-notebooks"></a>Spark 1.6 bärbara datorer

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): innehåller information om hur du utför datagranskning modellering och bedömningen med flera olika algoritmer.

### <a name="spark-20-notebooks"></a>Spark 2.0 bärbara datorer
Regression och klassificering uppgifter som implementeras med hjälp av ett 2.0 Spark-kluster finns i separata anteckningsböcker och klassificering anteckningsboken använder en annan datauppsättning:

- [Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): den här filen innehåller information om hur du utför datagranskning modellering, och bedömningen i Spark 2.0-kluster med NYC Taxi resa och avgiften datauppsättning beskrivs [här](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Den här anteckningsboken kan vara en bra utgångspunkt för att snabbt utforska koden som vi har angetts för Spark 2.0. För en mer detaljerad anteckningsbok analyserar NYC Taxi data, finns i nästa anteckningsboken i den här listan. Om du hittar information efter den här listan som jämför dessa datorer. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): den här filen visar hur du utför data wrangling (Spark SQL och dataframe operations), utforskning modellering och bedömningen med NYC Taxi resa och avgiften datauppsättning beskrivs [här](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): den här filen visar hur du utför data wrangling (Spark SQL och dataframe operations), utforskning modellering och bedömningen med välkända flygbolag i tid avvikelse datauppsättningen från 2011 och 2012. Vi integrerad flygbolag dataset med flygplats väder data (t.ex. vindhastigheten temperatur, höjd etc.) innan du modellera, så att funktionerna väder kan ingå i modellen.

<!-- -->

> [!NOTE]
> Flygbolag datamängden har lagts till Spark 2.0 bärbara datorer att illustrera bättre användning av algoritmer för klassificering. Se följande länkar för information om flygbolag i tid avvikelse dataset och väder datamängd:

>- Flygbolag i tid avvikelse data: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Flygplats väder data: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Spark 2.0 anteckningsböcker på NYC taxi och flygbolag svarta fördröjning-datauppsättningar kan ta 10 minuter eller mer att köra (beroende på storleken på HDI-kluster). Första anteckningsboken i listan ovan visas många aspekter av datagranskning, visualisering och ML-modell utbildning i en bärbar dator som det tar mindre tid att köra med ned provtagning NYC datamängd, där taxi och avgiften filer har redan anslutits: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) anteckningsboken tar mycket kortare tid att slutföra (2-3 minuter) och kan vara en bra utgångspunkt för snabbt utforska koden som vi har angetts för Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
Beskrivningarna nedan är relaterade till att använda Spark 1.6. Spark 2.0 versioner, Använd anteckningsböcker beskrivs och länkarna ovan. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Konfigurera: lagringsplatser, bibliotek och förinställda Spark-kontexten
Spark kan läsa och skriva till Azure Storage Blob (även kallat WASB). Så någon av dina befintliga data lagras kan det bearbetas med Spark och resultatet lagras igen i WASB.

Om du vill spara modeller eller filer i WASB måste sökvägen anges korrekt. Standardbehållaren kopplade till Spark-klustret kan refereras med en sökväg som börjar med ”: wasb: / / /”. Andra platser refererar till ”wasb: / /”.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ange katalogsökvägar för lagringsplatser i WASB
Följande kodexempel anger platsen för data som ska läsas och sökvägen för modellen Arkivkatalog som modellen sparas:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importera bibliotek
Konfigurera kräver också importera nödvändiga bibliotek. Ange spark kontext och importera nödvändiga bibliotek med följande kod:

    # IMPORT LIBRARIES
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


### <a name="preset-spark-context-and-pyspark-magics"></a>Förinställda Spark-kontexten och PySpark användbara
PySpark-kernel som tillhandahålls med Jupyter-anteckningsböcker har en förinställd kontext. Så du behöver inte ange Spark eller Hive-kontexterna explicit innan du börjar arbeta med programmet som du utvecklar. Dessa kontexter är tillgängliga för dig som standard. Dessa kontexter är:

* SC - Spark 
* sqlContext - för Hive

PySpark-kerneln innehåller vissa fördefinierade ”användbara”, som är särskilda kommandon som kan anropas med %%. Det finns två kommandon som används i följande kodexempel.

* **%% lokala** anger att koden i efterföljande rader är att köras lokalt. Koden måste vara giltig Python-kod.
* **%% sql -o <variable name>**  kör en Hive-fråga mot sqlContext. Om parametern -o skickas resultatet av frågan sparas i den %% lokala Python kontext som en Pandas DataFrame.

För mer information om kärnor för Jupyter-anteckningsböcker och den fördefinierade ”magics” som de tillhandahåller, se [kernlar som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Datapåfyllning från offentliga blob
Det första steget i processen för datavetenskap är att mata in data som ska analyseras från källor där är finns i din data från kartläggning av naturresurser och modellering miljö. Miljön är Spark i den här genomgången. Det här avsnittet innehåller koden för att genomföra ett antal aktiviteter:

* mata in datasampel till modelleras
* Läs i inkommande datauppsättningen (som lagras som en TSV-fil)
* Formatera och rensa data
* Skapa och cachelagra objekt (RDDs eller ramar) i minnet
* registrera den som en temp-tabell i SQL-kontext.

Här är koden för datapåfyllning.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
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

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**UTDATA:**

Åtgången tid för att köra ovanför cellen: 51.72 sekunder

## <a name="data-exploration--visualization"></a>Datagranskning & visualiseringen
När data har trätt i Spark, är nästa steg i processen för datavetenskap att får en djupare förståelse av data via undersökning och visualisering. I det här avsnittet vi undersöka taxi data med SQL-frågor och rita target-variabler och potentiella funktioner för granskning. Mer specifikt Rita vi frekvensen av passagerare antal i taxi resor frekvensen av tips belopp och hur tips varierar beroende på belopp och typen.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Rita ett histogram passagerare antal frekvenser i exemplet på taxi resor
Den här koden och efterföljande kodavsnitt kan du använda SQL Magiskt tal för att fråga exemplet och lokala Magiskt tal för att rita data.

* **SQL-magic (`%%sql`)** i HDInsight PySpark-kerneln stöder enkelt infogade HiveQL frågor mot sqlContext. Den (-o VARIABLE_NAME) argumentet kvarstår utdata från SQL-frågan som en Pandas DataFrame på Jupyter-servern. Det innebär att den är tillgänglig i lokalt läge.
* Den  **`%%local` magic** används för att köra kod lokalt på servern Jupyter som headnode av HDInsight-klustret. Normalt använder du `%%local` magiskt tillsammans med den `%%sql` magiskt med parametern -o. Parametern -o skulle spara resultatet av SQL-frågan lokalt och sedan %% lokala magic skulle leda till en uppsättning kodstycke kör lokalt mot utdata för SQL-frågor som sparas lokalt

Utdata visualiseras automatiskt när du kör kod.

Den här frågan hämtar resor av passagerare count. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Den här koden skapar en lokala data-ram för frågeresultatet och visar data. Den `%%local` magic skapar lokala data-ram, `sqlResults`, som kan användas för att rita upp med matplotlib. 

> [!NOTE]
> Den här PySpark-magic används flera gånger i den här genomgången. Om mängden data som är stor, bör du prov för att skapa en data-ram som ryms i lokalt minne.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Här är koden för att rita resor med passagerare räknare

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**UTDATA:**

![Resa frekvens av passagerare antal](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Du kan välja bland flera olika typer av grafik (register, cirkeldiagram, rad, område eller fältet) med hjälp av den **typen** menyn knapparna i den bärbara datorn. Fältet ritytans visas här.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Rita histogrammet tips belopp och hur mycket tips varierar efter passagerare antal och avgiften belopp.
Använd en SQL-fråga för att samla in data.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25


Den här koden cellen använder SQL-frågan för att skapa tre områden data.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**UTDATA:** 

![Fördelning av tips](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tips belopp av passagerare antal](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tips belopp med avgiften belopp](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funktionen teknik, omvandling och data förberedelse för modellering
Det här avsnittet beskriver och innehåller koden för procedurer som används för att förbereda data för användning i ML-modell. Den visar hur du gör följande:

* Skapa en ny funktion med diskretisering timmar till trafik tid buckets
* Index och koda kategoriska funktioner
* Skapa märkt point-objekt för indata till ML-funktioner
* Skapa ett slumpmässigt underordnade urval av data och dela upp den i träning och testning uppsättningar
* Funktionen skalning
* Cacheobjekt i minnet

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Skapa en ny funktion med diskretisering timmar till trafik tid buckets
Den här koden visar hur du skapar en ny funktion med diskretisering timmar till trafik tid buckets och cachelagra dataramen resulterande i minnet. Om flexibel distribuerade datauppsättningar (RDDs) och ramar data används flera gånger, leder cachelagring till förbättrad körningstider. Därför cachelagra vi RDDs och data ramar i flera steg i den här genomgången. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**UTDATA:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Index och koda kategoriska funktioner för indata i modeling funktioner
Det här avsnittet visar hur du index eller koda kategoriska funktioner för indata i modelleringsfunktioner. Modellering och förutsäga funktioner för MLlib kräver funktioner med kategoriska indata till indexerade eller kodade före användning. Beroende på modellen som du behöver index eller koda dem på olika sätt:  

* **Trädet-baserade modellering** kräver kategorier för att kodas som värden (funktion med tre kategorier kan till exempel vara kodad med 0, 1, 2). Detta tillhandahålls av Mllib's [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) funktion. Den här funktionen kodar en strängkolumn etiketter till en kolumn med etiketten index som sorteras efter etikett frekvenser. Även om indexeras med numeriska värden för indata- och datahantering kan trädet-baserade algoritmer anges för att behandla dem på lämpligt sätt som kategorier. 
* **Logistic och linjär Regression modeller** kräver en hot kodning, till exempel en funktion med tre kategorier kan expanderas till tre funktionen kolumner med varje som innehåller 0 eller 1 beroende på kategorin för en observationsintervallet. MLlib ger [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funktion för att göra en hot kodning. Den här kodaren mappar etikett index i en kolumn till en kolumn med binära angreppsmetoderna med högst ett ett-värde. Den här kodningen kan algoritmer som räknar numeriska värden funktioner, till exempel logistic regression som ska tillämpas på kategoriska funktioner.

Här är koden för att indexera och koda kategoriska funktioner:

    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
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

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Åtgången tid för att köra ovanför cellen: 1,28 sekunder

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Skapa märkt point-objekt för indata till ML-funktioner
Det här avsnittet innehåller kod som visar hur du indexera kategoriska textdata med datatypen märkt punkt och koda den så att den kan användas för att träna och testa MLlib logistic regression och andra klassificering modeller. Märkt point-objekt är flexibel distribuerade datauppsättningar (RDD) formaterad på ett sätt som krävs av de flesta ML algoritmer i MLlib som indata. En [etikett punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vector kompakta eller utspridd, som är associerade med en etikett/svar.  

Det här avsnittet innehåller kod som visar hur du indexera kategoriska textdata som en [etikett punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) datatypen och koda den så att den kan användas för att träna och testa MLlib logistic regression och andra klassificering modeller. Märkt point-objekt är flexibel distribuerade datauppsättningar (RDD) som består av en etikett (mål/svar-variabel) och funktionen vector. Det här formatet krävs av många ML algoritmer i MLlib som indata.

Här är koden för att indexera och koda textfunktioner för binär klassificering.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Här är koden för att koda och index kategoriska textfunktioner för linjär regressionsanalys.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Skapa ett slumpmässigt underordnade urval av data och dela upp den i träning och testning uppsättningar
Den här koden skapar en slumpmässig provtagning data (25% används här). Även om det inte krävs för det här exemplet på grund av storleken på dataset, visar vi hur du kan prova här så att du vet hur du använder för din egen problem vid behov. När prover är stort, kan detta spara mycket tid när utbildning modeller. Nästa dela vi exemplet i en utbildning (här 75%) och en tester del (här 25%) ska användas i klassificering och regressionen modeling.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Åtgången tid för att köra ovanför cellen: 0,24 sekunder

### <a name="feature-scaling"></a>Funktionen skalning
Funktionen skalning, även kallat databasnormalisering garanterar att funktioner med brett erläggas värden är inte den angivna överdriven väg i mål-funktionen. Koden för funktionen skalning använder den [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) att skala funktioner enhet variansen. Den tillhandahålls av MLlib för användning i linjär regression med Stokastisk toning härstammar (SGD), en populär algoritm för att träna en mängd andra maskininlärning modeller som reglerats regressioner eller support vector datorer (SVM).

> [!NOTE]
> Vi har hittat algoritmen LinearRegressionWithSGD vara känsliga funktion skalning.
> 
> 

Här är koden för att skala variabler för användning med algoritmen regularized SGD linjär.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Åtgången tid för att köra ovanför cellen: 13.17 sekunder

### <a name="cache-objects-in-memory"></a>Cacheobjekt i minnet
Den tid det tar för träning och testning av ML algoritmer kan minskas genom cachelagring av indata-ram objekt används för klassificering, regression, och skalas funktioner.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:** 

Åtgången tid för att köra ovanför cellen: 0,15 sekunder

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Förutsäga huruvida ett tips är betald med binär klassificering modeller
Det här avsnittet visas hur användning tre modeller för binär klassificering uppgiften att förutsäga ett tips betalat för en taxi resa eller inte. Modeller som visas är:

* Reglerats logistic regression 
* Slumpmässiga Skogsmodell
* Toning träd

Varje modell skapa avsnitt med exempelkod delas upp i steg: 

1. **Modellen utbildning** data med en enda parameter
2. **Modellen utvärdering** på en datauppsättning med test
3. **Spara modellen** i blob för framtida användning

### <a name="classification-using-logistic-regression"></a>Klassificering med logistic regression
Koden i det här avsnittet visar hur du träna, utvärdera och spara en logistic regressionsmodell med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som beräknar huruvida ett tips är betalat för en resa i NYC taxi resa och avgiften dataset.

**Träna regressionsmodellen logistic med KA och hyperparameter omfattande**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics


    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**UTDATA:** 

Koefficienter: [0.0082065285375,-0.0223675576104,-0.0183812028036, -3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Skärningspunkt:-0.0111216486893

Åtgången tid för att köra ovanför cellen: 14.43 sekunder

**Utvärdera modellen binär klassificering med standard mått**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**UTDATA:** 

Området under PR = 0.985297691373

Området under ROC = 0.983714670256

Sammanfattande statistik

Precision = 0.984304060189

Återkalla = 0.984304060189

F1 Poängsätta = 0.984304060189

Åtgången tid för att köra ovanför cellen: 57.61 sekunder

**Rita ROC-kurvan.**

Den *predictionAndLabelsDF* registreras som en tabell, *tmp_results*, i föregående cell. *tmp_results* kan användas för att göra frågor och resultatet i sqlResults data-ram för att rita upp. Här är koden.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Här är koden för att göra förutsägelser och rita ROC-kurvan.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**UTDATA:**

![Logistic regression ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Slumpmässiga skog klassificering
Koden i det här avsnittet visar hur du träna, utvärdera och spara en slumpmässig Skogsmodell som beräknar huruvida ett tips är betalat för en resa i NYC taxi resa och avgiften dataset.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Området under ROC = 0.985297691373

Åtgången tid för att köra ovanför cellen: 31.09 sekunder

### <a name="gradient-boosting-trees-classification"></a>Toning den träd klassificering
Koden i det här avsnittet visar hur du träna, utvärdera, och spara en toning den träd modell som beräknar huruvida ett tips är betalat för en resa i NYC taxi resan och färdavgiften dataset.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**UTDATA:**

Området under ROC = 0.985297691373

Åtgången tid för att köra ovanför cellen: 19.76 sekunder

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Förutsäga tips belopp för taxi resor med regression modeller
Det här avsnittet visas hur användning tre modeller för regression uppgiften att förutsäga mängden tips betalat för en taxi färd baserat på andra tips funktioner. Modeller som visas är:

* Reglerats linjär regression
* Slumpmässiga skog
* Toning träd

Dessa modeller beskrivs i inledningen. Varje modell skapa avsnitt med exempelkod delas upp i steg: 

1. **Modellen utbildning** data med en enda parameter
2. **Modellen utvärdering** på en datauppsättning med test
3. **Spara modellen** i blob för framtida användning

### <a name="linear-regression-with-sgd"></a>Linjär regression med SGD
Koden i det här avsnittet visar hur du använder skalade funktioner för att träna en linjär regression som använder stokastisk toning härstammar (SGD) för optimering, och hur du poängsätta, utvärdera och spara modellen i Azure Blob Storage (WASB).

> [!TIP]
> Det kan finnas problem med att Konvergera LinearRegressionWithSGD modeller i vår erfarenhet och parametrar måste vara ändras/optimerad noggrant för att erhålla en giltig modell. Skalning av variabler avsevärt hjälper med konvergens. 
> 
> 

    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

Koefficienter: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Fånga: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

Åtgången tid för att köra ovanför cellen: 58,42 sekunder

### <a name="random-forest-regression"></a>Slumpmässiga skog regression
Koden i det här avsnittet visar hur du träna, utvärdera och spara en slumpmässig skog regression som beräknar tips belopp för NYC taxi resa data.

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

RMSE = 0.891209218139

R-sqr = 0.759661334921

Åtgången tid för att köra ovanför cellen: 49.21 sekunder

### <a name="gradient-boosting-trees-regression"></a>Toning den träd regression
Koden i det här avsnittet visar hur du träna, utvärdera och spara en toning den träd modell som beräknar tips belopp för NYC taxi resa data.

** Träna och utvärdera **

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

RMSE = 0.908473148639

R-sqr = 0.753835096681

Åtgången tid för att köra ovanför cellen: 34.52 sekunder

**Rita**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultat från tabellen utdata till den *sqlResults* data ram för att rita upp. Här är koden

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Här är koden för att rita data med Jupyter-server.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)


**UTDATA:**

![Aktuella-vs-förutsade-tips-belopp](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Rensa objekt från minnet
Använd `unpersist()` att ta bort objekt som cachelagrats i minnet.

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Posten lagringsplatser av modeller för användnings- och poängsättning
Att använda och resultatet av en oberoende datauppsättning som beskrivs i den [poängsätta och utvärdera Spark-inbyggda machine learning-modeller](spark-model-consumption.md) avsnittet, du måste kopiera och klistra in dessa filnamn som innehåller sparade modeller som skapas här till förbrukning Jupyter-anteckningsbok. Här är koden för att skriva ut sökvägar till modellfiler som du behöver det.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**UTDATA**

logisticRegFileLoc = modelDir + ”LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568”

linearRegFileLoc = modelDir + ”LinearRegressionWithSGD_2016-05-0317_05_21.577773”

randomForestClassificationFileLoc = modelDir + ”RandomForestClassification_2016-05-0317_04_11.950206”

randomForestRegFileLoc = modelDir + ”RandomForestRegression_2016-05-0317_06_08.723736”

BoostedTreeClassificationFileLoc = modelDir + ”GradientBoostingTreeClassification_2016-05-0317_04_36.346583”

BoostedTreeRegressionFileLoc = modelDir + ”GradientBoostingTreeRegression_2016-05-0317_06_51.737282”

## <a name="whats-next"></a>Nästa steg
Nu när du har skapat regression och klassificering modeller med Spark MlLib, är du redo att lära dig hur du poängsätta och utvärdera dessa modeller. Avancerade datagranskning och modellering anteckningsboken dyker ned djupare i inklusive korsvalidering, hyper-parametern omfattande, och modellerar utvärdering. 

**Modellen förbrukning:** information om hur du poängsätta och utvärdera klassificering och regression modeller som skapats i det här avsnittet finns [poängsätta och utvärdera Spark-inbyggda machine learning-modeller](spark-model-consumption.md).

**Korsvalidering och hyperparameter omfattande**: se [avancerade datagranskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) på hur modeller kan vara tränas med omfattande korsvalidering och hyper-parameter

