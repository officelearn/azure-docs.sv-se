---
title: Återställa en befintlig Azure SQL Data Warehouse | Microsoft Docs
description: Instruktions guide för att återställa en befintlig Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7470dd96109823057a174d2ecf097583dcb51898
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575283"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Återställa en befintlig Azure SQL Data Warehouse

I den här artikeln får du lära dig att återställa en befintlig SQL Data Warehouse via Azure Portal och PowerShell:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje SQL Data Warehouse hanteras av en SQL-Server (till exempel myserver.database.windows.net) som har en standard-DTU-kvot. Kontrol lera att SQL Server har tillräckligt med den återstående DTU-kvoten för databasen som återställs. Information om hur du beräknar DTU krävs eller begär mer DTU finns i [begär en ändring av DTU-kvot][Request a DTU quota change].

## <a name="before-you-begin"></a>Innan du börjar

1. Se till att [installera Azure PowerShell][Install Azure PowerShell].
2. Ha en befintlig återställnings punkt som du vill återställa från. Om du vill skapa en ny återställning går [du till självstudien för att skapa en ny användardefinierad återställnings punkt][the tutorial to create a new user-defined restore point].

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Återställa ett befintligt informations lager via PowerShell

Om du vill återställa ett befintligt informations lager från en återställnings punkt använder du PowerShell-cmdleten Restore [-AzSqlDatabase][Restore-AzSqlDatabase] .

1. Öppna PowerShell.

2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.

3. Välj den prenumeration som innehåller den databas som ska återställas.

4. Lista över återställnings punkter för data lagret.

5. Välj önskad återställnings punkt med hjälp av RestorePointCreationDate.

6. Återställ data lagret till önskad återställnings punkt med hjälp av Restore [-AzSqlDatabase][Restore-AzSqlDatabase] PowerShell-cmdlet.
        1. Om du vill återställa SQL Data Warehouse till en annan logisk server, måste du ange namnet på den andra logiska servern.  Den här logiska servern kan också finnas i en annan resurs grupp och region.
        2. Om du vill återställa till en annan prenumeration använder du knappen flytta för att flytta den logiska servern till en annan prenumeration.

7. Kontrol lera att det återställda informations lagret är online.

8. När återställningen har slutförts kan du konfigurera det återställda data lagret genom att följa [konfiguration av databasen efter][Configure your database after recovery]återställningen.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Återställa ett befintligt informations lager via Azure Portal

1. Logga in på [Azure Portal][Azure portal].
2. Navigera till SQL Data Warehouse som du vill återställa från.
3. Överst på bladet översikt väljer du **Återställ**.

    ![ Återställa översikt](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Välj antingen **automatiska återställnings punkter** eller **användardefinierade återställnings punkter**. Om data lagret inte har några automatiska återställnings punkter väntar du några timmar eller skapar en användardefinierad återställnings punkt innan du återställer. För användardefinierade återställnings punkter väljer du en befintlig eller skapar en ny. För **Server**kan du välja en logisk server i en annan resurs grupp och region eller skapa en ny. När du har angett alla parametrar klickar du på **Granska + Återställ**.

    ![Automatiska återställningspunkter](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Nästa steg
- [Återställa ett borttaget informations lager][Restore a deleted data warehouse]
- [Återställa från ett informations lager för geo-säkerhetskopiering][Restore from a geo-backup data warehouse]
 
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
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
