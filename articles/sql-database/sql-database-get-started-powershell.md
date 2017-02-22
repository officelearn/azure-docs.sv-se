---
title: "Powershell: Kom igång med Azure SQL Database | Microsoft Docs"
description: "Lär dig hur du skapar en logisk SQL Database-server, brandväggsregel på servernivå och databaser med hjälp av PowerShell. Du lär dig också att köra frågor mot databaser med hjälp av PowerShell."
keywords: "skapa ny sql-databas, databasinställningar"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: 7d99869b-cec5-4583-8c1c-4c663f4afd4d
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 12/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 93efe1a08149e7c027830b03a9e426ac5a05b27b
ms.openlocfilehash: cf626be4914168d3ed3caae7f959a79324487b4e


---

# <a name="tutorial-provision-and-access-an-azure-sql-database-using-powershell"></a>Självstudie: Etablera och få åtkomst till en Azure SQL Database med PowerShell

I den här introduktionskursen lär du dig hur du använder PowerShell för att:

* Skapa en ny Azure-resursgrupp
* Skapa en logisk Azure SQL-server
* Visa egenskaper för Azure SQL-servrar
* Skapa en brandväggsregel på servernivå
* Skapa AdventureWorksLT-exempeldatabasen som en enskild databas
* Visa egenskaper för AdventureWorksLT-exempeldatabasen
* Skapa en enda tom databas

I den här självstudiekursen ska du även:

* Ansluta till den logiska servern och dess huvuddatabas
* Visa egenskaper för huvuddatabasen
* Ansluta till exempeldatabasen
* Visa egenskaper för användardatabasen

När du är klar med den här självstudiekursen har du en exempeldatabas och en tom databas som körs i en Azure-resursgrupp och som är ansluten till en logisk server. Du har också en brandväggsregel på servernivå som konfigurerats så att huvudobjektet på servernivå kan logga in på servern från en angiven IP-adress (eller ett angivet IP-adressintervall). 

**Uppskattad tidsåtgång**: Den här självstudiekursen tar cirka 30 minuter (förutsatt att du redan uppfyller kraven).


> [!TIP]
> Du kan utföra samma uppgifter i en ”komma igång”-självstudiekurs med hjälp av [Azure Portal](sql-database-get-started.md).
>


## <a name="prerequisites"></a>Krav

* Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* Du måste vara inloggad med ett konto som är medlem i rollen som prenumerationsägare eller deltagare. Mer information om rollbaserad åtkomstkontroll (RBAC) finns i [Getting started with access management in the Azure portal](../active-directory/role-based-access-control-what-is.md) (Komma igång med åtkomsthantering på Azure Portal).

* Du behöver .bacpac-filen för AdventureWorksLT-exempeldatabasen i Azure Blob Storage

### <a name="download-the-adventureworkslt-sample-database-bacpac-file-and-save-it-in-azure-blob-storage"></a>Ladda ned .bacpac-filen för AdventureWorksLT-exempeldatabasen och spara den i Azure Blob Storage

Den här självstudiekursen skapar en ny AdventureWorksLT-databas genom att importera en .bacpac-fil från Azure Storage. Det första steget är att hämta en kopia av AdventureWorksLT.bacpac och att överföra den till Blob Storage.
Följande steg förbereder exempeldatabasen för import:

