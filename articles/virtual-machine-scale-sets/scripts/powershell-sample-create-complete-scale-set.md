---
title: Azure PowerShell-exempel – skapa en fullständig VM-skalningsuppsättning| Microsoft Docs
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
ms.date: 05/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5bbdcf8b45af6cf0c56af52941f205267ebdecbd
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839715"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Skapa en fullständig VM-skalningsuppsättning med PowerShell
Det här skriptet skapar en VM-skalningsuppsättning som kör Windows Server 2016. Enskilda resurser konfigureras och skapas i stället för att använda de [inbyggda alternativen för att skapa resurser som finns här i New-AzureRmVmss](powershell-sample-create-simple-scale-set.md). När skriptet har körts får du åtkomst till de virtuella datorinstanserna via RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Skapar en klientdels-IP-konfiguration för en belastningsutjämnare. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Skapar en konfiguration för serverdelsadresspool för en belastningsutjämnare. |
| [New-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Skapar en inkommande NAT-regelkonfiguration för en belastningsutjämnare. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Skapar en belastningsutjämnare. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Skapar en avsökningskonfiguration för en belastningsutjämnare. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Skapar en regelkonfiguration för en belastningsutjämnare. |
| [Set-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Set-AzureRmLoadBalancer) | Uppdatera belastningsutjämnaren med den angivna informationen. |
| [New-AzureRmVmssIpConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssIpConfig) | Skapa en IP-konfiguration för skalningsuppsättningens VM-instanser. VM-instanser är anslutna till belastningsutjämnarens serverdelspool, NAT-pool och undernät för virtuellt nätverk. |
| [New-AzureRmVmssConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssConfig) | Skapar en skalningsuppsättningskonfiguration. Den här konfigurationen innehåller information såsom antal VM-instanser att skapa, SKU för VM (storlek) och uppgraderingsläge för principer. Ytterligare cmdleter läggs till konfigurationen och används vid skapande av skalningsuppsättning. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Definiera avbildningen som ska användas för VM-instanser och lägg till det i skalningsuppsättningskonfig. |
| [Set-AzureRmVmssOsProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Definiera autentiseringsuppgifterna för administrativa användarnamn och lösenord samt VM-namnprefix. Lägg till dessa värden i skalningsuppsättningskonfig. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/AzureRM.Compute/Add-AzureRmVmssNetworkInterfaceConfiguration) | Lägg till ett virtuellt nätverkssgränssnitt i VM-instansen baserat på IP-konfigurationen. Lägg till dessa värden i skalningsuppsättningskonfig. |
| [New-AzureRmVmss](/powershell/module/AzureRM.Compute/New-AzureRmVmss) | Skapa skaluppsättningen baserat på informationen i konfigurationen för skalningsuppsättning. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för VM-skalningsuppsättningar finns i [dokumentationen för skalningsuppsättningar för virtuella Azure-datorer](../powershell-samples.md).
