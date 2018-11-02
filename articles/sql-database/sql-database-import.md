---
title: Importera en BACPAC-fil för att skapa en Azure SQL database | Microsoft Docs
description: Skapa en newAzure SQL-databas genom att importera en BACPAC-fil.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 913e374e51b968650b14d3f6563f64c217b76ad0
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913819"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>Snabbstart: Importera en BACPAC-fil till en ny Azure SQL Database

När du behöver importera en databas från ett arkiv eller när du migrerar från en annan plattform kan du importera databasschema och data från en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil. En BACPAC-fil är en ZIP-fil med filnamnstillägget BACPAC som innehåller metadata och data från en SQL Server-databas. En BACPAC-fil kan importeras från Azure blob storage (endast standard storage) eller från lokal lagring på en lokal plats. För att maximera import hastighet, rekommenderar vi att du anger en högre tjänstnivå och compute storlek, till exempel en P6 och skala sedan ned efter behov när importen har slutförts. Dessutom kompatibilitetsnivån för databas efter importen baseras på källdatabasen kompatibilitetsnivå.

> [!IMPORTANT]
> När du har migrerat din databas till Azure SQL Database kan du välja att köra databasen på den aktuella kompatibilitetsnivån (nivå 100 för AdventureWorks2008R2 databasen) eller på en högre nivå. Mer information om effekterna av och alternativ för att köra en databas på en specifik kompatibilitetsnivå finns i [Ändra databasens kompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). I [Ändra konfiguration av databasomfång](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) finns även information om ytterligare databasnivåinställningar som rör kompatibilitetsnivåer.

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Importera från en BACPAC-fil med hjälp av Azure portal

Den här artikeln innehåller anvisningar för att skapa en Azure SQL database från en BACPAC-fil som lagras i Azure blob storage med den [Azure-portalen](https://portal.azure.com). Importera med hjälp av Azure portal har stöd för importera en BACPAC-fil från Azure blob storage.

Om du vill importera en databas med Azure portal öppnar du sidan för servern (inte sidan för databasen) för att koppla databasen till och klicka sedan på **importera** i verktygsfältet. Anger lagringskontot och behållaren och väljer BACPAC-fil som du vill importera. Välj storleken på den nya databasen (vanligtvis samma som ursprung) och ange målet autentiseringsuppgifterna för SQL Server.  

   ![databasimport](./media/sql-database-import/import.png)

Öppna sidan för den logiska servern som innehåller den databas som importeras för att övervaka förloppet för importåtgärden. Rulla ned till **Operations** och klicka sedan på **Import/Export** historik.

> [!NOTE]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md) stöds importera från en BACPAC-fil med de andra metoderna i den här artikeln men stöder för närvarande inte stöd för migrering med Azure portal.

### <a name="monitor-the-progress-of-an-import-operation"></a>Övervaka förloppet för en importåtgärd

Öppna sidan för den logiska servern där databasen håller på att importeras för att övervaka förloppet för importåtgärden. Rulla ned till **Operations** och klicka sedan på **Import/Export** historik.

   ![importera](./media/sql-database-import/import-history.png) ![importstatus](./media/sql-database-import/import-status.png)

Kontrollera att databasen är aktiv på servern, klicka på **SQL-databaser** och kontrollera den nya databasen är **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importera från en BACPAC-fil med hjälp av SQLPackage

Importera en SQL-databas med den [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) kommandoradsverktyget finns i [importera parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). Verktyget SQLPackage levereras med de senaste versionerna av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller så kan du hämta den senaste versionen av [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) direkt från Microsoft download center.

Vi rekommenderar användning av verktyget SQLPackage för skalning och prestanda i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

Se följande SQLPackage-kommando för en exempel-skript att importera den **AdventureWorks2008R2** databas från lokal lagring till en Azure SQL Database logisk server, som kallas **mynewserver20170403** i det här exemplet. Det här skriptet visar att skapa en ny databas med namnet **myMigratedDatabase**, med en tjänstnivå för **Premium**, och ett Tjänstmål för **P6**. Ändra dessa värden efter behov i miljön.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> En logisk Azure SQL Database-server avlyssnar port 1433. Om du försöker ansluta till en logisk Azure SQL Database-server inifrån en företagsbrandvägg, måste den porten vara öppen i företagsbrandväggen för att du ska kunna ansluta.
>

Det här exemplet visar hur du importerar en databas med hjälp av SqlPackage.exe med Active Directory Universal-autentisering:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importera från en BACPAC-fil med hjälp av PowerShell

Använd den [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) cmdlet för att skicka en begäran om importen till Azure SQL Database-tjänsten. Importen kan ta lite tid att slutföra beroende på databasens storlek.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Om du vill kontrollera status för begäran om import använder den [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet. Kör detta omedelbart efter att begäran vanligtvis returnerar **Status: pågår**. När du ser **Status: lyckades** importen är klar.

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

- Importera till en databas i elastiska poolen stöds inte. Du kan importera data till en enkel databas och sedan flytta databasen till en pool.

## <a name="import-using-other-methods"></a>Importera med hjälp av andra metoder

Du kan också använda dessa guider:

- [Importera Data-tier Application Wizard i SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server-Import och exportguiden](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig mer om att ansluta till och fråga importerade SQL Database, se [ansluter till SQL Database med SQL Server Management Studio och kör en exempelfråga i T-SQL](sql-database-connect-query-ssms.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
- En beskrivning av hela SQL Server-databasmigreringsprocessen, inklusive prestandarekommendationer, finns i [migrera en SQL Server-databas till Azure SQL Database](sql-database-cloud-migrate.md).
- Lär dig hur du hantera och dela lagringsnycklar och delad åtkomst signaturer på ett säkert sätt, finns i [säkerhetsguiden för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
