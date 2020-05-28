---
title: PowerShell-exempel – övervaka-en enkel databas i Azure SQL Database
description: Azure PowerShell exempel skript för att övervaka och skala en enskild databas i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9fa70e5888ecde8437c7d6ea6bbd52827eb6639d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054073"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Använd PowerShell för att övervaka och skala en enskild databas i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Det här PowerShell-skriptexemplet övervakar prestandavärden för en databas, skalar ut den till en större beräkningsstorlek och skapar en varningsregel för ett av prestandamåtten.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell-1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=15-16 "Monitor and scale single database")]

> [!NOTE]
> En fullständig lista över mått finns i mått som [stöds](../../../azure-monitor/platform/metrics-supported.md#microsoftsqlserversdatabases).
> [!TIP]
> Använd [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) för att hämta status för databas åtgärder och Använd [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity) för att avbryta en databas uppdaterings åtgärd.

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för en enskild databas eller elastisk pool. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Visar användningsinformation om storlek för databasen.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Uppdaterar databasegenskaper eller flyttar en databas till, från eller mellan elastiska pooler. |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Ställer in en varnings regel för att automatiskt övervaka mått i framtiden. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skript exempel finns i [Azure PowerShell skript](../powershell-script-content-guide.md).
