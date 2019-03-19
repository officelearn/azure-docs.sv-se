---
title: Azure HDInsight Tools - använder Visual Studio Code för Hive, LLAP eller PySpark | Microsoft Docs
description: Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code för att skapa och skicka frågor och skript.
Keywords: Visual Studio Code, Azure HDInsight-verktyg, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, interaktiv fråga
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 71e12e661c704af028ac4dc48f255bdee980619c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100334"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Använd Azure HDInsight Tools för Visual Studio Code

Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code för att skapa och skicka Apache Hive-batchjobb, interaktiva Hive-frågor och PySpark-skript för Apache Spark. Först kommer vi att beskriva hur du installerar HDInsight-verktygen i Visual Studio Code och sedan vi gå igenom hur du skickar in jobb för att Hive och Spark.  

Azure HDInsight-verktyg som kan installeras på plattformar som stöds av Visual Studio Code, inklusive Windows, Linux och macOS. Nedan hittar du de nödvändiga förutsättningarna för olika plattformar.


## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra stegen i den här artikeln:

- Ett HDInsight-kluster. För att skapa ett kluster, se [Kom igång med HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono är endast krävs för Linux och macOS.
- Den [tillägget Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för Visual Studio Code.
- [Ställ in interaktiv PySpark-miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md).
- En lokal katalog med namnet **HDexample**.  Den här artikeln använder **C:\HD\HDexample**.

## <a name="install-azure-hdinsight-tools"></a>Installera Azure HDInsight-verktyg

När du har slutfört förutsättningarna kan installera du Azure HDInsight Tools för Visual Studio Code.  Utför följande steg för att installera Azure HDInsight-verktyg:

1. Öppna Visual Studio Code.

2. Från menyraden navigerar du till **visa** > **tillägg**.

3. I sökrutan anger **HDInsight**.

4. Välj **Azure HDInsight Tools** sökresultat och välj sedan **installera**.  

   ![HDInsight Visual Studio Code Python-installation](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Välj **Reload** att aktivera den **Azure HDInsight Tools** tillägget när det har installerats.


## <a name="open-hdinsight-work-folder"></a>Öppna arbetsmapp för HDInsight

Utför följande steg för att öppna en arbetsmapp och skapa en fil i Visual Studio Code:

1. Från menyraden navigerar du till **filen** > **Öppna mapp...**   >  **C:\HD\HDexample**och välj sedan den **Välj mapp** knappen. Mappen visas i den **Explorer** vyn till vänster.

2. Från den **Explorer** väljer du mappen **HDexample**, och sedan den **ny fil** ikonen bredvid Arbetsmappens.

   ![Ny fil](./media/hdinsight-for-vscode/new-file.png)

3. Namnge den nya filen med filnamnstillägget .py (Spark-skriptet) eller .hql (Hive-frågor).  Det här exemplet används **HelloWorld.hql**.

## <a name="connect-to-hdinsight-cluster"></a>Ansluta till HDInsight-kluster

Innan du kan skicka skript till HDInsight-kluster från Visual Studio Code, måste du ansluta till ditt Azure-konto eller länka ett kluster (med hjälp av Ambari användarnamn/lösenord eller domänanslutna konto).  Utför följande steg för att ansluta till Azure:

1. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange **HDInsight: Logga in**.

    ![HDInsight Tools för Visual Studio Code-inloggning](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Följ anvisningarna för inloggning i den **utdata** fönstret.
    + För globala Azure-miljön, **HDInsight: Logga in** kommandot utlöser **logga in på Azure** åtgärd i HDInsight explorer och vice versa.

        ![Inloggningsinstruktioner för azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + För andra miljöer, följer du instruktionerna för inloggning.

        ![Inloggningsinstruktioner för andra miljöer](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   När du är ansluten, visas namnet på ditt Azure i statusfältet längst ned till vänster i Visual Studio Code-fönstret.  
  

<h2 id="linkcluster">Skapa länk: Azure HDInsight</h2>

Du kan länka ett normalt kluster med hjälp av en [Apache Ambari](https://ambari.apache.org/) hanteras användarnamn eller länka ett Enterprise Security Pack säker Hadoop-kluster med hjälp av en domän-användarnamn (t.ex: user1@contoso.com).

1. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange **HDInsight: Länka ett kluster**.

   ![länken cluster kommando](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Välj länkade klustertyp **Azure HDInsight**.

3. Ange URL: en för HDInsight-kluster.

4. Ange användarnamn för Ambari, standardvärdet är **admin**.

5. Ange Ambari-lösenord.

6. Välj typ av kluster.

7. Granska **utdata** vy för verifiering.

   > [!NOTE]  
   > Länkade användarnamn och lösenord används om klustret både loggas i Azure-prenumeration och länkad ett kluster.  


## <a name="create-link-generic-livy-endpoint"></a>Skapa länk: Allmän Livy slutpunkt

1. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange **HDInsight: Länka ett kluster**.

2. Välj länkade klustertyp **allmän Livy Endpoint**.

3. Ange allmänna Livy slutpunkten, till exempel: http\:/ / 10.172.41.42:18080.

4. Välj auktoriseringstyp av **grundläggande** eller **ingen**.  Om **grundläggande**, sedan:  
    &emsp;a. Ange användarnamn för Ambari, standardvärdet är **admin**.  
    &emsp;b. Ange Ambari-lösenord.

5. Granska **utdata** vy för verifiering.

## <a name="list-hdinsight-clusters"></a>Lista över HDInsight-kluster

1. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange **HDInsight: Klustret visas**.

2. Välj den önskade prenumerationen.

3. Granska den **utdata** vy.  Vyn visar din länkade kluster och alla kluster under din Azure-prenumeration.

    ![Ange en standardkonfiguration för kluster](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Ange standard-kluster

1. Öppnar mappen **HDexample** skapade [tidigare](#open-hdinsight-work-folder) om stängd.  

2. Välj filen **HelloWorld.hql** skapade [tidigare](#open-hdinsight-work-folder) så öppnas i Skriptredigeraren.

3. [Ansluta](#connect-to-hdinsight-cluster) på Azure-kontot om du inte gjort det ännu.

4. Högerklicka på Skriptredigeraren och välj **HDInsight: Ange Standardkluster**.  

5. Välj ett kluster som standardklustret för den aktuella skriptfilen. Verktygen automatiskt uppdatera konfigurationsfilen **. VSCode\settings.json**. 

   ![Ställ in standardkonfiguration för kluster](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Ange Azure-miljön

1. [Ansluta](#connect-to-hdinsight-cluster) på Azure-kontot om du inte gjort det ännu.

2. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange **HDInsight: Ange Azure-miljön**.

3. Välj en miljö som din standardalternativet för inloggning.

4. Under tiden kan verktyget redan har sparats din inloggning standardposten i **. VSCode\settings.json**. Du kan också direkt uppdatera den i den här konfigurationsfilen. 

   ![Ange standard inloggningen post konfiguration](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Skicka interaktiva Hive-frågor, Hive kommandoskript

Med HDInsight Tools för Visual Studio Code, kan du skicka interaktiva Hive-frågor och Hive batch skript till HDInsight-kluster.

1. Öppna mappen **HDexample** skapade [tidigare](#open-hdinsight-work-folder) om stängd.  

2. Välj filen **HelloWorld.hql** skapade [tidigare](#open-hdinsight-work-folder) så öppnas i Skriptredigeraren.

3. [Ansluta](#connect-to-hdinsight-cluster) på Azure-kontot om du inte gjort det ännu.

4. Sedan kopierar du och klistrar in följande kod i din Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. Högerklicka på Skriptredigeraren, Välj **HDInsight: Hive interaktiv** skicka frågan eller använder genvägen **Ctrl + Alt + I**.  Välj **HDInsight: Hive Batch** skicka skriptet eller använder genvägen **Ctrl + Alt + H**.  

6. Välj klustret om du inte har angett ett standardkluster. Med verktygen kan du dessutom skicka ett kodblock istället för hela skriptfilen med snabbmenyn. Efter en liten stund visas resultatet av frågan i en ny flik.

   ![Interaktiva Hive-resultat](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panelen **RESULTAT**: Du kan spara hela resultatet som en CSV-, JSON- eller Excel-fil till en lokal sökväg, eller markera flera rader.

    - Panelen **MEDDELANDEN**: När du väljer **Rad**nummer, leder det till den första raden i det aktuella skriptet.

## <a name="submit-interactive-pyspark-queries"></a>Köra en interaktiv PySpark-fråga

1. Öppna mappen **HDexample** skapade [tidigare](#open-hdinsight-work-folder) om stängd.  

2. Skapa en ny fil **HelloWorld.py** följande den [tidigare](#open-hdinsight-work-folder) steg.

3. Du får en Python-tillägg rekommendation dialogruta om du inte har installerat Python för nödvändiga.  Installera och läsa in Visual Studio Code för att slutföra installationen.

    >![HDInsight Visual Studio Code Python-installation](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [Ansluta](#connect-to-hdinsight-cluster) på Azure-kontot om du inte gjort det ännu.

5. Kopiera och klistra in följande kod i skriptfilen:
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

6. Högerklicka på Skriptredigeraren, Välj **HDInsight: Interaktiv PySpark** skicka frågan eller använder genvägen **Ctrl + Alt + I**.  

7. Välj klustret om du inte har angett ett standardkluster. Med verktygen kan du dessutom skicka ett kodblock istället för hela skriptfilen med snabbmenyn. Efter en liten stund visas resultatet av frågan i en ny flik.

   ![Skicka resultat för Python-jobb](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. Verktyget stöder också den **SQL-satsen** fråga.

   ![Skicka Python jobb resultatet](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) status för ansökan visas till vänster i nedre statusfältet när du kör frågor. Inte skicka andra frågor när statusen är **PySpark-Kernel (upptagen)**.  

>[!NOTE]  
>Klustren kan underhålla sessionsinformation. Definierad variabel, funktionen och motsvarande värden hålls i sessionen, så att de kan refereras över flera tjänstanrop för samma kluster. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3 stöds inte med Spark2.2/2.3

PySpark3 stöds inte längre med Apache Spark 2.2 och Spark2.3 klustret, endast ”PySpark” stöds för Python. Det är känt problem som skickas till spark 2.2/2.3 misslyckas med Python3.

   ![Skicka till fel vid hämtning av python3](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Följ anvisningarna för att använda Python2.x: 

1. Installera Python 2.7 till lokala datorn och lägger till den i systemsökvägen.

2. Starta om Visual Studio Code.

3. Växla till Python 2 genom att klicka på den **Python XXX** enhetens status menyraden och väljer sedan målet Python.

   ![Välj python-version](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>Skicka PySpark batch-jobb

1. Öppna mappen **HDexample** skapade [tidigare](#open-hdinsight-work-folder) om stängd.  

2. Skapa en ny fil **BatchFile.py** följande den [tidigare](#open-hdinsight-work-folder) steg.

3. [Ansluta](#connect-to-hdinsight-cluster) på Azure-kontot om du inte gjort det ännu.

4. Kopiera och klistra in följande kod i skriptfilen:

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

4. Högerklicka på skriptredigeraren och välj sedan **HDInsight: PySpark Batch**, eller använder genväg **Ctrl + Alt + H**. 

5. Välja ett kluster som du vill skicka PySpark-jobb. 

   ![Skicka resultat för Python-jobb](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

När du skickar in ett Python-jobb, skicka loggarna visas i den **utdata** fönstret i Visual Studio Code. Den **Spark Användargränssnittet URL** och **Yarn UI URL** visas också. Du kan öppna URL: en i en webbläsare för att visa jobbstatusen.

## <a name="apache-livy-configuration"></a>Apache Livy konfiguration

[Apache Livy](https://livy.incubator.apache.org/) konfiguration stöds, kan ställas in på den **. VSCode\settings.json** i mappen arbete utrymme. Livy-konfigurationen stöder för närvarande endast Python-skriptet. Mer information finns i [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Hur du utlöser livy konfiguration**

Metod 1  
1. Från menyraden navigerar du till **filen** > **inställningar** > **inställningar**.  
2. I den **sökinställningar** textrutan Ange **Sumission för HDInsight-jobb: Livy Conf**.  
3. Välj **redigera i settings.json** för det relevanta sökresultatet.

Metod 2   
Skickar en fil kan du se mappen .vscode läggs automatiskt till mappen arbete. Du kan hitta livy-konfigurationen genom att klicka på **.vscode\settings.json**.

+ Projektinställningar:

    ![Livy-konfiguration](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>För inställningar **driverMomory** och **executorMomry**, anger du värdet med enhet, till exempel 1 g eller 1 024 m. 

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
    | appId | Program-id för den här sessionen |  String |
    | appInfo | Detaljerad programinformation | Karta över nyckel = värde |
    | log | Log-rader | lista med strängar |
    | state |   Batch-tillstånd | sträng |

>[!NOTE]
>Tilldelade livy konfig visas i utdatafönstret när skicka skript.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrera med Azure HDInsight i Utforskaren

**Azure HDInsight** läggs till i Explorer-vy. Du kan söka efter och hantera du kluster direkt via **Azure HDInsight**.

1. [Ansluta](#connect-to-hdinsight-cluster) på Azure-kontot om du inte gjort det ännu.

2. Från menyraden navigerar du till **visa** > **Explorer**.

3. I den vänstra rutan expanderar **AZURE HDINSIGHT**.  De tillgängliga prenumerationerna och kluster (Spark, Hadoop och HBase stöds) visas. 

   ![Azure HDInsight-prenumeration](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Expandera klustret om du vill visa hive-metadata-databasen och tabellschemat.

   ![Azure HDInsight-kluster](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>Ytterligare funktioner

HDInsight för Visual Studio Code har stöd för följande funktioner:

- **IntelliSense automatisk komplettering**. Förslag för nyckelord, metoder, variabler och så vidare. Olika ikoner representerar olika typer av objekt.

    ![HDInsight Tools för Visual Studio Code, IntelliSense objekttyper](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense fel markör**. Tjänsten språk understryker redigering fel för Hive-skriptet.     
- **Syntax höjdpunkter**. Språk-tjänsten använder olika färger för att skilja variabler, nyckelord, datatyp, funktioner och så vidare. 

    ![HDInsight Tools för Visual Studio Code syntax höjdpunkter](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>Ta bort länken till kluster

1. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange sedan **HDInsight: Ta bort länken till ett kluster**.  

2. Markera klustret att Avlänka.  

3. Granska **utdata** vy för verifiering.  


## <a name="logout"></a>Utloggning  

Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange sedan **HDInsight: Logga ut**.  Det är ett popup-fönster i det nedre högra hörnet om **utloggning har!**.


## <a name="next-steps"></a>Nästa steg
Se en demonstration video för att använda HDInsight för Visual Studio Code [HDInsight för Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
