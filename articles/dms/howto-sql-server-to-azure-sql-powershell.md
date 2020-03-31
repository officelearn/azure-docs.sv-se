---
title: 'Powershell: Migrera SQL Server till SQL Database'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera från lokala SQL Server till Azure SQL Database med hjälp av Azure PowerShell med Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: f63f79402b457017257f1762c6ddc7e04c0ee1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650698"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-using-azure-powershell"></a>Migrera SQL Server lokalt till Azure SQL Database med Azure PowerShell

I den här artikeln migrerar du **Adventureworks2012-databasen** som återställs till en lokal instans av SQL Server 2016 eller senare till en Azure SQL-databas med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en lokal SQL Server-instans till `Az.DataMigration` Azure SQL Database med hjälp av modulen i Microsoft Azure PowerShell.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Skapa en resursgrupp.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt i en Azure Database Migration Service-instans.
> * Köra migreringen.

## <a name="prerequisites"></a>Krav

För att kunna utföra de här stegen behöver du:

* [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (valfri utgåva)
* Så här aktiverar du TCP/IP-protokollet, som är inaktiverat som standard med SQL Server Express-installation. Aktivera TCP/IP-protokollet genom att följa artikeln [Aktivera eller inaktivera ett servernätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Så här konfigurerar du [Windows-brandväggen för åtkomst till databasmotor](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* En Azure SQL Database-instans. Du kan skapa en Azure SQL Database-instans genom att följa detaljerna i artikeln [Skapa en Azure SQL-databas i Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* [Datamigreringsassistenten](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 eller senare.
* Du vill ha skapat ett Virtuellt Microsoft Azure-nätverk med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller Azure Database Migration Service med plats-till-plats-anslutning till dina lokala källservrar med hjälp av [antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Så här har du slutfört utvärderingen av din lokala databas och schemamigrering med hjälp av Datamigreringsassistenten enligt beskrivningen i artikeln [Utför en SQL Server-migreringsutvärdering](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
* Så här hämtar och installerar du modulen Az.DataMigration från PowerShell Gallery med hjälp av [PowerShell-cmdlet för installationsmodulen](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1); se till att öppna kommandofönstret powershell med körning som administratör.
* För att säkerställa att de autentiseringsuppgifter som används för att ansluta till källan SQL Server-instans har [behörigheten CONTROL SERVER.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* För att säkerställa att de autentiseringsuppgifter som används för att ansluta till azure SQL DB-instansen har behörigheten CONTROL DATABASE för målet Azure SQL Database-databaser.
* En Azure-prenumeration. Om du inte har ett, skapa ett [gratis](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på din Microsoft Azure-prenumeration

Använd anvisningarna i artikeln [Logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) för att logga in på din Azure-prenumeration med powershell.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp innan du kan skapa en virtuell dator.

Skapa en resursgrupp med kommandot [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i *EastUS-regionen.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

Du kan skapa en ny instans av `New-AzDataMigrationService` Azure Database Migration Service med hjälp av cmdlet. Denna cmdlet förväntar sig följande nödvändiga parametrar:

* *Namn på Azure Resource Group*. Du kan använda kommandot [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) för att skapa Azure Resource-gruppen som tidigare visats och ange dess namn som en parameter.
* *Tjänstnamn*. Sträng som motsvarar önskat unikt tjänstnamn för Azure Database Migration Service 
* *Plats*. Anger tjänstens plats. Ange en Azure-datacenterplats, till exempel Västra USA eller Sydostasien
* *Sku*. Den här parametern motsvarar DMS Sku-namnet. Det Sku-namn som stöds stöds är *GeneralPurpose_4vCores*.
* *Identifierare för virtuellt undernät*. Du kan använda cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) för att skapa ett undernät. 

I följande exempel skapas en tjänst med namnet *MyDMS* i resursgruppen *MyDMSResourceGroup* i regionen *Östra USA* med hjälp av ett virtuellt nätverk med namnet *MyVNET* och undernät som heter *MySubnet*.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När du har skapat en Azure Database Migration Service-instans skapar du ett migreringsprojekt. Ett Azure Database Migration Service-projekt kräver anslutningsinformation för både käll- och målinstanser samt en lista över databaser som du vill migrera som en del av projektet.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Skapa ett databasanslutningsinformationsobjekt för käll- och målanslutningarna

Du kan skapa ett databasanslutningsinformationsobjekt med hjälp av cmdleten. `New-AzDmsConnInfo` Denna cmdlet förväntar sig följande parametrar:

* *ServerType*. Den typ av databasanslutning som begärs, till exempel SQL, Oracle eller MySQL. Använd SQL för SQL Server och Azure SQL.
* *DataSource*. Namnet eller IP-adressen för en SQL Server-instans eller Azure SQL-databas.
* *AuthType*. Autentiseringstypen för anslutning, som kan vara antingen SqlAuthentication eller WindowsAuthentication.
* *Parametern TrustServerCertificate* anger ett värde som anger om kanalen är krypterad när du kringgår att gå i certifikatkedjan för att validera förtroende. Värdet kan vara sant eller falskt.

I följande exempel skapas Connection Info-objekt för käll-SQL Server som kallas MySourceSQLServer med SQL-autentisering:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

I nästa exempel visas skapandet av anslutningsinformation för en Azure SQL-databasserver som heter SQLAzureTarget med SQL-autentisering:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Tillhandahålla databaser för migreringsprojektet

Skapa en `AzDataMigrationDatabaseInfo` lista över objekt som anger databaser som en del av Azure Database Migration-projektet som kan tillhandahållas som parameter för att skapa projektet. Cmdlet `New-AzDataMigrationDatabaseInfo` kan användas för att skapa AzDataMigrationDatabaseInfo. 

I följande exempel `AzDataMigrationDatabaseInfo` skapas projekt för **AdventureWorks2016-databasen** och det läggs till i listan som ska anges som parameter för att skapa projekt.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Skapa ett projektobjekt

Slutligen kan du skapa Azure Database Migration-projektet *MyDMSProject* som finns i *östra USA* med hjälp av `New-AzDataMigrationProject` och lägga till tidigare skapade käll- och målanslutningar och listan över databaser som ska migreras.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Skapa och starta en migreringsuppgift

Slutligen skapa och starta Azure Database Migration-uppgift. Azure Database Migration-aktivitet kräver anslutningsautentiseringsinformation för både käll- och mål- och lista över databastabeller som ska migreras utöver den information som redan finns i projektet som skapats som en förutsättning.

### <a name="create-credential-parameters-for-source-and-target"></a>Skapa referensparametrar för källa och mål

Autentiseringsuppgifter för anslutningssäkerhet kan skapas som ett [PSCredential-objekt.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

I följande exempel visas skapandet av *PSCredential-objekt* för både käll- och målanslutningar som ger lösenord som strängvariabler *$sourcePassword* och *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>Skapa en tabellkarta och välj käll- och målparametrar för migrering

En annan parameter som behövs för migrering är mappning av tabeller från källa till mål som ska migreras. Skapa ordlista med tabeller som tillhandahåller en mappning mellan käll- och måltabeller för migrering. Följande exempel illustrerar mappning mellan käll- och måltabeller Personalschema för AdventureWorks 2016-databasen.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

Nästa steg är att välja käll- och måldatabaser och tillhandahålla tabellmappning för att migrera som en parameter med hjälp av `New-AzDmsSelectedDB` cmdlet, som visas i följande exempel:

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>Skapa migreringsuppgiften och starta den

Använd `New-AzDataMigrationTask` cmdlet för att skapa och starta en migreringsuppgift. Denna cmdlet förväntar sig följande parametrar:

* *TaskType*. Typ av migreringsuppgift som ska skapas för SQL Server till Azure SQL Database migreringstyp *MigrateSqlServerSqlDb* förväntas. 
* *Namn på resursgrupp*. Namn på Azure-resursgrupp där aktiviteten ska skapas.
* *ServiceName*. Azure Database Migration Service-instans där uppgiften ska skapas.
* *ProjectName*. Namn på Azure Database Migration Service-projektet där aktiviteten ska skapas. 
* *TaskName*. Namn på den uppgift som ska skapas. 
* *SourceConnection*. AzDmsConnInfo-objekt som representerar källan SQL Server-anslutning.
* *TargetConnection*. AzDmsConnInfo-objekt som representerar mål-Azure SQL Database-anslutning.
* *SourceCred*. [PSCredential-objekt](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) för anslutning till källserver.
* *TargetCred*. [PSCredential-objekt](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) för anslutning till målservern.
* *SelectedDatabase*. AzDataMigrationSelectedDB-objekt som representerar käll- och måldatabasmappningen.
* *SchemaValidation*. (valfritt, växelparameter) Efter migreringen utför en jämförelse av schemainformationen mellan källa och mål.
* *DataIntegrityValidation*. (valfritt, växelparameter) Efter migreringen utför en kontrollsummabaserad dataintegritetsverifiering mellan källa och mål.
* *QueryAnalysisValidation*. (valfritt, växelparameter) Efter migreringen utför du en snabb och intelligent frågeanalys genom att hämta frågor från källdatabasen och köra dem i målet.

I följande exempel skapas och startas en migreringsuppgift med namnet myDMSTask:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

I följande exempel skapas och startas samma migreringsuppgift som ovan men alla tre valideringarna utförs:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>Övervaka migreringen

Du kan övervaka migreringsuppgiften som körs genom att fråga egenskapen state för aktiviteten enligt följande exempel:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Ta bort DMS-instansen

När migreringen är klar kan du ta bort Azure DMS-instansen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>Nästa steg

* Läs migreringsvägledningen i [Migreringsguiden för](https://datamigration.microsoft.com/)Microsoft Database .
