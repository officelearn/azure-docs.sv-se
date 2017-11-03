---
title: "Datavetenskap med hjälp av Scala och Spark på Azure | Microsoft Docs"
description: "Hur du använder Scala för övervakade machine learning aktiviteter med Spark skalbara MLlib och Spark ML paketen på ett Azure HDInsight Spark-kluster."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a7c97153-583e-48fe-b301-365123db3780
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;deguhath
ms.openlocfilehash: 8f1d9ab5186684c4aac806ace4ebfd38ca1fb306
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Datavetenskap med Scala och Spark på Azure
Den här artikeln visar hur du använder Scala för övervakade machine learning uppgifter med Spark skalbara MLlib och Spark ML paketen på ett Azure HDInsight Spark-kluster. Den vägleder dig igenom de aktiviteter som utgör den [datavetenskap processen](http://aka.ms/datascienceprocess): datapåfyllning och undersökning, visualisering, funktionen tekniker, modellering och förbrukning av modellen. Modeller i artikeln är logistic och linjär regression, slumpmässiga skogar och ökat toningen träd (GBTs), förutom två övervakat utföra vanliga:

* Regression problem: förutsägelser av tips belopp ($) för en taxi resa
* Binär klassificering: förutsägelser av tips eller inget tips (1/0) för en taxi resa

Modelleringsprocessen kräver träning och utvärdering på en datauppsättning test och relevanta noggrannhet mått. I den här artikeln får du lära dig hur du lagrar dessa modeller i Azure Blob storage och poängsätta och utvärdera deras förutsägbar prestanda. Den här artikeln beskriver också mer avancerade avsnitt om hur du optimerar modeller med hjälp av korsvalidering och hyper-parametern omfattande. De data som används är ett exempel på de 2013 NYC taxi resa och avgiften data som finns på GitHub.

[Scala](http://www.scala-lang.org/), ett språk baserat på Java virtual machine integrerar Språkbegrepp för objektorienterad och fungerar. Det är ett skalbart språk som är utmärkt för distribuerad databehandling i molnet och körs på Azure Spark-kluster.

[Spark](http://spark.apache.org/) är ett ramverk för parallell bearbetning av öppen källkod som stöder minnesintern bearbetning för att höja prestandan hos analytics stordataprogram. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks funktioner för beräkning för distribuerade i minnet gör det ett bra alternativ för iterativa algoritmer i machine learning och grafberäkningar. Den [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) paketet ger en enhetlig uppsättning övergripande API: er som bygger på data ramar som kan hjälpa dig att skapa och finjustera praktiska maskininlärning pipelines. [MLlib](http://spark.apache.org/mllib/) är Sparks skalbara machine learning-biblioteket, som ger funktioner för modellering denna distribuerad miljö.

[HDInsight Spark](../../hdinsight/hdinsight-apache-spark-overview.md) är värd för Azure-erbjudande för öppen källkod Spark. Det även inkluderar stöd för Jupyter Scala-anteckningsböcker i Spark-klustret och köra interaktiva Spark SQL-frågor för att omvandla, filtrera och visualisera data som lagras i Azure Blob storage. Scala kodavsnitten i den här artikeln som tillhandahåller lösningarna och visa relevanta områden att visualisera data körs i Jupyter-anteckningsböcker som är installerad på Spark-kluster. Modellering stegen i följande avsnitt har kod som visar hur du träna, utvärdera, spara och använda varje typ av modellen.

Konfigurationsstegen och kod i den här artikeln gäller Azure HDInsight 3.4 Spark 1.6. Men koden i den här artikeln och i den [Scala Jupyter-anteckningsbok](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) är allmänna och bör fungera på Spark-kluster. Klustret installation och hantering av stegen kanske skiljer sig från vad som anges i den här artikeln om du inte använder HDInsight Spark.

> [!NOTE]
> Ett avsnitt som visar hur du använder Python i stället för Scala utföra uppgifter för en datavetenskap slutpunkt till slutpunkt-processen, se [datavetenskap med Spark på Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Krav
* Du måste ha en Azure-prenumeration. Om du inte redan har en, [hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Du behöver ett Azure HDInsight 3.4 Spark 1.6 klustret för att slutföra följande procedurer. Om du vill skapa ett kluster, se anvisningarna i [komma igång: skapa Apache Spark på Azure HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Anger typ av kluster och versionen på den **Välj typ av kluster** menyn.

![HDInsight klusterkonfiguration för typen](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

En beskrivning av NYC taxi resa data och instruktioner om hur du kör kod från en Jupyter notebook i Spark-klustret finns i de relevanta avsnitten i [översikt av datavetenskap med Spark på Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Köra Scala kod från en Jupyter notebook i Spark-klustret
Du kan starta en Jupyter-anteckningsbok från Azure-portalen. Hitta Spark-kluster på instrumentpanelen och klickar på knappen för att ange sidan för klustret. Klicka sedan på **Klusterinstrumentpaneler**, och klicka sedan på **Jupyter-anteckningsbok** att öppna den bärbara datorn som är associerade med Spark-klustret.

![Instrumentpanelen klustret och Jupyter-anteckningsböcker](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Du kan också komma åt Jupyter-anteckningsböcker med https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Ersätt *klusternamn* med namnet på klustret. Du behöver lösenordet för ditt administratörskonto att komma åt Jupyter-anteckningsböcker.

![Gå till Jupyter-anteckningsböcker med hjälp av klustrets namn](./media/scala-walkthrough/spark-jupyter-notebook.png)

Välj **Scala** att se en mapp med några exempel på färdigförpackade bärbara datorer som använder PySpark-API. Exempel på undersökning modellering och bedömningen med Scala.ipynb bärbar dator som innehåller koden för den här sviten Spark avsnitt är tillgängliga på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Du kan ladda upp anteckningsboken direkt från GitHub på servern Jupyter Notebook i Spark-kluster. På startsidan Jupyter klickar du på den **överför** knappen. Klistra in URL-Adressen för GitHub (rådata innehåll) av Scala notebook i Utforskaren, och klicka sedan på **öppna**. Scala anteckningsboken är tillgänglig på följande URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Konfigurera: Förinställda Spark och Hive-kontexterna, Spark användbara och Spark-bibliotek
### <a name="preset-spark-and-hive-contexts"></a>Förinställningen Spark och Hive-kontexterna
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Spark-kernlar som tillhandahålls med Jupyter-anteckningsböcker har förinställda sammanhang. Du behöver inte explicit inställd på Spark eller Hive-kontexterna innan du börjar arbeta med programmet som du utvecklar. De förinställda kontexterna är:

* `sc`för SparkContext
* `sqlContext`för HiveContext

### <a name="spark-magics"></a>Spark användbara
Spark-kärnan innehåller vissa fördefinierade ”användbara”, som är särskilda kommandon som kan anropas med `%%`. Två av dessa kommandon som används i följande kodexempel.

* `%%local`Anger att koden i efterföljande rader körs lokalt. Koden måste vara en giltig Scala-kod.
* `%%sql -o <variable name>`Kör en Hive-fråga mot `sqlContext`. Om den `-o` -parameter har skickats, resultatet av frågan sparas i den `%%local` Scala kontext som en Spark data ram.

För mer information om kärnor för Jupyter-anteckningsböcker och deras fördefinierade ”magics” som du anropar med `%%` (till exempel `%%local`), se [kernlar som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importera bibliotek
Importera Spark, MLlib och andra bibliotek som du behöver genom att använda följande kod.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Datainhämtning
Det första steget i processen datavetenskap är att mata in data som du vill analysera. Data hämtas från externa källor eller system där den finns i din data från kartläggning av naturresurser och modellering miljö. I den här artikeln är de data du mata in ett domänanslutna 0,1% exempel filernas taxi resa och avgiften (som lagras som en TSV-fil). Data från kartläggning av naturresurser och modellering miljön är Spark. Det här avsnittet innehåller koden för att genomföra följande antal aktiviteter:

1. Ange katalogsökvägar för lagring av data och modell.
2. Läs i inkommande datauppsättningen (som lagras som en TSV-fil).
3. Definiera ett schema för data och rensa data.
4. Skapa en rensade data ram och cachelagra i minnet.
5. Registrera data som en tillfällig tabell i SQLContext.
6. Fråga tabellen och importera resultaten till en data-ram.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Ange katalogsökvägar för lagringsplatser i Azure Blob storage
Spark kan läsa och skriva till Azure Blob storage. Du kan använda Spark bearbeta några befintliga data och spara resultatet i Blob storage igen.

Du måste ange korrekt sökväg för att spara modeller eller filer i Blob storage. Referera standardbehållaren kopplad till Spark-kluster med hjälp av en sökväg som börjar med `wasb:///`. Referera till andra platser med hjälp av `wasb://`.

Följande kodexempel anger platsen för indata som ska läsas och sökvägen till Blob storage som är kopplad till Spark-kluster där modellen ska sparas.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importera data, skapa en RDD och definierar data enligt schemat
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Utdata:**

Tid att köra cellen: 8 sekunder.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Fråga tabellen och importera resulterar i en data-ram
Därefter fråga tabellen för avgiften, resande och tips data. Filtrera bort skadad och öar data. och Skriv ut flera rader.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Utdata:**

| fare_amount | passenger_count | tip_amount | lutad |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Datagranskning och visualisering
När data hämtas i Spark är nästa steg i processen datavetenskap att få en bättre förståelse av data via undersökning och visualisering. I det här avsnittet kan undersöka du taxi data med hjälp av SQL-frågor. Importera sedan resultaten i ett data ska ritas target-variabler och potentiella funktioner för granskning med hjälp av funktionen för automatisk visualisering av Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Använda lokala och Magiskt tal för SQL data ska ritas
Som standard är resultatet av alla kodstycke som du kör från en Jupyter-anteckningsbok tillgänglig inom ramen för sessionen som sparas på arbetsnoderna. Om du vill spara en resa till arbetsnoderna för varje beräkning och om alla data som du behöver för din beräkning är tillgängligt lokalt på noden Jupyter (vilket är huvudnoden) kan du använda den `%%local` Magiskt tal för att köra kodfragmentet på Jupyter-servern.

* **SQL-magic** (`%%sql`). HDInsight Spark-kernel stöder enkelt infogade HiveQL frågor mot SQLContext. Den (`-o VARIABLE_NAME`) argumentet kvarstår utdata från SQL-frågan som en Pandas data ram på Jupyter-servern. Det innebär att den ska vara tillgänglig i lokalt läge.
* `%%local`**magic**. Den `%%local` magic körs koden lokalt på servern Jupyter som huvudnod HDInsight-klustret. Normalt använder du `%%local` magiskt tillsammans med den `%%sql` magiskt med den `-o` parameter. Den `-o` parametern skulle spara resultatet av SQL-frågan lokalt, och sedan `%%local` magic skulle leda till en uppsättning kodstycke kör lokalt mot utdata för SQL-frågor som sparas lokalt.

### <a name="query-the-data-by-using-sql"></a>Fråga efter data med hjälp av SQL
Den här frågan hämtar taxi resor av avgiften, passagerare antal, och tips belopp.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

I följande kod i `%%local` magic skapas en lokala data, sqlResults. Du kan använda sqlResults ska ritas med hjälp av matplotlib.

> [!TIP]
> Lokala magic används flera gånger i den här artikeln. Om din datauppsättning är stor, exempel om du vill skapa en data-ram som får plats i det lokala minnet.
> 
> 

### <a name="plot-the-data"></a>Ritas
Du kan rita med hjälp av Python-kod när dataramen är i lokala kontext som en Pandas data ram.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Spark-kernel visualizes automatiskt utdata för SQL (HiveQL)-frågor när du kör kod. Du kan välja mellan flera olika typer av visualiseringar:

* Tabell
* Cirkeldiagram
* Rad
* Område
* Fältet

Här är koden för att rita data:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Utdata:**

![Tips belopp histogram](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tips belopp av passagerare antal](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tips belopp med avgiften belopp](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Skapa funktioner och transformera funktioner och Förbered dig för indata i modeling funktioner
För trädet-baserade modelleringsfunktioner från Spark ML och MLlib måste du förbereda mål och funktioner med hjälp av en mängd olika tekniker, till exempel diskretisering, indexering, en hot kodning och vectorization. Här följer du procedurerna för att följa i det här avsnittet:

1. Skapa en ny funktion av **diskretisering** timmar till trafik tid buckets.
2. Tillämpa **indexering och en hot kodning** kategoriska funktioner.
3. **Exempel och dela datauppsättningen** till bråk träning och testning.
4. **Ange variabeln utbildning och funktioner**, och sedan skapa indexerade eller ett hot kodade utbildning och testar inkommande märkt punkt flexibel distribueras datauppsättningar (RDDs) eller ramar.
5. Automatiskt **kategorisera och vectorize funktioner och mål** ska användas som indata för machine learning-modeller.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Skapa en ny funktion med diskretisering timmar till trafik tid buckets
Den här koden visar hur du skapar en ny funktion med diskretisering timmar i trafik tid buckets och cachelagra dataramen resulterande i minnet. Där RDDs och data används flera gånger, cachelagring leder till en förbättrad körningstider. Du måste därför cachelagra RDDs och dataramar i flera steg i följande procedurer.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexering och en hot kodning av kategoriska funktioner
Modellering och förutsäga funktioner för MLlib kräver funktioner med kategoriska indata till indexerade eller kodade före användning. Det här avsnittet visar hur du index eller koda kategoriska funktioner för indata i modelleringsfunktioner.

Du behöver index eller koda modeller på olika sätt beroende på modellen. Till exempel kräva logistic och linjär regression modeller ett hot kodning. Till exempel kan en funktion med tre kategorier expanderas i tre kolumner i funktionen. Varje kolumn innehåller 0 eller 1 beroende på kategorin för en observationsintervallet. MLlib ger den [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funktionen för en hot kodning. Den här kodaren mappar etikett index i en kolumn till en kolumn med binära angreppsmetoderna med högst ett ett-värde. Med den här kodningen kan algoritmer som räknar numeriska värden funktioner, till exempel logistic regression tillämpas på kategoriska funktioner.

Här kan du omvandla bara fyra variabler för att visa exempel som är strängar. Du kan också indexera andra variabler, till exempel veckodag representeras av numeriska värden som kategoriska variabler.

Använd för indexering, `StringIndexer()`, och för ett hot encoding använder `OneHotEncoder()` funktioner från MLlib. Här är koden för att indexera och koda kategoriska funktioner:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Utdata:**

Tid att köra cellen: 4 sekunder.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Exempel och dela datauppsättningen till bråk träning och testning
Den här koden skapar en slumpmässig provtagning data (i det här exemplet 25%). Även om sampling inte krävs för det här exemplet på grund av storleken på datamängden som visar artikeln hur du kan prova så att du vet hur du använder för din egen problem vid behov. När prover är stort, kan detta spara mycket tid medan du träna modeller. Dela exemplet i en utbildning (75% i det här exemplet) och en tester del (i det här exemplet 25%) för att använda i klassificering och regressionen modeling.

Lägg till ett slumptal (mellan 0 och 1) på varje rad (i en kolumn ”SLUMP”) som kan användas för att välja korsvalidering vikningar vid träning.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Utdata:**

Tid att köra cellen: 2 sekunder.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Ange utbildning variabeln och funktioner, och skapa indexerade eller ett hot kodade träning och testning indata med etiketten punkt RDDs eller data ramar
Det här avsnittet innehåller kod som visar hur du indexera kategoriska textdata med datatypen märkt punkt och koda den så att du kan använda för att träna och testa MLlib logistic regression och andra klassificering modeller. Märkt point-objekt är RDDs som är formaterad på ett sätt som krävs av de flesta maskininlärningsalgoritmer i MLlib som indata. En [etikett punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vector kompakta eller utspridd, som är associerade med en etikett/svar.

I den här koden anger du målvariabel (beroende) och funktionerna du använder för att träna modeller. Sedan kan skapa du indexerade eller ett hot kodade träning och testning indata med etiketten punkt RDDs eller data ramar.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Utdata:**

Tid att köra cellen: 4 sekunder.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Kategorisera och vectorize funktioner och mål för att användas som indata för maskininlärningsmodeller automatiskt
Använda Spark ML för att kategorisera mål- och funktioner du vill använda i trädet-baserade modelleringsfunktioner. Koden är klar två aktiviteter:

* Skapar ett binärt mål för klassificering genom att tilldela varje datapunkt mellan 0 och 1 med hjälp av ett tröskelvärde för 0,5 värdet 0 eller 1.
* Automatiskt kategoriserar funktioner. Om antalet distinkta numeriska värden för alla funktioner som är mindre än 32, kategoriseras den funktionen.

Här är koden för dessa två aktiviteter.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binär klassificering modell: förutsäga om tipsrutor ska användas för betalning
I det här avsnittet kan du skapa tre olika typer av binär klassificering modeller att förutsäga om tipsrutor ska betald:

* En **logistic regressionsmodell** med hjälp av Spark ML `LogisticRegression()` funktion
* En **slumpmässiga klassificering Skogsmodell** med hjälp av Spark ML `RandomForestClassifier()` funktion
* En **toning den trädet klassificering modellen** med hjälp av MLlib `GradientBoostedTrees()` funktion

### <a name="create-a-logistic-regression-model"></a>Skapa en logistic regressionsmodell
Skapa sedan en logistic regressionsmodell med hjälp av Spark ML `LogisticRegression()` funktion. Du kan skapa modellen skapa koden i ett antal steg:

1. **Träna modellen** data med en enda parameter.
2. **Utvärdera modellen** på en datauppsättning med test.
3. **Spara modellen** i Blob storage för framtida användning.
4. **Poängsätt modellen** mot testdata.
5. **Rita resultaten** med operativsystemet kurvor egenskap (ROC).

Här är koden för dessa procedurer:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Läsa in poängsätta och spara resultatet.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Utdata:**

ROC på testdata = 0.9827381497557599

Använda Python på lokala Pandas dataramar ska ritas ROC-kurvan.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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


**Utdata:**

![Tips eller några tips ROC-kurvan](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Skapa en modell för klassificering av slumpmässiga skog
Skapa sedan en klassificering för slumpmässiga Skogsmodell med hjälp av Spark ML `RandomForestClassifier()` fungera och sedan utvärdera modellen på testdata.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Utdata:**

ROC på testdata = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Skapa en modell för klassificering av GBT
Skapa sedan en modell för klassificering av GBT med hjälp av Mllib's `GradientBoostedTrees()` fungera och sedan utvärdera modellen på testdata.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Utdata:**

Området under ROC-kurvan: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressionsmodell: förutsäga tips belopp
I det här avsnittet kan du skapa två typer av regression modeller att förutse hur tips:

* En **reglerats linjär regressionsmodell** med hjälp av Spark ML `LinearRegression()` funktion. Du måste spara modellen och utvärdera modellen på testdata.
* En **toningen förstärkning trädet regressionsmodell** med hjälp av Spark ML `GBTRegressor()` funktion.

### <a name="create-a-regularized-linear-regression-model"></a>Skapa en reglerats linjär regressionsmodell
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Utdata:**

Tid att köra cellen: 13 sekunder.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Utdata:**

R-sqr på testdata = 0.5960320470835743

Därefter fråga testresultaten en data-ram och använda AutoVizWidget och matplotlib visualisera den.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Koden skapas en lokala data från frågeresultatet och visar data. Den `%%local` magic skapar en lokala data ram `sqlResults`, som du kan använda för att rita med matplotlib.

> [!NOTE]
> Den här Spark magic används flera gånger i den här artikeln. Om mängden data som är stor, bör du prov för att skapa en data-ram som ryms i lokalt minne.
> 
> 

Skapa områden med Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Utdata:**

![Tips belopp: faktiska kontra förväntade](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Skapa en GBT regressionsmodell
Skapa en GBT regressionsmodell med hjälp av Spark ML `GBTRegressor()` fungera och sedan utvärdera modellen på testdata.

[Ökat toningen träd](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) är ensembler av beslutsträd. GBTs träna beslutsträd upprepade gånger för att minimera dataförlust funktionen. Du kan använda GBTs för regression och klassificering. De kan hantera kategoriska funktioner kräver inte funktionen skalning och kan avbilda nonlinearities och funktionen interaktioner. Du kan också använda dem i en multiclass klassificering.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Utdata:**

Testa R-sqr är: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Avancerade modellering verktyg för optimering
I det här avsnittet använder du machine learning verktyg som utvecklare använder ofta för optimering av modellen. Mer specifikt kan du optimera machine learning-modeller tre olika sätt med hjälp av parametern omfattande och korsvalidering:

* Dela data i tåg och validering uppsättningar, optimera modellen med hjälp av hyper-parametern omfattande på en träningsmängden och utvärdera en verifiering mängd (linjär regression)
* Optimera modellen med hjälp av korsvalidering och hyper-parametern omfattande med funktionen Spark ML CrossValidator (binär klassificering)
* Optimera modellen med hjälp av anpassade korsvalidering och parametern omfattande kod för att använda alla maskininlärning funktion och parametern uppsättning (linjär regression)

**Korsvalidering** är en teknik som utvärderar hur väl en modell med en känd uppsättning data kommer generalize för att förutsäga funktioner för datauppsättningar som den inte har tränats. Allmänna syftet med den här metoden är att en modell tränas på en datauppsättning med kända data och sedan korrektheten i dess förutsägelser kontrolleras mot en oberoende datamängd. En vanlig tillämpning är att dela en datamängd i *k*-vikningar och träna modellen i ett resursallokering sätt på alla utom en av vikningar.

**Hyper-parametern optimering** är problem med att välja en uppsättning hyper-parametrar för en inlärningsalgoritm vanligtvis med målet att optimera ett mått på den algoritm prestanda på en fristående uppsättning data. Hyper-parametern är ett värde som du måste ange utanför modellen utbildning proceduren. Antaganden om hyper-parametervärden kan påverka flexibilitet och korrektheten i modellen. Beslutsträd har till exempel hyper-parametrar, till exempel önskad djup och många blad i trädet. Du måste ange en felklassificering påföljder term för en support vector machine (SVM).

Ett vanligt sätt att utföra en optimering för hyper-parametern är att använda en rutnätet sökning, kallas även en **parametern Svep**. I ett rutnät sökning utförs en fullständig sökning genom värdena för en angiven del av hyper-parametern utrymmet för en inlärningsalgoritm. Korsvalidering kan tillhandahålla ett prestanda mått för att lösa bästa möjliga resultat som genereras av algoritmen rutnätet sökning. Om du använder korsvalidering hyper parametern omfattande, kan du hjälpa gränsen problem angående overfitting en modell för utbildningsdata. Det här sättet modellen behåller kapacitet att tillämpa en allmän uppsättning data från vilket utbildning data extraheras.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimera en linjär regressionsmodell med hyper-parametern omfattande
Därefter dela data i tåg och validering uppsättningar, Använd hyper-parametern omfattande på en träningsmängden att optimera modellen och utvärdera en verifiering mängd (linjär regression).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Utdata:**

Testa R-sqr är: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimera binär klassificering modellen med hjälp av korsvalidering och hyper-parametern omfattande
Det här avsnittet visar hur du optimerar en binär klassificering modell med omfattande korsvalidering och hyper-parametern. Här används Spark ML `CrossValidator` funktion.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Utdata:**

Tid att köra cellen: 33 sekunder.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimera linjär regressionsmodell med hjälp av anpassade korsvalidering och parametern omfattande kod
Sedan optimera modellen med hjälp av anpassade kod och identifiera de lämpligaste modellparametrarna med hjälp av kriterium högsta noggrannhet. Skapa sedan sista modellen, utvärdera modellen på testdata och spara modellen i Blob storage. Slutligen läsa in modellen poängsätta testdata och utvärdera noggrannhet.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Utdata:**

Tid att köra cellen: 61 sekunder.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Använda Spark-inbyggda machine learning-modeller automatiskt med Scala
En översikt över ämnen som vägleder dig igenom de aktiviteter som utgör processen datavetenskap i Azure finns [Team datavetenskap Process](http://aka.ms/datascienceprocess).

[Teamet datavetenskap Process genomgång](walkthroughs.md) beskriver andra slutpunkt till slutpunkt-genomgång som visar stegen i teamet vetenskap av data för specifika scenarier. Genomgångar visar även hur du kombinerar molnet och lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program.

[Poängsätta Spark-inbyggda machine learning-modeller](spark-model-consumption.md) visar hur du kan använda Scala-kod för att läsa in och poängsätta nya datauppsättningar med machine learning-modeller inbyggd Spark och sparas i Azure Blob storage automatiskt. Du kan följer du det och Ersätt bara Python-kod med Scala kod i den här artikeln för automatisk användning.

