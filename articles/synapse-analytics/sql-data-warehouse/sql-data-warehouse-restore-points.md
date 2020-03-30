---
title: Användardefinierade återställningspunkter
description: Så här skapar du en återställningspunkt för SQL-pool.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d056b30c44ced5f3e8ce9041e2366290bee485da
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350151"
---
# <a name="user-defined-restore-points"></a>Användardefinierade återställningspunkter

I den här artikeln får du lära dig att skapa en ny användardefinierad återställningspunkt för en SQL-pool i Azure Synapse Analytics med Hjälp av PowerShell och Azure-portalen.

## <a name="create-user-defined-restore-points-through-powershell"></a>Skapa användardefinierade återställningspunkter via PowerShell

Om du vill skapa en användardefinierad återställningspunkt använder du cmdleten [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) PowerShell.

1. Innan du börjar, se till att [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Öppna PowerShell.
3. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
4. Välj den prenumeration som innehåller databasen som ska återställas.
5. Skapa en återställningspunkt för en omedelbar kopia av ditt informationslager.

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

6. Se listan över alla befintliga återställningspunkter.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Skapa användardefinierade återställningspunkter via Azure-portalen

Användardefinierade återställningspunkter kan också skapas via Azure-portalen.

1. Logga in på ditt [Azure-portalkonto.](https://portal.azure.com/)

2. Navigera till den SQL-pool som du vill skapa en återställningspunkt för.

3. Välj **Översikt** i den vänstra rutan, välj **+ Ny återställningspunkt**. Om knappen Ny återställningspunkt inte är aktiverad kontrollerar du att SQL-poolen inte pausas.

    ![Ny återställningspunkt](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Ange ett namn för den användardefinierade återställningspunkten och klicka på **Använd**. Användardefinierade återställningspunkter har en standardkvarhållningsperiod på sju dagar.

    ![Namn på återställningspunkt](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Nästa steg

- [Återställa en befintlig SQL-pool](sql-data-warehouse-restore-active-paused-dw.md)
- [Återställa en borttagen SQL-pool](sql-data-warehouse-restore-deleted-dw.md)
- [Återställa från en SQL-pool med geo säkerhetskopiering](sql-data-warehouse-restore-from-geo-backup.md)

