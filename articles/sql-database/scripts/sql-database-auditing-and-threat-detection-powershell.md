---
title: PowerShell exempel granskning threat detection Azure SQL Database | Microsoft Docs
description: Azure PowerShell-exempelskript konfigurera auditing & threat detection i en Azure SQL Database
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,security
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 09/08/2017
ms.author: janeng
ms.openlocfilehash: da4c5cd2c18e62ae40fe1c3f0a7fa014163b351c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-threat-detection"></a>Använd PowerShell för att konfigurera granskning och hotidentifiering identifiering av SQL-databas

Exempel på detta PowerShell-skript konfigurerar gransknings- och hot identifiering av SQL-databas. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=13-14 "Configure auditing and threat detection")]

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
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Skapar en databas i en logisk server som en enda eller en delad databas. |
| [Ny AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Skapar ett lagringskonto. |
| [Ange AzureRmSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabaseauditingpolicy) | Anger granskningsprincipen för en databas. |
| [Ange AzureRmSqlDatabaseThreatDetectionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasethreatdetectionpolicy) | Anger en princip för identifiering av hotet för en databas. |
| [Ta bort AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare exempel för SQL Database PowerShell-skript finns i den [Azure SQL Database PowerShell-skript](../sql-database-powershell-samples.md).
