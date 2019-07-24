---
title: Azure HDInsight för Visual Studio Code
description: Lär dig hur du använder Spark & Hive-verktyg (Azure HDInsight) för Visual Studio Code för att skapa och skicka frågor och skript.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 31f6c34089c1825eca21283b01eae181c8112216
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312176"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Använda Spark & Hive-verktyg för Visual Studio Code

Lär dig hur du använder Spark & Hive-verktyg för Visual Studio Code för att skapa och skicka Apache Hive batch-jobb, interaktiva Hive-frågor och PySpark-skript för Apache Spark. Först beskriver vi hur du installerar Spark & Hive-verktygen i Visual Studio Code och sedan går vi igenom hur du skickar jobb till Hive och Spark.  

Spark & Hive-verktyg kan installeras på plattformar som stöds av Visual Studio Code, inklusive Windows, Linux och macOS. Nedan hittar du förutsättningarna för olika plattformar.


## <a name="prerequisites"></a>Förutsättningar

Följande objekt krävs för att slutföra stegen i den här artikeln:

- An-HDInsight kluster. Information om hur du skapar ett kluster finns i [Kom igång med HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Eller ett Spark/Hive-kluster som stöder livy-slutpunkt.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono krävs bara för Linux och macOS.
- [Konfigurera PySpark-interaktiv miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md).
- En lokal katalog med namnet **HDexample**.  I den här artikeln används **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Installera Spark & Hive-verktyg

När du har slutfört kraven kan du installera Spark & Hive-verktyg för Visual Studio Code.  Utför följande steg för att installera Spark & Hive-verktyg:

1. Öppna Visual Studio Code.

2. I meny raden navigerar du till **Visa** > **tillägg**.

3. I rutan Sök anger du **Spark & Hive**.

4. Välj **Spark & Hive-verktyg** från Sök resultaten och välj sedan **Installera**.  

   ![Spark & Hive för Visual Studio Code python-installation](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. **Läs in igen** när det behövs.


## <a name="open-work-folder"></a>Öppna arbetsmapp

Slutför följande steg för att öppna en arbetsmapp och skapa en fil i Visual Studio Code:

1. I meny raden navigerar du till **filen** > **Öppna mapp...** C:\HD\HDexample och välj sedan knappen **Välj mapp** .   >  Mappen visas **i trädvyn till** vänster.

2. I trädvyn **väljer** du mappen, **HDexample**och sedan den **nya fil** ikonen bredvid arbetsmappen.

   ![Ny fil](./media/hdinsight-for-vscode/new-file.png)

3. Namnge den nya filen med antingen `.hql` fil namns tillägget (Hive-frågor) `.py` eller (Spark-skript).  I det här exemplet används **HelloWorld. HQL**.

## <a name="set-the-azure-environment"></a>Ange Azure-miljön

För nationell moln användare följer du stegen för att ställa in Azure-miljön först och **sedan använder du Azure: Inloggnings** kommando för att logga in på Azure.
   
1. Klicka på **File\Preferences\Settings**.
2. Sök **i Azure: Kunde**
3. Välj det nationella molnet i listan.

   ![Ange standard konfiguration för inloggnings post](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Anslut till Azure-konto

Innan du kan skicka skript till dina kluster från Visual Studio Code måste du antingen ansluta till ditt Azure-konto eller länka ett kluster (med Ambari användar namn/lösen ord eller domänanslutet konto).  Utför följande steg för att ansluta till Azure:

1. Från meny raden navigerar du till **Visa** > **kommando paletten...** och **anger Azure: Logga in**.

    ![Spark & Hive-verktyg för Visual Studio Code login](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Följ anvisningarna för inloggning för att logga in på Azure. När du är ansluten visas namnet på ditt Azure-konto i statusfältet längst ned i Visual Studio Code-fönstret.  
  

## <a name="link-a-cluster"></a>Länka ett kluster

### <a name="link-azure-hdinsight"></a>Operationsföljdslänkkod Azure HDInsight

Du kan länka ett vanligt kluster genom att använda ett [Apache Ambari](https://ambari.apache.org/) -hanterat användar namn eller länka ett skyddat Hadoop-kluster i Enterprise Security-paketet genom att user1@contoso.comanvända ett domän användar namn (t. ex.:).

1. Från meny raden navigerar du till **Visa** > **kommando paletten...** och **anger Spark/Hive: Länka ett kluster**.

   ![länka kluster kommando](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Välj länkat kluster typ **Azure HDInsight**.

3. Ange HDInsight-kluster-URL.

4. Ange användar namnet för Ambari, standard är **admin**.

5. Ange Ambari-lösenord.

6. Välj kluster typ.

7. Ange klusterets visnings namn (valfritt).

8. Granska vyn **utdata** för verifiering.

   > [!NOTE]  
   > Det länkade användar namnet och lösen ordet används om klustret både är inloggat i Azure-prenumerationen och länkat ett kluster.  


### <a name="link-generic-livy-endpoint"></a>Operationsföljdslänkkod Allmän livy-slutpunkt

1. Från meny raden navigerar du till **Visa** > **kommando paletten...** och **anger Spark/Hive: Länka ett kluster**.

2. Välj den länkade kluster typen **allmän livy-slutpunkt**.

3. Ange den allmänna livy-slutpunkten, till\:exempel: http//10.172.41.42:18080.

4. Välj typ av auktorisering **Basic** eller **none**.  Om **grundläggande**, sedan:  
    &emsp;a. Ange användar namnet för Ambari, standard är **admin**.  
    &emsp;b. Ange Ambari-lösenord.

5. Granska vyn **utdata** för verifiering.

## <a name="list-clusters"></a>Lista kluster

1. Från meny raden navigerar du till **Visa** > **kommando paletten...** och **anger Spark/Hive: Lista kluster**.

2. Välj önskad prenumeration.

3. Granska vyn **utdata** .  I vyn visas de länkade klustren och alla kluster i din Azure-prenumeration.

    ![Ange en standard kluster konfiguration](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Ange standard kluster

1. Öppna mappen **HDexample** som skapades [tidigare](#open-work-folder) om den stängdes.  

2. Välj filen **HelloWorld. HQL** som skapades [tidigare](#open-work-folder) och öppnas i skript redigeraren.

3. Högerklicka på skript redigeraren och välj **Spark/Hive: Ange standard kluster**.  

4. [Anslut](#connect-to-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Välj ett kluster som standard kluster för den aktuella skript filen. Verktyget uppdaterar konfigurations filen automatiskt **. VSCode\settings.json**. 

   ![Ange standard kluster konfiguration](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Skicka interaktiva Hive-frågor, Hive-batch-skript

Med Spark & Hive-verktyg för Visual Studio Code kan du skicka interaktiva Hive-frågor och Hive-kommando skript till dina kluster.

1. Öppna mappen **HDexample** som skapades [tidigare](#open-work-folder) om den stängdes.  

2. Välj filen **HelloWorld. HQL** som skapades [tidigare](#open-work-folder) och öppnas i skript redigeraren.


3. Sedan kopierar du och klistrar in följande kod i din Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Anslut](#connect-to-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Högerklicka på skript redigeraren, Välj **Hive: Interaktiv** för att skicka frågan eller använd kortkommando **CTRL + ALT + I**.  Välj **Hive: Batch** för att skicka skriptet eller använda kortkommando **CTRL + ALT + H**.  

6. Välj klustret om du inte har angett ett standard kluster. Med verktygen kan du dessutom skicka ett kodblock istället för hela skriptfilen med snabbmenyn. Efter en liten stund visas frågeresultaten på en ny flik.

   ![Interaktiva Hive-resultat](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panelen **RESULTAT**: Du kan spara hela resultatet som en CSV-, JSON- eller Excel-fil till en lokal sökväg, eller markera flera rader.

    - Panelen **MEDDELANDEN**: När du väljer **Rad**nummer, leder det till den första raden i det aktuella skriptet.

## <a name="submit-interactive-pyspark-queries"></a>Skicka interaktiva PySpark-frågor

Du kan skicka interaktiva PySpark-frågor genom att följa stegen nedan:

1. Öppna mappen **HDexample** som skapades [tidigare](#open-work-folder) om den stängdes.  

2. Skapa en ny fil **HelloWorld.py** följer de [tidigare](#open-work-folder) stegen.

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

4. [Anslut](#connect-to-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Välj all kod och högerklicka på skript redigeraren, Välj **Spark: PySpark Interactive** för att skicka frågan, eller använd kortkommando **CTRL + ALT + I**.

   ![pyspark interaktiv snabb meny](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Välj klustret om du inte har angett ett standard kluster. Efter en liten stund visas det **interaktiva python** -resultatet på en ny flik. Med verktygen kan du dessutom skicka ett kodblock istället för hela skriptfilen med snabbmenyn. 

   ![pyspark interaktiva python-fönster](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Ange **%% info**och tryck sedan på **SKIFT + RETUR** för att visa jobb information. Valfritt

   ![Visa jobb information](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Verktyget stöder även **Spark SQL** -frågan.

   ![Pyspark interaktiv vy resultat](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Sändnings statusen visas till vänster om det nedersta statusfältet när du kör frågor. Skicka inte andra frågor när statusen är **PySpark kernel (upptagen)** .  

   > [!NOTE] 
   >
   > När **python-tillägget aktiverat** är avmarkerat i inställningarna (Standardinställningen är markerad), kommer de skickade pyspark-interaktions resultaten att använda det gamla fönstret.
   >
   > ![pyspark interaktiv python-tillägg inaktiverat](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Skicka PySpark batch job

1. Öppna mappen **HDexample** som skapades [tidigare](#open-work-folder) om den stängdes.  

2. Skapa en ny fil **BatchFile.py** följer de [tidigare](#open-work-folder) stegen.

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

4. [Anslut](#connect-to-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

5. Högerklicka på skript redigeraren och välj **sedan Spark: PySpark batch**eller använd kortkommando **CTRL + ALT + H**. 

6. Välj ett kluster som ditt PySpark-jobb ska skickas till. 

   ![Skicka python-jobb resultat](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

När du har skickat ett python-jobb visas sändnings loggar i fönstret **utdata** i Visual Studio Code. URL: en för **Spark UI URL** och **garn-UI** visas också. Du kan öppna webb adressen i en webbläsare för att spåra jobbets status.

## <a name="apache-livy-configuration"></a>Apache livy-konfiguration

[Apache livy](https://livy.incubator.apache.org/) -konfigurationen stöds, den kan ställas in på **. VSCode\settings.json** i mappen arbets yta. För närvarande stöder livy-konfiguration endast Python-skript. Mer information finns i [LIVY viktigt](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Så här utlöser du livy-konfigurationen**

Metod 1  
1. I meny raden navigerar du till**Inställningar**för **fil** >  > inställningar.  
2. I text rutan **Sök inställningar** anger **du HDInsight-jobb sumission: Livy conf**.  
3. Välj **Redigera i Settings. JSON** för det relevanta Sök resultatet.

Metod 2   
Skicka in en fil, Lägg märke till att mappen. VSCode automatiskt läggs till i arbetsmappen. Du kan hitta livy-konfigurationen genom att klicka på **. vscode\settings.JSON**.

+ Projekt inställningarna:

    ![Livy-konfiguration](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>För Settings **driverMomory** och **executorMomry**anger du värdet med enhet, till exempel 1G eller 1 024. 

+ Livy-konfigurationer som stöds:   

    **PUBLICERA/batches**   
    Begärandetext

    | name | description | type | 
    | :- | :- | :- | 
    | file | Fil som innehåller det program som ska köras | sökväg (obligatoriskt) | 
    | proxyUser | Användare som ska personifieras när jobbet körs | sträng | 
    | className | Program java/Spark, huvud klass | sträng |
    | args | Kommando rads argument för programmet | lista över strängar | 
    | jars | jar v7 som ska användas i den här sessionen | Lista med sträng | 
    | pyFiles | Python-filer som ska användas i den här sessionen | Lista med sträng |
    | files | filer som ska användas i den här sessionen | Lista med sträng |
    | driverMemory | Mängd minne som ska användas för driv rutins processen | sträng |
    | driverCores | Antal kärnor som ska användas för driv rutins processen | int |
    | executorMemory | Mängden minne som ska användas per utförar-process | sträng |
    | executorCores | Antal kärnor som ska användas för varje utförar | int |
    | numExecutors | Antal körningar som ska startas för den här sessionen | int |
    | archives | Arkiv som ska användas i den här sessionen | Lista med sträng |
    | queue | Namnet på den garn kö som har skickats | sträng |
    | name | Namnet på den här sessionen | sträng |
    | medför | Konfigurations egenskaper för Spark | Karta över nyckel = val |

    Svars text   
    Det skapade batch-objektet.

    | name | description | type | 
    | :- | :- | :- | 
    | id | Sessions-ID | int | 
    | appId | Programmets ID för den här sessionen |  Sträng |
    | appInfo | Detaljerad programinfo | Karta över nyckel = val |
    | log | Logg raderna | lista över strängar |
    | state |   Batch-tillstånd | sträng |

>[!NOTE]
>Den tilldelade livy-konfigurationen visas i fönstret utdata vid sändning av skript.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrera med Azure HDInsight från Utforskaren

**Azure HDInsight** har lagts till i trädvyn. Du kan bläddra och hantera kluster (er) direkt via **Azure HDInsight**.

1. [Anslut](#connect-to-azure-account) till ditt Azure-konto eller länka ett kluster om du inte redan har gjort det.

2. Gå till **Visa** > **Utforskaren**från meny raden.

3. I den vänstra rutan expanderar du **Azure HDInsight**.  Tillgängliga prenumerationer och kluster (Spark, Hadoop och HBase stöds) visas i listan. 

   ![Azure HDInsight-prenumeration](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Expandera klustret om du vill visa Hive metadata-databas och tabell schema.

   ![Azure HDInsight-kluster](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Förhandsgranska Hive-tabell
Du kan förhandsgranska Hive-tabellen i dina kluster direkt via **Azure HDInsight** Explorer.
1. [Anslut](#connect-to-azure-account) till ditt Azure-konto om du inte redan har gjort det.

2. Klicka på **Azure** -ikonen från kolumnen längst till vänster.

3. I den vänstra rutan expanderar du **Azure HDInsight**. De tillgängliga prenumerationerna och klustren visas i listan.

4. Expandera klustret om du vill visa Hive metadata-databas och tabell schema.

5. Högerklicka på Hive-tabellen, t. ex. hivesampletable. Välj för **hands version**. 

   ![Spark & Hive för Visual Studio Code för hands version av Hive-tabell](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Fönstret för förhands **gransknings resultat** öppnas.

   ![Spark & Hive för resultat fönstret för Visual Studio Code Preview](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **Resultat** panel

   Du kan spara hela resultatet som en CSV-, JSON- eller Excel-fil till en lokal sökväg, eller markera flera rader.

- Panelen **meddelanden**
   1. När antalet rader i tabellen är större än 100 rader visar meddelandet: **De första 100 raderna visas för Hive-tabellen**.
   2. När antalet rader i tabellen är mindre än eller lika med 100 rader visar meddelandet: **60 rader visas för Hive-tabellen**.
   3. När det inte finns något innehåll i tabellen visar meddelandet: **0 rad visas för Hive-tabell**.

>[!NOTE]
>
>I Linux installerar du xclip för att aktivera kopiera tabell data.
>
>![Spark & Hive för Visual Studio Code i Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Ytterligare funktioner

Spark & Hive för Visual Studio Code stöder följande funktioner:

- **IntelliSense-komplettering**. Förslag på popup-fönster för nyckelord, metoder, variabler och så vidare. Olika ikoner representerar olika typer av objekt.

    ![Spark & Hive-verktyg för Visual Studio Codes IntelliSense-objekttyper](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense**-felmarkör. Språk tjänsten stryker under redigerings felen för Hive-skriptet.     
- **Egenskaper för syntax**. Språk tjänsten använder olika färger för att särskilja variabler, nyckelord, data typer, funktioner och så vidare. 

    ![Spark & Hive-verktyg för Visual Studio Code syntax](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Roll för endast läsare

Användare med endast kluster **läsaren**  **roll** kan inte längre skicka jobb till HDInsight-klustret eller Visa Hive-databasen. Kontakta kluster administratören för att uppgradera rollen till [ **HDInsight** - **klustrets** **operator** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) i [Azure Portal](https://ms.portal.azure.com/). Om du känner till Ambari-autentiseringsuppgifter kan du manuellt länka klustret genom att följa anvisningarna nedan.

### <a name="browse-hdinsight-cluster"></a>Bläddra i HDInsight-kluster  

När du klickar på Azure HDInsight Explorer för att expandera ett HDInsight-kluster uppmanas du att länka klustret om du är en roll som endast är läsare för klustret. Följ stegen nedan för att länka till klustret via Ambari-autentiseringsuppgifter. 

### <a name="submit-job-to-hdinsight-cluster"></a>Skicka jobb till HDInsight-kluster

När du skickar ett jobb till ett HDInsight-kluster uppmanas du att länka klustret om du är endast läsarens roll för klustret. Följ stegen nedan för att länka till klustret via Ambari-autentiseringsuppgifter. 

### <a name="link-to-cluster"></a>Länka till kluster

1.  Ange Ambari användar namn 
2.  Ange Ambari användar lösen ord.

   ![Spark & Hive-verktyg för Visual Studio Code användar namn](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Hive-verktyg för Visual Studio Code Password](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Du kan använda Spark/Hive: Lista kluster för att kontrol lera det länkade klustret.
>
>![Spark & Hive-verktyg för Visual Studio Code Reader](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>Bläddra i ett ADLS Gen2 konto

När du klickar på Azure HDInsight Explorer för att expandera ett ADLS Gen2-konto uppmanas du att ange lagrings **åtkomst nyckeln** om ditt Azure-konto inte har åtkomst till Gen2-lagringen. ADLS Gen2s kontot expanderas automatiskt när åtkomst nyckeln har verifierats. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Skicka jobb till HDInsight-kluster med ADLS Gen2

När du skickar jobb till ett HDInsight-kluster med ADLS Gen2 uppmanas du att ange lagrings **åtkomst nyckeln** om ditt Azure-konto saknar skriv åtkomst till Gen2-lagringen.  Jobbet skickas när åtkomst nyckeln har verifierats korrekt. 

![Spark & Hive-verktyg för Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Du kan hämta åtkomst nyckeln för lagrings kontot från Azure Portal. Mer information finns i [Visa och kopiera åtkomst nycklar](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Ta bort länk till kluster

1. Från meny raden navigerar du till **Visa** > **kommando palett...** och anger **sedan Spark/Hive: Ta bort länk till**ett kluster.  

2. Välj kluster att ta bort länk.  

3. Granska vyn **utdata** för verifiering.  

## <a name="sign-out"></a>Logga ut  

Från meny raden navigerar du till **Visa** > **kommando palett...** och anger **sedan Azure: Logga ut**.


## <a name="next-steps"></a>Nästa steg
En demonstrations video om hur man använder Spark & Hive för Visual Studio Code finns i [Spark & Hive för Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
