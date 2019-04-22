---
title: PowerShell-exempel på geo-replikering av redundans för enkel grupp med Azure SQL Database | Microsoft Docs
description: Azure PowerShell-exempelskript som konfigurerar aktiv geo-replikering av redundansgrupp för en enskild databas i Azure SQL Database och sedan redundansväxlar den.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 421972303ae6d63adf8f4a26af626a13e20a411d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359134"
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-database-in-azure-sql-database"></a>Använd PowerShell för att konfigurera en aktiv geo-replikering av en redundansgrupp för en enkel databas i Azure SQL Database

Det här PowerShell-skriptexemplet konfigurerar en aktiv geo-replikering av en redundansgrupp för en enkel databas och redundansväxlar den till en sekundär replik av databasen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-database-failover-group.ps1?highlight=18-21 "Set up failover group for single database")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk pool. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Uppdaterar databasegenskaper eller flyttar en databas till, från eller mellan elastiska pooler. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Skapar en sekundär databas för en befintlig databas och startar datareplikeringen. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Hämtar en eller flera databaser. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Växlar en sekundär databas till att vara primär för att initiera redundans.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Hämtar geo-replikeringslänkar mellan en Azure SQL Database och en resursgrupp eller SQL Server. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) | Avslutar datareplikering mellan en SQL Database och den angivna sekundära databasen. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny Azure SQL Database-redundansgrupp för de angivna servrarna. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Växlar roller på servrarna i redundansgruppen och växlar alla sekundära databaser till den primära rollen. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar en specifik Azure SQL Database-redundansgrupp eller listar redundansgrupperna på en server. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
