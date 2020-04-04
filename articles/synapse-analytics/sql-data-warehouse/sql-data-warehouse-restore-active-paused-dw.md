---
title: Återställa ett befintligt informationslager
description: Så här kan du styra för att återställa en befintlig SQL-pool.
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
ms.openlocfilehash: e1d0c5cd850fa0dbacf104d3fcd72d83007df878
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632258"
---
# <a name="restore-an-existing-sql-pool"></a>Återställa en befintlig SQL-pool

I den här artikeln får du lära dig hur du återställer en befintlig SQL-pool i Azure Synapse Analytics med Azure-portalen och PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje pool är värd för en SQL-server (till exempel myserver.database.windows.net) som har en DTU-standardkvot. Kontrollera att SQL-servern har tillräckligt med återstående DTU-kvot för databasen som återställs. Mer information om hur du beräknar DTU som behövs eller begär mer DTU finns i [Begär en DTU-kvotändring](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Innan du börjar

1. Se till att [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Har en befintlig återställningspunkt som du vill återställa från. Om du vill skapa en ny återställning läser du [självstudien för att skapa en ny användardefinierad återställningspunkt](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Återställa en befintlig SQL-pool via PowerShell

Om du vill återställa en befintlig SQL-pool från en återställningspunkt använder du cmdleten [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell.

1. Öppna PowerShell.

2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.

3. Välj den prenumeration som innehåller databasen som ska återställas.

4. Lista återställningspunkterna för SQL-poolen.

5. Välj önskad återställningspunkt med RestorePointCreationDate.

6. Återställ SQL-poolen till önskad återställningspunkt med Hjälp av [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell-cmdlet.
        1. Om du vill återställa SQL-poolen till en annan logisk server kontrollerar du att det andra logiska servernamnet anges.  Den här logiska servern kan också finnas i en annan resursgrupp och region.
        2. Om du vill återställa till en annan prenumeration använder du knappen Flytta för att flytta den logiska servern till en annan prenumeration.

7. Kontrollera att den återställda SQL-poolen är online.

8. När återställningen har slutförts kan du konfigurera den återställda SQL-poolen genom att [följa konfigurera databasen efter återställning](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Återställa en befintlig SQL-pool via Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till den SQL-pool som du vill återställa från.
3. Högst upp på bladet Översikt väljer du **Återställ**.

    ![ Återställa översikt](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Välj antingen **Automatiska återställningspunkter** eller **användardefinierade återställningspunkter**. Om SQL-poolen inte har några automatiska återställningspunkter väntar du några timmar eller skapar en användardefinierad återställningspunkt innan du återställer. För användardefinierade återställningspunkter väljer du en befintlig eller skapar en ny. För **Server**kan du välja en logisk server i en annan resursgrupp och region eller skapa en ny. När du har tillhandahållit alla parametrar klickar du på **Granska + Återställ**.

    ![Automatiska återställningspunkter](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Efterföljande moment

- [Återställa en borttagen SQL-pool](sql-data-warehouse-restore-deleted-dw.md)
- [Återställa från en SQL-pool med geo säkerhetskopiering](sql-data-warehouse-restore-from-geo-backup.md)
