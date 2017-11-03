---
title: PowerShell exempel-skapa en Azure SQL database | Microsoft Docs
description: "Azure PowerShell-exempelskript för att skapa en Azure SQL-databas"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: d74e2bc799bda6d5355e47b726b1e97d0b604fcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Använda PowerShell för att skapa en Azure SQL-databas och konfigurera en brandväggsregel

Exempel på detta PowerShell-skript skapar en Azure SQL database och konfigurerar en brandväggsregel på servernivå. När skriptet har körts utan problem, kan SQL-databasen nås från alla Azure-tjänster och den konfigurerade IP-adressen. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/sql-database/create-and-configure-database/create-and-configure-database.ps1?highlight=13-14 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Rensa distribution

Följande kommando kan användas för att ta bort resursgruppen och alla resurser som är associerade med den efter skriptexempel har körts.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Ny AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Skapar en logisk server som är värd för en databas eller elastisk pool. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | Skapar en brandväggsregel för att tillåta åtkomst till alla SQL-databaser på servern från det angivna IP-adressintervallet. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Skapar en databas i en logisk server som en enda eller en delad databas. |
| [Ta bort AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare exempel för SQL Database PowerShell-skript finns i den [Azure SQL Database PowerShell-skript](../sql-database-powershell-samples.md).



