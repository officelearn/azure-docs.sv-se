---
title: Azure PowerShell-skriptexempel – Skapa en NLB för en virtuell Windows-dator | Microsoft Docs
description: Azure PowerShell-skriptexempel – Skapa en NLB för en virtuell Windows-dator
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: ddcea1063d955875ced916d2486774e6227dc301
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470650"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Belastningsutjämna trafiken mellan virtuella datorer med hög tillgänglighet

Det här skriptexemplet skapar allt som behövs för att köra flera virtuella Windows Server 2016-datorer, konfigurerade med hög tillgänglighet och belastningsutjämning. När du har kört skriptet har du tre virtuella datorer som är anslutna till en Azure-tillgänglighetsuppsättning och är tillgängliga via Azure Load Balancer.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Create VM NLB")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Skapar en klientdels-IP-konfiguration för en lastbalanserare. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Skapar en konfiguration för serverdelsadresspool för en lastbalanserare. |
| [New-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Skapar en avsökningskonfiguration för en lastbalanserare. |
| [New-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Skapar en regelkonfiguration för en lastbalanserare. |
| [New-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Skapar en inkommande NAT-regelkonfiguration för en lastbalanserare. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Skapar en lastbalanserare. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Skapar en regelkonfiguration för nätverkssäkerhetsgrupper. Konfigurationen används för att skapa en NSG-regel när NSG:n skapas. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp. |
| [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | Hämtar information om undernät. Den här informationen används när du skapar ett nätverksgränssnitt. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Skapar ett nätverksgränssnitt. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Skapa en virtuell dator. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Windows-datorer](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
