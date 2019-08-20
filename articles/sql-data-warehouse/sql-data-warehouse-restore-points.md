---
title: Användardefinierade återställnings punkter | Microsoft Docs
description: Så här skapar du en återställnings punkt Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 9dcb8b2e9c1b75aac3c195f89777ac9c6eb030d7
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575259"
---
# <a name="user-defined-restore-points"></a>Användardefinierade återställnings punkter

I den här artikeln får du lära dig att skapa en ny användardefinierad återställnings punkt för Azure SQL Data Warehouse med PowerShell och Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Skapa användardefinierade återställnings punkter via PowerShell

Om du vill skapa en användardefinierad återställnings punkt använder du PowerShell-cmdleten [New-AzSqlDatabaseRestorePoint][New-AzSqlDatabaseRestorePoint] .

1. Innan du börjar ska du se till att [installera Azure PowerShell][Install Azure PowerShell].
2. Öppna PowerShell.
3. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
4. Välj den prenumeration som innehåller den databas som ska återställas.
5. Skapa en återställnings punkt för en omedelbar kopia av ditt informations lager.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Se listan över alla befintliga återställnings punkter.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Skapa användardefinierade återställnings punkter via Azure Portal

Användardefinierade återställnings punkter kan också skapas via Azure Portal.

1. Logga in på ditt [Azure Portal][Azure portal] -konto.

2. Navigera till SQL Data Warehouse som du vill skapa en återställnings punkt för.

3. Välj **Översikt** i den vänstra rutan och välj **+ ny återställnings punkt**. Om knappen Ny återställnings punkt inte är aktive rad kontrollerar du att informations lagret inte har pausats.

    ![Ny återställningspunkt](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Ange ett namn för den användardefinierade återställnings punkten och klicka på **Använd**. Användardefinierade återställnings punkter har en standard kvarhållningsperiod på sju dagar.

    ![Namn på återställnings punkt](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa ett befintligt informations lager][Restore an existing data warehouse]
- [Återställa ett borttaget informations lager][Restore a deleted data warehouse]
- [Återställa från ett informations lager för geo-säkerhetskopiering][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
