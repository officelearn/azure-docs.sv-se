---
title: "Kör ett Hadoop-jobb med hjälp av Azure Cosmos DB och HDInsight | Microsoft Docs"
description: "Lär dig hur du kör ett enkelt Hive, Pig och MapReduce-jobb med Azure Cosmos DB och Azure HDInsight."
services: cosmos-db
author: dennyglee
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 06f0ea9d-07cb-4593-a9c5-ab912b62ac42
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/08/2017
ms.author: denlee
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b72bdc546c824515867daa062c4a94f7326d7fb
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="Azure Cosmos DB-HDInsight"></a>Kör ett Apache Hive, Pig eller Hadoop-jobb med hjälp av Azure Cosmos DB och HDInsight
Den här kursen visar hur du kör [Apache Hive][apache-hive], [Apache Pig][apache-pig], och [Apache Hadoop] [ apache-hadoop] MapReduce-jobb på Azure HDInsight med Cosmos DB Hadoop-anslutningen. Cosmos DB Hadoop-anslutningen kan Cosmos-Databsen ska fungera som både käll- och mottagarnoderna för Hive, Pig och MapReduce-jobb. Den här kursen använder Cosmos DB som både källa och mål för Hadoop-jobb.

När du har slutfört den här självstudiekursen kommer du att kunna besvara följande frågor:

* Hur jag för att läsa in data från Cosmos-databasen med ett Hive, Pig eller MapReduce-jobb?
* Hur lagrar data i Cosmos-databasen med ett Hive, Pig eller MapReduce-jobb?

Vi rekommenderar att komma igång med att titta på nedanstående video, där vi kör via ett Hive-jobb med hjälp av Cosmos-DB och HDInsight.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-Azure-DocumentDB-Hadoop-Connector-with-Azure-HDInsight/player]
>
>

Återvänd sedan till den här artikeln, där du får fullständig information om hur du kan köra analytics-jobb på Cosmos-DB-data.

> [!TIP]
> Den här kursen förutsätter att du har tidigare erfarenhet av Apache Hadoop, Hive och Pig. Om du har använt Apache Hadoop Hive och Pig, rekommenderar vi att besöka den [Apache Hadoop-dokumentation][apache-hadoop-doc]. Den här kursen förutsätter också att du har tidigare erfarenhet av Cosmos-DB och har en Cosmos-DB-konto. Om du är nybörjare på Cosmos DB eller du har inte ett Cosmos-DB-konto kan du kolla vår [komma igång] [ getting-started] sidan.
>
>

Inte har tid att slutföra kursen, och bara vill hämta fullständigt exempelprogram PowerShell-skript för Hive, Pig och MapReduce? Inga problem, hämta dem [här][hdinsight-samples]. Hämtningen innehåller också hql, pig och java-filer för exemplen.

## <a name="NewestVersion"></a>Senaste versionen
<table border='1'>
    <tr><th>Hadoop Connector-Version</th>
        <td>1.2.0</td></tr>
    <tr><th>Skript-Uri</th>
        <td>https://portalcontent.BLOB.Core.Windows.NET/scriptaction/documentdb-hadoop-Installer-v04.ps1</td></tr>
    <tr><th>Ändrad den</th>
        <td>04/26/2016</td></tr>
    <tr><th>Stöds HDInsight-versioner</th>
        <td>3.1, 3.2</td></tr>
    <tr><th>Ändringslogg</th>
        <td>Uppdatera Azure Cosmos DB Java SDK för att 1.6.0</br>
            Tillagt stöd för partitionerade samlingar som både källa och mottagare</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Förhandskrav
Se till att du har följande innan du följer anvisningarna i den här självstudiekursen:

* Ett Cosmos-DB-konto, en databas och en samling med dokument i. Mer information finns i [komma igång med Cosmos DB][getting-started]. Importera exempeldata Cosmos-DB-konto med den [Cosmos-DB-Importverktyget][import-data].
* Genomflöde. Läser och skriver från HDInsight räknas mot dina angivna frågeenheter för samlingar.
* Kapacitet för ytterligare en lagrad procedur inom varje utdata samling. Lagrade procedurer används för att överföra resulterande dokument.
* Kapacitet för dokumenten från Hive, Pig eller MapReduce-jobb.
* [*Valfritt*] kapacitet för en ytterligare samling.

