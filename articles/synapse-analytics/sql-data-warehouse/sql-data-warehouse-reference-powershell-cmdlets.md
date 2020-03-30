---
title: PowerShell-& REST-API:er
description: Hitta de översta PowerShell-cmdlets för Azure Synapse Analytics SQL-pool, inklusive hur du pausar och återupptar en databas.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351389"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell & REST-API:er för Azure Synapse Analytics SQL-pool

Många administrativa Azure Synapse Analytics SQL-pool-uppgifter kan hanteras med antingen Azure PowerShell-cmdlets eller REST-API:er.  Nedan följer några exempel på hur du använder PowerShell-kommandon för att automatisera vanliga uppgifter i DIN SQL-pool.  Några bra REST-exempel finns i artikeln [Hantera skalbarhet med REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Komma igång med Azure PowerShell-cmdlets

1. Öppna Windows PowerShell.
2. I PowerShell-prompten kör du dessa kommandon för att logga in på Azure Resource Manager och välja din prenumeration.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Pausa informationslagerexempel
Pausa en databas med namnet "Database02" som finns på en server med namnet "Server01".  Servern finns i en Azure-resursgrupp med namnet "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

En variant, det här exemplet rör det hämtade objektet till [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Därför pausas databasen. Det slutliga kommandot visar resultaten.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Starta exempel på informationslager

Återuppta driften av en databas med namnet "Database02" som finns på en server med namnet "Server01". Servern finns i en resursgrupp med namnet "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

En variant, det här exemplet hämtar en databas med namnet "Database02" från en server med namnet "Server01" som finns i en resursgrupp med namnet "ResourceGroup1". Det rör det hämtade objektet till [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Observera att om servern är foo.database.windows.net använder du "foo" som -ServerName i PowerShell-cmdletar.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Andra PowerShell-cmdlets som stöds
Dessa PowerShell-cmdlets stöds med Azure Synapse Analytics-informationslagret.

* [Hämta AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Hämta-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Hämta AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [Ny-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Ta bort-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Återställ-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Meritförteckning-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Välj-azabonnemang](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Nästa steg
Mer PowerShell-exempel finns i:

* [Skapa ett informationslager med PowerShell](create-data-warehouse-powershell.md)
* [Återställning av databas](sql-data-warehouse-restore-points.md)

Andra uppgifter som kan automatiseras med PowerShell finns i [Azure SQL Database cmdlets](https://docs.microsoft.com/powershell/module/az.sql). Alla Azure SQL Database-cmdlets stöds inte för Azure Synapse Analytics-datalager.  En lista över uppgifter som kan automatiseras med REST finns i [Åtgärder för Azure SQL Database](/rest/api/sql/).
