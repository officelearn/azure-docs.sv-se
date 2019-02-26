---
title: Kör Apache Sqoop jobb med Azure HDInsight (Apache Hadoop)
description: Lär dig hur du använder Azure PowerShell på en arbetsstation för att köra Sqoop-import och export mellan ett Hadoop-kluster och en Azure SQL database.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 4e56d22dde5fca50d17c055be93db5b91deeb287
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819181"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Använd Apache Sqoop med Hadoop i HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Lär dig hur du använder Apache Sqoop i HDInsight för att importera och exportera mellan HDInsight-kluster och Azure SQL database eller SQL Server-databas.

Även om Apache Hadoop är det naturliga valet för att bearbeta Ostrukturerade och semistrukturerade data, till exempel loggar och filer, kan det också vara nödvändigt att bearbeta strukturerade data som lagras i relationsdatabaser.

[Apache Sqoop] [ sqoop-user-guide-1.4.4] är ett verktyg som utformats för att överföra data mellan Hadoop-kluster och relationsdatabaser. Du kan använda den för att importera data från ett hanteringssystem för relationsdatabaser (RDBMS) som SQL Server, MySQL eller Oracle i Hadoop distributed file system (HDFS), transformera data i Hadoop MapReduce eller Apache Hive och sedan exportera data tillbaka till en RDBMS . I den här självstudien får använder du en SQL Server-databas för din relationsdatabas.

Sqoop-versioner som stöds i HDInsight-kluster, se [vad är nytt i klusterversionerna från HDInsight?][hdinsight-versions]

## <a name="understand-the-scenario"></a>Förstå scenariot

HDInsight-kluster levereras med exempeldata. Du kan använda följande två exempel:

* En Apache Log4j-loggfil som finns i */example/data/sample.log*. Följande loggar extraheras från filen:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* En Hive-tabell med namnet *hivesampletable*, som hänvisar till datafilen i */hive/warehouse/hivesampletable*. Tabellen innehåller några data för mobila enheter. 
  
  | Fält | Datatyp |
  | --- | --- |
  | clientid |sträng |
  | querytime |sträng |
  | marknaden |sträng |
  | deviceplatform |sträng |
  | devicemake |sträng |
  | devicemodel |sträng |
  | state |sträng |
  | Land |sträng |
  | querydwelltime |double |
  | sessions-ID |bigint |
  | sessionpagevieworder |bigint |

I den här självstudien använder du dessa två datamängder för att testa Sqoop-import och export.

