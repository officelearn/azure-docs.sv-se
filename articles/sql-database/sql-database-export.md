---
title: Exportera en enskild eller poolad databas till en BACPAC-fil
description: Exportera en Azure SQL-databas till en BACPAC-fil med Azure-portalen
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 0bc72f0ad58829a3ff6545b5c4741ddc20916c31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061634"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportera en Azure SQL-databas till en BACPAC-fil

När du behöver exportera en databas för arkivering eller för att flytta till en annan plattform kan du exportera databasschemat och data till en [BACPAC-fil.](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) En BACPAC-fil är en ZIP-fil med ett tillägg av BACPAC som innehåller metadata och data från en SQL Server-databas. En BACPAC-fil kan lagras i Azure Blob-lagring eller i lokal lagring på en lokal plats och senare importeras tillbaka till Azure SQL Database eller till en lokal SQL Server-installation.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Överväganden vid export av en Azure SQL-databas

- För att en export ska vara transaktionellt konsekvent måste du se till att ingen skrivaktivitet inträffar under exporten eller att du exporterar från en [transaktionellt konsekvent kopia](sql-database-copy.md) av din Azure SQL-databas.
- Om du exporterar till blob-lagring är den maximala storleken på en BACPAC-fil 200 GB. Om du vill arkivera en större BACPAC-fil exporterar du till lokal lagring.
- Det går inte att exportera en BACPAC-fil till Azure premium-lagring med de metoder som beskrivs i den här artikeln.
- Lagring bakom en brandvägg stöds för närvarande inte.
- Om exportåtgärden från Azure SQL Database överskrider 20 timmar kan den avbrytas. Om du vill öka prestanda under export kan du:

  - Öka beräkningsstorleken tillfälligt.
  - Upphör med all läs- och skrivaktivitet under exporten.
  - Använd ett [grupperat index](https://msdn.microsoft.com/library/ms190457.aspx) med värden som inte är null i alla stora tabeller. Utan klustrade index kan en export misslyckas om det tar längre tid än 6-12 timmar. Detta beror på att exporttjänsten måste slutföra en tabellsökning för att försöka exportera hela tabellen. Ett bra sätt att avgöra om dina tabeller är optimerade för export är att köra **DBCC SHOW_STATISTICS** och se till att *RANGE_HI_KEY* inte är null och dess värde har bra distribution. Mer information finns i [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs är inte avsedda att användas för säkerhetskopiering och återställning. Azure SQL Database skapar automatiskt säkerhetskopior för varje användardatabas. Mer information finns i [översikt över affärskontinuitet](sql-database-business-continuity.md) och [säkerhetskopiering av SQL-databas](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportera till en BACPAC-fil med Azure-portalen

Det stöds för närvarande inte att exportera en BACPAC för en databas från en [hanterad instans](sql-database-managed-instance.md) med Azure PowerShell. Använd SQL Server Management Studio eller SQLPackage i stället.

> [!NOTE]
> Datorer som bearbetar import-/exportbegäranden som skickas via Azure-portalen eller PowerShell måste lagra BACPAC-filen samt temporära filer som genereras av DacFX (Data-Tier Application Framework). Det diskutrymme som krävs varierar avsevärt mellan databaser med samma storlek och kan kräva diskutrymme upp till 3 gånger storleken på databasen. Datorer som kör import-/exportbegäran har bara 450 GB lokalt diskutrymme. Därför kan vissa begäranden misslyckas `There is not enough space on the disk`med felet . I det här fallet är lösningen att köra sqlpackage.exe på en dator med tillräckligt med lokalt diskutrymme. Vi uppmuntrar till att använda [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) för att importera/exportera databaser som är större än 150 GB för att undvika det här problemet.

1. Om du vill exportera en databas med [Azure-portalen](https://portal.azure.com)öppnar du sidan för databasen och klickar på **Exportera** i verktygsfältet.

   ![Databasexport](./media/sql-database-export/database-export1.png)

2. Ange BACPAC-filnamnet, välj ett befintligt Azure-lagringskonto och behållare för export och ange sedan lämpliga autentiseringsuppgifter för åtkomst till källdatabasen. En SQL **Server-administratörsinloggning** behövs här även om du är Azure-administratör, eftersom en Azure-administratör inte motsvarar att ha SQL Server-administratörsbehörigheter.

    ![Databasexport](./media/sql-database-export/database-export2.png)

3. Klicka på **OK**.

4. Om du vill övervaka hur exportåtgärden fortskrider öppnar du sidan för SQL Database-servern som innehåller databasen som exporteras. Klicka på **Importera/exportera historik**under till **Inställningar.**

   ![exporthistorik](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportera till en BACPAC-fil med hjälp av SQLPackage-verktyget

Information om hur du exporterar en SQL-databas med kommandoradsverktyget [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) finns i [Exportera parametrar och egenskaper](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). SQLPackage-verktyget levereras med de senaste versionerna av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och SQL Server Data Tools för Visual [Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller så kan du hämta den senaste versionen av [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direkt från Microsoft Download Center.

Vi rekommenderar att du använder verktyget SQLPackage för skalning och prestanda i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

I det här exemplet visas hur du exporterar en databas med SqlPackage.exe med Universell Active Directory-autentisering:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportera till en BACPAC-fil med SQL Server Management Studio (SSMS)

De senaste versionerna av SQL Server Management Studio innehåller en guide för att exportera en Azure SQL-databas till en BACPAC-fil. Se [Exportprogrammet på datanivå](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportera till en BACPAC-fil med PowerShell

> [!NOTE]
> [En hanterad instans](sql-database-managed-instance.md) stöder för närvarande inte export av en databas till en BACPAC-fil med Azure PowerShell. Om du vill exportera en hanterad instans till en BACPAC-fil använder du SQL Server Management Studio eller SQLPackage.

Använd cmdleten [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) för att skicka en begäran om exportdatabas till Azure SQL Database-tjänsten. Beroende på databasens storlek kan det ta lite tid att slutföra exportåtgärden.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Om du vill kontrollera status för exportbegäran använder du cmdleten [Get-AzSqlDatabaseImportImportStatus.](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) Köra detta omedelbart efter begäran returnerar vanligtvis **Status: InProgress**. När du ser **Status: Exporten har slutförts.**

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

- Mer information om långsiktig lagring av säkerhetskopiering av en enskild databas och poolade databaser som ett alternativ till exporterad databas för arkivändamål finns i [Lagring av säkerhetskopiering på lång sikt](sql-database-long-term-retention.md). Du kan använda SQL [Agent-jobb](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) för att schemalägga säkerhetskopior med endast kopior som ett alternativ till långsiktig lagring av säkerhetskopiering.
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
- Mer information om hur du importerar en BACPAC till en SQL Server-databas finns i [Importera en BACPAC till en SQL Server-databas](https://msdn.microsoft.com/library/hh710052.aspx).
- Mer information om hur du exporterar en BACPAC från en SQL [Server-databas](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) finns i Exportera ett program på datanivå
- Mer information om hur du använder datamigreringstjänsten för att migrera en databas finns i [Migrera SQL Server till Azure SQL Database offline med DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Om du exporterar från SQL Server som förspel till migrering till Azure SQL Database läser [du Migrera en SQL Server-databas till Azure SQL Database](sql-database-single-database-migrate.md).
- Mer information om hur du hanterar och delar lagringsnycklar och signaturer för delad åtkomst på ett säkert sätt finns i [Azure Storage Security Guide](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
