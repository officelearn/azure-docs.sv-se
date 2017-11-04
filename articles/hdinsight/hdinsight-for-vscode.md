---
title: "Azure HDInsight Tools - Använd Visual Studio-koden för Hive, LLAP eller pySpark | Microsoft Docs"
description: "Lär dig hur du använder Azure HDInsight Tools för Visual Studio-koden för att skapa, skicka frågor och skript."
Keywords: "VScode, Azure HDInsight-verktyg, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktiva Hive, interaktiva fråga"
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
ms.openlocfilehash: 36ce117076ed5c15ddff850485d8f8912ec53caf
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>Använda Azure HDInsight-verktyg för Visual Studio Code

Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code (VSCode) för att skapa, skicka Hive batchjobb, interaktiva Hive-frågor och pySpark-skript. Azure HDInsight-verktyg kan installeras på de plattformar som stöds av VSCode, inklusive Windows, Linux och MacOS. Du kan hitta de nödvändiga förutsättningarna för olika plattformar.


## <a name="prerequisites"></a>Krav

Följande krävs för att slutföra den här artikeln:

- Ett HDInsight-kluster.  Om du vill skapa ett kluster, se [komma igång med HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono är bara krävs för Linux och MacOS.

## <a name="install-the-hdinsight-tools"></a>Installera HDInsight-verktyg
   
När du har installerat kraven kan installera du Azure HDInsight-verktyg för VSCode. 

**Installera Azure HDInsight-verktyg**

1. Öppna **Visual Studio Code**.
2. Klicka på **tillägg** i den vänstra rutan. Ange **HDInsight** i sökrutan.
3. Klicka på **installera** bredvid **Azure HDInsight tools**. Efter några sekunder den **installera** knappen ändras till **ladda**.
4. Klicka på **ladda** att aktivera den **Azure HDInsight tools** tillägg.
5. Klicka på **ladda fönstret** att bekräfta. Du kan se **Azure HDInsight tools** i fönstret tillägg.

   ![HDInsight Visual Studio Code Python-installation](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Öppna HDInsight-arbetsyta

Du måste skapa en arbetsyta i VSCode innan du kan ansluta till Azure.

**Öppna en arbetsyta**

1. Från den **filen** -menyn klickar du på **öppna mappen**, ange en befintlig mapp eller skapa en ny mapp som mappen arbete. Mappen visas i det vänstra fönstret.
2. Till vänster och klicka på den **ny fil** ikonen bredvid Arbetsmappens.

   ![Ny fil](./media/hdinsight-for-vscode/new-file.png)
3. Namnge den nya filen med .hql (Hive-frågor) eller filnamnstillägget .py (Spark skript). Meddelande en **XXXX_hdi_settings.json** konfigurationsfilen läggs automatiskt till mappen arbete.
4. Öppna **XXXX_hdi_settings.json** från **EXPLORER**, eller högerklicka på Skriptredigeraren att välja **ange konfiguration**. Du kan konfigurera inloggning transaktionen, standardkluster och skicka parametrar, som visas i exemplet i filen. Du också lämna återstående parametrar tom.

## <a name="connect-to-azure"></a>Anslut till Azure

Innan du kan skicka skript till HDInsight-kluster från VSCode, måste du ansluta till ditt Azure-konto.

**Att ansluta till Azure**

1. Skapa en ny arbetsmapp och en ny skriptfil om du inte har någon.
2. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: inloggning** på snabbmenyn. Du kan även trycka **CTRL + SKIFT + P** och ange **HDInsight: inloggning**.

    ![HDInsight Tools för Visual Studio Code logga in](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. Följ instruktionerna för inloggning i den **utdata** fönstret för att logga in.

    **Azure:** ![HDInsight Tools för Visual Studio Code inloggningen info](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    När du är ansluten, visas namnet på ditt Azure i statusfältet längst ned vänster-fönstret VSCode. 

    > [!NOTE]
    > Öppna webbläsaren med privata eller incognito läge på grund av autentiseringsproblem med kända Azure. Om ditt Azure-konto har två faktorer är aktiverat, rekommenderas att använda phone autentisering i stället för PIN-kod.
  

4. Högerklicka på skriptet Redigera om du vill öppna snabbmenyn. På snabbmenyn kan du utföra följande uppgifter:

    - Logga ut
    - Lista över kluster
    - Ange standard-kluster
    - Skicka interaktiva Hive-frågor
    - Skicka Hive batch-skript
    - Skicka interaktiva PySpark-frågor
    - Skicka PySpark kommandoskript
    - Ange konfiguration

## <a name="list-hdinsight-clusters"></a>Visa en lista med HDInsight-kluster

Om du vill testa anslutningen måste ange du dina HDInsight-kluster:

**För HDInsight-kluster i listan under din Azure-prenumeration**
1. Öppna en arbetsyta och Anslut till Azure. Se [öppna HDInsight arbetsytan](#open-hdinsight-workspace) och [Anslut till Azure](#connect-to-azure).
2. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: listan kluster** på snabbmenyn. 
3. Hive och Spark-kluster visas i den **utdata** fönstret.

    ![Ange standard klusterkonfigurationen](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Ange standard-kluster
1. Öppna en arbetsyta och Anslut till Azure. Se [öppna HDInsight arbetsytan](#open-hdinsight-workspace) och [Anslut till Azure](#connect-to-azure).
2. Högerklicka på Skriptredigeraren och klicka sedan på **HDInsight: Ange standard klustret**. 
3. Välj ett kluster som standardkluster för den aktuella skriptfilen. Verktygen automatiskt uppdatera konfigurationsfilen **XXXX_hdi_settings.json**. 

   ![Ange standard klusterkonfigurationen](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Ange Azure-miljön 
1. Öppna paletten kommandot genom att trycka på **CTRL + SKIFT + P**.
2. Ange **HDInsight: Ange Azure-miljön**.
3. Välj ett sätt från Azure och AzureChina som standard inloggnings-posten.
4. Under tiden vårt verktyg redan sparats vad du har valt standardvärdet för inloggning till **XXXX_hdi_settings.json**. Du uppdatera också direkt den i konfigurationsfilen. 

   ![Ange standard inloggningen post konfiguration](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Skicka interaktiva Hive-frågor

HDInsight-verktyg för VSCode kan du skicka interaktiva Hive-frågor till interaktiva frågan för HDInsight-kluster.

1. Skapa en ny arbetsmapp och en ny fil för Hive-skript om du inte har någon.
2. Anslut till ditt Azure-konto och sedan konfigurera standardklustret om du inte gjort det.
3. Kopiera och klistra in följande kod i dina Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Högerklicka på Skriptredigeraren och klicka sedan på **HDInsight: Hive interaktiva** att skicka fråga. Verktygen kan du skicka ett kodblock i stället för hela skriptfilen använder snabbmenyn. Snart därefter visas frågeresultatet i en ny flik:

   ![interaktiva Hive-resultat](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTATEN** panelen: du kan spara hela resultatet som CSV, JSON, EXCEL till lokal sökväg eller markera flera rader.
    - **MEDDELANDEN** Kontrollpanelen: Klicka på **rad** nummer, leder det till den första raden i skriptet körs.

Jämföra [Kör batchjobb Hive](#submit-hive-batch-scripts), interaktiva fråga tar mycket kortare tid.

## <a name="submit-hive-batch-scripts"></a>Skicka Hive-kommandoskript

1. Skapa en ny arbetsmapp och en ny fil för Hive-skript om du inte har någon.
2. Anslut till ditt Azure-konto och sedan konfigurera standardklustret om du inte gjort det.
3. Kopiera och klistra in följande kod i dina Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Högerklicka på Skriptredigeraren och klicka sedan på **HDInsight: Hive Batch** att skicka ett Hive-jobb. 
4. Välj ett kluster där du vill skicka den till.  

    Efter att ha skickat ett Hive-jobb, skicka lyckade info och jobid visas i **utdata** panelen. Och öppnas **WEBBLÄSARE** som jobbet realtid loggar och status visas i.

   ![Skicka Hive-jobbet resultat](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

Jämföra [skickar interaktiva Hive-frågor](#submit-interactive-hive-queries), batch-jobbet tar mycket längre tid.

## <a name="submit-interactive-pyspark-queries"></a>Skicka interaktiva PySpark-frågor
HDInsight-verktyg för VSCode kan du skicka den interaktiva PySpark frågor till Spark-kluster.
1. Skapa en ny arbetsmapp och en ny skriptfil med filnamnstillägget .py om du inte har någon.
2. Ansluta till ditt Azure-konto om du inte gjort det.
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
4. Markera skripten och högerklicka på Skriptredigeraren och klicka på **HDInsight: PySpark interaktiva**.
5. Klicka på följande **installera** knappen om du inte har installerat **Python** tillägget VSCode.
    ![HDInsight Visual Studio Code Python-installation](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Ställ in python-miljö i systemet om du inte installera den. 
   - Windows, hämta och installera [Python](https://www.python.org/downloads/). Kontrollera att `Python` och `pip` i systemet med SÖKVÄGEN.
   - Instruktionen för MacOS och Linux, se [ställa in PySpark interaktiv miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md).
7. Välj ett kluster att skicka din PySpark-fråga. Snart därefter visas frågeresultatet på fliken höger nytt:

   ![Skicka python jobbet resultat](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Vår verktyget stöder också fråga den **SQL-satsen**.

   ![Skicka python jobbet resultatet](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) skicka statusen visas till vänster i statusfältet längst ned när du kör frågor. Du kan inte skicka andra frågor när status är **PySpark-kerneln (upptagen)**, annars är körningen låser sig.
9. Vår kluster kan underhålla en session. Till exempel **en = 100**, hålla sessionen redan i klustret, nu du bara köra **skriva ut en** till klustret.
 

## <a name="submit-pyspark-batch-job"></a>Skicka PySpark batchjobb

1. Skapa en ny arbetsmapp och en ny skriptfil med filnamnstillägget .py om du inte har någon.
2. Ansluta till ditt Azure-konto om du inte gjort det.
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
4. Högerklicka på Skriptredigeraren och klicka sedan på **HDInsight: PySpark Batch**. 
5. Välj ett kluster att skicka PySpark-jobbet. 

   ![Skicka python jobbet resultat](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

När du skickar ett jobb för python, skicka loggarna visas i **utdata** fönster i VSCode. Den **Spark UI URL** och **Yarn UI URL** visas också. Du kan öppna URL: en i en webbläsare för att spåra jobbstatus.


## <a name="additional-features"></a>Ytterligare funktioner

HDInsight för VSCode stöder följande funktioner:

- **Automatisk komplettering IntelliSense**. Förslag skapats runt nyckelord, metod, variabler och så vidare. Olika ikoner representerar olika typer av objekt:

    ![HDInsight Tools för Visual Studio Code IntelliSense objekttyper](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense fel markör**. Tjänsten språk understryker redigera fel för Hive-skript.     
- **Syntax visar**. Tjänsten språk använder olika färg för att särskilja variabler, nyckelord, datatyp, funktioner och så vidare. 

    ![HDInsight Tools för Visual Studio Code syntax visar](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo
* HDInsight för VScode: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg
* [Konfigurera PySpark interaktiv miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Använda Azure Toolkit för IntelliJ för att skapa och skicka Spark Scala-program](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda Azure Toolkit för IntelliJ för att felsöka Spark-program via fjärranslutning via VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Använda HDInsight Tools för IntelliJ med Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](hdinsight-apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: Använda Spark i HDInsight för att skapa realtid direktuppspelning av program](hdinsight-apache-spark-eventhub-streaming.md)
* [Webbplatslogganalys med Spark i HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](hdinsight-apache-spark-job-debugging.md)



