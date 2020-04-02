---
title: Använda PowerShell för att distribuera virtuella Azure Spot-datorer
description: Lär dig hur du använder Azure PowerShell för att distribuera spot-datorer för att spara på kostnader.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 234cf3f51173c53ef8ca15af4ca6f24881be3109
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547274"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Distribuera spot-datorer med Azure PowerShell


Med hjälp av [Spot virtuella datorer](spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till en betydande kostnadsbesparingar. När som helst när Azure behöver tillbaka kapaciteten kommer Azure-infrastrukturen att ta bort spot-datorer. Därför är spot-virtuella datorer bra för arbetsbelastningar som kan hantera avbrott som batchbearbetningsjobb, utvecklings-/testmiljöer, stora beräkningsarbetsbelastningar med mera.

Priserna för spot-virtuella datorer varierar, baserat på region och SKU. Mer information finns i VM-priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Mer information om hur du anger maxpriset finns i [Spot-virtuella datorer - Prissättning](spot-vms.md#pricing).

Du har möjlighet att ställa in ett maxpris som du är villig att betala, per timme, för den virtuella datorn. Maxpriset för en spot-VM kan ställas in i US-dollar (USD), med upp till 5 decimaler. Värdet `0.98765`skulle till exempel vara ett maxpris på 0,98765 USD per timme. Om du ställer in `-1`maxpriset så kommer den virtuella datorn inte att vräkas baserat på priset. Priset för den virtuella datorn blir det aktuella priset för avista eller priset för en vanlig virtuell dator, som någonsin är mindre, så länge det finns kapacitet och kvot tillgänglig.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en spotVM med [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) för att skapa konfigurationen. Inkludera `-Priority Spot` och `-MaxPrice` ställ in på antingen:
- `-1`så den virtuella datorn inte vräkas baserat på pris.
- en dollar belopp, upp till 5 siffror. Innebär till `-MaxPrice .98765` exempel att den virtuella datorn kommer att frigöras när priset för en spotVM går ca $.98765 per timme.


Det här exemplet skapar en spotVM som inte kommer att hanteras baserat på prissättning (endast när Azure behöver tillbaka kapaciteten).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

När den virtuella datorn har skapats kan du fråga för att se maxpriset för alla virtuella datorer i resursgruppen.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa en spot-vm med [Azure CLI](../linux/spot-cli.md) eller en [mall](../linux/spot-template.md).

Om du stöter på ett fel läser du [Felkoder](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).