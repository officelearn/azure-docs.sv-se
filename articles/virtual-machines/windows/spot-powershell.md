---
title: Använd PowerShell för att distribuera virtuella Azure-datorer
description: Lär dig hur du använder Azure PowerShell för att distribuera virtuella datorer för virtuella datorer för att spara pengar.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 321983fbe99d17dc78198feb195eed8ea26de569
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100625"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Distribuera virtuella datorer med hjälp av Azure PowerShell


Med hjälp av [virtuella datorer](spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till betydande besparingar. Vid alla tidpunkter när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort virtuella datorer. De virtuella datorerna är därför fantastiska för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Priser för virtuella datorer i virtuella datorer är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Mer information om hur du ställer in högsta pris finns i [VM-priser för virtuella datorer](spot-vms.md#pricing).

Du har möjlighet att ange ett högsta pris som du är villig att betala per timme för den virtuella datorn. Det maximala priset för en VM-VM kan anges i USD (USD) med upp till 5 decimaler. Värdet `0.98765`skulle till exempel vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1`kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en spotVM med [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) för att skapa konfigurationen. Inkludera `-Priority Spot` och ange `-MaxPrice` antingen:
- `-1`Det innebär att den virtuella datorn inte avlägsnas utifrån priset.
- en dollar mängd, upp till 5 siffror. Till exempel `-MaxPrice .98765` innebär att den virtuella datorn kommer att frigöras när priset för en spotVM går ungefär $. 98765 per timme.


I det här exemplet skapas en spotVM som inte frigörs baserat på priser (endast när Azure behöver kapaciteten tillbaka).

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

När den virtuella datorn har skapats kan du fråga om du vill se det högsta priset för alla virtuella datorer i resurs gruppen.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa en virtuell dator med hjälp av [Azure CLI](../linux/spot-cli.md) eller en [mall](../linux/spot-template.md).

Om du stöter på ett fel, se [felkoder](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).