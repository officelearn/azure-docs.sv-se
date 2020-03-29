---
title: Avancerad datautforskning och modellering med Spark - Team Data Science Process
description: Använd HDInsight Spark för att göra datautforskning och träna binära klassificerings- och regressionsmodeller med korsvalidering och hyperparameteroptimering.
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
ms.openlocfilehash: 15d9d186ef36ee9181a6ce0386aa9cc5de7838e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718659"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Avancerad datagranskning och modellering med Spark

Den här genomgången använder HDInsight Spark för att göra datautforskning och träna binära klassificerings- och regressionsmodeller med korsvalidering och hyperparameteroptimering på ett urval av NYC-taxiresan och biljettpriset 2013-datauppsättningen. Den går igenom stegen i [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, med hjälp av ett HDInsight Spark-kluster för bearbetning och Azure-blobbar för att lagra data och modeller. Processen utforskar och visualiserar data som tas in från en Azure Storage Blob och förbereder sedan data för att skapa prediktiva modeller. Python har använts för att koda lösningen och för att visa relevanta ritplaner. Dessa modeller bygger med spark MLlib-verktygslådan för att utföra binära klassificerings- och regressionsmodelleringsuppgifter. 

* Den **binära klassificeringsuppgiften** är att förutsäga om ett tips betalas för resan eller inte. 
* **Regressionsuppgiften** är att förutsäga mängden av spetsen baserat på andra tipsfunktioner. 

Modelleringsstegen innehåller också kod som visar hur du tränar, utvärderar och sparar varje typ av modell. Ämnet täcker några av samma mark som [datautforskning och modellering med Spark](spark-data-exploration-modeling.md) ämne. Men det är mer "avancerad" i att den också använder korsvalidering med hyperparameter svepande för att träna optimalt exakt klassificering och regressionsmodeller. 

**Korsvalidering (CV)** är en teknik som bedömer hur väl en modell tränas på en känd uppsättning data generaliserar för att förutsäga funktionerna i datauppsättningar som den inte har tränats på.  En gemensam implementering som används här är att dela upp en datauppsättning i K veck och sedan träna modellen i en round-robin mode på alla utom en av vecken. Modellens förmåga att förutsäga exakt när den testas mot den oberoende datauppsättningen i denna vikning som inte används för att träna modellen bedöms.

**Hyperparameter optimering** är problemet med att välja en uppsättning hyperparametrar för en inlärningsalgoritm, vanligtvis med målet att optimera ett mått på algoritmens prestanda på en oberoende datauppsättning. **Hyperparametrar** är värden som måste anges utanför modellutbildningsproceduren. Antaganden om dessa värden kan påverka flexibiliteten och noggrannheten hos modellerna. Beslutsträd har hyperparametrar, till exempel, till exempel önskat djup och antal löv i trädet. Support Vector Machines (SVMs) kräver att du anger en felaktig klassificering straffperiod. 

Ett vanligt sätt att utföra hyperparameteroptimering som används här är en rutnätssökning eller ett **parametersvep**. Den här sökningen går igenom en delmängd av hyperparameterutrymmet för en inlärningsalgoritm. Korsvalidering kan ge ett prestandamått för att reda ut de optimala resultat som produceras av rutnätssökalgoritmen. CV som används med hyperparametersvepning hjälper till att begränsa problem som övermontering av en modell till träningsdata så att modellen behåller kapaciteten att tillämpa på den allmänna uppsättning data från vilka träningsdata extraherades.

De modeller vi använder inkluderar logistisk och linjär regression, slumpmässiga skogar och gradient boostade träd:

* [Linjär regression med SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) är en linjär regressionsmodell som använder en SGD-metod (Stokastisk gradient descent) och för optimering och funktionsskalning för att förutsäga de betalda dricksbeloppen. 
* [Logistisk regression med LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) eller "logit" regression, är en regressionsmodell som kan användas när den beroende variabeln är kategorisk för att göra dataklassificering. LBFGS är en nästan Newton optimeringsalgoritm som approximerar Broyden-Fletcher-Goldfarb-Shanno (BFGS) algoritm med hjälp av en begränsad mängd datorminne och som ofta används i maskininlärning.
* [Slumpmässiga skogar](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler av beslutsträd.  De kombinerar många beslutsträd för att minska risken för övermontering. Slumpmässiga skogar används för regression och klassificering och kan hantera kategoriska funktioner och kan utökas till klassificeringsinställningen för fleraklasser. De kräver inte funktionsskalning och kan fånga icke-linjäriteter och funktionsinteraktioner. Slumpmässiga skogar är en av de mest framgångsrika maskininlärningsmodellerna för klassificering och regression.
* [Gradient boosted trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) är ensembler av beslutsträd. GBTS tåg beslut träd iterativt för att minimera en förlust funktion. GBTS används för regression och klassificering och kan hantera kategoriska funktioner, kräver inte funktionsskalning och kan fånga icke-linjäriteter och funktionsinteraktioner. De kan också användas i en multiklassklassificeringsinställning.

Modelleringsexempel med CV- och Hyperparameter-svep visas för binärklassificeringsproblemet. Enklare exempel (utan parametervep) presenteras i huvudavsnittet för regressionsaktiviteter. Men i bilagan presenteras också validering med elastiskt nät för linjär regression och CV med parametervep med hjälp av slumpmässig skogsregression. Det **elastiska nätet** är en reglerad regressionsmetod för montering av linjära regressionsmodeller som linjärt kombinerar L1- och L2-måtten som påföljder för [lasso-](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) och [åsenmetoderna.](https://en.wikipedia.org/wiki/Tikhonov_regularization)   

<!-- -->

> [!NOTE]
> Även om Spark MLlib-verktygslådan är utformad för att fungera med stora datamängder, används ett relativt litet urval (~30 Mb med 170 K rader, cirka 0,1 % av den ursprungliga NYC-datauppsättningen) här för enkelhetens skull. Övningen som ges här körs effektivt (på cirka 10 minuter) på ett HDInsight-kluster med 2 arbetsnoder. Samma kod, med mindre ändringar, kan användas för att bearbeta större datauppsättningar, med lämpliga ändringar för cachelagring av data i minnet och ändra klusterstorleken.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Installation: Spark kluster och anteckningsböcker
Installationssteg och kod finns i den här genomgången för att använda en HDInsight Spark 1.6. Men Jupyter-anteckningsböcker finns för både HDInsight Spark 1.6- och Spark 2.0-kluster. En beskrivning av anteckningsböckerna och länkarna till dem finns i [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom är koden här och i de länkade anteckningsböckerna allmän och bör fungera på alla Spark-kluster. Om du inte använder HDInsight Spark kan stegen för klusterinställningar och hantering skilja sig något från vad som visas här. För enkelhetens skull, här är länkarna till Jupyter bärbara datorer för Spark 1,6 och 2,0 som ska köras i pyspark kärnan i Jupyter Notebook server:

### <a name="spark-16-notebooks"></a>Spark 1.6 bärbara datorer

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Innehåller ämnen i bärbara #1 och modellutveckling med hyperparameterjustering och korsvalidering.

### <a name="spark-20-notebooks"></a>Spark 2.0 bärbara datorer

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Den här filen innehåller information om hur du utför datautforskning, modellering och bedömning i Spark 2.0-kluster.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**Produktionen**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importera bibliotek
Importera nödvändiga bibliotek med följande kod:

    # LOAD PYSPARK LIBRARIES
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

Mer information om kärnorna för Jupyter-anteckningsböcker och de fördefinierade "magi" som de tillhandahåller finns i [Kärnor som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster på HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Datainmatning från offentlig blob:
Det första steget i datascience-processen är att inta de data som ska analyseras från källor där den finns i din datautforskning och modelleringsmiljö. Den här miljön är Spark i den här genomgången. Det här avsnittet innehåller koden för att slutföra en serie uppgifter:

* intag av det dataprov som ska modelleras
* läsa i indatauppsättningen (lagras som en TSV-fil)
* formatera och rensa data
* skapa och cachelagra objekt (RDD:er eller dataramar) i minnet
* registrera den som en temp-tabell i SQL-kontext.

Här är koden för datainmatning.

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

    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Produktionen**

Tid det tar att köra över cellen: 276,62 sekunder

## <a name="data-exploration--visualization"></a>Datautforskning & visualisering
När data har förts in i Spark, är nästa steg i datascience processen att få djupare förståelse för data genom utforskning och visualisering. I det här avsnittet undersöker vi taxidata med hjälp av SQL-frågor och ritar målvariablerna och prospektiva funktioner för visuell inspektion. Specifikt plottar vi frekvensen av passagerarantal i taxiresor, frekvensen av tipsbelopp och hur tips varierar beroende på betalningsbelopp och typ.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Rita ett histogram av passagerarantalfrekvenser i urvalet av taxiresor
Den här koden och efterföljande utdrag använder SQL-magi för att fråga exemplet och lokal magi för att rita data.

* **SQL magi`%%sql`( )** HDInsight PySpark-kärnan stöder enkla inline HiveQL-frågor mot sqlContext. Argumentet (-o VARIABLE_NAME) kvarstår utdata för SQL-frågan som en Pandas DataFrame på Jupyter-servern. Det innebär att den är tillgänglig i det lokala läget.
* ** `%%local` Magin** används för att köra kod lokalt på Jupyter-servern, som är headnod för HDInsight-klustret. Vanligtvis använder `%%local` du magi `%%sql -o` när magin används för att köra en fråga. Parametern -o skulle bevara utdata för SQL-frågan lokalt. Sedan `%%local` utlöser magin nästa uppsättning kodavsnitt för att köras lokalt mot utdata från SQL-frågor som har sparats lokalt. Utdata visualiseras automatiskt när du har kört koden.

Den här frågan hämtar resorna efter antalet passagerare. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Den här koden skapar en lokal dataram från frågeutdata och ritar data. Magin `%%local` skapar en lokal dataram, `sqlResults`, som kan användas för plottning med matplotlib. 

<!-- -->

> [!NOTE]
> Denna PySpark magi används flera gånger i denna genomgång. Om mängden data är stor bör du ta ett exempel för att skapa en dataram som får plats i det lokala minnet.

<!-- -->

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Här är koden för att rita resor av passagerare räknas

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**Produktionen**

![Frekvens av resor per antal passagerare](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Du kan välja mellan flera olika typer av visualiseringar (Tabell, Cirkel, Linje, Område eller Stapel) med hjälp av **menyknapparna Typ** i anteckningsboken. Bar-tomten visas här.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Rita ett histogram av tips belopp och hur tips belopp varierar beroende på passagerare räkna och belopp biljettpris.
Använd en SQL-fråga för att exempela data..

    # SQL SQUERY
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

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline

    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()


**Produktionen:** 

![Distribution av tipsbelopp](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tipsbelopp per antal passagerare](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipsbelopp efter pris Belopp](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funktionsteknik, omvandling och dataförberedelse för modellering
Det här avsnittet beskriver och innehåller koden för procedurer som används för att förbereda data för användning i ML-modellering. Den visar hur du utför följande uppgifter:

* Skapa en ny funktion genom att partitionera timmar i trafiktidslagerplatser
* Kategoriska funktioner för index och on-hot-kod
* Skapa märkta punktobjekt för inmatning i ML-funktioner
* Skapa en slumpmässig delbempling av data och dela upp dem i utbildnings- och testuppsättningar
* Funktionsskalning
* Cacheobjekt i minnet

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Skapa en ny funktion genom att partitionera trafiktider på lagerplatser
Den här koden visar hur du skapar en ny funktion genom att partitionera trafiktider på lagerplatser och sedan hur du cachelagrar den resulterande dataramen i minnet. Cachelagring leder till förbättrad körningstid där Fröna distribuerade datauppsättningar (RDDs) och dataramar används upprepade gånger. Så vi cache rdds och data-ramar i flera steg i den här genomgången.

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

**Produktionen**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index och en-het kod kategoriska funktioner
Det här avsnittet visar hur du indexerar eller kodar kategoriska funktioner för indata i modelleringsfunktionerna. Modellerings- och förutsäga funktioner i MLlib kräver att funktioner med kategoriska indata indexeras eller kodas före användning. 

Beroende på modell måste du indexera eller koda dem på olika sätt. Till exempel kräver modeller för logistisk och linjär regression en frekvent kodning, där till exempel en funktion med tre kategorier kan expanderas till tre funktionskolumner, där var och en innehåller 0 eller 1 beroende på kategorin för en observation. MLlib tillhandahåller [OneHotEncoder-funktionen](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) för att göra en frekvent kodning. Den här kodaren mappar en kolumn med etikettindex till en kolumn med binära vektorer, med högst ett enda värde. Med den här kodningen kan algoritmer som förväntar sig numeriska värdefunktioner, till exempel logistisk regression, tillämpas på kategoriska funktioner.

Här är koden för att indexera och koda kategoriska funktioner:

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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


**Produktionen**

Tid det tar att köra över cellen: 3,14 sekunder

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Skapa märkta punktobjekt för inmatning i ML-funktioner
Det här avsnittet innehåller kod som visar hur du indexerar kategoriska textdata som en märkt punktdatatyp och hur du kodar den. Den här omvandlingen förbereder textdata som ska användas för att träna och testa MLlib logistisk regression och andra klassificeringsmodeller. Märkta punktobjekt är RDD-formaterade frönade datauppsättningar (Resilient Distributed Datasets) på ett sätt som behövs som indata av de flesta ML-algoritmer i MLlib. En [märkt punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vektor, antingen tät eller gles, som är associerad med en etikett/ett svar.

Här är koden för att indexera och koda textfunktioner för binär klassificering.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Denna kod skapar ett slumpmässigt urval av data (25% används här). Även om det inte krävs för det här exemplet på grund av datauppsättningens storlek, visar vi hur du kan prova data här. Då vet du hur man använder den för ditt eget problem om det behövs. När proverna är stora kan provtagning spara mycket tid under träningsmodeller. Därefter delar vi upp provet i en träningsdel (75% här) och en testdel (25% här) att använda i klassificering och regressionsmodellering.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand

    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()

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

**Produktionen**

Tid det tar att köra över cellen: 0,31 sekund

### <a name="feature-scaling"></a>Funktionsskalning
Funktionsskalning, även känd som datanormalisering, försäkrar att funktioner med allmänt utbetalda värden inte ges överdriven vägning i målfunktionen. Koden för funktionsskalning använder [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) för att skala funktionerna till enhetsavvikelse. Den tillhandahålls av MLlib för användning i linjär regression med Stokastisk gradient descent (SGD). SGD är en populär algoritm för utbildning av ett brett spektrum av andra maskininlärningsmodeller, till exempel legaliserade regressioner eller stöd för vektormaskiner (SVM).   

> [!TIP]
> Vi har hittat LinearRegressionWithSGD-algoritmen för att vara känslig för funktionsskalning.   
> 
> 

Här är koden för att skala variabler för användning med den legaliserade linjära SGD-algoritmen.

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

**Produktionen**

Tid det tar att köra över cellen: 11,67 sekunder

### <a name="cache-objects-in-memory"></a>Cacheobjekt i minnet
Den tid det tar för utbildning och testning av ML-algoritmer kan minskas genom att cachelagra indataramobjekt som används för klassificering, regression och skalade funktioner.

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

**Produktionen** 

Tid det tar att köra över cellen: 0,13 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Förutsäga om ett tips betalas med binära klassificeringsmodeller
Det här avsnittet visar hur du använder tre modeller för den binära klassificeringsuppgiften att förutsäga om ett tips betalas för en taxiresa eller inte. De modeller som presenteras är:

* Logistisk regression 
* Slumpmässig skog
* Lutning öka träd

Varje modell byggkod avsnitt är uppdelad i steg: 

1. **Modellutbildningsdata** med en parameteruppsättning
2. **Modellutvärdering** på en testdatauppsättning med mått
3. **Spara modell** i blob för framtida konsumtion

Vi visar hur du gör korsvalidering (CV) med parametersvepning på två sätt:

1. Använda **generisk** anpassad kod som kan tillämpas på valfri algoritm i MLlib och på alla parameteruppsättningar i en algoritm. 
2. Använda **pipelinefunktionen pySpark CrossValidator**. CrossValidator har några begränsningar för Spark 1.5.0: 
   
   * Pipeline-modeller kan inte sparas eller sparas för framtida förbrukning.
   * Det går inte att använda för varje parameter i en modell.
   * Kan inte användas för varje MLlib-algoritm.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Generisk korsvalidering och hyperparametersvepning som används med den logistiska regressionsalgoritmen för binär klassificering
Koden i det här avsnittet visar hur man tränar, utvärderar och sparar en logistisk regressionsmodell med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om ett tips betalas för en resa i NYC taxiresa och biljettprisdatauppsättning. Modellen tränas med korsvalidering (CV) och hyperparametersvepning implementerad med anpassad kod som kan tillämpas på någon av inlärningsalgoritmerna i MLlib.   

<!-- -->

> [!NOTE]
> Körningen av den här anpassade CV-koden kan ta flera minuter.

<!-- -->

**Träna den logistiska regressionsmodellen med CV och hyperparametersvepning**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics

    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)

    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric

    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)


    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Produktionen**

Koefficienter: [0,0082065285375, -0,0223675576104, -0,0183812028036, -3,48124578069e-05, -0,00247646947233, -0,00165897881503, 0,0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Avlyssna: -0,0111216486893

Tid det tar att köra över cellen: 14,43 sekunder

**Utvärdera den binära klassificeringsmodellen med standardmått**

Koden i det här avsnittet visar hur du utvärderar en logistisk regressionsmodell mot en testdatauppsättning, inklusive ett område i ROC-kurvan.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Produktionen**

Yta under PR = 0,985336538462

Yta under ROC = 0,983383274312

Sammanfattning Statistik

Precision = 0,984174341679

Minns = 0,984174341679

F1 Betyg = 0,984174341679

Tid det tar att köra över cellen: 2,67 sekunder

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

    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVES
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


**Produktionen**

![Logistisk regressions-ROC-kurva för generisk metod](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Persist modell i en blob för framtida konsumtion**

Koden i det här avsnittet visar hur du sparar den logistiska regressionsmodellen för förbrukning.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;

    logitBest.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**Produktionen**

Tid det tar att köra över cellen: 34,57 sekunder

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Använd MLlibs pipelinefunktion CrossValidator med modell för logistisk regression (elastisk regression)
Koden i det här avsnittet visar hur man tränar, utvärderar och sparar en logistisk regressionsmodell med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om ett tips betalas för en resa i NYC taxiresa och biljettprisdatauppsättning. Modellen tränas med korsvalidering (CV) och hyperparametersvep implementerad med MLlib CrossValidator-rörledningsfunktionen för CV med parametersvep.   

<!-- -->

> [!NOTE]
> Körningen av denna MLlib CV-kod kan ta flera minuter.

<!-- -->

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc

    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**Produktionen**

Tid det tar att köra över cellen: 107,98 sekunder

**Rita ROC-kurvan.**

*FörutsägelseAndLabelsDF* registreras som en tabell, *tmp_results*, i föregående cell. *tmp_results* kan användas för att göra frågor och utdata i sqlResults-dataramen för plottning. Här är koden.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Här är koden för att rita ROC-kurvan.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc

    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    #PLOT
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


**Produktionen**

![Logistisk regressions-ROC-kurva med MLlibs CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Slumpmässig skogsklassificering
Koden i det här avsnittet visar hur man tränar, utvärderar och sparar en slumpmässig skogsregression som förutsäger om ett tips betalas för en resa i NYC taxiresa och biljettprisdatauppsättning.

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
    ## UN-COMMENT IF YOU WANT TO PRING TREES
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


**Produktionen**

Yta under ROC = 0,985336538462

Tid det tar att köra över cellen: 26,72 sekunder

### <a name="gradient-boosting-trees-classification"></a>Gradient öka träd klassificering
Koden i det här avsnittet visar hur man tränar, utvärderar och sparar en gradient öka träd modell som förutspår om ett tips betalas för en resa i NYC taxi resa och biljettpris datauppsättning.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # Area under ROC curve
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

**Produktionen**

Yta under ROC = 0,985336538462

Tid det tar att köra över cellen: 28,13 sekunder

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Förutsäga tipsbelopp med regressionsmodeller (använder inte CV)
Det här avsnittet visar hur du använder tre modeller för regressionsuppgiften: förutsäg dricksbeloppet som betalas för en taxiresa baserat på andra spetsfunktioner. De modeller som presenteras är:

* Regelbunden linjär regression
* Slumpmässig skog
* Lutning öka träd

Dessa modeller beskrevs i inledningen. Varje modell byggkod avsnitt är uppdelad i steg: 

1. **Modellutbildningsdata** med en parameteruppsättning
2. **Modellutvärdering** på en testdatauppsättning med mått
3. **Spara modell** i blob för framtida konsumtion   

<!-- -->

> [!NOTE] 
> Korsvalidering används inte med de tre regressionsmodellerna i det här avsnittet, eftersom detta visades i detalj för de logistiska regressionsmodellerna. Ett exempel som visar hur du använder CV med elastiskt nät för linjär regression finns i tillägget till det här avsnittet.

<!-- -->

<!-- -->

> [!NOTE] 
> Enligt vår erfarenhet kan det finnas problem med konvergens av LinearRegressionWithSGD modeller, och parametrar måste ändras / optimeras noggrant för att få en giltig modell. Skalning av variabler hjälper avsevärt till med konvergens. Elastisk nettoregression, som visas i tillägget till det här avsnittet, kan också användas i stället för LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Linjär regression med SGD
Koden i det här avsnittet visar hur du använder skalade funktioner för att träna en linjär regression som använder stokastisk gradientnedstigning (SGD) för optimering och hur du poäng, utvärdera och spara modellen i Azure Blob Storage (WASB).

> [!TIP]
> Enligt vår erfarenhet kan det finnas problem med konvergensen av LinearRegressionWithSGD-modeller, och parametrar måste ändras /optimeras noggrant för att få en giltig modell. Skalning av variabler hjälper avsevärt till med konvergens.
> 
> 

    # LINEAR REGRESSION WITH SGD 

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

    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Produktionen**

Koefficienter: [0,0141707753435. -0,0252930927087, -0,0231442517137, 0,247070902996, 0,312544147152, 0,360296120645, 0,0122079566092, -0,00456498588241, -0,0898228505177, 0,0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, - 0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Avlyssna: 0,854507624459

RMSE = 1,23485131376

R-sqr = 0,597963951127

Tid det tar att köra över cellen: 38,62 sekunder

### <a name="random-forest-regression"></a>Slumpmässig tillbakagång i skogen
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en slumpmässig skogsmodell som förutsäger tipsbelopp för NYC-taxiresadata.   

<!-- -->

> [!NOTE]
> Korsvalidering med parametervepning med anpassad kod finns i bilagan.

<!-- -->

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

**Produktionen**

RMSE = 0,931981967875

R-sqr = 0,733445485802

Tid det tar att köra över cellen: 25,98 sekunder

### <a name="gradient-boosting-trees-regression"></a>Gradient öka träd regression
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en gradient öka träd modell som förutspår tips belopp för NYC taxi resa data.

**Träna och utvärdera**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Produktionen**

RMSE = 0,928172197114

R-sqr = 0,732680354389

Tid det tar att köra över cellen: 20,9 sekunder

**Tomt**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultaten från tabellen matas ut i *sqlResults-dataramen* för plottning. Här är koden

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Här är koden för att rita data med jupyter servern.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np

    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Faktiska-kontra-förväntade-tip-belopp](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Tillägg: Ytterligare regressionsaktiviteter med korsvalidering med parametersvep
Den här bilagan innehåller kod som visar hur du gör CV med elastiskt nät för linjär regression och hur du gör CV med parametervep med anpassad kod för slumpmässig skogsregression.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Korsvalidering med elastiskt nät för linjär regression
Koden i det här avsnittet visar hur du gör korsvalidering med elastiskt nät för linjär regression och hur du utvärderar modellen mot testdata.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 

    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)

    # CONVERT TO DF AND SAVE REGISTER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Produktionen**

Tid det tar att köra över cellen: 161,21 sekunder

**Utvärdera med R-SQR-mått**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultaten från tabellen matas ut i *sqlResults-dataramen* för plottning. Här är koden

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Här är koden för att beräkna R-sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**Produktionen**

R-sqr = 0,619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Korsvalidering med parametervep med anpassad kod för slumpmässig skogsregression
Koden i det här avsnittet visar hur du gör korsvalidering med parametervep med anpassad kod för slumpmässig skogsregression och hur du utvärderar modellen mot testdata.

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid

    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))

    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric

    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Produktionen**

RMSE = 0,906972198262

R-sqr = 0,740751197012

Tid det tar att köra över cellen: 69,17 sekunder

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Rensa objekt från minnes- och utskriftsmodellplatser
Används `unpersist()` för att ta bort objekt som cachelagrats i minnet.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()

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


**Produktionen**

PythonRDD[122] på RDD på PythonRDD.scala: 43

**Utdatasökväg till modellfiler som ska användas i förbrukningsanteckningsboken. ** Om du vill använda och göra en oberoende datauppsättning måste du kopiera och klistra in dessa filnamn i "Förbrukningsanteckningsboken".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Produktionen**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linjärRegFileLoc = modellDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Nästa steg
Nu när du har skapat regressions- och klassificeringsmodeller med Spark MlLib är du redo att lära dig hur du poängar och utvärderar dessa modeller.

**Modellförbrukning:** Mer information om hur du poängar och utvärderar de klassificerings- och regressionsmodeller som skapas i det här avsnittet finns i [Betygsätta och utvärdera Spark-byggda maskininlärningsmodeller](spark-model-consumption.md).

