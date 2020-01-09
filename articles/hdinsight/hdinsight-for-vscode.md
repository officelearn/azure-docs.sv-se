---
title: Azure HDInsight för Visual Studio Code
description: Lär dig hur du använder Spark & Hive-verktyg (Azure HDInsight) för Visual Studio Code för att skapa och skicka frågor och skript.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435673"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Använda Spark & Hive-verktyg för Visual Studio Code

Lär dig hur du använder Spark & Hive-verktyg för Visual Studio Code för att skapa och skicka Apache Hive batch-jobb, interaktiva Hive-frågor och PySpark-skript för Apache Spark. Först beskriver vi hur du installerar Spark & Hive-verktyg i Visual Studio Code, och sedan kommer vi att gå igenom hur du skickar jobb till Spark & Hive-verktyg.  

Spark & Hive-verktyg kan installeras på plattformar som stöds av Visual Studio Code, inklusive Windows, Linux och macOS. Observera följande krav för olika plattformar.

## <a name="prerequisites"></a>Krav

Följande objekt krävs för att slutföra stegen i den här artikeln:

- Ett Azure HDInsight-kluster. Information om hur du skapar ett kluster finns i [Kom igång med HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Eller Använd ett Spark-och Hive-kluster som har stöd för en Apache livy-slutpunkt.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono krävs bara för Linux och macOS.
- [En PySpark interaktiv miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md).
- En lokal katalog. I den här artikeln används **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Installera Spark & Hive-verktyg

När du uppfyller kraven kan du installera Spark & Hive-verktyg för Visual Studio Code genom att följa dessa steg:

1. Öppna Visual Studio Code.

2. I meny raden navigerar du till **visa** > - **tillägg**.

3. I rutan Sök anger du **Spark & Hive**.

4. Välj **Spark & Hive-verktyg** från Sök resultaten och välj sedan **Installera**:

   ![Spark & Hive för Visual Studio Code python-installation](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Välj **Läs in igen** när det behövs.

## <a name="open-a-work-folder"></a>Öppna en arbetsmapp

Följ dessa steg om du vill öppna en arbetsmapp och skapa en fil i Visual Studio Code:

1. Från meny raden går du till **fil** > **Öppna mapp...**  > **C:\HD\HDexample**och väljer sedan knappen **Välj mapp** . Mappen visas **i trädvyn till** vänster.

2. I **Utforskarvyn** väljer du mappen **HDexample** och väljer sedan ikonen **ny fil** bredvid arbetsmappen:

   ![ikon för ny fil i Visual Studio-kod](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Namnge den nya filen genom att antingen använda `.hql` (Hive-frågor) eller fil tillägget `.py` (Spark-skript). I det här exemplet används **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Ange Azure-miljön

För en nationell moln användare följer du de här stegen för att konfigurera Azure-miljön först och använder sedan kommandot **Azure: Sign in** för att logga in på Azure:

1. Navigera till **fil** > **Inställningar** > **Inställningar**.
2. Sök efter följande sträng: **Azure: Cloud**.
3. Välj det nationella molnet i listan:

   ![Ange standard konfiguration för inloggnings post](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Ansluta till ett Azure-konto

Innan du kan skicka skript till dina kluster från Visual Studio Code måste du antingen ansluta till ditt Azure-konto eller länka ett kluster (med Apache Ambari användar namn och lösen ord för autentiseringsuppgifter eller ett domänanslutet konto). Följ dessa steg för att ansluta till Azure:

1. I meny raden navigerar du till **visa** > **kommando palett...** och anger **Azure: Logga**in:

    ![Spark & Hive-verktyg för Visual Studio Code login](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Följ anvisningarna i inloggningen för att logga in på Azure. När du är ansluten visas namnet på ditt Azure-konto i statusfältet längst ned i Visual Studio Code-fönstret.  

## <a name="link-a-cluster"></a>Länka ett kluster

### <a name="link-azure-hdinsight"></a>Länk: Azure HDInsight

Du kan länka ett vanligt kluster genom att använda ett [Apache Ambari](https://ambari.apache.org/)-hanterat användar namn, eller så kan du länka ett skyddat Hadoop-kluster för Enterprise Security genom att använda ett domän användar namn (t. ex.: `user1@contoso.com`).

1. I meny raden navigerar du till **visa** > **kommando palett...** och anger **Spark/Hive: länka ett kluster**.

   ![Kommando rads verktyget länk kluster kommando](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Välj länkat kluster typ **Azure HDInsight**.

3. Ange HDInsight-klustrets URL.

4. Ange ditt Ambari-användarnamn; Standardvärdet är **admin**.

5. Ange ditt Ambari-lösenord.

6. Välj kluster typ.

7. Ange klusterets visnings namn (valfritt).

8. Granska vyn **utdata** för verifiering.

   > [!NOTE]  
   > Det länkade användar namnet och lösen ordet används om klustret både är inloggat i Azure-prenumerationen och länkat ett kluster.  

### <a name="link-generic-livy-endpoint"></a>Länk: allmän livy-slutpunkt

1. I meny raden navigerar du till **visa** > **kommando palett...** och anger **Spark/Hive: länka ett kluster**.

2. Välj den länkade kluster typen **allmän livy-slutpunkt**.

3. Ange den allmänna livy-slutpunkten. Exempel: http\://10.172.41.42:18080.

4. Välj typ av auktorisering **Basic** eller **none**.  Om du väljer **Basic**:  
    &emsp;a. Ange ditt Ambari-användarnamn; Standardvärdet är **admin**.  
    &emsp;b. Ange ditt Ambari-lösenord.

5. Granska vyn **utdata** för verifiering.

## <a name="list-clusters"></a>Lista kluster

1. I meny raden går du till **visa** > **kommando palett...** och anger **Spark/Hive: list kluster**.

2. Välj den prenumeration som du vill använda.

3. Granska vyn **utdata** . I den här vyn visas de länkade klustren (eller klustren) och alla kluster i din Azure-prenumeration:

    ![Ange en standard kluster konfiguration](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Ange standard kluster

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Välj filen **HelloWorld. HQL** som skapades [tidigare](#open-a-work-folder). Den öppnas i skript redigeraren.

3. Högerklicka på skript redigeraren och välj sedan **Spark/Hive: Ange standard kluster**.  

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Välj ett kluster som standard kluster för den aktuella skript filen. Verktygen uppdaterar automatiskt **. VSCode\settings.json** konfigurations fil:

   ![Ange standard kluster konfiguration](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Skicka interaktiva Hive-frågor och Hive-batch-skript

Med Spark & Hive-verktyg för Visual Studio Code kan du skicka interaktiva Hive-frågor och Hive-kommando skript till dina kluster.

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Välj filen **HelloWorld. HQL** som skapades [tidigare](#open-a-work-folder). Den öppnas i skript redigeraren.

3. Kopiera och klistra in följande kod i Hive-filen och spara den:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Högerklicka på skript redigeraren och välj **Hive: interaktiv** för att skicka frågan, eller använd kortkommandot Ctrl + Alt + I kortkommandot.  Välj **Hive: batch** för att skicka skriptet eller använd kortkommandot CTRL + ALT + H.  

6. Om du inte har angett ett standard kluster väljer du ett kluster. Med verktygen kan du också skicka in ett kodblock i stället för hela skript filen med hjälp av snabb menyn. Efter en liten stund visas frågeresultaten på en ny flik:

   ![Frågeresultat för interaktiv Apache Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **Resultat** panel: du kan spara hela resultatet som en CSV-, JSON-eller Excel-fil till en lokal sökväg eller bara markera flera rader.

    - Panelen **meddelanden** : när du väljer ett **rad** nummer hoppar det till den första raden i skriptet som körs.

## <a name="submit-interactive-pyspark-queries"></a>Skicka interaktiva PySpark-frågor

Följ dessa steg om du vill skicka interaktiva PySpark-frågor:

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Skapa en ny **HelloWorld.py** -fil enligt de [tidigare](#open-a-work-folder) stegen.

3. Kopiera och klistra in följande kod i skript filen:

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

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Markera all kod, högerklicka på skript redigeraren och välj **Spark: PySpark Interactive** för att skicka frågan. Eller använd kortkommandot Ctrl + Alt + I.

   ![pyspark interaktiv snabb meny](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Välj klustret, om du inte har angett ett standard kluster. Efter en liten stund visas det **interaktiva python** -resultatet på en ny flik. Med verktygen kan du också skicka in ett kodblock i stället för hela skript filen med hjälp av snabb menyn:

   ![pyspark interaktiva python-fönster](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Ange **%% info**och tryck sedan på SKIFT + RETUR för att visa jobb informationen (valfritt):

   ![pyspark interaktiva Visa jobb information](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Verktyget stöder även **Spark SQL** -fr åga:

   ![Pyspark interaktiv vy resultat](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Sändnings statusen visas till vänster om det nedre statusfältet när du kör frågor. Skicka inte andra frågor när statusen är **PySpark kernel (upptagen)** .  

   > [!NOTE]
   >
   > När **python-tillägget har Aktiver ATS** rensas i inställningarna (den väljs som standard), kommer de skickade pyspark-interaktions resultaten att använda det gamla fönstret:
   >
   > ![pyspark interaktiv python-tillägg inaktiverat](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Skicka PySpark batch job

1. Öppna mappen **HDexample** som du [tidigare](#open-a-work-folder)beskrivit, om den är stängd.  

2. Skapa en ny **BatchFile.py** -fil genom att följa de [tidigare](#open-a-work-folder) stegen.

3. Kopiera och klistra in följande kod i skript filen:

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

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Högerklicka på skript redigeraren och välj sedan **Spark: PySpark batch**eller använd kortkommandot CTRL + ALT + H.

6. Välj ett kluster att skicka ditt PySpark-jobb till:

   ![Skicka python jobb resultat utdata](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

När du har skickat ett python-jobb visas sändnings loggar i fönstret **utdata** i Visual Studio Code. URL: en för Spark UI URL och garn-UI visas också. Du kan öppna webb adressen i en webbläsare för att spåra jobbets status.

## <a name="apache-livy-configuration"></a>Apache livy-konfiguration

[Apache livy](https://livy.incubator.apache.org/) -konfiguration stöds. Du kan konfigurera den i **. VSCode\settings.json** -fil i mappen arbetsytan. För närvarande stöder livy-konfiguration endast Python-skript. Mer information finns i [LIVY viktigt](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Så här utlöser du livy-konfigurationen**

Metod 1  
1. I meny raden navigerar du till **fil** > **Inställningar** > **Inställningar**.
2. I rutan **Sök inställningar** anger du **insändningen av HDInsight-jobb: livy conf**.  
3. Välj **Redigera i Settings. JSON** för det relevanta Sök resultatet.

Metod 2 skicka en fil och Lägg märke till att mappen. VSCode automatiskt läggs till i arbetsmappen. Du kan se livy-konfigurationen genom att välja **. vscode\settings.JSON**.

+ Projekt inställningarna:

    ![HDInsight Apache livy-konfiguration](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Ange värdet och enheten för inställningarna **driverMemory** och **executorMemory** . Till exempel: 1G eller 1 024.

+ Livy-konfigurationer som stöds:

    **Publicera/batches** Brödtext i begäran

    | namn | description | typ |
    | :- | :- | :- |
    | file | Fil som innehåller det program som ska köras | Sökväg (obligatoriskt) |
    | proxyUser | Användare som ska personifieras när jobbet körs | String |
    | className | Program java/Spark, huvud klass | String |
    | args | Kommando rads argument för programmet | Lista över strängar |
    | jars | Jar v7 som ska användas i den här sessionen | Lista över strängar | 
    | pyFiles | Python-filer som ska användas i den här sessionen | Lista över strängar |
    | files | Filer som ska användas i den här sessionen | Lista över strängar |
    | driverMemory | Mängd minne som ska användas för driv rutins processen | String |
    | driverCores | Antal kärnor som ska användas för driv rutins processen | Int |
    | executorMemory | Mängden minne som ska användas per utförar-process | String |
    | executorCores | Antal kärnor som ska användas för varje utförar | Int |
    | numExecutors | Antal körningar som ska startas för den här sessionen | Int |
    | archives | Arkiv som ska användas i den här sessionen | Lista över strängar |
    | kö | Namnet på den garn kö som ska skickas till| String |
    | namn | Namnet på den här sessionen | String |
    | conf | Konfigurations egenskaper för Spark | Karta över nyckel = val |

    Svars text för det skapade batch-objektet.

    | namn | description | typ |
    | :- | :- | :- |
    | id | Sessions-ID | Int |
    | appId | Programmets ID för den här sessionen | String |
    | appInfo | Detaljerad programinformation | Karta över nyckel = val |
    | log | Logg rader | Lista över strängar |
    | state |Batch-tillstånd | String |

    > [!NOTE]
    > Den tilldelade livy-konfigurationen visas i fönstret utdata när du skickar skriptet.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrera med Azure HDInsight från Utforskaren

Du kan förhandsgranska Hive-tabellen i klustren direkt via **Azure HDInsight** Explorer:

1. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto om du inte redan har gjort det.

2. Välj **Azure** -ikonen från kolumnen längst till vänster.

3. I den vänstra rutan expanderar du **Azure: HDInsight**. Tillgängliga prenumerationer och kluster visas.

4. Expandera klustret om du vill visa Hive metadata-databasen och tabell schemat.

5. Högerklicka på Hive-tabellen. Till exempel: **hivesampletable**. Välj för **hands version**.

   ![Spark & Hive för Visual Studio Code för hands version av Hive-tabell](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Fönstret för **förhands gransknings resultat** öppnas:

   ![Spark & Hive för resultat fönstret för Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- RESULTAT panel

   Du kan spara hela resultatet som en CSV-, JSON-eller Excel-fil till en lokal sökväg eller bara markera flera rader.

- Panelen meddelanden
   1. När antalet rader i tabellen är större än 100 visas följande meddelande: "de första 100 raderna visas för Hive-tabellen."
   2. När antalet rader i tabellen är mindre än eller lika med 100 visas ett meddelande som liknar följande: "60 rader visas för Hive-tabellen".
   3. När det inte finns något innehåll i tabellen visas följande meddelande: "0 rader visas för Hive-tabell".

        >[!NOTE]
        >
        >I Linux installerar du xclip för att aktivera kopiera tabell data.
        >
        >![Spark & Hive för Visual Studio Code i Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Ytterligare funktioner

Spark & Hive för Visual Studio Code stöder också följande funktioner:

- **IntelliSense-komplettering**. Förslag på popup för nyckelord, metoder, variabler och andra programmerings element. Olika ikoner representerar olika typer av objekt:

    ![Spark & Hive-verktyg för Visual Studio Codes IntelliSense-objekt](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense-felmarkör**. Språk tjänsten stryker under redigering av fel i Hive-skriptet.     
- **Egenskaper för syntax**. Språk tjänsten använder olika färger för att särskilja variabler, nyckelord, datatyp, funktioner och andra programmerings element:

    ![Spark & Hive-verktyg för Visual Studio Code syntax](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Roll som endast läsare

Användare som har tilldelats rollen som endast läsare för klustret kan inte längre skicka jobb till HDInsight-klustret, eller så kan de inte Visa Hive-databasen. Kontakta kluster administratören för att uppgradera rollen till [**HDInsight-klustrets operator**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) i [Azure Portal](https://ms.portal.azure.com/). Om du har giltiga Ambari-autentiseringsuppgifter kan du manuellt länka klustret med hjälp av följande rikt linjer.

### <a name="browse-the-hdinsight-cluster"></a>Bläddra i HDInsight-klustret  

När du väljer Azure HDInsight Explorer för att expandera ett HDInsight-kluster uppmanas du att länka klustret om du har rollen som endast läsare för klustret. Använd följande metod för att länka till klustret med hjälp av dina Ambari-autentiseringsuppgifter.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Skicka jobbet till HDInsight-klustret

När du skickar jobb till ett HDInsight-kluster uppmanas du att länka klustret om du är i den skrivskyddade rollen för klustret. Använd följande steg för att länka till klustret med hjälp av Ambari-autentiseringsuppgifter.

### <a name="link-to-the-cluster"></a>Länk till klustret

1. Ange ett giltigt Ambari-användarnamn.
2. Ange ett giltigt lösen ord.

   ![Spark & Hive-verktyg för Visual Studio Code användar namn](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Hive-verktyg för Visual Studio Code Password](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Du kan använda `Spark / Hive: List Cluster` för att kontrol lera det länkade klustret:
  >
  >![Spark & Hive-verktyg för Visual Studio Code Reader](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Bläddra i ett Data Lake Storage Gen2 konto

När du väljer Azure HDInsight Explorer för att expandera ett Data Lake Storage Gen2-konto uppmanas du att ange lagrings åtkomst nyckeln om ditt Azure-konto inte har åtkomst till Gen2-lagring. När åtkomst nyckeln har verifierats expanderas Data Lake Storage Gen2-kontot automatiskt.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Skicka jobb till ett HDInsight-kluster med Data Lake Storage Gen2

När du skickar ett jobb till ett HDInsight-kluster med hjälp av Data Lake Storage Gen2 uppmanas du att ange lagrings åtkomst nyckeln om ditt Azure-konto saknar skriv åtkomst till Gen2-lagring. När du har verifierat åtkomst nyckeln kommer jobbet att skickas.

![Spark & Hive-verktyg för Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Du kan hämta åtkomst nyckeln för lagrings kontot från Azure Portal. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Ta bort länk till kluster

1. I meny raden går du till **visa** > **Command-paletten**och anger **Spark/Hive: ta bort länk till ett kluster**.  

2. Välj ett kluster att ta bort länken.  

3. Se vyn **utdata** för verifiering.  

## <a name="sign-out"></a>Logga ut  

I meny raden går du till **visa** > **Command-paletten**och anger sedan **Azure: Logga ut**.

## <a name="next-steps"></a>Nästa steg

En video som visar hur man använder Spark & Hive för Visual Studio Code finns i [Spark & Hive för Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
