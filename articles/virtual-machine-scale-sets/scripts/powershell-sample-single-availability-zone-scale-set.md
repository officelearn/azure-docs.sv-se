---
title: Azure PowerShell-exempel – skalningsuppsättningar med en zon | Microsoft Docs
description: Azure PowerShell-exempel
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6cecd5f45c7e663ff70564ea26b76d07f9cd606d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Skapa en VM-skalningsuppsättning med en zon med PowerShell
Det här skriptet skapar en VM-skalningsuppsättning som kör Windows Server 2016 i en enda tillgänglighetszon. När du kört skriptet har du åtkomst till den virtuella datorn över RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Skapar VM-skalningsuppsättningen och alla stödresurser, inklusive virtuellt nätverk, belastningsutjämning och NAT-regler. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Hämtar information om en VM-skalningsuppsättning. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Lägger till ett virtuellt datortillägg för att det anpassade skriptet ska installera ett grundläggande webbprogram. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Uppdaterar modellen för VM-skalningsuppsättningen för att använda det virtuella datortillägget. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Hämtar information om den offentliga IP-adress som används av belastningsutjämnaren. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för VM-skalningsuppsättningar finns i [dokumentationen för skalningsuppsättningar för virtuella Azure-datorer](../powershell-samples.md).