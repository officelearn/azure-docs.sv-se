---
title: Återställa ett informations lager från en geo-säkerhetskopiering
description: Instruktions guide för geo-återställning av en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 69ba3ed981a27dfff41ea9ea52e1da769a9366c4
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759643"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Geo-Restore-Azure SQL Data Warehouse

I den här artikeln får du lära dig att återställa ditt informations lager från en geo-säkerhetskopiering via Azure Portal och PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje SQL Data Warehouse hanteras av en SQL-Server (till exempel myserver.database.windows.net) som har en standard-DTU-kvot. Kontrol lera att SQL Server har tillräckligt med den återstående DTU-kvoten för databasen som återställs. Information om hur du beräknar DTU krävs eller begär mer DTU finns i [begär en ändring av DTU-kvot](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Återställa från en geografisk Azure-region via PowerShell

Om du vill återställa från en geo-säkerhets kopiering använder du cmdleten [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) och [restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

> [!NOTE]
> Du kan utföra en geo-återställning till Gen2! Det gör du genom att ange en Gen2-ServiceObjectiveName (t. ex. DW1000**c**) som en valfri parameter.
>

1. Innan du börjar ska du se till att [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Öppna PowerShell.
2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
3. Välj den prenumeration som innehåller det data lager som ska återställas.
4. Hämta det data lager som du vill återställa.
5. Skapa en begäran om återställning av data lagret.
6. Kontrol lera statusen för det geo-återställda informations lagret.
7. Information om hur du konfigurerar ditt informations lager när återställningen har slutförts finns i [Konfigurera din databas efter återställning]( ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

Den återställda databasen kommer att TDEs om käll databasen är TDE-aktive rad.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Återställa från en geografisk region i Azure via Azure Portal

Följ stegen som beskrivs nedan för att återställa en Azure SQL Data Warehouse från en geo-säkerhets kopiering:

1. Logga in på ditt [Azure Portal](https://portal.azure.com/) -konto.
1. Klicka på **+ skapa en resurs** och sök efter SQL Data Warehouse och klicka på **skapa**.

    ![Ny DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Fyll i informationen som begärs på fliken **grundläggande** och klicka på **Nästa: ytterligare inställningar**.

    ![Grundläggande inställningar](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Om du vill **använda befintlig data** parameter väljer du **säkerhets kopiering** och väljer lämplig säkerhets kopia från rullnings List alternativen. Klicka på **Granska + skapa**.
 
   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. När data lagret har återställts kontrollerar du att **statusen** är online.

## <a name="next-steps"></a>Efterföljande moment
- [Återställa ett befintligt informations lager](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa ett borttaget informations lager](sql-data-warehouse-restore-deleted-dw.md)