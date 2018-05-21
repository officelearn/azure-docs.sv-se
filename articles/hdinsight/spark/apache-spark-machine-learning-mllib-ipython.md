---
title: Exempel på Machine learning med MLlib Spark på HDInsight - Azure | Microsoft Docs
description: Lär dig hur du skapar en machine learning-app som analyserar en datauppsättning med hjälp av klassificering via logistic regression med hjälp av Spark MLlib.
keywords: Spark maskininlärning spark machine learning-exempel
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jgao
ms.openlocfilehash: 1fc89f2181a5b9fb6b6c5a26d974b016fa1926a6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Använda Spark MLlib att skapa machine learning-program och analysera en datamängd

Lär dig hur du kan använda Spark [MLlib](https://spark.apache.org/mllib/) att skapa machine learning-program att göra enkla förutsägbar analys för en öppen datauppsättningen. Från Sparks inbyggda maskininlärning bibliotek, det här exemplet används *klassificering* via logistic regression. 

> [!TIP]
> Det här exemplet är också tillgängliga som en Jupyter-anteckningsbok på ett kluster med Spark (Linux) som du skapar i HDInsight. Upplevelse för bärbara datorer kan du köra Python kodavsnitt från den bärbara datorn sig själv. Om du vill följa vägledningen i en bärbar dator, skapa ett Spark-kluster och starta en Jupyter-anteckningsbok (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Kör sedan anteckningsboken **Spark Machine Learning - förutsägbar analys på mat inspektion data med hjälp av MLlib.ipynb** under den **Python** mapp.
>
>

MLlib är ett Spark core-bibliotek som innehåller många användbara verktyg för machine learning aktiviteter, inklusive verktyg som är lämpliga för:

* Klassificering
* Regression
* Klustring
* Avsnittet modellering
* Enda värde uppdelning (SVD) och viktigaste komponenten analys (PCA)
* Hypotesen testning och beräkna exempel statistik

## <a name="understand-classification-and-logistic-regression"></a>Förstå klassificering och logistic regression
*Klassificering*, populära machine learning-aktivitet, är processen att sortera indata i kategorier. Det är en klassificeringsalgoritm att ta reda på hur du tilldelar ”etiketter” för att mata in data som du anger. Exempelvis kan du tänka på en maskininlärningsalgoritmen som accepterar lager information som indata och dividerar börs i två kategorier: du bör säljer och lagren bör du tänka.

Logistic regression är den algoritm som du använder för klassificering. Sparks logistic regression API är användbart för *binär klassificering*, eller klassificera indata till en av två grupper. Läs mer om logistic regressioner [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Sammanfattningsvis av logistic regression producerar en *logistic funktionen* som kan användas för att förutsäga sannolikheten att ett inkommande vector tillhör i en grupp eller den andra.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Förutsägbar analys exempel på mat inspektion data
I det här exemplet använder du Spark för att utföra vissa förutsägbar analys på mat inspektion data (**Food_Inspections1.csv**) som har köpts via den [stad Chicago dataportalen](https://data.cityofchicago.org/). Den här datauppsättningen innehåller information om mat etablering kontroller som har utförts i Chicago, inklusive information om varje anläggning överträdelser hittades (eventuella) och resultatet av kontrollen. CSV-filen finns redan i lagringskontot som är associerade med klustret när **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

I stegen nedan kan du utveckla en modell för att se vad som krävs för att lyckat eller misslyckat en mat-kontroll.

## <a name="create-a-spark-mllib-machine-learning-app"></a>Skapa en Spark MLlib machine learning-app

1. Skapa en Jupyter-anteckningsbok med PySpark-kerneln. Instruktioner finns i [Skapa en Jupyter-anteckningsbok](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importera de typer som krävs för det här programmet. Kopiera och klistra in följande kod i en tom cell och tryck sedan på **SHIRT + RETUR**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    På grund av PySpark-kerneln behöver du inte uttryckligen skapa några kontexter. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen. 

## <a name="construct-the-input-dataframe"></a>Skapa inkommande dataframe

Eftersom rådata är i ett CSV-format måste du använda Spark-kontext och hämtar filen i minnet som Ostrukturerade text och sedan använda Python's CSV-bibliotek för att tolka varje rad i data.

1. Kör följande rader om du vill skapa en flexibel distribuerade datauppsättningen (RDD) genom att importera och tolka indata.

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

2. Kör följande kod för att hämta en rad från RDD, så att du kan ta en titt på dataschemat:

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

    Utdata ger dig en uppfattning om schemat för indatafilen. Den innehåller namnet på varje genomförande, typen av etablering, adress, av data för kontrollerna och plats, bland annat. 

3. Kör följande kod för att skapa en dataframe (*df*) och en tillfällig tabell (*CountResults*) med några kolumner som är användbara för förutsägbar analys. `sqlContext` används för att genomföra transformationer på strukturerade data. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    De fyra kolumnerna av intresse för dataframe är **id**, **namn**, **resultat**, och **överträdelser**.

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

Låt oss börja få en uppfattning om datamängden innehåller. 

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

2. Kör följande kod för att visualisera fördelningen av dessa resultat:

    ```PySpark
    %%sql -o countResultsdf
    SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results
    ```

    Den `%%sql` magic följt av `-o countResultsdf` säkerställer att utdata från frågan sparas lokalt på Jupyter-servern (vanligtvis headnode i klustret). Utdata sparas som en [Pandas](http://pandas.pydata.org/) dataframe med det angivna namnet **countResultsdf**. Mer information om `%%sql`-funktionen, samt andra användbara funktioner hos PySpark-kerneln, finns i [Kernlar som är tillgängliga i Jupyter-anteckningsböcker med HDInsight Spark-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Utdata är:

    ![SQL-frågan](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL frågeresultatet")


3. Du kan också använda [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), ett bibliotek som används för att konstruera visualisering av data för att skapa en rityta. Eftersom området måste skapas från lokalt beständiga **countResultsdf** dataframe, kodfragmentet måste börja med den `%%local` Magiskt tal. Detta säkerställer att koden körs lokalt på Jupyter-servern.

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

    ![Spark maskininlärning programmet utdata - cirkeldiagram med fem olika resultaten](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark maskininlärning resultatet utdata")

    Det finns 5 distinkta resultat som kan ha en inspektion:

    - Företag som inte finns
    - Misslyckades
    - Godkänd
    - Skicka med villkor
    - Out-of-Business

    För att förutsäga ett resultat för kontroll av mat, måste du utveckla en modell utifrån överträdelser. Eftersom logistic regression är en klassificeringsmetod i binär, är det praktiskt att gruppera Resultatdata i två kategorier: **misslyckas** och **skicka**:

    - Godkänd
        - Godkänd
        - Skicka med villkor
    - Misslyckades
        - Misslyckades
    - Ta bort
        - Företag som inte finns
        - Out-of-Business

    Data med andra resultat (”företag kan inte hitta” eller ”Out-of-Business”) är inte användbar och de utgör en liten andel av resultaten ändå.

4. Kör följande kod för att konvertera befintliga dataframe (`df`) till en ny dataframe där varje inspektion representeras som ett par etikett överträdelser. I det här fallet en etikett för `0.0` representerar ett fel, en etikett för `1.0` representerar en lyckad och en etikett för `-1.0` representerar vissa resultat utöver de två. 

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

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Skapa en logistic regressionsmodell från inkommande dataframe

Det sista steget är att omvandla märkta data till ett format som kan analyseras av logistic regression. Indata för en algoritmen logistic regression måste vara en uppsättning *etikett-funktionen vector par*, där ”funktionen vector” är en vektor med siffror som representerar indata. Därför måste du konvertera kolumnen ”överträdelser”, som är halvstrukturerade och innehåller många kommentarer i fritext, till en matris med reellt tal som en dator lätt kan förstå.

En standard maskininlärning metod för behandling av naturligt språk är att tilldela varje distinkta ord ”index” och sedan skicka en vector till maskininlärningsalgoritmen så att varje indexvärde innehåller hur ofta ordet i textsträngen.

MLlib ger ett enkelt sätt att utföra åtgärden. Först ”tokenize” varje överträdelser sträng för att hämta de enskilda orden i varje sträng. Använd sedan en `HashingTF` konvertera varje uppsättning token till en funktion vector som sedan kan skickas logistic regression-algoritmen för att skapa en modell. Du kan utföra alla dessa steg i sekvensen med hjälp av en ”pipeline”.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Utvärdera modellen med en annan dataset

Du kan använda den modell som du skapade tidigare till *förutsäga* vad resultatet av nya kontroller kommer att baseras på överträdelser som observerades. Tränats av den här modellen för datamängden **Food_Inspections1.csv**. Du kan använda en andra dataset **Food_Inspections2.csv**, *utvärdera* styrkan hos den här modellen på nya data. Den här andra datamängden (**Food_Inspections2.csv**) är i standardbehållaren för lagring som är associerade med klustret.

1. Kör följande kod för att skapa en ny dataframe **predictionsDf** som innehåller förutsägelser som genererats av modellen. Sammandraget skapar även en tillfällig tabell som kallas **förutsägelser** baserat på dataframe.

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

1. Titta på en av förutsägelser. Kör följande kodutdrag:

    ```PySpark
    predictionsDf.take(1)
    ```

   Det finns en förutsägelse för den första posten i datauppsättningen test.
1. Den `model.transform()` metoden gäller samma omvandlingen för alla nya data med samma schema och kommer till en förutsägelse av hur du klassificerar data. Du kan göra några enkla statistik för att få en uppfattning om hur korrekta förutsägelser var:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Det ser ut som följande utdata:

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Med Spark logistic regression ger dig en korrekt modell för förhållandet mellan överträdelser beskrivningar på engelska och om ett visst företag skulle lyckat eller misslyckat en mat-kontroll.

## <a name="create-a-visual-representation-of-the-prediction"></a>Skapa en bild av förutsägelser
Du kan nu skapa en slutlig visualisering för att du skäl om resultatet av det här testet.

1. Du startar genom att extrahera olika förutsägelser och resultat från den **förutsägelser** tillfällig tabell skapade tidigare. Följande frågor separata utdata som *true_positive*, *false_positive*, *true_negative*, och *false_negative*. I frågorna nedan du inaktivera visualisering med hjälp av `-q` och även spara utdata (med hjälp av `-o`) som dataframes som sedan kan användas med den `%%local` Magiskt tal.

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

1. Slutligen använder du följande kodavsnitt för att generera ritytans med **Matplotlib**.

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

    ![Spark maskininlärning programinnehåll - cirkeldiagram procentandelar av misslyckade mat kontroller. ] (./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark maskininlärning resultatet utdata")

    I det här diagrammet avser ”positivt” resultat misslyckade mat inspektionen, medan ett negativt resultat refererar till en skickade kontroll.

## <a name="shut-down-the-notebook"></a>Stäng av den bärbara datorn
När du har kört programmet bör du stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Detta stängs av och stänger den bärbara datorn.

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
