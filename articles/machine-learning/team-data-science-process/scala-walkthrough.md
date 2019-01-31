---
title: Datavetenskap med Scala och Spark på Azure - Team Data Science Process
description: Så här använder Scala för övervakade machine learning-aktiviteter med Spark skalbar MLlib och Spark ML paketen på ett Azure HDInsight Spark-kluster.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3109c4e6190cd8e485ae9b28117c4688836dfc26
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470322"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Datavetenskap med Scala och Spark på Azure
Den här artikeln visar hur du använder Scala för övervakade machine learning-aktiviteter med Spark skalbar MLlib och Spark ML paketen på ett Azure HDInsight Spark-kluster. Vi går igenom de aktiviteter som utgör den [Data Science process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): datainmatning och utforskning, visualisering, funktionsframställning, modellering och förbrukning av modellen. Modeller i artikeln är logistic och linjär regression, slumpmässiga skogar och gradient-förstärkta träd (GBTs), utöver de två vanliga övervakade machine learning-aktiviteter:

* Regressionsproblem: Förutsägelse av tips belopp ($) för en taxi-resa
* Binär klassificering: Förutsägelser av tips eller inga tips (1/0) för en taxi-resa

Modelleringsprocessen kräver träning och utvärdering av på en datauppsättning för testning och relevanta Precision mått. I den här artikeln får du lära dig hur du lagrar dessa modeller i Azure Blob storage och hur du bedöma och utvärdera deras förutsägande prestanda. Den här artikeln beskriver också mer avancerade avsnitt om hur du optimerar modeller med hjälp av oinskränkt korsvalidering och hyper-parametern. De data som används är ett exempel på 2013 NYC taxi resa och avgiften datauppsättningen finns på GitHub.

