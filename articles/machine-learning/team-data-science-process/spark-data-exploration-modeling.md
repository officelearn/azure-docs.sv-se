---
title: Datagranskning och modellering med Spark | Microsoft Docs
description: Visar data funktionerna för utforskning och modellering i Spark MLlib toolkit på Azure.
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
ms.openlocfilehash: 803b190514ce0638e8eac5e937805d4a69e76995
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842942"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Datagranskning och modellering med Spark

Den här genomgången använder HDInsight Spark för att utföra datagranskning och binär klassificering och regression modellering uppgifter på ett exempel på NYC Taxitransport resa och färdavgiften 2013 datauppsättning.  Vi går igenom stegen i den [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), slutpunkt till slutpunkt, använda ett HDInsight Spark-kluster för bearbetning och Azure BLOB för att lagra data och modeller. Processen utforskar och visualiserar data som tillkommit från en Azure Storage Blob och förbereder data för att skapa förutsägelsemodeller. De här modellerna är build med hjälp av Spark MLlib verktygen till binär klassificering och regression modellering aktiviteter.

* Den **binär klassificering** uppgift är att förutsäga om ett tips betalas för resan. 
* Den **regression** uppgift är att förutsäga mängden tips baserat på andra funktioner som tips. 

Modeller som vi använder är logistic och linjär regression, slumpmässigt skogar och gradient bättre träd:

