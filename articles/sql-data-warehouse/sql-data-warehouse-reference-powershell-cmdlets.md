---
title: 'PowerShell & REST API: er'
description: Hitta de främsta PowerShell-cmdletarna för Azure Synapse Analytics SQL-poolen, inklusive hur du pausar och återupptar en databas.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0c8b1e9b7526bd45d037f053715613b53ec163f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198464"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell & REST API: er för Azure Synapse Analytics SQL-pool

Många administrativa uppgifter i Azure Synapse Analytics SQL-poolen kan hanteras med hjälp av antingen Azure PowerShell-cmdlets eller REST-API: er.  Nedan visas några exempel på hur du använder PowerShell-kommandon för att automatisera vanliga uppgifter i SQL-poolen.  En del användbara exempel finns i artikeln [Hantera skalbarhet med rest](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Kom igång med Azure PowerShell-cmdletar

1. Öppna Windows PowerShell.
2. Kör dessa kommandon i PowerShell-prompten för att logga in på Azure Resource Manager och välj din prenumeration.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Exempel på Pausa data lager
Pausa en databas med namnet "Database02" som finns på en server med namnet "Server01".  Servern finns i en Azure-resurs grupp med namnet "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

En variation, det här exemplet rör det hämtade objektet för att [pausa-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Databasen pausas därför. Det slutliga kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Exempel på Start data lager

Återuppta åtgärden för en databas med namnet "Database02" som finns på en server med namnet "Server01". Servern finns i en resurs grupp med namnet "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

En variation, det här exemplet hämtar en databas med namnet "Database02" från en server med namnet "Server01" som finns i en resurs grupp med namnet "ResourceGroup1". Den rör det hämtade objektet för att [återuppta-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Observera att om servern är foo.database.windows.net använder du "foo" som-ServerName i PowerShell-cmdletarna.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Andra PowerShell-cmdletar som stöds
Dessa PowerShell-cmdletar stöds med Azure Synapse Analytics data Warehouse.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Pausa – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Nästa steg
Fler PowerShell-exempel finns i:

* [Skapa ett informations lager med PowerShell](create-data-warehouse-powershell.md)
* [Återställning av databas](sql-data-warehouse-restore-database-powershell.md)

För andra uppgifter som kan automatiseras med PowerShell, se [Azure SQL Database-cmdletar](https://docs.microsoft.com/powershell/module/az.sql). Alla Azure SQL Database-cmdlets stöds inte för Azure Synapse Analytics-datalagret.  En lista över uppgifter som kan automatiseras med REST finns i [åtgärder för Azure SQL Database](/rest/api/sql/).
