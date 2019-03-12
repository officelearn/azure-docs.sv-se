---
title: Använda Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight
description: Stegvisa instruktioner om hur du använder Zeppelin-anteckningsböcker med Apache Spark-kluster på Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 019232308ec5fa6d735e4499c3fb5f3ac2727e2d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766402"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight

HDInsight Spark-kluster innehåller [Apache Zeppelin](https://zeppelin.apache.org/) anteckningsböcker som du kan använda för att köra [Apache Spark](https://spark.apache.org/) jobb. I den här artikeln får du lära dig hur du använder Zeppelin-anteckningsbok på ett HDInsight-kluster.

**Krav:**

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* URI-schemat för ditt kluster primär lagring. Detta skulle vara `wasb://` för Azure Blob Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage Gen1. Om säker överföring har aktiverats för Blob Storage eller Data Lake Storage Gen2, URI: N blir `wasbs://` eller `abfss://`respektive.  Se även [Kräv säker överföring i Azure Storage](../../storage/common/storage-require-secure-transfer.md) för mer information.

## <a name="launch-an-apache-zeppelin-notebook"></a>Starta en Apache Zeppelin-anteckningsbok

1. Från Spark-kluster **översikt**väljer **Zeppelin-anteckningsbok** från **Klusterinstrumentpaneler**. Ange administratörsautentiseringsuppgifterna för klustret.  

   > [!NOTE]  
   > Du kan också nå Zeppelin-anteckningsbok för ditt kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Skapa en ny anteckningsbok. Rubrikfönstret, navigera till **Notebook** > **Skapa ny anteckning**.

    ![Skapa en ny Zeppelin notebook](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "skapa en ny Zeppelin-anteckningsbok")

    Ange ett namn för anteckningsboken och välj sedan **skapa anteckning**.

3. Se till att rubriken notebook visar statusen ansluten. Den markeras med en grön punkt i det övre högra hörnet.

    ![Zeppelin notebook status](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notebook-status")

4. Läs in exempeldata i en tillfällig tabell. När du skapar ett Spark-kluster i HDInsight, Exempeldatafilen, `hvac.csv`, har kopierats till det associerade lagringskontot under `\HdiSamples\SensorSampleData\hvac`.

    Klistra in följande kodfragment i det tomma stycket som skapas som standard i denna nya notebook.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Tryck på **SKIFT + RETUR** eller klicka på den **spela upp** knappen för punkt till kör fragment. Status på höger styckets bör vidare från är klart, väntar, KÖRS till avslutad. Utdata som visas längst ned på samma stycke. Skärmbilden ser ut som följande:

    ![Skapa en tillfällig tabell från rådata](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "skapa en tillfällig tabell från rådata")

    Du kan också ange en rubrik i varje stycke. I det högra hörnet styckets väljer den **inställningar** ikonen (kugghjul) och välj sedan **visa rubriken**.  

    > [!NOTE]  
    > % spark2 tolk stöds inte i Zeppelin-anteckningsböcker i alla HDInsight-versioner och % sh tolk stöds inte från HDInsight 4.0 och senare.

5. Du kan nu köra Spark SQL-uttryck på den `hvac` tabell. Klistra in följande fråga i ett nytt stycke. Frågan hämtar ID för att bygga och skillnaden mellan mål- och faktiska temperaturer för varje att bygga på ett speciellt datum. Tryck på **SKIFT + RETUR**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    Den **% sql** instruktion i början talar om anteckningsboken för att använda Livy Scala-tolk.

6. Välj den **stapeldiagram** ikon för att ändra visningen.  **inställningar för**, som visas när du har valt **stapeldiagram**, kan du välja **nycklar**, och **värden**.  I följande skärmbild visas utdata.

    ![Kör en Spark SQL-instruktion som använder anteckningsboken](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "kör en Spark SQL-instruktion som använder anteckningsboken")

7. Du kan också köra Spark SQL-instruktioner med hjälp av variabler i frågan. Nästa kodfragmentet visar hur du definierar en variabel, `Temp`, i frågan med möjliga värden du vill fråga med. Första gången du kör frågan, fylls automatiskt en listruta med de värden du angav för variabeln.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Klistra in kodfragmentet i ett nytt stycke och trycka på **SKIFT + RETUR**. Välj sedan **65** från den **Temp** listrutan ist. 

8. Välj den **stapeldiagram** ikon för att ändra visningen.  Välj sedan **inställningar** och gör följande ändringar:

    * **Grupper:**  Lägg till **targettemp**.  
    * **Värden:** 1. Ta bort **datum**.  2. Lägg till **temp_diff**.  3.  Ändra aggregator från **SUMMAN** till **Genomsnittlig**.  

    I följande skärmbild visas utdata.

    ![Kör en Spark SQL-instruktion som använder anteckningsboken](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "kör en Spark SQL-instruktion som använder anteckningsboken")

9. Starta om Livy-tolk om du vill avsluta programmet. Om du vill göra det, öppna tolk inställningar genom att välja den inloggade användarens namn i det övre högra hörnet och välj sedan **tolk**.  

    ![Starta tolk](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")

10. Bläddra till **livy**, och välj sedan **starta om**.  Välj **OK** i Kommandotolken.

    ![Starta om Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "starta om Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hur jag för att använda externa paket med anteckningsboken?
Du kan konfigurera Zeppelin notebook i Apache Spark-kluster i HDInsight för att använda externa, communityn har bidragit med paket som inte är inkluderade out-of the box i klustret. Du kan söka i [Maven databasen](https://search.maven.org/) för en fullständig lista över paket som är tillgängliga. Du kan också hämta en lista över tillgängliga paket från andra källor. Exempelvis kan en fullständig lista över communityn har bidragit med paket finns på [Spark paket](https://spark-packages.org/).

I den här artikeln visas hur du använder den [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paket med Jupyter-anteckningsboken.

1. Öppna tolk inställningarna. I det övre högra hörnet, Välj den inloggade användarens namn och välj sedan **tolk**.

    ![Starta tolk](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")

2. Bläddra till **livy**och välj sedan **redigera**.

    ![Ändra inställningarna för tolk](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "ändra tolk inställningar")

3. Lägg till en ny nyckel som heter `livy.spark.jars.packages`, och ange värdet i formatet `group:id:version`. Om du vill använda den [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketet, måste du ange värdet för nyckeln till `com.databricks:spark-csv_2.10:1.4.0`.

    ![Ändra inställningarna för tolk](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "ändra tolk inställningar")

    Välj **spara** och starta sedan om Livy-tolk.

4. Om du vill lära dig att komma till värdet för nyckeln som anges ovan, här är hur.
   
    a. Leta upp paketet i Maven-centrallagret. Den här självstudien har vi använt [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Samla in värden för från databasen, **GroupId**, **ArtifactId**, och **Version**.
   
    ![Använda externa paket med Jupyter-anteckningsbok](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "använda externa paket med Jupyter-anteckningsbok")
   
    c. Sammanfoga tre värden, avgränsade med kolon (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Där sparas Zeppelin-anteckningsböcker
Zeppelin-anteckningsböcker sparas i klustrets huvudnoder. Så om du tar bort klustret raderas de bärbara datorerna samt. Om du vill bevara dina anteckningsböcker för senare användning i andra kluster måste du exportera dem när du har kört jobben. Om du vill exportera en bärbar dator, Välj den **exportera** ikonen som visas i bilden nedan.

![Hämta anteckningsboken](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "hämta anteckningsboken")

Anteckningsboken sparas som en JSON-fil i din nedladdningsplatsen.

## <a name="livy-session-management"></a>Livy sessionshantering
När du kör det första stycket kod i din Zeppelin-anteckningsbok, skapas en ny session Livy i ditt HDInsight Spark-kluster. Den här sessionen delas mellan alla Zeppelin-anteckningsböcker som du skapar senare. Om du av någon anledning Livy sessionen avslutades (kluster omstart, osv.), kan du inte köra jobb från Zeppelin-anteckningsbok.

I sådana fall måste du utföra följande steg innan du kan börja köra jobb från en Zeppelin-anteckningsbok.  

1. Starta om Livy-tolk från Zeppelin-anteckningsbok. Om du vill göra det, öppna tolk inställningar genom att välja den inloggade användarens namn i det övre högra hörnet och välj sedan **tolk**.

    ![Starta tolk](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")

2. Bläddra till **livy**och välj sedan **starta om**.

    ![Starta om Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "starta om Zeppelin intepreter")

3. Kör en kodcell från en befintlig Zeppelin-anteckningsbok. Detta skapar en ny session Livy i HDInsight-klustret.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark på Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: Utföra interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: Använda Spark i HDInsight för att förutse matinspektionsresultat](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använda HDInsight Tools-Plugin för IntelliJ IDEA till att skapa och skicka Apache Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-Plugin för IntelliJ IDEA till att felsöka Apache Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernlar som är tillgängliga för Jupyter notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
