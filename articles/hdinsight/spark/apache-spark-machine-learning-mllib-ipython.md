---
title: Exempel på maskininlärning med Spark MLlib på HDInsight - Azure
description: Lär dig hur du använder Spark MLlib för att skapa en maskininlärningsapp som analyserar en datauppsättning med hjälp av klassificering genom logistisk regression.
keywords: spark maskininlärning, gnista maskininlärning exempel
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: c8ead7abc454df387db31b2ce65d2ba714b0067d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494083"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Använd Apache Spark MLlib för att skapa ett maskininlärningsprogram och analysera en datauppsättning

Lär dig hur du använder Apache Spark [MLlib](https://spark.apache.org/mllib/) för att skapa ett maskininlärningsprogram för att göra enkla prediktiva analyser på en öppen datauppsättning. Från Sparks inbyggda maskininlärningsbibliotek används *klassificering* genom logistisk regression i det här exemplet. 

MLlib är ett spark-huvudbibliotek som tillhandahåller många verktyg som är användbara för maskininlärningsuppgifter, inklusive verktyg som är lämpliga för:

* Klassificering
* Regression
* Klustring
* Ämnesmodellering
* Singularvärdesdekomposit (SVD) och huvudkomponentanalys (PCA)
* Hypotestning och beräkning av exempelstatistik

## <a name="understand-classification-and-logistic-regression"></a>Förstå klassificering och logistisk regression
*Klassificering*, en populär maskininlärningsuppgift, är processen att sortera indata i kategorier. Det är jobbet för en klassificeringsalgoritm för att ta reda på hur du tilldelar "etiketter" till indata som du tillhandahåller. Du kan till exempel tänka dig en maskininlärningsalgoritm som accepterar lagerinformation som indata och delar upp lagret i två kategorier: aktier som du bör sälja och lager som du bör behålla.

Logistisk regression är den algoritm som du använder för klassificering. Sparks logistiska regressions-API är användbart för *binär klassificering*eller klassificering av indata i en av två grupper. Mer information om logistiska regressioner finns i [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Sammanfattningsvis ger processen för logistisk regression en *logistisk funktion* som kan användas för att förutsäga sannolikheten för att en indatavektor hör hemma i en eller annan grupp.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exempel på prediktiv analys av uppgifter om livsmedelsinspektion
I det här exemplet använder du Spark för att utföra vissa prediktiva analyser av livsmedelsinspektionsdata **(Food_Inspections1.csv**) som förvärvades via [dataportalen City of Chicago](https://data.cityofchicago.org/). Denna datauppsättning innehåller information om inspektioner av livsmedelsanläggningar som genomfördes i Chicago, inklusive information om varje anläggning, de överträdelser som hittats (om sådana finns) och resultaten av inspektionen. CSV-datafilen är redan tillgänglig i lagringskontot som är associerat med **klustret på /HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

I stegen nedan utvecklar du en modell för att se vad som krävs för att passera eller misslyckas med en livsmedelsinspektion.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Skapa en Apache Spark MLlib-maskininlärningsapp

1. Skapa en Jupyter-anteckningsbok med PySpark-kerneln. Instruktioner finns i [Skapa en Jupyter-anteckningsbok](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importera de typer som krävs för det här programmet. Kopiera och klistra in följande kod i en tom cell och tryck sedan på **SKIFT + RETUR**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    På grund av PySpark-kärnan behöver du inte skapa några sammanhang uttryckligen. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen. 

## <a name="construct-the-input-dataframe"></a>Konstruera indataramen

Eftersom rådata är i ett CSV-format kan du använda Spark-kontexten för att hämta filen till minne som ostrukturerad text och sedan använda Pythons CSV-bibliotek för att tolka varje rad i data.

1. Kör följande rader för att skapa en RDD (Resilient Distributed Dataset) genom att importera och tolka indata.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Kör följande kod för att hämta en rad från FJÄRRSKRIVBORDSD:

    ```PySpark
    inspections.take(1)
    ```

    Utdata ser ut så här:

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

    Utdata ger dig en uppfattning om schemat för indatafilen. Den omfattar namnet på varje anläggning, typ av anläggning, adress, uppgifter om inspektionerna och platsen, bland annat. 

3. Kör följande kod för att skapa en dataram (*df*) och en tillfällig tabell (*CountResults*) med några kolumner som är användbara för den prediktiva analysen. `sqlContext`används för att utföra omvandlingar på strukturerade data. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    De fyra kolumnerna av intresse för dataramen är **id,** **namn,** **resultat**och **överträdelser**.

4. Kör följande kod för att få ett litet urval av data:

    ```PySpark
    df.show(5)
    ```

    Utdata ser ut så här:

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

Låt oss börja få en känsla för vad datauppsättningen innehåller. 

1. Kör följande kod för att visa de distinkta värdena i **resultatkolumnen:**

    ```PySpark
    df.select('results').distinct().show()
    ```

    Utdata ser ut så här:

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
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    Magin `%%sql` följt `-o countResultsdf` av säkerställer att utdata för frågan sparas lokalt på Jupyter-servern (vanligtvis huvudnoden för klustret). Utdata sparas som en [Pandas-dataram](https://pandas.pydata.org/) med det angivna namnet **countResultsdf**. Mer information om `%%sql` magin och andra magier som finns med PySpark-kärnan finns i [Kärnor som finns tillgängliga på Jupyter-anteckningsböcker med Apache Spark HDInsight-kluster](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Utdata ser ut så här:

    ![SQL-frågeutdata](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-frågeutdata")


3. Du kan också använda [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), ett bibliotek som används för att konstruera visualisering av data, för att skapa en ritplan. Eftersom diagrammet måste skapas från den lokalt beständiga **dataramen countResultsdf** måste `%%local` kodavsnittet börja med magin. Detta säkerställer att koden körs lokalt på Jupyter-servern.

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

    Utdata ser ut så här:

    ![Spark machine learning-programutdata - cirkeldiagram med fem distinkta inspektionsresultat](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Resultatutdata för sparkmaskininlärning")

    För att förutsäga ett resultat livsmedelsinspektion måste du utveckla en modell baserad på kränkningarna. Eftersom logistisk regression är en binär klassificeringsmetod är det vettigt att gruppera resultatdata i två kategorier: **Misslyckas** och **Godkänd:**

   - Passera
       - Passera
       - Passera med villkor
   - Underkänn
       - Underkänn
   - Ignorera
       - Företag som inte finns
       - I konkurs

     Data med andra resultat ("Business Not Located" eller "Out of Business") är inte användbara, och de utgör en mycket liten andel av resultaten ändå.

4. Kör följande kod för att konvertera`df`den befintliga dataramen( ) till en ny dataram där varje inspektion representeras som ett etikettfelspar. I det här fallet `0.0` representerar en etikett `1.0` av ett fel, `-1.0` en etikett av representerar en framgång och en etikett av representerar vissa resultat förutom dessa två. 

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

5. Kör följande kod om du vill visa en rad med de märkta data:

    ```PySpark
    labeledData.take(1)
    ```

    Utdata ser ut så här:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Skapa en logistisk regressionsmodell från indataramen

Den sista uppgiften är att konvertera de märkta data till ett format som kan analyseras genom logistisk regression. Indata till en logistisk regressionsalgoritm behöver vara en uppsättning *vektorpar*för etikettfunktionen , där "funktionsvektorn" är en vektor med tal som representerar indatapunkten. Så måste du konvertera kolumnen "överträdelser", som är halvstrukturerad och innehåller många kommentarer i fritext, till en rad verkliga tal som en maskin lätt kan förstå.

En standardmaskininlärningsmetod för bearbetning av naturligt språk är att tilldela varje distinkt ord ett "index" och sedan skicka en vektor till maskininlärningsalgoritmen så att varje indexs värde innehåller den relativa frekvensen för det ordet i textsträngen.

MLlib är ett enkelt sätt att utföra den här åtgärden. Först "tokenize" varje överträdelser sträng för att få de enskilda orden i varje sträng. Använd sedan `HashingTF` en för att konvertera varje uppsättning token till en funktionsvektor som sedan kan skickas till den logistiska regressionsalgoritmen för att konstruera en modell. Du utför alla dessa steg i följd med hjälp av en "pipeline".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Utvärdera modellen med en annan datauppsättning

Du kan använda modellen som du skapade tidigare för att *förutsäga* vad resultaten av nya inspektioner kommer att bli, baserat på de överträdelser som observerades. Du har tränat den här modellen på datauppsättningen **Food_Inspections1.csv**. Du kan använda en andra datauppsättning, **Food_Inspections2.csv**, för att *utvärdera* styrkan i den här modellen på de nya data. Den andra datauppsättningen (**Food_Inspections2.csv**) finns i standardlagringsbehållaren som är associerad med klustret.

1. Kör följande kod för att skapa en ny dataram, **förutsägelserDf** som innehåller förutsägelsen som genereras av modellen. Kodavsnittet skapar också en tillfällig tabell med **namnet Förutsägelser** baserat på dataramen.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Du bör se utdata som liknar följande:

    ```
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

1. Titta på en av förutsägelserna. Kör det här kodavsnittet:

    ```PySpark
    predictionsDf.take(1)
    ```

   Det finns en förutsägelse för den första posten i testdatauppsättningen.
1. Metoden `model.transform()` tillämpar samma omvandling på alla nya data med samma schema och kommer fram till en förutsägelse om hur data ska klassificeras. Du kan göra några enkla statistik för att få en känsla av hur exakt förutsägelserna var:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Utdata ser ut så här:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Genom att använda logistisk regression med Spark får du en korrekt modell av förhållandet mellan överträdelser på engelska och om ett visst företag skulle passera eller underkänna en livsmedelsinspektion.

## <a name="create-a-visual-representation-of-the-prediction"></a>Skapa en visuell representation av förutsägelsen
Du kan nu skapa en slutlig visualisering som hjälper dig att resonera om resultatet av det här testet.

1. Du börjar med att extrahera de olika förutsägelserna och resultaten från den **temporära** tabellen Förutsägelser som skapats tidigare. Följande frågor separerar utdata som *true_positive*, *false_positive*, *true_negative*och *false_negative*. I frågorna nedan inaktiverar du visualisering `-q` genom att använda och `-o`sparar även utdata (med hjälp `%%local` av) som dataramar som sedan kan användas med magin.

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

1. Slutligen använder du följande utdrag för att generera diagrammet med **Matplotlib**.

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

    ![Spark machine learning-programutdata - cirkeldiagramprocent av misslyckade livsmedelsinspektioner.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Resultatutdata för sparkmaskininlärning")

    I det här diagrammet avser ett "positivt" resultat den misslyckade livsmedelsinspektionen, medan ett negativt resultat avser en godkänd inspektion.

## <a name="shut-down-the-notebook"></a>Stänga av anteckningsboken
När du har kört programmet bör du stänga av anteckningsboken för att frigöra resurserna. Du gör det genom att välja **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Detta stängs av och stänger anteckningsboken.

## <a name="see-also"></a><a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight Tools Plugin för IntelliJ IDEA för att felsöka Apache Spark-program på distans](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
