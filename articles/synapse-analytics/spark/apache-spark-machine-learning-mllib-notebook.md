---
title: Skapa en maskininlärningsapp med Apache Spark MLlib och Azure Synapse Analytics
description: Lär dig hur du använder Apache Spark MLlib för att skapa en maskininlärningsapp som analyserar en datauppsättning med hjälp av klassificering genom logistisk regression.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430011"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Skapa en maskininlärningsapp med Apache Spark MLlib och Azure Synapse Analytics

I den här artikeln får du lära dig hur du använder Apache Spark [MLlib](https://spark.apache.org/mllib/) för att skapa ett maskininlärningsprogram som gör enkel prediktiv analys på en Azure-öppen datauppsättning. Spark tillhandahåller inbyggda maskininlärningsbibliotek. I det här exemplet används *klassificering* genom logistisk regression.

MLlib är ett spark-huvudbibliotek som tillhandahåller många verktyg som är användbara för maskininlärningsuppgifter, inklusive verktyg som är lämpliga för:

- Klassificering
- Regression
- Klustring
- Ämnesmodellering
- Singularvärdesdekomposit (SVD) och huvudkomponentanalys (PCA)
- Hypotestning och beräkning av exempelstatistik

## <a name="understand-classification-and-logistic-regression"></a>Förstå klassificering och logistisk regression

*Klassificering*, en populär maskininlärningsuppgift, är processen att sortera indata i kategorier. Det är jobbet för en klassificeringsalgoritm för att ta reda på hur du tilldelar *etiketter* till indata som du anger. Du kan till exempel tänka dig en maskininlärningsalgoritm som accepterar lagerinformation som indata och delar upp lagret i två kategorier: aktier som du bör sälja och lager som du bör behålla.

*Logistisk regression* är en algoritm som du kan använda för klassificering. Sparks logistiska regressions-API är användbart för *binär klassificering*eller klassificering av indata i en av två grupper. Mer information om logistiska regressioner finns i [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Sammanfattningsvis ger processen för logistisk regression en *logistisk funktion* som kan användas för att förutsäga sannolikheten för att en indatavektor hör hemma i en eller annan grupp.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Exempel på prediktiv analys på NYC Taxi-data

I det här exemplet använder du Spark för att utföra vissa prediktiva analyser på tipsdata för taxiresor från New York. Data är tillgängliga via [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Den här delmängden av datauppsättningen innehåller information om gula taxiresor, inklusive information om varje resa, start- och sluttid och platser, kostnaden och andra intressanta attribut.

> [!IMPORTANT]
> Det kan finnas ytterligare avgifter för att hämta dessa data från dess lagringsplats.

I följande steg utvecklar du en modell för att förutsäga om en viss resa innehåller ett tips eller inte.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Skapa en Apache Spark MLlib-maskininlärningsapp

1. Skapa en anteckningsbok med PySpark-kärnan. Instruktioner finns i [Skapa en anteckningsbok](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook).
2. Importera de typer som krävs för det här programmet. Kopiera och klistra in följande kod i en tom cell och tryck sedan på **SKIFT + RETUR**eller kör cellen med hjälp av den blå uppspelningsikonen till vänster om koden.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    På grund av PySpark-kärnan behöver du inte skapa några sammanhang uttryckligen. Spark-kontexten skapas automatiskt åt dig när du kör den första kodcellen.

## <a name="construct-the-input-dataframe"></a>Konstruera indataramen

Eftersom rådata är i ett parkettformat kan du använda Spark-kontexten för att hämta filen till minnet som en dataram direkt. Medan koden nedan använder standardalternativen, är det möjligt att tvinga mappning av datatyper och andra schemaattribut om det behövs.

1. Kör följande rader för att skapa en Spark-dataram genom att klistra in koden i en ny cell. Det första avsnittet tilldelar Azure-lagringsåtkomstinformation till variabler. Det andra avsnittet gör att Spark kan läsa från blob-lagring på distans. Den sista raden av kod läser parkett, men inga data laddas på denna punkt.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Om du drar alla dessa data genereras cirka 1,5 miljarder rader. Beroende på storleken på Spark-poolen (förhandsgranskning) kan rådata vara för stora eller ta för lång tid att använda. Du kan filtrera ned dessa data till något mindre. Om det behövs lägger du till följande rader för att filtrera ned data till cirka 2 miljoner rader för en mer responsiv upplevelse. Använd dessa parametrar för att hämta en veckas data.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Nackdelen med enkel filtrering är att, ur ett statistiskt perspektiv, kan det införa partiskhet i data. En annan metod är att använda provtagningen inbyggd i Spark. Följande kod minskar datauppsättningen ner till cirka 2000 rader, om den tillämpas efter koden ovan. Detta samplingssteg kan användas i stället för det enkla filtret eller tillsammans med det enkla filtret.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Det är nu möjligt att titta på data för att se vad som lästes. Det är normalt bättre att granska data med en delmängd i stället för den fullständiga uppsättningen beroende på datauppsättningens storlek. Följande kod erbjuder två sätt att visa data: den förra är grundläggande och den senare ger en mycket rikare rutnätsupplevelse, samt möjlighet att visualisera data grafiskt.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. Beroende på storleken på den datamängdsstorlek som genereras och ditt behov av att experimentera eller köra anteckningsboken många gånger, kan det vara lämpligt att cachelagra datauppsättningen lokalt på arbetsytan. Det finns tre sätt att utföra explicit cachelagring:

   - Spara dataramen lokalt som en fil
   - Spara dataramen som en tillfällig tabell eller vy
   - Spara dataramen som en permanent tabell

De två första av dessa metoder ingår i följande kodexempel.

När du skapar en temporär tabell eller vy får du olika åtkomstsökvägar till data, men bara varar under Spark-förekomstsessionen.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Förstå data

Normalt skulle du gå igenom en fas av *undersökande dataanalys* (EDA) på denna punkt för att utveckla en förståelse av data. Följande kod visar tre olika visualiseringar av data relaterade till tips som leder till slutsatser om datas tillstånd och kvalitet.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Histogram](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![Box Whisker Plot](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![Scatter Plot](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Förbereda data

Datan i dess råa bildar är vanligt inte passande för att passera direkt till en modellera. En serie åtgärder måste utföras på data för att få den i ett tillstånd där modellen kan använda den.

I koden under utförs fyra klasser av åtgärder:

- Borttagning av extremvärden/felaktiga värden genom filtrering.
- Borttagning av kolumner, som inte behövs.
- Skapandet av nya kolumner som härleds från rådata för att få modellen att fungera mer effektivt, ibland kallad featurization.
- Märkning, som du genomför binär klassificering (kommer det att finnas ett tips eller inte på en viss resa) det finns ett behov av att konvertera tipsbeloppet till ett 0 eller 1 värde.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Ett andra pass görs sedan över data för att lägga till de slutliga funktionerna.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Skapa en modell för logistisk regression

Den sista uppgiften är att konvertera de märkta data till ett format som kan analyseras genom logistisk regression. Indata till en logistisk regressionsalgoritm måste vara en uppsättning *vektorpar för etikett-funktionen*, där *funktionsvektorn* är en vektor med tal som representerar indatapunkten. Så vi måste konvertera de kategoriska kolumnerna till siffror. `trafficTimeBins` Kolumnerna `weekdayString` och måste konverteras till heltalsrepresentationer. Det finns flera metoder för att utföra konverteringen, men den strategi som används i det här exemplet är *OneHotEncoding*, en gemensam strategi.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Detta resulterar i en ny dataram med alla kolumner i rätt format för att träna en modell.

## <a name="train-a-logistic-regression-model"></a>Träna en logistisk regressionsmodell

Den första uppgiften är att dela upp datauppsättningen i en utbildningsuppsättning och en test- eller valideringsuppsättning. Uppdelningen här är godtycklig och du bör leka med olika delade inställningar för att se om de påverkar modellen.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Nu när det finns två DataFrames är nästa uppgift att skapa modellformeln och köra den mot träningsdataramen och sedan validera mot testdataramen. Du bör experimentera med olika versioner av modellformeln för att se effekten av olika kombinationer.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

Utdata från den här cellen är

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Skapa en visuell representation av förutsägelsen

Du kan nu skapa en slutlig visualisering som hjälper dig att resonera om resultatet av det här testet. En [ROC-kurva](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) är ett sätt att granska resultatet.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![ROC-kurva för logistisk regressionstipsmodell](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "ROC-kurva för logistisk regressionstipsmodell")

## <a name="shut-down-the-spark-instance"></a>Stäng av Spark-instansen

När du har kört programmet stänger du av anteckningsboken för att frigöra resurserna genom att stänga fliken eller välja **Avsluta session** på statuspanelen längst ned i anteckningsboken.

## <a name="see-also"></a>Se även

- [Översikt: Apache Spark på Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Nästa steg

- [.NET för Apache Spark-dokumentation](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark officiell dokumentation](https://spark.apache.org/docs/latest/)

>[!NOTE]
> En del av den officiella Apache Spark-dokumentationen är beroende av att använda Spark-konsolen, som inte är tillgänglig på Azure Synapse Spark. Använd [anteckningsboken](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) eller [IntelliJ-upplevelserna](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) i stället.
