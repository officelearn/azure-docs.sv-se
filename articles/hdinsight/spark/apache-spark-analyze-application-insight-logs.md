---
title: Analysera Application Insight-loggar med Spark - Azure HDInsight
description: Lär dig hur du exporterar Application Insight-loggar till bloblagring och analyserar sedan loggarna med Spark på HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 6fd7682f56fbe446904a4acdb39e78525f2523a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435247"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analysera telemetriloggar för application insights med Apache Spark på HDInsight

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) på HDInsight för att analysera programinsekmetridata.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) är en analystjänst som övervakar dina webbprogram. Telemetridata som genereras av Application Insights kan exporteras till Azure Storage. När data finns i Azure Storage kan HDInsight användas för att analysera dem.

## <a name="prerequisites"></a>Krav

* Ett program som är konfigurerat för att använda Application Insights.

* Förtrogenhet med att skapa ett Linux-baserat HDInsight-kluster. Mer information finns i [Skapa Apache Spark på HDInsight](apache-spark-jupyter-spark-sql.md).

* En webbläsare.

Följande resurser användes för att utveckla och testa det här dokumentet:

* Telemetridata för Application Insights genererades med hjälp av en [nod.js-webbapp som konfigurerats för att använda Application Insights](../../azure-monitor/app/nodejs.md).

* En Linux-baserad Spark på HDInsight-klusterversion 3.5 användes för att analysera data.

## <a name="architecture-and-planning"></a>Arkitektur och planering

Följande diagram illustrerar tjänstarkitekturen i det här exemplet:

