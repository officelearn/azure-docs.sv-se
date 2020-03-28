---
title: PowerShell-exempel-återställning-backup-Azure SQL-databas
description: Azure PowerShell-exempelskript för att återställa en enda Azure SQL-databas till en tidigare tidpunkt från automatiska säkerhetskopieringar
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/27/2019
ms.openlocfilehash: da4236e138bd75237ca10b85dc1586fecd1cece4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73691549"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-to-an-earlier-point-in-time"></a>Använda PowerShell för att återställa en enda Azure SQL-databas till en tidigare tidpunkt

Det här PowerShell-skriptexemplet återställer en Azure SQL-databas till en viss tidpunkt.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Ny-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapa en SQL Database-server som är värd för en enskild databas eller en elastisk pool. |
| [Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en databas på en SQL Database-server i form av en fristående databas eller en pooldatabas. |
[Hämta AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Hämtar en geo-redundant säkerhetskopia av en fristående databas eller en pooldatabas. |
| [Återställ-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Återställer en fristående SQL-databas eller en SQL-pooldatabas. |
|[Ta bort-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Tar bort en fristående Azure SQL-databas eller en Azure SQL-pooldatabas. |
| [Hämta-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hämtar en borttagen databas (fristående databas eller pooldatabas) som du kan återställa. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
