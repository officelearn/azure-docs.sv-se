---
title: 'PowerShell: Migrera SQL Server till SQL-hanterad instans'
titleSuffix: Azure Database Migration Service
description: Lär dig att migrera från lokal SQL Server till Azure SQL Database hanterad instans med hjälp av Azure PowerShell och Azure Database Migration Service.
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
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650732"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>Migrera SQL Server till SQL Database-hanterad instans med PowerShell & Azure Database Migration Service

I den här artikeln migrerar du **Adventureworks2016-databasen** som återställs till en lokal instans av SQL Server 2005 eller senare till en Azure SQL Database-hanterad instans med hjälp av Microsoft Azure PowerShell. Du kan migrera databaser från en lokal SQL Server-instans till en `Az.DataMigration` Azure SQL Database-hanterad instans med hjälp av modulen i Microsoft Azure PowerShell.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
>
> * Skapa en resursgrupp.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt i en instans av Azure Database Migration Service.
> * Köra migreringen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln innehåller information om hur du utför både online- och offlinemigreringar.

## <a name="prerequisites"></a>Krav

För att kunna utföra de här stegen behöver du:

* [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (valfri utgåva).
* En lokal kopia av **AdventureWorks2016** databas, som finns att ladda ner [här](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Så här aktiverar du TCP/IP-protokollet, som är inaktiverat som standard med SQL Server Express-installation. Aktivera TCP/IP-protokollet genom att följa artikeln [Aktivera eller inaktivera ett servernätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Så här konfigurerar du [Windows-brandväggen för åtkomst till databasmotor](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En hanterad Azure SQL-databas-hanterad instans. Du kan skapa en Hanterad Azure SQL-databas-instans genom att följa detaljerna i artikeln [Skapa en hanterad Azure SQL-databas-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Så här hämtar och installerar du [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 eller senare.
* Ett Microsoft Azure Virtual Network som skapats med hjälp av distributionsmodellen för Azure Resource Manager, som ger Azure Database Migration Service anslutning till dina lokala källservrar med hjälp av [antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* En slutförd utvärdering av din lokala databas och schemamigrering med hjälp av DataMigreringsassistenten, enligt beskrivningen i artikeln [Utför en SQL Server-migreringsutvärdering](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Så här hämtar `Az.DataMigration` och installerar du modulen (version 0.7.2 eller senare) från PowerShell-galleriet med hjälp av [PowerShell-cmdlet för installationsmodulen](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Så här kontrollerar du att de autentiseringsuppgifter som används för att ansluta till källan SQL Server-instans har [behörigheten CONTROL SERVER.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* För att säkerställa att de autentiseringsuppgifter som används för att ansluta till azure SQL Database hanterad instans har behörigheten CONTROL DATABASE för målet Azure SQL Database hanterade instansdatabaser.

    > [!IMPORTANT]
    > För onlinemigreringar måste du redan ha konfigurerat dina Azure Active Directory-autentiseringsuppgifter. Mer information finns i artikeln [Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Logga in på din Microsoft Azure-prenumeration

Logga in på din Azure-prenumeration med PowerShell. Mer information finns i artikeln [Logga in med Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) med kommandot.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i regionen *Östra USA.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

Du kan skapa en ny instans av `New-AzDataMigrationService` Azure Database Migration Service med hjälp av cmdlet.
Denna cmdlet förväntar sig följande nödvändiga parametrar:

* *Namn på Azure Resource Group*. Du kan [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) använda kommandot för att skapa en Azure Resource-grupp som tidigare visats och ange dess namn som en parameter.
* *Tjänstnamn*. Sträng som motsvarar önskat unikt tjänstnamn för Azure Database Migration Service.
* *Plats*. Anger tjänstens plats. Ange en Azure-datacenterplats, till exempel Västra USA eller Sydostasien.
* *Sku*. Den här parametern motsvarar DMS Sku-namnet. Sku-namn som stöds *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identifierare för virtuellt undernät*. Du kan använda cmdleten [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) för att skapa ett undernät.

I följande exempel skapas en tjänst med namnet *MyDMS* i resursgruppen *MyDMSResourceGroup* i regionen *Östra USA* med hjälp av ett virtuellt nätverk med namnet *MyVNET* och ett undernät med namnet *MySubnet*.

> [!IMPORTANT]
> Kodavsnittet nedan är för en offlinemigrering, som inte kräver en instans av Azure Database Migration Service baserat på en Premium SKU. För en onlinemigrering måste värdet för parametern -Sku innehålla en Premium SKU.

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

Du kan skapa ett databasanslutningsinformationsobjekt med hjälp av `New-AzDmsConnInfo` cmdleten, som förväntar sig följande parametrar:

* *ServerType*. Den typ av databasanslutning som begärs, till exempel SQL, Oracle eller MySQL. Använd SQL för SQL Server och Azure SQL.
* *DataSource*. Namnet eller IP-adressen för en SQL Server-instans eller Azure SQL Database-instans.
* *AuthType*. Autentiseringstypen för anslutning, som kan vara antingen SqlAuthentication eller WindowsAuthentication.
* *TrustServerCertificate*. Den här parametern anger ett värde som anger om kanalen är krypterad när du kringgår att gå i certifikatkedjan för att validera förtroendet. Värdet kan `$true` vara `$false`eller .

I följande exempel skapas ett Connection Info-objekt för en källa SQL Server som kallas *MySourceSQLServer* med SQL-autentisering:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

I nästa exempel visas skapandet av anslutningsinformation för en Hanterad instansserver för Azure SQL Database med namnet "targetmanagedinstance.database.windows.net" med sql-autentisering:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Tillhandahålla databaser för migreringsprojektet

Skapa en `AzDataMigrationDatabaseInfo` lista över objekt som anger databaser som en del av Azure Database Migration Service-projektet, som kan tillhandahållas som parameter för att skapa projektet. Du kan använda `New-AzDataMigrationDatabaseInfo` cmdlet `AzDataMigrationDatabaseInfo`för att skapa .

I följande exempel `AzDataMigrationDatabaseInfo` skapas projektet för **AdventureWorks2016-databasen** och det läggs till i listan som ska anges som parameter för att skapa projekt.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Skapa ett projektobjekt

Slutligen kan du skapa ett Azure Database Migration Service-projekt som heter `New-AzDataMigrationProject` *MyDMSProject* som finns i östra *USA* med och lägga till tidigare skapade käll- och målanslutningar och listan över databaser som ska migreras.

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

## <a name="create-and-start-a-migration-task"></a>Skapa och starta en migreringsuppgift

Skapa och starta sedan en Azure Database Migration Service-uppgift. Den här aktiviteten kräver information om anslutningsautentiseringsuppgifter för både källan och målet, samt listan över databastabeller som ska migreras och den information som redan medföljer projektet som skapats som en förutsättning.

### <a name="create-credential-parameters-for-source-and-target"></a>Skapa referensparametrar för källa och mål

Skapa autentiseringsuppgifter för anslutningssäkerhet som ett [PSCredential-objekt.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

I följande exempel visas skapandet av *PSCredential-objekt* för både käll- och målanslutningar, vilket ger lösenord som strängvariabler *$sourcePassword* och *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Skapa ett säkerhetskopiera FileShare-objekt

Nu skapa ett FileShare-objekt som representerar den lokala SMB-nätverksresursen som `New-AzDmsFileShare` Azure Database Migration Service kan ta källdatabasen säkerhetskopior med hjälp av cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Skapa markerat databasobjekt

Nästa steg är att välja käll- och `New-AzDmsSelectedDB` måldatabaser med hjälp av cmdleten.

Följande exempel är att migrera en enskild databas från SQL Server till en hanterad Azure SQL-databas-hanterad instans:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Om en hel SQL Server-instans behöver en lift-and-shift till en Azure SQL Database hanterad instans, finns en loop för att ta alla databaser från källan nedan. I följande exempel, för $Server, $SourceUserName och $SourcePassword, ange din källa SQL Server-information.

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

### <a name="sas-uri-for-azure-storage-container"></a>SAS URI för Azure Storage-behållare

Skapa variabel som innehåller SAS URI som ger Azure Database Migration Service åtkomst till lagringskontobehållaren som tjänsten överför säkerhetskopieringsfilerna till.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Ytterligare konfigurationskrav

Det finns några ytterligare krav som du måste ta itu med, men de skiljer sig åt beroende på om du utför en offline- eller onlinemigrering.

#### <a name="offline-migrations"></a>Offlinemigreringar

Endast för offlinemigreringar utför du följande ytterligare konfigurationsuppgifter.

* **Välj inloggningar**. Skapa en lista över inloggningar som ska migreras enligt följande exempel:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > För närvarande stöder Azure Database Migration Service endast migreraNDE SQL-inloggningar.

* **Välj agentjobb**. Skapa lista över agentjobb som ska migreras enligt följande exempel:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > För närvarande stöder Azure Database Migration Service endast jobb med T-SQL-jobbsteg.

#### <a name="online-migrations"></a>Online migreringar

Endast för onlinemigreringar utför du följande ytterligare konfigurationsuppgifter.

* **Konfigurera Azure Active Directory-app**

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

### <a name="create-and-start-the-migration-task"></a>Skapa och starta migreringsuppgiften

Använd `New-AzDataMigrationTask` cmdlet för att skapa och starta en migreringsuppgift.

#### <a name="specify-parameters"></a>Ange parametrar

##### <a name="common-parameters"></a>Vanliga parametrar

Oavsett om du utför en offline- eller `New-AzDataMigrationTask` onlinemigrering förväntar sig cmdlet följande parametrar:

* *TaskType*. Typ av migreringsuppgift som ska skapas för SQL Server till Azure SQL Database Managed Instance migrationstyp *MigrateSqlServerSqlDbMi* förväntas. 
* *Namn på resursgrupp*. Namn på Azure-resursgrupp där aktiviteten ska skapas.
* *ServiceName*. Azure Database Migration Service-instans där uppgiften ska skapas.
* *ProjectName*. Namn på Azure Database Migration Service-projektet där aktiviteten ska skapas. 
* *TaskName*. Namn på den uppgift som ska skapas. 
* *SourceConnection*. AzDmsConnInfo-objekt som representerar källan SQL Server-anslutning.
* *TargetConnection*. AzDmsConnInfo-objekt som representerar målanslutningen för Azure SQL Database Managed Instance.
* *SourceCred*. [PSCredential-objekt](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) för anslutning till källserver.
* *TargetCred*. [PSCredential-objekt](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) för anslutning till målservern.
* *SelectedDatabase*. AzDataMigrationSelectedDB-objekt som representerar käll- och måldatabasmappningen.
* *BackupFileShare*. FileShare-objekt som representerar den lokala nätverksresurs som Azure Database Migration Service kan ta källdatabasens säkerhetskopior till.
* *BackupBlobSasUri*. SAS-URI:n som ger Azure Database Migration Service åtkomst till behållaren för lagringskonto som tjänsten överför säkerhetskopieringsfilerna till. Lär dig hur du hämtar SAS-URI för blobbcontainer.
* *SelectedLogins*. Lista över valda inloggningar som ska migreras.
* *SelectedAgentJobs*. Lista över valda agentjobb som ska migreras.

##### <a name="additional-parameters"></a>Ytterligare parametrar

Cmdlet `New-AzDataMigrationTask` förväntar sig också parametrar som är unika för den typ av migrering, offline eller online, som du utför.

* **Offlinemigreringar**. För offlinemigreringar `New-AzDataMigrationTask` förväntar sig cmdlet också följande parametrar:

  * *SelectedLogins*. Lista över valda inloggningar som ska migreras.
  * *SelectedAgentJobs*. Lista över valda agentjobb som ska migreras.

* **Online migreringar**. För onlinemigreringar `New-AzDataMigrationTask` förväntar sig cmdlet också följande parametrar.

* *AzureActiveDirectoryApp*. Active Directory-program.
* *StorageResourceID*. Resurs-ID för lagringskonto.

#### <a name="create-and-start-an-offline-migration-task"></a>Skapa och starta en offlinemigreringsuppgift

I följande exempel skapas och startas en offlinemigreringsuppgift med namnet **myDMSTask:**

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

#### <a name="create-and-start-an-online-migration-task"></a>Skapa och starta en onlinemigreringsuppgift

I följande exempel skapas och startas en onlinemigreringsuppgift med namnet **myDMSTask:**

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

Om du vill övervaka migreringen utför du följande uppgifter.

1. Konsolidera alla migreringsinformation i en variabel som kallas $CheckTask.

    Om du vill kombinera migreringsinformation som egenskaper, tillstånd och databasinformation som är associerad med migreringen använder du följande kodavsnitt:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Använd `$CheckTask` variabeln för att hämta det aktuella tillståndet för migreringsaktiviteten.

    Om du `$CheckTask` vill använda variabeln för att hämta det aktuella tillståndet för migreringsaktiviteten kan du övervaka migreringsaktiviteten som körs genom att fråga aktivitetens tillståndsegenskap, vilket visas i följande exempel:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Utför cutover (endast onlinemigreringar)

Med en onlinemigrering utförs en fullständig säkerhetskopiering och återställning av databaser och sedan fortsätter arbetet med att återställa transaktionsloggarna som lagras i BackupFileShare.

När databasen i en Hanterad Azure SQL-databas-hanterad instans uppdateras med senaste data och är synkroniserad med källdatabasen kan du utföra en rensning.

I följande exempel slutförs cutover\migrationen. Användare anropar det här kommandot efter eget gottfinnande.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Ta bort instansen av Azure Database Migration Service

När migreringen är klar kan du ta bort Azure Database Migration Service-instansen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Ytterligare resurser

Information om ytterligare migrerande scenarier (käll-/målpar finns i Microsoft [Database Migration Guide](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Database Migration Service i artikeln [Vad är Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
