---
title: Användardefinierade återställnings punkter
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
ms.custom: seo-lt-2019
ms.openlocfilehash: bf092b5b6c6eb88b565a940de56d614426e34d8e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759609"
---
# <a name="user-defined-restore-points"></a>Användardefinierade återställnings punkter

I den här artikeln får du lära dig att skapa en ny användardefinierad återställnings punkt för Azure SQL Data Warehouse med PowerShell och Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Skapa användardefinierade återställnings punkter via PowerShell

Om du vill skapa en användardefinierad återställnings punkt använder du PowerShell-cmdleten [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) .

1. Innan du börjar ska du se till att [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
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

2. Navigera till SQL Data Warehouse som du vill skapa en återställnings punkt för.

3. Välj **Översikt** i den vänstra rutan och välj **+ ny återställnings punkt**. Om knappen Ny återställnings punkt inte är aktive rad kontrollerar du att informations lagret inte har pausats.

    ![Ny återställnings punkt](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Ange ett namn för den användardefinierade återställnings punkten och klicka på **Använd**. Användardefinierade återställnings punkter har en standard kvarhållningsperiod på sju dagar.

    ![Namn på återställnings punkt](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa ett befintligt informations lager](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa ett borttaget informations lager](sql-data-warehouse-restore-deleted-dw.md)
- [Återställa från ett informations lager för geo-säkerhetskopiering](sql-data-warehouse-restore-from-geo-backup.md)

