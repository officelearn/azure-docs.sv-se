---
title: Skapa en virtuell dator med en statisk privat IP-adress - Azure PowerShell
description: Lär dig hur du skapar en virtuell dator med en privat IP-adress med PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2019
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1745ca176fac18b4903686cb556670531ee40a1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244763"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Skapa en virtuell dator med en statisk privat IP-adress med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skapa en virtuell dator (VM) med en statisk privat IP-adress. Tilldela en statisk privat IP-adress i stället för en dynamisk adress om du vill välja vilken adress från ett undernät som tilldelas en virtuell dator. Läs mer om [statiska privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Information om hur du ändrar en privat IP-adress som tilldelats en befintlig virtuell dator från dynamisk till statisk eller för att arbeta med offentliga IP-adresser finns [i Lägga till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan slutföra följande steg från din lokala dator eller genom att använda Azure Cloud Shell. Om du vill använda den lokala datorn kontrollerar du att du har [Azure PowerShell installerat](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill använda Azure Cloud Shell väljer du **Prova det** i det övre högra hörnet av en kommandoruta som följer. Cloud Shell loggar in dig i Azure.

1. Om du använder Cloud Shell, hoppa till steg 2. Öppna en kommandosession och `Connect-AzAccount`logga in på Azure med .
2. Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp i regionen Östra USA Azure:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Skapa en undernätskonfiguration och ett virtuellt nätverk med [kommandona New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) och [New-AzVirtualNetwork:](/powershell/module/az.network/new-azvirtualnetwork)

   ```azurepowershell-interactive
   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object for use in a later step.
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

4. Skapa ett nätverksgränssnitt i det virtuella nätverket och tilldela en privat IP-adress från undernätet till nätverksgränssnittet med kommandona [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) och [New-AzNetworkInterface:](/powershell/module/az.network/new-aznetworkinterface)

   ```azurepowershell-interactive
   $IpConfigName1 = "IPConfig-1"
   $IpConfig1     = New-AzNetworkInterfaceIpConfig `
     -Name $IpConfigName1 `
     -Subnet $Subnet `
     -PrivateIpAddress 10.0.0.4 `
     -Primary

   $NIC = New-AzNetworkInterface `
     -Name MyNIC `
     -ResourceGroupName $RgName `
     -Location $Location `
     -IpConfiguration $IpConfig1
   ```

5. Skapa en [VM-konfiguration med New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig)och skapa sedan den virtuella datorn med [New-AzVM](/powershell/module/az.Compute/New-azVM). Ange ett användarnamn och lösenord som ska användas som inloggningsuppgifter för den virtuella datorn när du uppmanas att göra det:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Även om du kan lägga till privata IP-adressinställningar i operativsystemet rekommenderar vi att du inte gör det förrän du har läst [Lägg till en privat IP-adress i ett operativsystem](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> För att komma åt den virtuella datorn från internet måste du tilldela en offentlig IP-adress till den virtuella datorn. Du kan också ändra en dynamisk privat IP-adresstilldelning till en statisk tilldelning. Mer information finns i [Lägga till eller ändra IP-adresser](virtual-network-network-interface-addresses.md). Dessutom rekommenderar vi att du begränsar nätverkstrafiken till den virtuella datorn genom att associera en nätverkssäkerhetsgrupp till nätverksgränssnittet, undernätet som du skapade nätverksgränssnittet i eller både och. Mer information finns i [Hantera nätverkssäkerhetsgrupper](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla resurser som den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldela en [statisk privat IP-adress](virtual-network-network-interface-addresses.md#add-ip-addresses) till en virtuell Azure-dator.
- Läs mer om hur du skapar virtuella [Linux-](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows-datorer.](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
