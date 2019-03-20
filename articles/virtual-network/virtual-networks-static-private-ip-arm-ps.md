---
title: Skapa en virtuell dator med en statisk privat IP-adress – Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator med en privat IP-adress med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 812b3752bfffd16c09b466b036fb0ac91e77d5a4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104360"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-powershell"></a>Skapa en virtuell dator med en statisk privat IP-adress med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skapa en virtuell dator (VM) med en statisk privat IP-adress. Tilldela en statisk privat IP-adress i stället för en dynamisk adress om du vill välja vilken adress från ett undernät är tilldelad till en virtuell dator. Läs mer om [Statiska privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#allocation-method). Ändra en privat IP-adress som tilldelats till en befintlig virtuell dator från dynamisk till statisk, eller arbeta med offentliga IP-adresser i avsnittet [Lägg till, ändra eller ta bort IP-adresser](virtual-network-network-interface-addresses.md).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Du kan utföra följande steg från din lokala dator eller genom att använda Azure Cloud Shell. Om du vill använda din lokala dator, se till att du har den [Azure PowerShell installerad](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill använda Azure Cloud Shell, Välj **prova** i det övre högra hörnet i vilken ruta för kommandot som följer. Cloud Shell loggar du in på Azure.

1. Om du använder Cloud Shell, kan du gå vidare till steg 2. Öppna en kommandosession och inloggning i Azure med `Connect-AzAccount`.
2. Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp i regionen östra USA Azure:

   ```azurepowershell-interactive
   $RgName = "myResourceGroup"
   $Location = "eastus"
   New-AzResourceGroup -Name $RgName -Location $Location
   ```

3. Skapa en konfiguration av undernät och virtuellt nätverk med den [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) och [New AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) kommandon:

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

4. Skapa ett nätverksgränssnitt i det virtuella nätverket och tilldela en privat IP-adress från undernätet till nätverksgränssnittet med den [New-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/New-AzNetworkInterfaceIpConfig) och [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) kommandon:

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

5. Skapa en VM-konfiguration med [New-AzVMConfig](/powershell/module/Az.Compute/New-AzVMConfig), och sedan skapa den virtuella datorn med [New-AzVM](/powershell/module/az.Compute/New-azVM). När du uppmanas, anger du ett användarnamn och lösenord som ska användas som autentiseringsuppgifter för inloggning för den virtuella datorn:

   ```azurepowershell-interactive
   $VirtualMachine = New-AzVMConfig -VMName MyVM -VMSize "Standard_DS3"
   $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName MyServerVM -ProvisionVMAgent -EnableAutoUpdate
   $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
   $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest
   New-AzVM -ResourceGroupName $RgName -Location $Location -VM $VirtualMachine -Verbose
   ```

> [!WARNING]
> Även om du kan lägga till privata IP-adressinställningarna för operativsystemet, vi rekommenderar att du inte gör det tills när du har läst [lägga till en privat IP-adress till ett operativsystem](virtual-network-network-interface-addresses.md#private).
> 
> 
> <a name = "change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>
> 
> [!IMPORTANT]
> För att komma åt den virtuella datorn från internet, måste du tilldela en offentlig IP-adress till den virtuella datorn. Du kan också ändra en dynamisk privat IP-adresstilldelning till en statisk tilldelning. Mer information finns i [Lägg till eller ändra IP-adresser](virtual-network-network-interface-addresses.md). Dessutom rekommenderar vi att du begränsar nätverkstrafiken till den virtuella datorn genom att associera en nätverkssäkerhetsgrupp till nätverksgränssnittet, det undernät du skapade nätverksgränssnittet i eller båda. Mer information finns i [hantera nätverkssäkerhetsgrupper](manage-network-security-group.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) att ta bort resursgruppen och alla resurser den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [privata IP-adresser](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) och tilldela en [statiska privata IP-adressen](virtual-network-network-interface-addresses.md#add-ip-addresses) till en Azure-dator.
- Läs mer om hur du skapar [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuella datorer.
