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
ms.date: 02/21/2018
ms.openlocfilehash: b4c79ada0a243d50aad64600de6dfdfebd59b9da
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005844"
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Använda Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight

HDInsight Spark-kluster innehåller Zeppelin-anteckningsböcker som du kan använda för att köra Spark-jobb. I den här artikeln får du lära dig hur du använder Zeppelin-anteckningsbok på ett HDInsight-kluster.

**Krav:**

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Starta en Zeppelin-anteckningsbok
1. Spark-klusterbladet, klickar du på **Klusterinstrumentpanel**, och klicka sedan på **Zeppelin Notebook**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.
   
   > [!NOTE]
   > Du kan också nå Zeppelin-anteckningsbok för ditt kluster genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
1. Skapa en ny anteckningsbok. Huvud-fönstret klickar du på **Notebook**, och klicka sedan på **Skapa ny anteckning**.
   
    ![Skapa en ny Zeppelin notebook](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "skapa en ny Zeppelin-anteckningsbok")
   
    Ange ett namn för anteckningsboken och klicka sedan på **skapa anteckning**.
1. Kontrollera också att rubriken notebook visar statusen ansluten. Den markeras med en grön punkt i det övre högra hörnet.
   
    ![Zeppelin notebook status](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notebook-status")
1. Läs in exempeldata i en tillfällig tabell. När du skapar ett Spark-kluster i HDInsight, Exempeldatafilen, **hvac.csv**, har kopierats till det associerade lagringskontot under **\HdiSamples\SensorSampleData\hvac**.
   
    Klistra in följande kodfragment i det tomma stycket som skapas som standard i denna nya notebook.
   
        %livy2.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
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
   
    Tryck på **SKIFT + RETUR** eller klicka på den **spela upp** knappen för punkt till kör fragment. Status på höger styckets bör vidare från är klart, väntar, KÖRS till avslutad. Utdata som visas längst ned på samma stycke. Skärmbilden ser ut som följande:
   
    ![Skapa en tillfällig tabell från rådata](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "skapa en tillfällig tabell från rådata")
   
    Du kan också ange en rubrik i varje stycke. I det högra hörnet, klickar du på den **inställningar** ikon och klicka sedan på **visa rubriken**.

> [!NOTE]
> % spark2 tolk stöds inte i Zeppelin-anteckningsböcker i alla HDInsight-versioner och % sh tolk stöds inte från HDInsight 4.0 och senare.
>

1. Du kan nu köra Spark SQL-uttryck på den **hvac** tabell. Klistra in följande fråga i ett nytt stycke. Frågan hämtar ID för att bygga och skillnaden mellan mål- och faktiska temperaturer för varje att bygga på ett speciellt datum. Tryck på **SKIFT + RETUR**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    Den **% sql** instruktion i början talar om anteckningsboken för att använda Livy Scala-tolk.
   
    I följande skärmbild visas utdata.
   
    ![Kör en Spark SQL-instruktion som använder anteckningsboken](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "kör en Spark SQL-instruktion som använder anteckningsboken")
   
     Klicka på visningsalternativen (markerat i rektangeln) om du vill växla mellan olika representationer för samma utdata. Klicka på **inställningar** att välja vilka consitutes nyckel och värden i utdata. Den här skärmbilden ovan använder **buildingID** som nyckeln och medelvärdet av **temp_diff** som värde.
1. Du kan också köra Spark SQL-instruktioner med hjälp av variabler i frågan. Nästa kodfragmentet visar hur du definiera en variabel **Temp**, i frågan med möjliga värden du vill fråga med. Första gången du kör frågan, fylls automatiskt en listruta med de värden du angav för variabeln.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Klistra in kodfragmentet i ett nytt stycke och trycka på **SKIFT + RETUR**. I följande skärmbild visas utdata.
   
    ![Kör en Spark SQL-instruktion som använder anteckningsboken](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "kör en Spark SQL-instruktion som använder anteckningsboken")
   
    För efterföljande frågor kan du välja ett nytt värde från listrutan och kör frågan igen. Klicka på **inställningar** att välja vilka consitutes nyckel och värden i utdata. Den här skärmbilden ovan använder **buildingID** som nyckel, medelvärdet av **temp_diff** som värde, och **targettemp** som gruppen.
1. Starta om Livy-tolk om du vill avsluta programmet. Du gör detta genom att öppna tolk inställningar genom att klicka på den inloggade användarens namn i det övre högra hörnet och klicka sedan på **tolk**.
   
    ![Starta tolk](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")
1. Bläddra till Livy tolk inställningar och klickar sedan på **starta om**.
   
    ![Starta om Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "starta om Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hur jag för att använda externa paket med anteckningsboken?
Du kan konfigurera Zeppelin notebook i Apache Spark-kluster i HDInsight (Linux) om du vill använda externa, communityn har bidragit med paket som inte är inkluderade out-of the box i klustret. Du kan söka i [Maven databasen](http://search.maven.org/) för en fullständig lista över paket som är tillgängliga. Du kan också hämta en lista över tillgängliga paket från andra källor. Exempelvis kan en fullständig lista över communityn har bidragit med paket finns på [Spark paket](http://spark-packages.org/).

I den här artikeln visas hur du använder den [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paket med Jupyter-anteckningsboken.

1. Öppna tolk inställningarna. I det övre högra hörnet, klickar du på den inloggade användarens namn och klicka sedan på **tolk**.
   
    ![Starta tolk](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")
1. Bläddra till Livy tolk inställningar och klickar sedan på **redigera**.
   
    ![Ändra inställningarna för tolk](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "ändra tolk inställningar")
1. Lägg till en ny nyckel kallas **livy.spark.jars.packages** och ange värdet i formatet `group:id:version`. Om du vill använda den [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketet, måste du ange värdet för nyckeln till `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Ändra inställningarna för tolk](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "ändra tolk inställningar")
   
    Klicka på **spara** och starta sedan om Livy-tolk.
1. **Tips**: Om du vill lära dig att komma till värdet för nyckeln som anges ovan, här är hur.
   
    a. Leta upp paketet i Maven-centrallagret. Den här självstudien har vi använt [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Samla in värden för från databasen, **GroupId**, **ArtifactId**, och **Version**.
   
    ![Använda externa paket med Jupyter-anteckningsbok](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "använda externa paket med Jupyter-anteckningsbok")
   
    c. Sammanfoga tre värden, avgränsade med kolon (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Där sparas Zeppelin-anteckningsböcker
Zeppelin-anteckningsböcker sparas i klustrets huvudnoder. Så om du tar bort klustret raderas de bärbara datorerna samt. Om du vill bevara dina anteckningsböcker för senare användning i andra kluster måste du exportera dem när du har kört jobben. Om du vill exportera en bärbar dator, klickar du på den **exportera** ikonen som visas i bilden nedan.

![Hämta anteckningsboken](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "hämta anteckningsboken")

Anteckningsboken sparas som en JSON-fil i din nedladdningsplatsen.

## <a name="livy-session-management"></a>Livy sessionshantering
När du kör det första stycket kod i din Zeppelin-anteckningsbok, skapas en ny session Livy i ditt HDInsight Spark-kluster. Den här sessionen delas mellan alla Zeppelin-anteckningsböcker som du skapar senare. Om du av någon anledning Livy sessionen avslutades (kluster omstart, osv.), kan du inte köra jobb från Zeppelin-anteckningsbok.

I sådana fall måste du utföra följande steg innan du kan börja köra jobb från en Zeppelin-anteckningsbok. 

1. Starta om Livy-tolk från Zeppelin-anteckningsbok. Du gör detta genom att öppna tolk inställningar genom att klicka på den inloggade användarens namn i det övre högra hörnet och klicka sedan på **tolk**.
   
    ![Starta tolk](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")
1. Bläddra till Livy tolk inställningar och klickar sedan på **starta om**.
   
    ![Starta om Livy intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "starta om Zeppelin intepreter")
1. Kör en kodcell från en befintlig Zeppelin-anteckningsbok. Detta skapar en ny session Livy i HDInsight-klustret.

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
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att felsöka Spark-program via fjärranslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 







