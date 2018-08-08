---
title: Exempel på Machine learning med Spark MLlib i HDInsight - Azure
description: Lär dig hur du använder Spark MLlib för att skapa en machine learning-app som analyserar en datauppsättning med hjälp av klassificering via logistic regression.
keywords: maskininlärning för Spark, spark machine learning-exempel
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jasonh
ms.openlocfilehash: 068d5ee7200f9597da11c0a2850ef4941f4ea900
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619540"
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Använd Spark MLlib att skapa en machine learning-program och analysera en datauppsättning

Lär dig hur du använder Spark [MLlib](https://spark.apache.org/mllib/) att skapa en machine learning-programmet utför enkla förutsägande analys på en öppen datauppsättning. Från Sparks inbyggda machine learning-bibliotek, det här exemplet används *klassificering* via logistic regression. 

> [!TIP]
> Det här exemplet är också tillgängligt som en Jupyter-anteckningsbok i ett kluster med Spark (Linux) som du skapar i HDInsight. Notebook-upplevelsen kan du köra Python-kodavsnitt från anteckningsboken själva. Om du vill följa vägledningen i en bärbar dator, skapa ett Spark-kluster och starta en Jupyter-anteckningsbok (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Kör sedan anteckningsboken **Spark Machine Learning – förutsägande analys mat inspektion data med hjälp av MLlib.ipynb** under den **Python** mapp.
>
>

MLlib är en Spark-Kärnbibliotek som innehåller många verktyg som är användbara för machine learning-aktiviteter, inklusive verktyg som är lämpliga för:

* Klassificering
* Regression
* Klustring
* Avsnittet modellering
* Rapportanvändare värdet uppdelning (SVD) och huvudnamn komponenten analys (PCA)
* Hypotesen testning och beräkna exempel statistik

## <a name="understand-classification-and-logistic-regression"></a>Förstå klassificering och logistic regression
*Klassificering*, populära machine learning-aktivitet, är processen att sortera indata i kategorier. Det är en uppgift för en klassificeringsalgoritm att ta reda på hur du tilldelar ”etiketter” för att mata in data som du anger. Exempel: du kan tänka på en maskininlärningsalgoritm som tar emot lagerartiklar information som indata och delar upp lagret i två kategorier: som du bör säljer och lagren bör du tänka.

Logistic regression är den algoritm som du använder för klassificering. Spark's logistic regression API är användbart för *binär klassificering*, eller klassificera indata till något av två grupper. Läs mer om logistic regression [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Sammanfattningsvis processen för logistic regression producerar en *logistic funktionen* som kan användas för att förutse sannolikheten att en inkommande vektor tillhör en grupp eller den andra.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Förutsägande analys exempel på mat inspektion data
I det här exemplet använder du Spark för att utföra vissa förutsägande analys på mat inspektion data (**Food_Inspections1.csv**) som har köpts via den [stad Chicago dataportalen](https://data.cityofchicago.org/). Den här datauppsättningen innehåller information om mat upprätta inspektioner som har utförts i Chicago, inklusive information om varje anläggning, överträdelser som finns (om sådan finns) och resultatet av kontrollen. CSV-datafilen finns redan i lagringskontot som associerats med klustret i **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

I stegen nedan kan du utveckla en modell för att se vad som krävs för att lyckat eller misslyckat mat-kontroll.

## <a name="create-a-spark-mllib-machine-learning-app"></a>Skapa en Spark MLlib machine learning-app

1. Skapa en Jupyter-anteckningsbok med PySpark-kerneln. Instruktioner finns i [Skapa en Jupyter-anteckningsbok](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importera de typer som krävs för det här programmet. Kopiera och klistra in följande kod i en tom cell och tryck sedan på **skjorta + RETUR**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    På grund av PySpark-kerneln behöver du inte uttryckligen skapa några kontexter. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen. 

## <a name="construct-the-input-dataframe"></a>Skapa den inkommande dataramen

Eftersom rådata är CSV-format kan du använda Spark-kontext för att hämta filen i minnet som ostrukturerad text och sedan använda Python's CSV-bibliotek för att parsa varje rad med data.

1. Kör följande rader för att skapa en Resilient Distributed Dataset (RDD) genom att importera och parsning av indata.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Kör följande kod för att hämta en rad från RDD, så att du kan ta en titt på dataschema:

    ```PySpark
    inspections.take(1)
    ```

    Utdata är:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    Utdata ger dig en uppfattning om schemat för indatafilen. Den innehåller namnet på varje genomförande, typen av upprätta, adressen, data i kontrollerna och på plats, bland annat. 

3. Kör följande kod för att skapa en dataram (*df*) och en temporär tabell (*CountResults*) med flera kolumner som är användbara för förutsägande analys. `sqlContext` används för att utföra omvandlingar på strukturerade data. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Fyra kolumner av intresse i dataramen är **id**, **namn**, **resultat**, och **överträdelser**.

4. Kör följande kod för att få ett litet antal data:

    ```PySpark
    df.show(5)
    ```

    Utdata är:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Förstå data

Låt oss börja att få en uppfattning om vad som innehåller datauppsättningen. 

1. Kör följande kod för att visa de distinkta värdena i den **resultat** kolumn:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Utdata är:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Kör följande kod för att visualisera distributionen av de här resultaten returneras:

    ```PySpark
    %%sql -o countResultsdf
    SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results
    ```

    Den `%%sql` magic följt av `-o countResultsdf` garanterar att utdata från frågan sparas lokalt på Jupyter-servern (vanligtvis huvudnoden i klustret). Utdata sparas som en [Pandas](http://pandas.pydata.org/) dataframe med det angivna namnet **countResultsdf**. Mer information om `%%sql`-funktionen, samt andra användbara funktioner hos PySpark-kerneln, finns i [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med HDInsight Spark-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Utdata är:

    ![SQL-frågan returnerade](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-frågan returnerade")


3. Du kan också använda [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), ett bibliotek som används för att konstruera visualisering av data för att skapa en rityta. Eftersom området måste skapas från lokalt till beständiga **countResultsdf** dataframe, kodfragmentet måste börja med den `%%local` magic. Detta säkerställer att koden körs lokalt på Jupyter-server.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Utdata är:

    ![Spark maskininlärningstillämpning utdata - cirkeldiagram med fem olika resultaten](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark machine learning-resultatet utdata")

    Det finns 5 olika resultat som inspektion kan ha:

    - Företag som inte finns
    - Misslyckades
    - Godkänd
    - Skicka meddelanden med av villkor
    - Verksamhet

    För att förutsäga ett mat inspektion resultat, måste du utveckla en modell som baseras på överträdelser. Eftersom logistic regression är en metod för binär klassificering, är det praktiskt att gruppera Resultatdata i två kategorier: **misslyckas** och **skicka**:

    - Godkänd
        - Godkänd
        - Skicka meddelanden med av villkor
    - Misslyckades
        - Misslyckades
    - Ta bort
        - Företag som inte finns
        - Verksamhet

    Data med andra resultaten (”företag finns inte” eller ”Out of Business”) är inte användbara och de utgör en liten andel av resultaten ändå.

4. Kör följande kod för att konvertera den befintliga dataramen (`df`) till en ny dataframe där varje inspektion representeras som en etikett överträdelser-par. I det här fallet en etikett för `0.0` genererar ett fel, en etikett för `1.0` representerar lyckas och en etikett för `-1.0` representerar vissa resultat utöver dessa två. 

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Kör följande kod för att visa en rad med märkta data:

    ```PySpark
    labeledData.take(1)
    ```

    Utdata är:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Skapa en logistic regression-modellen från dataramen som indata

Det sista steget är att omvandla märkta data till ett format som kan analyseras av logistic regression. Indata till en logistic regression-algoritmen måste vara en uppsättning *etikett-funktionen vektor par*, där ”funktionen vektor” är en vektor med tal som representerar en tidpunkt som indata. Därför måste du konvertera kolumnen ”överträdelser”, som är delvis strukturerade och innehåller många kommentarer i fritext, till en matris med reella tal som en virtuell dator kan lätt att förstå.

En standard machine learning-metoden för bearbetning av naturligt språk är att tilldela varje distinkta ord ”index” och sedan skicka en vector till maskininlärningsalgoritmen så att varje indexvärde innehåller hur ofta ordet i textsträngen.

MLlib ger ett enkelt sätt att utföra den här åtgärden. Först ”tokenize” varje överträdelser sträng för att få de enskilda orden i varje sträng. Använd sedan en `HashingTF` att konvertera varje uppsättning token till en funktion vector som sedan kan skickas till logistic regression-algoritmen för att skapa en modell. Du kan utföra alla dessa steg i ordning med hjälp av en ”pipeline”.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Utvärdera modellen med en annan datauppsättning

Du kan använda den modell som du skapade tidigare till *förutsäga* vad resultatet av nya inspektioner kommer att baseras på överträdelser som har observerats. Den här modellen på datauppsättningen som har tränats **Food_Inspections1.csv**. Du kan använda en andra datauppsättning **Food_Inspections2.csv**till *utvärdera* styrkan hos den här modellen på nya data. Den här andra datamängden (**Food_Inspections2.csv**) är i standard storage-behållare som är associerade med klustret.

1. Kör följande kod för att skapa en ny dataframe **predictionsDf** som innehåller förutsägelser som genereras av modellen. Kodfragmentet skapar även en tillfällig tabell som kallas **förutsägelser** baserat på dataramen.

    ```PySpark
    testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Du bör se utdata som liknar följande:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Titta på en av förutsägelserna. Kör det här kodfragmentet:

    ```PySpark
    predictionsDf.take(1)
    ```

   Det finns en förutsägelse för den första posten i test-datauppsättningen.
1. Den `model.transform()` metoden gäller samma transformeringen för nya data med samma schema och komma fram till en förutsägelse av hur du klassificerar data. Du kan göra några enkla statistik för att få en uppfattning om hur exakt förutsägelserna har:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Utdata ser ut som följande:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Logistic regression ger med Spark dig en rättvisande modell för relationen mellan överträdelser beskrivningar på engelska och om ett visst företag skulle lyckat eller misslyckat mat-kontroll.

## <a name="create-a-visual-representation-of-the-prediction"></a>Skapa en visuell representation av förutsägelser
Nu kan du skapa en slutlig visualisering för att du anledning om resultaten av det här testet.

1. Du startar genom att extrahera olika förutsägelser och resultat från den **förutsägelser** temporära tabellen som skapades tidigare. Följande frågor avgränsa utdata som *true_positive*, *false_positive*, *true_negative*, och *false_negative*. I frågorna nedan du inaktivera visualisering med hjälp av `-q` och även spara utdata (med hjälp av `-o`) som dataramar som sedan kan användas med den `%%local` magic.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Använd slutligen följande kodfragment för att skapa diagram med hjälp av **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Du bör se följande utdata:

    ![Spark machine learning-utdata för program - cirkeldiagram procentandelar av misslyckade mat inspektioner. ] (./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark machine learning-resultatet utdata")

    I det här diagrammet ”positivt” resultat som refererar till misslyckade mat-kontroll, medan ett negativt resultat som refererar till en skickade undersökning.

## <a name="shut-down-the-notebook"></a>Stänger du anteckningsboken
När du har kört programmet bör du stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Detta stänger av och stänger anteckningsboken.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
