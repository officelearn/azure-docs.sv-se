---
title: Migrera SQLServer till Azure SQL Database Managed Instance med Database Migration Service och PowerShell | Microsoft Docs
description: Lär dig hur du migrerar från en lokal SQL Server till Azure SQL DB Managed Instance med Azure PowerShell.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: 93ab8b2aca49fcd0d2f27ec17f7fc519b19bf563
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305194"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrera SQL Server lokalt till Azure SQL Database Managed Instance med Azure PowerShell
I den här artikeln får du migrera den **Adventureworks2012** databasen återställs till en lokal instans av SQL Server 2005 eller senare till en Azure SQL Database Managed Instance med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en lokal SQL Server-instans till Azure SQL Database Managed Instance med hjälp av den `AzureRM.DataMigration` modul i Microsoft Azure PowerShell.

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
- Ladda ned och installera modulen AzureRM.DataMigration från PowerShell-galleriet med hjälp av [Install-Module PowerShell-cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- Se till att de autentiseringsuppgifter som används för att ansluta till SQL Server-instans har den [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) behörighet.
- För att säkerställa att de autentiseringsuppgifter som används för att ansluta till målet för Azure SQL DB har-instansen KONTROLLDATABAS-behörighet på Azure SQL Database måldatabaserna.
- En Azure-prenumeration. Om du inte har något skapar du en [kostnadsfria](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Logga in på Microsoft Azure-prenumerationen
Använd anvisningarna i artikeln [logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1) att logga in på Azure-prenumerationen med hjälp av PowerShell.

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
- *Sku*. Den här parametern motsvarar DMS Sku-namnet. För närvarande stöds Sku-namn är *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*
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

I nästa exempel visas skapandet av anslutningsinformation för en Azure SQL Database Managed Instance-servern med namnet 'targetmanagedinstance.database.windows.net' med sql-autentisering:

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Ange databaser för migreringsprojektet
Skapa en lista över `AzureRmDataMigrationDatabaseInfo` objekt som anger databaser som en del av Azure Database Migration-projekt som kan anges som parameter för att skapa projektet. Cmdlet: en `New-AzureRmDataMigrationDatabaseInfo` kan användas för att skapa AzureRmDataMigrationDatabaseInfo. 

I följande exempel skapas `AzureRmDataMigrationDatabaseInfo` projektet för den **AdventureWorks** databasen och lägger till den i listan anges som parameter för att skapa projekt.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
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
  -TargetType SQLMI `
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

### <a name="create-backup-fileshare-object"></a>Skapa backup filresursen objekt
Nu ska du skapa filresurs-objekt som representerar den lokala SMB-nätverksresurs som Azure Database Migration Service kan göra källan säkerhetskopior av databasen till med hjälp av cmdleten New-AzureRmDmsFileShare.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzureRmDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Skapa markerat databasobjekt
Nästa steg är att väljer du databaserna som källa och mål med hjälp av cmdleten New-AzureRmDmsSelectedDB som visas i följande exempel:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>SAS-URI för Azure Storage-behållare
Skapa variabeln som innehåller SAS-URI som ger tillgång till den lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna Azure Database Migration Service.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>Välj inloggningar
Skapa lista över inloggningar som ska migreras som visas i exemplet nedan:  Observera att för närvarande DMS stöder endast migrera SQL-inloggningar. 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>Välj agent-jobb
Skapa lista över agent-jobb som ska migreras som visas i exemplet nedan:

>[!NOTE]
>DMS stöder för närvarande jobb med T-SQL-undersystem jobbet steg.

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>Skapa och starta en migreringen

Använd den `New-AzureRmDataMigrationTask` cmdlet för att skapa och starta en migreringen. Cmdlet: en kräver följande parametrar:
- *TaskType*. Typ av migreringen för att skapa för SQL Server till Azure SQL Database Managed Instance Migreringstyp *MigrateSqlServerSqlDbMi* förväntas. 
- *Namn på resursgrupp*. Namnet på Azure-resursgrupp där du vill skapa uppgiften.
- *ServiceName*. Azure Database Migration Service-instans där du vill skapa uppgiften.
- *ProjectName*. Namnet på Azure Database Migration Service-projekt där du vill skapa uppgiften. 
- *Aktivitetsnamn*. Namnet på aktiviteten som ska skapas. 
- *SourceConnection*. AzureRmDmsConnInfo-objekt som representerar källan SQL Server-anslutning.
- *TargetConnection*. AzureRmDmsConnInfo-objekt som representerar target Azure SQL Database Managed Instance anslutning.
- *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt för att ansluta till källservern.
- *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt för att ansluta till målservern.
- *SelectedDatabase*. AzureRmDataMigrationSelectedDB-objekt som representerar källan och målet databasen mappningen.
- *BackupFileShare*. Filresurs-objekt som representerar den lokala nätverksresurs som Azure Database Migration Service kan ta källan säkerhetskopiorna av databasen på.
- *BackupBlobSasUri*. SAS-URI som ger tillgång till den lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna Azure Database Migration Service. Lär dig mer om att hämta SAS-URI för blob-behållare.
- *SelectedLogins*. Lista över valda inloggningar att migrera.
- *SelectedAgentJobs*. Lista över valda agent-jobb att migrera.

I följande exempel skapar och startar en myDMSTask för av migreringsaktiviteten:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

## <a name="monitor-the-migration"></a>Övervaka migreringen
Du kan övervaka migreringen som körs genom att fråga egenskapen state för uppgiften som du ser i följande exempel:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>Tar bort DMS-instans
När migreringen är klar, kan du ta bort Azure DMS-instans:

```powershell
Remove-AzureRmDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>Nästa steg
- Granska migreringsvägledningen i Microsofts [Guide för Databasmigrering](https://datamigration.microsoft.com/).
