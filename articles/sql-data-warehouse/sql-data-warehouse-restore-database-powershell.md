---
title: Återställa en Azure SQL Data Warehouse (PowerShell) | Microsoft Docs
description: PowerShell-åtgärder för att återställa en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6324eb11b334fd6a00e30d6f2fc6d1bec3f7a82c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870831"
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Återställa en Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Översikt över][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

I den här artikeln får du lära dig hur du återställer en Azure SQL Data Warehouse med hjälp av PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Kontrollera din DTU-kapacitet.** Varje SQL Data Warehouse finns på en SQLServer (t.ex. myserver.database.windows.net) som har en standard DTU-kvot.  Innan du kan återställa en SQL Data Warehouse, kontrollerar du att den SQLServer har tillräckligt med återstående DTU-kvot för den databas som återställs. Läs hur du beräkna DTU behövs eller begära mer DTU i [begära en DTU-kvot ändring][Request a DTU quota change].

### <a name="install-powershell"></a>Installera PowerShell
För att kunna använda Azure PowerShell med SQL Data Warehouse, kommer du behöva installera Azure PowerShell.  Du kan kontrollera din version genom att köra **Get-Module - ListAvailable-Name Az**.  Mer information om hur du installerar den senaste versionen finns i [Installera och konfigurera Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Återställ en databas med aktiv eller pausad
Att återställa en databas från en ögonblicksbild använder det [återställning AzSqlDatabase] [ Restore-AzSqlDatabase] PowerShell-cmdlet.

1. Öppna Windows PowerShell.
2. Anslut till ditt Azure-konto och lista över alla prenumerationer som är associerat med ditt konto.
3. Välj den prenumeration som innehåller databasen som skulle återställas.
4. Lista över återställningspunkter för databasen.
5. Välj önskad återställningspunkten med hjälp av RestorePointCreationDate.
6. Återställa databasen till den önskade återställningspunkten.
7. Kontrollera att den återställda databasen är online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> När återställningen har slutförts kan du konfigurera din återställda databas genom att följa [konfigurera databasen efter återställningen][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas
Återställ en borttagen databas med den [återställning AzSqlDatabase] [ Restore-AzSqlDatabase] cmdlet.

1. Öppna Windows PowerShell.
2. Anslut till ditt Azure-konto och lista över alla prenumerationer som är associerat med ditt konto.
3. Välj den prenumeration som innehåller den borttagna databasen återställs.
4. Få viss borttagen databas.
5. Återställa den borttagna databasen.
6. Kontrollera att den återställda databasen är online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> När återställningen har slutförts kan du konfigurera din återställda databas genom att följa [konfigurera databasen efter återställningen][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Återställa från en Azure-geografisk region
Om du vill återställa en databas kan använda den [återställning AzSqlDatabase] [ Restore-AzSqlDatabase] cmdlet.

> [!NOTE]
> Du kan utföra en geo-återställning till optimerad för beräkning! Om du vill göra det anger du en optimerad för beräkning ServiceObjectiveName som en valfri parameter. 
>
> 

1. Öppna Windows PowerShell.
2. Anslut till ditt Azure-konto och lista över alla prenumerationer som är associerat med ditt konto.
3. Välj den prenumeration som innehåller databasen som skulle återställas.
4. Hämta den databas som du vill återställa.
5. Skapa recovery-begäran för databasen.
6. Kontrollera status för geo-återställa databasen.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> För att konfigurera databasen efter återställningen har slutförts, se [konfigurera databasen efter återställningen][Configure your database after recovery].
> 
> 

Den återställda databasen kommer att TDE-aktiverade om källdatabasen är TDE-aktiverad.

## <a name="next-steps"></a>Nästa steg
Läs om funktionerna för affärskontinuitet för Azure SQL Database-versionerna, den [översikt över Azure SQL Database affärskontinuitet][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
