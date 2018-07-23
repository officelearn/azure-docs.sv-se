---
title: Avancerad datagranskning och modellering med Spark | Microsoft Docs
description: Använd HDInsight Spark för att göra datagranskning och träna binär klassificering och regression modeller med korsvalidering och finjustering optimering.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: f90d9a80-4eaf-437b-a914-23514390cd60
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: deguhath
ms.openlocfilehash: a81c23d6acb79e42157ac7d804dac259723b3b0e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37114358"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Avancerad datagranskning och modellering med Spark
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Den här genomgången använder HDInsight Spark att göra datagranskning och träna binär klassificering och regressionsmodeller med korsvalidering och finjustering optimering på ett urval av NYC Taxitransport resa och färdavgiften 2013 datauppsättning. Vi går igenom stegen i den [Data Science Process](http://aka.ms/datascienceprocess), slutpunkt till slutpunkt, använda ett HDInsight Spark-kluster för bearbetning och Azure BLOB för att lagra data och modeller. Processen utforskar och visualiserar data som tillkommit från en Azure Storage Blob och förbereder data för att skapa förutsägelsemodeller. Python har använts för att skriva kod till lösningen och för att visa relevanta områden. De här modellerna är build med hjälp av Spark MLlib verktygen till binär klassificering och regression modellering aktiviteter. 

* Den **binär klassificering** uppgift är att förutsäga om ett tips betalas för resan. 
* Den **regression** uppgift är att förutsäga mängden tips baserat på andra funktioner som tips. 

Modellering stegen också innehålla kod som visar hur du tränar, utvärdera och spara varje typ av modellen. I avsnittet beskrivs några av samma grunden som den [datagranskning och modellering med Spark](spark-data-exploration-modeling.md) avsnittet. Men det är mer ”avancerad” den också använder korsvalidering med finjustering omfattande för att skapa optimalt exakta modeller för klassificering och regression. 

**Korsvalidering (KA)** är en teknik som bedömer hur väl en modell som har tränats på en känd uppsättning data generaliserar att förutsäga funktionerna för datauppsättningar som den inte har tränats.  En vanlig tillämpning som används här är att dela upp en datauppsättning i K vikningar och sedan träna modellen i en resursallokering på alla utom ett av vikningar. Används för att utvärdera möjligheten för modellen till förutsägelse korrekt när testats mot oberoende datauppsättningen i den här vikning som inte används för att träna modellen.

**Finjustering optimering** är problemet för att välja en uppsättning av hyperparametrar för en inlärningsalgoritm vanligtvis med målet att optimera ett mått på den algoritmen prestanda på en oberoende datauppsättning. **Hyperparametrar** är värden som måste anges utanför modellen utbildning proceduren. Antaganden om dessa värden kan påverka flexibilitet och korrektheten i modeller. Beslutsträd har till exempel hyperparametrar, till exempel önskad djup och många blad i trädet. Support Vector datorer (SVMs) kräver en felklassificering särskilda avgifter term. 

Ett vanligt sätt att utföra finjustering optimering används här är en grid-sökning eller en **parameterrensning**. Det här består av utför en uttömmande sökning efter genom värdena en angiven delmängd finjustering utrymme för en inlärningsalgoritm. Mellan verifiering kan ange ett prestandamått för att lösa de optimala resultat som skapas av algoritmen grid sökning. KA användas med finjustering omfattande hjälper till att gränsen problem angående overfitting en modell på träningsdata så att modellen behåller kapacitet att gäller för allmän uppsättning data från vilken träningsdata har extraherats.

Modeller som vi använder är logistic och linjär regression, slumpmässigt skogar och gradient bättre träd:

* [Linjär regression med Descent](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) är en linjär regressionsmodell som använder en Stokastisk brantaste Lutningsmetoden (Descent)-metod och skalning för att förutsäga tips belopp betalas för optimering och funktion. 
* [Logistic regression med LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) eller ”logit” regression är en regressionsmodell som kan användas när den beroende variabeln är kategoriska göra dataklassificering. LBFGS är en kvasi Karlsson optimering algoritm som tillhandahåller algoritmen Broyden – Fletcher – Goldfarb – Shanno (BFGS) med hjälp av en begränsad del av minnet och som ofta används i machine learning.
* [Slumpmässig skogar](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler för beslutsträd.  De kombinera många beslutsträd för att minska risken för overfitting. Slumpmässig skogar för regression och klassificering och kan hantera kategoriska funktioner och kan utökas till inställningen multiklass-baserad klassificering. De kräver funktionen skalning och kan samla in icke-linjära och funktionen interaktioner. Slumpmässig skogar är en av de mest framgångsrika machine learning-modeller för klassificering och regression.
* [Toning förstärkta träd](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) är ensembler för beslutsträd. GBTs träna beslutsträd upprepade gånger för att minimera en förlust-funktion. GBTs för regression och klassificering och kan hantera kategoriska funktioner, kräver funktionen skalning och kan samla in icke-linjära och funktionen interaktioner. De kan också användas i en inställning för multiclass-klassificering.

Modellering exempel med KA och finjustering Svep visas för den binära klassificeringsproblem. Enklare exempel visas (utan parametern rensar) i avsnittet för regression uppgifter. Men i tillägget, validering med hjälp av elastisk net för linjär regression och KA parametern rensning av för slumpmässiga skog regression visas också. Den **elastiska net** är en metod som reglerats regression för Passning linjär regressionsmodeller som linjärt kombinerar L1 och L2 mått som påföljder för den [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) och [upphöjning](https://en.wikipedia.org/wiki/Tikhonov_regularization)metoder.   

> [!NOTE]
> Även om Spark MLlib toolkit är utformat för att arbeta med stora datauppsättningar, används ett relativt litet antal (cirka 30 Mb med 170K rader, ca 0,1% av den ursprungliga datauppsättningen NYC) här för att underlätta. Den här övningen körs effektivt (i cirka 10 minuter) på ett HDInsight-kluster med 2 arbetarnoder. Samma kod, med mindre ändringar kan användas för att bearbeta större-datauppsättningar, med lämpliga ändringar för att cachelagra data i minnet och ändrar storlek på klustret.
> 
> 

## <a name="setup-spark-clusters-and-notebooks"></a>Installationsprogrammet: Spark-kluster och anteckningsböcker
Steg för konfiguration och kod finns i den här genomgången för att använda ett HDInsight Spark 1.6. Men tillhandahålls Jupyter notebooks för HDInsight Spark 1.6- och Spark 2.0. En beskrivning av anteckningsböcker och länkar till dem finns i den [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-lagringsplatsen som innehåller dessa. Dessutom koden här och i länkade anteckningsböcker är generisk och bör fungera i ett Spark-kluster. Om du inte använder HDInsight Spark, konfiguration och hantering av steg kanske skiljer sig från vad som anges här. För enkelhetens skull följer du länkarna till Jupyter-anteckningsböcker för Spark 1.6 och 2.0 som ska köras i pyspark-kerneln för Jupyter Notebook-server:

### <a name="spark-16-notebooks"></a>Spark 1.6 anteckningsböcker

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): innehåller avsnitt i anteckningsboken #1 och modellen utveckling med hjälp av finjustering av hyperparametrar och korsvalidering.

### <a name="spark-20-notebooks"></a>Spark 2.0-anteckningsböcker

[Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): den här filen innehåller information om hur du utför datagranskning, modellering och bedömning i Spark 2.0-kluster.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**UTDATA**

datetime.datetime (2016, 4, 18, 17, 36, 27, 832799)

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Förinställda Spark-kontexten och PySpark användbara funktioner
PySpark-kärnor som tillhandahålls med Jupyter-anteckningsböcker har en förinställd kontext. Så du behöver inte ange Spark eller Hive-kontexterna explicit innan du börjar arbeta med programmet du utvecklar. Dessa kontexter är tillgängliga för dig som standard. Dessa kontexter är:

* SC - för Spark 
* sqlContext - för Hive

PySpark-kerneln innehåller vissa fördefinierade ”användbara”, vilket är särskilt kommandon som du kan anropa med %%. Det finns två kommandon som används i följande kodexempel.

* **%% lokala** anger att koden i efterföljande rader är att köras lokalt. Koden måste vara giltig Python-kod.
* **%% sql -o <variable name>**  kör en Hive-fråga mot sqlContext. Om parametern -o skickas resultatet av frågan sparas i den %% lokal Python-kontext som en Pandas-DataFrame.

För mer information om kärnor för Jupyter notebooks och den fördefinierade ”magics” som ger, se [Kernlar som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Datainmatning från offentlig blob:
Det första steget i data science process är att mata in data analyseras från källor som var den finns i din datagranskning och modelleringsmiljö. Den här miljön är Spark i den här genomgången. Det här avsnittet innehåller koden för att genomföra ett antal uppgifter:

* mata in data-exemplet för att modelleras
* Läs i datauppsättningen för indata (som lagras som en TSV-fil)
* Formatera och rensa data
* Skapa och cachelagrar objekt (rdd-datauppsättningar eller ramar) i minnet
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


**UTDATA**

Åtgången tid att köra ovanför cellen: 276.62 sekunder

## <a name="data-exploration--visualization"></a>Datagranskning och visualisering
När data har trätt i Spark, är nästa steg i data science process att få bättre förståelse av data via utforskning och visualisering. I det här avsnittet ska vi undersöka taxi-data med hjälp av SQL-frågor och rita target-variabler och potentiella funktioner för granskning. Mer specifikt Rita vi frekvensen för passagerar antal i taxi-kommunikation, frekvensen för tips belopp och hur tips varierar belopp och typen.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Rita ett histogram för passagerar antal frekvenser i taxi och RETUR-exemplet
Den här koden och efterföljande kodfragment kan du använda SQL magic för att fråga exemplet och lokala magic data ska ritas.

* **SQL magic (`%%sql`)** The HDInsight PySpark-kernel har stöd för enkel infogade HiveQL frågor mot sqlContext. Den (-o VARIABLE_NAME) argumentet kvarstår utdata från SQL-frågan som en Pandas-DataFrame på Jupyter-servern. Det innebär att den är tillgänglig i lokalt läge.
* Den  **`%%local` magic** används för att köra kod lokalt på servern och Jupyter, som är huvudnoden på HDInsight-klustret. Normalt använder du `%%local` magic efter den `%%sql -o` magic används för att köra en fråga. Parametern -o skulle spara utdata av SQL-frågan lokalt. Sedan `%%local` magic utlöser nästa uppsättning kodfragment för att köra lokalt mot utdata från SQL-frågor som har sparats lokalt. Utdata visualiseras automatiskt när du kör koden.

Den här frågan hämtar kommunikation efter passagerartrafik antal. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Den här koden skapar en lokal dataram från frågeresultatet visas och visar data. Den `%%local` magic skapar en lokal dataram, `sqlResults`, som kan användas för med matplotlib. 

> [!NOTE]
> Den här PySpark-magic används flera gånger i den här genomgången. Om mängden data som är stor, bör du ta prov för att skapa en dataram som ryms i lokalt minne.
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Här är koden för att rita kommunikation med passagerar-antal

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

**UTDATA**

![Frekvensen för kommunikation med passagerartrafik antalet](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Du kan välja bland flera olika typer av visualiseringar (register, cirkel, rad, området eller fält) med hjälp av den **typ** menyn knappar i anteckningsboken. Fältet området visas här.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Rita ett histogram för tips belopp och hur mycket tips varierar beroende på passagerartrafik antal och avgiften belopp.
Använd en SQL-fråga för att samla in data...

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


Den här kodcell använder SQL-fråga för att skapa tre områden data.

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


**UTDATA:** 

![Fördelning av tips](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tips belopp efter passagerar-antal](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tips belopp av avgiften belopp](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funktionen förberedelse av data, omvandling och tekniker för modellering
Det här avsnittet beskriver och innehåller koden för procedurer som används för att förbereda data för användning i ML-modeller. Den visar hur du utföra följande uppgifter:

* Skapa en ny funktion genom att partitionera timmar till trafik tid lagerplatser
* Indexera och på hot koda kategoriska funktioner
* Skapa taggade point-objekt för mata in ML-funktioner
* Skapa en slumpmässig underordnade sampling av data och dela upp den i utbildning och testningsuppsättningar
* Funktionen skalning
* Cacheobjekt i minnet

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Skapa en ny funktion genom att partitionera trafik gånger i lagerplatser
Den här koden visar hur du skapar en ny funktion genom att partitionera trafik gånger i lagerplatser och sedan cachelagra den resulterande dataramen i minnet. Cachelagring leder till bättre körningstid där Resilient Distributed Rdd-datauppsättningar () och dataramar används flera gånger. Så cachelagra vi rdd-datauppsättningar och ramar i flera led i den här genomgången.

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

**UTDATA**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Indexera och en frekvent koda kategoriska funktioner
Det här avsnittet visar hur du indexera eller koda kategoriska funktioner för mata in modelleringsfunktioner. Modellering och förutsäga funktioner för MLlib kräver att funktioner med inkommande kategoridata vara indexerade eller kodade före användning. 

Beroende på modellen behöver du indexera eller koda dem på olika sätt. Till exempel Logistic och linjära Regressionsmodeller kräver en frekvent kodning, där, till exempel en funktion med tre kategorier kan expanderas till tre kolumner i funktionen, med varje som innehåller 0 eller 1 beroende på kategorin för en uppmaning i. MLlib ger [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) som utför en frekvent kodning. Den här encoder mappar en kolumn med etiketten index till en kolumn med binära vektorer, med högst ett ett-värde. Den här kodningen kan algoritmer som förväntar sig numeriska värden funktioner, till exempel logistic regression som ska tillämpas på kategoriska funktioner.

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


**UTDATA**

Åtgången tid att köra ovanför cellen: 3,14 sekunder

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Skapa taggade point-objekt för mata in ML-funktioner
Det här avsnittet innehåller kod som visar hur du indexerar kategoriska textdata som datatypen taggade punkt och koda den. Detta förbereder den för att träna och testa MLlib logistic regression och andra klassificering modeller. Taggade point-objekt är flexibel distribuerade datauppsättningar (RDD) formaterad på ett sätt som krävs som indata av de flesta ML-algoritmer i MLlib. En [märkta punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vector kompakta eller null-optimerade, som är associerad med en etikett/svar.

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
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


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Skapa en slumpmässig underordnade sampling av data och dela upp den i utbildning och testningsuppsättningar
Den här koden skapar ett slumpmässigt urval av data (25% används här). Även om det inte krävs för det här exemplet på grund av storleken på datauppsättningen, visar vi hur du kan ta prov data här. Sedan vet du hur man använder det för dina egna problem om det behövs. När exempel är stor, kan detta spara betydande tid modeller för utbildning. Sedan dela vi exemplet i en utbildning (här 75%) och en testning delen (här 25%) ska användas i klassificering och regression modellering.

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

**UTDATA**

Åtgången tid att köra ovanför cellen: 0.31 sekunder

### <a name="feature-scaling"></a>Funktionen skalning
Funktionen skalning, även kallat databasnormalisering försäkrar att funktioner med brett erläggas värden har inte gett överdriven väga i funktionen servicenivåmål. Koden för funktionen skalning använder den [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) skala funktioner till enhet avvikelse. Den tillhandahålls av MLlib för användning i linjär regression med Stokastisk brantaste Lutningsmetoden (Descent). Descent är en populär algoritm för att träna en mängd andra maskininlärningsmodeller som reglerats upprepningar eller support vector datorer (SVM).   

> [!TIP]
> Vi har hittat LinearRegressionWithSGD-algoritmen för att vara känslig för funktionen skalning.   
> 
> 

Här är koden för att skala variabler för användning med regularized linjär Descent algoritmen.

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

**UTDATA**

Åtgången tid att köra ovanför cellen: 11.67 sekunder

### <a name="cache-objects-in-memory"></a>Cacheobjekt i minnet
Den tid det tar för träning och testning av ML-algoritmer kan minskas genom cachelagring av indata-ramen objekt används för klassificering, regression och skalas funktioner.

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

**UTDATA** 

Åtgången tid att köra ovanför cellen: 0,13 sekunder

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Förutsäga om ett tips är betald med binär klassificering modeller
Det här avsnittet visas hur användning tre modeller för binär klassificering uppgiften att förutsäga om huruvida ett tips betalas för en taxi-resa. Modeller som visas är:

* Logistic regression 
* Slumpmässig skog
* Gradient Boosting träd

Varje modell att skapa avsnitt med exempelkod delas upp i steg: 

1. **Modellera utbildning** data med en parameteruppsättning
2. **Modellera utvärdering** på en datauppsättning för testning med mått
3. **Sparar modell** i blob för framtida användning

Vi visar hur man gör korsvalidering (KA) med parametern oinskränkt på två sätt:

1. Med hjälp av **allmän** anpassad kod som kan tillämpas på alla algoritmer i MLlib och valfri parameter som anger i en algoritm. 
2. Med hjälp av den **pySpark CrossValidator pipeline funktionen**. Observera att CrossValidator har några begränsningar för Spark 1.5.0: 
   
   * Pipeline-modeller får inte vara sparade/sparas för framtida användning.
   * Kan inte användas för varje parameter i en modell.
   * Kan inte användas för varje MLlib-algoritmen.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Allmän verifiering och finjustering oinskränkt används med logistic regression-algoritmen för binär klassificering
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en logistic regression-modellen med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om huruvida ett tips betalas för en resa i NYC taxi resa och avgiften datauppsättningen. Modellen tränas med verifiering (KA) och finjustering oinskränkt har implementerats med anpassad kod som kan tillämpas på någon av learning-algoritmer i MLlib.   

> [!NOTE]
> Körningen av den här anpassade KA-koden kan ta flera minuter.
> 
> 

**Träna logistic regression-modellen med hjälp av KA och finjustering oinskränkt**

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


**UTDATA**

Koefficienter: [0.0082065285375,-0.0223675576104,-0.0183812028036, -3.48124578069e - 05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921, - 0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Skärningspunkt för:-0.0111216486893

Åtgången tid att köra ovanför cellen: 14.43 sekunder

**Utvärdera modellen binär klassificering med standardmått**

Koden i det här avsnittet visar hur du utvärderar en logistic regression-modell mot en test-datauppsättning, inklusive en rityta för ROC-kurvan.

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


**UTDATA**

Området under PR = 0.985336538462

Området under ROC = 0.983383274312

Sammanfattande statistik

Precision = 0.984174341679

Återkalla = 0.984174341679

F1 Poäng = 0.984174341679

Åtgången tid att köra ovanför cellen: 2.67 sekunder

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


**UTDATA**

![Logistic regression ROC-kurvan för allmän metod](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Spara modell i en blob för framtida användning**

Koden i det här avsnittet visar hur du sparar logistic regression-modellen för användning.

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


**UTDATA**

Åtgången tid att köra ovanför cellen: 34.57 sekunder

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Använda Mllib's CrossValidator pipeline-funktion med logistiska regressionsmodell (elastisk regression)
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en logistic regression-modellen med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om huruvida ett tips betalas för en resa i NYC taxi resa och avgiften datauppsättningen. Modellen tränas med verifiering (KA) och finjustering oinskränkt implementerats med funktionen MLlib CrossValidator pipeline för KA med parameterrensning.   

> [!NOTE]
> Körningen av den här MLlib KA-koden kan ta flera minuter.
> 
> 

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

**UTDATA**

Åtgången tid att köra ovanför cellen: 107.98 sekunder

**Rita ROC-kurvan.**

Den *predictionAndLabelsDF* registreras som en tabell, *tmp_results*, i föregående cell. *tmp_results* kan användas för att gör frågor och utdataresultat i sqlResults dataram för. Här är koden.

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


**UTDATA**

![Logistic regression ROC-kurvan med Mllib's CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Slumpmässig skog klassificering
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en slumpmässig skog regression som beräknar huruvida ett tips betalas för en resa i NYC taxi resa och avgiften datauppsättningen.

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


**UTDATA**

Området under ROC = 0.985336538462

Åtgången tid att köra ovanför cellen: 26.72 sekunder

### <a name="gradient-boosting-trees-classification"></a>Gradient boosting träd klassificering
Koden i det här avsnittet visar hur du tränar, utvärdera, och spara en gradient boosting träd-modell som förutsäger huruvida ett tips betalas för en resa i NYC taxi resa och färdavgiften datauppsättning.

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

**UTDATA**

Området under ROC = 0.985336538462

Åtgången tid att köra ovanför cellen: 28.13 sekunder

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Förutsäga tips belopp med regressionsmodeller (inte använda KA)
Det här avsnittet visas hur användning tre modeller för aktiviteten regression: förutsäga tips summa för en taxi-resa baserat på andra funktioner som tips. Modeller som visas är:

* Reglerats linjär regression
* Slumpmässig skog
* Gradient Boosting träd

Dessa modeller beskrivs i inledningen. Varje modell att skapa avsnitt med exempelkod delas upp i steg: 

1. **Modellera utbildning** data med en parameteruppsättning
2. **Modellera utvärdering** på en datauppsättning för testning med mått
3. **Sparar modell** i blob för framtida användning   

> AZURE Obs: Korsvalidering används inte med tre regressionsmodeller i det här avsnittet, eftersom detta visades i detalj för logistic regressionsmodeller. Ett exempel som visar hur du använder KA med elastisk Net för linjär regression tillhandahålls i tillägget i det här avsnittet.
> 
> AZURE Obs: Enligt vår erfarenhet medför det kan uppstå problem med möte för LinearRegressionWithSGD modeller och parametrar måste vara ändrats/optimerade noggrant för att hämta en giltig modell. Skalning av variabler avsevärt hjälper med konvergens. Elastisk net regression som visas i tillägg till det här avsnittet kan också användas i stället för LinearRegressionWithSGD.
> 
> 

### <a name="linear-regression-with-sgd"></a>Linjär regression med Descent
Koden i det här avsnittet visar hur du använder skalade funktioner för att träna en linjär regression som använder stokastisk brantaste lutningsmetoden (Descent) för optimering och hur du poäng, utvärdera och spara modellen i Azure Blob Storage (WASB).

> [!TIP]
> Enligt vår erfarenhet medför det kan finnas problem med möte för LinearRegressionWithSGD modeller och parametrar måste vara ändrats/optimerade noggrant för att hämta en giltig modell. Skalning av variabler avsevärt hjälper med konvergens.
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

**UTDATA**

Koefficienter: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,- 0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Fånga upp: 0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

Åtgången tid att köra ovanför cellen: 38.62 sekunder

### <a name="random-forest-regression"></a>Slumpmässig skog regression
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en slumpmässig Skogsmodell som beräknar tips beloppet för NYC taxi resedata.   

> [!NOTE]
> Korsvalidering med parametern oinskränkt med anpassad kod ges i bilagan.
> 
> 

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

**UTDATA**

RMSE = 0.931981967875

R-sqr = 0.733445485802

Åtgången tid att köra ovanför cellen: 25.98 sekunder

### <a name="gradient-boosting-trees-regression"></a>Gradient boosting träd regression
Koden i det här avsnittet visar hur du tränar, utvärdera och spara en gradient boosting träd-modell som förutsäger tips beloppet för NYC taxi resedata.

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


**UTDATA**

RMSE = 0.928172197114

R-sqr = 0.732680354389

Åtgången tid att köra ovanför cellen: 20,9 sekunder

**Rita**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultat från tabellen matats ut till den *sqlResults* dataram för. Här är koden

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Här är koden för att rita data med Jupyter-server.

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

![Aktuella-vs-förutse-tips-belopp](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Bilaga: Förhållandet uppgifter med parametersvep mellan verifiering
Den här bilagan innehåller kod som visar hur du gör KA med elastisk net för linjär regression och hur du gör KA med parameterrensning med anpassad kod för slumpmässiga skog regression.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Korsvalidering med elastisk net för linjär regression
Koden i det här avsnittet visar hur du korsvalidering med elastisk net för linjär regression och hur du utvärdera modellen mot testdata.

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

    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**UTDATA**

Åtgången tid att köra ovanför cellen: 161.21 sekunder

**Utvärdera med R-SQR mått**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultat från tabellen matats ut till den *sqlResults* dataram för. Här är koden

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Här är koden för att beräkna R sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**UTDATA**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Korsvalidering med parameterrensning med anpassad kod för slumpmässiga skog regression
Koden i det här avsnittet visar hur du korsvalidering med parameterrensning med anpassad kod för slumpmässiga skog regression och hur du utvärdera modellen mot testdata.

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


**UTDATA**

RMSE = 0.906972198262

R-sqr = 0.740751197012

Åtgången tid att köra ovanför cellen: 69.17 sekunder

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Rensa objekt från minne och skriva ut modellen platser
Använd `unpersist()` att ta bort objekt som cachelagrats i minnet.

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


**UTDATA**

PythonRDD [122] vid RDD på PythonRDD.scala: 43

** Utskrift sökvägen till modellfiler som ska användas i anteckningsboken förbrukning. ** Om du vill använda och bedöma en oberoende datauppsättning, måste du kopiera och klistra in dessa filnamn i ”dagbok förbrukning”.

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**UTDATA**

logisticRegFileLoc = modelDir + ”LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528”

linearRegFileLoc = modelDir + ”LinearRegressionWithSGD_2016-05-0316_51_28.433670”

randomForestClassificationFileLoc = modelDir + ”RandomForestClassification_2016-05-0316_50_17.454440”

randomForestRegFileLoc = modelDir + ”RandomForestRegression_2016-05-0316_51_57.331730”

BoostedTreeClassificationFileLoc = modelDir + ”GradientBoostingTreeClassification_2016-05-0316_50_40.138809”

BoostedTreeRegressionFileLoc = modelDir + ”GradientBoostingTreeRegression_2016-05-0316_52_18.827237”

## <a name="whats-next"></a>Nästa steg
Nu när du har skapat regression och klassificering modeller med Spark MlLib kan är du redo att lära dig hur du bedöma och utvärdera dessa modeller.

**Modellera förbrukning:** information om hur du bedöma och utvärdera klassificerings- och regressionsmodeller modeller som skapats i det här avsnittet finns [poäng och utvärdera Spark-byggda machine learning-modeller](spark-model-consumption.md).

