---
title: Återställa en borttagen SQL-pool
description: Guide för att återställa en borttagen SQL-pool.
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
ms.openlocfilehash: 34851203432b7e2daf44e840e45275de76bc3b3a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196675"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Återställa en borttagen SQL-pool med Azure Synapse Analytics

I den här artikeln får du lära dig att återställa en SQL med antingen Azure Portal eller PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje SQL-pool finns på en SQL-Server (till exempel myserver.database.windows.net) som har en standard-DTU-kvot.  Kontrol lera att SQL Server har tillräckligt med den återstående DTU-kvoten för databasen som återställs. Information om hur du beräknar DTU krävs eller begär mer DTU finns i [begär en ändring av DTU-kvot](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Återställa ett borttaget informations lager via PowerShell

Om du vill återställa en borttagen SQL-pool använder du cmdleten [restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) . Om motsvarande logiska server också har tagits bort kan du inte återställa det data lagret.

1. Innan du börjar ska du se till att [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Öppna PowerShell.
3. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
4. Välj den prenumeration som innehåller det borttagna informations lager som ska återställas.
5. Hämta det angivna borttagna informations lagret.
6. Återställa det borttagna informations lagret
    1. Om du vill återställa den borttagna SQL Data Warehouse till en annan logisk server, måste du ange namnet på den andra logiska servern.  Den här logiska servern kan också finnas i en annan resurs grupp och region.
    1. Om du vill återställa till en annan prenumeration använder du knappen [Flytta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal) för att flytta den logiska servern till en annan prenumeration.
1. Kontrol lera att det återställda informations lagret är online.
1. När återställningen har slutförts kan du konfigurera det återställda data lagret genom att följa [konfiguration av databasen efter återställningen](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Återställa en borttagen databas med hjälp av Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Gå till den SQL Server som du har tagit bort data lagret på.
3. Välj ikonen **borttagna databaser** i innehålls förteckningen.

    ![Borttagna databaser](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Välj den borttagna SQL Data Warehouse som du vill återställa.

    ![Välj borttagna databaser](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Ange ett nytt **databas namn** och klicka på **OK**

    ![Ange databas namn](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Nästa steg
- [Återställa en befintlig SQL-pool](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa från en geo-backup SQL-pool](sql-data-warehouse-restore-from-geo-backup.md)