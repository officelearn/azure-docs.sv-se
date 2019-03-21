---
title: PowerShell-exempel – aktiv geo-replikering – Azure SQL Databas i pool| Microsoft Docs
description: Azure PowerShell-exempelskript som konfigurerar aktiv geo-replikering för en poolad databas i Azure SQL Database och sedan redundansväxlar den.
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
ms.openlocfilehash: 8789e6c45940891e51638f9711fbcf66259882b1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842251"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-pooled-database-in-azure-sql-database"></a>Använda PowerShell för att konfigurera aktiv geo-replikering för en poolad databas i Azure SQL Database

Det här PowerShell-skriptexemplet konfigurerar aktiv geo-replikering för en poolad databas i Azure SQL Database och redundansväxlar den till den sekundära repliken av databasen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.ps1?highlight=17-20 "Set up active geo-replication for elastic pool")]

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

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
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en enkel databas eller en poolad databas. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Uppdaterar databasegenskaper eller flyttar en databas till, från eller mellan elastiska pooler. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Skapar en sekundär databas för en befintlig databas och startar datareplikeringen. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Hämtar en eller flera databaser. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Växlar en sekundär databas till att vara primär för att initiera redundans.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Hämtar geo-replikeringslänkar mellan en Azure SQL Database och en resursgrupp eller SQL Server. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