![Data som flödar från Application Insights till blob storage och sedan Spark](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Azure Storage

Application Insights kan konfigureras för att kontinuerligt exportera telemetriinformation till blobbar. HDInsight kan sedan läsa data som lagras i blobbar. Det finns dock vissa krav som du måste följa:

* **Plats**: Om lagringskontot och HDInsight finns på olika platser kan det öka svarstiden. Det ökar också kostnaderna, eftersom utgående avgifter tillämpas på data som flyttar mellan regioner.

    > [!WARNING]  
    > Det går inte att använda ett lagringskonto på en annan plats än HDInsight.

* **Blob typ:** HDInsight stöder endast blockblobar. Application Insights som standard använder blockblobar, så bör fungera som standard med HDInsight.

Information om hur du lägger till lagring i ett befintligt kluster finns i dokumentet [Lägg till ytterligare lagringskonton.](../hdinsight-hadoop-add-storage.md)

### <a name="data-schema"></a>Dataschema

Application Insights tillhandahåller [exportdatamodellinformation](../../azure-monitor/app/export-data-model.md) för telemetridataformatet som exporteras till blobbar. Stegen i det här dokumentet använder Spark SQL för att arbeta med data. Spark SQL kan automatiskt generera ett schema för JSON-datastrukturen som loggas av Application Insights.

## <a name="export-telemetry-data"></a>Exportera telemetridata

Följ stegen i [Konfigurera kontinuerlig export](../../azure-monitor/app/export-telemetry.md) för att konfigurera dina programstatistik för att exportera telemetriinformation till en Azure Storage-blob.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurera HDInsight för att komma åt data

Om du skapar ett HDInsight-kluster lägger du till lagringskontot när klustret skapas.

Om du vill lägga till Azure Storage-kontot i ett befintligt kluster använder du informationen i dokumentet [Lägg till ytterligare lagringskonton.](../hdinsight-hadoop-add-storage.md)

## <a name="analyze-the-data-pyspark"></a>Analysera data: PySpark

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/jupyter` den plats där CLUSTERNAME är namnet på klustret.

2. I det övre högra hörnet på Jupyter-sidan väljer du **Ny**och sedan **PySpark**. En ny webbläsarflik som innehåller en Python-baserad Jupyter Notebook öppnas.

3. I det första fältet (en så kallad **cell)** på sidan anger du följande text:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Den här koden konfigurerar Spark så att den återkommer kommer åt katalogstrukturen för indata. Telemetrin Application Insights loggas till en `/{telemetry type}/YYYY-MM-DD/{##}/`katalogstruktur som liknar .

4. Använd **SKIFT+RETUR** för att köra koden. Till vänster i cellen visas\*en ' ' mellan parenteserna för att ange att koden i den här cellen körs. När det är klart, ändras '\*' till ett tal och utdata som liknar följande text visas under cellen:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. En ny cell skapas under den första. Ange följande text i den nya cellen. Ersätt `CONTAINER` `STORAGEACCOUNT` och med Azure Storage-kontonamnet och blob-behållarnamnet som innehåller Application Insights-data.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Använd **SKIFT+RETUR** för att köra cellen. Du ser ett resultat som liknar följande text:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Sökvägen för wasbs som returneras är platsen för telemetridata för Application Insights. Ändra `hdfs dfs -ls` raden i cellen så att den sökvägen som returneras används och använd sedan **SKIFT+RETUR** för att köra cellen igen. Den här gången bör resultaten visa de kataloger som innehåller telemetridata.

   > [!NOTE]  
   > För resten av stegen i det `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` här avsnittet användes katalogen. Katalogstrukturen kan vara annorlunda.

6. I nästa cell anger du följande `WASB_PATH` kod: Ersätt med sökvägen från föregående steg.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Den här koden skapar en dataram från JSON-filer som exporteras av den kontinuerliga exportprocessen. Använd **SKIFT+RETUR** för att köra cellen.
7. I nästa cell anger och kör du följande för att visa schemat som Spark skapade för JSON-filerna:

   ```python
   jsonData.printSchema()
   ```

    Schemat för varje typ av telemetri är olika. Följande exempel är det schema som genereras för webbbegäranden (data som lagras i underkatalogen): `Requests`

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Använd följande för att registrera dataramen som en tillfällig tabell och köra en fråga mot data:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Den här frågan returnerar stadsinformationen för de 20 bästa posterna där context.location.city inte är null.

   > [!NOTE]  
   > Kontextstrukturen finns i all telemetri som loggas av Application Insights. Stadselementet kanske inte fylls i i loggarna. Använd schemat för att identifiera andra element som du kan fråga efter som kan innehålla data för dina loggar.

    Den här frågan returnerar information som liknar följande text:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>Analysera data: Scala

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/jupyter` den plats där CLUSTERNAME är namnet på klustret.

2. I det övre högra hörnet på Jupyter-sidan väljer du **Ny**och sedan **Scala**. En ny webbläsarflik som innehåller en Scala-baserad Jupyter-anteckningsbok visas.

3. I det första fältet (en så kallad **cell)** på sidan anger du följande text:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Den här koden konfigurerar Spark så att den återkommer kommer åt katalogstrukturen för indata. Telemetrin Application Insights loggas till en `/{telemetry type}/YYYY-MM-DD/{##}/`katalogstruktur som liknar .

4. Använd **SKIFT+RETUR** för att köra koden. Till vänster i cellen visas\*en ' ' mellan parenteserna för att ange att koden i den här cellen körs. När det är klart, ändras '\*' till ett tal och utdata som liknar följande text visas under cellen:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. En ny cell skapas under den första. Ange följande text i den nya cellen. Ersätt `CONTAINER` `STORAGEACCOUNT` och med Azure Storage-kontonamnet och blob-behållarnamnet som innehåller Application Insights-loggar.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Använd **SKIFT+RETUR** för att köra cellen. Du ser ett resultat som liknar följande text:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Sökvägen för wasbs som returneras är platsen för telemetridata för Application Insights. Ändra `hdfs dfs -ls` raden i cellen så att den sökvägen som returneras används och använd sedan **SKIFT+RETUR** för att köra cellen igen. Den här gången bör resultaten visa de kataloger som innehåller telemetridata.

   > [!NOTE]  
   > För resten av stegen i det `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` här avsnittet användes katalogen. Den här katalogen kanske inte finns om inte telemetridata är för en webbapp.

6. I nästa cell anger du följande `WASB\_PATH` kod: Ersätt med sökvägen från föregående steg.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Den här koden skapar en dataram från JSON-filer som exporteras av den kontinuerliga exportprocessen. Använd **SKIFT+RETUR** för att köra cellen.

7. I nästa cell anger och kör du följande för att visa schemat som Spark skapade för JSON-filerna:

   ```scala
   jsonData.printSchema
   ```

    Schemat för varje typ av telemetri är olika. Följande exempel är det schema som genereras för webbbegäranden (data som lagras i underkatalogen): `Requests`

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Använd följande för att registrera dataramen som en tillfällig tabell och köra en fråga mot data:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Den här frågan returnerar stadsinformationen för de 20 bästa posterna där context.location.city inte är null.

   > [!NOTE]  
   > Kontextstrukturen finns i all telemetri som loggas av Application Insights. Stadselementet kanske inte fylls i i loggarna. Använd schemat för att identifiera andra element som du kan fråga efter som kan innehålla data för dina loggar.

    Den här frågan returnerar information som liknar följande text:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Nästa steg

Fler exempel på hur du använder Apache Spark för att arbeta med data och tjänster i Azure finns i följande dokument:

* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

Information om hur du skapar och kör Spark-program finns i följande dokument:

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb på distans i ett Apache Spark-kluster med Livy](apache-spark-livy-rest-interface.md)
