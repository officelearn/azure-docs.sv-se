---
title: Använd anpassade Maven-paket med Jupyter i Spark - Azure HDInsight
description: Steg-för-steg-instruktioner om hur du konfigurerar Jupyter-anteckningsböcker som är tillgängliga med HDInsight Spark-kluster för att använda anpassade Maven-paket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cec94b2ecb18bc9e8cceb24a21967a3c829d78a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561740"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Använda externa paket med Jupyter-anteckningsböcker i Apache Spark-kluster på HDInsight

> [!div class="op_single_selector"]
> * [Använda cellmagi](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Använda skriptåtgärd](apache-spark-python-package-installation.md)

Lär dig hur du konfigurerar en [Jupyter Notebook](https://jupyter.org/) i Apache Spark-kluster på HDInsight för att använda externa, community-bidragna Apache **maven-paket** som inte ingår direkt i klustret.

Du kan söka i [Maven-databasen](https://search.maven.org/) efter en komplett lista över tillgängliga paket. Du kan också få en lista över tillgängliga paket från andra källor. En komplett lista över paket med community-bidrag finns till exempel på [Spark Packages](https://spark-packages.org/).

I den här artikeln får du lära dig hur du använder [spark-csv-paketet](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) med jupyter-anteckningsboken.

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Kunskaper om Jupyter Notebooks med Spark på HDInsight. Mer information finns i [Läsa in data och köra frågor med Apache Spark på HDInsight](./apache-spark-load-data-run-query.md).

* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta skulle `wasb://` vara för `abfs://` Azure Storage, för `adl://` Azure Data Lake Storage Gen2 eller för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Azure Storage eller Data `wasbs://` Lake `abfss://`Storage Gen2, skulle URI vara eller , respektive Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Använda externa paket med Jupyter-anteckningsböcker

1. Navigera `https://CLUSTERNAME.azurehdinsight.net/jupyter` till `CLUSTERNAME` var är namnet på Spark-klustret.

1. Skapa en ny anteckningsbok. Välj **Ny**och välj sedan **Spark**.

    ![Skapa en ny spark Jupyter-anteckningsbok](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Skapa en ny Jupyter-anteckningsbok")

1. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Välj anteckningsboksnamnet högst upp och ange ett eget namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Ange ett namn för anteckningsboken")

1. Du använder magin `%%configure` för att konfigurera anteckningsboken så att den använder ett externt paket. I anteckningsböcker som använder externa paket, `%%configure` se till att du kallar magin i den första kodcellen. Detta säkerställer att kärnan är konfigurerad för att använda paketet innan sessionen startar.

    >[!IMPORTANT]  
    >Om du glömmer att konfigurera kärnan i den `%%configure` första `-f` cellen kan du använda parametern med parametern, men som startar om sessionen och alla förlopp går förlorade.

    | HDInsight-version | Kommando |
    |-------------------|---------|
    | För HDInsight 3.5 och HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |För HDInsight 3.3 och HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Kodavsnittet ovan förväntar sig maven-koordinaterna för det externa paketet i Maven Central Repository. I detta utdrag, `com.databricks:spark-csv_2.11:1.5.0` är maven koordinaten för **spark-csv** paket. Så här skapar du koordinaterna för ett paket.

    a. Leta reda på paketet i Maven-arkivet. För den här artikeln använder vi [spark-csv](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Samla in värdena för **GroupId,** **ArtifactId**och **Version**från databasen . Kontrollera att de värden du samlar in stämmer överens med klustret. I det här fallet använder vi ett Scala 2.11- och Spark 1.5.0-paket, men du kan behöva välja olika versioner för lämplig Scala- eller Spark-version i klustret. Du kan ta reda på Scala-versionen i klustret genom att köras `scala.util.Properties.versionString` på Spark Jupyter-kärnan eller på Spark-inlämnad. Du kan ta reda på Spark-versionen i klustret genom att köra `sc.version` på Jupyter-anteckningsböcker.

    ![Använda externa paket med Jupyter notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Använda externa paket med Jupyter notebook")

    c. Sammanfoga de tre värdena, åtskilda av ett kolon (**:**).

        com.databricks:spark-csv_2.11:1.5.0

1. Kör kodcellen `%%configure` med magin. Detta kommer att konfigurera den underliggande Livy-sessionen för att använda paketet du angav. I de efterföljande cellerna i anteckningsboken kan du nu använda paketet, som visas nedan.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    För HDInsight 3.4 och lägre bör du använda följande utdrag.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Du kan sedan köra kodavsnitten, som visas nedan, för att visa data från dataramen som du skapade i föregående steg.

        df.show()
   
        df.select("Time").count()

## <a name="see-also"></a><a name="seealso"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda externa pythonpaket med Jupyter-anteckningsböcker i Apache Spark-kluster på HDInsight Linux](apache-spark-python-package-installation.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight Tools Plugin för IntelliJ IDEA för att felsöka Apache Spark-program på distans](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster på HDInsight](apache-spark-zeppelin-notebook.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
