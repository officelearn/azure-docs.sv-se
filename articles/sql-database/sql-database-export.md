---
title: Exportera en Azure SQL-databas till en BACPAC-fil | Microsoft Docs
description: Exportera en Azure SQL-databas till en BACPAC-fil med hjälp av Azure portal
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
ms.date: 10/15/2018
ms.openlocfilehash: fecc694e5520444be06dab82191b6454fb4ee8f5
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354044"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportera en Azure SQL-databas till en BACPAC-fil

När du vill exportera en databas för arkivering eller för att flytta till en annan plattform kan du exportera databasschema och data till en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil. En BACPAC-fil är en ZIP-fil med filnamnstillägget BACPAC som innehåller metadata och data från en SQL Server-databas. En BACPAC-fil kan lagras i Azure blob storage eller i lokal lagring på en lokal plats och senare importeras tillbaka till Azure SQL Database eller till en lokal SQL Server-installation.

> [!IMPORTANT]
> Azure SQL Database automatisk Export drogs tillbaka den 1 mars 2017. Du kan använda [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md
) eller [Azure Automation](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) att regelbundet Arkivera SQL-databaser med hjälp av PowerShell enligt ett schema som du önskar. Ladda ned ett exempel på [exempel på PowerShell-skript](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) från Github.
>

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

Så här exporterar du en databas med den [Azure-portalen](https://portal.azure.com), öppna sidan för din databas och klickar på **exportera** i verktygsfältet. Anger du BACPAC-filnamnet, ger Azure storage-konto och behållare för export och ange autentiseringsuppgifter för att ansluta till källdatabasen.  

![databasexport](./media/sql-database-export/database-export.png)

Öppna sidan för den logiska servern som innehåller databasen för att övervaka förloppet för exportåtgärden. Rulla ned till **Operations** och klicka sedan på **Import/Export** historik.

![Exportera tidigare](./media/sql-database-export/export-history.png)
![Exportera tidigare status](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportera till en BACPAC-fil med hjälp av verktyget SQLPackage

Så här exporterar du en SQL-databas med den [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) kommandoradsverktyget finns i [exportera parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Verktyget SQLPackage levereras med de senaste versionerna av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller så kan du hämta den senaste versionen av [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) direkt från Microsoft download center.

Vi rekommenderar användning av verktyget SQLPackage för skalning och prestanda i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

Det här exemplet visar hur du exporterar en databas med hjälp av SqlPackage.exe med Active Directory Universal-autentisering:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportera till en BACPAC-fil med hjälp av SQL Server Management Studio (SSMS)

De senaste versionerna av SQL Server Management Studio tillhandahåller även en guide för att exportera en Azure SQL-databas till en BACPAC-fil. Se den [exportera ett Dataskiktsprogram](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportera till en BACPAC-fil med hjälp av PowerShell

Använd den [New AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) cmdlet för att skicka en begäran om export till Azure SQL Database-tjänsten. Exportåtgärden kan ta lite tid att slutföra beroende på databasens storlek.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Du kan kontrollera status för exportbegäran om genom att använda den [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet. Kör detta omedelbart efter att begäran vanligtvis returnerar **Status: pågår**. När du ser **Status: lyckades** exporten har slutförts.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Nästa steg

- Mer information om långsiktig kvarhållning av säkerhetskopior av en Azure SQL database-säkerhetskopia som ett alternativ till exporterade en databas för att se [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
- Läs om hur du importerar en BACPAC till en SQL Server-databas i [importera en BACPCAC till en SQL Server-databas](https://msdn.microsoft.com/library/hh710052.aspx).
- Läs om hur du exporterar en BACPAC från en SQL Server-databas i [exportera ett Dataskiktsprogram](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) och [migrera din första databas](sql-database-migrate-your-sql-server-database.md).
- Om du exporterar från SQL Server som en prelude till migrering till Azure SQL Database, se [migrera en SQL Server-databas till Azure SQL Database](sql-database-cloud-migrate.md).
- Lär dig hur du hantera och dela lagringsnycklar och delad åtkomst signaturer på ett säkert sätt, finns i [säkerhetsguiden för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
