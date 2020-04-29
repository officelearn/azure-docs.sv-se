---
title: Exportera en enskild databas eller en databas till en BACPAC-fil
description: Exportera en Azure SQL-databas till en BACPAC-fil med hjälp av Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 0bc72f0ad58829a3ff6545b5c4741ddc20916c31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061634"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportera en Azure SQL-databas till en BACPAC-fil

När du behöver exportera en databas för arkivering eller för att flytta till en annan plattform kan du exportera databasens schema och data till en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) -fil. En BACPAC-fil är en ZIP-fil med en utökning av BACPAC som innehåller metadata och data från en SQL Server databas. En BACPAC-fil kan lagras i Azure Blob Storage eller i lokal lagring på en lokal plats och senare importeras tillbaka till Azure SQL Database eller till en SQL Server lokal installation.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Att tänka på när du exporterar en Azure SQL-databas

- För att en export ska kunna utföras konsekvent måste du se till att ingen Skriv aktivitet inträffar under exporten eller att du exporterar från en [transaktions konsekvent kopia](sql-database-copy.md) av din Azure SQL-databas.
- Om du exporterar till Blob Storage är den maximala storleken för en BACPAC-fil 200 GB. För att arkivera en större BACPAC-fil, exportera till lokal lagring.
- Det finns inte stöd för att exportera en BACPAC-fil till Azure Premium Storage med de metoder som beskrivs i den här artikeln.
- Det finns för närvarande inte stöd för lagring bakom en brand vägg.
- Om export åtgärden från Azure SQL Database överskrider 20 timmar kan den avbrytas. Om du vill öka prestanda under exporten kan du:

  - Öka din beräknings storlek tillfälligt.
  - Upphöra med alla Läs-och skriv aktiviteter under exporten.
  - Använd ett [grupperat index](https://msdn.microsoft.com/library/ms190457.aspx) med värden som inte är null på alla stora tabeller. Utan grupperade index kan en export Miss lyckas om det tar längre tid än 6-12 timmar. Detta beror på att export tjänsten måste slutföra en tabells ökning för att försöka Exportera hela tabellen. Ett bra sätt att avgöra om dina tabeller är optimerade för export är att köra **DBCC SHOW_STATISTICS** och se till att *RANGE_HI_KEY* inte är null och att dess värde har en bra distribution. Mer information finns i [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs är inte avsedda att användas för säkerhets kopierings-och återställnings åtgärder. Azure SQL Database skapar automatiskt säkerhets kopior för varje användar databas. Mer information finns i [Översikt över verksamhets kontinuitet](sql-database-business-continuity.md) och [SQL Database säkerhets kopieringar](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportera till en BACPAC-fil med hjälp av Azure Portal

Det finns för närvarande inte stöd för att exportera en BACPAC av en databas från en [hanterad instans](sql-database-managed-instance.md) med hjälp av Azure PowerShell. Använd SQL Server Management Studio eller SQLPackage i stället.

> [!NOTE]
> Datorer som bearbetar import/export-begäranden som skickas via Azure Portal eller PowerShell måste lagra BACPAC-filen och temporära filer som genereras av DacFX (data-Tier Application Framework). Det disk utrymme som krävs varierar kraftigt mellan databaser med samma storlek och kan kräva disk utrymme upp till 3 gånger databasens storlek. Datorer som kör import/export-begäran har bara 450GB lokalt disk utrymme. Därför kan vissa begär Anden Miss lyckas med felet `There is not enough space on the disk`. I det här fallet är lösningen att köra sqlpackage. exe på en dator med tillräckligt lokalt disk utrymme. Vi rekommenderar att du använder [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) för att importera/exportera databaser som är större än 150 GB för att undvika det här problemet.

1. Om du vill exportera en databas med hjälp av [Azure Portal](https://portal.azure.com)öppnar du sidan för din databas och klickar på **Exportera** i verktygsfältet.

   ![Databas export](./media/sql-database-export/database-export1.png)

2. Ange BACPAC-filnamn, Välj ett befintligt Azure Storage-konto och container för exporten och ange sedan lämpliga autentiseringsuppgifter för åtkomst till käll databasen. En SQL **Server Admin-inloggning** krävs här även om du är Azure-administratören, eftersom det är en Azure-administratör som inte motsvarar SQL Server administratörs behörighet.

    ![Databas export](./media/sql-database-export/database-export2.png)

3. Klicka på **OK**.

4. Du övervakar förloppet för export åtgärden genom att öppna sidan för den SQL Database servern som innehåller den databas som exporteras. Under **Inställningar** och klicka sedan på **import/export-historik**.

   ![Exportera historik](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportera till en BACPAC-fil med hjälp av SQLPackage-verktyget

Information om hur du exporterar en SQL-databas med hjälp av kommando rads verktyget [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) finns i [Exportera parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). SQLPackage-verktyget levereras med de senaste versionerna av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller så kan du ladda ned den senaste versionen av [SQLPackage](https://www.microsoft.com/download/details.aspx?id=53876) direkt från Microsoft Download Center.

Vi rekommenderar att du använder SQLPackage-verktyget för skalning och prestanda i de flesta produktions miljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

Det här exemplet visar hur du exporterar en databas med hjälp av SqlPackage. exe med Active Directory Universal Authentication:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportera till en BACPAC-fil med hjälp av SQL Server Management Studio (SSMS)

De senaste versionerna av SQL Server Management Studio innehåller en guide för att exportera en Azure SQL-databas till en BACPAC-fil. Se [Exportera ett data skikts program](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportera till en BACPAC-fil med hjälp av PowerShell

> [!NOTE]
> [En hanterad instans](sql-database-managed-instance.md) stöder för närvarande inte export av en databas till en BACPAC-fil med hjälp av Azure PowerShell. Om du vill exportera en hanterad instans till en BACPAC-fil använder du SQL Server Management Studio eller SQLPackage.

Använd cmdleten [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) för att skicka en begäran om att exportera databas till tjänsten Azure SQL Database. Det kan ta en stund att slutföra export åtgärden, beroende på databasens storlek.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Om du vill kontrol lera status för export förfrågan använder du cmdleten [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Om du kör det omedelbart efter det att begäran vanligt vis returnerar **status: pågår**. När du ser **status:** exporten har slutförts.

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

- Om du vill veta mer om långsiktig kvarhållning av säkerhets kopior av en enskild databas och databaser i pooler som ett alternativ till att exportera en databas för arkivering, se [långsiktig kvarhållning av säkerhets kopior](sql-database-long-term-retention.md). Du kan använda SQL Agent-jobb för att schemalägga säkerhets kopieringar av [kopierade databaser](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) som ett alternativ till långsiktig kvarhållning av säkerhets kopior.
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
- Mer information om hur du importerar en BACPAC till en SQL Server-databas finns i [Importera en BACPAC till en SQL Server-databas](https://msdn.microsoft.com/library/hh710052.aspx).
- Information om hur du exporterar en BACPAC från en SQL Server-databas finns i [Exportera ett data skikts program](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Information om hur du migrerar en databas med hjälp av tjänsten datamigrering finns i [migrera SQL Server till Azure SQL Database offline med DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Om du exporterar från SQL Server som en inledning för migrering till Azure SQL Database, se [Migrera en SQL Server databas till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om hur du hanterar och delar lagrings nycklar och signaturer för delad åtkomst på ett säkert sätt finns i [Azure Storage Security guide](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
