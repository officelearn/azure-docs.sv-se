---
title: Importera en BACPAC-fil för att skapa en Azure SQL database | Microsoft Docs
description: Skapa en newAzure SQL-databas genom att importera en BACPAC-fil.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/10/2018
ms.author: carlrab
ms.topic: conceptual
ms.openlocfilehash: d22c9a05d1fe56d71eb901c0a4bf22c179dfe937
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646923"
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Importera en BACPAC-fil till en ny Azure SQL-databas

När du behöver importera en databas från ett arkiv eller när du migrerar från en annan plattform, kan du importera databasschemat och data från en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil. En BACPAC-fil är en ZIP-fil med filnamnstillägget BACPAC som innehåller metadata och data från en SQL Server-databas. En BACPAC-fil kan importeras från Azure blob storage (endast standardlagring) eller från lokal lagring på en lokal plats. Om du vill maximera importera hastighet, rekommenderar vi att du anger en högre nivå och prestanda servicenivå, till exempel en P6 och anpassas ned efter behov när importen är klar. Dessutom Databaskompatibilitetsnivån efter importen baseras på kompatibilitetsnivån för källdatabasen. 

> [!IMPORTANT] 
> När du migrerar din databas till Azure SQL Database måste välja du att databasen på den aktuella kompatibilitetsnivån (nivå 100 för AdventureWorks2008R2 databasen) eller på en högre nivå. Mer information om effekterna av och alternativ för att köra en databas på en specifik kompatibilitetsnivå finns i [Ändra databasens kompatibilitetsnivå](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). I [Ändra konfiguration av databasomfång](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) finns även information om ytterligare databasnivåinställningar som rör kompatibilitetsnivåer.   >

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Importera från en BACPAC-fil med hjälp av Azure portal

Den här artikeln innehåller anvisningar för att skapa en Azure SQL-databas från en BACPAC-fil som lagras i Azure blob storage med den [Azure-portalen](https://portal.azure.com). Importera med hjälp av Azure portal stöder importera en BACPAC-fil från Azure blob storage.

Om du vill importera en databas med hjälp av Azure portal, öppna sidan för servern för att koppla databasen till och klicka sedan på **importera** i verktygsfältet. Ange storage-konto och behållare och välj BACPAC-fil som du vill importera. Välj storleken på den nya databasen (vanligtvis samma som ursprung) och ange målet autentiseringsuppgifterna för SQL Server.  

   ![Databasimport](./media/sql-database-import/import.png)

Öppna sidan för den logiska servern som innehåller den databas som importeras för att övervaka förloppet för importen. Rulla ned till **Operations** och klicka sedan på **Import/Export** historik.

> [!NOTE]
> [Azure SQL-hanterade databasinstans](sql-database-managed-instance.md) stöd för import från en BACPAC-fil med hjälp av de andra metoderna i den här artikeln, men stöder för närvarande inte migrera med hjälp av Azure portal.

### <a name="monitor-the-progress-of-an-import-operation"></a>Övervaka förloppet för en importåtgärd

Öppna sidan för den logiska servern där databasen importeras importeras för att övervaka förloppet för importen. Rulla ned till **Operations** och klicka sedan på **Import/Export** historik.
   
   ![importera](./media/sql-database-import/import-history.png) ![importera status](./media/sql-database-import/import-status.png)

Kontrollera att databasen är aktiv på servern, klicka på **SQL-databaser** och kontrollera att den nya databasen är **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Importera från en BACPAC-fil med SQLPackage

Så här importerar du en SQL-databas med den [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) kommandoradsverktyget finns [importera parametrar och egenskaper](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties). Verktyget SQLPackage levereras med de senaste versionerna av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller så kan du hämta den senaste versionen av [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direkt från Microsoft download center.

Vi rekommenderar användning av verktyget SQLPackage för skalning och prestanda i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

Se följande SQLPackage kommandot till exempel ett skript att importera den **AdventureWorks2008R2** databasen från lokal lagring till en Azure SQL Database logiska server, som kallas **mynewserver20170403** i det här exemplet. Det här skriptet visar skapandet av en ny databas kallas **myMigratedDatabase**, med en tjänstnivå för **Premium**, och ett Tjänstmål för **P6**. Ändra dessa värden som är lämplig i din miljö.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> En logisk Azure SQL Database-server avlyssnar port 1433. Om du försöker ansluta till en logisk Azure SQL Database-server inifrån en företagsbrandvägg, måste den porten vara öppen i företagsbrandväggen för att du ska kunna ansluta.
>

Det här exemplet visar hur du importerar en databas med hjälp av SqlPackage.exe med Active Directory Universal autentisering:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Importera från en BACPAC-fil med hjälp av PowerShell

Använd den [ny AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) för att skicka en begäran om importera databasen till Azure SQL Database-tjänsten. Importen kan ta lite tid att slutföra beroende på storleken på databasen.

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

Om du vill kontrollera status för begäran om att importera den [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet. Kör detta omedelbart efter begäran vanligtvis returnerar **Status: InProgress**. När du ser **Status: lyckades** importen är klar.

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
Exempel på ett annat skript finns [importera en databas från en BACPAC fil](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Begränsningar
- Importera till en databas i elastisk pool stöds inte. Du kan importera data till en singleton-databas och flytta databasen till en pool.

## <a name="import-using-other-methods"></a>Importera andra metoder

Du kan också använda dessa guider:

- [Importera Data-tier Application-guiden i SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server importera och exportera guiden](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Nästa steg
* Information om hur du ansluter till och fråga importerade SQL-databas finns [Anslut till SQL Database med SQL Server Management Studio och utföra en exempelfråga i T-SQL](sql-database-connect-query-ssms.md).
* En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
* En beskrivning av hela SQL Server-databasen migreringsprocessen, inklusive rekommendationer, se [migrera en SQL Server-databas till Azure SQL Database](sql-database-cloud-migrate.md).
* Information om hur du hanterar och dela lagringsnycklar och delad åtkomst signitures finns på ett säkert sätt, [säkerhetsguiden för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide). 


  

