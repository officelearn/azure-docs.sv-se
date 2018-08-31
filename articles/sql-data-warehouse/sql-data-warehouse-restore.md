---
title: Återställa en Azure SQL Data Warehouse | Microsoft Docs
description: Så här guide för att återställa en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: cf96e153c07211fe176839e713935adcf6b9dbe1
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306970"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Återställa Azure SQL Data Warehouse 
I den här artikeln får du lära dig hur du gör följande:

- Skapa en återställningspunkt
- Återställa från en automatisk återställningspunkt eller användardefinierade återställningspunkt
- Återställa från en borttagen databas
- Återställa från en geo-säkerhetskopia
- Skapa en kopia av ditt informationslager från en användardefinierad återställningspunkt

## <a name="before-you-begin"></a>Innan du börjar
**Kontrollera din DTU-kapacitet.** Varje SQL Data Warehouse finns på en SQLServer (t.ex. myserver.database.windows.net) som har en standard DTU-kvot.  Innan du kan återställa ett SQL data warehouse, kontrollerar du att den SQLServer har tillräckligt med återstående DTU-kvot för den databas som återställs. Läs hur du beräkna DTU behövs eller begära mer DTU i [begära en DTU-kvot ändring][Request a DTU quota change].

# <a name="restore-through-powershell"></a>Återställa via PowerShell

## <a name="install-powershell"></a>Installera PowerShell
För att kunna använda Azure PowerShell med SQL Data Warehouse, kommer du behöva installera Azure PowerShell version 1.0 eller senare.  Du kan kontrollera din version genom att köra **Get-Module - ListAvailable-Name AzureRM**.  Den senaste versionen kan installeras från [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Mer information om hur du installerar den senaste versionen finns i [Installera och konfigurera Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Återställ en databas med aktiv eller pausad
Att återställa en databas från en återställningspunkt använder den [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] PowerShell-cmdlet.

1. Öppna Windows PowerShell.

2. Anslut till ditt Azure-konto och lista över alla prenumerationer som är associerat med ditt konto.

3. Välj den prenumeration som innehåller databasen som skulle återställas.

4. Lista över återställningspunkter för databasen.

5. Välj önskad återställningspunkten med hjälp av RestorePointCreationDate.

   > [!NOTE]
   > När du återställer, kan du ange olika ServiceObjectiveName (DWU) eller en annan server som finns i en annan region.

6. Återställa databasen till den önskade återställningspunkten.

7. Kontrollera att den återställda databasen är online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> När återställningen har slutförts kan du konfigurera din återställda databas genom att följa [konfigurera databasen efter återställningen][Configure your database after recovery].
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points"></a>Kopiera ditt informationslager med en användardefinierad återställningspunkter
Att återställa en databas från en användardefinierad återställningspunkt använder den [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] PowerShell-cmdlet.

1. Öppna Windows PowerShell.
2. Anslut till ditt Azure-konto och lista över alla prenumerationer som är associerat med ditt konto.
3. Välj den prenumeration som innehåller databasen som skulle återställas.
4. Skapa en återställningspunkt för en omedelbar kopia av databasen
5. Byt namn på din databas till ett tillfälligt namn.
5. Hämta den senaste återställningspunkten genom den angivna RestorePointLabel.
6. Hämta resurs-id för databasen för att initiera återställningen
6. Återställa databasen till den önskade återställningspunkten.
7. Kontrollera att den återställda databasen är online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzureRmSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas
Återställ en borttagen databas med den [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] cmdlet.

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

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> När återställningen har slutförts kan du konfigurera din återställda databas genom att följa [konfigurera databasen efter återställningen][Configure your database after recovery].
>

## <a name="restore-from-an-azure-geographical-region"></a>Återställa från en Azure-geografisk region
Om du vill återställa en databas kan använda den [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] cmdlet.

> [!NOTE]
> Du kan utföra en geo-återställning till Gen2! Du gör detta genom att ange en Gen2 ServiceObjectiveName (t.ex. DW1000**c**) som en valfri parameter.
>

1. Öppna Windows PowerShell.
2. Anslut till ditt Azure-konto och lista över alla prenumerationer som är associerat med ditt konto.
3. Välj den prenumeration som innehåller databasen som skulle återställas.
4. Hämta den databas som du vill återställa.
5. Skapa recovery-begäran för databasen.
6. Kontrollera status för geo-återställa databasen.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> För att konfigurera databasen efter återställningen har slutförts, se [konfigurera databasen efter återställningen][Configure your database after recovery].
>

Den återställda databasen kommer att TDE-aktiverade om källdatabasen är TDE-aktiverad.

# <a name="restore-through-the-azure-portal"></a>Återställa via Azure Portal

## <a name="create-a-user-defined-restore-point"></a>Skapa en användardefinierad återställningspunkt
1. Logga in på [Azure Portal][Azure portal].

2. Gå till SQL data warehouse som du vill skapa en återställningspunkt för.

3. Högst upp på bladet översikt, Välj **+ ny återställningspunkt**.

    ![Ny återställningspunkt](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)
    
4. Ange ett namn för återställningspunkten.

    ![Namnet på återställningspunkt](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database"></a>Återställ en databas med aktiv eller pausad
1. Logga in på [Azure Portal][Azure portal].
2. Gå till SQL data warehouse som du vill återställa från.
3. Högst upp på bladet översikt, Välj **återställa**.

    ![ Återställa översikt](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)
    
4. Välj antingen **automatisk återställningspunkter** eller **användardefinierade återställningspunkter**.

    ![Automatiska återställningspunkter](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)
    
5. För användardefinierade återställningspunkter **Välj en återställningspunkt** eller **skapa en ny användardefinierad återställningspunkt**.

    ![Användardefinierade återställningspunkter](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas
1. Logga in på [Azure Portal][Azure portal].
2. Gå till din borttagen databas finns på SQL-servern.
3. Välj ikonen borttagna databaser i innehållsförteckningen.

    ![Borttagna databaser](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)
    
4. Välj den borttagna databasen som du vill återställa.

    ![Välj borttagna databaser](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)
    
5. Ange ett nytt databasnamn.

    ![Ange databasnamn](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

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
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
