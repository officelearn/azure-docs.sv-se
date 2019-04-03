---
title: PowerShell-cmdletar för Azure SQL Data Warehouse
description: Hitta de översta PowerShell-cmdletarna för Azure SQL Data Warehouse, inklusive hur du pausar och återupptar en databas.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e62f12123ae9af4f5e09d19622ba1558c2f43184
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846425"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell-cmdletar och REST API: er för SQL Data Warehouse
Många administrationsuppgifter för SQL Data Warehouse kan hanteras med hjälp av Azure PowerShell-cmdletar eller REST API: er.  Nedan följer några exempel på hur du använder PowerShell-kommandon för att automatisera vanliga uppgifter i din SQL Data Warehouse.  Några bra REST-exempel finns i artikeln [hantera skalbarhet med REST][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Kom igång med Azure PowerShell-cmdlets
1. Öppna Windows PowerShell.
2. I PowerShell-Kommandotolken kör du följande kommandon för att logga in till Azure Resource Manager och välj din prenumeration.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Pausa SQL Data Warehouse-exempel
Pausa en databas med namnet ”Database02” finns på en server med namnet ”Server01”.  Servern är i ett Azure-resursgrupp med namnet ”ResourceGroup1”.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
En variant det här exemplet kommer det hämtade objektet som ska [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase].  Därför kan har databasen pausats. Det slutliga kommandot visas resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Starta SQL Data Warehouse-exempel
Återuppta drift av en databas med namnet ”Database02” finns på en server med namnet ”Server01”. Servern finns i en resursgrupp med namnet ”ResourceGroup1”.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

En variant det här exemplet hämtar en databas med namnet ”Database02” från en server med namnet ”Server01” som finns i en resursgrupp med namnet ”ResourceGroup1”. Det rör objektet hämtades till [återuppta AzSqlDatabase][Resume-AzSqlDatabase].

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Observera att om din server är foo.database.windows.net, använder du ”foo” som - servernamn i PowerShell-cmdlets.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Andra stöd för PowerShell-cmdletar
Dessa PowerShell-cmdletar stöds med Azure SQL Data Warehouse.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoints][Get-AzSqlDatabaseRestorePoints]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Nästa steg
Mer PowerShell-exempel finns:

* [Skapa ett SQL Data Warehouse med hjälp av PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Återställning av databasen][Database restore]

Andra uppgifter som kan automatiseras med PowerShell, se [cmdlet: ar för Azure SQL Database][Azure SQL Database Cmdlets]. Observera att alla Azure SQL Database-cmdlets har stöd för Azure SQL Data Warehouse.  En lista över aktiviteter som kan automatiseras med REST finns i [åtgärder för Azure SQL Database][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/az.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoints
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
