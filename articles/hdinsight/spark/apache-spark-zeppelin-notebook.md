---
title: "Använda Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight | Microsoft Docs"
description: "Stegvisa instruktioner om hur du använder Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: e09283402efdaad97f5f5eefc0f02c937093f9b4
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Använda Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight

HDInsight Spark-kluster innehåller Zeppelin-anteckningsböcker som du kan använda för att köra Spark-jobb. I den här artikeln får du lära dig använda Zeppelin-anteckningsboken på ett HDInsight-kluster.

> [!NOTE]
> Zeppelin-anteckningsböcker är endast tillgängligt för Spark 1.6.3 i HDInsight 3.5 och Spark 2.1.0 i HDInsight 3,6.
>

**Krav:**

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Instruktioner finns i [skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Starta en Zeppelin-anteckningsbok
1. Klicka på bladet Spark-kluster **Klusterinstrumentpanel**, och klicka sedan på **Zeppelin anteckningsboken**. Ange administratörsautentiseringsuppgifterna för klustret om du uppmanas att göra det.
   
   > [!NOTE]
   > Du kan också nå Zeppelin-anteckningsboken för klustret genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. Skapa en ny anteckningsbok. Rubrikfönstret, klicka på **anteckningsboken**, och klicka sedan på **Skapa ny anteckning**.
   
    ![Skapa en ny anteckningsbok Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "skapa en ny anteckningsbok Zeppelin")
   
    Ange ett namn för anteckningsboken och klicka sedan på **skapa anteckning**.
3. Kontrollera också att rubriken anteckningsboken visar status för anslutna. Den betecknas med en grön punkt i det övre högra hörnet.
   
    ![Status för Zeppelin-anteckningsboken](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin anteckningsboken status")
4. Läs in exempeldata i en tillfällig tabell. När du skapar ett Spark-kluster i HDInsight, Exempeldatafilen, **hvac.csv**, kopieras till det associerade lagringskontot under **\HdiSamples\SensorSampleData\hvac**.
   
    Klistra in följande kodavsnitt i det tomma stycket som skapas som standard i ny anteckningsbok.
   
        %livy.spark
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
   
    Tryck på **SKIFT + RETUR** eller klicka på den **spela upp** knappen att köra kodavsnittet stycke. Status på höger styckets ska passera från klar, VÄNTANDE KÖRS till avslutad. Utdata visas längst ned i samma paragraph. Skärmbilden ser ut ungefär så här:
   
    ![Skapa en tillfällig tabell från rådata](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "skapa en tillfällig tabell från rådata")
   
    Du kan också ange ett namn för varje punkt. I det högra hörnet, klickar du på den **inställningar** ikonen och klickar sedan på **Visa rubrik**.
5. Du kan nu köra Spark SQL-uttryck på den **hvac** tabell. Klistra in följande fråga i ett nytt stycke. Frågan hämtar byggnad-ID och skillnaden mellan mål- och faktiska temperaturer för varje bygger på ett visst datum. Tryck på **SKIFT + RETUR**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    Den **% sql** uttrycket i början talar om den bärbara datorn att använda Livius Scala-tolken.
   
    Följande skärmbild visar utdata.
   
    ![Köra Spark SQL-uttryck med den bärbara datorn](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "köra Spark SQL-uttryck med den bärbara datorn")
   
     Klicka på Visningsalternativ (markerat med rektangel) om du vill växla mellan olika representationer för samma utdata. Klicka på **inställningar** att välja vilka consitutes nyckel och värden i utdata. Den här skärmbilden ovan använder **buildingID** som nyckeln och medelvärdet av **temp_diff** som värde.
6. Du kan också köra Spark SQL-uttryck med hjälp av variabler i frågan. Nästa utdrag visar hur du definierar en variabel, **Temp**, i frågan med de möjliga värdena som du vill fråga med. När du först kör frågan fylls automatiskt en listruta med de värden du angav för variabeln.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Klistra in följande kodutdrag i en ny punkt och trycker på **SKIFT + RETUR**. Följande skärmbild visar utdata.
   
    ![Köra Spark SQL-uttryck med den bärbara datorn](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "köra Spark SQL-uttryck med den bärbara datorn")
   
    För efterföljande frågor kan du välja ett nytt värde från listrutan och kör frågan igen. Klicka på **inställningar** att välja vilka consitutes nyckel och värden i utdata. Den här skärmbilden ovan använder **buildingID** som nyckel, medelvärdet av **temp_diff** som värde, och **targettemp** som gruppen.
7. Starta om Livius tolken om du vill avsluta programmet. Om du vill göra det, öppna tolkens inställningar genom att klicka på den inloggade användarnamn från det övre högra hörnet och klicka sedan på **tolken**.
   
    ![Starta tolken](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive utdata")
8. Bläddra till Livius tolkens inställningar och klickar sedan på **starta om**.
   
    ![Starta om Livius intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "starta om Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hur använder externa paket med den bärbara datorn?
Du kan konfigurera Zeppelin-anteckningsboken i Apache Spark-kluster i HDInsight (Linux) för att använda externa, community-bidragit paket som inte är inkluderade out-of-the-box i klustret. Du kan söka i [Maven databasen](http://search.maven.org/) för en fullständig lista över paket som är tillgängliga. Du kan också hämta en lista över tillgängliga paket från andra källor. Till exempel en fullständig lista över community-bidragit paket finns på [Spark paket](http://spark-packages.org/).

I den här artikeln visas hur du använder den [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paket med Jupyter-anteckningsboken.

1. Öppna tolkens inställningar. Klicka på den inloggade i användarnamn längst upp till höger och klicka sedan på **tolken**.
   
    ![Starta tolken](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive utdata")
2. Bläddra till Livius tolkens inställningar och klickar sedan på **redigera**.
   
    ![Ändra inställningarna för tolken](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "ändra tolkens inställningar")
3. Lägg till en ny nyckel kallas **livy.spark.jars.packages** och ange värdet i formatet `group:id:version`. Om du vill använda den [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketet, måste du ange värdet för nyckeln till `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Ändra inställningarna för tolken](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "ändra tolkens inställningar")
   
    Klicka på **spara** och starta sedan om Livius tolken.
4. **Tips**: Om du vill förstå hur du kommer till värdet för nyckeln som anges ovan, detta är hur.
   
    a. Leta upp paketet i Maven-databasen. Den här självstudiekursen kommer vi använde [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Samla in värden för från databasen, **GroupId**, **artefakt-ID**, och **Version**.
   
    ![Använda externa paket med Jupyter-anteckningsbok](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "använda externa paket med Jupyter-anteckningsbok")
   
    c. Sammanfoga tre värden, avgränsade med kolon (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Där sparas Zeppelin-anteckningsböcker
Zeppelin-anteckningsböcker sparas headnodes för klustret. Så om du tar bort klustret de bärbara datorerna tas bort även. Om du vill behålla dina anteckningsböcker för senare användning på andra kluster måste du exportera dem när du har kört jobben. Om du vill exportera en bärbar dator klickar du på den **exportera** ikon som visas i bilden nedan.

![Ladda ned anteckningsboken](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "ladda ned anteckningsboken")

Anteckningsboken sparas som en JSON-fil i din nedladdningsplatsen.

## <a name="livy-session-management"></a>Livius sessionshantering
När du kör kod punkt i anteckningsboken Zeppelin, skapas en ny session Livius i HDInsight Spark-kluster. Den här sessionen delas mellan alla Zeppelin-anteckningsböcker som du skapar. Om du av någon anledning Livius sessionen har avslutats (klustret omstart osv.), kan du inte köra jobb från Zeppelin-anteckningsboken.

I sådana fall måste du utföra följande steg innan du kan starta jobb som körs från en Zeppelin bärbar dator. 

1. Starta om Livius tolken från Zeppelin-anteckningsboken. Om du vill göra det, öppna tolkens inställningar genom att klicka på den inloggade användarnamn från det övre högra hörnet och klicka sedan på **tolken**.
   
    ![Starta tolken](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive utdata")
2. Bläddra till Livius tolkens inställningar och klickar sedan på **starta om**.
   
    ![Starta om Livius intepreter](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "starta om Zeppelin intepreter")
3. Kör en cell kod från en befintlig Zeppelin-anteckningsbok. Detta skapar en ny session Livius i HDInsight-klustret.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att bygga program för strömning i realtid](apache-spark-eventhub-streaming.md)
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







