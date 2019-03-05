---
title: Exportera en enda eller en Azure SQL-databas till en BACPAC-fil i pooler | Microsoft Docs
description: Exportera en Azure SQL-databas till en BACPAC-fil med hjälp av Azure portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: ce5635c97d91514f3fca63162f5356cdf89a7ca6
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307487"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportera en Azure SQL-databas till en BACPAC-fil

När du vill exportera en databas för arkivering eller för att flytta till en annan plattform kan du exportera databasschema och data till en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil. En BACPAC-fil är en ZIP-fil med filnamnstillägget BACPAC som innehåller metadata och data från en SQL Server-databas. En BACPAC-fil som kan lagras i Azure Blob storage eller i lokal lagring på en lokal plats och senare importerade tillbaka till Azure SQL Database eller till en SQL Server on-premises-installationen.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Att tänka på när du exporterar en Azure SQL database

- För en export transaktionellt konsekvent, måste du kontrollera antingen att någon aktivitet sker under export eller som du exporterar från en [transaktionellt konsekvent kopia](sql-database-copy.md) för din Azure SQL database.
- Om du exporterar till blob storage, är den maximala storleken för en BACPAC-fil 200 GB. Exportera till lokal lagring för att arkivera en större BACPAC-fil.
- Exportera en BACPAC-fil till Azure premium storage med hjälp av metoderna som beskrivs i den här artikeln stöds inte.
- Om exportåtgärden från Azure SQL Database överstiger 20 timmar, kan det avbrytas. För att öka prestanda under export kan du:

  - Tillfälligt öka beräkningsstorleken på din.
  - Upphör alla läsa och skriva aktivitet under exporten.
  - Använd en [grupperat index](https://msdn.microsoft.com/library/ms190457.aspx) med icke-null-värden på alla stora tabeller. Utan grupperade index, kan en export misslyckas om det tar längre tid än 6 – 12 timmar. Det beror på att exporttjänsten måste utföra en tabellgenomsökning att exportera hela tabellen. Ett bra sätt att bestämma om dina tabeller är optimerade för export är att köra **DBCC SHOW_STATISTICS** och se till att den *RANGE_HI_KEY* inte är null och dess värde har bra distribution. Mer information finns i [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs är inte avsedda att användas för säkerhetskopiering och återställning. Azure SQL Database skapar automatiskt säkerhetskopior för varje användardatabas. Mer information finns i [översikt över affärskontinuitet](sql-database-business-continuity.md) och [SQL Database-säkerhetskopior](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportera till en BACPAC-fil med hjälp av Azure portal

> [!NOTE]
> [En hanterad instans](sql-database-managed-instance.md) stöder för närvarande inte exportera en databas till en BACPAC-fil med hjälp av Azure portal. Om du vill exportera en hanterad instans till en BACPAC-fil, använder du SQL Server Management Studio eller SQLPackage.

1. Så här exporterar du en databas med den [Azure-portalen](https://portal.azure.com), öppna sidan för din databas och klickar på **exportera** i verktygsfältet.

   ![databasexport](./media/sql-database-export/database-export1.png)

2. Anger du BACPAC-filnamnet och välj en befintlig Azure-lagringskonto och en behållare för exporten tillhandahålla lämpliga autentiseringsuppgifter för åtkomst till källplatsens databas.

    ![databasexport](./media/sql-database-export/database-export2.png)

3. Klicka på **OK**.

4. Öppna sidan för SQL Database-server som innehåller databasen för att övervaka förloppet för exportåtgärden. Under till **inställningar** och klicka sedan på **Import/Export-historik**.

   ![export-historik](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportera till en BACPAC-fil med hjälp av verktyget SQLPackage

Så här exporterar du en SQL-databas med den [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) kommandoradsverktyget finns i [exportera parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Verktyget SQLPackage levereras med de senaste versionerna av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller så kan du hämta den senaste versionen av [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) direkt från Microsoft download center.

Vi rekommenderar användning av verktyget SQLPackage för skalning och prestanda i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

Det här exemplet visar hur du exporterar en databas med hjälp av SqlPackage.exe med Active Directory Universal-autentisering:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportera till en BACPAC-fil med hjälp av SQL Server Management Studio (SSMS)

De senaste versionerna av SQL Server Management Studio innehåller en guide för att exportera en Azure SQL-databas till en BACPAC-fil. Se den [exportera ett Dataskiktsprogram](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportera till en BACPAC-fil med hjälp av PowerShell

> [!NOTE]
> [En hanterad instans](sql-database-managed-instance.md) stöder för närvarande inte exportera en databas till en BACPAC-fil med hjälp av Azure PowerShell. Om du vill exportera en hanterad instans till en BACPAC-fil, använder du SQL Server Management Studio eller SQLPackage.

Använd den [New AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) cmdlet för att skicka en begäran om export till Azure SQL Database-tjänsten. Exportåtgärden kan ta lite tid att slutföra beroende på databasens storlek.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Du kan kontrollera status för exportbegäran om genom att använda den [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet. Kör detta omedelbart efter att begäran vanligtvis returnerar **Status: InProgress**. När du ser **Status: Lyckades** exporten har slutförts.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Nästa steg

- Vill veta mer om långsiktig kvarhållning av säkerhetskopior för en enskild databaser och databaser i en pool som ett alternativ till exporterade en databas för arkivering, se [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md). Du kan använda SQL Agent-jobb för att schemalägga [endast kopiering databassäkerhetskopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) som ett alternativ till långsiktig kvarhållning av säkerhetskopior.
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
- Läs om hur du importerar en BACPAC till en SQL Server-databas i [importera en BACPAC till en SQL Server-databas](https://msdn.microsoft.com/library/hh710052.aspx).
- Läs om hur du exporterar en BACPAC från en SQL Server-databas i [exporterar en Data-tier-program](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Läs om hur du använder Data Migration Service för att migrera en databas i [migrera SQL Server till Azure SQL Database med DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Om du exporterar från SQL Server som en prelude till migrering till Azure SQL Database, se [migrera en SQL Server-databas till Azure SQL Database](sql-database-single-database-migrate.md).
- Lär dig hur du hantera och dela lagringsnycklar och delad åtkomst signaturer på ett säkert sätt, finns i [säkerhetsguiden för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
