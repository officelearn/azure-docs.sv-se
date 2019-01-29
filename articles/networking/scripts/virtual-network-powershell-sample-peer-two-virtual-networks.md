---
title: Exempel på Azure PowerShell-skript – Peering i två virtuella nätverk | Microsoft Docs
description: Exempel på Azure PowerShell-skript – Peering i två virtuella nätverk
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 8a892f109b368d7ff0d4ec2ad9174bb99114fd7c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100163"
---
# <a name="peer-two-virtual-networks"></a>Peerkoppla två virtuella nätverk

Det här skriptet skapar och ansluter två virtuella nätverk i samma region via Azures nätverk. När du har kört skriptet har du en peering mellan två virtuella nätverk.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) och kör sedan `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. | 
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)| Skapar ett virtuellt Azure-nätverk och undernät. |
| [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering) | Skapar en peering mellan två virtuella nätverk.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare exempel på PowerShell-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
