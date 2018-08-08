---
title: Skapa ett Apache Spark-machine learning-pipeline – Azure HDInsight
description: Använda Apache Spark machine learning-biblioteket för att skapa datapipelines.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: eb7959255a0b3c1597592eaae41d83dabd333d05
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617062"
---
# <a name="create-a-spark-machine-learning-pipeline"></a>Skapa en Spark-maskininlärningspipeline

Apache Spark skalbar machine learning-biblioteket (MLlib) ger funktioner för modellering till en distribuerad miljö. Spark-paket [ `spark.ml` ](http://spark.apache.org/docs/latest/ml-pipeline.html) är en uppsättning avancerade API: er som bygger på dataramar. Dessa API: er för att skapa och finjustera praktiska machine learning pipelines.  *Spark-maskininlärning* refererar till den här MLlib DataFrame-baserat API inte äldre RDD-baserade pipelinen API.

Machine learning-pipeline (ML) är en fullständig arbetsflöde kombinera flera machine learning-algoritmer tillsammans. Det kan finnas många steg som krävs för att bearbeta och lära sig från data, som kräver en sekvens av algoritmer. Pipelines definiera stegen och Standardordning för machine learning-processen. I MLlib representeras faser i en pipeline av en viss sekvens av PipelineStages, där en omvandlare och en kostnadsuppskattning uppgifter.

En omvandlare är en algoritm som omvandlar en DataFrame till en annan med hjälp av den `transform()` metoden. En funktion omvandlare kan till exempel läsa en kolumn med en dataram, mappa den till en annan kolumn och utdata en ny DataFrame med mappade kolumnen som lagts till den.

En kostnadsuppskattning är en abstraktion av algoritmer och ansvarar för att passa eller utbildning på en datauppsättning för att skapa en Transformer. En kostnadsuppskattning implementerar en metod med namnet `fit()`, som accepterar en dataram och producerar en dataram som är en Transformer.

Varje tillståndslösa instans av en omvandlare eller en kostnadsuppskattning har sin egen unika identifierare som används när du anger parametrar. Båda använder ett enhetligt API för att ange dessa parametrar.

## <a name="pipeline-example"></a>Pipeline-exempel

För att demonstrera en praktisk användning av en ML-pipeline, det här exemplet använder exemplet `HVAC.csv` datafil som är förinstallerade på standardlagringen för ditt HDInsight-kluster, antingen Azure Storage eller Data Lake Store. Om du vill visa innehållet i filen, navigera till den `/HdiSamples/HdiSamples/SensorSampleData/hvac` directory. `HVAC.csv` innehåller en uppsättning med både mål- och faktiska temperaturer för HVAC (*uppvärmning, ventilation och luftkonditionering*) system i olika byggnader. Målet är att träna modellen på data och skapa en prognos temperaturen för en viss byggnad.

Följande kod:

1. Definierar en `LabeledDocument`, som lagrar den `BuildingID`, `SystemInfo` (ett system-ID och ålder) och en `label` (1.0 om byggnaden är för varmt 0,0 på annat sätt).
2. Skapar en anpassad parser funktion `parseDocument` som tar en rad (rad) och avgör om byggnaden är ”heta” genom att jämföra target temperaturen till den faktiska temperaturen.
3. Gäller parsern vid extrahering av källdata.
4. Skapar träningsdata.

```python
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
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Denna exempel-pipeline har tre steg: `Tokenizer` och `HashingTF` (båda transformatorer), och `Logistic Regression` (en kostnadsuppskattning).  Extraherade och tolkade data i den `training` DataFrame flödar genom pipelinen när `pipeline.fit(training)` anropas.

1. Det första steget `Tokenizer`, delar upp de `SystemInfo` indatakolumnen (som består av system-ID och ålder värden) i en `words` utdatakolumnen. Den här nya `words` kolumnen läggs till i DataFrame. 
2. Det andra steget `HashingTF`, konverterar den nya `words` kolumnen till funktionen vektorer. Den här nya `features` kolumnen läggs till i DataFrame. Dessa två första stegen är transformatorer. 
3. Den tredje fasen `LogisticRegression`, är en kostnadsuppskattning och så pipelinen anropar den `LogisticRegression.fit()` metod för att skapa en `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Se den nya `words` och `features` kolumner som läggs till av den `Tokenizer` och `HashingTF` transformatorer och ett exempel på den `LogisticRegression` kostnadsuppskattning, kör en `PipelineModel.transform()` metoden på den ursprungliga dataramen. I produktionskoden är nästa steg att skicka in ett test DataFrame att validera utbildningen.

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

Den `model` objekt kan nu användas för att göra förutsägelser. Det fullständiga exemplet i den här machine learning-program, samt stegvisa instruktioner för att köra det se [skapa Apache Spark machine learning-program på Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Se också

* [Datavetenskap med Scala och Spark på Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
