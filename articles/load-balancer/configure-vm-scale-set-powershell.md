---
title: Konfigurera skalnings uppsättning för virtuell dator med en befintlig Azure Load Balancer-Azure PowerShell
description: Lär dig hur du konfigurerar en skalnings uppsättning för en virtuell dator med en befintlig Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349994"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Konfigurera en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer att använda Azure PowerShell

I den här artikeln får du lära dig hur du konfigurerar en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer. 

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration.
- En befintlig SKU för standard-SKU i prenumerationen där den virtuella datorns skalnings uppsättning ska distribueras.
- En Azure-Virtual Network för skalnings uppsättningen för den virtuella datorn.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI

Logga in på Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuera en skalnings uppsättning för virtuell dator med befintlig belastningsutjämnare

Ersätt värdena inom hakparenteser med namnen på resurserna i konfigurationen.

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

I exemplet nedan distribueras en skalnings uppsättning för virtuella datorer med:

- Skalnings uppsättning för virtuell dator med namnet **myVMSS**
- Azure Load Balancer med namnet **myLoadBalancer**
- Backend-pool för belastningsutjämnare med namnet **myBackendPool**
- Azure Virtual Network med namnet **myVnet**
- Undernät med namnet mina **undernät**
- Resurs grupp med namnet **myResourceGroup**

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
> När skalnings uppsättningen har skapats kan Server dels porten inte ändras för en belastnings Utjämnings regel som används av en hälso avsökning av belastningsutjämnaren. Om du vill ändra porten kan du ta bort hälso avsökningen genom att uppdatera skalnings uppsättningen för den virtuella Azure-datorn, uppdatera porten och sedan konfigurera hälso avsökningen igen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer.  Mer information om skalnings uppsättningar för virtuella datorer och belastningsutjämnare finns i:

- [Vad är Azure Load Balancer?](load-balancer-overview.md)
- [Vad är VM-skalningsuppsättningar?](../virtual-machine-scale-sets/overview.md)
                                