1. [Hämta AdventureWorksLT.bacpac](https://sqldbbacpacs.blob.core.windows.net/bacpacs/AdventureWorksLT.bacpac) och spara den med filnamnstillägget .bacpac.
2. [Skapa ett lagringskonto](../storage/storage-create-storage-account.md#create-a-storage-account) – Du kan skapa lagringskontot med standardinställningarna.
3. Skapa en ny **behållare** genom att bläddra till lagringskontot, välja **Blobbar** och klicka sedan på **+ Behållare**.
4. Överför .bacpac-filen till blobbehållaren i ditt lagringskonto. Du kan använda knappen **Överför** längst upp på behållarsidan eller [använda AzCopy](../storage/storage-use-azcopy.md#blob-upload). 
5. När du har sparat AdventureWorksLT.bacpac behöver du URL:en och lagringskontots nyckel för att importera kodfragmentet senare i den här självstudiekursen. 
   * Välj din .bacpac-fil och kopiera URL:en. Den ser ut ungefär så här: https://{storage-account-name}.blob.core.windows.net/{container-name}/AdventureWorksLT.bacpac. På sidan för lagringskontot klickar du på **Åtkomstnycklar** och kopierar **key1**.


[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="create-a-new-logical-sql-server-using-azure-powershell"></a>Skapa en ny logisk SQL-server med hjälp av Azure PowerShell

Du behöver en resursgrupp som servern ska ingå i, så det första steget är att antingen skapa en ny resursgrupp och server ([New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroup), [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver)) eller att hämta referenser till befintliga ([Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/get-azurermresourcegroup), [Get-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserver)).
Följande kodfragment skapar en resursgrupp och Azure SQL-server om de inte redan finns:

En lista över giltiga Azure-platser och strängformat finns i [Hjälpkodfragment](#helper-snippets) nedan.
```
# Create new, or get existing resource group
############################################

$resourceGroupName = "{resource-group-name}"
$resourceGroupLocation = "{resource-group-location}"

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-Output "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-Output "Resource group $resourceGroupName already exists:"
}
$myResourceGroup



# Create a new, or get existing server
######################################

$serverName = "{server-name}"
$serverVersion = "12.0"
$serverLocation = $resourceGroupLocation
$serverResourceGroupName = $resourceGroupName

$serverAdmin = "{server-admin}"
$serverAdminPassword = "{server-admin-password}"

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-Output "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-Output "SQL server $serverName already exists:"
}
$myServer

```


## <a name="view-the-logical-sql-server-properties-using-azure-powershell"></a>Visa egenskaperna för den logiska SQL-servern med hjälp av Azure PowerShell

```
#$serverResourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin
```


## <a name="create-a-server-level-firewall-rule-using-azure-powershell"></a>Skapa en brandväggsregel på servernivå med hjälp av Azure PowerShell

Du behöver din offentliga IP-adress för att kunna konfigurera brandväggsregeln. Du kan hämta din IP-adress med hjälp av valfri webbläsare (fråga ”what is my IP address”). Mer information finns i avsnittet om [brandväggsregler](sql-database-firewall-configure.md).

I följande kodfragment används cmdleten [Get-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqlserverfirewallrule) och cmdleten [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserverfirewallrule) för att hämta en referens eller skapa en ny regel. Om regeln redan finns hämtas bara en referens till den i det här kodfragmentet, och start- och slut-IP-adresserna uppdateras inte. Du kan alltid ändra **else**-satsen och använda [Set AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverfirewallrule) för att skapa eller uppdatera funktioner.

> [!NOTE] 
> Du kan öppna SQL Database-brandväggen på servern för en enskild IP-adress eller ett helt adressintervall. Om du öppnar brandväggen kan SQL-administratörer och användare logga in i valfri databas på servern som de har giltiga autentiseringsuppgifter för.
>

```
#$serverName = "{server-name}"
#$serverResourceGroupName = "{resource-group-name}"
$serverFirewallRuleName = "{server-firewall-rule-name}"
$serverFirewallStartIp = "{server-firewall-rule-startIp}"
$serverFirewallEndIp = "{server-firewall-rule-endIp}"

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule
```


## <a name="connect-to-sql-server-using-azure-powershell"></a>Ansluta till SQL-servern med Azure PowerShell

Nu ska vi köra en snabb fråga mot huvuddatabasen för att kontrollera att vi kan ansluta till servern. I följande kodfragment används [.NET Framework Provider for SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) för att ansluta och köra frågan mot serverns huvuddatabas. Den skapar en anslutningssträng baserat på de variabler som vi använde i de tidigare kodfragmenten. Ersätt platshållare med det lösenord för SQL-serveradministratören som du använde när du skapade servern i föregående steg.


```
#$serverName = "{server-name}"
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"



$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```


## <a name="create-new-adventureworkslt-sample-database-using-azure-powershell"></a>Skapa den nya AdventureWorksLT-exempeldatabasen med Azure PowerShell

Följande kodfragment importerar en bacpac av AdventureWorksLT-exempeldatabasen med hjälp av cmdleten [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabaseimport). Bacpac finns i Azure Blob Storage. När du kör cmdleten för import kan du övervaka importförloppet med hjälp av cmdleten [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabaseimportexportstatus).
$StorageUri är URL-egenskapen för bacpac-filen som du tidigare överförde till portalen och bör likna följande: https://{storage-account}.blob.core.windows.net/{container}/AdventureWorksLT.bacpac

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"

$databaseName = "AdventureWorksLT"
$databaseEdition = "Basic"
$databaseServiceLevel = "Basic"

$storageKeyType = "StorageAccessKey"
$storageUri = "{storage-uri}" # URL of bacpac file you uploaded to your storage account
$storageKey = "{storage-key}" # key1 in the Access keys setting of your storage account

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000


Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus
```



## <a name="view-database-properties-using-azure-powershell"></a>Visa databasegenskaper med hjälp av Azure PowerShell

När du har skapat databasen kan du visa några av dess egenskaper.

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
#$databaseName = "{database-name}"

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName


Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status
```

## <a name="connect-and-query-the-sample-database-using-azure-powershell"></a>Ansluta och fråga exempeldatabas med hjälp av Azure PowerShell

Nu ska vi köra en snabb fråga mot AdventureWorksLT-databasen för att kontrollera att vi kan ansluta. I följande kodfragment används [.NET Framework Provider for SQL Server (System.Data.SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) för att ansluta och köra frågan mot databasen. Den skapar en anslutningssträng baserat på de variabler som vi använde i de tidigare kodfragmenten. Ersätt platshållaren med lösenordet för SQL-serveradministratören.

```
#$serverName = {server-name}
#$serverAdmin = "{server-admin}"
#$serverAdminPassword = "{server-admin-password}"
#$databaseName = {database-name}

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=$serverAdmin;Password=$serverAdminPassword" + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()


$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()
```

## <a name="create-a-new-blank-database-using-azure-powershell"></a>Skapa en ny tom databas med hjälp av Azure PowerShell

```
#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```


## <a name="complete-azure-powershell-script-to-create-a-server-firewall-rule-and-database"></a>Slutför Azure PowerShell-skriptet för att skapa en server, brandväggsregel och databas



```
# Sign in to Azure and set the subscription to work with
########################################################

$SubscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myResourceGroupLocation = "{resource-group-location}"

$myServerName = "{server-name}"
$myServerVersion = "12.0"
$myServerLocation = $myResourceGroupLocation
$myServerResourceGroupName = $myResourceGroupName
$myServerAdmin = "{server-admin}"
$myServerAdminPassword = "{server-admin-password}" 

$myServerFirewallRuleName = "{server-firewall-rule-name}"
$myServerFirewallStartIp = "{start-ip}"
$myServerFirewallEndIp = "{end-ip}"

$myDatabaseName = "AdventureWorksLT"
$myDatabaseEdition = "Basic"
$myDatabaseServiceLevel = "Basic"

$myStorageKeyType = "StorageAccessKey"
# Get these values from your Azure storage account:
$myStorageUri = "{http://your-storage-account.blob.core.windows.net/your-container/AdventureWorksLT.bacpac}"
$myStorageKey = "{your-storage-key}"


# Create new, or get existing resource group
############################################

$resourceGroupName = $myResourceGroupName
$resourceGroupLocation = $myResourceGroupLocation

$myResourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ea SilentlyContinue

if(!$myResourceGroup)
{
   Write-host "Creating resource group: $resourceGroupName"
   $myResourceGroup = New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else
{
   Write-host "Resource group $resourceGroupName already exists:"
}
$myResourceGroup


# Create a new, or get existing server
######################################

$serverName = $myServerName
$serverVersion = $myServerVersion
$serverLocation = $myServerLocation
$serverResourceGroupName = $myServerResourceGroupName

$serverAdmin = $myServerAdmin
$serverAdminPassword = $myServerAdminPassword

$securePassword = ConvertTo-SecureString -String $serverAdminPassword -AsPlainText -Force
$serverCreds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue
if(!$myServer)
{
   Write-host "Creating SQL server: $serverName"
   $myServer = New-AzureRmSqlServer -ResourceGroupName $serverResourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
}
else
{
   Write-host "SQL server $serverName already exists:"
}
$myServer


# View server properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$myServer = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 

Write-Host "Server name: " $myServer.ServerName
Write-Host "Fully qualified server name: $serverName.database.windows.net"
Write-Host "Server location: " $myServer.Location
Write-Host "Server version: " $myServer.ServerVersion
Write-Host "Server administrator login: " $myServer.SqlAdministratorLogin


# Create or update server firewall rule
#######################################

$serverFirewallRuleName = $myServerFirewallRuleName
$serverFirewallStartIp = $myServerFirewallStartIp
$serverFirewallEndIp = $myServerFirewallEndIp

$myFirewallRule = Get-AzureRmSqlServerFirewallRule -FirewallRuleName $serverFirewallRuleName -ServerName $serverName -ResourceGroupName $serverResourceGroupName -ea SilentlyContinue

if(!$myFirewallRule)
{
   Write-host "Creating server firewall rule: $serverFirewallRuleName"
   $myFirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $serverResourceGroupName -ServerName $serverName -FirewallRuleName $serverFirewallRuleName -StartIpAddress $serverFirewallStartIp -EndIpAddress $serverFirewallEndIp
}
else
{
   Write-host "Server firewall rule $serverFirewallRuleName already exists:"
}
$myFirewallRule


# Connect to the server and master database
###########################################
$databaseName = "master"

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"


$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create the AdventureWorksLT database from a bacpac
####################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName

$databaseName = $myDatabaseName
$databaseEdition = $myDatabaseEdition
$databaseServiceLevel = $myDatabaseServiceLevel

$storageKeyType = $myStorageKeyType
$storageUri = $myStorageUri
$storageKey = $myStorageKey

$importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -StorageKeytype $storageKeyType -StorageKey $storageKey -StorageUri $storageUri -AdministratorLogin $serverAdmin -AdministratorLoginPassword $securePassword -Edition $databaseEdition -ServiceObjectiveName $databaseServiceLevel -DatabaseMaxSizeBytes 5000000

Do {
     $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
     Write-host "Importing database..." $importStatus.StatusMessage
     Start-Sleep -Seconds 30
     $importStatus.Status
   }
   until ($importStatus.Status -eq "Succeeded")
$importStatus


# View database properties
##########################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseName

$myDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

Write-Host "Database name: " $myDatabase.DatabaseName
Write-Host "Server name: " $myDatabase.ServerName
Write-Host "Creation date: " $myDatabase.CreationDate
Write-Host "Database edition: " $myDatabase.Edition
Write-Host "Database performance level: " $myDatabase.CurrentServiceObjectiveName
Write-Host "Database status: " $myDatabase.Status


# Query the database
####################

$connectionString = "Server=tcp:" + $serverName + ".database.windows.net" + ",1433;Initial Catalog=" + $databaseName + ";Persist Security Info=False;User ID=" + $myServer.SqlAdministratorLogin + ";Password=" + $myServerAdminPassword + ";MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

$connection = New-Object System.Data.SqlClient.SqlConnection
$connection.ConnectionString = $connectionString
$connection.Open()
$command = New-Object System.Data.SQLClient.SQLCommand("select * from sys.objects", $connection)

$command.Connection = $connection
$reader = $command.ExecuteReader()

$sysObjects = ""
while ($reader.Read()) {
    $sysObjects += $reader["name"] + "`n"
}
$sysObjects

$connection.Close()


# Create a blank database
#########################

$blankDatabaseName = "blankdb"
$blankDatabaseEdition = "Basic"
$blankDatabaseServiceLevel = "Basic"


$myBlankDatabase = New-AzureRmSqlDatabase -DatabaseName $blankDatabaseName -ServerName $serverName -ResourceGroupName $resourceGroupName -Edition $blankDatabaseEdition -RequestedServiceObjectiveName $blankDatabaseServiceLevel
$myBlankDatabase
```



> [!TIP]
> Du kan spara pengar medan du lär dig genom att ta bort databaser som du inte använder. Databaser med Basic-versionen kan återställas inom sju dagar. Men ta inte bort en server. Om du gör det kan du inte återställa servern eller någon av dess borttagna databaser.

## <a name="helper-snippets"></a>Hjälpkodfragment

```
# Get a list of Azure regions where you can create SQL resources

$sqlRegions = (Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" })
foreach ($region in $sqlRegions)
{
   $region.Location
}

# Clean up
# Delete a resource group (and all contained resources)
Remove-AzureRmResourceGroup -Name {resource-group-name}
```

> [!TIP]
> Du kan spara pengar medan du lär dig genom att ta bort databaser som du inte använder. Databaser med Basic-versionen kan återställas inom sju dagar. Men ta inte bort servern. Om du gör det kan du inte återställa servern eller någon av dess borttagna databaser.
>

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här första ”komma igång”-självstudiekursen och skapat en databas med exempeldata finns det ett antal andra självstudier som du kanske vill utforska och som bygger vidare på det som du har lärt dig i den här kursen. 

- En självstudiekurs som hjälper dig att komma igång med SQL Server-autentisering finns i [SQL-autensiering och -auktorisering](sql-database-control-access-sql-authentication-get-started.md)
- En självstudiekurs som hjälper dig att komma igång med Azure Active Directory-autentisering finns i [Azure AD-autensiering och -auktorisering](sql-database-control-access-aad-authentication-get-started.md)
* Om du vill fråga exempeldatabasen i Azure Portal kan du läsa [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/) (Allmänt tillgänglig förhandsversion: Interaktiva frågor för SQL-databaser)
* Om du kan Excel, lär du dig hur man [Ansluter till en SQL Database i Azure med Excel](sql-database-connect-excel.md).
* Om du är redo att börja koda, väljer du programmeringsspråk på [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md).
* Om du vill flytta dina lokala SQL Server-databaser till Azure läser du [Migrera en databas till SQL Database](sql-database-cloud-migrate.md).
* Om du vill läsa in data i en ny tabell från en CSV-fil med kommandoradsverktyget BCP, se [Läs in data i SQL Database från en CSV-fil med BCP](sql-database-load-from-csv-with-bcp.md).
* Om du vill börja skapa tabeller och andra objekt läser du avsnittet ”Så här skapar du en tabell” i [Skapa en tabell](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Ytterligare resurser
[Vad är SQL Database?](sql-database-technical-overview.md)



<!--HONumber=Feb17_HO3-->


