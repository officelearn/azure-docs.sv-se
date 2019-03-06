---
title: PowerShell-exempel – Belastningsutjämning av flera webbplatser med Azure PowerShell | Microsoft Docs
description: Det här exemplet på Azure PowerShell-skript visar hur du belastningsutjämnar flera webbplatser på samma virtuella dator
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: 6bedba2c68c05ee7ed6518996e54d007f3e83dc1
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591652"
---
# <a name="azure-powershell-script-example-load-balance-multiple-websites"></a>Azure PowerShell-skriptexempel: Belastningsutjämning för flera webbplatser

Det här exemplet på Azure PowerShell-skript skapar ett virtuellt nätverk med två virtuella datorer som är medlemmar i en tillgänglighetsuppsättning. En lastbalanserare dirigerar trafik för två olika IP-adresser till de två virtuella datorerna. När du har kört skriptet kan du distribuera webbserverprogramvara för virtuella datorer och vara värd för flera webbplatser, var och en med egen IP-adress.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuellt nätverk, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Skapar en Azure-tillgänglighetsuppsättning för att ge hög tillgänglighet. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Skapar en klientdels-IP-konfiguration för en lastbalanserare. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Skapar en konfiguration för serverdelsadresspool för en lastbalanserare. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Skapar en NLB-probe. En NLB-probe används för att övervaka de enskilda virtuella datorerna i NLB-uppsättningen. Om en virtuell dator blir otillgänglig dirigeras trafiken förbi den. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Skapar en NLB-regel. I det här exemplet skapas en regel för port 80. När HTTP-trafik kommer in till NLB dirigeras den till port 80 på någon av de virtuella datorerna i NLB-uppsättningen. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Skapar en lastbalanserare. |
| [New-AzNetworkInterfaceIpConfig](/powershell/module/az.network/new-aznetworkinterfaceipconfig) | Definierar avancerade funktioner för ett virtuellt nätverksgränssnitt. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar ett nätverksgränssnitt. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapa en virtuell dator. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare exempel på PowerShell-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
