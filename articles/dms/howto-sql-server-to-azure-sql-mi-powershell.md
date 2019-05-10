---
title: Migrera SQLServer till Azure SQL Database Managed Instance med Database Migration Service och PowerShell | Microsoft Docs
description: Lär dig hur du migrerar från en lokal SQL Server till Azure SQL DB Managed Instance med Azure PowerShell.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: d83410efd26f8c2078d3abdb01d061db0b83d33d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233735"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Migrera lokala SQL Server till en Azure SQL Database managed instance med Azure PowerShell
I den här artikeln får du migrera den **Adventureworks2016** databasen återställas till en lokal instans av SQL Server 2005 eller senare till en Azure SQL Database-hanterad instans med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en lokal SQL Server-instans till en hanterad Azure SQL Database-instans med hjälp av den `Az.DataMigration` modul i Microsoft Azure PowerShell.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Skapa en resursgrupp.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt i en instans av Azure Database Migration Service.
> * Köra migreringen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln innehåller information om hur du utför både online och offline-migrering.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att slutföra dessa steg:

* [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (alla versioner).
* En lokal kopia av den **AdventureWorks2016** databas som är tillgänglig för nedladdning [här](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Att aktivera TCP/IP-protokollet som är inaktiverad som standard med SQL Server Express-installationen. Aktivera TCP/IP-protokollet genom att följa artikeln [aktivera eller inaktivera ett Server-nätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Så här konfigurerar du din [Windows brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En Azure SQL Database-hanterad instans. Du kan skapa en hanterad Azure SQL Database-instans genom att följa detaljerat i artikeln [skapa en hanterad Azure SQL Database-instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 eller senare.
* Azure Virtual Network (VNet) skapas med hjälp av Azure Resource Manager-distributionsmodellen, som ger Azure Database Migration Service med plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* En slutförda utvärderingar av en lokal databas och schema migreringen med Data Migration Assistant, enligt beskrivningen i artikeln [utför en SQL Server-migreringsutvärdering](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Ladda ned och installera den `Az.DataMigration` modulen (version 0.7.2 eller senare) från PowerShell-galleriet med hjälp av [Install-Module PowerShell-cmdleten](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Att säkerställa att de autentiseringsuppgifter som används för att ansluta till SQL Server-instans har den [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) behörighet.
* För att säkerställa att de autentiseringsuppgifter som används för att ansluta till målet för Azure SQL Database har hanterad instans KONTROLLDATABAS-behörighet på måldatabaserna Azure SQL Database-hanterad instans.

    > [!IMPORTANT]
    > För migrering av online, måste du redan har ställt in dina Azure Active Directory-autentiseringsuppgifter. Mer information finns i artikeln [använda portalen för att skapa en Azure AD-program och tjänstens huvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Logga in på Microsoft Azure-prenumerationen

Logga in på Azure-prenumerationen med hjälp av PowerShell. Mer information finns i artikeln [logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk behållare där Azure resurser distribueras och hanteras.

Skapa en resursgrupp med hjälp av den [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) kommando.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *USA, östra* region.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

Du kan skapa ny instans av Azure Database Migration Service med hjälp av den `New-AzDataMigrationService` cmdlet.
Den här cmdleten förväntar sig att följande obligatoriska parametrar:

* *Namn på Azure-resursgrupp*. Du kan använda [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) kommando för att skapa en Azure-resursgrupp som tidigare visas och ange dess namn som en parameter.
* *Tjänstnamnet*. Sträng som motsvarar namnet på den önskade unika tjänst för Azure Database Migration Service.
* *Plats*. Anger platsen för tjänsten. Ange en plats för Azure data center, till exempel USA, västra och Sydostasien.
* *Sku*. Den här parametern motsvarar DMS Sku-namnet. För närvarande stöds Sku-namn är *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Virtuella undernät-ID*. Du kan använda cmdleten [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) att skapa ett undernät.

I följande exempel skapas en tjänst med namnet *MyDMS* i resursgruppen *MyDMSResourceGroup* finns i den *USA, östra* region med hjälp av ett virtuellt nätverk med namnet  *MyVNET* och ett undernät med namnet *MySubnet*.

> [!IMPORTANT]
> Kodfragmentet nedan avser en offline-migrering, som inte kräver en instans av Azure Database Migration Service baserat på en Premium-SKU. Värdet för parametern - Sku måste innehålla en Premium-SKU för en online-migrering.

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

När du har skapat en Azure Database Migration Service-instans, skapar du ett migreringsprojekt. Ett projekt med Azure Database Migration Service kräver anslutningsinformationen för både källa och mål-instanser, samt en lista över databaser som du vill migrera som en del av projektet.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Skapa ett Database Connection-Info-objekt för källa och mål-anslutningar

Du kan skapa en databas Connection-Info-objekt med hjälp av den `New-AzDmsConnInfo` cmdleten, som förväntar sig att följande parametrar:

* *ServerType*. Typ av databasanslutning begärts, till exempel SQL, Oracle eller MySQL. Använda SQL för SQLServer och Azure SQL.
* *DataSource*. Namn eller IP-Adressen för en instans av SQL Server eller Azure SQL-databasinstans.
* *AuthType*. Autentiseringstyp för anslutning, som kan vara antingen SqlAuthentication eller WindowsAuthentication.
* *TrustServerCertificate*. Den här parametern anger ett värde som anger om kanalen är krypterad när kringgår walking certifikatkedjan för att verifiera förtroendet. Värdet kan vara `$true` eller `$false`.

I följande exempel skapas ett objekt för anslutningsinformation för en källa som SQL Server kallas *MySourceSQLServer* med sql-autentisering:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

I nästa exempel visas skapandet av anslutningsinformation för en Azure SQL Database hanterad instans-server med namnet 'targetmanagedinstance.database.windows.net' med sql-autentisering:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Ange databaser för migreringsprojektet

Skapa en lista över `AzDataMigrationDatabaseInfo` objekt som anger databaser som en del av Azure Database Migration Service-projekt som kan anges som parameter för att skapa projektet. Du kan använda cmdleten `New-AzDataMigrationDatabaseInfo` att skapa `AzDataMigrationDatabaseInfo`.

I följande exempel skapas den `AzDataMigrationDatabaseInfo` projektet för den **AdventureWorks2016** databasen och lägger till den i listan anges som parameter för att skapa projekt.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Skapa ett projektobjekt

Slutligen kan du skapa en Azure Database Migration Service-projekt med namnet *MyDMSProject* i *USA, östra* med `New-AzDataMigrationProject` och lägga till de tidigare skapade källa och mål-anslutningarna och lista över databaser som ska migreras.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
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

Därefter skapa och starta en Azure Database Migration Service-uppgift. Den här uppgiften kräver autentiseringsuppgifter anslutningsinformationen för både källa och mål, samt listan över databastabeller som ska migreras och den information som redan tillhandahålls med projekt som skapas som ett krav.

### <a name="create-credential-parameters-for-source-and-target"></a>Skapa autentiseringsuppgifter parametrar för källa och mål

Skapa anslutning säkerhetsreferenser när en [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt.

I följande exempel visar *PSCredential* objekt för både källa och mål-anslutningar, vilket ger lösenord som strängvariabler *$sourcePassword* och *$ Mållösenord*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Skapa en säkerhetskopiering filresurs-objekt

Nu skapa en filresurs-objekt som representerar den lokala SMB-nätverksresurs som Azure Database Migration Service kan göra källan säkerhetskopior av databasen med hjälp av den `New-AzDmsFileShare` cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Skapa markerat databasobjekt

Nästa steg är att väljer du databaserna som källa och mål med hjälp av den `New-AzDmsSelectedDB` cmdlet.

I följande exempel är för att migrera en databas från SQL Server till en Azure SQL Database managed instance:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Om en hela SQL Server-instansen måste ha en lift and shift till en Azure SQL Database-hanterad instans och sedan en loop för att ta alla databaser från källan finns nedan. I följande exempel för $Server och $SourceUserName $SourcePassword, anger du källan SQL Server-information.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>SAS-URI för Azure Storage-behållare

Skapa variabeln som innehåller SAS-URI som ger tillgång till den lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna Azure Database Migration Service.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Ytterligare konfigurationskrav

Det finns några ytterligare krav som behöver åtgärdas, men de skiljer sig åt beroende på om du utför en migrering offline eller online.

#### <a name="offline-migrations"></a>Offline-migrering

För offline migreringar, utför du följande ytterligare konfigurationsåtgärder.

* **Välj inloggningar**. Skapa en lista över inloggningar som ska migreras som visas i följande exempel:

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > Azure Database Migration Service stöder för närvarande endast migrera SQL-inloggningar.

* **Välj agentjobb**. Skapa lista över jobb som ska migreras som visas i följande exempel:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Azure Database Migration Service stöder för närvarande endast jobb med T-SQL-undersystem jobbet steg.

#### <a name="online-migrations"></a>Online migreringar

För online migreringar, utför du följande ytterligare konfigurationsåtgärder.

* **Konfigurera Azure Active Directory-App**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Konfigurera lagringsresurs**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Skapa och starta migreringen

Använd den `New-AzDataMigrationTask` cmdlet för att skapa och starta en migreringen.

#### <a name="specify-parameters"></a>Ange parametrar

##### <a name="common-parameters"></a>Gemensamma parametrar

Oavsett om du utför en migrering offline eller online, den `New-AzDataMigrationTask` cmdleten förväntar sig att följande parametrar:

* *TaskType*. Typ av migreringen för att skapa för SQL Server till Azure SQL Database Managed Instance Migreringstyp *MigrateSqlServerSqlDbMi* förväntas. 
* *Namn på resursgrupp*. Namnet på Azure-resursgrupp där du vill skapa uppgiften.
* *ServiceName*. Azure Database Migration Service-instans där du vill skapa uppgiften.
* *ProjectName*. Namnet på Azure Database Migration Service-projekt där du vill skapa uppgiften. 
* *Aktivitetsnamn*. Namnet på aktiviteten som ska skapas. 
* *SourceConnection*. AzDmsConnInfo-objekt som representerar källan SQL Server-anslutning.
* *TargetConnection*. AzDmsConnInfo-objekt som representerar target Azure SQL Database Managed Instance anslutning.
* *SourceCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt för att ansluta till källservern.
* *TargetCred*. [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) objekt för att ansluta till målservern.
* *SelectedDatabase*. AzDataMigrationSelectedDB-objekt som representerar källan och målet databasen mappningen.
* *BackupFileShare*. Filresurs-objekt som representerar den lokala nätverksresurs som Azure Database Migration Service kan ta källan säkerhetskopiorna av databasen på.
* *BackupBlobSasUri*. SAS-URI som ger tillgång till den lagringskontobehållare som tjänsten Överför säkerhetskopieringsfilerna Azure Database Migration Service. Lär dig mer om att hämta SAS-URI för blob-behållare.
* *SelectedLogins*. Lista över valda inloggningar att migrera.
* *SelectedAgentJobs*. Lista över valda agent-jobb att migrera.

##### <a name="additional-parameters"></a>Ytterligare parametrar

Den `New-AzDataMigrationTask` cmdlet även förväntar sig parametrar som är unika för den typ av migrering, online eller offline, som du utför.

* **Offline migreringar**. För offline-migrering, den `New-AzDataMigrationTask` cmdlet: en också kräver följande parametrar:

  * *SelectedLogins*. Lista över valda inloggningar att migrera.
  * *SelectedAgentJobs*. Lista över valda agent-jobb att migrera.

* **Online migreringar**. För online migreringar den `New-AzDataMigrationTask` cmdlet: en också kräver följande parametrar.

* *AzureActiveDirectoryApp*. Active Directory-program.
* *StorageResourceID*. Resurs-ID för Storage-konto.

#### <a name="create-and-start-an-offline-migration-task"></a>Skapa och starta en migrering offline-uppgift

I följande exempel skapar och startar en offline-migreringsaktiviteten **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Skapa och starta en onlinemigrering uppgift

I följande exempel skapar och startar en onlinemigrering aktiviteten **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Övervaka migreringen

Du kan övervaka migreringen genom att utföra följande uppgifter.

1. Samla ihop alla migreringsinformation i en variabel med namnet $CheckTask.

    Om du vill kombinera information om migrering, till exempel egenskaper, status och information om databas som är associerade med migreringen, använder du följande kodavsnitt:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Använd den `$CheckTask` variabeln för att hämta det aktuella tillståndet för migreringen.

    Du använder den `$CheckTask` variabeln för att hämta det aktuella tillståndet för migreringen, kan du övervaka migreringen som körs genom att fråga egenskapen state för aktiviteten, som visas i följande exempel:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Genomför startpunkt (online migreringarna endast)

Med en online-migrering, utförs en fullständig säkerhetskopiering och återställning av databaser och sedan arbetet fortsätter om hur du återställer transaktionsloggarna som lagras i BackupFileShare.

När databasen i en hanterad Azure SQL Database-instans uppdateras med senaste data och är synkroniserad med källdatabasen, kan du utföra en snabb lösning.

I följande exempel slutförs cutover\migration. Användare anropa det här kommandot på deras eget gottfinnande.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Tar bort instansen av Azure Database Migration Service

När migreringen är klar, kan du ta bort Azure Database Migration Service-instans:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Ytterligare resurser

Information om ytterligare migrera scenarier (källa/mål-par) finns i Microsofts [Guide för Databasmigrering](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Azure Database Migration Service i artikeln [vad är Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
