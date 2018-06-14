---
title: Använd Azure databastjänsten migrering modul i Microsoft Azure PowerShell för att migrera SQL Server lokalt till Azure SQL DB | Microsoft Docs
description: Lär dig att migrera från lokala SQL Server till SQL Azure med hjälp av Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/05/2018
ms.openlocfilehash: d70d39f027df1fba7934c4b752b1dd2100f2e8b9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30905382"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrera SQL Server lokalt till Azure SQL-databas med hjälp av Azure PowerShell
I den här artikeln migrerar du den **Adventureworks2012** databasen återställs till en lokal instans av SQL Server 2016 eller senare till ett Azure SQL Database med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en lokal SQL Server-instans till Azure SQL Database med hjälp av den `AzureRM.DataMigration` modul i Microsoft Azure PowerShell.

I den här artikeln får du lära dig hur du:
> [!div class="checklist"]
> * Skapa en resursgrupp.
> * Skapa en instans av tjänsten Azure Database migrering.
> * Skapa ett migreringsprojekt i en Azure-databas migrering Service-instans.
> * Kör migreringen.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra dessa steg:

- [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (någon utgåva)
- TCP/IP-protokollet är inaktiverat som standard med SQL Server Express-installationen. Aktivera det genom att följa den [anvisningarna i den här artikeln](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Så här konfigurerar du din [Windows-brandväggen för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- En Azure SQL Database-instans. Du kan skapa en Azure SQL Database-instans med följande information i artikeln [skapa en Azure SQL database i Azure portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 eller senare.
- Tjänsten Azure Database migrering kräver ett VNET som skapats med hjälp av Azure Resource Manager distributionsmodell, som ger plats-till-plats-anslutning till din lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Utvärderingen av din lokala databas och schema migrering med hjälp av Data Migration Assistant enligt beskrivningen i artikeln slutfördes [ utvärdering en SQL Server-migrering](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Hämta och installera modulen AzureRM.DataMigration från PowerShell-galleriet för använder [installera modulen PowerShell-cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- De autentiseringsuppgifter som används för att ansluta till datakällan SQL Server-instansen måste ha den [KONTROLLSERVERN](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) behörighet.
- De autentiseringsuppgifter som används för att ansluta till Azure SQL DB målinstansen måste ha CONTROL DATABASE-behörighet på Azure SQL Database måldatabaserna.
- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på Microsoft Azure-prenumerationen
Använd instruktionerna i artikeln [logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) att logga in på din Azure-prenumeration med hjälp av PowerShell.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Skapa en resursgrupp innan du kan skapa en virtuell dator.

Skapa en resursgrupp med hjälp av den [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) kommando. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *EastUS* region.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure-databas migrering Service-instans 
Du kan skapa ny instans av Azure databastjänsten migrering med hjälp av den `New-AzureRmDataMigrationService` cmdlet. Cmdlet: en kräver följande obligatoriska parametrar:
- *Namn på Azure-resursgrupp*. Du kan använda [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) kommando för att skapa Azure-resursgrupp som tidigare visas och ange dess namn som en parameter.
- *Tjänstnamnet*. Sträng som motsvarar önskad unikt tjänstnamn för Azure-databas migrering 
- *Plats*. Anger platsen för tjänsten. Ange en plats för Azure data center, till exempel västra USA eller Sydostasien
- *Sku*. Den här parametern motsvarar DMS Sku namn. För närvarande stöds Sku namn är *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Virtuella undernät-ID*. Du kan använda cmdleten [ny AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) att skapa ett undernät. 

I följande exempel skapar en tjänst med namnet *MyDMS* i resursgruppen *MyDMSResourceGroup*, som finns i *östra USA* region med ett virtuellt nätverk med namnet *MyVNET* och undernät som kallas *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt
När du har skapat ett Azure-databas migrering tjänstinstans, skapa ett migreringsprojekt. En Azure-databas migrering Service-projekt kräver anslutningsinformationen för både käll- och instanser, samt en lista över databaser som du vill migrera som del av projektet.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Skapa ett objekt för databasen anslutningsinformation för källa och mål-anslutningar
Du kan skapa en databas anslutningsinformation-objekt med hjälp av den `New-AzureRmDmsConnInfo` cmdlet. Cmdlet: en kräver följande parametrar:
- *ServerType*. Typ av databasanslutning begärde, till exempel SQL, Oracle eller MySQL. Använd SQL för SQLServer och SQL Azure.
- *DataSource*. Namn eller IP för en SQL-instans eller Azure SQL-server.
- *Autentiseringstyp*. Autentiseringstypen för anslutningen, som kan vara SqlAuthentication eller WindowsAuthentication.
- *TrustServerCertificate* parametern anger ett värde som anger om kanalen är krypterad när kringgå går certifikatkedjan för att validera förtroende. Värdet kan vara sant eller FALSKT.

I följande exempel skapar anslutningsinformation objekt för SQL Server som kallas MySQLSourceServer med sql-autentisering 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Nästa exempel visas skapa anslutningsinformation för SQL Azure database-server som kallas MySQLAzureTarget med sql-autentisering

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Ange databaser för projektet migrering
Skapa en lista över `AzureRmDataMigrationDatabaseInfo` objekt som anger databaser som en del av migreringen för Azure-databas projekt som kan anges som parameter för att skapa projektet. Cmdlet `New-AzureRmDataMigrationDatabaseInfo` kan användas för att skapa AzureRmDataMigrationDatabaseInfo. 

I följande exempel skapas `AzureRmDataMigrationDatabaseInfo` projekt för databasen AdventureWorks2016 och lägger till den i listan måste anges som parameter för projektet har skapats.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Skapa ett projektobjekt
Slutligen kan du skapa Azure-Databasmigrering projektet med namnet *MyDMSProject* finns i *östra USA* med `New-AzureRmDataMigrationProject` och lägga till de tidigare skapade källa och mål-anslutningarna och listan över databaser för att migrera.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Skapa och starta uppgiften för migrering
Slutligen, skapa och starta migreringen för Azure-databas. Azure-databas migreringen kräver autentiseringsuppgifter för anslutning för både käll- och mål- och listan över databastabeller som ska migreras utöver informationen som redan ingår i projektet har skapats som en förutsättning. 

### <a name="create-credential-parameters-for-source-and-target"></a>Skapa autentiseringsuppgifter parametrar för källa och mål
Säkerhetsreferenser för anslutningen kan skapas som en [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt. 

Följande exempel visar skapandet av *PSCredential* objekt för både käll- och anslutningar som tillhandahåller lösenord som strängvariabler *$sourcePassword* och *$ Mållösenord*. 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Skapa en tabell karta och välj käll- och parametrar för migrering
En annan parameter som behövs för migrering mappning av tabeller från källan till målet som ska migreras. Skapa ordlista över tabeller som innehåller en mappning mellan käll- och tabeller för migrering. Följande exempel illustrerar mappningsschemat mellan käll- och tabeller personal för AdventureWorks 2016-databasen.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Nästa steg är att Välj käll- och databaserna och ange mappningen för tabellen för att migrera som en parameter med hjälp av den `New-AzureRmDmsSqlServerSqlDbSelectedDB` cmdlet, som visas i följande exempel:

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Skapa och starta uppgiften för migrering

Använd den `New-AzureRmDataMigrationTask` för att skapa och starta uppgiften för migrering. Cmdlet: en kräver följande parametrar:
- *TaskType*. Typ av migreringen skapa för SQL Server till SQL Azure migreringstypen *MigrateSqlServerSqlDb* förväntas. 
- *Resursgruppens namn*. Namnet på Azure-resursgrupp där du vill skapa uppgiften.
- *ServiceName*. Azure-databas migrering Service-instans där du vill skapa uppgiften.
- *Projektnamn*. Namnet på Azure-Databasmigrering projekt där du vill skapa uppgiften. 
- *Aktivitetsnamn*. Namnet på aktiviteten som ska skapas. 
- *Datakällan anslutning*. AzureRmDmsConnInfo-objekt som representerar anslutningen till datakällan.
- *Rikta anslutning*. AzureRmDmsConnInfo-objekt som representerar målanslutningen.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt för att ansluta till källservern.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt för att ansluta till målservern.
- *SelectedDatabase*. AzureRmDmsSqlServerSqlDbSelectedDB-objekt som representerar källan och målet databasen mappningen.

I följande exempel skapar och startar en myDMSTask för av migreringsaktiviteten:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>Övervaka migreringen
Du kan övervaka migreringen kör genom att fråga egenskapen state för aktiviteten som visas i följande exempel:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Nästa steg
- Granska riktlinjerna för migrering i Microsoft [databasen Migreringsguide](https://datamigration.microsoft.com/).