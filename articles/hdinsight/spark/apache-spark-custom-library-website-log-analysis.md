---
title: Analysera webbplatsloggar med Python-bibliotek i Spark - Azure | Microsoft Docs
description: "Den här anteckningsboken visar hur du analysera loggdata som använder ett anpassat bibliotek med Spark på Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c61c70f-fe7f-4f0f-a4ab-0cccee5668c9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: edc12e07f69b19cf8ad4e32e14e317a78800392f
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-spark-cluster-on-hdinsight"></a>Analysera webbplatsloggar med hjälp av en anpassad Python-bibliotek med Spark-kluster i HDInsight

Den här anteckningsboken visar hur du analysera loggdata som använder ett anpassat bibliotek med Spark i HDInsight. Vi använder anpassade biblioteket är en Python-bibliotek kallas **iislogparser.py**.

> [!TIP]
> Den här kursen är också tillgängliga som en Jupyter-anteckningsbok på ett kluster med Spark (Linux) som du skapar i HDInsight. Upplevelse för bärbara datorer kan du köra Python kodavsnitt från den bärbara datorn sig själv. För att genomföra vägledningen i en bärbar dator, skapa ett Spark-kluster, starta en Jupyter-anteckningsbok (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), och kör sedan anteckningsboken **analysera loggar med Spark med hjälp av en anpassad library.ipynb** under den **PySpark** mapp.
>
>

