---
title: PowerShell-exempel- Redundansgrupp - Elastisk Azure SQL-databaspool
description: Azure PowerShell exempel skript för att skapa en Azure SQL Database elastisk pool, lägga till den i en redundansgrupp och testa redundans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: a8c9b7dbddba59408808e1c33de045ad0b7ffd7a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691785"
---
# <a name="use-powershell-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Använda PowerShell för att lägga till en elastisk Azure SQL-databaspool i en redundansgrupp 

Det här PowerShell-skriptexemplet skapar en enskild databas, lägger till den i en elastisk pool, skapar en redundansgrupp och testar redundans. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Ny-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för en logisk server. | 
| [Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en ny enda Azure SQL Database-databas. | 
| [Ny-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk databaspool för en Azure SQL-databas.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool. | 
| [Nya-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny redundansgrupp. |
| [Hämta AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hämtar en eller flera SQL-databaser. |
| [Tillägg-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL-databaser i en redundansgrupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Redundansgrupper för Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundans för en redundansgrupp för Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
