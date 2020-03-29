---
title: Datautforskning och modellering med Spark - Team Data Science Process
description: Visar datautforsknings- och modelleringsfunktionerna i Spark MLlib-verktygslådan på Azure.
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
ms.openlocfilehash: 208f176ca942fb382ff2ed81d872602f7229b0a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718641"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Datagranskning och modellering med Spark

Den här genomgången använder HDInsight Spark för att göra datautforskning och binär klassificering och regressionsmodelleringsuppgifter på ett exempel på NYC-taxiresan och biljettprisdatauppsättningen 2013.  Den går igenom stegen i [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, med hjälp av ett HDInsight Spark-kluster för bearbetning och Azure-blobbar för att lagra data och modeller. Processen utforskar och visualiserar data som tas in från en Azure Storage Blob och förbereder sedan data för att skapa prediktiva modeller. Dessa modeller bygger med spark MLlib-verktygslådan för att utföra binära klassificerings- och regressionsmodelleringsuppgifter.

* Den **binära klassificeringsuppgiften** är att förutsäga om ett tips betalas för resan eller inte. 
* **Regressionsuppgiften** är att förutsäga mängden av spetsen baserat på andra tipsfunktioner. 

De modeller vi använder inkluderar logistisk och linjär regression, slumpmässiga skogar och gradient boostade träd:

* [Linjär regression med SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) är en linjär regressionsmodell som använder en SGD-metod (Stokastisk gradient descent) och för optimering och funktionsskalning för att förutsäga de betalda dricksbeloppen. 
* [Logistisk regression med LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) eller "logit" regression, är en regressionsmodell som kan användas när den beroende variabeln är kategorisk för att göra dataklassificering. LBFGS är en nästan Newton optimeringsalgoritm som approximerar Broyden-Fletcher-Goldfarb-Shanno (BFGS) algoritm med hjälp av en begränsad mängd datorminne och som ofta används i maskininlärning.
* [Slumpmässiga skogar](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler av beslutsträd.  De kombinerar många beslutsträd för att minska risken för övermontering. Slumpmässiga skogar används för regression och klassificering och kan hantera kategoriska funktioner och kan utökas till klassificeringsinställningen för fleraklasser. De kräver inte funktionsskalning och kan fånga icke-linjäriteter och funktionsinteraktioner. Slumpmässiga skogar är en av de mest framgångsrika maskininlärningsmodellerna för klassificering och regression.
* [Gradient boosted trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) är ensembler av beslutsträd. GBTS tåg beslut träd iterativt för att minimera en förlust funktion. GBTS används för regression och klassificering och kan hantera kategoriska funktioner, kräver inte funktionsskalning och kan fånga icke-linjäriteter och funktionsinteraktioner. De kan också användas i en multiklassklassificeringsinställning.

Modelleringsstegen innehåller också kod som visar hur du tränar, utvärderar och sparar varje typ av modell. Python har använts för att koda lösningen och för att visa relevanta ritplaner.   

> [!NOTE]
> Även om Spark MLlib-verktygslådan är utformad för att fungera med stora datamängder, används ett relativt litet urval (~30 Mb med 170 K rader, cirka 0,1 % av den ursprungliga NYC-datauppsättningen) här för enkelhetens skull. Övningen som ges här körs effektivt (på cirka 10 minuter) på ett HDInsight-kluster med 2 arbetsnoder. Samma kod, med mindre ändringar, kan användas för att bearbeta större datauppsättningar, med lämpliga ändringar för cachelagring av data i minnet och ändra klusterstorleken.
> 
> 

## <a name="prerequisites"></a>Krav
Du behöver ett Azure-konto och ett Spark 1.6 -(eller Spark 2.0) HDInsight-kluster för att slutföra den här genomgången. Se [översikten över datavetenskap med Spark på Azure HDInsight](spark-overview.md) för instruktioner om hur du uppfyller dessa krav. Det avsnittet innehåller också en beskrivning av NYC 2013 Taxi data som används här och instruktioner om hur man kör kod från en Jupyter anteckningsbok på Spark klustret. 

## <a name="spark-clusters-and-notebooks"></a>Spark kluster och bärbara datorer
Installationssteg och kod finns i den här genomgången för att använda en HDInsight Spark 1.6. Men Jupyter-anteckningsböcker finns för både HDInsight Spark 1.6- och Spark 2.0-kluster. En beskrivning av anteckningsböckerna och länkarna till dem finns i [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom är koden här och i de länkade anteckningsböckerna allmän och bör fungera på alla Spark-kluster. Om du inte använder HDInsight Spark kan stegen för klusterinställningar och hantering skilja sig något från vad som visas här. För enkelhetens skull, här är länkarna till Jupyter bärbara datorer för Spark 1,6 (som ska köras i pySpark kärnan i Jupyter Notebook server) och Spark 2.0 (som ska köras i pySpark3 kärnan i Jupyter Notebook server):

### <a name="spark-16-notebooks"></a>Spark 1.6 bärbara datorer

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Ger information om hur du utför datautforskning, modellering och bedömning med flera olika algoritmer.

### <a name="spark-20-notebooks"></a>Spark 2.0 bärbara datorer
De regressions- och klassificeringsuppgifter som implementeras med hjälp av ett Spark 2.0-kluster finns i separata anteckningsböcker och klassificeringsdatorn använder en annan datauppsättning:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Den här filen innehåller information om hur du utför datautforskning, modellering och bedömning i Spark 2.0-kluster med hjälp av NYC Taxi-resan och biljettprisdatauppsättningen som beskrivs [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Den här anteckningsboken kan vara en bra utgångspunkt för att snabbt utforska koden vi har angett för Spark 2.0. En mer detaljerad anteckningsbok analyserar NYC Taxi-data i nästa anteckningsbok i den här listan. Se anteckningarna som följer den här listan som jämför dessa anteckningsböcker. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Den här filen visar hur du utför datakäbbel (Spark SQL och dataframe operationer), prospektering, modellering och poängsättning med hjälp av NYC Taxi resa och biljettpris data-set som beskrivs [här](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Den här filen visar hur du utför datakäbbel (Spark SQL- och dataframe-åtgärder), utforskning, modellering och bedömning med hjälp av den välkända airline on-time-avvikelsedatauppsättningen från 2011 och 2012. Vi integrerade flygbolagets datauppsättning med flygplatsens väderdata (till exempel vindhastighet, temperatur, höjd etc.) före modellering, så att dessa väderfunktioner kan ingå i modellen.

<!-- -->

> [!NOTE]
> Flygbolagsdatauppsättningen lades till i Spark 2.0-anteckningsböckerna för att bättre illustrera användningen av klassificeringsalgoritmer. Se följande länkar för information om flygbolagens avgångsdatauppsättning i tid och väderdatauppsättning:
> 
> - Uppgifter om avgång i tid:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Väderdata på flygplatsen:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> Spark 2.0-bärbara datorer på NYC-taxi- och flygförseningsdatauppsättningarna kan ta 10 minuter eller mer att köra (beroende på storleken på ditt HDI-kluster). Den första anteckningsboken i listan ovan visar många aspekter av datautforskning, visualisering och ML modell utbildning i en bärbar dator som tar mindre tid att köra med ned-samplade NYC datauppsättning, där taxi- och biljettfilerna har föranslutits: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Den här anteckningsboken tar mycket kortare tid att slutföra (2-3 minuter) och kan vara en bra utgångspunkt för att snabbt utforska koden vi har tillhandahållit för Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> Beskrivningarna nedan är relaterade till användning av Spark 1.6. För Spark 2.0-versioner kan du använda de anteckningsböcker som beskrivs och länkas ovan. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Inställningar: lagringsplatser, bibliotek och den förinställda Spark-kontexten
Spark kan läsa och skriva till Azure Storage Blob (kallas även WASB). Så alla dina befintliga data som lagras där kan bearbetas med Spark och resultaten lagras igen i WASB.

Om du vill spara modeller eller filer i WASB måste sökvägen anges korrekt. Standardbehållaren som är kopplad till Spark-klustret kan refereras med hjälp av en sökväg som börjar med: "wasb:///". Andra platser refereras av "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ange katalogsökvägar för lagringsplatser i WASB
Följande kodexempel anger platsen för de data som ska läsas och sökvägen för den modelllagringskatalog som modellutdata sparas till:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importera bibliotek
Ställ in kräver också import nödvändiga bibliotek. Ange gnistkontext och importera nödvändiga bibliotek med följande kod:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Preset Spark sammanhang och PySpark magi
PySpark-kärnorna som medföljer Jupyter-anteckningsböcker har en förinställd kontext. Så du behöver inte ange Spark- eller Hive-kontexterna uttryckligen innan du börjar arbeta med det program du utvecklar. Dessa sammanhang är tillgängliga som standard. Dessa sammanhang är:

* sc - för Spark 
* sqlContext - för Hive

PySpark-kärnan tillhandahåller några fördefinierade "magi", som är speciella kommandon som du kan anropa med %%. Det finns två sådana kommandon som används i dessa kodexempel.

* **%%lokal** Anger att koden i efterföljande rader ska köras lokalt. Koden måste vara giltig Python-kod.
* **%%sql -o \<variabelnamn>** Kör en Hive-fråga mot sqlContext. Om parametern -o skickas sparas resultatet av frågan i kontexten %%lokal Python som en Pandas DataFrame.

Mer information om Jupyters bärbara kärnor och de fördefinierade "magi", finns [i Kärnor tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster på HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Datainmatning från offentlig blob
Det första steget i datascience-processen är att inta de data som ska analyseras från källor där finns i din datautforskning och modellering miljö. Miljön är Spark i den här genomgången. Det här avsnittet innehåller koden för att slutföra en serie uppgifter:

* intag av det dataprov som ska modelleras
* läsa i indatauppsättningen (lagras som en TSV-fil)
* formatera och rensa data
* skapa och cachelagra objekt (RDD:er eller dataramar) i minnet
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

**Produktionen:**

Tid det tar att köra över cellen: 51,72 sekunder

## <a name="data-exploration--visualization"></a>Datautforskning & visualisering
När data har förts in i Spark, är nästa steg i datascience processen att få djupare förståelse för data genom utforskning och visualisering. I det här avsnittet undersöker vi taxidata med hjälp av SQL-frågor och ritar målvariablerna och prospektiva funktioner för visuell inspektion. Specifikt plottar vi frekvensen av passagerarantal i taxiresor, frekvensen av tipsbelopp och hur tips varierar beroende på betalningsbelopp och typ.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Rita ett histogram av passagerarantalfrekvenser i urvalet av taxiresor
Den här koden och efterföljande utdrag använder SQL-magi för att fråga exemplet och lokal magi för att rita data.

* **SQL magi`%%sql`( )** HDInsight PySpark-kärnan stöder enkla inline HiveQL-frågor mot sqlContext. Argumentet (-o VARIABLE_NAME) kvarstår utdata för SQL-frågan som en Pandas DataFrame på Jupyter-servern. Den här inställningen gör utdata tillgänglig i det lokala läget.
* ** `%%local` Magin** används för att köra kod lokalt på Jupyter-servern, som är headnod för HDInsight-klustret. Vanligtvis använder `%%local` du magi tillsammans `%%sql` med magin med -o-parametern. Parametern -o skulle bevara utdata för SQL-frågan lokalt och sedan %%lokal magi skulle utlösa nästa uppsättning kodavsnitt för att köras lokalt mot utdata från SQL-frågor som sparas lokalt

Utdata visualiseras automatiskt när du har kört koden.

Den här frågan hämtar resorna efter antalet passagerare. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Den här koden skapar en lokal dataram från frågeutdata och ritar data. Magin `%%local` skapar en lokal dataram, `sqlResults`, som kan användas för plottning med matplotlib. 

> [!NOTE]
> Denna PySpark magi används flera gånger i denna genomgång. Om mängden data är stor bör du ta ett exempel för att skapa en dataram som får plats i det lokala minnet.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Här är koden för att rita resor av passagerare räknas

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

**Produktionen:**

![Resefrekvens per antal passagerare](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Du kan välja mellan flera olika typer av visualiseringar (Tabell, Cirkel, Linje, Område eller Stapel) med hjälp av **menyknapparna Typ** i anteckningsboken. Bar-tomten visas här.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Rita ett histogram av tips belopp och hur tips belopp varierar beroende på passagerare räkna och belopp biljettpris.
Använd en SQL-fråga för att exempela data.

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


Den här kodcellen använder SQL-frågan för att skapa tre ritdiagram efter data.

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


**Produktionen:** 

![Distribution av tipsbelopp](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tipsbelopp per antal passagerare](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipsbelopp efter prisbelopp](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funktionsteknik, omvandling och dataförberedelse för modellering
Det här avsnittet beskriver och innehåller koden för procedurer som används för att förbereda data för användning i ML-modellering. Den visar hur du utför följande uppgifter:

* Skapa en ny funktion genom att gå in timmar i trafiktidshinkar
* Indexera och koda kategoriska funktioner
* Skapa märkta punktobjekt för inmatning i ML-funktioner
* Skapa en slumpmässig delbempling av data och dela upp dem i utbildnings- och testuppsättningar
* Funktionsskalning
* Cacheobjekt i minnet

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Skapa en ny funktion genom att gå in timmar i trafiktidshinkar
Den här koden visar hur du skapar en ny funktion genom att binning timmar i trafik tid hinkar och sedan hur man cachelagrar den resulterande dataramen i minnet. Om RDD-datauppsättningar (Resilient Distributed Datasets) och dataramar används upprepade gånger leder cachelagring till förbättrade körningstider. Därför cachelagrar vi RDD:er och dataramar i flera steg i genomgången. 

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

**Produktionen:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexera och koda kategoriska funktioner för inmatning i modelleringsfunktioner
Det här avsnittet visar hur du indexerar eller kodar kategoriska funktioner för indata i modelleringsfunktionerna. Modellerings- och förutsäga funktioner i MLlib kräver att funktioner med kategoriska indata indexeras eller kodas före användning. Beroende på modell måste du indexera eller koda dem på olika sätt:  

* **Trädbaserad modellering** kräver att kategorier kodas som numeriska värden (till exempel kan en funktion med tre kategorier kodas med 0, 1, 2). Den här algoritmen tillhandahålls av MLlibs [StringIndexer-funktion.](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) Den här funktionen kodar en strängkolumn med etiketter till en kolumn med etikettindex som sorteras efter etikettfrekvenser. Även om indexeras med numeriska värden för indata och datahantering, kan de trädbaserade algoritmerna anges för att behandla dem på lämpligt sätt som kategorier. 
* **Logistiska och linjära regressionsmodeller** kräver en-het kodning, där till exempel en funktion med tre kategorier kan utökas till tre funktionskolumner, där var och en innehåller 0 eller 1 beroende på observationskategorin. MLlib tillhandahåller [OneHotEncoder-funktionen](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) för att göra en frekvent kodning. Den här kodaren mappar en kolumn med etikettindex till en kolumn med binära vektorer, med högst ett enda värde. Med den här kodningen kan algoritmer som förväntar sig numeriska värdefunktioner, till exempel logistisk regression, tillämpas på kategoriska funktioner.

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

**Produktionen:**

Tid det tar att köra över cellen: 1,28 sekunder

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Skapa märkta punktobjekt för inmatning i ML-funktioner
Det här avsnittet innehåller kod som visar hur du indexerar kategoriska textdata som en märkt punktdatatyp och kodar den så att den kan användas för att träna och testa MLlib-logistisk regression och andra klassificeringsmodeller. Märkta punktobjekt är RDD-formaterade frönade datauppsättningar (Resilient Distributed Datasets) på ett sätt som behövs som indata av de flesta ML-algoritmer i MLlib. En [märkt punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vektor, antingen tät eller gles, som är associerad med en etikett/ett svar.  

Det här avsnittet innehåller kod som visar hur du indexerar kategoriska textdata som en [märkt punktdatatyp](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) och kodar den så att den kan användas för att träna och testa MLlib-logistisk regression och andra klassificeringsmodeller. Märkta punktobjekt är RDD (Resilient Distributed Datasets) som består av en etikett (mål-/svarsvariabel) och funktionsvektor. Detta format behövs som indata av många ML-algoritmer i MLlib.

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
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


### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Skapa en slumpmässig delbempling av data och dela upp dem i utbildnings- och testuppsättningar
Denna kod skapar ett slumpmässigt urval av data (25% används här). Även om det inte krävs för det här exemplet på grund av storleken på datauppsättningen, visar vi hur du kan prova här så att du vet hur du använder den för ditt eget problem när det behövs. När proverna är stora kan provtagning spara mycket tid under träningsmodeller. Därefter delar vi upp provet i en träningsdel (75% här) och en testdel (25% här) att använda i klassificering och regressionsmodellering.

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

**Produktionen:**

Tid det tar att köra över cellen: 0,24 sekund

### <a name="feature-scaling"></a>Funktionsskalning
Funktionsskalning, även känd som datanormalisering, försäkrar att funktioner med allmänt utbetalda värden inte ges överdriven vägning i målfunktionen. Koden för funktionsskalning använder [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) för att skala funktionerna till enhetsavvikelse. Det tillhandahålls av MLlib för användning i linjär regression med Stokastisk Gradient Descent (SGD), en populär algoritm för utbildning av ett brett spektrum av andra maskininlärningsmodeller som legaliserade regressioner eller stöd vektormaskiner (SVM).

> [!NOTE]
> Vi har hittat LinearRegressionWithSGD-algoritmen för att vara känslig för funktionsskalning.
> 
> 

Här är koden för att skala variabler för användning med den legaliserade linjära SGD-algoritmen.

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

**Produktionen:**

Tid det tar att köra över cellen: 13,17 sekunder

### <a name="cache-objects-in-memory"></a>Cacheobjekt i minnet
Den tid det tar för utbildning och testning av ML-algoritmer kan minskas genom cachelagring av indatabildramobjekt som används för klassificering, regression och skalade funktioner.

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

**Produktionen:** 

Tid det tar att köra över cellen: 0,15 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Förutsäga om ett tips betalas med binära klassificeringsmodeller
Det här avsnittet visar hur du använder tre modeller för den binära klassificeringsuppgiften att förutsäga om ett tips betalas för en taxiresa eller inte. De modeller som presenteras är:

* Regelbunden logistisk regression 
* Slumpmässig skogsmodell
* Lutning öka träd

Varje modell byggkod avsnitt är uppdelad i steg: 

1. **Modellutbildningsdata** med en parameteruppsättning
2. **Modellutvärdering** på en testdatauppsättning med mått
3. **Spara modell** i blob för framtida konsumtion

### <a name="classification-using-logistic-regression"></a>Klassificering med hjälp av logistisk regression
Koden i det här avsnittet visar hur man tränar, utvärderar och sparar en logistisk regressionsmodell med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om ett tips betalas för en resa i NYC taxiresa och biljettprisdatauppsättning.

**Träna den logistiska regressionsmodellen med CV och hyperparametersvepning**

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


**Produktionen:** 

Koefficienter: [0,0082065285375, -0,0223675576104, -0,0183812028036, -3,48124578069e-05, -0,00247646947233, -0,00165897881503, 0,0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Avlyssna: -0,0111216486893

Tid det tar att köra över cellen: 14,43 sekunder

**Utvärdera den binära klassificeringsmodellen med standardmått**

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

**Produktionen:** 

Yta under PR = 0,985297691373

Yta under ROC = 0,983714670256

Sammanfattning Statistik

Precision = 0,984304060189

Återkallelse = 0,984304060189

F1 Poäng = 0,984304060189

Tid det tar att köra över cellen: 57,61 sekunder

**Rita ROC-kurvan.**

*FörutsägelseAndLabelsDF* registreras som en tabell, *tmp_results*, i föregående cell. *tmp_results* kan användas för att göra frågor och utdata i sqlResults-dataramen för plottning. Här är koden.

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


**Produktionen:**

![Logistisk regression ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Slumpmässig skogsklassificering
Koden i det här avsnittet visar hur man tränar, utvärderar och sparar en slumpmässig skogsmodell som förutsäger om ett tips betalas för en resa i NYC taxiresa och biljettprisdatauppsättning.

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

**Produktionen:**

Yta under ROC = 0,985297691373

Tid det tar att köra över cellen: 31,09 sekunder

### <a name="gradient-boosting-trees-classification"></a>Gradient öka träd klassificering
Koden i det här avsnittet visar hur man tränar, utvärderar och sparar en gradient öka träd modell som förutspår om ett tips betalas för en resa i NYC taxi resa och biljettpris datauppsättning.

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


**Produktionen:**

Yta under ROC = 0,985297691373

Tid det tar att köra över cellen: 19,76 sekunder

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Tippa tipsbelopp för taxiresor med regressionsmodeller
Det här avsnittet visar hur du använder tre modeller för regressionsuppgiften att förutsäga mängden tips som betalas för en taxiresa baserat på andra spetsfunktioner. De modeller som presenteras är:

* Regelbunden linjär regression
* Slumpmässig skog
* Lutning öka träd

Dessa modeller beskrevs i inledningen. Varje modell byggkod avsnitt är uppdelad i steg: 

1. **Modellutbildningsdata** med en parameteruppsättning
2. **Modellutvärdering** på en testdatauppsättning med mått
3. **Spara modell** i blob för framtida konsumtion

### <a name="linear-regression-with-sgd"></a>Linjär regression med SGD
Koden i det här avsnittet visar hur du använder skalade funktioner för att träna en linjär regression som använder stokastisk gradientnedstigning (SGD) för optimering och hur du poäng, utvärdera och spara modellen i Azure Blob Storage (WASB).

> [!TIP]
> Enligt vår erfarenhet kan det finnas problem med konvergensen av LinearRegressionWithSGD-modeller, och parametrar måste ändras /optimeras noggrant för att få en giltig modell. Skalning av variabler hjälper avsevärt till med konvergens. 
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

**Produktionen:**

Koefficienter: [0,00457675809917, -0,0226314167349, -0,0191910355236, 0,246793409578, 0,312047890459, 0,359634405999, 0,00928692253981, -0,000987181489428, -0,0888306617845, 0,0569376211553, 0,115519551711, 0,149250164995, - 0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Avlyssna: 0,853872718283

RMSE = 1,24190115863

R-sqr = 0,608017146081

Tid det tar att köra över cellen: 58,42 sekunder

### <a name="random-forest-regression"></a>Slumpmässig tillbakagång i skogen
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en slumpmässig skogsregression som förutsäger tipsbelopp för NYC-taxiresadata.

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

**Produktionen:**

RMSE = 0,891209218139

R-sqr = 0,759661334921

Tid det tar att köra över cellen: 49,21 sekunder

### <a name="gradient-boosting-trees-regression"></a>Gradient öka träd regression
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en gradient öka träd modell som förutspår tips belopp för NYC taxi resa data.

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

**Produktionen:**

RMSE = 0,908473148639

R-sqr = 0,753835096681

Tid det tar att köra över cellen: 34,52 sekunder

**Tomt**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultaten från tabellen matas ut i *sqlResults-dataramen* för plottning. Här är koden

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Här är koden för att rita data med jupyter servern.

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


**Produktionen:**

![Faktiska-kontra-förväntade-tip-belopp](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Rensa objekt från minnet
Används `unpersist()` för att ta bort objekt som cachelagrats i minnet.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Registrera lagringsplatser för modellerna för förbrukning och poängsättning
Om du vill använda och göra en oberoende datauppsättning som beskrivs i avsnittet [Betyg och utvärdera Spark-byggda maskininlärningsmodeller](spark-model-consumption.md) måste du kopiera och klistra in dessa filnamn som innehåller de sparade modellerna som skapats här i notebooken Consumption Jupyter. Här är koden för att skriva ut sökvägarna till modellfiler du behöver där.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Produktionen**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linjärRegFileLoc = modellDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Nästa steg
Nu när du har skapat regressions- och klassificeringsmodeller med Spark MlLib är du redo att lära dig hur du poängar och utvärderar dessa modeller. Den avancerade datautforskningen och modelleringsanteckningsboken djupdykar djupare in i blandvalidering, hyperparametersvepning och modellutvärdering. 

**Modellförbrukning:** Mer information om hur du poängar och utvärderar de klassificerings- och regressionsmodeller som skapas i det här avsnittet finns i [Betygsätta och utvärdera Spark-byggda maskininlärningsmodeller](spark-model-consumption.md).

**Korsvalidering och hyperparametersvepning:** Se [Avancerad datautforskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med korsvalidering och hyperparametersvepning

