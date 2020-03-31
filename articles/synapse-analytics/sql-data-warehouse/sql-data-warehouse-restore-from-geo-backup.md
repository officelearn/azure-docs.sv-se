---
title: Återställa ett informationslager från en geo-säkerhetskopiering
description: Så här guidar du för att geo-återställa en SQL-pool.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4390ed39c86e041d3fbd776415f0ffbe71f605bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350167"
---
# <a name="geo-restore-for-sql-pool"></a>Geoåterställning för SQL-pool

I den här artikeln lär du dig att återställa din SQL-pool från en geo-säkerhetskopiering via Azure portal och PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje SQL-pool är värd för en SQL-server (till exempel myserver.database.windows.net) som har en DTU-standardkvot. Kontrollera att SQL-servern har tillräckligt med återstående DTU-kvot för databasen som återställs. Mer information om hur du beräknar DTU som behövs eller begär mer DTU finns i [Begär en DTU-kvotändring](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Återställa från en geografisk Azure-region via PowerShell

Om du vill återställa från en geo-säkerhetskopiering använder du cmdleten [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) och [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> Du kan utföra en geo-återställning till Gen2! Om du vill göra det anger du ett Gen2 ServiceObjectiveName (t.ex.**c**
>

1. Innan du börjar, se till att [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Öppna PowerShell.
2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
3. Välj den prenumeration som innehåller det informationslager som ska återställas.
4. Hämta det informationslager som du vill återställa.
5. Skapa återställningsbegäran för informationslagret.
6. Kontrollera status för det geoåterda datalager.
7. Informationsstället när återställningen har slutförts finns i [Konfigurera databasen efter återställning]( ../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Den återställda databasen är TDE-aktiverad om källdatabasen är TDE-aktiverad.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Återställa från en geografisk Azure-region via Azure-portalen

Följ stegen nedan för att återställa en SQL-pool från en geo-backup:

1. Logga in på ditt [Azure-portalkonto.](https://portal.azure.com/)
1. Klicka på **+ Skapa en resurs**. 

![Nya DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Klicka på **Databaser** och sedan **Azure Synapse Analytics (tidigare SQL DW) **.

![Nya DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Fyll i den information som **begärs** på fliken Grunderna och klicka på **Nästa: Ytterligare inställningar**.

![Grundläggande inställningar](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. För **Använd befintlig dataparameter** väljer du **Säkerhetskopiering** och väljer lämplig säkerhetskopiering i alternativen för rullning nedåt. Klicka på **Granska + Skapa**.
 
![säkerhetskopiering](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. När informationslagret har återställts kontrollerar du att **statusen** är online.

## <a name="next-steps"></a>Efterföljande moment
- [Återställa en befintlig SQL-pool](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa en borttagen SQL-pool](sql-data-warehouse-restore-deleted-dw.md)