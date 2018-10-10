---
title: Använd Azure Database Migration Service-modul i Microsoft Azure PowerShell för att migrera SQL Server lokalt till Azure SQL DB | Microsoft Docs
description: Lär dig hur du migrerar från en lokal SQL Server till Azure SQL med hjälp av Azure PowerShell.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: ffa4d5f87a722ed3cb95d873d02707ed1c797dc6
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886674"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Migrera SQL Server lokalt till Azure SQL DB med hjälp av Azure PowerShell
I den här artikeln får du migrera den **Adventureworks2012** databasen återställs till en lokal instans av SQL Server 2016 eller senare till en Azure SQL Database med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en lokal SQL Server-instans till Azure SQL Database med hjälp av den `AzureRM.DataMigration` modul i Microsoft Azure PowerShell.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Skapa en resursgrupp.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt i en Azure Database Migration Service-instans.
> * Köra migreringen.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra dessa steg:

- [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (alla versioner)
- Att aktivera TCP/IP-protokollet som är inaktiverad som standard med SQL Server Express-installationen. Aktivera TCP/IP-protokollet genom att följa artikeln [aktivera eller inaktivera ett Server-nätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Så här konfigurerar du din [Windows brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- En Azure SQL Database-instans. Du kan skapa en Azure SQL Database-instans genom att följa detaljerat i artikeln [skapa en Azure SQL database i Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 eller senare.
- Du har skapat ett virtuellt nätverk med hjälp av Azure Resource Manager-distributionsmodellen, som ger Azure Database Migration Service med plats-till-plats-anslutningen till din lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Du har slutfört en bedömning av din lokala-databas och schema-migrering med Data Migration Assistant enligt beskrivningen i artikeln [ utför en migreringsutvärdering för SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- Ladda ned och installera modulen AzureRM.DataMigration från PowerShell-galleriet med hjälp av [Install-Module PowerShell-cmdleten](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); se till att öppna powershell-kommandofönster med Kör som administratör.
- Se till att de autentiseringsuppgifter som används för att ansluta till SQL Server-instans har den [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) behörighet.
- För att säkerställa att de autentiseringsuppgifter som används för att ansluta till målet för Azure SQL DB har-instansen KONTROLLDATABAS-behörighet på Azure SQL Database måldatabaserna.
- En Azure-prenumeration. Om du inte har något skapar du en [kostnadsfria](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på Microsoft Azure-prenumerationen
Använd anvisningarna i artikeln [logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) att logga in på din Azure-prenumeration med hjälp av PowerShell.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp innan du kan skapa en virtuell dator.

Skapa en resursgrupp med hjälp av den [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) kommando. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *EastUS* region.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service 
Du kan skapa ny instans av Azure Database Migration Service med hjälp av den `New-AzureRmDataMigrationService` cmdlet. Den här cmdleten förväntar sig att följande obligatoriska parametrar:
- *Namn på Azure-resursgrupp*. Du kan använda [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) kommando för att skapa Azure-resursgrupp som tidigare visas och ange dess namn som en parameter.
- *Tjänstnamnet*. Sträng som motsvarar det önskade unikt tjänstnamnet för Azure Database Migration Service 
- *Plats*. Anger platsen för tjänsten. Ange en plats för Azure data center, till exempel USA, västra eller Sydostasien
- *SKU*. Den här parametern motsvarar DMS Sku-namnet. För närvarande stöds Sku-namn är *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
- *Virtuella undernät-ID*. Du kan använda cmdleten [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) att skapa ett undernät. 

I följande exempel skapas en tjänst med namnet *MyDMS* i resursgruppen *MyDMSResourceGroup* finns i den *USA, östra* region med hjälp av ett virtuellt nätverk med namnet  *MyVNET* och undernät som kallas *MySubnet*.

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
När du har skapat en Azure Database Migration Service-instans, skapar du ett migreringsprojekt. Ett projekt med Azure Database Migration Service kräver anslutningsinformationen för både källa och mål-instanser, samt en lista över databaser som du vill migrera som en del av projektet.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Skapa ett Database Connection-Info-objekt för källa och mål-anslutningar
Du kan skapa en databas Connection-Info-objekt med hjälp av den `New-AzureRmDmsConnInfo` cmdlet. Cmdlet: en kräver följande parametrar:
- *ServerType*. Typ av databasanslutning begärts, till exempel SQL, Oracle eller MySQL. Använda SQL för SQLServer och Azure SQL.
- *DataSource*. Namn eller IP-Adressen för en instans av SQL Server eller Azure SQL-databas.
- *AuthType*. Autentiseringstyp för anslutning, som kan vara antingen SqlAuthentication eller WindowsAuthentication.
- *TrustServerCertificate* parametern anger ett värde som anger om kanalen är krypterad när kringgår walking certifikatkedjan för att verifiera förtroendet. Värdet kan vara sant eller FALSKT.

I följande exempel skapar anslutningsinformation objekt för källan SQL-servern med namnet MySourceSQLServer med sql-autentisering: 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

I nästa exempel visas skapandet av anslutningsinformation för en Azure SQL database-server som heter SQLAzureTarget med sql-autentisering:

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Ange databaser för migreringsprojektet
Skapa en lista över `AzureRmDataMigrationDatabaseInfo` objekt som anger databaser som en del av Azure Database Migration-projekt som kan anges som parameter för att skapa projektet. Cmdlet: en `New-AzureRmDataMigrationDatabaseInfo` kan användas för att skapa AzureRmDataMigrationDatabaseInfo. 

I följande exempel skapas `AzureRmDataMigrationDatabaseInfo` projektet för den **AdventureWorks2016** databasen och lägger till den i listan anges som parameter för att skapa projekt.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Skapa ett projektobjekt
Slutligen kan du skapa Azure Database Migration-projekt med namnet *MyDMSProject* i *USA, östra* med `New-AzureRmDataMigrationProject` och lägga till de tidigare skapade källa och mål-anslutningarna och listan över databaser som ska migreras.

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

## <a name="create-and-start-a-migration-task"></a>Skapa och starta en migreringen
Slutligen skapar du och Azure Database Migration startaktivitet. Azure Databasmigrering uppgiften kräver autentiseringsuppgifter anslutningsinformationen för både källa och mål och listan över databastabeller som ska migreras utöver informationen som följer med projekt som skapas som ett krav. 

### <a name="create-credential-parameters-for-source-and-target"></a>Skapa autentiseringsuppgifter parametrar för källa och mål
Säkerhetsreferenser för anslutningen kan skapas som en [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt. 

I följande exempel visar *PSCredential* objekt för både källa och mål-anslutningar ger lösenord som strängvariabler *$sourcePassword* och *$ Mållösenord*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Skapa en karta i tabellen och välj käll- och parametrar för migrering
En annan parameter som krävs för migrering av mappning av tabeller från källan till målet som ska migreras. Skapa ordlista över tabeller som innehåller en mappning mellan käll- och tabeller för migrering. I följande exempel illustrerar mappning mellan källa och mål tabeller personalfrågor schemat för AdventureWorks 2016-databas.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Nästa steg är att väljer du databaserna som källa och mål och ge tabellmappning för att migrera som en parameter med hjälp av den `New-AzureRmDmsSelectedDB` cmdlet, enligt följande exempel:

```powershell
$selectedDbs = New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>Skapa och starta en migreringen

Använd den `New-AzureRmDataMigrationTask` cmdlet för att skapa och starta en migreringen. Cmdlet: en kräver följande parametrar:
- *TaskType*. Typ av migreringen för att skapa för SQL Server till Azure SQL Database Migreringstyp *MigrateSqlServerSqlDb* förväntas. 
- *Namn på resursgrupp*. Namnet på Azure-resursgrupp där du vill skapa uppgiften.
- *ServiceName*. Azure Database Migration Service-instans där du vill skapa uppgiften.
- *ProjectName*. Namnet på Azure Database Migration Service-projekt där du vill skapa uppgiften. 
- *Aktivitetsnamn*. Namnet på aktiviteten som ska skapas. 
- *SourceConnection*. AzureRmDmsConnInfo-objekt som representerar källan SQL Server-anslutning.
- *TargetConnection*. AzureRmDmsConnInfo-objekt som representerar target Azure SQL Database-anslutningar.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt för att ansluta till källservern.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt för att ansluta till målservern.
- *SelectedDatabase*. AzureRmDataMigrationSelectedDB-objekt som representerar källan och målet databasen mappningen.

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
Du kan övervaka migreringen som körs genom att fråga egenskapen state för uppgiften som du ser i följande exempel:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Nästa steg
- Granska migreringsvägledningen i Microsofts [Guide för Databasmigrering](https://datamigration.microsoft.com/).