---
title: Zeppelin-datorer & Apache Spark-kluster - Azure HDInsight
description: Steg-för-steg-instruktioner om hur du använder Zeppelin-anteckningsböcker med Apache Spark-kluster på Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: e313048986beca1991e38ce2e65ea12f954170d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598280"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Använda Apache Zeppelin-anteckningsböcker med Apache Spark-kluster i Azure HDInsight

HDInsight Spark-kluster innehåller [Apache Zeppelin-anteckningsböcker](https://zeppelin.apache.org/) som du kan använda för att köra [Apache Spark-jobb.](https://spark.apache.org/) I den här artikeln får du lära dig hur du använder den zeppelinska anteckningsboken i ett HDInsight-kluster.

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* URI-schemat för klustrets primära lagring. Detta skulle `wasb://` vara för Azure `abfs://` Blob Storage, för `adl://` Azure Data Lake Storage Gen2 eller för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Blob `wasbs://`Storage, skulle URI vara .  Mer information finns [i Kräv säker överföring i Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Starta en apache Zeppelin-anteckningsbok

1. Välj **Zeppelin-anteckningsbok** från **klusterinstrumentpaneler**i **Miniatyr-klusteröversikten**. Ange administratörsautentiseringsuppgifterna för klustret.  

   > [!NOTE]  
   > Du kan också nå Zeppelin Notebook för ditt kluster genom att öppna följande webbadress i din webbläsare. Ersätt **CLUSTERNAME** med namnet på klustret:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Skapa en ny anteckningsbok. Gå till **Anteckningsbok** > **skapa ny anteckning**i rubrikfönstret.

    ![Skapa en ny Zeppelin-anteckningsbok](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Skapa en ny Zeppelin-anteckningsbok")

    Ange ett namn på anteckningsboken och välj sedan **Skapa anteckning**.

3. Kontrollera att anteckningsbokshuvudet visar en ansluten status. Det betecknas med en grön prick i det övre högra hörnet.

    ![Zeppelin-status för anteckningsbok](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin-status för anteckningsbok")

4. Läs in exempeldata i en tillfällig tabell. När du skapar ett Spark-kluster i HDInsight kopieras exempeldatafilen `hvac.csv`till `\HdiSamples\SensorSampleData\hvac`det associerade lagringskontot under .

    Klistra in följande kodavsnitt i det tomma stycket som skapas som standard i den nya anteckningsboken.

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

    Tryck på **SKIFT + RETUR** eller välj knappen **Spela** upp för att stycket ska köra utdraget. Statusen i styckets högra hörn ska gå från KLAR, VÄNTANDE, KÖR TILL FÄRDIG. Utdata visas längst ned i samma stycke. Skärmbilden ser ut så här:

    ![Skapa en temporär tabell från rådata](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Skapa en temporär tabell från rådata")

    Du kan också ange en rubrik för varje stycke. I styckets högra hörn väljer du ikonen **Inställningar** (kedjehjul) och väljer sedan **Visa rubrik**.  

    > [!NOTE]  
    > %spark2-tolk stöds inte i Zeppelin-anteckningsböcker i alla HDInsight-versioner, och %sh-tolken stöds inte från HDInsight 4.0 och framåt.

5. Du kan nu köra Spark `hvac` SQL-uttryck i tabellen. Klistra in följande fråga i ett nytt stycke. Frågan hämtar bygg-ID och skillnaden mellan målet och faktiska temperaturer för varje byggnad på ett visst datum. Tryck på **SKIFT + RETUR**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    **%sql-uttrycket** i början talar om för anteckningsboken att använda Livy Scala-tolken.

6. Välj ikonen **Stapeldiagram** om du vill ändra visningen.  **inställningar**, som visas när du har valt **stapeldiagram**, kan du välja **Nycklar**och **Värden**.  Följande skärmbild visar utdata.

    ![Köra ett Spark SQL-uttryck med anteckningsboken1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Köra ett Spark SQL-uttryck med anteckningsboken1")

7. Du kan också köra Spark SQL-uttryck med hjälp av variabler i frågan. Nästa kodavsnitt visar hur du definierar en variabel, `Temp`i frågan med de möjliga värden som du vill fråga med. När du först kör frågan fylls en listruta automatiskt med de värden som du har angett för variabeln.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Klistra in det här kodavsnittet i ett nytt stycke och tryck på **SKIFT + RETUR**. Välj sedan **65** i listrutan **Temp.**

8. Välj ikonen **Stapeldiagram** om du vill ändra visningen.  Välj sedan **inställningar** och gör följande ändringar:

   * **Grupper:**  Lägg till **targettemp**.  
   * **Värden:** 1. Ta bort **datum**.  2. Lägg till **temp_diff**.  3.  Ändra aggregatorn från **SUMMA** till **AVG**.  

     Följande skärmbild visar utdata.

     ![Köra ett Spark SQL-uttryck med hjälp av anteckningsboken2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Köra ett Spark SQL-uttryck med hjälp av anteckningsboken2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hur använder jag externa paket med anteckningsboken?

Du kan konfigurera Zeppelin-anteckningsboken i Apache Spark-klustret på HDInsight så att de använder externa paket som medföljer community-bidrag som inte ingår direkt i klustret. Du kan söka i [Maven-databasen](https://search.maven.org/) efter en komplett lista över tillgängliga paket. Du kan också få en lista över tillgängliga paket från andra källor. En komplett lista över paket med community-bidrag finns till exempel på [Spark Packages](https://spark-packages.org/).

I den här artikeln får du se hur du använder [spark-csv-paketet](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) med jupyter-anteckningsboken.

1. Öppna tolkinställningar. Välj det inloggade användarnamnet längst upp till höger och välj sedan **Tolk**.

    ![Starta tolk](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Utdata från hive")

2. Bläddra till **livy2**och välj sedan **redigera**.

    ![Ändra tolkinställningar1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Ändra tolkinställningar1")

3. Navigera till `livy.spark.jars.packages`nyckeln och ange dess `group:id:version`värde i formatet . Så, om du vill använda [spark-csv paketet,](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) måste du `com.databricks:spark-csv_2.10:1.4.0`ställa in värdet på nyckeln till .

    ![Ändra tolkinställningar2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Ändra tolkinställningar2")

    Välj **Spara** och sedan **OK** om du vill starta om Livy-tolken.

4. Så här gör du om du vill förstå hur du kommer fram till värdet på nyckeln ovan.

    a. Leta reda på paketet i Maven-arkivet. För den här artikeln använde vi [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. Samla in värdena för **GroupId,** **ArtifactId**och **Version**från databasen .

    ![Använda externa paket med Jupyter notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Använda externa paket med Jupyter notebook")

    c. Sammanfoga de tre värdena, åtskilda av ett kolon (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Var sparas Zeppelin-anteckningsböckerna?

Zeppelin-anteckningsböckerna sparas i klusterhuvudnoderna. Så om du tar bort klustret tas även anteckningsböckerna bort. Om du vill bevara dina anteckningsböcker för senare användning i andra kluster måste du exportera dem när du har kört jobben. Om du vill exportera en anteckningsbok väljer du **ikonen Exportera** enligt bilden nedan.

![Ladda ner anteckningsbok](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Ladda ned anteckningsboken")

Då sparas anteckningsboken som en JSON-fil på nedladdningsplatsen.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Använda Shiro för att konfigurera åtkomst till Zeppelintolkar i ESP-kluster (Enterprise Security Package)
Som nämnts `%sh` ovan stöds tolken inte från HDInsight 4.0 och framåt. Eftersom `%sh` tolken introducerar potentiella säkerhetsproblem, till exempel åtkomstnyckelflikar med skalkommandon, har den också tagits bort från HDInsight 3.6 ESP-kluster. Det `%sh` innebär att tolk inte är tillgänglig när du klickar på **Skapa ny anteckning** eller i tolkgränssnittet som standard. 

Privilegierade domänanvändare kan `Shiro.ini` använda filen för att styra åtkomsten till tolkgränssnittet. Därför kan endast dessa `%sh` användare skapa nya tolkar `%sh` och ange behörigheter för varje ny tolk. Så här styr `shiro.ini` du åtkomsten med hjälp av filen:

1. Definiera en ny roll med ett befintligt domännamn. I följande exempel `adminGroupName` är en grupp privilegierade användare i AAD. Använd inte specialtecken eller blanksteg i gruppnamnet. Tecknen efter `=` ger behörighet för den här rollen. `*`innebär att gruppen har fullständiga behörigheter.

    ```
    [roles]
    adminGroupName = *
    ```

2. Lägg till den nya rollen för åtkomst till Zeppelintolkar. I följande exempel får `adminGroupName` alla användare i åtkomst till Zeppelintolkar och kan skapa nya tolkar. Du kan placera flera roller `roles[]`mellan hakparenteserna i , avgränsade med kommatecken. Sedan kan användare som har de behörigheter som krävs komma åt Zeppelin-tolkar.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy session förvaltning

När du kör det första kodstycket i din Zeppelin-anteckningsbok skapas en ny Livy-session i ditt HDInsight Spark-kluster. Den här sessionen delas mellan alla Zeppelin-anteckningsböcker som du sedan skapar. Om Livy-sessionen av någon anledning dödas (klusteromstart och så vidare) kan du inte köra jobb från den zeppelinska anteckningsboken.

I så fall måste du utföra följande steg innan du kan börja köra jobb från en Zeppelin-anteckningsbok.  

1. Starta om Livy-tolken från den bärbara Zeppelin-anteckningsboken. Om du vill göra det öppnar du tolkinställningarna genom att välja det inloggade användarnamnet i det övre högra hörnet och väljer sedan **Tolk**.

    ![Starta tolk](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Utdata från hive")

2. Bläddra till **livy2**och välj sedan **starta om**.

    ![Starta om Livy-tolken](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Starta om Zeppelin-tolken")

3. Kör en kodcell från en befintlig Zeppelin-anteckningsbok. Detta skapar en ny Livy-session i HDInsight-klustret.

## <a name="general-information"></a>Allmän information

### <a name="validate-service"></a>Verifiera tjänsten

Om du vill verifiera tjänsten från `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` Ambari navigerar du till den plats där CLUSTERNAME är namnet på klustret.

Om du vill validera tjänsten från en kommandorad SSH till huvudnoden. Växla användare till zeppelin med kommandot `sudo su zeppelin`. Statuskommandon:

|Kommando |Beskrivning |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Tjänstens status.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Tjänstversion.|
|`ps -aux | grep zeppelin`|Identifiera PID.|

### <a name="log-locations"></a>Loggplatser

|Tjänst |Sökväg |
|---|---|
|zeppelin-server|/usr/hdp/current/zeppelin-server/|
|Serverloggar|/var/log/zeppelin|
|Konfigurationstolk, Shiro, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf eller /etc/zeppelin/conf|
|PID-katalog|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Aktivera felsökningsloggning

1. Navigera `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` till den plats där CLUSTERNAME är namnet på klustret.

1. Navigera till **CONFIGS** > **Advanced zeppelin-log4j-properties** > **log4j_properties_content**.

1. Ändra `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`till .

1. Lägg `log4j.logger.org.apache.zeppelin.realm=DEBUG`till .

1. Spara ändringar och starta om tjänsten.

## <a name="next-steps"></a>Nästa steg

[Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: Utför interaktiv dataanalys med Spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att analysera byggnadstemperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med maskininlärning: Använd Spark i HDInsight för att förutsäga resultat för livsmedelsinspektion](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använd HDInsight Tools Plugin för IntelliJ IDEA för att skapa och skicka Apache Spark Scala applikationer](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight Tools Plugin för IntelliJ IDEA för att felsöka Apache Spark-program på distans](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kärnor tillgängliga för Jupyter-anteckningsbok i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
