---
title: Azure HDInsight Tools - använder Visual Studio Code för Hive, LLAP eller PySpark
description: Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code för att skapa och skicka frågor och skript.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: aadfae9a7b74986fd0ac8857669dd3ccaf62af1f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166162"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Använd Azure HDInsight Tools för Visual Studio Code

Lär dig hur du använder Azure HDInsight Tools för Visual Studio Code för att skapa och skicka Apache Hive-batchjobb, interaktiva Hive-frågor och PySpark-skript för Apache Spark. Först kommer vi att beskriva hur du installerar HDInsight-verktygen i Visual Studio Code och sedan vi gå igenom hur du skickar in jobb för att Hive och Spark.  

Azure HDInsight-verktyg som kan installeras på plattformar som stöds av Visual Studio Code, inklusive Windows, Linux och macOS. Nedan hittar du de nödvändiga förutsättningarna för olika plattformar.


## <a name="prerequisites"></a>Nödvändiga komponenter

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

3. Namnge den nya filen med antingen den `.hql` (Hive-frågor) eller `.py` (Spark-skriptet) filnamnstillägg.  Det här exemplet används **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Ange Azure-miljön

1. [Ansluta](#connect-to-azure-account) till din Azure-konto eller länka ett kluster om du inte gjort det ännu.

2. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange **HDInsight: Ange Azure-miljön**.

3. Välj en miljö som din standardalternativet för inloggning.

4. Under tiden kan verktyget redan har sparats din inloggning standardposten i **. VSCode\settings.json**. Du kan också direkt uppdatera den i den här konfigurationsfilen. 

   ![Ange standard inloggningen post konfiguration](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Ansluta till Azure-konto

Innan du kan skicka skript till HDInsight-kluster från Visual Studio Code, måste du ansluta till ditt Azure-konto eller länka ett kluster (med hjälp av Ambari användarnamn/lösenord eller domänanslutna konto).  Utför följande steg för att ansluta till Azure:

1. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange **HDInsight: Logga in**.

    ![HDInsight Tools för Visual Studio Code-inloggning](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Följ instruktionerna i inloggningen i **utdata** fönstret.
    + För globala Azure-miljön, **HDInsight: Logga in** kommandot utlöser **logga in på Azure** åtgärd i HDInsight explorer och vice versa.

        ![Inloggningsinstruktioner för azure](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + För andra miljöer, följer du anvisningarna inloggningen.

        ![Inloggningsinstruktioner för andra miljöer](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   När du är ansluten, visas namnet på ditt Azure i statusfältet längst ned till vänster i Visual Studio Code-fönstret.  
  

## <a name="link-a-cluster"></a>Länka ett kluster

### <a name="link-azure-hdinsight"></a>Länk: Azure HDInsight

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



### <a name="link-generic-livy-endpoint"></a>Länk: Allmän Livy slutpunkt

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

3. Högerklicka på Skriptredigeraren och välj **HDInsight: Ange Standardkluster**.  

4. [Ansluta](#connect-to-azure-account) till din Azure-konto eller länka ett kluster om du inte gjort det ännu.

5. Välj ett kluster som standardklustret för den aktuella skriptfilen. Verktygen automatiskt uppdatera konfigurationsfilen **. VSCode\settings.json**. 

   ![Ställ in standardkonfiguration för kluster](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>Skicka interaktiva Hive-frågor, Hive kommandoskript

Med HDInsight Tools för Visual Studio Code, kan du skicka interaktiva Hive-frågor och Hive batch skript till HDInsight-kluster.

1. Öppna mappen **HDexample** skapade [tidigare](#open-hdinsight-work-folder) om stängd.  

2. Välj filen **HelloWorld.hql** skapade [tidigare](#open-hdinsight-work-folder) så öppnas i Skriptredigeraren.


3. Sedan kopierar du och klistrar in följande kod i din Hive-fil och sparar den.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Ansluta](#connect-to-azure-account) till din Azure-konto eller länka ett kluster om du inte gjort det ännu.

5. Högerklicka på Skriptredigeraren, Välj **HDInsight: Hive interaktiv** skicka frågan eller använder genvägen **Ctrl + Alt + I**.  Välj **HDInsight: Hive Batch** skicka skriptet eller använder genvägen **Ctrl + Alt + H**.  

6. Välj klustret om du inte har angett ett standardkluster. Med verktygen kan du dessutom skicka ett kodblock istället för hela skriptfilen med snabbmenyn. Efter en liten stund visas resultatet av frågan i en ny flik.

   ![Interaktiva Hive-resultat](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panelen **RESULTAT**: Du kan spara hela resultatet som en CSV-, JSON- eller Excel-fil till en lokal sökväg, eller markera flera rader.

    - Panelen **MEDDELANDEN**: När du väljer **Rad**nummer, leder det till den första raden i det aktuella skriptet.

## <a name="submit-interactive-pyspark-queries"></a>Köra en interaktiv PySpark-fråga

Du kan skicka interaktiv PySpark-frågor genom att följa stegen nedan:

1. Öppna mappen **HDexample** skapade [tidigare](#open-hdinsight-work-folder) om stängd.  

2. Skapa en ny fil **HelloWorld.py** följande den [tidigare](#open-hdinsight-work-folder) steg.

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

4. [Ansluta](#connect-to-azure-account) till din Azure-konto eller länka ett kluster om du inte gjort det ännu.

5. Välj all kod och högerklicka Skriptredigeraren, Välj **HDInsight: Interaktiv PySpark** skicka frågan eller använder genvägen **Ctrl + Alt + I**.

   ![interaktiv pyspark högerklickar du på](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Välj klustret om du inte har angett ett standardkluster. Efter en liten stund den **Python interaktiva resulterar** visas i en ny flik. Med verktygen kan du dessutom skicka ett kodblock istället för hela skriptfilen med snabbmenyn. 

   ![pyspark interaktiva python interaktiva fönstret](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Ange **”%% info”** , och tryck sedan på **SKIFT + RETUR** att visa jobbinformation. (Valfritt)

   ![Visa jobbinformation](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Verktyget stöder också den **Spark SQL** fråga.

   ![Interaktiv Pyspark visa resultat](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Status för ansökan visas till vänster i statusfältet längst ned när du kör frågor. Inte skicka andra frågor när statusen är **PySpark-Kernel (upptagen)** .  

   > [!NOTE] 
   >
   > När **Python tillägget aktiverat** är avmarkerad i inställningarna för (standardinställningen är markerad) har skickats pyspark interaktion resultatet kommer att använda fönstret gamla.
   >
   > ![pyspark interaktiva python-tillägg som inaktiverad](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>Skicka PySpark batch-jobb

1. Öppna mappen **HDexample** skapade [tidigare](#open-hdinsight-work-folder) om stängd.  

2. Skapa en ny fil **BatchFile.py** följande den [tidigare](#open-hdinsight-work-folder) steg.

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

4. [Ansluta](#connect-to-azure-account) till din Azure-konto eller länka ett kluster om du inte gjort det ännu.

5. Högerklicka på skriptredigeraren och välj sedan **HDInsight: PySpark Batch**, eller använder genväg **Ctrl + Alt + H**. 

6. Välja ett kluster som du vill skicka PySpark-jobb. 

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

    | name | description | type | 
    | :- | :- | :- | 
    | file | Fil som innehåller programmet ska köras | sökvägen (krävs) | 
    | proxyUser | Användaren att personifiera när jobbet körs | string | 
    | className | Programmet Java/Spark-huvudklass | string |
    | args | Kommandoradsargument för programmet | lista med strängar | 
    | jars | JAR-filer som ska användas i den här sessionen | Lista över strängar | 
    | pyFiles | Python-filer som ska användas i den här sessionen | Lista över strängar |
    | files | filer som ska användas i den här sessionen | Lista över strängar |
    | driverMemory | Mängden minne som ska användas för processen för drivrutinen | string |
    | driverCores | Antalet kärnor som ska användas för processen för drivrutinen | int |
    | executorMemory | Mängden minne för att använda per körningsprocess | string |
    | executorCores | Antalet kärnor som ska användas för varje executor | int |
    | numExecutors | Antal executors att starta för den här sessionen | int |
    | archives | Arkiv som ska användas i den här sessionen | Lista över strängar |
    | queue | Namnet på YARN-kö som har skickats | string |
    | name | Namnet på den här sessionen | string |
    | conf | Egenskaper för Spark-konfiguration | Karta över nyckel = värde |

    Svarstext   
    Det Batch-objektet.

    | name | description | type | 
    | :- | :- | :- | 
    | id | Sessions-id | int | 
    | appId | Program-id för den här sessionen |  String |
    | appInfo | Detaljerad programinformation | Karta över nyckel = värde |
    | log | Log-rader | lista med strängar |
    | state |   Batch-tillstånd | string |

>[!NOTE]
>Tilldelade livy konfig visas i utdatafönstret när skicka skript.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrera med Azure HDInsight i Utforskaren

**Azure HDInsight** läggs till i Explorer-vy. Du kan söka efter och hantera du kluster direkt via **Azure HDInsight**.

1. [Ansluta](#connect-to-azure-account) till din Azure-konto eller länka ett kluster om du inte gjort det ännu.

2. Från menyraden navigerar du till **visa** > **Explorer**.

3. I den vänstra rutan expanderar **AZURE HDINSIGHT**.  De tillgängliga prenumerationerna och kluster (Spark, Hadoop och HBase stöds) visas. 

   ![Azure HDInsight-prenumeration](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Expandera klustret om du vill visa hive-metadata-databasen och tabellschemat.

   ![Azure HDInsight-kluster](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Förhandsversion av Hive-tabell
Du kan förhandsgranska Hive-tabell i ditt kluster som är direkt via **Azure HDInsight** explorer.
1. [Ansluta](#connect-to-azure-account) på Azure-kontot om du inte gjort det ännu.

2. Klicka på **Azure** ikon i kolumnen längst till vänster.

3. I den vänstra rutan expanderar du AZURE HDINSIGHT. De tillgängliga prenumerationerna och kluster visas.

4. Expandera klustret om du vill visa hive-metadata-databasen och tabellschemat.

5. Högerklicka på Hive-tabell, t.ex hivesampletable. Välj **förhandsversion**. 

   ![HDInsight för vscode förhandsversion hive-tabell](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Den **Förhansgranskningsresultat** fönstret öppnas.

   ![HDInsight för vscode förhandsgranskningsfönstret resultat](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **RESULTATEN** panelen

   Du kan spara hela resultatet som en CSV-, JSON- eller Excel-fil till en lokal sökväg, eller markera flera rader.

- **MEDDELANDEN** panelen
   1. Meddelandet visas när antalet rader i tabellen är större än 100 rader: **De första 100 raderna visas för Hive-tabell**.
   2. När antalet rader i tabellen är mindre än eller lika med 100 rader, visas meddelandet: **60 rader visas för Hive-tabell**.
   3. När det finns inget innehåll i tabellen, visas meddelandet: **0 raden visas för Hive-tabell**.

>[!NOTE]
>
>I Linux, installerar du xclip om du vill aktivera kopiera tabelldata.
>
>![HDInsight för vscode i linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>Ytterligare funktioner

HDInsight för Visual Studio Code har stöd för följande funktioner:

- **IntelliSense automatisk komplettering**. Förslag för nyckelord, metoder, variabler och så vidare. Olika ikoner representerar olika typer av objekt.

    ![HDInsight Tools för Visual Studio Code, IntelliSense objekttyper](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense fel markör**. Tjänsten språk understryker redigering fel för Hive-skriptet.     
- **Syntax höjdpunkter**. Språk-tjänsten använder olika färger för att skilja variabler, nyckelord, datatyp, funktioner och så vidare. 

    ![HDInsight Tools för Visual Studio Code syntax höjdpunkter](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Läsarroll för endast

Användare med klustret **läsare** **endast** **rollen** kan inte längre skicka jobb till HDInsight-klustret och inte heller visa Hive-databasen. Du behöver kontakta Klusteradministratören för att uppgradera din roll till [ **HDInsight** **kluster** **operatorn** ](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) i den [ Azure-portalen](https://ms.portal.azure.com/). Om du vet Ambari autentiseringsuppgifter, kan du manuellt koppla klustret följa anvisningarna nedan.

### <a name="browse-hdinsight-cluster"></a>Bläddra HDInsight-kluster  

När du klickar på Azure HDInsight-explorer för att expandera ett HDInsight-kluster, uppmanas du att länka klustret om du endast läsarroll för klustret. Följ stegen nedan för att länka till klustret via Ambari-autentiseringsuppgifter. 

### <a name="submit-job-to-hdinsight-cluster"></a>Skicka jobb till HDInsight-kluster

När du skickar jobbet till ett HDInsight-kluster, uppmanas du att länka klustret om du endast läsarroll för klustret. Följ stegen nedan för att länka till klustret via Ambari-autentiseringsuppgifter. 

### <a name="link-to-cluster"></a>Länka till kluster

1.  Ange Ambari-användarnamn 
2.  Ange användare för Ambari lösenord.

   ![HDInsight Tools för Visual Studio Code-användarnamn](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![HDInsight Tools för Visual Studio Code lösenord](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>Du kan använda HDInsight: Lista över kluster för att kontrollera länkade klustret.
>
>![HDInsight Tools för Visual Studio Code läsare länkad](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-reader-linked.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>Bläddra ett Gen2 ADLS-konto

När du klickar på Azure HDInsight-explorer för att expandera ett Gen2 ADLS-konto, uppmanas du att ange lagringen **åtkomstnyckel** om ditt Azure-konto har ingen åtkomst till Gen2-lagring. Gen2 ADLS-kontot ska automatisk expanderas när åtkomstnyckeln är har verifierats. 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>Skicka jobb till HDInsight-kluster med ADLS Gen2

När du skickar jobbet till ett HDInsight-kluster med ADLS Gen2 uppmanas du att ange lagringen **åtkomstnyckel** om ditt Azure-konto har inte skrivbehörighet till Gen2-lagring.  Jobbet skickas har när åtkomstnyckeln är har verifierats. 

![HDInsight Tools för Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>Du kan hämta åtkomstnyckeln för lagringskontot från Azure-portalen. Mer information finns i [visa och kopiera åtkomstnycklar](https://docs.microsoft.com/azure/storage/common/storage-account-manage#view-and-copy-access-keys).

## <a name="unlink-cluster"></a>Ta bort länken till kluster

1. Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange sedan **HDInsight: Ta bort länken till ett kluster**.  

2. Markera klustret att Avlänka.  

3. Granska **utdata** vy för verifiering.  

## <a name="sign-out"></a>Logga ut  

Från menyraden navigerar du till **visa** > **Kommandopaletten...** , och ange sedan **HDInsight: Logga ut**.  Det är ett popup-fönster i det nedre högra hörnet om **utloggning har!** .


## <a name="next-steps"></a>Nästa steg
Se en demonstration video för att använda HDInsight för Visual Studio Code [HDInsight för Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