> [!WARNING]
> För att undvika att skapa en ny samling under någon av projekt du antingen skriva ut resultatet STDOUT, spara utdata till WASB-behållare eller ange en redan befintlig samling. För att ange en befintlig samling nya dokument kommer att skapas i samlingen och redan befintliga dokument påverkas endast om det finns en konflikt i *ID*. **Anslutningen automatiskt ska skriva över befintliga dokument med id-konflikter**. Du kan stänga av den här funktionen genom att ange alternativet upsert till false. Om upsert är false och en konflikt uppstår, Hadoop-jobb misslyckas; ett id konflikt fel rapporteras.
>
>

## <a name="ProvisionHDInsight"></a>Steg 1: Skapa ett nytt HDInsight-kluster
Den här kursen använder skriptåtgärd från Azure Portal för att anpassa ditt HDInsight-kluster. I den här självstudiekursen kommer använder vi Azure Portal för att skapa ditt HDInsight-kluster. Anvisningar för hur du använder PowerShell-cmdlets eller HDInsight .NET SDK, ta en titt på [anpassa HDInsight-kluster med skriptåtgärder] [ hdinsight-custom-provision] artikel.

1. Logga in på den [Azure-portalen][azure-portal].
2. Klicka på **+ ny** överst i navigeringen till vänster, söka efter **HDInsight** i det övre sökfältet på det nya bladet.
3. **HDInsight** publiceras av **Microsoft** visas överst i resultaten. Klicka på den och klicka sedan på **skapa**.
4. Skapa bladet på nytt HDInsight-kluster, ange din **klusternamnet** och välj den **prenumeration** du vill etablera den här resursen under.

    <table border='1'>
        <tr><td>Klusternamn</td><td>Namnet på klustret.<br/>
DNS-namn måste börja och sluta med ett alfanumeriska tecken och får innehålla tankstreck.<br/>
Fältet måste vara en sträng mellan 3 och 63 tecken.</td></tr>
        <tr><td>Prenumerationsnamn</td>
            <td>Om du har mer än en Azure-prenumeration, Välj den prenumeration som är värd för ditt HDInsight-kluster. </td></tr>
    </table>
5.Klicka på **Välj typ av kluster** och ange följande egenskaper för det angivna värdet.

    <table border='1'>
        <tr><td>Klustertyp</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Kluster-nivå</td><td><strong>Standard</strong></td></tr>
        <tr><td>Operativsystem</td><td><strong>Windows</strong></td></tr>
        <tr><td>Version</td><td>senaste versionen</td></tr>
    </table>

    Klicka på **Välj**.

    ![Ange Hadoop HDInsight inledande klusterinformation][image-customprovision-page1]
6. Klicka på **autentiseringsuppgifter** att ange dina autentiseringsuppgifter för inloggning och fjärråtkomst. Välj din **kluster inloggning användarnamn** och **kluster inloggningslösenordet**.

    Om du vill till fjärranslutna i klustret, väljer *Ja* längst ned på bladet och ange ett användarnamn och lösenord.
7. Klicka på **datakällan** att ange den primära platsen för dataåtkomst. Välj den **urvalsmetod** och ange ett redan befintligt lagringskonto eller skapa en ny.
8. I bladet samma ange en **standard behållaren** och en **plats**. Klicka på **Välj**.

   > [!NOTE]
   > Välj en plats i närheten av din Cosmos DB konto region för bättre prestanda
   >
   >
9. Klicka på **priser** att välja antal och typer av noder. Du kan hålla standardkonfigurationen och skala antalet arbetarnoder vid ett senare tillfälle.
10. Klicka på **valfri konfiguration**, sedan **skript åtgärder** i bladet valfri konfiguration.

     Ange följande information för att anpassa ditt HDInsight-kluster i skriptåtgärder.

     <table border='1'>
         <tr><th>Egenskap</th><th>Värde</th></tr>
         <tr><td>Namn</td>
             <td>Ange ett namn för skriptåtgärden.</td></tr>
         <tr><td>Skript-URI</td>
             <td>Ange URI till det skript som anropas för att anpassa klustret.</br></br>
