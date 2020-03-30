---
title: Återställa en borttagen SQL-pool
description: Så här kan du styra för att återställa en borttagen SQL-pool.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f0432cafee07dbed071d24aa8c24ee9b2176967
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350182"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Återställa en borttagen SQL-pool med Azure Synapse Analytics

I den här artikeln lär du dig att återställa en SQL med antingen Azure-portalen eller PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje SQL-pool är värd för en SQL-server (till exempel myserver.database.windows.net) som har en DTU-standardkvot.  Kontrollera att SQL-servern har tillräckligt med återstående DTU-kvot för databasen som återställs. Mer information om hur du beräknar DTU som behövs eller begär mer DTU finns i [Begär en DTU-kvotändring](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Återställa ett borttaget informationslager via PowerShell

Om du vill återställa en borttagen SQL-pool använder du cmdleten [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) Om även motsvarande logiska server har tagits bort kan du inte återställa informationslagret.

1. Innan du börjar, se till att [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Öppna PowerShell.
3. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
4. Välj den prenumeration som innehåller det borttagna informationslager som ska återställas.
5. Hämta det specifika borttagna informationslagret.
6. Återställa det borttagna informationslagret
    1. Om du vill återställa det borttagna SQL Data Warehouse till en annan logisk server kontrollerar du att det andra logiska servernamnet anges.  Den här logiska servern kan också finnas i en annan resursgrupp och region.
    1. Om du vill återställa till en annan prenumeration använder du knappen [Flytta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal) för att flytta den logiska servern till en annan prenumeration.
1. Kontrollera att det återställda informationslagret är online.
1. När återställningen har slutförts kan du konfigurera det återställda informationslagret genom att [följa konfigurera databasen efter återställning](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Återställa en borttagen databas med Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till SQL-servern som det borttagna informationslagret var värd för.
3. Välj ikonen **Borttagna databaser** i innehållsförteckningen.

    ![Borttagna databaser](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Markera det borttagna SQL Data Warehouse som du vill återställa.

    ![Välj Borttagna databaser](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Ange ett nytt **databasnamn** och klicka på **OK**

    ![Ange databasnamn](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Efterföljande moment
- [Återställa en befintlig SQL-pool](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa från en SQL-pool med geo säkerhetskopiering](sql-data-warehouse-restore-from-geo-backup.md)