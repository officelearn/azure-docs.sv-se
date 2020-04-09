---
title: Azure HDInsight för Visual Studio-kod
description: Lär dig hur du använder Spark & Hive Tools (Azure HDInsight) för Visual Studio-kod. Använd verktygen för att skapa och skicka frågor och skript.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: de433d85c2f04a7140fbcb918730218ac3a05e54
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878637"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Använda Spark & Hive-verktyg för Visual Studio-kod

Läs om hur du använder Apache Spark & Hive-verktyg för Visual Studio-kod. Använd verktygen för att skapa och skicka apache hive-batchjobb, interaktiva Hive-frågor och PySpark-skript för Apache Spark. Först ska vi beskriva hur du installerar Spark & Hive Tools i Visual Studio Code. Sedan går vi igenom hur du skickar jobb till Spark & Hive Tools.  

Spark & Hive Tools kan installeras på plattformar som stöds av Visual Studio Code. Observera följande förutsättningar för olika plattformar.

## <a name="prerequisites"></a>Krav

Följande objekt krävs för att slutföra stegen i den här artikeln:

- Ett Azure HDInsight-kluster. Om du vill skapa ett kluster finns [i Komma igång med HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Eller använd ett Spark- och Hive-kluster som stöder en Apache Livy-slutpunkt.
- [Visual Studio-kod](https://code.visualstudio.com/).
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono krävs endast för Linux och macOS.
- [En pyspark interaktiv miljö för Visual Studio Code](set-up-pyspark-interactive-environment.md).
- En lokal katalog. Den här artikeln använder **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Installera Spark & Hive-verktyg

När du har uppfyllt förutsättningarna kan du installera Spark & Hive Tools för Visual Studio-kod genom att följa följande steg:

1. Öppna Visual Studio Code.

2. Gå till **Visa** > **tillägg**på menyraden .

3. Skriv Spark & **Hive**i sökrutan .

4. Välj **Spark & Hive-verktyg** från sökresultaten och välj sedan **Installera:**

   ![Spark & Hive för Visual Studio Code Python-installation](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Välj **Ladda om** vid behov.

## <a name="open-a-work-folder"></a>Öppna en arbetsmapp

Så här öppnar du en arbetsmapp och skapar en fil i Visual Studio-kod:

1. Navigera till Öppna mapp **på** > menyraden...**Open Folder...**  >  **C:\HD\HDexample**och välj sedan knappen **Välj mapp.** Mappen visas i **Utforskarvyn** till vänster.

2. I **Explorer** explorer-vyn väljer du mappen **HDexample** och väljer sedan ikonen **Ny fil** bredvid arbetsmappen:

   ![Visual Studio-kod ny fil ikon](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Ge den nya filen `.hql` ett namn med filtillägget (Hive-frågor) eller `.py` (Spark script). I det här exemplet används **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Ange Azure-miljö

För en nationell molnanvändare följer du dessa steg för att ange Azure-miljön först och använder sedan **azure: sign in-kommandot** för att logga in på Azure:

1. Navigera till**Inställningar**för > **filinställningar** > . **File**
2. Sök på följande sträng: **Azure: Cloud**.
3. Välj det nationella molnet i listan:

   ![Ange standardkonfiguration för inloggningsinmatning](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Ansluta till ett Azure-konto

Innan du kan skicka skript till dina kluster från Visual Studio-kod måste du antingen ansluta till ditt Azure-konto eller länka ett kluster. Använd Apache Ambaris användarnamn och lösenordsuppgifter eller ett domänanslutet konto. Följ dessa steg för att ansluta till Azure:

1. På menyraden navigerar du till **Visa** > **kommandopalett...** och anger **Azure: Logga in:**

    ![Spark & Hive-verktyg för Visual Studio-kodinloggning](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Följ inloggningsinstruktionerna för att logga in på Azure. När du är ansluten visas ditt Azure-kontonamn i statusfältet längst ned i Visual Studio-kodfönstret.  

## <a name="link-a-cluster"></a>Länka ett kluster

### <a name="link-azure-hdinsight"></a>Länk: Azure HDInsight

Du kan länka ett normalt kluster med ett [Apache Ambari-hanterat](https://ambari.apache.org/)användarnamn eller länka ett Enterprise Security Pack-säkert Hadoop-kluster med hjälp av ett domänanvändarnamn (till exempel: `user1@contoso.com`).

1. Från menyraden navigerar du till **Visa** > **kommandopalett...** och anger **Spark / Hive: Länka ett kluster**.

   ![Kommandopalettlänkskluster, kommando](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Välj länkad klustertyp **Azure HDInsight**.

3. Ange URL:en för HDInsight-kluster.

4. Ange ditt användarnamn på Ambari; standard är **admin**.

5. Ange ditt Ambari-lösenord.

6. Välj klustertyp.

7. Ange visningsnamnet för klustret (valfritt).

8. Granska **UTDATA-vyn** för verifiering.

   > [!NOTE]  
   > Det länkade användarnamnet och lösenordet används om klustret både loggat in på Azure-prenumerationen och länkade ett kluster.  

### <a name="link-generic-livy-endpoint"></a>Länk: Generic Livy slutpunkt

1. Från menyraden navigerar du till **Visa** > **kommandopalett...** och anger **Spark / Hive: Länka ett kluster**.

2. Välj länkad klustertyp **Generic Livy Endpoint**.

3. Ange den allmänna Livy-slutpunkten. Till exempel:\:http //10.172.41.42:18080.

4. Välj auktoriseringstyp **Basic** eller **Ingen**.  Om du väljer **Grundläggande:**  
    &emsp;A. Ange ditt användarnamn på Ambari; standard är **admin**.  
    &emsp;B. Ange ditt Ambari-lösenord.

5. Granska **UTDATA-vyn** för verifiering.

## <a name="list-clusters"></a>Lista kluster

1. Från menyraden navigerar du till **Visa** > **kommandopalett...** och anger **Spark / Hive: List Cluster**.

2. Välj den prenumeration du vill använda.

3. Granska **utdatavyn.** I den här vyn visas ditt länkade kluster (eller kluster) och alla kluster under din Azure-prenumeration:

    ![Ange en standardklusterkonfiguration](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Ange standardklustret

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Välj filen **HelloWorld.hql** som skapades [tidigare](#open-a-work-folder). Den öppnas i manusredigeraren.

3. Högerklicka på skriptredigeraren och välj sedan **Spark / Hive: Ange standardkluster**.  

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du ännu inte har gjort det.

5. Välj ett kluster som standardkluster för den aktuella skriptfilen. Verktygen uppdaterar automatiskt **. VSCode\settings.json** konfigurationsfil:

   ![Ange standardklusterkonfiguration](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Skicka in interaktiva Hive-frågor och Hive-batchskript

Med Spark & Hive Tools for Visual Studio Code kan du skicka in interaktiva Hive-frågor och Hive-batchskript till dina kluster.

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Välj filen **HelloWorld.hql** som skapades [tidigare](#open-a-work-folder). Den öppnas i manusredigeraren.

3. Kopiera och klistra in följande kod i Hive-filen och spara den sedan:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du ännu inte har gjort det.

5. Högerklicka på skriptredigeraren och välj **Hive: Interaktivt** för att skicka frågan, eller använd kortkommandot Ctrl+Alt+I.  Välj **Hive: Batch** om du vill skicka skriptet eller använd kortkommandot Ctrl+Alt+H.  

6. Om du inte har angett något standardkluster väljer du ett kluster. Med verktygen kan du också skicka ett kodblock i stället för hela skriptfilen med hjälp av snabbmenyn. Efter en stund visas frågeresultaten på en ny flik:

   ![Interaktivt Apache Hive-frågeresultat](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTATpanel:** Du kan spara hela resultatet som en CSV-, JSON- eller Excel-fil på en lokal sökväg eller bara markera flera rader.

    - **MEDDELANDEpanelen:** När du väljer ett **radnummer** hoppar den till den första raden i det skript som körs.

## <a name="submit-interactive-pyspark-queries"></a>Skicka in interaktiva PySpark-frågor

Så här skickar du interaktiva PySpark-frågor:

1. Öppna mappen **HDexample** som diskuterades [tidigare](#open-a-work-folder), om den är stängd.  

2. Skapa en ny **HelloWorld.py-fil** i de [tidigare](#open-a-work-folder) stegen.

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

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du ännu inte har gjort det.

5. Markera all kod, högerklicka på skriptredigeraren och välj **Spark: PySpark Interactive** för att skicka frågan. Du kan också använda genvägen Ctrl+Alt+I.

   ![pyspark interaktiv snabbmeny](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Markera klustret om du inte har angett ett standardkluster. Efter en stund visas **Python Interactive-resultaten** på en ny flik. Med verktygen kan du också skicka ett kodblock i stället för hela skriptfilen med hjälp av snabbmenyn:

   ![pyspark interaktiv python interaktivt fönster](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. Ange **%%info**och tryck sedan på Skift+Retur för att visa jobbinformationen (valfritt):

   ![pyspark interaktiv vy jobbinformation](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. Verktyget stöder också **Spark SQL-frågan:**

   ![Pyspark Interaktivt vyresultat](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   Inlämningsstatusen visas till vänster om det lägre statusfältet när du kör frågor. Skicka inte in andra frågor när statusen är **PySpark Kernel (upptagen).**  

   > [!NOTE]
   >
   > När **Python-tillägget Aktiverat** rensas i inställningarna (det är markerat som standard) använder de inskickade pyspark-interaktionsresultaten det gamla fönstret:
   >
   > ![pyspark interaktiv python förlängning inaktiverad](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Skicka batch-jobb i PySpark

1. Öppna mappen **HDexample** som du diskuterade [tidigare](#open-a-work-folder), om den är stängd.  

2. Skapa en ny **BatchFile.py-fil** genom att följa de [tidigare](#open-a-work-folder) stegen.

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

4. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto eller länka ett kluster om du ännu inte har gjort det.

5. Högerklicka på skriptredigeraren och välj sedan **Spark: PySpark Batch**eller använd kortkommandot Ctrl+Alt+H.

6. Välj ett kluster som du vill skicka in PySpark-jobbet till:

   ![Skicka python-jobbresultatutdata](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

När du har skickat in ett Python-jobb visas inlämningsloggar i **utdatafönstret** i Visual Studio-kod. Url:en för Spark UI och garngränssnittets URL visas också. Du kan öppna WEBBADRESSEN i en webbläsare för att spåra jobbstatusen.

## <a name="apache-livy-configuration"></a>Apache Livy konfiguration

[Apache Livy-konfiguration](https://livy.incubator.apache.org/) stöds. Du kan konfigurera den i **. VSCode\settings.json-filen** i arbetsytemappen. För närvarande stöder Livy-konfigurationen endast Python-skript. Mer information finns i [Livy README](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Så här utlöser du Livy-konfiguration**

Metod 1  
1. Gå till Inställningar för > **filinställningar** > på menyraden . **File****Settings**
2. I rutan **Sökinställningar** anger du **HDInsight Job Submission: Livy Conf**.  
3. Välj **Redigera i settings.json** för det aktuella sökresultatet.

Metod 2 Skicka en fil `.vscode` och observera att mappen läggs automatiskt till i arbetsmappen. Du kan se Livy-konfigurationen genom att välja **.vscode\settings.json**.

+ Projektinställningarna:

    ![HDInsight Apache Livy-konfiguration](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >För **inställningarna för driverMemory** och **executorMemory** ställer du in värdet och enheten. Till exempel: 1g eller 1024m.

+ Livy-konfigurationer som stöds:

    **POST /batchar** Begäran kropp

    | namn | description | typ |
    | --- | --- | --- |
    | file | Fil som innehåller programmet som ska köras | Sökväg (obligatoriskt) |
    | proxyAnvändare | Användare att personifiera när du kör jobbet | Sträng |
    | Classname | Huvudklassen För Användning Java/Spark | Sträng |
    | args | Kommandoradsargument för programmet | Lista över strängar |
    | Burkar | Burkar som ska användas i den här sessionen | Lista över strängar | 
    | pyFiles (b)) | Python-filer som ska användas i den här sessionen | Lista över strängar |
    | filer | Filer som ska användas i den här sessionen | Lista över strängar |
    | driverMemory | Mängden minne som ska användas för drivrutinsprocessen | Sträng |
    | driverCores | Antal kärnor som ska användas för drivrutinsprocessen | Int |
    | testamentsexekutorMemory | Mängden minne som ska användas per executor-process | Sträng |
    | executorCores | Antal kärnor som ska användas för varje utförare | Int |
    | numExecutors | Antal utförare som ska startas för den här sessionen | Int |
    | Arkiv | Arkiv som ska användas i denna session | Lista över strängar |
    | kö | Namnet på den YARN-kö som ska skickas till| Sträng |
    | namn | Namnet på den här sessionen | Sträng |
    | Conf | Egenskaper för sparkkonfiguration | Karta över key=val |

    Svarstext Det skapade batch-objektet.

    | namn | description | typ |
    | --- | ---| --- |
    | ID | Sessions-ID | Int |
    | appId | Program-ID för den här sessionen | Sträng |
    | appInfo (på) | Detaljerad information om ansökan | Karta över key=val |
    | Logga in | Logga rader | Lista över strängar |
    | state |Batchtillstånd | Sträng |

    > [!NOTE]
    > Den tilldelade Livy-config visas i utdatafönstret när du skickar skriptet.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integrera med Azure HDInsight från Explorer

Du kan förhandsgranska Hive-tabellen i dina kluster direkt via Utforskaren för **Azure HDInsight:**

1. [Anslut](#connect-to-an-azure-account) till ditt Azure-konto om du ännu inte har gjort det.

2. Välj **Azure-ikonen** från kolumnen längst till vänster.

3. Expandera **AZURE: HDINSIGHT**från den vänstra rutan . Tillgängliga prenumerationer och kluster visas.

4. Expandera klustret för att visa Hive-metadatadatabasen och tabellschemat.

5. Högerklicka på tabellen Hive. Till exempel: **hivesampletable**. Välj **Förhandsgranska**.

   ![Spark & Hive för visual studio kod förhandsgranskningsdatafilen](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Fönstret **Förhandsgranska resultat** öppnas:

   ![Fönstret & Hive för visual studiokod förhandsgranskningsfönster](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- panelen RESULTAT

   Du kan spara hela resultatet som en CSV-, JSON- eller Excel-fil på en lokal sökväg, eller bara markera flera rader.

- Panelen MEDDELANDEN
   1. När antalet rader i tabellen är större än 100 visas följande meddelande: "De första 100 raderna visas för Hive-tabellen."
   2. När antalet rader i tabellen är mindre än eller lika med 100 visas följande meddelande: "60 rader visas för Hive-tabellen."
   3. När det inte finns något innehåll i tabellen visas`0 rows are displayed for Hive table.`följande meddelande: "

        >[!NOTE]
        >
        >Installera xclip i Linux för att aktivera data med kopieringstabeller.
        >
        >![Spark & Hive för Visual Studio-kod i Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Ytterligare funktioner

Spark & Hive för Visual Studio Code stöder också följande funktioner:

- **IntelliSense autocomplete**. Förslag visas för nyckelord, metoder, variabler och andra programmeringselement. Olika ikoner representerar olika typer av objekt:

    ![Spark & Hive-verktyg för Visual Studio-kod IntelliSense-objekt](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense felmarkör**. Språktjänsten understryker redigeringsfel i Hive-skriptet.     
- **Syntaxhöjdpunkter**. Språktjänsten använder olika färger för att skilja från variabler, nyckelord, datatyp, funktioner och andra programmeringselement:

    ![Markera & i & Hive-verktyg för Visual Studio-kod](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Roll endast för läsare

Användare som har tilldelats rollen endast för läsaren för klustret kan inte skicka jobb till HDInsight-klustret eller visa Hive-databasen. Kontakta klusteradministratören för att uppgradera din roll till [**HDInsight Cluster Operator**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) i [Azure-portalen](https://ms.portal.azure.com/). Om du har giltiga Ambari-autentiseringsuppgifter kan du länka klustret manuellt med hjälp av följande vägledning.

### <a name="browse-the-hdinsight-cluster"></a>Bläddra i HDInsight-klustret  

När du väljer Azure HDInsight explorer för att expandera ett HDInsight-kluster uppmanas du att länka klustret om du har rollen endast för läsaren för klustret. Använd följande metod för att länka till klustret med hjälp av Ambari-autentiseringsuppgifterna.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Skicka jobbet till HDInsight-klustret

När du skickar jobb till ett HDInsight-kluster uppmanas du att länka klustret om du är med i rollen endast för läsaren för klustret. Följ följande steg för att länka till klustret med hjälp av Ambari-autentiseringsuppgifter.

### <a name="link-to-the-cluster"></a>Länk till klustret

1. Ange ett giltigt användarnamn på Ambari.
2. Ange ett giltigt lösenord.

   ![Spark & Hive-verktyg för användarnamn för Visual Studio-kod](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Hive-verktyg för Visual Studio-kodlösenord](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Du kan `Spark / Hive: List Cluster` använda för att kontrollera det länkade klustret:
  >
  >![Spark & Hive-verktyg för Visual Studio-kodläsare Länkad](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Bläddra bland ett Data Lake Storage Gen2-konto

Välj Azure HDInsight explorer för att expandera ett Data Lake Storage Gen2-konto. Du uppmanas att ange lagringsåtkomstnyckeln om ditt Azure-konto inte har åtkomst till Gen2-lagring. När åtkomstnyckeln har validerats expanderas Data Lake Storage Gen2-kontot automatiskt.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Skicka jobb till ett HDInsight-kluster med Gen2 för lagring av datasjö2

Skicka ett jobb till ett HDInsight-kluster med Data Lake Storage Gen2. Du uppmanas att ange lagringsåtkomstnyckeln om ditt Azure-konto inte har någon skrivåtkomst till Gen2-lagring. När åtkomstnyckeln har validerats skickas jobbet.

![Spark & Hive-verktyg för Visual Studio-kodåtkomst](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Du kan hämta åtkomstnyckeln för lagringskontot från Azure-portalen. Mer information finns i [Hantera åtkomstnycklar för lagringskonto](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Ta bort länken till klustret

1. Gå till **Visa** > **kommandopalett**på menyraden och ange sedan **Spark/Hive: Ta bort länken till ett kluster**.  

2. Välj ett kluster som du vill ta bort länken.  

3. Se **utdatavyn** för verifiering.  

## <a name="sign-out"></a>Logga ut  

Gå till **Visa** > **kommandopalett**på menyraden och ange sedan **Azure: Logga ut**.

## <a name="next-steps"></a>Nästa steg

En video som visas med Spark & Hive för Visual Studio-kod finns i [Spark & Hive för Visual Studio-kod](https://go.microsoft.com/fwlink/?linkid=858706).