Ange: </br> <strong>https://portalcontent.BLOB.Core.Windows.NET/scriptaction/documentdb-hadoop-Installer-v04.ps1</strong>.</td></tr>
         <tr><td>Huvud</td>
             <td>Klicka på kryssrutan för att köra PowerShell-skript på huvudnoden.</br></br>
             <strong>Den här kryssrutan</strong>.</td></tr>
         <tr><td>Underordnad</td>
             <td>Klicka på kryssrutan för att köra PowerShell-skript till arbetsnoden.</br></br>
             <strong>Den här kryssrutan</strong>.</td></tr>
         <tr><td>Zookeeper</td>
             <td>Klicka på kryssrutan för att köra PowerShell-skript på Zookeeper.</br></br>
             <strong>Behövs inte</strong>.
             </td></tr>
         <tr><td>Parametrar</td>
             <td>Ange parametrar, om det krävs av skriptet.</br></br>
             <strong>Inga parametrar som behövs för</strong>.</td></tr>
     </table>
11.Skapa en ny **resursgruppen** eller Använd en befintlig resursgrupp under Azure-prenumeration.
12. Kontrollera nu **fäst på instrumentpanelen** att spåra dess distribution och klickar på **skapa**!

## <a name="InstallCmdlets"></a>Steg 2: Installera och konfigurera Azure PowerShell
1. Installera Azure PowerShell. Anvisningar finns [här][powershell-install-configure].

   > [!NOTE]
   > Du kan också använda Hdinsight's online Hive-Redigeraren för Hive-frågor. Om du vill göra det loggar du in på den [Azure Portal][azure-portal], klickar du på **HDInsight** i den vänstra rutan att visa en lista över dina HDInsight-kluster. Klicka på det kluster som du vill köra Hive-frågor och klicka sedan på **frågan konsolen**.
   >
   >
2. Öppna Azure PowerShell Integrated Scripting Environment:

   * Du kan använda inbyggda sökningen på en dator som kör Windows 8 eller Windows Server 2012 eller senare. Ange från startskärmen **powershell ise** och på **RETUR**.
   * Använd Start-menyn på en dator som kör en version tidigare än Windows 8 eller Windows Server 2012. Ange från Start-menyn **kommandotolk** i sökrutan och sedan i listan över resultat, klickar du på **kommandotolk**. I Kommandotolken skriver **powershell_ise** och på **RETUR**.
3. Lägg till ditt Azure-konto.

   1. Skriv i konsolfönstret **Add-AzureAccount** och på **RETUR**.
   2. Skriv e-postadressen som är associerade med din Azure-prenumeration och klicka på **Fortsätt**.
   3. Skriv in lösenordet för din Azure-prenumeration.
   4. Klicka på **logga in**.
