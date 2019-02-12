---
title: Importera en BACPAC-fil för att skapa en Azure SQL database | Microsoft Docs
description: Skapa en newAzure SQL-databas genom att importera en BACPAC-fil.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 7ea1de4719d171605a49727d6924cb4b617b04bc
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097871"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>Snabbstart: Importera en BACPAC-fil till en ny Azure SQL Database

Du kan migrera en SQL Server-databas till en Azure SQL database med en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil (en zip-fil med en `.bacpac` tillägg som har en databas metadata och data). Du kan importera en BACPAC-fil från Azure Blob storage (endast standard storage) eller från lokal lagring på en lokal plats. Du kan ange en högre tjänstnivå och beräkna storleken (till exempel P6) för att maximera import hastighet. Du kan sedan skala när importen har slutförts. Den importerade databasen kompatibilitetsnivå baseras på källan databaskompatibilitetsnivå.

> [!IMPORTANT]
> När du har importerat din databas, kan du välja att köra databasen på den aktuella kompatibilitetsnivån (nivå 100 för AdventureWorks2008R2 databasen) eller på en högre nivå. Mer information om effekterna av och alternativ för att köra en databas på en specifik kompatibilitetsnivå finns i [Ändra databasens kompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). I [Ändra konfiguration av databasomfång](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) finns även information om ytterligare databasnivåinställningar som rör kompatibilitetsnivåer.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importera från en BACPAC-fil i Azure portal

Det här avsnittet visas hur i den [Azure-portalen](https://portal.azure.com), för att skapa en Azure SQL database från en BACPAC-fil som lagras i Azure Blob storage. Portalen *endast* stöder importera en BACPAC-fil från Azure Blob storage.

> [!NOTE]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md) stöder import från en BACPAC-fil med hjälp av de andra metoderna i den här artikeln, men stöder för närvarande inte migrering i Azure-portalen.

Du importerar en databas i Azure portal genom att öppna sidan för SQL Database-server som är värdar för import och, i verktygsfältet och välj **Importera databas**.  

   ![databasimport](./media/sql-database-import/import.png)

Välj lagringskontot, behållaren och BACPAC-fil som du vill importera. Ange den nya databasstorleken (vanligtvis samma som ursprung) och ange målet autentiseringsuppgifter för SQL Server. En lista över möjliga värden för en ny Azure SQL-databas finns i [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

### <a name="monitor-imports-progress"></a>Övervaka förloppet för import's

Övervaka en import pågår kan du öppna den importerade databasen serversidan och under **inställningar**väljer **Import/Export-historik**. När återställningen har genomförts, importen har en **slutförd** status.

Om du vill kontrollera att databasen är aktiv på servern, Välj **SQL-databaser** och kontrollera den nya databasen är **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importera från en BACPAC-fil med hjälp av SqlPackage

Importera en SQL-databas med den [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) kommandoradsverktyget finns i [importera parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage har senast [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Du kan också hämta senast [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) från Microsoft download center.

Vi rekommenderar för skalbarhet och prestanda med hjälp av SqlPackage i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

Följande SqlPackage kommando importerar den **AdventureWorks2008R2** databas från lokal lagring till en Azure SQL Database-server som kallas **mynewserver20170403**. Den skapar en ny databas med namnet **myMigratedDatabase** med en **Premium** tjänstnivå och en **P6** Tjänstmål. Ändra dessa värden som passar din miljö.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> En SQL Database-server lyssnar på port 1433. För att anslutning ska kunna ske till en SQL Database-server inifrån en företagsbrandvägg måste brandväggen ha den här porten öppen.
>

Det här exemplet visar hur du importerar en databas med hjälp av SqlPackage med Active Directory Universal-autentisering.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importera från en BACPAC-fil med hjälp av PowerShell

Använd den [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) cmdlet för att skicka en begäran om importen till Azure SQL Database-tjänsten. Importen kan ta lite tid att slutföra beroende på databasens storlek.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Du kan använda den [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet för att kontrollera den import pågår. Kör cmdleten omedelbart efter att begäran vanligtvis returnerar **Status: InProgress**. Importen är klar när du ser **Status: Lyckades**.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Ett annat skript exempel finns i [importerar en databas från en BACPAC-fil](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Begränsningar

Importera till en databas i elastiska poolen stöds inte. Du kan importera data till en enkel databas och sedan flytta databasen till en elastisk pool.

## <a name="import-using-wizards"></a>Importera med hjälp av guider

Du kan också använda guiderna.

- [Importera Data-tier Application Wizard i SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server-Import och exportguiden](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Nästa steg

- Läs hur du ansluter till och fråga importerade SQL Database i [snabbstarten: Azure SQL Database: Använda SQL Server Management Studio för att ansluta och fråga data](sql-database-connect-query-ssms.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
- En beskrivning av hela SQL Server-databasmigreringsprocessen, inklusive prestandarekommendationer, finns i [migrering av SQL Server-databas till Azure SQL Database](sql-database-single-database-migrate.md).
- Lär dig hur du hantera och dela lagringsnycklar och delad åtkomst signaturer på ett säkert sätt, finns i [säkerhetsguiden för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