* [Linjär regression med Descent](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) är en linjär regressionsmodell som använder en Stokastisk brantaste Lutningsmetoden (Descent)-metod och skalning för att förutsäga tips belopp betalas för optimering och funktion. 
* [Logistic regression med LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) eller ”logit” regression är en regressionsmodell som kan användas när den beroende variabeln är kategoriska göra dataklassificering. LBFGS är en kvasi Karlsson optimering algoritm som tillhandahåller algoritmen Broyden – Fletcher – Goldfarb – Shanno (BFGS) med hjälp av en begränsad del av minnet och som ofta används i machine learning.
* [Slumpmässig skogar](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler för beslutsträd.  De kombinera många beslutsträd för att minska risken för overfitting. Slumpmässig skogar för regression och klassificering och kan hantera kategoriska funktioner och kan utökas till inställningen multiklass-baserad klassificering. De kräver funktionen skalning och kan samla in icke-linjära och funktionen interaktioner. Slumpmässig skogar är en av de mest framgångsrika machine learning-modeller för klassificering och regression.
* [Toning förstärkta träd](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) är ensembler för beslutsträd. GBTs träna beslutsträd upprepade gånger för att minimera en förlust-funktion. GBTs för regression och klassificering och kan hantera kategoriska funktioner, kräver funktionen skalning och kan samla in icke-linjära och funktionen interaktioner. De kan också användas i en inställning för multiclass-klassificering.

Modellering stegen också innehålla kod som visar hur du tränar, utvärdera och spara varje typ av modellen. Python har använts för att skriva kod till lösningen och för att visa relevanta områden.   

> [!NOTE]
> Även om Spark MLlib toolkit är utformat för att arbeta med stora datauppsättningar, används ett relativt litet antal (cirka 30 Mb med 170K rader, ca 0,1% av den ursprungliga datauppsättningen NYC) här för att underlätta. Den här övningen körs effektivt (i cirka 10 minuter) på ett HDInsight-kluster med 2 arbetarnoder. Samma kod, med mindre ändringar kan användas för att bearbeta större-datauppsättningar, med lämpliga ändringar för att cachelagra data i minnet och ändrar storlek på klustret.
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Du behöver ett Azure-konto och en Spark 1.6 (eller Spark 2.0) HDInsight-kluster för att slutföra den här genomgången. Se den [översikt över datavetenskap med Spark på Azure HDInsight](spark-overview.md) för instruktioner om hur du uppfyller dessa krav. Avsnittet innehåller också en beskrivning av NYC 2013 Taxi-data som används här och instruktioner om hur du kör kod från en Jupyter notebook i Spark-klustret. 

## <a name="spark-clusters-and-notebooks"></a>Spark-kluster och bärbara datorer
Steg för konfiguration och kod finns i den här genomgången för att använda ett HDInsight Spark 1.6. Men tillhandahålls Jupyter notebooks för HDInsight Spark 1.6- och Spark 2.0. En beskrivning av anteckningsböcker och länkar till dem finns i den [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-lagringsplatsen som innehåller dessa. Dessutom koden här och i länkade anteckningsböcker är generisk och bör fungera i ett Spark-kluster. Om du inte använder HDInsight Spark, konfiguration och hantering av steg kanske skiljer sig från vad som anges här. För enkelhetens skull följer du länkarna till Jupyter-anteckningsböcker för Spark 1.6 (för att köras i pySpark-kerneln för Jupyter Notebook-server) och Spark 2.0 (för att köras i pySpark3 kernel Jupyter Notebook-server):

### <a name="spark-16-notebooks"></a>Spark 1.6 anteckningsböcker

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): innehåller information om hur du utför datagranskning, modellering och bedömning med flera olika algoritmer.

### <a name="spark-20-notebooks"></a>Spark 2.0-anteckningsböcker
Regression och klassificering uppgifter som implementeras med hjälp av ett Spark 2.0-kluster finns i separata anteckningsböcker och klassificering anteckningsboken använder en annan datauppsättning:

- [Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): den här filen innehåller information om hur du utför datagranskning, modellering, och bedömning i Spark 2.0-kluster med NYC Taxi-resa och avgiften-datauppsättning beskrivs [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Den här anteckningsboken kan vara en bra utgångspunkt för att snabbt utforska koden som vi har lagt till för Spark 2.0. För en mer detaljerad anteckningsbok analyserar NYC Taxi-data, visas i nästa anteckningsboken i den här listan. Se information efter den här listan som jämför dessa anteckningsböcker. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): den här filen visar hur du utför data experimenteringsfunktioner (Spark SQL och dataframe åtgärder), undersökning, modellering och bedömning med hjälp av NYC Taxi resa och avgiften datauppsättning beskrivs [här ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): den här filen visar hur du utför data experimenteringsfunktioner (Spark SQL och dataframe åtgärder), undersökning, modellering och bedömning med hjälp av välkända flygbolag i tid avgår datauppsättningen från 2011 och 2012. Vi integrerat flygbolag datauppsättning med en flygplats weather-data (t.ex. vindhastigheten, temperaturen, höjd osv) före modellering, så dessa väder-funktioner kan tas med i modellen.

<!-- -->

> [!NOTE]
> Flygbolag datauppsättningen har lagts till Spark 2.0-anteckningsböcker för att ge en bättre illustrering användningen av algoritmer för klassificering. Se följande länkar för information om flygbolag i tid avgång datauppsättningen och väder datauppsättning:

>- Flygbolag i tid avgång data: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Flygplats weather-data: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Spark 2.0-anteckningsböcker på NYC taxi och flygbolag flygning fördröjning-datauppsättningar kan ta 10 minuter eller mer att köra (beroende på storleken på HDI-kluster). Första anteckningsboken i listan ovan visar många aspekter av datagranskning, visualisering och ML modellera utbildning på en bärbar dator som tar mindre tid att köra med ned samplas NYC datamängd, där filerna taxi och avgiften har redan domänansluten: [ Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) anteckningsboken tar mycket kortare tid att slutföra (2-3 minuter) och kan vara en bra startpunkt för att snabbt utforska koden har vi lagt till för Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
Beskrivningarna nedan är relaterade till med hjälp av Spark 1.6. Använd anteckningsböcker som beskrivs och länkarna ovan för Spark 2.0-versioner. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Installationen: lagringsplatser, bibliotek och förinställda Spark-kontext
Spark kan läsa och skriva till Azure Storage Blob (även kallat WASB). Så att någon av dina befintliga data lagras kan det bearbetas med hjälp av Spark och resultatet lagras igen i WASB.

Om du vill spara modeller eller filer i WASB, måste sökvägen anges korrekt. Standardbehållaren kopplade till Spark-kluster kan refereras med en sökväg som börjar med ”: wasb: / / /”. Refererar till andra platser ”wasb: / /”.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ange katalogsökvägar för lagringsplatser i WASB
Följande kodexempel anger platsen för data som ska läsas och sökvägen för modellen Arkivkatalog modell t sparas som:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importera bibliotek
Konfigurera kräver också importera nödvändiga bibliotek. Ange spark-kontexten och importera nödvändiga bibliotek med följande kod:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Förinställda Spark-kontexten och PySpark användbara funktioner
PySpark-kärnor som tillhandahålls med Jupyter-anteckningsböcker har en förinställd kontext. Så du behöver inte ange Spark eller Hive-kontexterna explicit innan du börjar arbeta med programmet du utvecklar. Dessa kontexter är tillgängliga för dig som standard. Dessa kontexter är:

* SC - för Spark 
* sqlContext - för Hive

PySpark-kerneln innehåller vissa fördefinierade ”användbara”, vilket är särskilt kommandon som du kan anropa med %%. Det finns två kommandon som används i följande kodexempel.

* **%% lokala** anger att koden i efterföljande rader är att köras lokalt. Koden måste vara giltig Python-kod.
* **%% sql -o <variable name>**  kör en Hive-fråga mot sqlContext. Om parametern -o skickas resultatet av frågan sparas i den %% lokal Python-kontext som en Pandas-DataFrame.

För mer information om kärnor för Jupyter notebooks och den fördefinierade ”magics” som ger, se [Kernlar som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Datainmatning från offentlig blob
Det första steget i data science process är att mata in data analyseras från källor där är finns i miljön data utforskning och modellering. Miljön är Spark i den här genomgången. Det här avsnittet innehåller koden för att genomföra ett antal uppgifter:

* mata in data-exemplet för att modelleras
* Läs i datauppsättningen för indata (som lagras som en TSV-fil)
* Formatera och rensa data
* Skapa och cachelagrar objekt (rdd-datauppsättningar eller ramar) i minnet
* registrera den som en temp-tabell i SQL-kontext.

Här är koden för datainmatning.

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

Åtgången tid att köra ovanför cellen: 51.72 sekunder

## <a name="data-exploration--visualization"></a>Datagranskning och visualisering
När data har trätt i Spark, är nästa steg i data science process att få bättre förståelse av data via utforskning och visualisering. I det här avsnittet ska vi undersöka taxi-data med hjälp av SQL-frågor och rita target-variabler och potentiella funktioner för granskning. Mer specifikt Rita vi frekvensen för passagerar antal i taxi-kommunikation, frekvensen för tips belopp och hur tips varierar belopp och typen.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Rita ett histogram för passagerar antal frekvenser i taxi och RETUR-exemplet
Den här koden och efterföljande kodfragment kan du använda SQL magic för att fråga exemplet och lokala magic data ska ritas.

* **SQL magic (`%%sql`)** The HDInsight PySpark-kernel har stöd för enkel infogade HiveQL frågor mot sqlContext. Den (-o VARIABLE_NAME) argumentet kvarstår utdata från SQL-frågan som en Pandas-DataFrame på Jupyter-servern. Det innebär att den är tillgänglig i lokalt läge.
* Den  **`%%local` magic** används för att köra kod lokalt på servern och Jupyter, som är huvudnoden på HDInsight-klustret. Normalt använder du `%%local` magic tillsammans med den `%%sql` magic med parametern -o. Parametern -o skulle spara utdata av SQL-frågan lokalt och sedan %% lokala magic ska utlösa nästa uppsättning kodfragmentet att köra lokalt mot utdata från SQL-frågor som sparas lokalt

Utdata visualiseras automatiskt när du kör koden.

Den här frågan hämtar kommunikation efter passagerartrafik antal. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Den här koden skapar en lokal dataram från frågeresultatet visas och visar data. Den `%%local` magic skapar en lokal dataram, `sqlResults`, som kan användas för med matplotlib. 

> [!NOTE]
> Den här PySpark-magic används flera gånger i den här genomgången. Om mängden data som är stor, bör du ta prov för att skapa en dataram som ryms i lokalt minne.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Här är koden för att rita kommunikation med passagerar-antal

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

![Resans frekvens efter passagerar-antal](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Du kan välja bland flera olika typer av visualiseringar (register, cirkel, rad, området eller fält) med hjälp av den **typ** menyn knappar i anteckningsboken. Fältet området visas här.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Rita ett histogram för tips belopp och hur mycket tips varierar beroende på passagerartrafik antal och avgiften belopp.
Använd en SQL-fråga till exempeldata.

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


Den här kodcell använder SQL-fråga för att skapa tre områden data.

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

![Tips belopp efter passagerar-antal](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tips belopp med avgiften belopp](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funktionen engineering, omvandling och data förberedelse för modellering
Det här avsnittet beskriver och innehåller koden för procedurer som används för att förbereda data för användning i ML-modeller. Den visar hur du utföra följande uppgifter:

* Skapa en ny funktion med datagrupperingen timmar till trafik tid buckets
* Indexera och koda kategoriska funktioner
* Skapa taggade point-objekt för mata in ML-funktioner
* Skapa en slumpmässig underordnade sampling av data och dela upp den i utbildning och testningsuppsättningar
* Funktionen skalning
* Cacheobjekt i minnet

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Skapa en ny funktion med datagrupperingen timmar till trafik tid buckets
Den här koden visar hur du skapar en ny funktion med datagrupperingen timmar till trafik tid buckets och sedan cachelagra den resulterande dataramen i minnet. Om Resilient Distributed Rdd-datauppsättningar () och dataramar används flera gånger, leder cachelagring till bättre körningstider. Därför cachelagra vi rdd-datauppsättningar och ramar i flera steg under den här genomgången. 

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

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexera och koda kategoriska funktioner för mata in modellering funktioner
Det här avsnittet visar hur du indexera eller koda kategoriska funktioner för mata in modelleringsfunktioner. Modellering och förutsäga funktioner för MLlib kräver funktioner med kategoriska indata till indexerade eller kodade före användning. Beroende på modellen måste du indexera eller koda dem på olika sätt:  

* **Trädet-baserade modellering** kräver kategorier som ska kodas som numeriska värden (till exempel en funktion med tre kategorier kan kodas med 0, 1, 2). Detta tillhandahålls av Mllib's [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) funktion. Den här funktionen kodar en strängkolumn för etiketter till en kolumn med etiketten index som sorteras efter frekvenser som etikett. Även om indexeras med numeriska värden för indata och datahantering kan i trädet-baserade algoritmer anges för att hantera dem på lämpligt sätt som kategorier. 
* **Logistic och linjära Regressionsmodeller** kräver en frekvent kodning, var, till exempel en funktion med tre kategorier kan expanderas till tre kolumner i funktionen, med varje som innehåller 0 eller 1 beroende på kategorin för en uppmaning i. MLlib ger [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) som utför en frekvent kodning. Den här encoder mappar en kolumn med etiketten index till en kolumn med binära vektorer, med högst ett ett-värde. Den här kodningen kan algoritmer som förväntar sig numeriska värden funktioner, till exempel logistic regression som ska tillämpas på kategoriska funktioner.

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

Åtgången tid att köra ovanför cellen: 1,28 sekunder

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Skapa taggade point-objekt för mata in ML-funktioner
Det här avsnittet innehåller kod som visar hur du indexera kategoriska textdata som datatypen taggade punkt och koda den så att den kan användas för att träna och testa MLlib logistic regression och andra klassificering modeller. Taggade point-objekt är flexibel distribuerade datauppsättningar (RDD) formaterad på ett sätt som krävs som indata av de flesta ML-algoritmer i MLlib. En [märkta punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vector kompakta eller null-optimerade, som är associerad med en etikett/svar.  

Det här avsnittet innehåller koden som visar hur du indexerar kategoriska textdata som en [märkta punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) datatypen och koda den så att den kan användas för att träna och testa MLlib logistic regression och andra klassificering modeller. Taggade point-objekt är flexibel distribuerade datauppsättningar (RDD) som består av en etikett (mål/svar-variabel) och funktionen vektor. Det här formatet krävs som indata av många ML-algoritmer i MLlib.

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


Här är koden för att koda och indexera kategoriska textfunktioner för linjär regressionsanalys.

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


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Skapa en slumpmässig underordnade sampling av data och dela upp den i utbildning och testningsuppsättningar
Den här koden skapar ett slumpmässigt urval av data (25% används här). Även om det inte krävs för det här exemplet på grund av storleken på datauppsättningen, visar vi hur du kan ta prov här så att du vet hur du använder det för dina egna problem när det behövs. När exempel är stor, kan detta spara betydande tid modeller för utbildning. Sedan dela vi exemplet i en utbildning (här 75%) och en testning delen (här 25%) ska användas i klassificering och regression modellering.

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

Åtgången tid att köra ovanför cellen: 0,24 sekunder

### <a name="feature-scaling"></a>Funktionen skalning
Funktionen skalning, även kallat databasnormalisering försäkrar att funktioner med brett erläggas värden har inte gett överdriven väga i funktionen servicenivåmål. Koden för funktionen skalning använder den [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) skala funktioner till enhet avvikelse. Den tillhandahålls av MLlib för användning i linjär regression med Stokastisk brantaste Lutningsmetoden (Descent), en populär algoritm för att träna en mängd andra maskininlärningsmodeller som reglerats upprepningar eller support vector datorer (SVM).

> [!NOTE]
> Vi har hittat LinearRegressionWithSGD-algoritmen för att vara känslig för funktionen skalning.
> 
> 

Här är koden för att skala variabler för användning med regularized linjär Descent algoritmen.

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

Åtgången tid att köra ovanför cellen: 13.17 sekunder

### <a name="cache-objects-in-memory"></a>Cacheobjekt i minnet
Den tid det tar för träning och testning av ML-algoritmer kan minskas genom cachelagring av indata-ramen objekt används för klassificering, regression, och som kan skalas funktioner.

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

Åtgången tid att köra ovanför cellen: 0,15 sekunder

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Förutsäga om ett tips är betald med binär klassificering modeller
Det här avsnittet visas hur användning tre modeller för binär klassificering uppgiften att förutsäga om huruvida ett tips betalas för en taxi-resa. Modeller som visas är:

* Reglerats logistic regression 
* Slumpmässig Skogsmodell
* Gradient Boosting träd

Varje modell att skapa avsnitt med exempelkod delas upp i steg: 

1. **Modellera utbildning** data med en parameteruppsättning
2. **Modellera utvärdering** på en datauppsättning för testning med mått
3. **Sparar modell** i blob för framtida användning

### <a name="classification-using-logistic-regression"></a>Klassificering med hjälp av logistic regression
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en logistic regression-modellen med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om huruvida ett tips betalas för en resa i NYC taxi resa och avgiften datauppsättningen.

**Träna logistic regression-modellen med hjälp av KA och finjustering oinskränkt**

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

Koefficienter: [0.0082065285375,-0.0223675576104,-0.0183812028036, -3.48124578069e - 05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921, - 0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Skärningspunkt för:-0.0111216486893

Åtgången tid att köra ovanför cellen: 14.43 sekunder

**Utvärdera modellen binär klassificering med standardmått**

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

F1 Poäng = 0.984304060189

Åtgången tid att köra ovanför cellen: 57.61 sekunder

**Rita ROC-kurvan.**

Den *predictionAndLabelsDF* registreras som en tabell, *tmp_results*, i föregående cell. *tmp_results* kan användas för att gör frågor och utdataresultat i sqlResults dataram för. Här är koden.

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

### <a name="random-forest-classification"></a>Slumpmässig skog klassificering
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en slumpmässig Skogsmodell som beräknar huruvida ett tips betalas för en resa i NYC taxi resa och avgiften datauppsättningen.

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

Åtgången tid att köra ovanför cellen: 31.09 sekunder

### <a name="gradient-boosting-trees-classification"></a>Gradient boosting träd klassificering
Koden i det här avsnittet visar hur du tränar, utvärdera, och spara en gradient boosting träd-modell som förutsäger huruvida ett tips betalas för en resa i NYC taxi resa och färdavgiften datauppsättning.

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

Åtgången tid att köra ovanför cellen: 19.76 sekunder

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Förutsäga tips belopp för taxi-kommunikation med regressionsmodeller
Det här avsnittet visas hur användning tre modeller för regression uppgiften att förutse hur mycket tipset betalat för en taxi-resa baserat på andra funktioner som tips. Modeller som visas är:

* Reglerats linjär regression
* Slumpmässig skog
* Gradient Boosting träd

Dessa modeller beskrivs i inledningen. Varje modell att skapa avsnitt med exempelkod delas upp i steg: 

1. **Modellera utbildning** data med en parameteruppsättning
2. **Modellera utvärdering** på en datauppsättning för testning med mått
3. **Sparar modell** i blob för framtida användning

### <a name="linear-regression-with-sgd"></a>Linjär regression med Descent
Koden i det här avsnittet visar hur du använder skalade funktioner för att träna en linjär regression som använder stokastisk brantaste lutningsmetoden (Descent) för optimering och hur du poäng, utvärdera och spara modellen i Azure Blob Storage (WASB).

> [!TIP]
> Enligt vår erfarenhet medför det kan finnas problem med möte för LinearRegressionWithSGD modeller och parametrar måste vara ändrats/optimerade noggrant för att hämta en giltig modell. Skalning av variabler avsevärt hjälper med konvergens. 
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

Koefficienter: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,- 0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Fånga upp: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

Åtgången tid att köra ovanför cellen: 58,42 sekunder

### <a name="random-forest-regression"></a>Slumpmässig skog regression
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en slumpmässig skog regression som beräknar tips beloppet för NYC taxi resedata.

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

Åtgången tid att köra ovanför cellen: 49.21 sekunder

### <a name="gradient-boosting-trees-regression"></a>Gradient boosting träd regression
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en gradient boosting träd-modell som förutsäger tips beloppet för NYC taxi resedata.

**Träna och utvärdera**

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

    # CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UTDATA:**

RMSE = 0.908473148639

R-sqr = 0.753835096681

Åtgången tid att köra ovanför cellen: 34.52 sekunder

**Rita**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultat från tabellen matats ut till den *sqlResults* dataram för. Här är koden

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

![Aktuella-vs-förutse-tips-belopp](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Post lagringsplatser av modeller för förbrukning och bedömning
Att använda och bedöma en oberoende datauppsättning som beskrivs i den [poäng och utvärdera Spark-byggda machine learning-modeller](spark-model-consumption.md) avsnittet, som du behöver kopiera och klistra in dessa filnamn som innehåller de sparade modeller som skapats här i förbrukningen Jupyter-anteckningsboken. Här är koden för att skriva ut sökvägar till modellfiler som du behöver det inte.

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
Nu när du har skapat regression och klassificering modeller med Spark MlLib kan är du redo att lära dig hur du bedöma och utvärdera dessa modeller. Avancerad datagranskning och modellering notebook aktivitetsgruppsrapport djupare i inklusive korsvalidering, hyper-parametern oinskränkt, och modellera utvärdering. 

**Modellera förbrukning:** information om hur du bedöma och utvärdera klassificerings- och regressionsmodeller modeller som skapats i det här avsnittet finns [poäng och utvärdera Spark-byggda machine learning-modeller](spark-model-consumption.md).

**Korsvalidering och finjustering oinskränkt**: se [avancerad datagranskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med hjälp av oinskränkt korsvalidering och hyper-parameter

