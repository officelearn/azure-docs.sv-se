---
title: Azure PowerShell skriptexempel - belastningsutjämna trafiken till virtuella datorer för hög tillgänglighet | Microsoft Docs
description: Azure PowerShell skriptexempel - belastningsutjämna trafiken till virtuella datorer för hög tillgänglighet
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 61d65cbdcf7867cc7a2a225648dc8b1ab7137bcb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31595981"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Läs in Utjämna trafiken till virtuella datorer för hög tillgänglighet

Det här exemplet i skriptet skapar allt som behövs för att köra flera Windows-datorer som konfigurerats i en hög tillgänglighet och läsa in belastningsutjämnade konfigurationen. När du har kört skriptet har du tre virtuella datorer som är anslutna till en Azure-tillgänglighetsuppsättning och är tillgängliga via Azure Load Balancer.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) och kör sedan `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, belastningsutjämnare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)  | Skapar en offentlig IP-adress med en statisk IP-adress och ett tillhörande DNS-namn. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer)  | Skapar en Azure belastningsutjämnare. |
| [Ny AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Skapar en belastningsutjämningsavsökning. En belastningsutjämningsavsökning används för att övervaka varje virtuell dator i uppsättningen av belastningen belastningsutjämnaren. Om en virtuell dator blir otillgänglig dirigeras trafiken förbi den. |
| [Ny AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Skapar en regel för belastningsutjämning. I det här exemplet skapas en regel för port 80. När HTTP-trafik anländer på belastningsutjämnaren, dirigeras till port 80 något av de virtuella datorerna i uppsättningen av belastningen belastningsutjämnaren. |
| [Ny AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Skapar en belastningsutjämningsregel för NAT (Network Address Translation).  NAT-regler kan du mappa en port för belastningsutjämnaren till en port på en virtuell dator. I det här exemplet skapas en NAT-regel för SSH-trafik på varje virtuell dator i uppsättningen av belastningen belastningsutjämnaren.  |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp (NSG), som är en säkerhetsgräns mellan internet och den virtuella datorn. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Skapar en NSG-regel för att tillåta inkommande trafik. I det här exemplet öppnas port 22 för SSH-trafik. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Skapar ett virtuellt nätverkskort och ansluter det till det virtuella nätverket, undernätet och NSG. |
| [Ny AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Skapar en tillgänglighetsuppsättning. Tillgänglighetsuppsättningar garanterar programmets drifttid genom att fördela virtuella datorer mellan fysiska resurser så att hela uppsättningen inte berörs om något fel inträffar. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)  | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Kommandot specificerar även vilken avbildning av virtuell dator som ska användas samt administrativa autentiseringsuppgifter.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare nätverk PowerShell-skript-exempel finns i den [dokumentation för Azure-nätverk – översikt](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
