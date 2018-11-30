---
title: Analysera loggar i Application Insights med Spark - Azure HDInsight
description: Lär dig hur du exporterar loggar i Application Insights för att blob-lagring och sedan analysera loggarna med Spark på HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.openlocfilehash: 951292a34f59fd143a7997571513a3c852bbce81
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497992"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analysera loggar i Application Insights telemetry med Apache Spark i HDInsight

Lär dig hur du använder [Apache Spark](https://spark.apache.org/) på HDInsight för att analysera telemetridata som Application Insights.

[Visual Studio Application Insights](../../application-insights/app-insights-overview.md) är en analystjänst som övervakar dina webbprogram. Telemetridata som genereras av Application Insights kan exporteras till Azure Storage. När data finns i Azure Storage, användas HDInsight för att analysera den.

## <a name="prerequisites"></a>Förutsättningar

* Ett program som är konfigurerad för att använda Application Insights.

* Liknar processen med att skapa en Linux-baserade HDInsight-kluster. Mer information finns i [skapa Apache Spark i HDInsight](apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En webbläsare.

Följande resurser har använts i att utveckla och testa det här dokumentet:

* Telemetridata för Application Insights har skapats med hjälp av en [Node.js-webbapp som konfigurerats för att använda Application Insights](../../application-insights/app-insights-nodejs.md).

* En Linux-baserade Spark på HDInsight-kluster av version 3.5 har använts för att analysera data.

## <a name="architecture-and-planning"></a>Arkitektur och planering

Följande diagram illustrerar arkitekturen för tjänsten för det här exemplet:

![diagram som visar data som flödar från Application Insights till blob storage och sedan bearbetas av Spark i HDInsight](./media/apache-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Azure-lagring

Application Insights kan konfigureras för att exportera telemetri kontinuerligt till BLOB-objekt. HDInsight läsa data som lagras i blobbar. Det finns dock vissa krav som måste följas:

* **Plats**: om Storage-konto och HDInsight finns på olika platser, kan det öka svarstiden. Kostnad, ökar även som utgående avgifterna tillämpas på data som flyttas mellan regioner.

    > [!WARNING]
    > Med ett Storage-konto i en annan plats än HDInsight stöds inte.

* **Typ av BLOB**: HDInsight stöder endast blockblobar. Standardvärdet är Application Insights med hjälp av blockblob-objekt, bör så fungera som standard med HDInsight.

Information om att lägga till lagring i ett befintligt kluster finns i den [lägga till ytterligare lagringskonton](../hdinsight-hadoop-add-storage.md) dokumentet.

### <a name="data-schema"></a>Dataschema

Application Insights ger [exportera datamodell](../../application-insights/app-insights-export-data-model.md) information för dataformatet telemetri som exporteras till BLOB-objekt. Stegen i det här dokumentet använder Spark SQL för att arbeta med data. Spark SQL kan automatiskt generera ett schema för JSON-datastruktur som loggats av Application Insights.

## <a name="export-telemetry-data"></a>Exportera dessa data

Följ stegen i [konfigurera löpande Export](../../application-insights/app-insights-export-telemetry.md) att konfigurera din Application Insights för att exportera telemetri till en Azure storage blob.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurera HDInsight för att komma åt data

Om du skapar ett HDInsight-kluster, lägger du till lagringskontot när klustret skapas.

Lägg till Azure Storage-konto till ett befintligt kluster genom att använda informationen i den [lägga till ytterligare Lagringskonton](../hdinsight-hadoop-add-storage.md) dokumentet.

## <a name="analyze-the-data-pyspark"></a>Analysera data: PySpark

1. Från den [Azure-portalen](https://portal.azure.com), Välj din Apache Spark på HDInsight-kluster. Från den **snabblänkar** väljer **Klusterinstrumentpaneler**, och välj sedan **Jupyter Notebook** från avsnittet Dashboard__ för klustret.

    ![Klusterinstrumentpaneler](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)

2. I det övre högra hörnet av sidan Jupyter väljer **New**, och sedan **PySpark**. En ny webbläsarflik som innehåller en Python-baserade Jupyter-anteckningsbok öppnas.

3. I det första fältet (kallas en **cell**) på sidan, anger du följande text:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Den här koden konfigurerar Spark för att rekursivt åtkomst katalogstrukturen för indata. Application Insights telemetry loggas i en katalogstruktur som liknar den `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Använd **SKIFT + RETUR** att köra koden. På vänster sida av cell, ett ”\*” visas inom parentes som anger att koden i den här cellen körs. När testet är klart den '\*' ändringar av ett tal och utdata som liknar följande text visas under cellen:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. En ny cell skapas under den första mallen för. Ange följande text i den nya cellen. Ersätt `CONTAINER` och `STORAGEACCOUNT` med Azure storage-kontonamn och blob-behållarnamn som innehåller Application Insights-data.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Använd **SKIFT + RETUR** att köra den här cellen. Du ser ett resultat liknande följande text:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Wasb-sökväg som returneras är platsen för telemetridata som Application Insights. Ändra den `hdfs dfs -ls` rad i cellen att använda wasb-sökväg som returneras och sedan använda **SKIFT + RETUR** köra cellen igen. Den här gången ska resultaten visa kataloger som innehåller dessa data.

   > [!NOTE]
   > Under resten av stegen i det här avsnittet i `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` directory användes. Din katalogstruktur skilja sig.

6. Ange följande kod i cellen nästa: Ersätt `WASB_PATH` med sökvägen från föregående steg.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Den här koden skapar en dataram från JSON-filer som exporteras av processen för löpande export. Använd **SKIFT + RETUR** att köra den här cellen.
7. I nästa cell, ange och kör följande om du vill visa det schema som Spark som skapats för JSON-filerna:

   ```python
   jsonData.printSchema()
   ```

    Schemat för varje typ av telemetri är olika. I följande exempel är det schema som genereras för begäranden (data som lagras i den `Requests` underkatalog):

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

    Den här frågan returnerar adressinformation för de översta 20 posterna där context.location.city inte är null.

   > [!NOTE]
   > Kontext-struktur finns i all telemetri som loggats av Application Insights. Stad-element kan inte fyllas i loggarna. Använd schemat för att identifiera andra element som du kan fråga som kan innehålla data för dina loggar.

    Den här frågan returnerar information liknande följande text:

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

1. Från den [Azure-portalen](https://portal.azure.com), Välj din Apache Spark på HDInsight-kluster. Från den **snabblänkar** väljer **Klusterinstrumentpaneler**, och välj sedan **Jupyter Notebook** från avsnittet Dashboard__ för klustret.

    ![Klusterinstrumentpaneler](./media/apache-spark-analyze-application-insight-logs/clusterdashboards.png)
2. I det övre högra hörnet av sidan Jupyter väljer **New**, och sedan **Scala**. En ny webbläsarflik som innehåller en Scala-baserade Jupyter-anteckningsbok visas.
3. I det första fältet (kallas en **cell**) på sidan, anger du följande text:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Den här koden konfigurerar Spark för att rekursivt åtkomst katalogstrukturen för indata. Application Insights telemetry loggas i en katalogstruktur som liknar `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Använd **SKIFT + RETUR** att köra koden. På vänster sida av cell, ett ”\*” visas inom parentes som anger att koden i den här cellen körs. När testet är klart den '\*' ändringar av ett tal och utdata som liknar följande text visas under cellen:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. En ny cell skapas under den första mallen för. Ange följande text i den nya cellen. Ersätt `CONTAINER` och `STORAGEACCOUNT` med Azure storage-kontonamn och blob-behållarnamn som innehåller Application Insights loggar.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Använd **SKIFT + RETUR** att köra den här cellen. Du ser ett resultat liknande följande text:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Wasb-sökväg som returneras är platsen för telemetridata som Application Insights. Ändra den `hdfs dfs -ls` rad i cellen att använda wasb-sökväg som returneras och sedan använda **SKIFT + RETUR** köra cellen igen. Den här gången ska resultaten visa kataloger som innehåller dessa data.

   > [!NOTE]
   > Under resten av stegen i det här avsnittet i `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` directory användes. Den här katalogen finns inte annat telemetridata för en webbapp.

6. Ange följande kod i cellen nästa: Ersätt `WASB\_PATH` med sökvägen från föregående steg.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Den här koden skapar en dataram från JSON-filer som exporteras av processen för löpande export. Använd **SKIFT + RETUR** att köra den här cellen.

7. I nästa cell, ange och kör följande om du vill visa det schema som Spark som skapats för JSON-filerna:

   ```scala
   jsonData.printSchema
   ```

    Schemat för varje typ av telemetri är olika. I följande exempel är det schema som genereras för begäranden (data som lagras i den `Requests` underkatalog):

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
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Den här frågan returnerar adressinformation för de översta 20 posterna där context.location.city inte är null.

   > [!NOTE]
   > Kontext-struktur finns i all telemetri som loggats av Application Insights. Stad-element kan inte fyllas i loggarna. Använd schemat för att identifiera andra element som du kan fråga som kan innehålla data för dina loggar.
   >
   >

    Den här frågan returnerar information liknande följande text:

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

* [Apache Spark med BI: utföra interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

Information om att skapa och köra Spark-program finns i följande dokument:

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med Livy](apache-spark-livy-rest-interface.md)
