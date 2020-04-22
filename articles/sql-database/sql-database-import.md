---
title: Importera en BACPAC-fil för att skapa en databas
description: Skapa en nyAzure SQL-databas genom att importera en BACPAC-fil.
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
ms.openlocfilehash: 7db3f6f50745526876ef2ca6e3253f1931420f0f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683251"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Snabbstart: Importera en BACPAC-fil till en databas i Azure SQL Database

Du kan importera en SQL Server-databas till en databas i Azure SQL Database med hjälp av en [BACPAC-fil.](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) Du kan importera data `BACPAC` från en fil som lagras i Azure Blob-lagring (endast standardlagring) eller från lokal lagring på en lokal plats. Om du vill maximera importhastigheten genom att tillhandahålla fler och snabbare resurser skalar du databasen till en högre tjänstnivå och beräknar storlek under importprocessen. Du kan sedan skala ned när importen har slutförts.

> [!NOTE]
> Den importerade databasens kompatibilitetsnivå baseras på källdatabasens kompatibilitetsnivå.

> [!IMPORTANT]
> När du har importerat databasen kan du välja att använda databasen på den aktuella kompatibilitetsnivån (nivå 100 för AdventureWorks2008R2-databasen) eller på en högre nivå. Mer information om effekterna av och alternativ för att köra en databas på en specifik kompatibilitetsnivå finns i [Ändra databasens kompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). I [Ändra konfiguration av databasomfång](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) finns även information om ytterligare databasnivåinställningar som rör kompatibilitetsnivåer.

## <a name="using-azure-portal"></a>Använda Azure Portal

