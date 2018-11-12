---
title: Använda anpassade Maven-paket med Jupyter i Spark på Azure HDInsight
description: Stegvisa instruktioner om hur du konfigurerar tillgänglig Jupyter-anteckningsböcker med HDInsight Spark-kluster att använda anpassade Maven-paket.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: hrasheed
ms.openlocfilehash: 3d01f058101da9bbe790ef06ad7d78c556755bd6
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011603"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Använda externa paket med Jupyter notebooks i Apache Spark-kluster i HDInsight
> [!div class="op_single_selector"]
> * [Med cellfunktioner](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Med skriptåtgärder](apache-spark-python-package-installation.md)
>
>

Lär dig hur du konfigurerar en Jupyter notebook i Apache Spark-kluster i HDInsight för att använda externa, communityn har bidragit med **maven** paket som inte är inkluderat out-of the box i klustret. 

Du kan söka i [Maven databasen](http://search.maven.org/) för en fullständig lista över paket som är tillgängliga. Du kan också hämta en lista över tillgängliga paket från andra källor. Exempelvis kan en fullständig lista över communityn har bidragit med paket finns på [Spark paket](http://spark-packages.org/).

I den här artikeln får du lära dig hur du använder den [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paket med Jupyter-anteckningsboken.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande:

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Använda externa paket med Jupyter-anteckningsböcker
1. På startsidan i [Azure-portalen](https://portal.azure.com/) klickar du på panelen för ditt Spark-kluster (om du har fäst det på startsidan). Du kan också navigera till ditt kluster under **Bläddra bland alla** > **HDInsight-kluster**.   

1. Klicka på **Snabblänkar** på Spark-klusterbladet och sedan på **Jupyter Notebook** på **Klusterinstrumentpanel**-bladet. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.

    > [!NOTE]
    > Du kan också nå Jupyter Notebook för ditt kluster genom att öppna nedanstående URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

1. Skapa en ny anteckningsbok. Klicka på **New**, och klicka sedan på **Spark**.
   
    ![Skapa en ny Jupyter-anteckningsbok](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Skapa en ny Jupyter-anteckningsbok")

1. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Klicka på anteckningsbokens namn högst upp och ange ett trevligt namn.
   
    ![Ange ett namn för anteckningsboken](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Ange ett namn för anteckningsboken")

1. Du kommer att använda den `%%configure` magic att konfigurera den för att använda ett externt paket. I anteckningsböcker som använda externa paket, se till att du anropar den `%%configure` magic i den första kodcellen. Detta säkerställer att kerneln är konfigurerad för att använda paketet innan sessionen startar.

    >[!IMPORTANT] 
    >Om du glömmer att konfigurera kerneln i den första cellen, kan du använda den `%%configure` med den `-f` parametern, men som kommer starta om sessionen och alla ändringar går förlorade.

    | HDInsight-version | Kommando |
    |-------------------|---------|
    |För HDInsight 3.3 och HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | För HDInsight 3.5 och HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. Fragmentet ovan förväntar sig maven-koordinaterna för det externa paketet i Maven Central Repository. I det här kodfragmentet `com.databricks:spark-csv_2.10:1.4.0` är maven-koordinat för **spark-csv** paketet. Här är hur du konstruerar koordinaterna för ett paket.
   
    a. Leta upp paketet i Maven-centrallagret. Den här självstudien använder vi [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Samla in värden för från databasen, **GroupId**, **ArtifactId**, och **Version**. Kontrollera att de värden som du har samlat in matchar ditt kluster. I det här fallet använder vi en 2.10 Scala och Spark 1.4.0-paket, men du kan behöva välja olika versioner för lämpliga Scala och Spark-version i klustret. Du hittar den Scala-versionen i ditt kluster genom att köra `scala.util.Properties.versionString` på Spark Jupyter-kernel eller på Spark-submit. Du hittar den Spark-versionen i ditt kluster genom att köra `sc.version` i Jupyter-anteckningsböcker.
   
    ![Använda externa paket med Jupyter-anteckningsbok](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "använda externa paket med Jupyter-anteckningsbok")
   
    c. Sammanfoga tre värden, avgränsade med kolon (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

1. Kör kodcellen med den `%%configure` magic. Detta konfigurerar den underliggande Livy-sessionen om du vill använda paketet som du angav. I efterföljande cellerna i anteckningsboken, kan du nu använda paketet, enligt nedan.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    För HDInsight 3.6, bör du använda följande fragment.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Du kan sedan köra kodfragment, som visas nedan, för att visa data från den dataram som du skapade i föregående steg.
   
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
