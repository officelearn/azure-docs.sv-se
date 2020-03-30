---
title: Konfigurera skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer - Azure PowerShell
description: Lär dig hur du konfigurerar en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349994"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Konfigurera en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer med Azure PowerShell

I den här artikeln får du lära dig hur du konfigurerar en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer. 

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration.
- En befintlig standard sku belastningsutjämnare i prenumerationen där den virtuella datorn skala set kommer att distribueras.
- Ett Virtuellt Azure-nätverk för skalningsuppsättningen för den virtuella datorn.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI

Logga in på Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuera en skalningsuppsättning för virtuella datorer med befintlig belastningsutjämnare

Ersätt värdena inom parentes med namnen på resurserna i konfigurationen.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

I exemplet nedan distribueras en skalningsuppsättning för virtuella datorer med:

- Skala uppsättning för virtuell dator med namnet **myVMSS**
- Azure Load Balancer heter **myLoadBalancer**
- Backend-backend-pool med namnet **myBackendPool**
- Azure Virtual Network heter **myVnet**
- Undernät med namnet **mySubnet**
- Resursgrupp med namnet **myResourceGroup**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> När skalningsuppsättningen har skapats kan serverdaporten inte ändras för en belastningsutjämningsregel som används av en hälsoavsökning av belastningsutjämnaren. Om du vill ändra porten kan du ta bort hälsoavsökningen genom att uppdatera Azure-skalauppsättningen för virtuella datorer, uppdatera porten och sedan konfigurera hälsoavsökningen igen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer.  Mer information om skalningsuppsättningar och belastningsutjämnare för virtuella datorer finns i:

- [Vad är Azure Load Balancer?](load-balancer-overview.md)
- [Vad är VM-skalningsuppsättningar?](../virtual-machine-scale-sets/overview.md)
                                