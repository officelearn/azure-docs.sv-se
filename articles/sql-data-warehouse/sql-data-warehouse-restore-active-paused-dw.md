---
title: Återställa ett befintligt informationslager
description: Instruktions guide för att återställa en befintlig SQL-pool.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bf83de481cf15711da45edf00e63262a05489247
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199552"
---
# <a name="restore-an-existing-sql-pool"></a>Återställa en befintlig SQL-pool

I den här artikeln får du lära dig hur du återställer en befintlig SQL-pool i Azure Synapse Analytics med hjälp av Azure Portal och PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje pool finns på en SQL-Server (till exempel myserver.database.windows.net) som har en standard-DTU-kvot. Kontrol lera att SQL Server har tillräckligt med den återstående DTU-kvoten för databasen som återställs. Information om hur du beräknar DTU krävs eller begär mer DTU finns i [begär en ändring av DTU-kvot](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Innan du börjar

1. Se till att [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Ha en befintlig återställnings punkt som du vill återställa från. Om du vill skapa en ny återställning går [du till självstudien för att skapa en ny användardefinierad återställnings punkt](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Återställa en befintlig SQL-pool via PowerShell

För att återställa en befintlig SQL-pool från en återställnings punkt använder du PowerShell-cmdleten [restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

1. Öppna PowerShell.

2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.

3. Välj den prenumeration som innehåller den databas som ska återställas.

4. Visa en lista över återställnings punkterna för SQL-poolen.

5. Välj önskad återställnings punkt med hjälp av RestorePointCreationDate.

6. Återställ SQL-poolen till önskad återställnings punkt med hjälp av [restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell-cmdlet.
        1. Om du vill återställa SQL-poolen till en annan logisk server, måste du ange namnet på den andra logiska servern.  Den här logiska servern kan också finnas i en annan resurs grupp och region.
        2. Om du vill återställa till en annan prenumeration använder du knappen flytta för att flytta den logiska servern till en annan prenumeration.

7. Kontrol lera att den återställda SQL-poolen är online.

8. När återställningen har slutförts kan du konfigurera en återställd SQL-pool genom att följa [Konfigurera databasen efter återställningen](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Återställa en befintlig SQL-pool via Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till den SQL-pool som du vill återställa från.
3. Överst på bladet översikt väljer du **Återställ**.

    ![ Återställa översikt](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Välj antingen **automatiska återställnings punkter** eller **användardefinierade återställnings punkter**. Om SQL-poolen inte har några automatiska återställnings punkter väntar du några timmar eller skapar en användardefinierad återställnings punkt innan du återställer. För användardefinierade återställnings punkter väljer du en befintlig eller skapar en ny. För **Server**kan du välja en logisk server i en annan resurs grupp och region eller skapa en ny. När du har angett alla parametrar klickar du på **Granska + Återställ**.

    ![Automatiska återställnings punkter](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Nästa steg
- [Återställa en borttagen SQL-pool](sql-data-warehouse-restore-deleted-dw.md)
- [Återställa från en geo-backup SQL-pool](sql-data-warehouse-restore-from-geo-backup.md)

 