## <a name="create-cluster-and-sql-database"></a>Skapa kluster och SQL-databas
Det här avsnittet visar hur du skapar ett kluster, en SQL-databas och SQL databasscheman för att köra självstudien med Azure portal och en Azure Resource Manager-mall. Mallen finns i [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Resource Manager-mallen anropar en bacpac-paket för att distribuera tabellscheman till SQL-databas.  Bacpac-paketet finns i en offentlig blobbehållare https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Om du vill använda en privat behållare för bacpac-filer, använder du följande värden i mallen:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Om du föredrar att använda Azure PowerShell för att skapa klustret och SQL-databasen, se [bilaga A](#appendix-a---a-powershell-sample).

> [!NOTE]  
> Importera med hjälp av en mall eller Azure-portalen stöder bara importera en BACPAC-fil från Azure blob storage.

**Konfigurera miljön med en resource management-mall**
1. Klicka på följande bild för att öppna en Resource Manager-mall i Azure-portalen.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Ange följande egenskaper:

    - **Prenumeration**: Ange din Azure-prenumeration.
    - **Resursgrupp**: Skapa en ny Azure-resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är för hantering av ändamål.  Det är en behållare för objekt.
    - **Plats**: Välj en region.
    - **Klusternamn**: Ange ett namn för Hadoop-klustret.
    - **Klustrets inloggningsnamn och lösenord**: Standardinloggningsnamnet är admin.
    - **SSH-användarnamn och lösenord**.
    - **SQL-databas serverns inloggningsnamn och lösenord**.
    - **_artifacts plats**: Använd standardvärdet om du inte vill använda din egen backpac-fil på en annan plats.
    - **Sas-Token för _artifacts plats**: Lämna tomt.
    - **Bacpac filnamn**: Använd standardvärdet om du inte vill använda din egen backpac-fil.
     
        Följande värden är hårdkodad i variables-avsnittet:
        
        |Namn|Värde|
        |----|-----|
        | Namn på standardlagringskonto | &lt;Klusternamn > lagra |
        | Azure SQL database-servernamn | &lt;Klusternamn > dbserver |
        | Azure SQL-databasnamn | &lt;Klusternamn > db |
     
3. Välj **jag godkänner villkoren som anges ovan**.
4. Klicka på **Köp**. Du ser en ny panel visas med rubriken skicka distribution för malldistribution. Det tar cirka 20 minuter att skapa klustret och SQL Database.

Om du väljer att använda befintliga Azure SQL-databas eller Microsoft SQL Server

* **Azure SQL-databas**: Du måste konfigurera en brandväggsregel för Azure SQL database-server att tillåta åtkomst från din arbetsstation. Mer information om att skapa en Azure SQL-databas och konfigurerar brandväggen finns i [komma igång med Azure SQL-databas][sqldatabase-get-started]. 
  
  > [!NOTE]  
  > Som standard tillåter anslutningar från Azure-tjänster, till exempel Azure HDInsight i en Azure SQL database. Om brandväggsinställningen är inaktiverad, måste du aktivera det från Azure-portalen. Anvisningar om att skapa en Azure SQL-databas och konfigurera brandväggsregler finns i [skapa och konfigurera SQL Database][sqldatabase-create-configure].

* **SQL Server**: Om ditt HDInsight-kluster finns på samma virtuella nätverk i Azure som SQL Server, kan du använda stegen i den här artikeln för att importera och exportera data till en SQL Server-databas.
  
  > [!NOTE]  
  > HDInsight stöder platsbaserad endast till virtuella nätverk och det för närvarande fungerar inte med tillhörighetsgrupp-baserade virtuella nätverk.

  
  * För att skapa och konfigurera ett virtuellt nätverk, se [skapa ett virtuellt nätverk med Azure portal](../../virtual-network/quick-create-portal.md).
    
    * När du använder SQL Server i ditt datacenter, måste du konfigurera det virtuella nätverket som *plats-till-plats* eller *punkt-till-plats*.
      
      > [!NOTE]  
      > För **punkt-till-plats** virtuella nätverk, SQL Server måste köra den VPN-klienten configuration program, som är tillgängliga från den **instrumentpanelen** av din konfiguration för Azure-nätverk.


    * När du använder SQL Server på virtuella Azure-datorer, kan konfiguration av virtuellt nätverk användas om den virtuella datorn som är värd för SQL Server är medlem i samma virtuella nätverk som HDInsight.
  * Om du vill skapa ett HDInsight-kluster i ett virtuellt nätverk, se [skapa Apache Hadoop-kluster i HDInsight med anpassade alternativ](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]  
    > SQL Server måste även tillåta autentisering. Du måste använda en SQL Server-inloggning för att slutföra stegen i den här artikeln.


**Verifiera konfigurationen**

1. Öppna resursgruppen i Azure-portalen. Du bör se fyra resurser i gruppen:

    - klustret
    - databasservern
    - databasen
    - standardkontot för lagring

2. Öppna databasen i Microsoft SQL Server Management Studio.  Du bör se två databaser som har distribuerats:

    ![Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Köra Sqoop-jobb
HDInsight kan köra Sqoop-jobb med hjälp av olika metoder. Använd följande tabell för att bestämma vilken metod som passar dig och klicka sedan på länken för en genomgång.

| **Använd den här** om du vill... | ...an **interaktiva** shell | ...**batch** bearbetning | ...med detta **klustret operativsystem** | ...from detta **klientoperativsystem** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux |Linux, Unix, Mac OS X eller Windows |
| [.NET SDK för Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |? |Linux eller Windows |Windows (för tillfället) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Linux eller Windows |Windows |

## <a name="limitations"></a>Begränsningar
* Massinläsning exportera – med Linux-baserade HDInsight, Sqoop anslutningen används för att exportera data till Microsoft SQL Server eller Azure SQL Database stöder för närvarande inte bulkinfogningar.
* Batchbearbetning - med Linux-baserade HDInsight när du använder den `-batch` växlar när du utför infogningar och Sqoop utför flera infogningar i stället för batchbearbetning insert-åtgärder.

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du använder Sqoop. Du kan läsa mer här:

* [Använda Apache Hive med HDInsight](../hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](../hdinsight-use-pig.md)
* [Ladda upp data till HDInsight][hdinsight-upload-data]: Hitta andra metoder för att ladda upp data till HDInsight/Azure Blob storage.

## <a name="appendix-a---a-powershell-sample"></a>Bilaga A – ett PowerShell-exempel
PowerShell-exemplet utför följande steg:

1. Ansluta till Azure.
2. Skapa en Azure-resursgrupp. Mer information finns i [med hjälp av Azure PowerShell med Azure Resource Manager](../../azure-resource-manager/manage-resource-groups-powershell.md)
3. Skapa en Azure SQL Database-server, en Azure SQL database och två tabeller. 
   
    Om du använder SQL Server i stället använder du följande uttryck för att skapa tabellerna:
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    Det enklaste sättet att undersöka den databasen och tabeller är att använda Visual Studio. Databasservern och databasen granskas med hjälp av Azure portal.
4. Skapa ett HDInsight-kluster.
   
    Du kan använda Azure portal eller Azure PowerShell om du vill kontrollera klustret.
5. Förbearbeta källdatafilen.
   
    I den här självstudien får exportera du en log4j-loggfil (en avgränsad fil) och en Hive-tabell till en Azure SQL-databas. Avgränsad fil kallas */example/data/sample.log*. Tidigare i självstudien fick du se några exempel på log4j loggar över. I loggfilen finns det några tomma rader och rader liknar dessa:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Detta är bra för andra exempel som använder dessa data, men vi måste ta bort de här undantagen innan vi kan importera till Azure SQL database eller SQL Server. Sqoop export misslyckas om det finns en tom sträng eller en rad med ett lägre-element än antalet fält som definierats i Azure SQL-databastabell. Tabellen log4jlogs har sju typen string-fält.
   
    Den här proceduren skapar en ny fil i klustret: tutorials/usesqoop/data/sample.log. Du kan använda Azure-portalen, ett Azure Storage explorer-verktyget eller Azure PowerShell för att granska filen modifierade data. [Kom igång med HDInsight] [ hdinsight-get-started] har ett kodexempel för att använda Azure PowerShell för att hämta en fil och visa filinnehållet.
6. Exportera en fil till Azure SQL-databasen.
   
    Källfilen är tutorials/usesqoop/data/sample.log. Tabellen som data exporteras till kallas log4jlogs.
   
   > [!NOTE]  
   > Förutom informationen i anslutningssträngen, bör stegen i det här avsnittet fungera för en Azure SQL database eller SQL Server. Dessa steg har testats med följande konfiguration:
   > 
   > * **Punkt-till-plats-konfiguration för Azure-nätverk**: Ett virtuellt nätverk är anslutna i HDInsight-klustret till en SQL-Server i ett privat datacenter. Se [konfigurera en punkt-till-plats-VPN i hanteringsportalen](../../vpn-gateway/vpn-gateway-point-to-site-create.md) för mer information.
   > * **Azure HDInsight**: Se [skapa Hadoop-kluster i HDInsight med anpassade alternativ](../hdinsight-hadoop-provision-linux-clusters.md) information om hur du skapar ett kluster i ett virtuellt nätverk.
   > * **SQL Server 2014**: Konfigurerad för att tillåta autentisering och köra den VPN-klienten konfigurationspaketet för att ansluta säkert till det virtuella nätverket.
   > 
   > 
7. Exportera en Hive-tabell till Azure SQL-databasen.
8. Importera tabellen mobiledata i HDInsight-klustret.
   
    Du kan använda Azure-portalen, ett Azure Storage explorer-verktyget eller Azure PowerShell för att granska filen modifierade data.  [Kom igång med HDInsight] [ hdinsight-get-started] har ett kodexempel om hur du använder Azure PowerShell för att hämta en fil och visa filinnehållet.

### <a name="the-powershell-sample"></a>PowerShell-exempel

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "https://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Connect-AzureRmAccount}
#endregion

#region - Create Azure resource group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch{
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzureRmSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzureRMSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#endregion

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configure]: ../../sql-database/sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
