---
title: Användardefinierade återställnings punkter
description: Så här skapar du en återställnings punkt för dedikerad SQL-pool.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9d7266e0b84ae57682ddcfe7195be9574a702c74
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313254"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Användardefinierade återställnings punkter för en dedikerad SQL-pool i Azure Synapse Analytics

I den här artikeln får du lära dig att skapa en ny användardefinierad återställnings punkt för en dedikerad SQL-pool i Azure Synapse Analytics med hjälp av PowerShell och Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Skapa användardefinierade återställnings punkter via PowerShell

Om du vill skapa en användardefinierad återställnings punkt använder du PowerShell-cmdleten [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

1. Innan du börjar ska du se till att [installera Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
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

1. Logga in på ditt [Azure Portal](https://portal.azure.com/) -konto.

2. Navigera till den dedikerade SQL-pool som du vill skapa en återställnings punkt för.

3. Välj **Översikt** i den vänstra rutan och välj **+ ny återställnings punkt**. Om knappen Ny återställnings punkt inte är aktive rad kontrollerar du att den dedikerade SQL-poolen inte är pausad.

    ![Ny återställnings punkt](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Ange ett namn för den användardefinierade återställnings punkten och klicka på **Använd**. Användardefinierade återställnings punkter har en standard kvarhållningsperiod på sju dagar.

    ![Namn på återställnings punkt](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa en befintlig dedikerad SQL-pool](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa en borttagen dedikerad SQL-pool](sql-data-warehouse-restore-deleted-dw.md)
- [Återställa från en dedikerad geo-säkerhetskopia SQL-pool](sql-data-warehouse-restore-from-geo-backup.md)

