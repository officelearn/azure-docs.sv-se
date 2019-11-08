---
title: Importera en BACPAC-fil för att skapa en databas
description: Skapa en newAzure SQL-databas genom att importera en BACPAC-fil.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: b6b2366a10f38c1938e5ecc09f77271db04dcbc4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810496"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Snabb start: importera en BACPAC-fil till en databas i Azure SQL Database

Du kan importera en SQL Server-databas till en databas i Azure SQL Database med hjälp av en [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -fil. Du kan importera data från en `BACPAC`-fil som lagras i Azure Blob Storage (endast standard lagring) eller från lokal lagring på en lokal plats. För att maximera import hastigheten genom att tillhandahålla mer och snabbare resurser kan du skala databasen till en högre tjänst nivå och beräknings storlek under import processen. Du kan sedan skala ned när importen har slutförts.

> [!NOTE]
> Den importerade databasens kompatibilitetsnivå baseras på käll databasens kompatibilitetsnivå.
> [!IMPORTANT]
> När du har importerat databasen kan du välja att köra databasen på dess aktuella kompatibilitetsnivå (nivå 100 för AdventureWorks2008R2-databasen) eller på en högre nivå. Mer information om effekterna av och alternativ för att köra en databas på en specifik kompatibilitetsnivå finns i [Ändra databasens kompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). I [Ändra konfiguration av databasomfång](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) finns även information om ytterligare databasnivåinställningar som rör kompatibilitetsnivåer.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Importera från en BACPAC-fil i Azure Portal

[Azure Portal](https://portal.azure.com) har *bara* stöd för att skapa en enda databas i Azure SQL Database och *endast* från en BACPAC-fil som lagras i Azure Blob Storage.

Det finns för närvarande inte stöd för att migrera en databas till en [hanterad instans](sql-database-managed-instance.md) från en BACPAC-fil med hjälp av Azure PowerShell. Använd SQL Server Management Studio eller SQLPackage i stället.

> [!NOTE]
> Datorer som bearbetar import/export-begäranden som skickas via Azure Portal eller PowerShell måste lagra BACPAC-filen och temporära filer som genereras av DacFX (data-Tier Application Framework). Det disk utrymme som krävs varierar kraftigt mellan databaser med samma storlek och kan kräva disk utrymme upp till 3 gånger databasens storlek. Datorer som kör import/export-begäran har bara 450GB lokalt disk utrymme. Därför kan vissa förfrågningar Miss lyckas med fel `There is not enough space on the disk`. I det här fallet är lösningen att köra sqlpackage. exe på en dator med tillräckligt lokalt disk utrymme. Vi rekommenderar att du använder [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) för att importera/exportera databaser som är större än 150 GB för att undvika det här problemet.
 
1. Om du vill importera från en BACPAC-fil till en ny databas med hjälp av Azure Portal öppnar du lämplig databas Server sida och väljer sedan **Importera databas**i verktygsfältet.  

   ![Databas import1](./media/sql-database-import/import1.png)

2. Välj lagrings kontot och behållaren för BACPAC-filen och välj sedan den BACPAC-fil som du vill importera från.
3. Ange den nya databas storleken (vanligt vis samma som ursprung) och ange mål SQL Server autentiseringsuppgifter. En lista över möjliga värden för en ny Azure SQL-databas finns i [skapa databas](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Databas import2](./media/sql-database-import/import2.png)

4. Klicka på **OK**.

5. Om du vill övervaka en import status öppnar du sidan Server för databasen och väljer **import/export-historik**under **Inställningar**. När det är klart har importen statusen **slutförd** .

   ![Import status för databas](./media/sql-database-import/import-status.png)

6. Kontrol lera att databasen är aktiv på databas servern genom att välja **SQL-databaser** och kontrol lera att den nya databasen är **online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importera från en BACPAC-fil med hjälp av SqlPackage

Om du vill importera en SQL Server-databas med hjälp av kommando rads verktyget [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , se [import parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage har senaste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Du kan också hämta de senaste [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) från Microsoft Download Center.

För skalning och prestanda rekommenderar vi att du använder SqlPackage i de flesta produktions miljöer snarare än att använda Azure Portal. En SQL Server kund expert team blogg om hur du migrerar med hjälp av `BACPAC` filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Följande SqlPackage-kommando importerar **AdventureWorks2008R2** -databasen från lokal lagring till en Azure SQL Database Server som kallas **mynewserver20170403**. Den skapar en ny databas med namnet **myMigratedDatabase** med en **Premium** service-nivå och ett **P6** -tjänst mål. Ändra värdena efter behov för din miljö.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Om du vill ansluta till en SQL Database Server som hanterar en enskild databas från bakom en företags brand vägg måste brand väggen ha port 1433 öppen. Om du vill ansluta till en hanterad instans måste du ha en [punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md) eller en Express Route-anslutning.
>

Det här exemplet visar hur du importerar en databas med hjälp av SqlPackage med Active Directory Universal Authentication.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importera till en enda databas från en BACPAC-fil med hjälp av PowerShell

> [!NOTE]
> [En hanterad instans](sql-database-managed-instance.md) stöder för närvarande inte migrering av en databas till en instans databas från en BACPAC-fil med hjälp av Azure PowerShell. Använd SQL Server Management Studio eller SQLPackage om du vill importera till en hanterad instans.

> [!NOTE]
> De datorer som bearbetar import/export-begäranden som skickas via portalen eller PowerShell måste lagra BACPAC-filen och temporära filer som genereras av DacFX (data Tier Application Framework). Det disk utrymme som krävs varierar kraftigt mellan databaser med samma storlek och kan ta upp till tre gånger från databasens storlek. Datorer som kör import/export-begäran har bara 450GB lokalt disk utrymme. Som ett resultat kan vissa förfrågningar Miss lyckas med fel meddelandet "det finns inte tillräckligt med disk utrymme på disken". I det här fallet är lösningen att köra sqlpackage. exe på en dator med tillräckligt lokalt disk utrymme. När du importerar/exporterar databaser som är större än 150 GB använder du [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) för att undvika det här problemet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Använd cmdleten [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) för att skicka en begäran om att importera databas till tjänsten Azure SQL Database. Det kan ta en stund att slutföra importen, beroende på databasens storlek.

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

 Du kan använda cmdleten [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) för att kontrol lera importens förlopp. Att köra cmdleten direkt efter att begäran vanligt vis returnerar **status: pågår**. Importen är klar när du ser **status: lyckades**.

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
> Ett annat skript exempel finns i [Importera en databas från en BACPAC-fil](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Begränsningar

Import till en databas i elastisk pool stöds inte. Du kan importera data till en enda databas och sedan flytta databasen till en elastisk pool.

## <a name="import-using-wizards"></a>Importera med hjälp av guider

Du kan också använda de här guiderna.

- [Guiden Importera data nivå program i SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server import-och export guiden](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Nästa steg

- Information om hur du ansluter till och frågar en importerad SQL Database finns i [snabb start: Azure SQL Database: använd SQL Server Management Studio för att ansluta och fråga efter data](sql-database-connect-query-ssms.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (på engelska).
- En beskrivning av hela processen för migrering av SQL Server databasen, inklusive prestanda rekommendationer, finns i [SQL Server Database migration till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om hur du hanterar och delar lagrings nycklar och signaturer för delad åtkomst på ett säkert sätt finns i [Azure Storage Security guide](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
