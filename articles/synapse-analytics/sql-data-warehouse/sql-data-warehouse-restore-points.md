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
ms.openlocfilehash: 5c9b7eb1b03b6b6e3721c13f9ebf7da25dd2e376
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745070"
---
# <a name="user-defined-restore-points"></a>Användardefinierade återställningspunkter

I den här artikeln får du lära dig att skapa en ny användardefinierad återställningspunkt för en SQL-pool i Azure Synapse Analytics med Hjälp av PowerShell och Azure-portalen.

## <a name="create-user-defined-restore-points-through-powershell"></a>Skapa användardefinierade återställningspunkter via PowerShell

Om du vill skapa en användardefinierad återställningspunkt använder du cmdleten [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell.

1. Innan du börjar, se till att [installera Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
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

