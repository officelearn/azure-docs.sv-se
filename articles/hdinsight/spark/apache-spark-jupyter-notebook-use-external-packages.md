---
title: "Använda anpassade Maven-paket med Jupyter i Spark på Azure HDInsight | Microsoft Docs"
description: "Stegvisa instruktioner om hur du konfigurerar tillgängliga Jupyter-anteckningsböcker med HDInsight Spark-kluster att använda anpassade Maven-paket."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 93b29480b51f7a664411ffa92ed07c26b2ebafca
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Använda externa paket med Jupyter notebooks i Apache Spark-kluster i HDInsight
> [!div class="op_single_selector"]
> * [Med hjälp av cell Magiskt tal](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Med skriptåtgärder](apache-spark-python-package-installation.md)
>
>

Lär dig hur du konfigurerar en Jupyter-anteckningsbok i Apache Spark-kluster i HDInsight för att använda externa, community-bidragit **maven** paket som inte ingår out box i klustret. 

Du kan söka i [Maven databasen](http://search.maven.org/) för en fullständig lista över paket som är tillgängliga. Du kan också hämta en lista över tillgängliga paket från andra källor. Till exempel en fullständig lista över community-bidragit paket finns på [Spark paket](http://spark-packages.org/).

I den här artikeln får du lära dig hur du använder den [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paket med Jupyter-anteckningsboken.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande:

* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Använda externa paket med Jupyter-anteckningsböcker
1. På startsidan i [Azure-portalen](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.   

2. Klicka på **Snabblänkar** på Spark-klusterbladet och sedan på **Jupyter Notebook** på **Klusterinstrumentpanel**-bladet. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    > [!NOTE]
    > Du kan också nå Jupyter Notebook för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

3. Skapa en ny anteckningsbok. Klicka på **ny**, och klicka sedan på **Spark**.
   
    ![Skapa en ny Jupyter-anteckningsbok](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Skapa en ny Jupyter-anteckningsbok")

4. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Klicka på anteckningsbokens namn högst upp och ange ett trevligt namn.
   
    ![Ange ett namn för anteckningsboken](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Ange ett namn för anteckningsboken")

5. Du kommer att använda den `%%configure` Magiskt tal för att konfigurera anteckningsboken för att använda ett externa paket. Kontrollera att du anropar i bärbara datorer som använder externa paket, det `%%configure` magiskt i den första kodcellen. Detta säkerställer att kerneln är konfigurerad för att använda paketet innan sessionen startar.

    >[!IMPORTANT] 
    >Om du glömmer att konfigurera kärnan i den första cellen kan du använda den `%%configure` med den `-f` parameter, men som kommer att starta om sessionen och alla pågår kommer att förloras.

    | HDInsight-version | Kommando |
    |-------------------|---------|
    |För HDInsight 3.3 och HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | För HDInsight 3.5 och HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

6. Fragment ovan förväntar maven koordinaterna för det externa paketet i Maven centrallager. I det här kodstycket `com.databricks:spark-csv_2.10:1.4.0` är maven-koordinaten för **spark-csv** paketet. Här är hur du skapar koordinaterna för ett paket.
   
    a. Leta upp paketet i Maven-databasen. Den här självstudiekursen kommer vi att använda [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Samla in värden för från databasen, **GroupId**, **artefakt-ID**, och **Version**. Kontrollera att du samlar in värdena matchar ditt kluster. I det här fallet använder du en Scala 2.10 och Spark 1.4.0 paket, men du kan behöva välja olika versioner för lämplig Scala eller Spark-version i klustret. Du kan hitta Scala-version på ditt kluster genom att köra `scala.util.Properties.versionString` på Spark Jupyter-kernel eller skicka Spark. Du kan hitta Spark-version på ditt kluster genom att köra `sc.version` i Jupyter-anteckningsböcker.
   
    ![Använda externa paket med Jupyter-anteckningsbok](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "använda externa paket med Jupyter-anteckningsbok")
   
    c. Sammanfoga tre värden, avgränsade med kolon (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

7. Kör kodcellen med den `%%configure` Magiskt tal. Den underliggande Livius sessionen om du vill använda paketet som du har angett kommer att konfigurera. I efterföljande celler i anteckningsboken kan du nu använda paketet, enligt nedan.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    För HDInsight 3,6 bör du använda följande kodavsnitt.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

8. Sedan kan du köra kodavsnitt som visas nedan, för att visa data från dataframe som du skapade i föregående steg.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda externa python-paket med Jupyter notebooks i Apache Spark-kluster i HDInsight Linux](apache-spark-python-package-installation.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