Titta på det här videoklippet om du vill se hur du importerar från en BACPAC-fil i Azure-portalen eller fortsätter läsa nedan:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure-portalen](https://portal.azure.com) stöder *bara* att skapa en enda databas i Azure SQL Database och *endast* från en BACPAC-fil som lagras i Azure Blob-lagring.

Att migrera en databas till en [hanterad instans](sql-database-managed-instance.md) från en BACPAC-fil med Azure PowerShell stöds för närvarande inte. Använd SQL Server Management Studio eller SQLPackage i stället.

> [!NOTE]
> Datorer som bearbetar import-/exportbegäranden som skickas via Azure-portalen eller PowerShell måste lagra BACPAC-filen samt temporära filer som genereras av DacFX (Data-Tier Application Framework). Det diskutrymme som krävs varierar avsevärt mellan databaser med samma storlek och kan kräva diskutrymme upp till 3 gånger storleken på databasen. Datorer som kör import-/exportbegäran har bara 450 GB lokalt diskutrymme. Därför kan vissa begäranden misslyckas `There is not enough space on the disk`med felet . I det här fallet är lösningen att köra sqlpackage.exe på en dator med tillräckligt med lokalt diskutrymme. Vi uppmuntrar till att använda SqlPackage för att importera/exportera databaser som är större än 150 GB för att undvika det här problemet.

1. Om du vill importera från en BACPAC-fil till en ny enskild databas med Azure-portalen öppnar du lämplig databasserversida och väljer sedan **Importera databas**i verktygsfältet .  

   ![Databasimport1](./media/sql-database-import/sql-server-import-database.png)

1. Välj lagringskontot och behållaren för BACPAC-filen och välj sedan den BACPAC-fil som du vill importera från.

1. Ange den nya databasstorleken (vanligtvis samma som ursprung) och ange sql server-autentiseringsuppgifterna för målet. En lista över möjliga värden för en ny Azure SQL-databas finns i [Skapa databas](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Import av databas2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Klicka på **OK**.

1. Om du vill övervaka en imports förlopp öppnar du databasens serversida och väljer **importera/exportera historik**under **Inställningar**. När importen har en **slutförd** status har den.

   ![Status för databasimport](./media/sql-database-import/sql-server-import-database-history.png)

1. Om du vill kontrollera att databasen är live på databasservern väljer du **SQL-databaser** och verifierar att den nya databasen är **Online**.

## <a name="using-sqlpackage"></a>Använda SqlPackage

Information om hur du importerar en SQL Server-databas med kommandoradsverktyget [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) finns i [importparametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage har de senaste [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och SQL Server Data Tools för Visual [Studio](https://msdn.microsoft.com/library/mt204009.aspx). Du kan också hämta det senaste [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) från Microsoft Download Center.

För skalning och prestanda rekommenderar vi att du använder SqlPackage i de flesta produktionsmiljöer i stället för att använda Azure-portalen. En blogg för SQL Server Customer Advisory `BACPAC` Team om migrering med filer finns [i migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Följande SqlPackage-kommando importerar **AdventureWorks2008R2-databasen** från lokal lagring till en Azure SQL Database-server som kallas **mynewserver20170403**. Det skapar en ny databas som kallas **myMigratedDatabase** med en **Premium-tjänstnivå** och en **P6** Service Objective. Ändra dessa värden som lämpliga för din miljö.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Om du vill ansluta till en SQL Database-server som hanterar en enskild databas bakom en företagsbrandvägg måste brandväggen ha port 1433 öppen. Om du vill ansluta till en hanterad instans måste du ha en [punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md) eller en expressvägsanslutning.

I det här exemplet visas hur du importerar en databas med SqlPackage med Universell Active Directory-autentisering.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Använda PowerShell

> [!NOTE]
> [En hanterad instans](sql-database-managed-instance.md) stöder för närvarande inte migrering av en databas till en instansdatabas från en BACPAC-fil med Azure PowerShell. Om du vill importera till en hanterad instans använder du SQL Server Management Studio eller SQLPackage.

> [!NOTE]
> De datorer som bearbetar import-/exportbegäranden som skickas via portalen eller Powershell måste lagra bacpac-filen samt temporära filer som genereras av DacFX (Data-Tier Application Framework). Det diskutrymme som krävs varierar avsevärt mellan DBs med samma storlek och kan ta upp till 3 gånger av databasstorleken. Datorer som kör import-/exportbegäran har bara 450 GB lokalt diskutrymme. Därför kan vissa begäranden misslyckas med felet "Det finns inte tillräckligt med utrymme på disken". I det här fallet är lösningen att köra sqlpackage.exe på en dator med tillräckligt med lokalt diskutrymme. När du importerar/exporterar databaser som är större än 150 GB använder du SqlPackage för att undvika det här problemet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

Använd cmdleten [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) för att skicka en begäran om importdatabas till Azure SQL Database-tjänsten. Beroende på databasens storlek kan importen ta lite tid att slutföra.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Du kan använda cmdleten [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) för att kontrollera importens förlopp. Kör cmdleten direkt efter `Status: InProgress`att begäran returneras vanligtvis . Importen är klar när `Status: Succeeded`du ser .

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) för att skicka en begäran om importdatabas till Azure SQL Database-tjänsten. Beroende på databasens storlek kan importen ta lite tid att slutföra.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Ett annat skriptexempel finns i [Importera en databas från en BACPAC-fil](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Begränsningar

- Det går inte att importera till en databas i elastisk pool. Du kan importera data till en enda databas och sedan flytta databasen till en elastisk pool.
- Import exporttjänsten fungerar inte när Tillåt åtkomst till Azure-tjänster är inställt på OFF. Du kan dock komma runt problemet genom att manuellt köra sqlpackage.exe från en Virtuell Azure eller utföra exporten direkt i koden med hjälp av DACFx API.

## <a name="import-using-wizards"></a>Importera med guider

Du kan också använda dessa guider.

- [Guiden Importera program på datanivå i SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Guiden Import och export av SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du ansluter till och frågar en importerad SQL-databas finns i [Snabbstart: Azure SQL Database: Använd SQL Server Management Studio för att ansluta och fråga data](sql-database-connect-query-ssms.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (på engelska).
- En diskussion om hela SQL Server-databasmigreringsprocessen, inklusive prestandarekommendationer, finns i [SQL Server-databasmigrering till Azure SQL Database](sql-database-single-database-migrate.md).
- Mer information om hur du hanterar och delar lagringsnycklar och signaturer för delad åtkomst på ett säkert sätt finns i [Azure Storage Security Guide](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
