---
title: Azure HDInsight-verktyg – Använd Visual Studio Code för Hive, LLAP eller pySpark
description: Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code för att skapa och skicka frågor och skript.
keywords: VS Code, Azure HDInsight-verktyg, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interaktiv fråga
services: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: b66658bde818e2b4cce2cbc0dc5b419ba33dc737
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43108836"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Använd Azure HDInsight Tools för Visual Studio Code

Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code (VS Code) för att skapa och skicka Hive-batchjobb, interaktiva Hive-frågor och pySpark-skript. Azure HDInsight-verktyg kan installeras på de plattformar som stöds av VS Code. Dessa omfattar Windows, Linux och macOS. Du hittar kraven för olika plattformar.


## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra stegen i den här artikeln:

- Ett HDInsight-kluster. För att skapa ett kluster, se [Kom igång med HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono är endast krävs för Linux och macOS.

## <a name="install-the-hdinsight-tools"></a>Installera HDInsight-verktyg
   
När du har installerat kraven kan installera du Azure HDInsight Tools för VS Code. 

**Installera Azure HDInsight-verktyg**

1. Öppna Visual Studio Code.

2. I den vänstra rutan väljer **tillägg**. I sökrutan anger **HDInsight**.

3. Bredvid **Azure HDInsight-verktyg**väljer **installera**. Efter några sekunder i **installera** knappen ändras till **Reload**.

4. Välj **Reload** att aktivera den **Azure HDInsight-verktyg** tillägget.

5. Välj **Reload Window** att bekräfta. Du kan se **Azure HDInsight-verktyg** i den **tillägg** fönstret.

   ![HDInsight Visual Studio Code Python-installation](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Öppna HDInsight-arbetsyta

Skapa en arbetsyta i VS Code innan du kan ansluta till Azure.

**Öppna en arbetsyta**

1. På den **filen** menyn och välj **Öppna mapp**. Sedan ange en befintlig mapp som mappen arbete eller skapa en ny. Mappen visas i den vänstra rutan.

2. I det vänstra fönstret, Välj den **ny fil** ikonen bredvid Arbetsmappens.

   ![Ny fil](./media/hdinsight-for-vscode/new-file.png)

3. Namnge den nya filen med filnamnstillägget .py (Spark-skriptet) eller .hql (Hive-frågor). Observera att en **XXXX_hdi_settings.json** konfigurationsfilen läggs automatiskt till Arbetsmappens.

4. Öppna **XXXX_hdi_settings.json** från **EXPLORER**, eller högerklicka på Skriptredigeraren att välja **ange konfigurationen**. Du kan konfigurera inloggning posten, standardkluster och skicka parametrar som du ser i exemplet i filen. Du kan också lämna återstående parametrar tomt.

## <a name="connect-to-hdinsight-cluster"></a>Ansluta till HDInsight-kluster

Innan du kan skicka skript till HDInsight-kluster från VS Code, måste du ansluta till ditt Azure-konto eller länka ett kluster (med hjälp av Ambari användarnamn/lösenord eller domänanslutna konto).

**Att ansluta till Azure**

1. Skapa en ny arbetsmapp och en ny skriptfil om inte redan har.

2. Högerklicka på Skriptredigeraren och klicka sedan på snabbmenyn väljer **HDInsight: inloggning**. Du kan också ange **Ctrl + Skift + P**, och ange sedan **HDInsight: inloggning**.

    ![HDInsight Tools för Visual Studio Code logga in](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. För att logga in, följer du anvisningarna logga in i den **utdata** fönstret.

    **Azure:** ![HDInsight Tools för Visual Studio Code inloggningen info](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    När du är ansluten, visas namnet på ditt Azure i statusfältet längst ned till vänster i VS Code-fönstret. 

    > [!NOTE]
    > På grund av ett problem med kända Azure-autentisering måste du öppna en webbläsare i privat eller inkognito läge. Om ditt Azure-konto har två faktorer som är aktiverad, rekommenderar vi att använda phone autentisering i stället för PIN-autentisering.
  

4. Högerklicka på Skriptredigeraren för att öppna snabbmenyn. På snabbmenyn kan du utföra följande uppgifter:

    - Logga ut
    - En lista över kluster
    - Ange standard-kluster
    - Skicka interaktiva Hive-frågor
    - Skicka Hive-batchskript
    - Köra en interaktiv PySpark-fråga
    - Skicka PySpark-skript för batch
    - Set-konfigurationer

<a id="linkcluster"></a>**Länka ett kluster**

Du kan länka ett normalt kluster med Ambari hanteras användarnamn, också länka ett hadoop-kluster med säkerhet med hjälp av domänanvändarnamn (t.ex: user1@contoso.com).
1. Öppna kommandopaletten genom att välja **CTRL + SKIFT + P**, och ange sedan **HDInsight: länka ett kluster**.

   ![länken cluster kommando](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Ange HDInsight-kluster-URL > indata Username -> lösenordet -> Välj typ av kluster -> den visar lyckad info om verifiering.
   
   ![länka kluster dialog](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Länkade användarnamn och lösenord används om klustret både loggas i Azure-prenumeration och länkad ett kluster. 
   
3. Du kan se ett länkade-kluster med hjälp av kommandot **listan kluster**. Nu kan du skicka ett skript för att den här länkade kluster.

   ![länkade kluster](./media/hdinsight-for-vscode/linked-cluster.png)

4. Du kan också Avlänka ett kluster genom att mata in **HDInsight: ta bort länken till ett kluster** från kommandopaletten.

## <a name="list-hdinsight-clusters"></a>Lista över HDInsight-kluster

Om du vill testa anslutningen, kan du lista dina HDInsight-kluster:

**Visa en lista över HDInsight-kluster under din Azure-prenumeration**
1. Öppnar en arbetsyta och sedan ansluta till Azure. Mer information finns i [öppna HDInsight-arbetsyta](#open-hdinsight-workspace) och [Anslut till Azure](#connect-to-azure).

2. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: lista över kluster** på snabbmenyn. 

3. Hive och Spark-kluster som visas i den **utdata** fönstret.

    ![Ange en standardkonfiguration för kluster](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Ange ett standardkluster
1. Öppna en arbetsyta och Anslut till Azure. Se [öppna HDInsight-arbetsyta](#open-hdinsight-workspace) och [Anslut till Azure](#connect-to-azure).

2. Högerklicka på Skriptredigeraren och välj sedan **HDInsight: Ange standard-kluster**. 

3. Välj ett kluster som standardklustret för den aktuella skriptfilen. Verktygen automatiskt uppdatera konfigurationsfilen **XXXX_hdi_settings.json**. 

   ![Ställ in standardkonfiguration för kluster](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Ange Azure-miljön 
1. Öppna kommandopaletten genom att välja **CTRL + SKIFT + P**.

2. Ange **HDInsight: Ställ in Azure-miljön**.

3. Välj ett sätt från Azure och AzureChina som din standardalternativet för inloggning.

4. Under tiden kan verktyget redan har sparats din inloggning standardposten i **XXXX_hdi_settings.json**. Du uppdatera också direkt den i den här konfigurationsfilen. 

   ![Ange standard inloggningen post konfiguration](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Skicka interaktiva Hive-frågor

Med HDInsight Tools för VS Code, kan du skicka interaktiva Hive-frågor till HDInsight interaktiva frågekluster.

1. Skapa en ny arbetsmapp och en ny fil för Hive-skript om du inte redan har gjort det.

2. Anslut till ditt Azure-konto och sedan konfigurera standardklustret om du inte redan gjort det.

3. Sedan kopierar du och klistrar in följande kod i din Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Högerklicka på skriptredigeraren och välj sedan **HDInsight: Hive Interactive** att skicka frågan. Med verktygen kan du dessutom skicka ett kodblock istället för hela skriptfilen med snabbmenyn. Strax därefter efter visas resultatet av frågan i en ny flik.

   ![Interaktiva Hive-resultat](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panelen **RESULTAT**: du kan spara hela resultatet som CSV-, JSON- eller Excel-filer till en lokal sökväg eller markera flera rader.

    - Panelen **MEDDELANDE**: när du väljer **Rad**nummer, leder det till den första raden i det aktuella skriptet.

Interaktiva frågor tar mycket kortare tid än [att köra ett Hive-batchjobb](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Skicka Hive-batchskript

1. Skapa en ny arbetsmapp och en ny fil för Hive-skript om du inte redan har gjort det.

2. Anslut till ditt Azure-konto och sedan konfigurera standardklustret om du inte redan gjort det.

3. Sedan kopierar du och klistrar in följande kod i din Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Högerklicka på skriptredigeraren och välj sedan **HDInsight: Hive Batch** för att skicka ett Hive-jobb. 

4. Välj ett kluster som du vill skicka.  

    När du har skickat ett Hive-jobb visas information om resultat och jobb-ID i panelen **UTDATA**. Hive-jobb öppnas också **WEBBLÄSARE**, vilket visar jobbloggar och status i realtid.

   ![skicka resultat om Hive-jobbet](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

Det tar mycket kortare tid att [skicka interaktiva Hive-frågor](#submit-interactive-hive-queries) än att skicka ett batchjobb.

## <a name="submit-interactive-pyspark-queries"></a>Köra en interaktiv PySpark-fråga
HDInsight Tools för VS Code kan du köra en interaktiv PySpark-fråga till Spark-kluster.
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
4. Markera dessa skript. Högerklicka Skriptredigeraren och välj **HDInsight: interaktiv PySpark**.

5. Om du inte redan har installerat den **Python** tillägg i VS Code, Välj den **installera** knappen som du ser i följande bild:

    ![HDInsight Visual Studio Code Python-installation](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Installera Python-miljön i systemet om du inte redan har gjort. 
   - Windows, hämta och installera [Python](https://www.python.org/downloads/). Kontrollera `Python` och `pip` finns i systemet sökväg.

   - Anvisningar för macOS och Linux finns i [konfigurera interaktiv PySpark-miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Välja ett kluster som du vill skicka din PySpark-fråga. Snart därefter visas frågeresultatet i den nya rätta fliken:

   ![Skicka resultat för Python-jobb](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Verktyget stöder också den **SQL-satsen** fråga.

   ![Skicka Python jobb resultatet](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) status för ansökan visas till vänster i nedre statusfältet när du kör frågor. Inte skicka andra frågor när statusen är **PySpark-Kernel (upptagen)**. 

>[!NOTE]
>Klustren kan underhålla sessionsinformation. Definierad variabel, funktionen och motsvarande värden hålls i sessionen, så att de kan refereras över flera tjänstanrop för samma kluster. 
 

## <a name="submit-pyspark-batch-job"></a>Skicka PySpark batch-jobb

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

5. Välja ett kluster som du vill skicka PySpark-jobb. 

   ![Skicka resultat för Python-jobb](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

När du skickar in ett Python-jobb, skicka loggarna visas i den **utdata** fönster i VS Code. Den **Spark Användargränssnittet URL** och **Yarn UI URL** visas också. Du kan öppna URL: en i en webbläsare för att visa jobbstatusen.

>[!NOTE]
>PySpark3 stöds inte längre i Livy 0.4 (vilket är HDI spark 2.2-kluster). Endast ”PySpark” har stöd för python. Det är känt problem som skickar till spark 2.2 misslyckas med python3.
   
## <a name="livy-configuration"></a>Livy-konfiguration
Livy konfiguration stöds, den kan ställas in på projektinställningarna i arbetsmappar utrymme. Mer information finns i [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

+ Projektinställningar:

    ![Livy-konfiguration](./media/hdinsight-for-vscode/hdi-livyconfig.png)

+ De Livy-konfigurationerna som stöds:   

    **POST /batches**   
    Begärandetext

    | namn | beskrivning | typ | 
    | :- | :- | :- | 
    | fil | Fil som innehåller programmet ska köras | sökvägen (krävs) | 
    | proxyUser | Användaren att personifiera när jobbet körs | sträng | 
    | Klassnamn | Programmet Java/Spark-huvudklass | sträng |
    | args | Kommandoradsargument för programmet | lista med strängar | 
    | JAR-filer | JAR-filer som ska användas i den här sessionen | Lista över strängar | 
    | pyFiles | Python-filer som ska användas i den här sessionen | Lista över strängar |
    | filer | filer som ska användas i den här sessionen | Lista över strängar |
    | driverMemory | Mängden minne som ska användas för processen för drivrutinen | sträng |
    | driverCores | Antalet kärnor som ska användas för processen för drivrutinen | int |
    | executorMemory | Mängden minne för att använda per körningsprocess | sträng |
    | executorCores | Antalet kärnor som ska användas för varje executor | int |
    | numExecutors | Antal executors att starta för den här sessionen | int |
    | Arkiv | Arkiv som ska användas i den här sessionen | Lista över strängar |
    | kö | Namnet på YARN-kö som har skickats | sträng |
    | namn | Namnet på den här sessionen | sträng |
    | conf | Egenskaper för Spark-konfiguration | Karta över nyckel = värde |

    Svarstext   
    Det Batch-objektet.

    | namn | beskrivning | typ | 
    | :- | :- | :- | 
    | id | Sessions-id | int | 
    | appId | Program-id för den här sessionen |  Sträng |
    | appInfo | Detaljerad programinformation | Karta över nyckel = värde |
    | log | Log-rader | lista med strängar |
    | state |   Batch-tillstånd | sträng |


## <a name="additional-features"></a>Ytterligare funktioner

HDInsight för VS Code har stöd för följande funktioner:

- **IntelliSense automatisk komplettering**. Förslag för nyckelord, metoder, variabler och så vidare. Olika ikoner representerar olika typer av objekt.

    ![HDInsight Tools för Visual Studio Code, IntelliSense objekttyper](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense fel markör**. Tjänsten språk understryker redigering fel för Hive-skriptet.     
- **Syntax höjdpunkter**. Språk-tjänsten använder olika färger för att skilja variabler, nyckelord, datatyp, funktioner och så vidare. 

    ![HDInsight Tools för Visual Studio Code syntax höjdpunkter](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Nästa steg

### <a name="demo"></a>Demo
* HDInsight för VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Felsöka Spark-program via fjärranslutning via VPN med hjälp av Azure Toolkit för IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Felsöka Spark-program via fjärranslutning via SSH med hjälp av Azure Toolkit för IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda HDInsight Tools för IntelliJ med begränsat Hortonworks-läge](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Använda HDInsight-verktyg i Azure Toolkit för Eclipse för att skapa Spark-program](spark/apache-spark-eclipse-tool-plugin.md)
* [Använda Zeppelin-anteckningsböcker med ett Spark-kluster i HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernlar som är tillgängliga för Jupyter Notebook i Spark-klustret för HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisera Hive-data med Microsoft Power BI i Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Ställ in interaktiv PySpark-miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Använda Zeppelin för att köra Hive-frågor i Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scenarier
* [Spark med BI: Utföra interaktiv dataanalys med hjälp av Spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för analys av byggnadstemperatur med HVAC-data](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark med Machine Learning: Använda Spark i HDInsight för att förutsäga resultatet av en livsmedelskontroll](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Webbplatslogganalys med Spark i HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Skapa och köra program
* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Spark-kluster med Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Hantera resurser
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](spark/apache-spark-job-debugging.md)