4. Följande diagram identifierar viktiga delar i datormiljön Azure PowerShell-skript.

    ![Diagram för Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Steg 3: Kör en Hive-jobb med hjälp av Cosmos-databas och HDInsight
> [!IMPORTANT]
> Alla variabler som anges av < > måste fyllas i med hjälp av inställningarna.
>
>

1. Ange följande variabler i PowerShell-skript-fönstret.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"
2. <p>Vi börjar konstruera frågesträngen. Vi ska skriva en Hive-fråga som tar alla dokument systemgenererade tidsstämplar (_ts) och unika ID: n (_rid) från en samling Azure Cosmos DB, räknar alla dokument per minut och lagrar resultaten tillbaka till en ny Azure DB som Cosmos-samling.</p>

    <p>Först ska vi skapa en Hive-tabell från våra Azure DB som Cosmos-samling. Lägga till följande kodavsnitt i fönstret PowerShell-skript <strong>när</strong> kodstycke från #1. Kontrollera att du inkluderar valfria Frågeparametern Beskär dokument till bara _ts och _rid.</p>

   > [!NOTE]
   > **Namnge DocumentDB.inputCollections var inte fel.** Ja, tillåter vi lägger till flera samlingar som indata: </br>
   >
   >

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.

        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

1. Nu ska vi skapa en Hive-tabell för utdata-samling. Dokumentegenskaper utdata blir månad, dag, timme, minut och det totala antalet förekomster.

   > [!NOTE]
   > **Namnge DocumentDB.outputCollections var ännu igen inte fel.** Ja, tillåter vi lägger till flera samlingar som utdata: </br>
   > '*DocumentDB.outputCollections*'='*\<utdata för DocumentDB-samlingsnamn 1\>*,*\<utdata för DocumentDB-samlingsnamn 2\>*' </br> Samlingen namnen avgränsas utan blanksteg, med bara ett komma. </br></br>
   > Dokument kommer att distribuerade resursallokering över flera samlingar. En batch med dokument kommer att lagras i en samling och sedan en andra grupp dokument kommer att lagras i nästa insamling och så vidare.
   >
   >

       # Create a Hive table for the output data to DocumentDB.
       $queryStringPart2 = "drop table DocumentDB_analytics; " +
                             "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                             "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                             "tblproperties ( " +
                                 "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                 "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                 "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                 "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "
2. Slutligen ska vi tally dokumenten av månad, dag, timme och minut och infoga resultaten tillbaka till utdata Hive-tabell.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "
3. Lägg till följande skript fragment för att skapa en Hive-jobbdefinition från den föregående frågan.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Du kan också använda - filen växeln för att ange en skriptfil för HiveQL på HDFS.
4. Lägg till följande fragment för att spara starttiden och skicka Hive-jobb.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
5. Lägg till följande för att vänta tills Hive-jobb ska slutföras.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
6. Lägg till följande om du vill skriva ut standardutdata och start- och sluttider.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
7. **Kör** nya skriptet! **Klicka på** knappen grön kör.
8. Kontrollera resultaten. Logga in på den [Azure-portalen][azure-portal].

   1. Klicka på <strong>Bläddra</strong> i den vänstra rutan. </br>
   2. Klicka på <strong>allt</strong> längst upp till höger på panelen Bläddra. </br>
   3. Hitta och klickar på <strong>Azure Cosmos DB konton</strong>. </br>
   4. Leta sedan reda din <strong>Azure Cosmos-DB konto</strong>, sedan <strong>Azure Cosmos DB Database</strong> och <strong>Azure Cosmos DB samling</strong> som är associerade med samlingen utdata som anges i Hive-fråga.</br>
   5. Klicka slutligen på <strong>Dokumentutforskaren</strong> under <strong>Developer Tools</strong>.</br></p>

   Du kommer att se resultatet av dina Hive-fråga.

   ![Hive frågeresultat][image-hive-query-results]

## <a name="RunPig"></a>Steg 4: Kör ett Pig-jobb med hjälp av Cosmos-DB och HDInsight
> [!IMPORTANT]
> Alla variabler som anges av < > måste fyllas i med hjälp av inställningarna.
>
>

1. Ange följande variabler i PowerShell-skript-fönstret.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"
2. <p>Vi börjar konstruera frågesträngen. Vi ska skriva en Pig-fråga som tar alla dokument systemgenererade tidsstämplar (_ts) och unika ID: n (_rid) från en samling Azure Cosmos DB, räknar alla dokument per minut och lagrar resultaten tillbaka till en ny Azure DB som Cosmos-samling.</p>
    <p>Ladda först dokument från Cosmos-DB i HDInsight. Lägga till följande kodavsnitt i fönstret PowerShell-skript <strong>när</strong> kodstycke från #1. Se till att lägga till en fråga i parametern DocumentDB-fråga att ta bort våra dokument bara _ts och _rid.</p>

   > [!NOTE]
   > Ja, tillåter vi lägger till flera samlingar som indata: </br>
   > '*\<Indata för DocumentDB-samlingsnamn 1\>*,*\<indata för DocumentDB-samlingsnamn 2\>*'</br> Samlingen namnen avgränsas utan blanksteg, med bara ett komma. </b>
   >
   >

    Dokument kommer att distribuerade resursallokering över flera samlingar. En batch med dokument kommer att lagras i en samling och sedan en andra grupp dokument kommer att lagras i nästa insamling och så vidare.

        # Load data from Cosmos DB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
3. Nu ska vi tally dokumenten av månad, dag, timme, minut och det totala antalet förekomster.

       # GROUP BY minute and COUNT entries for each.
       $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                           "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                           "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "
4. Slutligen ska vi lagra resultaten i vår nya utdata-samlingen.

   > [!NOTE]
   > Ja, tillåter vi lägger till flera samlingar som utdata: </br>
   > '\<Utdata för DocumentDB-samlingsnamn 1\>,\<utdata för DocumentDB-samlingsnamn 2\>'</br> Samlingen namnen avgränsas utan blanksteg, med bara ett komma.</br>
   > Dokument kommer att distribuerade resursallokering över flera samlingar. En batch med dokument kommer att lagras i en samling och sedan en andra grupp dokument kommer att lagras i nästa insamling och så vidare.
   >
   >

        # Store output data to Cosmos DB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "
5. Lägg till följande skript fragment för att skapa en definition av Pig-jobbet från den föregående frågan.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Du kan också använda - filen växeln för att ange en skriptfil för Pig på HDFS.
6. Lägg till följande fragment för att spara starttiden och skicka Pig-jobbet.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition
7. Lägg till följande för att vänta tills Pig-jobbet ska slutföras.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600
8. Lägg till följande om du vill skriva ut standardutdata och start- och sluttider.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
9. **Kör** nya skriptet! **Klicka på** knappen grön kör.
10. Kontrollera resultaten. Logga in på den [Azure-portalen][azure-portal].

    1. Klicka på <strong>Bläddra</strong> i den vänstra rutan. </br>
    2. Klicka på <strong>allt</strong> längst upp till höger på panelen Bläddra. </br>
    3. Hitta och klickar på <strong>Azure Cosmos DB konton</strong>. </br>
    4. Leta sedan reda din <strong>Azure Cosmos-DB konto</strong>, sedan <strong>Azure Cosmos DB Database</strong> och <strong>Azure Cosmos DB samling</strong> som är associerade med samlingen utdata som anges i Pig-fråga.</br>
    5. Klicka slutligen på <strong>Dokumentutforskaren</strong> under <strong>Developer Tools</strong>.</br></p>

    Du kommer att se resultatet av Pig-fråga.

    ![Pig frågeresultat][image-pig-query-results]

## <a name="RunMapReduce"></a>Steg 5: Kör ett MapReduce-jobb med hjälp av Azure Cosmos DB och HDInsight
1. Ange följande variabler i PowerShell-skript-fönstret.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name
2. Vi ska köra ett MapReduce-jobb som räknar antalet förekomster för varje dokumentegenskap från Azure DB som Cosmos-samling. Lägg till det här skriptet fragment **när** fragment ovan.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

   > [!NOTE]
   > TallyProperties v01.jar levereras med anpassad installation av Cosmos DB Hadoop-anslutningen.
   >
   >
3. Lägg till följande kommando för att skicka MapReduce-jobbet.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Förutom jobbdefinitionen MapReduce ange du också klusternamnet HDInsight där du vill köra MapReduce-jobb och autentiseringsuppgifter. Start-AzureHDInsightJob är en asynkron anrop. Om du vill kontrollera jobbet slutförs den *vänta AzureHDInsightJob* cmdlet.
4. Lägg till följande kommando för att kontrollera eventuella fel med MapReduce-jobb.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
5. **Kör** nya skriptet! **Klicka på** knappen grön kör.
6. Kontrollera resultaten. Logga in på den [Azure-portalen][azure-portal].

   1. Klicka på <strong>Bläddra</strong> i den vänstra rutan.
   2. Klicka på <strong>allt</strong> längst upp till höger på panelen Bläddra.
   3. Hitta och klickar på <strong>Azure Cosmos DB konton</strong>.
   4. Leta sedan reda din <strong>Azure Cosmos-DB konto</strong>, sedan <strong>Azure Cosmos DB Database</strong> och <strong>Azure Cosmos DB samling</strong> som är associerade med samlingen utdata som anges i MapReduce-jobb.
   5. Klicka slutligen på <strong>Dokumentutforskaren</strong> under <strong>Developer Tools</strong>.

      Du kommer att se resultatet av MapReduce-jobbet.

      ![MapReduce frågeresultat][image-mapreduce-query-results]

## <a name="NextSteps"></a>Nästa steg
Grattis! Du körde din första Hive, Pig och MapReduce-jobb med hjälp av Azure Cosmos DB och HDInsight.

Vi har öppen källkod våra Hadoop-anslutningen. Om du är intresserad av du kan bidra på [GitHub][github].

Mer information finns i följande artiklar:

* [Utveckla ett Java-program med Documentdb][documentdb-java-application]
* [Utveckla Java-MapReduce-program för Hadoop i HDInsight][hdinsight-develop-deploy-java-mapreduce]
* [Komma igång med Hadoop med Hive i HDInsight för att analysera mobila luren användning][hdinsight-get-started]
* [Använda MapReduce med HDInsight][hdinsight-use-mapreduce]
* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Anpassa HDInsight-kluster med skriptåtgärder][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[import-data]: import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight-develop-deploy-java-mapreduce]:../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight/hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-pig]:../hdinsight/hadoop/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0
