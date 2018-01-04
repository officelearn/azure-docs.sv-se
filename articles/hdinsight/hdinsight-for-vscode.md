---
title: "Azure HDInsight Tools - Använd Visual Studio-koden för Hive, LLAP eller pySpark | Microsoft Docs"
description: "Lär dig hur du använder Azure HDInsight Tools för Visual Studio-koden för att skapa och skicka frågor och skript."
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 89e83dc02f32f6f2a781cf2e35040b29cc3d3c06
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Använd Azure HDInsight Tools för Visual Studio Code

Lär dig hur du använder Azure HDInsight Tools för Visual Studio-koden (VS) för att skapa och skicka Hive batchjobb, interaktiva Hive-frågor och pySpark-skript. Azure HDInsight-verktyg kan installeras på de plattformar som stöds av VS-kod. Dessa omfattar Windows, Linux och macOS. Du kan hitta de nödvändiga förutsättningarna för olika plattformar.


## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra stegen i den här artikeln:

- Ett HDInsight-kluster.  Om du vill skapa ett kluster, se [komma igång med HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono är bara krävs för Linux och macOS.

## <a name="install-the-hdinsight-tools"></a>Installera HDInsight-verktyg
   
När du har installerat förutsättningarna kan du installera Azure HDInsight-verktyg för VS-kod. 

**Installera Azure HDInsight-verktyg**

1. Öppna Visual Studio-koden.

2. I den vänstra rutan, Välj **tillägg**. I sökrutan anger **HDInsight**.

3. Bredvid **Azure HDInsight tools**väljer **installera**. Efter några sekunder den **installera** ändras knappen till **ladda**.

4. Välj **ladda** att aktivera den **Azure HDInsight tools** tillägg.

5. Välj **ladda fönstret** att bekräfta. Du kan se **Azure HDInsight tools** i den **tillägg** fönstret.

   ![HDInsight Visual Studio Code Python-installation](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Öppna HDInsight-arbetsyta

Skapa en arbetsyta i VS-kod innan du kan ansluta till Azure.

**Öppna en arbetsyta**

1. På den **filen** väljer du **öppna mappen**. Ange en befintlig mapp som mappen arbete eller skapa en ny sedan. Mappen visas i den vänstra rutan.

2. I den vänstra rutan, Välj den **ny fil** ikonen bredvid Arbetsmappens.

   ![Ny fil](./media/hdinsight-for-vscode/new-file.png)

3. Namnge den nya filen med .hql (Hive-frågor) eller filnamnstillägget .py (Spark skript). Observera att en **XXXX_hdi_settings.json** konfigurationsfilen läggs automatiskt till mappen arbete.

4. Öppna **XXXX_hdi_settings.json** från **EXPLORER**, eller högerklicka på Skriptredigeraren att välja **ange konfiguration**. Du kan konfigurera inloggningen transaktionen, standardkluster och skicka parametrar som visas i exemplet i filen. Du också lämna återstående parametrar tom.

## <a name="connect-to-azure"></a>Anslut till Azure

Innan du kan skicka skript till HDInsight-kluster från VS-kod, måste du ansluta till ditt Azure-konto.

**Att ansluta till Azure**

1. Skapa en ny arbetsmapp och en ny skriptfil om inte redan har.

2. Högerklicka på Skriptredigeraren och markera sedan på snabbmenyn **HDInsight: inloggning**. Du kan också ange **Ctrl + Skift + P**, och ange sedan **HDInsight: inloggning**.

    ![HDInsight Tools för Visual Studio Code logga in](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Om du vill logga in, följ instruktionerna logga in i den **utdata** fönstret.

    **Azure:** ![HDInsight Tools för Visual Studio Code inloggningen info](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    När du är ansluten visas din Azure-konto i statusfältet längst ned till vänster i fönstret VS-kod. 

    > [!NOTE]
    > Du måste öppna en webbläsare i privat eller incognito läge på grund av ett känt Azure authentication-problem. Om ditt Azure-konto har två faktorer är aktiverat, rekommenderar vi med phone autentisering i stället för PIN-autentisering.
  

4. Högerklicka på Skriptredigeraren för att öppna snabbmenyn. På snabbmenyn kan du utföra följande uppgifter:

    - Logga ut
    - Lista över kluster
    - Ange standard-kluster
    - Skicka interaktiva Hive-frågor
    - Skicka Hive-kommandoskript
    - Skicka interaktiva PySpark-frågor
    - Skicka PySpark kommandoskript
    - Set-konfigurationer

## <a name="list-hdinsight-clusters"></a>Visa en lista med HDInsight-kluster

Om du vill testa anslutningen måste ange du dina HDInsight-kluster:

**För HDInsight-kluster i listan under din Azure-prenumeration**
1. Öppna en arbetsyta och sedan ansluta till Azure. Mer information finns i [öppna HDInsight arbetsytan](#open-hdinsight-workspace) och [Anslut till Azure](#connect-to-azure).

2. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: listan kluster** på snabbmenyn. 

3. Hive och Spark-kluster visas i den **utdata** fönstret.

    ![Ange en standardkonfiguration för kluster](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Ange ett standardkluster
1. Öppna en arbetsyta och Anslut till Azure. Se [öppna HDInsight arbetsytan](#open-hdinsight-workspace) och [Anslut till Azure](#connect-to-azure).

2. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: Ange standard klustret**. 

3. Välj ett kluster som standardklustret för den aktuella skriptfilen. Verktygen automatiskt uppdatera konfigurationsfilen **XXXX_hdi_settings.json**. 

   ![Ange standard klusterkonfigurationen](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Ange Azure-miljön 
1. Öppna paletten kommandot genom att välja **CTRL + SKIFT + P**.

2. Ange **HDInsight: Ange Azure-miljön**.

3. Välj ett sätt från Azure och AzureChina som standard inloggnings-posten.

4. Under tiden verktyget redan sparats din inloggning standardposten i **XXXX_hdi_settings.json**. Du uppdatera också direkt den i konfigurationsfilen. 

   ![Ange standard inloggningen post konfiguration](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Skicka interaktiva Hive-frågor

Med HDInsight Tools för VS-kod, kan du skicka interaktiva Hive-frågor till interaktiva frågan HDInsight-kluster.

1. Skapa en ny arbetsmapp och en ny fil för Hive-skript om du inte redan har.

2. Anslut till ditt Azure-konto och sedan konfigurera standardklustret om du inte redan gjort det.

3. Kopiera och klistra in följande kod i dina Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: Hive interaktiva** att skicka fråga. Verktygen kan du skicka ett kodblock i stället för hela skriptfilen använder snabbmenyn. Strax efter visas resultatet av frågan i en ny flik.

   ![interaktiva Hive-resultat](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTATEN** panelen: du kan spara hela resultatet som CSV, JSON eller Excel-fil till en lokal sökväg eller markera flera rader.

    - **MEDDELANDEN** panelen: när du väljer **rad** nummer, leder det till den första raden i skriptet körs.

Köra interaktiva fråga tar mycket kortare tid än [Kör batchjobb Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Skicka Hive-kommandoskript

1. Skapa en ny arbetsmapp och en ny fil för Hive-skript om du inte redan har.

2. Anslut till ditt Azure-konto och sedan konfigurera standardklustret om du inte redan gjort det.

3. Kopiera och klistra in följande kod i dina Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: Hive Batch** att skicka ett Hive-jobb. 

4. Välj ett kluster som du vill skicka.  

    När du har skickat ett Hive-jobb skicka lyckade info och jobid visas i den **utdata** panelen. Hive-jobb öppnas också **WEBBLÄSARE**, vilket visar realtid jobbloggar och status.

   ![Skicka Hive-jobbet resultat](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Skicka interaktiva Hive-frågor](#submit-interactive-hive-queries) tar mycket kortare tid än att skicka ett batchjobb.

## <a name="submit-interactive-pyspark-queries"></a>Skicka interaktiva PySpark-frågor
HDInsight-verktyg för VS-kod kan du skicka den interaktiva PySpark frågor till Spark-kluster.
1. Skapa en ny arbetsmapp och en ny skriptfil med filnamnstillägget .py om inte redan har.

2. Ansluta till ditt Azure-konto om du inte gjort det ännu.

3. Kopiera och klistra in följande kod i skriptfilen:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Markera dessa skript. Högerklicka Skriptredigeraren och välj **HDInsight: PySpark interaktiva**.

5. Om du inte redan har installerat den **Python** tillägget VS-kod, Välj den **installera** knappen som visas i följande bild:

    ![HDInsight Visual Studio Code Python-installation](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Installera Python-miljön i ditt system om du inte redan gjort. 
   - Windows, hämta och installera [Python](https://www.python.org/downloads/). Kontrollera att `Python` och `pip` finns i systemet sökväg.

   - Instruktioner för macOS- och Linux finns [konfigurera PySpark interaktiv miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Välj ett kluster som du vill skicka PySpark-fråga. Snart därefter visas frågeresultatet i den nya rätta fliken:

   ![Skicka Python jobbet resultat](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Verktyget stöder också den **SQL-satsen** frågan.

   ![Skicka Python jobbet resultatet](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) skicka status visas till vänster i nedre statusfältet när du kör frågor. Skicka inte andra frågor om status är **PySpark-kerneln (upptagen)**. 

>[!NOTE]
>Kluster kan underhålla sessionsinformation. Definierade variabeln, funktion och motsvarande värden hålls i sessionen, så att de kan refereras över flera tjänstanrop för samma kluster. 
 

## <a name="submit-pyspark-batch-job"></a>Skicka PySpark batchjobb

1. Skapa en ny arbetsmapp och en ny skriptfil med filnamnstillägget .py om inte redan har.

2. Ansluta till ditt Azure-konto om du inte redan gjort det.

3. Kopiera och klistra in följande kod i skriptfilen:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: PySpark Batch**. 

5. Välj ett kluster som du vill skicka PySpark-jobbet. 

   ![Skicka Python jobbet resultat](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

När du skickar en Python-jobb skicka loggarna visas i den **utdata** fönster i VS-kod. Den **Spark UI URL** och **Yarn UI URL** visas också. Du kan öppna URL: en i en webbläsare för att spåra jobbstatus.


## <a name="additional-features"></a>Ytterligare funktioner

HDInsight VS-kod har stöd för följande funktioner:

- **Automatisk komplettering IntelliSense**. Förslag visas för nyckelordet, metoder, variabler och så vidare. Olika typer av objekt som representerar olika ikoner.

    ![HDInsight Tools för Visual Studio Code IntelliSense objekttyper](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense fel markör**. Tjänsten språk understryker redigera fel för Hive-skript.     
- **Syntax visar**. Språk-tjänsten använder olika färger för att skilja mellan variabler, nyckelord, datatyp, funktioner och så vidare. 

    ![HDInsight Tools för Visual Studio Code syntax visar](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo
* HDInsight VS-kod: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](spark/apache-spark-eclipse-tool-plugin.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualisera interaktiva frågan Hive-data med Power BI i Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Konfigurera PySpark interaktiv miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att skapa realtid direktuppspelning av program](spark/apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](spark/apache-spark-job-debugging.md)



