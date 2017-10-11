---
title: Analysera programmet Insight loggar med Spark - Azure HDInsight | Microsoft Docs
description: "Lär dig hur du exporterar programmet Insight loggar blob storage och analysera loggar med Spark i HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/15/2017
ms.author: larryfr
ms.openlocfilehash: d98e403683618ef6115372f99e4949af87af4490
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>Analysera Application Insights telemetri loggar med Spark i HDInsight

Lär dig använda Spark i HDInsight för att analysera programmet inblick telemetridata.

[Visual Studio Application Insights](../application-insights/app-insights-overview.md) är en analytics-tjänst som övervakar dina webbprogram. Telemetridata som genererats av Application Insights kan exporteras till Azure Storage. När data finns i Azure Storage, användas HDInsight för att analysera den.

## <a name="prerequisites"></a>Krav

* Ett program som är konfigurerad för att använda Application Insights.

* Om du är bekant med att skapa ett Linux-baserade HDInsight-kluster. Mer information finns i [skapa Spark i HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

  > [!IMPORTANT]
  > Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* En webbläsare.

Följande resurser har använts i utvecklar och testar det här dokumentet:

* Application Insights telemetridata genererades med en [Node.js-webbapp som konfigurerats för att använda Application Insights](../application-insights/app-insights-nodejs.md).

* En Linux-baserade Spark i HDInsight-kluster av version 3.5 användes för att analysera data.

## <a name="architecture-and-planning"></a>Arkitektur och planering

Följande diagram illustrerar arkitekturen tjänsten för det här exemplet:

![diagram som visar data som flödar från Application Insights till blob storage och sedan bearbetas av Spark i HDInsight](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Azure Storage

Application Insights kan konfigureras för att exportera kontinuerligt telemetri information till BLOB. HDInsight kan sedan läsa data som lagras i blobar. Det finns dock vissa krav som måste följas:

* **Plats**: om Storage-konto och HDInsight finns på olika platser, kan öka svarstid. Kostnad, ökar även som utgång avgifter som tillämpas på data flyttas mellan regioner.

    > [!WARNING]
    > Med hjälp av ett Lagringskonto i en annan plats än HDInsight stöds inte.

* **BLOB-typen**: HDInsight stöder endast blockblobar. Programmet insikter som standard använder blockblobar, ska så fungera som standard med HDInsight.

Mer information om att lägga till ytterligare lagringsutrymme i ett befintligt HDInsight-kluster finns i [lägga till ytterligare lagringskonton](hdinsight-hadoop-add-storage.md) dokumentet.

### <a name="data-schema"></a>Dataschemat

Application Insights ger [exportera datamodellen](../application-insights/app-insights-export-data-model.md) information för dataformatet telemetri exporteras till BLOB. Stegen i det här dokumentet använder Spark SQL för att arbeta med data. Spark SQL kan automatiskt skapa ett schema för JSON-datastrukturen som loggats av Application Insights.

## <a name="export-telemetry-data"></a>Exportera telemetridata

Följ stegen i [konfigurera löpande Export](../application-insights/app-insights-export-telemetry.md) att konfigurera din Application Insights för att exportera telemetri information till en Azure storage blob.

## <a name="configure-hdinsight-to-access-the-data"></a>Konfigurera HDInsight för att komma åt data

Om du skapar ett HDInsight-kluster, kan du lägga till lagringskontot när klustret skapas.

Lägg till Azure Storage-konto i ett befintligt kluster genom att använda informationen i den [lägga till ytterligare Lagringskonton](hdinsight-hadoop-add-storage.md) dokumentet.

## <a name="analyze-the-data-pyspark"></a>Analysera data: PySpark

1. Från den [Azure-portalen](https://portal.azure.com), Välj ditt Spark på HDInsight-kluster. Från den **snabblänkar** väljer **Klusterinstrumentpaneler**, och välj sedan **Jupyter-anteckningsbok** från klustret Dashboard__-bladet.

    ![Klusterinstrumentpaneler](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)

2. Välj i det övre högra hörnet på sidan Jupyter **ny**, och sedan **PySpark**. En ny webbläsarflik som innehåller en Python-baserade Jupyter-anteckningsbok öppnas.

3. I det första fältet (kallas en **cell**) på sidan, Skriv följande:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Den här koden konfigurerar Spark rekursivt åtkomsten katalogstrukturen för indata. Application Insights telemetry loggas i en katalogstruktur som liknar den `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Använd **SKIFT + RETUR** att köra koden. På vänster sida av cell, ett '\*' visas inom hakparenteser att indikera att koden i den här cellen som körs. När den är klar på '\*' ändringar av ett tal och utdata som liknar följande text visas under cellen:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. En ny cell skapas under den första. Ange följande text i den nya cellen. Ersätt `CONTAINER` och `STORAGEACCOUNT` med Azure storage-kontonamnet och blob behållarens namn som innehåller Application Insights-data.

   ```python
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Använd **SKIFT + RETUR** att köra den här cellen. Du får ett resultat som liknar följande:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Wasb sökväg som returneras är platsen för telemetridata Application Insights. Ändra den `hdfs dfs -ls` rad i cellen att använda wasb sökväg som returneras och sedan använda **SKIFT + RETUR** köra cellen igen. Den här gången ska resultaten visa kataloger som innehåller telemetridata.

   > [!NOTE]
   > Under resten av stegen i det här avsnittet i `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` directory användes. Katalogstrukturen kan vara olika.

6. Ange följande kod i nästa cell: ersätta `WASB_PATH` med sökvägen från föregående steg.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Den här koden skapar en dataframe från JSON-filer som exporterats av löpande export-process. Använd **SKIFT + RETUR** att köra den här cellen.
7. Ange i nästa cell och kör följande om du vill visa det schema som Spark skapat för JSON-filer:

   ```python
   jsonData.printSchema()
   ```

    Schemat för varje typ av telemetri är olika. Följande exempel är det schema som genereras för webbegäranden (data som lagras i den `Requests` underkatalog):

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
8. Använd följande för att registrera dataframe som en temporär tabell och köra en fråga mot data:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Den här frågan returnerar adressinformation för de översta 20 posterna där context.location.city inte är null.

   > [!NOTE]
   > Kontexten strukturen finns all telemetri som loggats av Application Insights. Ort-element kan inte fyllas i loggarna. Använd schemat för att identifiera andra element som du kan fråga som kan innehålla data för loggarna.

    Den här frågan returnerar information som liknar följande:

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

1. Från den [Azure-portalen](https://portal.azure.com), Välj ditt Spark på HDInsight-kluster. Från den **snabblänkar** väljer **Klusterinstrumentpaneler**, och välj sedan **Jupyter-anteckningsbok** från klustret Dashboard__-bladet.

    ![Klusterinstrumentpaneler](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. Välj i det övre högra hörnet på sidan Jupyter **ny**, och sedan **Scala**. En ny webbläsarflik som innehåller en Scala-baserade Jupyter-anteckningsbok visas.
3. I det första fältet (kallas en **cell**) på sidan, Skriv följande:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Den här koden konfigurerar Spark rekursivt åtkomsten katalogstrukturen för indata. Application Insights telemetry loggas i en katalogstruktur som liknar `/{telemetry type}/YYYY-MM-DD/{##}/`.

4. Använd **SKIFT + RETUR** att köra koden. På vänster sida av cell, ett '\*' visas inom hakparenteser att indikera att koden i den här cellen som körs. När den är klar på '\*' ändringar av ett tal och utdata som liknar följande text visas under cellen:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. En ny cell skapas under den första. Ange följande text i den nya cellen. Ersätt `CONTAINER` och `STORAGEACCOUNT` Azure lagringskontonamn och blob behållarens namn som innehåller Programinsikter för inloggning.

   ```scala
   %%bash
   hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Använd **SKIFT + RETUR** att köra den här cellen. Du får ett resultat som liknar följande:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Wasb sökväg som returneras är platsen för telemetridata Application Insights. Ändra den `hdfs dfs -ls` rad i cellen att använda wasb sökväg som returneras och sedan använda **SKIFT + RETUR** köra cellen igen. Den här gången ska resultaten visa kataloger som innehåller telemetridata.

   > [!NOTE]
   > Under resten av stegen i det här avsnittet i `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` directory användes. Den här katalogen finns inte om inte telemetridata är för en webbapp.

6. Ange följande kod i nästa cell: ersätta `WASB\_PATH` med sökvägen från föregående steg.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Den här koden skapar en dataframe från JSON-filer som exporterats av löpande export-process. Använd **SKIFT + RETUR** att köra den här cellen.

7. Ange i nästa cell och kör följande om du vill visa det schema som Spark skapat för JSON-filer:

   ```scala
   jsonData.printSchema
   ```

    Schemat för varje typ av telemetri är olika. Följande exempel är det schema som genereras för webbegäranden (data som lagras i den `Requests` underkatalog):

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

8. Använd följande för att registrera dataframe som en temporär tabell och köra en fråga mot data:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   ```

    Den här frågan returnerar adressinformation för de översta 20 posterna där context.location.city inte är null.

   > [!NOTE]
   > Kontexten strukturen finns all telemetri som loggats av Application Insights. Ort-element kan inte fyllas i loggarna. Använd schemat för att identifiera andra element som du kan fråga som kan innehålla data för loggarna.
   >
   >

    Den här frågan returnerar information som liknar följande:

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

Fler exempel på med Spark för att arbeta med data och tjänster i Azure finns i följande dokument:

* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](hdinsight-apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning](hdinsight-apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

Information om hur du skapar och kör Spark-program finns i följande dokument:

* [Skapa ett fristående program med hjälp av Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](hdinsight-apache-spark-livy-rest-interface.md)
