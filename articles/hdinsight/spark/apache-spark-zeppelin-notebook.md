---
title: Zeppelin Notebook & Apache Spark-kluster – Azure HDInsight
description: Stegvisa instruktioner om hur du använder Zeppelin-anteckningsböcker med Apache Spark-kluster på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: b50baa41c4758ba3c0a405df3f54b4ea2f3f2d13
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241289"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Använda Apache Zeppelin-anteckningsböcker med Apache Spark kluster i Azure HDInsight

HDInsight Spark-kluster innehåller [Apache Zeppelin](https://zeppelin.apache.org/) -anteckningsböcker som du kan använda för att köra [Apache Spark](https://spark.apache.org/) -jobb. I den här artikeln får du lära dig hur du använder Zeppelin Notebook i ett HDInsight-kluster.

**Krav:**

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* URI-schemat för klustrets primära lagring. Detta är `wasb://` för Azure Blob Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage Gen1. Om säker överföring har Aktiver ATS för Blob Storage blir URI: n `wasbs://`.  Se även [Kräv säker överföring i Azure Storage](../../storage/common/storage-require-secure-transfer.md) för mer information.

## <a name="launch-an-apache-zeppelin-notebook"></a>Starta en Apache Zeppelin-anteckningsbok

1. I **översikten**Spark-kluster väljer du **Zeppelin Notebook** från **kluster instrument paneler**. Ange administratörs behörighet för klustret.  

   > [!NOTE]  
   > Du kan också komma åt Zeppelin Notebook för klustret genom att öppna följande URL i webbläsaren. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Skapa en ny anteckningsbok. I fönstret sidhuvud navigerar du till **Notebook**  > **Skapa ny anteckning**.

    ![Skapa en ny Zeppelin-anteckningsbok](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Skapa en ny Zeppelin-anteckningsbok")

    Ange ett namn för antecknings boken och välj sedan **Skapa anteckning**.

3. Se till att antecknings bokens huvud visar en ansluten status. Den betecknas av en grön prick i det övre högra hörnet.

    ![Status för Zeppelin Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Status för Zeppelin Notebook")

4. Läs in exempeldata i en tillfällig tabell. När du skapar ett Spark-kluster i HDInsight kopieras exempel data filen `hvac.csv` till det associerade lagrings kontot under `\HdiSamples\SensorSampleData\hvac`.

    I det tomma stycket som skapas som standard i den nya antecknings boken klistrar du in följande kodfragment.

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

    Tryck på **SKIFT + RETUR** eller klicka på **uppspelnings** knappen för stycket för att köra kodfragmentet. Status i det högra hörnet av stycket bör förloppet från klart, väntar, som körs till SLUTFÖRt. Utdata visas längst ned i samma stycke. Skärm bilden ser ut ungefär så här:

    ![Skapa en tillfällig tabell från rå data](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Skapa en tillfällig tabell från rå data")

    Du kan också ange en rubrik för varje stycke. I det högra hörnet av stycket väljer du **inställnings** ikonen (Sprocket) och väljer sedan **Visa rubrik**.  

    > [!NOTE]  
    > % spark2-tolken stöds inte i Zeppelin Notebook-versioner i alla HDInsight-versioner och% sh-tolken stöds inte från HDInsight 4,0 och senare.

5. Du kan nu köra Spark SQL-uttryck i `hvac` tabellen. Klistra in följande fråga i ett nytt stycke. Frågan hämtar Bygg-ID och skillnaden mellan målet och de faktiska temperaturerna för varje byggnad vid ett visst datum. Tryck på **SKIFT + RETUR**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    **% SQL** -instruktionen i början talar om för antecknings boken att använda livy Scala-tolken.

6. Välj **stapeldiagrammet** för att ändra visningen.  **Inställningar**som visas när du har valt **stapeldiagram**kan du välja **nycklar**och **värden**.  På följande skärm bild visas utdata.

    ![Köra ett Spark SQL-uttryck med hjälp av notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Köra ett Spark SQL-uttryck med hjälp av notebook1")

7. Du kan också köra Spark SQL-uttryck med variabler i frågan. Nästa fragment visar hur du definierar en variabel, `Temp`, i frågan med de möjliga värden som du vill fråga efter. Första gången du kör frågan fylls en listruta automatiskt med de värden som du har angett för variabeln.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Klistra in det här kodfragmentet i ett nytt stycke och tryck på **SKIFT + RETUR**. Välj sedan **65** i list rutan **Temp** . 

8. Välj **stapeldiagrammet** för att ändra visningen.  Välj sedan **Inställningar** och gör följande ändringar:

   * **Grupper:**  Lägg till **targettemp**.  
   * **Värden:** 81.1. Ta bort **datum**.  2. Lägg till **temp_diff**.  3.  Ändra Aggregator från **Sum** till **AVG**.  

     På följande skärm bild visas utdata.

     ![Köra ett Spark SQL-uttryck med hjälp av notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Köra ett Spark SQL-uttryck med hjälp av notebook2")

9. Starta om livy-tolken för att avsluta programmet. Det gör du genom att öppna tolknings inställningar genom att välja det inloggade användar namnet i det övre högra hörnet och sedan välja **tolkaren**.  

    ![Starta tolken](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")

10. Bläddra till **livy**och välj sedan **starta om**.  Välj **OK** vid prompten.

    ![Starta om livy-tolken](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Starta om Zeppelin-tolken")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hur gör jag för att använda externa paket med antecknings boken?
Du kan konfigurera Zeppelin Notebook i Apache Spark kluster i HDInsight för att använda externa paket som har tagits bort av community och som inte ingår i klustret. Du kan söka i [maven-lagringsplatsen](https://search.maven.org/) efter den fullständiga listan med tillgängliga paket. Du kan också hämta en lista över tillgängliga paket från andra källor. Till exempel finns en fullständig lista över community-paket som har bidragit till i [Spark-paket](https://spark-packages.org/).

I den här artikeln får du se hur du använder [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketet med Jupyter Notebook.

1. Öppna tolknings inställningar. Välj det inloggade användar namnet i det övre högra hörnet och välj sedan **tolkar**.

    ![Starta tolken](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")

2. Bläddra till **livy**och välj sedan **Redigera**.

    ![Ändra tolknings settings1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Ändra tolknings settings1")

3. Lägg till en ny nyckel med namnet `livy.spark.jars.packages` och ange dess värde i formatet `group:id:version`. Så om du vill använda [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketet måste du ange värdet för nyckeln till `com.databricks:spark-csv_2.10:1.4.0`.

    ![Ändra tolknings settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Ändra tolknings settings2")

    Välj **Spara** och starta om livy-tolken.

4. Gör så här om du vill veta hur du kommer till värdet för nyckeln som anges ovan.
   
    a. Leta upp paketet i maven-lagringsplatsen. I den här artikeln använde vi [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Samla in **värdena för** **ArtifactId**och **version**från databasen.
   
    ![Använda externa paket med Jupyter Notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Använda externa paket med Jupyter Notebook")
   
    c. Sammanfoga de tre värdena, avgränsade med kolon ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Var sparas antecknings böckerna för Zeppelin?
Antecknings böckerna för Zeppelin sparas i klustrets huvudnoderna. Så om du tar bort klustret tas antecknings böckerna också bort. Om du vill bevara dina antecknings böcker för senare användning i andra kluster måste du exportera dem när du är färdig med jobb körningen. Om du vill exportera en antecknings bok väljer du **export** ikonen som visas på bilden nedan.

![Hämta antecknings bok](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Ladda ned antecknings boken")

Detta sparar antecknings boken som en JSON-fil på nedladdnings platsen.

## <a name="livy-session-management"></a>Hantering av livy-sessioner
När du kör det första kod stycket i din Zeppelin-anteckningsbok skapas en ny livy-session i ditt HDInsight Spark-kluster. Den här sessionen delas över alla Zeppelin-anteckningsböcker som du sedan skapar. Om livy-sessionen stoppas (kluster omstart osv.) går det inte att köra jobb från Zeppelin Notebook.

I sådana fall måste du utföra följande steg innan du kan börja köra jobb från en Zeppelin Notebook.  

1. Starta om livy-tolken från Zeppelin Notebook. Det gör du genom att öppna tolknings inställningar genom att välja det inloggade användar namnet i det övre högra hörnet och sedan välja **tolkaren**.

    ![Starta tolken](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-utdata")

2. Bläddra till **livy**och välj **starta om**.

    ![Starta om livy-tolken](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Starta om Zeppelin-tolken")

3. Kör en Code-cell från en befintlig Zeppelin Notebook. Detta skapar en ny livy-session i HDInsight-klustret.

## <a name="seealso"></a>Se även
* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier
* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Använd HDInsight tools-plugin för IntelliJ idé för att skapa och skicka Apache Spark Scala-program](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels tillgängliga för Jupyter Notebook i Apache Spark kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
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