**Krav:**

Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Spara rådata som en RDD
I det här avsnittet ska vi använda den [Jupyter](https://jupyter.org) bärbar dator som är associerad med ett Apache Spark-kluster i HDInsight för att köra jobb som bearbetar dina rådata exempeldata och spara den som en Hive-tabell. Exempeldata är en CSV-fil (hvac.csv) tillgängliga på alla kluster som standard.

När du har sparat dina data som en Hive-tabell i nästa avsnitt ansluter vi till Hive-tabell med BI-verktyg som Power BI och Tableau.

1. På startsidan i [Azure Portal](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.   
2. Från Spark-klusterbladet, klickar du på **Klusterinstrumentpanel** och sedan på **Jupyter Notebook**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

   > [!NOTE]
   > Du kan också nå Jupyter Notebook för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Skapa en ny anteckningsbok. Klicka på **Ny** och sedan på **PySpark**.

    ![Skapa en ny Jupyter-anteckningsbok](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Skapa en ny Jupyter-anteckningsbok")
4. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Klicka på anteckningsbokens namn högst upp och ange ett trevligt namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Ange ett namn för anteckningsboken")
5. Du behöver inte uttryckligen skapa några kontexter eftersom du har skapat anteckningsboken med hjälp av PySpark-kerneln. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen. Du kan starta genom att importera de typer som krävs för det här scenariot. Klistra in följande kodavsnitt i en tom cell och tryck sedan på **SKIFT + RETUR**.

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. Skapa en RDD med loggen exempeldata redan finns på klustret. Du kan komma åt data i standardkontot för lagring som är associerade med klustret när **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. Hämta en exempellogg som anger att kontrollera att det föregående steget har slutförts.

        logs.take(5)

    Du bör se utdata som liknar följande:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analysera loggdata som använder en anpassad Python-bibliotek
1. Första par raderna innehåller informationen i huvudet och varje återstående rad matchar schemat som beskrivs i det sidhuvudet i resultatet ovan. Parsning av dessa loggar kan vara komplicerat. Så vi använder en anpassad Python-bibliotek (**iislogparser.py**) som gör att parsning av dessa loggar som är mycket enklare. Det här biblioteket är som standard ingår i Spark-kluster i HDInsight på **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Det här biblioteket är dock inte i den `PYTHONPATH` så att vi inte kan använda den med hjälp av en import-sats som `import iislogparser`. Om du vill använda det här biblioteket distribuera vi den till alla arbetarnoder. Kör följande kodavsnitt.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. `iislogparser`innehåller en funktion `parse_log_line` som returnerar `None` om en logg är en rubrikrad, och returnerar en instans av den `LogLine` klassen om det uppstår en rad i loggfilen. Använd den `LogLine` klassen för att extrahera loggen raderna från RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. Hämta några extraherade loggen rader att kontrollera att steget har slutförts.

       logLines.take(2)

   Resultatet bör likna följande:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. Den `LogLine` klass, i sin tur har vissa användbara metoder som `is_error()`, som returnerar om en loggpost har en felkod. Används för att beräkna antalet fel i loggen extraherade raderna och sedan logga alla fel på en annan fil.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   Du bör se utdata som liknar följande:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
4. Du kan också använda **Matplotlib** att konstruera en visualisering av data. Om du vill ta reda på orsaken begäranden som körs under lång tid kan du till exempel vill hitta filer som tar mest tid att fungera i genomsnitt.
   Kodfragmentet nedan hämtar de översta 25 resurser som tog de flesta tid att hantera en begäran.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   Du bör se utdata som liknar följande:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [(u'/blogposts/mvc4/step13.png', 197.5),
        (u'/blogposts/mvc2/step10.jpg', 179.5),
        (u'/blogposts/extractusercontrol/step5.png', 170.0),
        (u'/blogposts/mvc4/step8.png', 159.0),
        (u'/blogposts/mvcrouting/step22.jpg', 155.0),
        (u'/blogposts/mvcrouting/step3.jpg', 152.0),
        (u'/blogposts/linqsproc1/step16.jpg', 138.75),
        (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
        (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
        (u'/blogposts/nested/step2.jpg', 126.0),
        (u'/blogposts/adminpack/step1.png', 124.0),
        (u'/BlogPosts/datalistpaging/step2.png', 118.0),
        (u'/blogposts/mvc4/step35.png', 117.0),
        (u'/blogposts/mvcrouting/step2.jpg', 116.5),
        (u'/blogposts/aboutme/basketball.jpg', 109.0),
        (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
        (u'/blogposts/mvc4/step12.png', 106.0),
        (u'/blogposts/linq8/step0.jpg', 105.5),
        (u'/blogposts/mvc2/step18.jpg', 104.0),
        (u'/blogposts/mvc2/step11.jpg', 104.0),
        (u'/blogposts/mvcrouting/step1.jpg', 104.0),
        (u'/blogposts/extractusercontrol/step1.png', 103.0),
        (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
        (u'/blogposts/mvcrouting/step21.jpg', 101.0),
        (u'/blogposts/mvc4/step1.png', 98.0)]
5. Du kan också visa denna information i form av området. Som ett första steg att skapa en rityta Låt oss först skapa en tillfällig tabell **AverageTime**. Tabellen grupper loggarna av tid för att se om några ovanliga svarstidsspikar vid en viss tidpunkt.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. Sedan kan du köra följande SQL-fråga för att hämta alla poster i den **AverageTime** tabell.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   Den `%%sql` magic följt av `-o averagetime` säkerställer att utdata från frågan sparas lokalt på Jupyter-servern (vanligtvis headnode i klustret). Utdata sparas som en [Pandas](http://pandas.pydata.org/) dataframe med det angivna namnet **averagetime**.

   Du bör se utdata som liknar följande:

   ![SQL-frågan](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL frågeresultatet")

   Mer information om den `%%sql` magiskt, se [parametrar stöds med den %% sql magic](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
7. Du kan nu använda Matplotlib, ett bibliotek som används för att konstruera visualisering av data, för att skapa en rityta. Eftersom området måste skapas från lokalt beständiga **averagetime** dataframe, kodfragmentet måste börja med den `%%local` Magiskt tal. Detta säkerställer att koden körs lokalt på Jupyter-servern.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Du bör se utdata som liknar följande:

   ![Matplotlib utdata](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib utdata")
8. När du har kört appen bör du stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Då avslutas anteckningsboken och stängs ned.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](../hdinsight-apache-spark-eventhub-streaming.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