[Scala](http://www.scala-lang.org/), ett språk baserat på Java virtual machine, integrerar objektorienterad och funktionella språkkoncept. Det är ett skalbart språk som är lämplig för distribuerad databehandling i molnet och körs på Azure Spark-kluster.

[Spark](http://spark.apache.org/) är ett ramverk för parallellbearbetning av öppen källkod som stöder intern bearbetning för att höja prestandan hos program för stordata-analys. Bearbetningsmotorn i Spark är byggd för hastighet, enkel användning och avancerade analyser. Sparks InMemory-distribuerad beräkning funktioner blir det ett bra alternativ för iterativa algoritmer i machine learning och grafberäkningar. Den [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) paketet tillhandahåller en enhetlig uppsättning avancerade API: er som bygger på data ramar som kan hjälpa dig att skapa och finjustera praktiska machine learning-pipelines. [MLlib](http://spark.apache.org/mllib/) är Sparks skalbar machine learning-biblioteket, som hämtar modelleringsfunktioner till den här distribuerad miljö.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) är i Azure som värd-versionen av öppen källkod Spark. Den också har stöd för Jupyter Scala-anteckningsböcker i Spark-klustret och köra interaktiva Spark SQL-frågor för att omvandla, filtrera och visualisera data som lagras i Azure Blob storage. Scala kodfragmenten i den här artikeln som tillhandahåller lösningar och visa relevanta områden att visualisera data körs i Jupyter-anteckningsböcker som installerats på Spark-kluster. Modellering stegen i följande avsnitt har kod som visar hur du tränar, utvärdera, spara och använda varje typ av modellen.

Steg för konfiguration och kod i den här artikeln är för Azure HDInsight 3.4 Spark 1.6. Men koden i den här artikeln och i den [Scala Jupyter Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) är allmänna och bör fungera i ett Spark-kluster. Klustret installation och hantering steg kanske skiljer sig från vad som anges i den här artikeln om du inte använder HDInsight Spark.

> [!NOTE]
> En artikel som visar hur du använder Python i stället för Scala för att utföra åtgärder för en slutpunkt till slutpunkt Data Science process, se [datavetenskap med Spark på Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Förutsättningar
* Du måste ha en Azure-prenumeration. Om du inte redan har en, [få en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Du behöver ett Azure HDInsight 3.4 Spark 1.6-kluster för att slutföra följande procedurer. Om du vill skapa ett kluster, se anvisningarna i [komma igång: Skapa Apache Spark på Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Ange typ av kluster och version på den **Välj typ av kluster** menyn.

![Konfiguration av HDInsight-kluster](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

En beskrivning av NYC taxi-resedata och anvisningar om hur du kör koden från en Jupyter notebook i Spark-klustret finns i de relevanta avsnitten i [översikt över datavetenskap med Spark på Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Köra Scala kod från en Jupyter notebook i Spark-klustret
Du kan starta en Jupyter-anteckningsbok från Azure-portalen. Hitta Spark-klustret på instrumentpanelen och klicka sedan på den för att ange hanteringssidan för klustret. Klicka sedan på **Klusterinstrumentpaneler**, och klicka sedan på **Jupyter Notebook** att öppna anteckningsboken som är associerade med Spark-klustret.

![Instrumentpanelen för klustret och Jupyter notebooks](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Du kan också komma åt Jupyter-anteckningsböcker på https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Ersätt *clustername* med namnet på klustret. Du behöver lösenordet för ditt administratörskonto att få åtkomst till Jupyter notebooks.

![Gå till Jupyter-anteckningsböcker med hjälp av klustrets namn](./media/scala-walkthrough/spark-jupyter-notebook.png)

Välj **Scala** att se en katalog som är några exempel på förpaketerade anteckningsböcker som använder PySpark-API. Exempel för att utforska modellering och bedömning med Scala.ipynb bärbar dator som innehåller koden för den här programsviten Spark ämnen på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Du kan ladda upp anteckningsboken direkt från GitHub för att Jupyter Notebook-server på Spark-kluster. På startsidan Jupyter klickar du på den **överför** knappen. Klistra in GitHub (raw innehåll)-URL: en för Scala anteckningsboken i Utforskaren, och klicka sedan på **öppna**. Scala anteckningsboken är tillgänglig på följande URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Konfigurera: Förinställda kontexter för Spark och Hive, användbara funktioner Spark och Spark-bibliotek
### <a name="preset-spark-and-hive-contexts"></a>Förinställda kontexter för Spark och Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Spark-kernlar som tillhandahålls med Jupyter-anteckningsböcker har förinställda kontexter. Du behöver inte uttryckligen ställa in Spark eller Hive-kontexterna innan du börjar arbeta med programmet du utvecklar. Förinställda kontexter finns:

* `sc` för SparkContext
* `sqlContext` för HiveContext

### <a name="spark-magics"></a>Spark användbara funktioner
Spark-kernel innehåller vissa fördefinierade ”användbara”, vilket är särskilt kommandon som du kan anropa med `%%`. Två av dessa kommandon som används i följande kodexempel.

* `%%local` Anger att koden i efterföljande rader kommer att köras lokalt. Koden måste vara giltig Scala-kod.
* `%%sql -o <variable name>` Kör en Hive-frågan mot `sqlContext`. Om den `-o` parametern har gått ut, resultatet av frågan sparas i den `%%local` Scala kontext som Spark data frame.

För mer information om kärnor för Jupyter notebooks och deras fördefinierade ”magics” som du anropar med `%%` (till exempel `%%local`), se [Kernlar som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster på HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importera bibliotek
Importera Spark MLlib och andra bibliotek som du behöver med hjälp av följande kod.

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
Det första steget i Data Science process är att mata in data som du vill analysera. Du kan sätta data från externa källor eller system där det finns i miljön data utforskning och modellering. I den här artikeln är de data du matar in ett domänansluten 0,1% exempel filens taxi resa och avgiften (som lagras som en TSV-fil). Utforskning och modellering miljön data är Spark. Det här avsnittet innehåller koden för att slutföra den följande metoder:

1. Ange katalogsökvägar för lagring av data och modell.
2. Läs i den inkommande datauppsättningen (som lagras som en TSV-fil).
3. Definiera ett schema för data och rensa data.
4. Skapa en rensad dataram och cachelagra i minnet.
5. Registrera informationen som en temporär tabell SQLContext.
6. Fråga efter tabellen och importera resultaten till en dataram.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Ange katalogsökvägar för lagringsplatser i Azure Blob storage
Spark kan läsa och skriva till Azure Blob storage. Du kan använda Spark för att bearbeta några befintliga data och spara resultatet i Blob storage igen.

Du måste ange korrekt sökväg för att spara modeller eller filer i Blob storage. Referera till behållare som standard ansluts till Spark-kluster med hjälp av en sökväg som börjar med `wasb:///`. Referera till andra platser med hjälp av `wasb://`.

Följande kodexempel anger platsen för indata som ska läsas och sökvägen till Blob-lagring som är kopplad till Spark-klustret där modellen kommer att sparas.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importera data, skapa en RDD och definiera en dataram enligt schemat
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

Tiden för att köra cellen: 8 sekunder.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Fråga efter tabellen och importera resultat i en dataram
Därefter fråga tabell efter avgiften och passagerartrafik tips data. Filtrera bort skadade och mindre data. och Skriv ut flera rader.

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

## <a name="data-exploration-and-visualization"></a>Datautforskning och visualisering
När du fört dina data i Spark är nästa steg i processen för datavetenskap att få en djupare förståelse av data via utforskning och visualisering. I det här avsnittet ska undersöka du taxi-data med hjälp av SQL-frågor. Importera sedan resultaten till en dataram ska ritas target-variabler och potentiella funktioner för granskning med hjälp av funktionen automatisk visualisering av Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Använda lokala och SQL magic data ska ritas
Som standard är utdata från alla kodfragment som du kör från en Jupyter-anteckningsbok tillgänglig inom ramen för sessionen som sparas på arbetsnoderna. Om du vill spara en resa till arbetsnoderna för varje beräkning och om alla data som du behöver för din beräkning är tillgängliga lokalt på noden Jupyter (vilket är huvudnoden), kan du använda den `%%local` magic att köra kodfragmentet på Jupyter Server.

* **SQL magic** (`%%sql`). HDInsight Spark-kernel har stöd för enkel infogade HiveQL frågor mot SQLContext. Den (`-o VARIABLE_NAME`) argumentet kvarstår utdata från SQL-frågan som en Pandas dataram på Jupyter-servern. Det innebär att den blir tillgänglig i lokalt läge.
* `%%local` **Magic**. Den `%%local` magic körs koden lokalt på servern och Jupyter, som är huvudnoden på HDInsight-klustret. Normalt använder du `%%local` magic tillsammans med den `%%sql` magic med den `-o` parametern. Den `-o` parametern skulle spara utdata av SQL-frågan lokalt, och sedan `%%local` magic ska utlösa nästa uppsättning kodfragmentet att köra lokalt mot utdata från SQL-frågor som är sparade lokalt.

### <a name="query-the-data-by-using-sql"></a>Fråga efter data med hjälp av SQL
Den här frågan hämtar taxi-kommunikation med avgiften, passagerartrafik antal, och tips belopp.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

I följande kod i `%%local` magic skapar en lokal dataram sqlResults. Du kan använda sqlResults ska ritas med hjälp av matplotlib.

> [!TIP]
> Lokala magic används flera gånger i den här artikeln. Om datauppsättningen är stor, exempel för att skapa en dataram som ryms i lokalt minne.
> 
> 

### <a name="plot-the-data"></a>Ritas
Du kan rita med hjälp av Python-kod när dataramen är i lokal kontext som en dataram Pandas.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Spark-kernel visualiserar resultatet av SQL (HiveQL)-frågor automatiskt när du kör koden. Du kan välja mellan flera typer av visualiseringar:

* Tabell
* Cirkel
* Rad
* Område
* Stapel

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

![Tips belopp efter passagerar-antal](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tips belopp med avgiften belopp](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Skapa funktioner och omvandla funktioner och Förbered sedan data för mata in modellering funktioner
För trädet-baserade modelleringsfunktioner från Spark ML och MLlib måste du förbereda mål- och funktioner genom att använda en mängd olika tekniker, till exempel datagruppering, indexering, en frekvent kodning och vectorization. Här följer procedurerna som ska följas i det här avsnittet:

1. Skapa en ny funktion av **datagruppering** timmar trafik tid buckets.
2. Tillämpa **indexering och en frekvent kodning** till kategoriska funktioner.
3. **Hämta exempel och dela upp datauppsättningen** i bråkdelar för träning och testning.
4. **Ange variabel för utbildning och funktioner**, och sedan skapa indexerade eller en frekvent kodad utbildning och testning inkommande taggade punkt elastiska distribueras Rdd-datauppsättningar () eller dataramar.
5. Automatiskt **kategorisera och vectorize funktioner och mål** ska användas som indata för machine learning-modeller.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Skapa en ny funktion med datagrupperingen timmar till trafik tid buckets
Den här koden visar hur du skapar en ny funktion med datagrupperingen timmar till trafik tid buckets och cachelagra den resulterande dataramen i minnet. Där rdd-datauppsättningar och data används flera gånger, cachelagring leder till bättre körningstider. Därför ska du Cachelagra rdd-datauppsättningar och dataramar i flera led i följande procedurer.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexering och hot som kodning av kategoriska funktioner
Modellering och förutsäga funktioner för MLlib kräver funktioner med kategoriska indata till indexerade eller kodade före användning. Det här avsnittet visar hur du indexera eller koda kategoriska funktioner för mata in modelleringsfunktioner.

Du behöver index eller koda dina modeller på olika sätt, beroende på modellen. Till exempel kräva logistic och linjära regressionsmodeller en frekvent kodning. Till exempel kan en funktion med tre kategorier expanderas till tre kolumner i funktionen. Varje kolumn innehåller 0 eller 1 beroende på en uppmaning i kategorin. MLlib ger den [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funktionen för en frekvent encoding. Den här encoder mappar en kolumn med etiketten index till en kolumn med binära vektorer med högst ett ett-värde. Med den här kodningen kan algoritmer som förväntar sig numeriska värden funktioner, till exempel logistic regression tillämpas på kategoriska funktioner.

Här kan du omvandla bara fyra variabler för att visa exempel som är teckensträngar. Du kan också indexera andra variabler, till exempel veckodag och representeras av numeriska värden som kategoriska variabler.

Använd för indexering, `StringIndexer()`, och för en frekvent encoding, använda `OneHotEncoder()` funktioner från MLlib. Här är koden för att indexera och koda kategoriska funktioner:

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

Tiden för att köra cellen: 4 sekunder.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Exempel och dela datauppsättningen i bråkdelar för träning och testning
Den här koden skapar ett slumpmässigt urval av data (i det här exemplet 25%). Även om sampling inte krävs för det här exemplet på grund av storleken på datauppsättningen, visar artikeln hur du kan ta prov så att du vet hur du använder det egna problem när det behövs. När exempel är stort, kan detta spara mycket tid medan du träna modeller. Dela exemplet till en utbildning (75% i det här exemplet) och en testning delen (i det här exemplet 25%) för att använda i klassificering och regression modellering.

Lägg till ett slumptal (mellan 0 och 1) i varje rad (i en kolumn för ”rand”) som kan användas för att välja korsvalidering vikningar vid träning.

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

Tiden för att köra cellen: 2 sekunder.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Ange variabel för utbildning och funktioner och sedan skapa indexerade eller en frekvent kodad träning och testning indata märkta punkt Rdd eller data bildrutor
Det här avsnittet innehåller kod som visar hur du indexera kategoriska textdata som datatypen taggade punkt och koda den så att du kan använda den för att träna och testa MLlib logistic regression och andra klassificering modeller. Taggade point-objekt är rdd-datauppsättningar som är formaterad på ett sätt som krävs som indata av de flesta av machine learning-algoritmer i MLlib. En [märkta punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vector kompakta eller null-optimerade, som är associerad med en etikett/svar.

I den här koden anger du en målvariabel (beroende) och vilka funktioner som ska använda för att träna modeller. Sedan kan skapa du indexerade eller en frekvent kodad träning och testning indata märkta punkt Rdd-och data.

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

Tiden för att köra cellen: 4 sekunder.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatiskt kategorisera och vectorize mål ska användas som indata för machine learning-modeller och funktioner
Använd Spark ML för att kategorisera mål- och vilka funktioner du vill använda i trädet-baserade modelleringsfunktioner. Koden utför två aktiviteter:

* Skapar ett binärt mål för klassificering genom att tilldela ett värde på 0 eller 1 till varje datapunkt mellan 0 och 1 med hjälp av ett tröskelvärde för 0,5.
* Automatiskt kategoriserar funktioner. Om antalet distinkta numeriska värden för alla funktioner som är mindre än 32 kan kategoriseras den funktionen.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binär klassificeringsmodell: Förutsäga om ett tips ska betalas
I det här avsnittet skapar du tre typer av binär klassificering modeller för att förutsäga om ett tips ska betalas:

* En **logistic regression-modellen** med hjälp av Spark ML `LogisticRegression()` funktion
* En **slumpmässiga klassificering Skogsmodell** med hjälp av Spark ML `RandomForestClassifier()` funktion
* En **gradient boosting trädet klassificeringsmodellen** med hjälp av MLlib `GradientBoostedTrees()` funktion

### <a name="create-a-logistic-regression-model"></a>Skapa en logistic regression-modell
Därefter skapa en logistic regression-modellen med hjälp av Spark ML `LogisticRegression()` funktion. Du skapar modellen att skapa koden i en serie steg:

1. **Träna modellen** data med en parameteruppsättning.
2. **Utvärdera modellen** på en datauppsättning för testning med mått.
3. **Spara modellen** i Blob storage för framtida användning.
4. **Poängsätt modellen** mot testdata.
5. **Rita resultaten** med mottagare fungerar egenskap (ROC) kurvor.

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

Läsa in poäng och spara resultaten.

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

![Tips eller inga ROC-kurvan för tips](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Skapa en klassificering för slumpmässiga Skogsmodell
Skapa sedan en klassificering för slumpmässiga Skogsmodell med hjälp av Spark ML `RandomForestClassifier()` fungera, och sedan utvärdera modellen på testdata.

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
Skapa sedan en modell för klassificering av GBT med hjälp av Mllib's `GradientBoostedTrees()` fungera, och sedan utvärdera modellen på testdata.

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

## <a name="regression-model-predict-tip-amount"></a>Regression-modellen: Förutsäga tips belopp
I det här avsnittet skapar du två typer av regressionsmodeller att förutsäga hur tips:

* En **reglerats linjär regressionsmodell** med hjälp av Spark ML `LinearRegression()` funktion. Du spara modellen och utvärdera modellen på testdata.
* En **gradient-boosting trädet regressionsmodell** med hjälp av Spark ML `GBTRegressor()` funktion.

### <a name="create-a-regularized-linear-regression-model"></a>Skapa en reglerats linjär regression-modell
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

Tiden för att köra cellen: 13 sekunder.

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

Sedan fråga testresultaten som en dataram och Använd AutoVizWidget och matplotlib för att visualisera den.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Koden skapar en lokal dataram från frågeresultatet visas och visar data. Den `%%local` magic skapar en lokal dataram `sqlResults`, som du kan använda för att rita med matplotlib.

> [!NOTE]
> Den här Spark magic används flera gånger i den här artikeln. Om mängden data som är stor, bör du ta prov för att skapa en dataram som ryms i lokalt minne.
> 
> 

Skapa områden med hjälp av Python matplotlib.

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

![Tips belopp: Faktiska intäkter jämfört med förväntade](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Skapa en regressionsmodell för GBT
Skapa en GBT regressionsmodell med hjälp av Spark ML `GBTRegressor()` fungera, och sedan utvärdera modellen på testdata.

[Toning förstärkta träd](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) är ensembler för beslutsträd. GBTs träna beslutsträd upprepade gånger för att minimera en förlust-funktion. Du kan använda GBTs för regression och klassificering. De kan hantera kategoriska funktioner kräver funktionen skalning och kan avbilda nonlinearities och funktionen interaktioner. Du kan också använda dem i en inställning för multiclass-klassificering.

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

## <a name="advanced-modeling-utilities-for-optimization"></a>Verktyg för avancerad modellering för optimering
I det här avsnittet använder du machine learning-verktyg som utvecklare använder ofta för optimering av modellen. Mer specifikt kan du optimera machine learning-modeller tre olika sätt med hjälp av oinskränkt parametern och korsvalidering:

* Dela upp data i träna och verifiering, optimera modellen med hjälp av oinskränkt hyper-parametern på en uppsättning med utbildning och utvärdera på en uppsättning med verifiering (linjär regression)
* Optimera modellen med hjälp av korsvalidering och hyper-parametern oinskränkt med hjälp av Spark ML CrossValidator funktion (binär klassificering)
* Optimera modellen genom att använda anpassad kod för korsvalidering och parametern-oinskränkt för att använda alla machine learning-funktion och parametern set (linjär regression)

**Korsvalidering** är en teknik som bedömer hur väl en modell som har tränats på en känd uppsättning data kommer generalize för att förutsäga funktionerna för datauppsättningar som den inte har tränats. Den allmänna idén bakom den här tekniken är att en modell som har tränats på en uppsättning av kända data och sedan korrektheten i dess förutsägelser har testats mot en oberoende datauppsättning. En vanlig tillämpning är att dela upp en datauppsättning i *k*-vikningar och sedan träna modellen i en resursallokering på alla utom ett av vikningar.

**Hyper-parametern optimering** är problemet för att välja en uppsättning hyperparametrar för en inlärningsalgoritm vanligtvis med målet att optimera ett mått på den algoritmen prestanda på en oberoende datauppsättning. En hyper-parameter är ett värde som du måste ange utanför modellen utbildning proceduren. Antaganden om hyper-parametervärden kan påverka flexibilitet och korrektheten i modellen. Beslutsträd har till exempel hyperparametrar, till exempel önskad djup och många blad i trädet. Du måste ange en felklassificering särskilda avgifter term för en dator för vektorstöd (SVM).

Ett vanligt sätt att utföra hyper parametern optimering är att använda en grid-sökning, kallas även en **parameterrensning**. I en grid-sökning utförs en uttömmande sökning efter via värdena för en angiven delmängd av hyper-parametern utrymme för en inlärningsalgoritm. Korsvalidering kan ange ett prestandamått för att lösa de optimala resultat som skapas av algoritmen grid sökning. Om du använder korsvalidering hyper parametern oinskränkt kan hjälpa du gränsen problem angående overfitting en modell på träningsdata. På så sätt kan modellen behåller kapacitet att gäller för allmän uppsättning data från vilken träningsdata har extraherats.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimera en linjär regressionsmodell med hyper-parametern omfattande
Dela sedan data till uppsättningar av träna upp och verifiering, Använd hyper-parametern oinskränkt på en träningsmängden att optimera modellen och utvärdera på en uppsättning med verifiering (linjär regression).

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

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimera binära klassificeringsmodellen med hjälp av oinskränkt korsvalidering och hyper-parameter
Det här avsnittet visar hur du optimerar en binär klassificeringsmodell med hjälp av oinskränkt korsvalidering och hyper-parametern. Här används Spark ML `CrossValidator` funktion.

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

Tiden för att köra cellen: 33 sekunder.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimera den linjära regressionsmodellen genom att använda anpassad kod för korsvalidering och parametern-oinskränkt
Sedan optimera modellen genom att använda anpassad kod och identifiera de lämpligaste parametrarna för modellen med hjälp av villkoret för bästa noggrannheten. Skapa sedan den slutliga modellen, utvärdera modellen på testdata och spara modellen i Blob storage. Slutligen kan läsa in modellen, bedöma testdata och utvärdera precision.

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

Tiden för att köra cellen: 61 sekunder.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Använda Spark-byggda machine learning-modeller automatiskt med Scala
En översikt över avsnitt som vägleder dig genom de uppgifter som utgör Data Science process i Azure finns i [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

[Team Data Science Process genomgångar](walkthroughs.md) beskriver andra slutpunkt till slutpunkt genomgång som visar stegen i Team Data Science Process för specifika scenarier. Genomgångar visar också hur du kombinera molnet och lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program.

[Rangordna Spark-byggda machine learning-modeller](spark-model-consumption.md) visar hur du använder Scala code för att läsa in automatiskt och poängsätta nya uppsättningar med machine learning-modeller som skapats i Spark och sparade i Azure Blob storage. Du kan följa anvisningarna det och Ersätt helt enkelt Python-kod med Scala kod i den här artikeln för automatisk användning.

