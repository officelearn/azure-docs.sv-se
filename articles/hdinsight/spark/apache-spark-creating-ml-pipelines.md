---
title: Skapa Apache Spark-pipeline för maskininlärning – Azure HDInsight
description: Använd Apache Spark-maskininlärningsbiblioteket för att skapa datapipelmän i Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: b0de9103fd022dc74e7c75017a602eb6701686fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494673"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Skapa en Apache Spark-maskininlärningspipeline

Apache Sparks skalbara maskininlärningsbibliotek (MLlib) ger modelleringsfunktioner till en distribuerad miljö. Spark-paketet [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) är en uppsättning API:er på hög nivå som bygger på DataFrames. Dessa API:er hjälper dig att skapa och justera praktiska machine learning-pipelines.  *Spark machine learning* refererar till det här MLlib DataFrame-baserade API:et, inte det äldre RDD-baserade pipeline-API:et.

En pipeline för maskininlärning (ML) är ett komplett arbetsflöde som kombinerar flera maskininlärningsalgoritmer tillsammans. Det kan finnas många steg som krävs för att bearbeta och lära av data, vilket kräver en sekvens av algoritmer. Pipelines definierar stadier och ordning på en maskininlärningsprocess. I MLlib representeras stadier av en pipeline av en specifik sekvens av PipelineStages, där en transformator och en estimator utför alla uppgifter.

En transformator är en algoritm som omvandlar `transform()` en DataFrame till en annan med hjälp av metoden. En funktionstransformator kan till exempel läsa en kolumn i en DataFrame, mappa den till en annan kolumn och mata ut en ny DataFrame med den mappade kolumnen bifogad till den.

En estimator är en abstraktion av inlärningsalgoritmer och ansvarar för montering eller utbildning på en datauppsättning för att producera en transformator. En estimator implementerar `fit()`en metod med namnet , som accepterar en DataFrame och producerar en DataFrame, som är en transformator.

Varje tillståndslös instans av en transformator eller en estimator har sin egen unika identifierare, som används när parametrar anges. Båda använder ett enhetligt API för att ange dessa parametrar.

## <a name="pipeline-example"></a>Exempel på pipeline

För att visa en praktisk användning av en `HVAC.csv` ML-pipeline använder det här exemplet exempeldatafilen som förinstallerats på standardlagringen för HDInsight-klustret, antingen Azure Storage eller Data Lake Storage. Om du vill visa innehållet i `/HdiSamples/HdiSamples/SensorSampleData/hvac` filen navigerar du till katalogen. `HVAC.csv`innehåller en uppsättning tider med både mål och faktiska temperaturer för VVS *(värme, ventilation och luftkonditionering)* system i olika byggnader. Målet är att träna modellen på data, och producera en prognostemperatur för en viss byggnad.

Följande kod:

1. Definierar `LabeledDocument`en , `BuildingID`som `SystemInfo` lagrar , (ett systems identifierare `label` och ålder) och en (1.0 om byggnaden är för varm, 0.0 annars).
2. Skapar en anpassad tolkfunktion `parseDocument` som tar en rad (rad) med data och avgör om byggnaden är "varm" genom att jämföra måltemperaturen med den faktiska temperaturen.
3. Använder tolken när källdata extraheras.
4. Skapar träningsdata.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument


def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)


# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Det här exemplet pipeline `Tokenizer` `HashingTF` har tre steg: `Logistic Regression` och (både Transformers), och (en estimator).  Extraherade och tolkade data `training` i DataFrame flödar `pipeline.fit(training)` via pipelinen när anropas.

1. Det första `Tokenizer`steget, delar `SystemInfo` indatakolumnen (som består av systemidentifieraren och åldersvärdena) i en `words` utdatakolumn. Den `words` här nya kolumnen läggs till i DataFrame. 
2. Det andra `HashingTF`steget, konverterar `words` den nya kolumnen till funktionsvektorer. Den `features` här nya kolumnen läggs till i DataFrame. Dessa första två arrangerar är Transformers. 
3. Den tredje `LogisticRegression`etappen, är en estimator, och `LogisticRegression.fit()` så rörledningen anropar metoden för att producera en `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Om du `words` vill `features` se de `Tokenizer` `HashingTF` nya kolumnerna som lagts `LogisticRegression` till av transformatorerna och ett exempel på kalkylatorn kör du en `PipelineModel.transform()` metod på den ursprungliga DataFrame. I produktionskod är nästa steg att passera i en testdataram för att validera utbildningen.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

Objektet `model` kan nu användas för att göra förutsägelser. Det fullständiga exemplet på det här maskininlärningsprogrammet och steg-för-steg-instruktioner för att köra det finns i [Skapa Apache Spark-maskininlärningsprogram på Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Se även

* [Data science med Scala och Apache Spark på Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
