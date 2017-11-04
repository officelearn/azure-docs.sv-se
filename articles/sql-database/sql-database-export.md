---
title: Exportera en Azure SQL database till en BACPAC fil | Microsoft Docs
description: "Exportera en Azure SQL database till en BACPAC-fil med hjälp av Azure-portalen"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 10/11/2017
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 2291088b3d8e50f4e8143bf8560580e89db4cae3
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportera en Azure SQL database till en BACPAC-fil

När du vill exportera en databas för arkivering eller för att flytta till en annan plattform du kan exportera databasschemat och data till en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil. En BACPAC-fil är en ZIP-fil med filnamnstillägget BACPAC som innehåller metadata och data från en SQL Server-databas. En BACPAC-fil kan lagras i Azure blob storage eller i lokal lagring på en lokal plats och senare importeras tillbaka till Azure SQL Database eller till en lokal SQL Server-installation. 

> [!IMPORTANT] 
> Azure SQL Database automatiserad Export har avslutats på 1 mars 2017. Du kan använda [långsiktig lagring av säkerhetskopior](sql-database-long-term-retention.md
) eller [Azure Automation](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) regelbundet Arkivera SQL-databaser med hjälp av PowerShell enligt ett schema som du väljer. Ett exempel kan du hämta den [exempel PowerShell-skript](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) från Github.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Att tänka på när du exporterar en Azure SQL database

* För en export transaktionellt konsekvent, måste du kontrollera antingen att inga skrivåtgärder aktivitet pågår under exporten eller som du exporterar från en [transaktionellt konsekvent kopiera](sql-database-copy.md) för din Azure SQL-databas.
* Om du exporterar till blob storage är den maximala storleken för en BACPAC fil 200 GB. Om du vill arkivera en större BACPAC fil att exportera till lokal lagring.
* Exportera en BACPAC-fil till Azure premium-lagring med hjälp av metoderna som beskrivs i den här artikeln stöds inte.
* Om exporten från Azure SQL Database överskrider 20 timmar, kan den avbrytas. Om du vill öka prestandan under exporten kan du:
  * Tillfälligt öka din servicenivå.
  * Upphör alla läsa och skriva aktiviteten under exporten.
  * Använd en [grupperat index](https://msdn.microsoft.com/library/ms190457.aspx) med icke-null-värden för alla stora tabeller. Utan grupperade index, kan en export misslyckas om det tar längre tid än 6 – 12 timmar. Det beror på att tjänsten export måste utföra en tabellgenomsökning om du vill exportera hela tabellen. Ett bra sätt att bestämma om dina tabeller är optimerade för export är att köra **DBCC SHOW_STATISTICS** och se till att den *RANGE_HI_KEY* inte är null och dess värde har bra distribution. Mer information finns i [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs är inte avsedda att användas för säkerhetskopiering och återställning. Azure SQL Database skapar automatiskt säkerhetskopior för varje databas. Mer information finns i [översikt över verksamhetskontinuitet](sql-database-business-continuity.md) och [SQL databassäkerhetskopieringar](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportera till en BACPAC-fil med hjälp av Azure portal

Så här exporterar du en databas med hjälp av den [Azure-portalen](https://portal.azure.com), öppna sidan för din databas och klickar på **exportera** i verktygsfältet. Ange filnamnet BACPAC, ange Azure storage-konto och behållare för export och ange autentiseringsuppgifter för att ansluta till källdatabasen.  

![Databasexport](./media/sql-database-export/database-export.png)

Öppna sidan för den logiska servern som innehåller den databas som exporteras för att övervaka förloppet för exporten. Rulla ned till **Operations** och klicka sedan på **Import/Export** historik.

![Exportera tidigare](./media/sql-database-export/export-history.png)
![exportstatus historik](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportera till en BACPAC-fil med hjälp av verktyget SQLPackage

Så här exporterar du en SQL-databas med den [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) kommandoradsverktyget finns [exportera parametrar och egenskaper](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). Verktyget SQLPackage levereras med de senaste versionerna av [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) och [SQL Server Data Tools för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller så kan du hämta den senaste versionen av [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direkt från Microsoft download center.

Vi rekommenderar användning av verktyget SQLPackage för skalning och prestanda i de flesta produktionsmiljöer. En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).

Det här exemplet visar hur du exporterar en databas med hjälp av SqlPackage.exe med Active Directory Universal autentisering:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportera till en BACPAC-fil med hjälp av SQL Server Management Studio (SSMS)

De senaste versionerna av SQL Server Management Studio ger också en guide för att exportera en Azure SQL Database till en BACPAC-fil. Finns det [exportera en Dataskiktsprogrammet](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportera till en BACPAC-fil med hjälp av PowerShell

Använd den [ny AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) för att skicka en begäran om export av databas till Azure SQL Database-tjänsten. Exportåtgärden kan ta lite tid att slutföra beroende på storleken på databasen.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Om du vill kontrollera status för begäran för export av [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet. Kör detta omedelbart efter begäran vanligtvis returnerar **Status: InProgress**. När du ser **Status: lyckades** exporten har slutförts.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Nästa steg

* Mer information om långsiktig säkerhetskopiering kvarhållning av en Azure SQL database-säkerhetskopia som ett alternativ till att exportera en databas för finns [långsiktig lagring av säkerhetskopior](sql-database-long-term-retention.md).
- En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
* Läs om hur du importerar en BACPAC till en SQL Server-databas i [importera en BACPCAC till en SQL Server-databas](https://msdn.microsoft.com/library/hh710052.aspx).
* Läs om hur du exporterar en BACPAC från en SQL Server-databas i [exportera en Dataskiktsprogrammet](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) och [migrera din första databas](sql-database-migrate-your-sql-server-database.md).
* Om du exporterar från SQL Server som en prelude för migrering till Azure SQL Database, se [migrera en SQL Server-databas till Azure SQL Database](sql-database-cloud-migrate.md).
