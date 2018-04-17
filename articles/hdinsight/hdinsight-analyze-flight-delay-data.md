---
title: Analysera svarta fördröjning data med Hadoop i HDInsight - Azure | Microsoft Docs
description: Lär dig hur du använder en Windows PowerShell-skript för att skapa ett HDInsight-kluster, kör en Hive-jobb, köra ett Sqoop jobb och tar bort klustret.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00e26aa9-82fb-4dbe-b87d-ffe8e39a5412
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 6e42cfa666ad6b6523043f4412a321789adad9a1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analysera svarta fördröjning data med hjälp av Hive i HDInsight
Hive ger dig möjlighet att köra Hadoop MapReduce jobb via en SQL-liknande skriptspråk som kallas  *[HiveQL][hadoop-hiveql]*, som kan användas mot sammanfattning, fråga och analys av stora mängder data.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett Windows-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Åtgärder för att arbeta med ett Linux-baserade kluster, se [analysera svarta fördröjning data med hjälp av Hive i HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

En av de största fördelarna med Azure HDInsight är uppdelning av datalagring och beräkning. HDInsight använder Azure Blob storage för lagring av data. En typisk jobbet innefattar tre delar:

1. **Lagra data i Azure Blob storage.**  Till exempel väder data, sensordata, webbloggar, och i det här fallet svarta fördröjning data sparas i Azure Blob storage.
2. **Kör jobb.** När det är dags att bearbeta data som du kör ett Windows PowerShell-skript (eller ett klientprogram) för att skapa ett HDInsight-kluster, köra jobb och tar bort klustret. Jobben spara data till Azure Blob storage. Utdata sparas även när klustret har tagits bort. På så sätt kan du betalar bara vad du har förbrukat.
3. **Hämta utdata från Azure Blob storage**, eller exportera data till en Azure SQL database i den här självstudiekursen.

Följande diagram illustrerar scenariot och strukturen för den här kursen:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

Observera att talen i diagrammet motsvarar avsnittsrubriker. **M** står för den huvudsakliga processen. **En** står för innehållet i tillägget.

Den största delen av kursen visar hur du använder en Windows PowerShell-skript för att utföra följande uppgifter:

* Skapa ett HDInsight-kluster.
* Köra en Hive-jobb på klustret för att beräkna genomsnitt förseningar på flygplatser. Den svarta fördröjning data lagras i ett Azure Blob storage-konto.
* Kör ett jobb för Sqoop för att exportera Hive-jobbutdata till en Azure SQL database.
* Ta bort HDInsight-klustret.

I bilagorna hittar du instruktionerna för överföringen svarta fördröjning data, skapa/ladda upp en Hive-frågesträng och förberedde Sqoop jobb på Azure SQL database.

> [!NOTE]
> Stegen i det här dokumentet är specifika för Windows-baserade HDInsight-kluster. Åtgärder för att arbeta med ett Linux-baserade kluster, se [analysera svarta fördröjning data med Hive i HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar den här självstudiekursen behöver du följande:

* **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **En arbetsstation med Azure PowerShell**.

    > [!IMPORTANT]
    > Azure PowerShell-stöd för hantering av HDInsight-resurser med hjälp av Azure Service Manager **är föråldrat** och togs bort den 1 januari 2017. I stegen i det här dokumentet används de nya HDInsight-cmdletarna som fungerar med Azure Resource Manager.
    >
    > Följ stegen i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) för att installera den senaste versionen av Azure PowerShell. Om du har skript som behöver ändras för att använda de nya cmdletarna som fungerar med Azure Resource Manager, hittar du mer information i [Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster](hdinsight-hadoop-development-using-azure-resource-manager.md).

**Filer som används i den här självstudiekursen**

Den här kursen används i tid prestandan för flygbolag svarta data från [forskning och innovativa tekniken Administration, Bureau transport statistik eller RITA][rita-website].
En kopia av data har överförts till en Azure Blob storage-behållare med offentliga Blob-behörighet.
En del av PowerShell-skript kopierar data från den offentliga blob-behållaren till standardbehållaren på klustret. HiveQL-skript kopieras också till samma Blob-behållaren.
Om du vill lära dig hur du get/överför data till ditt eget lagringskonto och hur du skapar/överför HiveQL skriptfilen finns [bilaga A](#appendix-a) och [bilaga B](#appendix-b).

I följande tabell visas de filer som används i den här självstudiekursen:

<table border="1">
<tr><th>Filer</th><th>Beskrivning</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>HiveQL skriptfilen som används av Hive-jobb. Det här skriptet har överförts till ett Azure Blob storage-konto med offentlig åtkomst. <a href="#appendix-b">Bilaga B</a> har instruktioner för att förbereda och överföra den här filen till din egen Azure Blob storage-konto.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Indata för Hive-jobb. Data har överförts till ett Azure Blob storage-konto med offentlig åtkomst. <a href="#appendix-a">Bilaga A</a> innehåller anvisningar som beskriver hämta data och överför data till din egen Azure Blob storage-konto.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Utdatasökvägen för Hive-jobb. Standardbehållaren används för att lagra utdata.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Hive-jobbet status mappen i standardbehållaren.</td></tr>
</table>

## <a name="create-cluster-and-run-hivesqoop-jobs"></a>Skapa kluster och köra Hive/Sqoop jobb
Hadoop MapReduce är batch-bearbetning. Det mest kostnadseffektiva sättet att köra en Hive-jobb är att skapa ett kluster för jobbet och ta bort jobbet när jobbet har slutförts. Följande skript täcker hela processen.
Mer information om hur du skapar ett HDInsight-kluster och köra Hive-jobb finns [skapa Hadoop-kluster i HDInsight] [ hdinsight-provision] och [använda Hive med HDInsight][hdinsight-use-hive].

**Att köra Hive-frågor med Azure PowerShell**

1. Skapa en Azure SQL database och tabellen Sqoop utdata för jobbet med hjälp av anvisningarna i [bilaga C](#appendix-c).
2. Öppna Windows PowerShell ISE och kör följande skript:

    ```powershell
    $subscriptionID = "<Azure Subscription ID>"
    $nameToken = "<Enter an Alias>"

    ###########################################
    # You must configure the follwing variables
    # for an existing Azure SQL Database
    ###########################################
    $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
    $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
    $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
    $existingSqlDatabaseName = "<Azure SQL Database name>"

    $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files.
    $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different

    ###########################################
    # (Optional) configure the following variables
    ###########################################

    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2"

    $HDInsightClusterName = $namePrefix + "hdi"
    $httpUserName = "admin"
    $httpPassword = "<Enter the Password>"

    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $HDInsightClusterName # use the cluster name

    $existingSqlDatabaseTableName = "AvgDelays"
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"

    $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql"

    $jobStatusFolder = "/tutorials/flightdelays/jobstatus"

    ###########################################
    # Login
    ###########################################
    try{
        $acct = Get-AzureRmSubscription
    }
    catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionID $subscriptionID

    ###########################################
    # Create a new HDInsight cluster
    ###########################################

    # Create ARM group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the default storage account
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext

    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $existingDefaultBlobContainerName

    ###########################################
    # Prepare the HiveQL script and source data
    ###########################################

    # Create the temp location
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
    #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload data to default container

    $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"

    Invoke-Expression -Command:$azcopycmd

    ###########################################
    # Submit the Hive job
    ###########################################
    Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
    $response = Invoke-AzureRmHDInsightHiveJob `
                    -Files $hqlScriptFile `
                    -DefaultContainer $defaultBlobContainerName `
                    -DefaultStorageAccountName $defaultStorageAccountName `
                    -DefaultStorageAccountKey $defaultStorageAccountKey `
                    -StatusFolder $jobStatusFolder

    write-Host $response

    ###########################################
    # Submit the Sqoop job
    ###########################################
    $exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                    -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ResourceGroupName $resourceGroupName `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -HttpCredential $httpCredential `
        -WaitTimeoutInSeconds 3600 `
        -Job $sqoopJob.JobId

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -DefaultContainer $existingDefaultBlobContainerName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    ###########################################
    # Delete the cluster
    ###########################################
    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName
    ```
3. Anslut till SQL-databas och se genomsnittlig svarta fördröjningar efter ort i tabellen AvgDelays:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]

- - -

## <a id="appendix-a"></a>Bilaga A – överföringsdata svarta fördröjning till Azure Blob storage
Ladda upp datafilen och HiveQL skriptfiler (se [bilaga B](#appendix-b)) kräver lite planering. Tanken är att lagra datafiler och HiveQL-filen innan du skapar ett HDInsight-kluster och köra Hive-jobb. Du kan välja mellan två alternativ:

* **Använd samma Azure Storage-konto som ska användas av HDInsight-klustret som standardfilsystem.** Eftersom HDInsight-klustret har åtkomstnyckel för lagring, behöver du inte göra några fler ändringar.
* **Använd ett annat Azure Storage-konto från filsystemet HDInsight-kluster.** Om så är fallet måste du ändra skapas en del av Windows PowerShell-skript finns i [skapa HDInsight-kluster och kör Hive/Sqoop jobb](#runjob) länka Storage-konto som ett ytterligare Storage-konto. Instruktioner finns i [skapa Hadoop-kluster i HDInsight][hdinsight-provision]. HDInsight-klustret vet sedan åtkomstnyckeln för lagringskontot.

> [!NOTE]
> Sökvägen för Blob-lagring för datafilen är hårddisken kodad i skriptfilen HiveQL. Du måste uppdatera därefter.

**Att hämta data rör sig**

1. Bläddra till [forskning och innovativa tekniken Administration, som administreras av transport statistik][rita-website].
2. På sidan Välj följande värden:

    <table border="1">
    <tr><th>Namn</th><th>Värde</th></tr>
    <tr><td>Filtrera år</td><td>2013 </td></tr>
    <tr><td>Filtrera Period</td><td>Januari</td></tr>
    <tr><td>Fält</td><td>*År*, *FlightDate*, *UniqueCarrier*, *operatör*, *FlightNum*, *OriginAirportID*, *Ursprung*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*,  *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*,  *LateAircraftDelay* (rensa alla andra fält)</td></tr>
    </table>
3. Klicka på **hämta**.
4. Packa upp filen till den **C:\Tutorials\FlightDelay\2013Data** mapp. Varje fil är en CSV-fil och är ungefär 60GB i storlek.
5. Byt namn på filen till namnet på den månad som den innehåller data för. Till exempel den fil som innehåller data januari namnet *January.csv*.
6. Upprepa steg 2 och 5 för att hämta en fil för varje månad i 2013. Du behöver minst en fil för att köra guiden.

**Överföra svarta fördröjning data till Azure Blob storage**

1. Förbered parametrarna:

    <table border="1">
    <tr><th>Variabelnamn</th><th>Anteckningar</th></tr>
    <tr><td>$storageAccountName</td><td>Azure Storage-konto där du vill överföra data till.</td></tr>
    <tr><td>$blobContainerName</td><td>Blob-behållaren där du vill överföra data till.</td></tr>
    </table>
2. Öppna Azure PowerShell ISE.
3. Klistra in följande skript i skriptfönstret:

    ```powershell
    [CmdletBinding()]
    Param(

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #Region - Variables
    $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
    $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
    #EndRegion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #Region - Copy the file from local workstation to Azure Blob storage
    if (test-path -Path $localFolder)
    {
        foreach ($item in Get-ChildItem -Path $localFolder){
            $fileName = "$localFolder\$item"
            $blobName = "$destFolder/$item"

            Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
        }
    }
    else
    {
        Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
    }

    # List the uploaded files on HDInsight
    Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
    #EndRegion
    ```
4. Tryck **F5** för att köra skriptet.

Om du väljer att använda en annan metod för att överföra filer, kontrollera att sökvägen till filen är självstudier-flightdelay-data. Syntaxen för åtkomst till filerna är:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Sökvägen självstudier/flightdelay/data är virtuell mapp som du skapade när du har överfört filerna. Kontrollera att det finns 12 filer, en för varje månad.

> [!NOTE]
> Du måste uppdatera Hive-fråga för att läsa från den nya platsen.
>
> Antingen måste du konfigurera åtkomstbehörighet för behållaren att vara offentlig eller binda Storage-konto till HDInsight-klustret. Annars frågesträngen Hive inte åtkomst till datafiler.

- - -

## <a id="appendix-b"></a>Bilaga B – skapa och ladda upp en HiveQL-skript
Med Azure PowerShell kan du köra flera HiveQL-instruktioner en i taget eller paketet HiveQL-instruktionen i en skriptfil. Det här avsnittet visar hur du skapar en HiveQL-skript och överföra skriptet till Azure Blob storage med hjälp av Azure PowerShell. Hive kräver HiveQL-skript som ska lagras i Azure Blob storage.

HiveQL-skript kommer att utföra följande:

1. **Ta bort tabellen delays_raw**, om tabellen redan finns.
2. **Skapa delays_raw externa Hive-tabell** pekar på platsen för Blob-lagring med filerna som rör sig fördröjning. Den här frågan anger att fälten avgränsas med ””, och att rader avslutas med ”\n”. Eftersom detta innebär ett problem när fältvärden innehålla kommatecken eftersom Hive kan skilja mellan ett kommatecken som är en fältavgränsaren och en som är en del av ett fältvärde (vilket är fallet i fältvärden för URSPRUNGET\_Stad\_namnet och MÅLET\_Stad\_namn). För att lösa det skapar TEMP kolumner för att lagra data som felaktigt delas upp i kolumner i frågan.
3. **Ta bort tabellen fördröjningar**, om tabellen redan finns.
4. **Skapa tabellen fördröjningar**. Det är bra att rensa data innan vidare bearbetning. Den här frågan skapar en ny tabell *fördröjningar*, från tabellen delays_raw. Observera att inte kopieras TEMP kolumner (som tidigare nämnts) och att den **delsträngen** funktionen används för att ta bort citattecken i informationen.
5. **Beräkna den genomsnittliga väderlek fördröjning och grupper resultaten av Ortnamn.** Det kommer också skriver resultatet till Blob storage. Observera att frågan tar bort apostrofer från data och utesluta rader där värdet för **weather_delay** är null. Detta är nödvändigt eftersom Sqoop, används senare i den här kursen kan inte hantera dessa värden avslutas som standard.

En fullständig lista över HiveQL-kommandon finns [Hive Data Definition Language][hadoop-hiveql]. Varje kommando HiveQL måste sluta med ett semikolon.

**Du skapar en skriptfil för HiveQL**

1. Förbered parametrarna:

    <table border="1">
    <tr><th>Variabelnamn</th><th>Anteckningar</th></tr>
    <tr><td>$storageAccountName</td><td>Azure Storage-konto där du vill överföra HiveQL-skript till.</td></tr>
    <tr><td>$blobContainerName</td><td>Blob-behållaren där du vill överföra HiveQL-skript till.</td></tr>
    </table>
    
2. Öppna Azure PowerShell ISE.  

3. Kopiera och klistra in följande skript i skriptfönstret:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Blob storage variables
        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #region - Define variables
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    # The HiveQL script file is exported as this file before it's uploaded to Blob storage
    $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"

    # The HiveQL script file will be uploaded to Blob storage as this blob name
    $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"

    # These two constants are used by the HiveQL script file
    #$srcDataFolder = "tutorials/flightdelay/data"
    $dstDataFolder = "/tutorials/flightdelay/output"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #region - Validate the file and file path

    # Check if a file with the same file name already exists on the workstation
    Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
    if (test-path $hqlLocalFileName){

        $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'

        if ($isDelete.ToLower() -ne "y")
        {
            Exit
        }
    }

    # Create the folder if it doesn't exist
    $folder = split-path $hqlLocalFileName
    if (-not (test-path $folder))
    {
        Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

        new-item $folder -ItemType directory
    }
    #end region

    #region - Write the Hive script into a local file
    Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                -ForegroundColor Green

    $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

    $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
            "YEAR string, " +
            "FL_DATE string, " +
            "UNIQUE_CARRIER string, " +
            "CARRIER string, " +
            "FL_NUM string, " +
            "ORIGIN_AIRPORT_ID string, " +
            "ORIGIN string, " +
            "ORIGIN_CITY_NAME string, " +
            "ORIGIN_CITY_NAME_TEMP string, " +
            "ORIGIN_STATE_ABR string, " +
            "DEST_AIRPORT_ID string, " +
            "DEST string, " +
            "DEST_CITY_NAME string, " +
            "DEST_CITY_NAME_TEMP string, " +
            "DEST_STATE_ABR string, " +
            "DEP_DELAY_NEW float, " +
            "ARR_DELAY_NEW float, " +
            "CARRIER_DELAY float, " +
            "WEATHER_DELAY float, " +
            "NAS_DELAY float, " +
            "SECURITY_DELAY float, " +
            "LATE_AIRCRAFT_DELAY float) " +
        "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
        "LINES TERMINATED BY '\n' " +
        "STORED AS TEXTFILE " +
        "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"

    $hqlDropDelays = "DROP TABLE delays;"

    $hqlCreateDelays = "CREATE TABLE delays AS " +
        "SELECT YEAR AS year, " +
            "FL_DATE AS flight_date, " +
            "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
            "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
            "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
            "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
            "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
            "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
            "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
            "DEST_AIRPORT_ID AS dest_airport_id, " +
            "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
            "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
            "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
            "DEP_DELAY_NEW AS dep_delay_new, " +
            "ARR_DELAY_NEW AS arr_delay_new, " +
            "CARRIER_DELAY AS carrier_delay, " +
            "WEATHER_DELAY AS weather_delay, " +
            "NAS_DELAY AS nas_delay, " +
            "SECURITY_DELAY AS security_delay, " +
            "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
        "FROM delays_raw;"

    $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
        "SELECT regexp_replace(origin_city_name, '''', ''), " +
            "avg(weather_delay) " +
        "FROM delays " +
        "WHERE weather_delay IS NOT NULL " +
        "GROUP BY origin_city_name;"

    $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

    $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
    #endregion

    #region - Upload the Hive script to the default Blob container
    Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green

    # Create a storage context object
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Upload the file from local workstation to Blob storage
    Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
    #endregion

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

    Här är de variabler som används i skriptet:

   * **$hqlLocalFileName** -skriptet sparar filen HiveQL-skript lokalt innan den skickas till Blob storage. Detta är namnet på filen. Standardvärdet är <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
   * **$hqlBlobName** -detta är HiveQL namnet på skriptfilen blob används i Azure Blob storage. Standardvärdet är tutorials/flightdelay/flightdelays.hql. Eftersom filen kommer att skrivas direkt till Azure Blob storage, är det inte en ”/” i början av blobbnamnet. Om du vill komma åt filen från Blob storage behöver du lägga till en ”/” i början av filnamnet.
   * **$srcDataFolder** och **$dstDataFolder** -= ”data-självstudier/flightdelay” = ”självstudier/flightdelay/utdata”

- - -
## <a id="appendix-c"></a>Bilaga C – förbereda en Azure SQL database Sqoop utdata för jobbet
**Så här förbereder du SQL-databasen (sammanfoga det med skriptet Sqoop)**

1. Förbered parametrarna:

    <table border="1">
    <tr><th>Variabelnamn</th><th>Anteckningar</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Namnet på Azure SQL database-server. Ange inget om du vill skapa en ny server.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>Inloggningsnamnet för Azure SQL database-server. Om $sqlDatabaseServerName är en befintlig server, används inloggningsnamn och inloggningslösenord för att autentisera med servern. Annars används de för att skapa en ny server.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Logga in lösenordet för Azure SQL database-server.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Det här värdet används endast när du skapar en ny Azure databasserver.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>SQL-databas som används för att skapa tabellen AvgDelays Sqoop jobbet. Lämna det tomt skapar en databas som heter HDISqoop. Tabellnamnet Sqoop utdata för jobbet är AvgDelays. </td></tr>
    </table>
    
2. Öppna Azure PowerShell ISE.

3. Kopiera och klistra in följande skript i skriptfönstret:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Resource group variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
        [String]$resourceGroupName,

        # SQL database server variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseServer,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user.")]
        [String]$sqlDatabaseLogin,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user password.")]
        [String]$sqlDatabasePassword,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the region to create the Database in.")]
        [String]$sqlDatabaseLocation,   #For example, West US.

        # SQL database variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
    )

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Constants and variables

    # IP address REST service used for retrieving external IP address and creating firewall rules
    [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
    [String]$fireWallRuleName = "FlightDelay"

    # SQL database variables
    [String]$sqlDatabaseMaxSizeGB = 10

    #SQL query string for creating AvgDelays table
    [String]$sqlDatabaseTableName = "AvgDelays"
    [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
                [origin_city_name] ASC
            )
            )"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #EndRegion

    #region - Create and validate Azure resouce group
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
    }

    #EndRegion

    #region - Create and validate Azure SQL database server
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
    }

    #endregion

    #region - Create and validate Azure SQL database

    try {
        Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
    }

    #endregion

    #region -  Execute an SQL command to create the AvgDelays table

    Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = $sqlCreateAvgDelaysTable
    $cmd.executenonquery()

    $conn.close()

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

   > [!NOTE]
   > Skriptet använder en representational tillstånd transfer (REST)-tjänst http://bot.whatismyipaddress.com, för att hämta extern IP-adress. IP-adressen används för att skapa en brandväggsregel för SQL-databasservern.

    Här följer några variabler som används i skriptet:

   * **$ipAddressRestService** -standardvärdet är http://bot.whatismyipaddress.com. Det är en offentlig IP-adress REST-tjänst för att hämta extern IP-adress. Du kan använda andra tjänster om du vill. Extern IP-adress som hämtats via tjänsten används för att skapa en brandväggsregel för din Azure SQL database-server så att du har åtkomst till databasen från din arbetsstation (med hjälp av Windows PowerShell-skript).
   * **$fireWallRuleName** -detta är namnet på brandväggsregeln för Azure SQL database-server. Standardnamnet är <u>FlightDelay</u>. Du kan byta namn på den om du vill.
   * **$sqlDatabaseMaxSizeGB** -det här värdet används endast när du skapar en ny Azure SQL database-server. Standardvärdet är 10GB. 10GB är tillräcklig för den här kursen.
   * **$sqlDatabaseName** -det här värdet används endast när du skapar en ny Azure SQL-databas. Standardvärdet är HDISqoop. Om du byter namn måste du uppdatera Sqoop Windows PowerShell-skriptet i enlighet med detta.
4. Tryck **F5** för att köra skriptet.
5. Validera utdata från skriptet. Kontrollera att skriptet har körts.

## <a id="nextsteps"></a>Nästa steg
Nu förstå du hur du överför en fil till Azure Blob storage, så att fylla i en Hive-tabell med hjälp av data från Azure Blob storage, hur du kör Hive-frågor och hur du använder Sqoop för att exportera data från HDFS till en Azure SQL database. Mer information finns i följande artiklar:

* [Komma igång med HDInsight][hdinsight-get-started]
* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Oozie med HDInsight][hdinsight-use-oozie]
* [Använda Sqoop med HDInsight][hdinsight-use-sqoop]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Utveckla Java-MapReduce-program för HDInsight][hdinsight-develop-mapreduce]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
