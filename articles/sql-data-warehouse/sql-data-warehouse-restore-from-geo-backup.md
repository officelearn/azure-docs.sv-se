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
ms.openlocfilehash: 69eb1221686da61868df8b06ed80664ae76d1627
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685499"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Geo-Restore-Azure SQL Data Warehouse

I den här artikeln får du lära dig att återställa ditt informations lager från en geo-säkerhetskopiering via Azure Portal och PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifiera din DTU-kapacitet.** Varje SQL Data Warehouse hanteras av en SQL-Server (till exempel myserver.database.windows.net) som har en standard-DTU-kvot. Kontrol lera att SQL Server har tillräckligt med den återstående DTU-kvoten för databasen som återställs. Information om hur du beräknar DTU krävs eller begär mer DTU finns i [begär en ändring av DTU-kvot][Request a DTU quota change].

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Återställa från en geografisk Azure-region via PowerShell

Om du vill återställa från en geo-säkerhets kopiering använder du cmdleten [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] och [restore-AzSqlDatabase][Restore-AzSqlDatabase] .

> [!NOTE]
> Du kan utföra en geo-återställning till Gen2! Det gör du genom att ange en Gen2-ServiceObjectiveName (t. ex. DW1000**c**) som en valfri parameter.
>

1. Innan du börjar ska du se till att [installera Azure PowerShell][Install Azure PowerShell].
2. Öppna PowerShell.
2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
3. Välj den prenumeration som innehåller det data lager som ska återställas.
4. Hämta det data lager som du vill återställa.
5. Skapa en begäran om återställning av data lagret.
6. Kontrol lera statusen för det geo-återställda informations lagret.
7. Information om hur du konfigurerar ditt informations lager när återställningen har slutförts finns i [Konfigurera din databas efter återställning][Configure your database after recovery].

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

1. Logga in på ditt [Azure Portal][Azure portal] -konto.
1. Klicka på **+ skapa en resurs** och sök efter SQL Data Warehouse och klicka på **skapa**.

    ![Ny DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Fyll i informationen som begärs på fliken **grundläggande** och klicka på **Nästa: ytterligare inställningar**.

    ![Grundläggande inställningar](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Om du vill **använda befintlig data** parameter väljer du **säkerhets kopiering** och väljer lämplig säkerhets kopia från rullnings List alternativen. Klicka på **Granska + skapa**.
 
   ![säkerhetskopiering](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. När data lagret har återställts kontrollerar du att **statusen** är online.

## <a name="next-steps"></a>Nästa steg
- [Återställa ett befintligt informations lager][Restore an existing data warehouse]
- [Återställa ett borttaget informations lager][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
