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
ms.date: 03/12/2019
ms.openlocfilehash: 3eaa12b5ba735d1e2aa0e074054328942a3041eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60338613"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Snabbstart: Importera en BACPAC-fil till en databas i Azure SQL Database

Du kan importera en SQL Server-databas till en databas i Azure SQL Database med hjälp av en [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) fil. Du kan importera data från en `BACPAC` -fil som lagras i Azure Blob storage (endast standard storage) eller från lokal lagring på en lokal plats. För att maximera import hastighet genom att tillhandahålla resurser för mer och snabbare, skala din databas till en högre tjänstnivå och beräkna storleken under importen. Du kan sedan skala när importen har slutförts.

> [!NOTE]
> Den importerade databasen kompatibilitetsnivå baseras på källan databaskompatibilitetsnivå.
> [!IMPORTANT]
> När du har importerat din databas, kan du välja att köra databasen på den aktuella kompatibilitetsnivån (nivå 100 för AdventureWorks2008R2 databasen) eller på en högre nivå. Mer information om effekterna av och alternativ för att köra en databas på en specifik kompatibilitetsnivå finns i [Ändra databasens kompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). I [Ändra konfiguration av databasomfång](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) finns även information om ytterligare databasnivåinställningar som rör kompatibilitetsnivåer.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importera från en BACPAC-fil i Azure portal

Den [Azure-portalen](https://portal.azure.com) *endast* kan du skapa en enkel databas i Azure SQL Database och *endast* från en BACPAC-fil som lagras i Azure Blob storage.

> [!NOTE]
> [En hanterad instans](sql-database-managed-instance.md) stöder för närvarande inte migrera en databas till en instans-databas från en BACPAC-fil med hjälp av Azure portal. Om du vill importera till en hanterad instans, använder du SQL Server Management Studio eller SQLPackage.

1. Om du vill importera från en BACPAC-fil till en ny databas med Azure portal, öppnar sidan rätt databas och i verktygsfältet, Välj **Importera databas**.  

   ![Databasen import1](./media/sql-database-import/import1.png)

2. Välj lagringskontot och behållaren för BACPAC-fil och välj sedan BACPAC-fil som ska importeras från.
3. Ange den nya databasstorleken (vanligtvis samma som ursprung) och ange målet autentiseringsuppgifter för SQL Server. En lista över möjliga värden för en ny Azure SQL-databas finns i [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Databasen import2](./media/sql-database-import/import2.png)

4. Klicka på **OK**.

5. Övervaka en import pågår kan du öppna databasens server-sida, och under **inställningar**väljer **Import/Export-historik**. När återställningen har genomförts, importen har en **slutförd** status.

   ![Databasen importstatus](./media/sql-database-import/import-status.png)

6. Om du vill kontrollera att databasen är aktiv på databasservern, Välj **SQL-databaser** och kontrollera den nya databasen är **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importera från en BACPAC-fil med hjälp av SqlPackage

Importera en SQL Server-databas med den [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) kommandoradsverktyget finns i [importera parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage har senast [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Du kan också hämta senast [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) från Microsoft download center.

För skalbarhet och prestanda rekommenderar vi att med hjälp av SqlPackage i de flesta produktionsmiljöer i stället för att använda Azure portal. För en SQL Server Customer Advisory Team-blogg om hur du migrerar med hjälp av `BACPAC` filer, se [migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Vi rekommenderar för skalbarhet och prestanda med hjälp av SqlPackage i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

Följande SqlPackage kommando importerar den **AdventureWorks2008R2** databas från lokal lagring till en Azure SQL Database-server som kallas **mynewserver20170403**. Den skapar en ny databas med namnet **myMigratedDatabase** med en **Premium** tjänstnivå och en **P6** Tjänstmål. Ändra dessa värden som passar din miljö.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Anslut till en SQL Database-server som hanterar en enkel databas bakom en företagsbrandvägg genom måste brandväggen ha port 1433 som är öppna. Om du vill ansluta till en hanterad instans, måste du ha en [punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md) eller en expressroute-anslutning.
>

Det här exemplet visar hur du importerar en databas med hjälp av SqlPackage med Active Directory Universal-autentisering.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importera till en enskild databas från en BACPAC-fil med hjälp av PowerShell

> [!NOTE]
> [En hanterad instans](sql-database-managed-instance.md) stöder för närvarande inte migrera en databas till en instans-databas från en BACPAC-fil med hjälp av Azure PowerShell. Om du vill importera till en hanterad instans, använder du SQL Server Management Studio eller SQLPackage.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

Använd den [New AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) cmdlet för att skicka en begäran om importen till Azure SQL Database-tjänsten. Importen kan ta lite tid att slutföra beroende på databasens storlek.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Du kan använda den [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet för att kontrollera den import pågår. Kör cmdleten omedelbart efter att begäran vanligtvis returnerar **Status: InProgress**. Importen är klar när du ser **Status: Lyckades**.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Ett annat skript exempel finns i [importerar en databas från en BACPAC-fil](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Begränsningar

Importera till en databas i elastiska poolen stöds inte. Du kan importera data till en enkel databas och sedan flytta databasen till en elastisk pool.

## <a name="import-using-wizards"></a>Importera med hjälp av guider

Du kan också använda guiderna.

- [Importera Data-tier Application Wizard i SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server-Import och exportguiden](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Nästa steg

- Läs hur du ansluter till och fråga importerade SQL Database i [snabbstarten: Azure SQL Database: Använda SQL Server Management Studio för att ansluta och fråga data](sql-database-connect-query-ssms.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (på engelska).
- En beskrivning av hela SQL Server-databasmigreringsprocessen, inklusive prestandarekommendationer, finns i [migrering av SQL Server-databas till Azure SQL Database](sql-database-single-database-migrate.md).
- Lär dig hur du hantera och dela lagringsnycklar och delad åtkomst signaturer på ett säkert sätt, finns i [säkerhetsguiden för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
