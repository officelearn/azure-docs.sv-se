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
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e4f6d26c119a113bab0220828c847f438e7bc7a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
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
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Skapar ett konfigurationsobjekt som definierar det virtuella nätverkets undernät. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Skapar ett virtuellt nätverk och undernät. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Skapar en statisk offentlig IP-adress. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Skapar ett konfigurationsobjekt som definierar belastningsutjämnarens klientdel, inklusive offentlig IP-adress. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Skapar ett konfigurationsobjekt som definierar belastningsutjämnarens serverdel. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Skapar en belastningsutjämnare baserat på konfigurationsobjekten för klientdelen och serverdelen. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Skapar en hälsoavsökning för belastningsutjämnaren för att övervaka trafik på TCP-port 80. Om två efterföljande fel med 15-sekunder intervall påträffas så anses slutpunkten som skadad. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Skapar ett konfigurationsobjekt som definierar regler för belastningsutjämning för att distribuera trafik på TCP-port 80 från serverdelens pool till serverdelens pool. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Uppdaterar belastningsutjämnaren med reglerna för hälsoavsökning och belastningsutjämning. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Skapar ett konfigurationsobjekt som definierar en regel för nätverkssäkerhetsgruppen för att tillåta trafik på TCP-port 80. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp och regel. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Uppdaterar det virtuella nätverksundernätet att associerar med nätverkssäkerhetsgruppen. Alla virtuella datorinstanser anslutna till undernätet ärver reglerna för nätverkssäkerhetsgrupper. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Uppdaterar det virtuella nätverket med konfigurationen för undernätet och nätverkssäkerhetsgruppen. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | Skapar ett konfigurationsobjekt som definierar IP-informationen för VM-skalningsuppsättningen för att ansluta virtuella datorinstanser till belastningsutjämnarens serverdelspool och inkommande NAT-pool.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | Skapar ett konfigurationsobjekt som definierar antalet virtuella datorinstanser i en skalningsuppsättning, den virtuella datorinstansens storlek och läge för uppgraderingsprincipen. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Skapar ett konfigurationsobjekt som definierar virtuell datoravbildning att använda för virtuella datorinstanser. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Skapar en konfigurationsprofil som definierar den virtuella datorinstansens namn och autentiseringsuppgifter. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Skapar ett konfigurationsobjekt som definierar det virtuella nätverkskortet för varje virtuell datorinstans. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Kombinerar alla konfigurationsobjekt för att skapa VM-skalningsuppsättningen. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Hämtar information om en VM-skalningsuppsättning. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Lägger till ett virtuellt datortillägg för att det anpassade skriptet ska installera ett grundläggande webbprogram. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Uppdaterar modellen för VM-skalningsuppsättningen för att använda det virtuella datortillägget. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Hämtar information om den offentliga IP-adress som används av belastningsutjämnaren. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för VM-skalningsuppsättningar finns i [dokumentationen för skalningsuppsättningar för virtuella Azure-datorer](../powershell-samples.md).