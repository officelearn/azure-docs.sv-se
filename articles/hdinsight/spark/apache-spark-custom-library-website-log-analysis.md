---
title: Analysera webbplats loggar med python-bibliotek i Spark – Azure
description: Den här antecknings boken visar hur du analyserar loggdata med ett anpassat bibliotek med Spark på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 6d23e8cfa8d20169d2b63723138b60dafb1069de
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146985"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analysera webbplats loggar med ett anpassat python-bibliotek med Apache Spark kluster i HDInsight

Den här antecknings boken visar hur du analyserar logg data med hjälp av ett anpassat bibliotek med Apache Spark på HDInsight. Det anpassade biblioteket som vi använder är ett Python-bibliotek med namnet **iislogparser.py**.

> [!TIP]  
> Den här artikeln är också tillgänglig som en Jupyter Notebook på ett Spark-kluster (Linux) som du skapar i HDInsight. Med Notebook-upplevelsen kan du köra python-kodfragmenten från själva antecknings boken. Om du vill utföra artikeln i en bärbar dator skapar du ett Spark-kluster, startar en Jupyter`https://CLUSTERNAME.azurehdinsight.net/jupyter`Notebook () och kör sedan antecknings boken **med Spark med hjälp av ett anpassat bibliotek. ipynb** under mappen **PySpark** .

**Krav:**

Du måste ha följande:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Spara rå data som en RDD
I det här avsnittet använder vi den [Jupyter](https://jupyter.org) Notebook som är associerad med ett Apache Spark kluster i HDInsight för att köra jobb som bearbetar dina rå data och spara dem som en Hive-tabell. Exempel data är en. csv-fil (HVAC. csv) som är tillgänglig i alla kluster som standard.

När dina data har sparats som en Apache Hive tabell i nästa avsnitt kommer vi att ansluta till Hive-tabellen med BI-verktyg som Power BI och Tableau.

1. På startsidan i [Azure Portal](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.

2. Från Spark-klusterbladet, klickar du på **Klusterinstrumentpanel** och sedan på **Jupyter Notebook**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

   > [!NOTE]
   > Du kan också nå Jupyter Notebook för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Skapa en ny anteckningsbok. Klicka på **Ny** och sedan på **PySpark**.

    ![Skapa en ny Apache Jupyter-anteckningsbok](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Skapa en ny Jupyter-anteckningsbok")

4. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Klicka på anteckningsbokens namn högst upp och ange ett trevligt namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Ange ett namn för anteckningsboken")
5. Du behöver inte uttryckligen skapa några kontexter eftersom du har skapat anteckningsboken med hjälp av PySpark-kerneln. Spark- och Hive-kontexterna skapas automatiskt för dig när du kör den första kodcellen. Du kan börja med att importera de typer som krävs för det här scenariot. Klistra in följande kodfragment i en tom cell och tryck sedan på **SKIFT+RETUR**.

        from pyspark.sql import Row
        from pyspark.sql.types import *

6. Skapa en RDD med hjälp av de exempel logg data som redan är tillgängliga i klustret. Du kan komma åt data i det standard lagrings konto som är associerat med klustret på **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')

7. Hämta en exempel logg uppsättning för att kontrol lera att föregående steg har slutförts.

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

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analysera loggdata med ett anpassat python-bibliotek

1. I utdata ovan inkluderar de första raderna rubrik informationen och varje återstående rad matchar schemat som beskrivs i rubriken. Det kan vara svårt att parsa sådana loggar. Vi använder därför ett anpassat python-bibliotek (**iislogparser.py**) som gör det mycket enklare att parsa dessa loggar. Som standard ingår det här biblioteket i ditt Spark-kluster i HDInsight på **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Det här biblioteket är dock inte i `PYTHONPATH` så att vi inte kan använda det med hjälp av en import instruktion som. `import iislogparser` För att kunna använda det här biblioteket måste vi distribuera det till alla arbetsnoder. Kör följande kodfragment.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')

1. `iislogparser`tillhandahåller en funktion `parse_log_line` som returnerar `None` om en logg rad är en rubrik rad och `LogLine` returnerar en instans av klassen om den påträffar en logg rad. `LogLine` Använd klassen för att endast extrahera logg rader från RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()

1. Hämta några extraherade logg rader för att verifiera att steget har slutförts.

       logLines.take(2)

   Resultatet bör likna följande:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]

1. Klassen, i sin tur, har en del användbara metoder, `is_error()`till exempel, som returnerar om en loggpost innehåller en felkod. `LogLine` Använd detta för att beräkna antalet fel i de extraherade logg raderna och logga sedan alla fel till en annan fil.

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
1. Du kan också använda **matplotlib** för att skapa en visualisering av data. Om du till exempel vill isolera orsaken till begär Anden som körs under en längre tid kanske du vill hitta de filer som tar mest tid att betjäna i genomsnitt.
   I kodfragmentet nedan hämtas de 25 främsta resurser som tog mest tid att betjäna en begäran.

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

1. Du kan också presentera den här informationen i form av rityta. Låt oss börja med att skapa en tillfällig tabell **AverageTime**, som ett första steg för att skapa en rityta. I tabellen grupperas loggarna efter tid för att se om det fanns några toppar vid en viss tidpunkt vid några ovanliga svar.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

1. Du kan sedan köra följande SQL-fråga för att hämta alla poster i tabellen **AverageTime** .

       %%sql -o averagetime
       SELECT * FROM AverageTime

   Magic följt av `-o averagetime` ser till att utdata från frågan sparas lokalt på Jupyter-servern (vanligt vis huvudnoden i klustret). `%%sql` Utdata sparas som en [Pandas](https://pandas.pydata.org/) -dataframe med det angivna namnet **averagetime**.

   Du bör se utdata som liknar följande:

   ![HDInsight Jupyter SQL qyery-utdata](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-frågans utdata")

   Mer information om `%%sql` Magic finns i [parametrar som stöds med SQL-Magic%%](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Nu kan du använda matplotlib, ett bibliotek som används för att konstruera visualisering av data, för att skapa en rityta. Eftersom området måste skapas från det lokalt sparade **averagetime** -dataframe måste kodfragmentet börja med `%%local` Magic. Detta säkerställer att koden körs lokalt på Jupyter-servern.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Du bör se utdata som liknar följande:

   ![analys ritning för Apache Spark-webblogg](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib-utdata")

1. När du har kört appen bör du stänga ned anteckningsboken för att frigöra resurser. Du gör det genom att klicka på **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Då avslutas anteckningsboken och stängs ned.

## <a name="seealso"></a>Se även
* [: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda spark i HDInsight för analys av bygg temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använd spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använd HDInsight tools-plugin för IntelliJ idé för att skapa och skicka Apache Spark Scala-program](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels tillgängliga för Jupyter Notebook i Apache Spark kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
