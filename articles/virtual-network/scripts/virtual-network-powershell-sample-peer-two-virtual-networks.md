---
title: Skriptexempel Azure PowerShell - Peer två virtuella nätverk | Microsoft Docs
description: Skriptexempel Azure PowerShell - Peer två virtuella nätverk
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: c0efdf759a0bdb87de4dc8ff9566a8e817503c5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="peer-two-virtual-networks"></a>Peer-två virtuella nätverk

Det här skriptet skapar och ansluter två virtuella nätverk i samma region via Azure-nätverk. När du har kört skriptet skapar du en peering mellan två virtuella nätverk.

Du kan köra skriptet från Azure [moln Shell](https://shell.azure.com/powershell), eller från en lokal installation av PowerShell. Om du använder PowerShell lokalt skriptet kräver AzureRM PowerShell Modulversion 5.4.1 eller senare. Du hittar den installerade versionen genom att köra `Get-Module -ListAvailable AzureRM`. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i följande tabell länkar till kommandot viss dokumentation:

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. | 
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)| Skapar ett virtuellt Azure-nätverk och undernät. |
| [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) | Skapar en peering mellan två virtuella nätverk.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare virtuella nätverk PowerShell-skript-exempel finns i [virtuella nätverk PowerShell-exempel](../powershell-samples.md).