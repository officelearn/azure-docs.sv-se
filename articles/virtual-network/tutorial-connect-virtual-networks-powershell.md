---
title: Ansluta virtuella nätverk med virtuella nätverk peering - PowerShell | Microsoft Docs
description: I den här artikeln får veta du hur du ansluter virtuella nätverk med ett virtuellt nätverk peering, med hjälp av Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3b4a67a06d628040d155a0fe2d78beb2eee25090
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Ansluta virtuella nätverk med virtuella nätverk peering med hjälp av PowerShell

Du kan ansluta virtuella nätverk till varandra med peerkoppling. När virtuella nätverk har peerkopplats kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna fanns i samma virtuella nätverk. I den här artikeln kan du se hur du:

* Skapa två virtuella nätverk
* Koppla samman två virtuella nätverk med en peerkoppling
* Distribuera en virtuell dator (VM) till varje virtuellt nätverk
* Kommunicera mellan virtuella datorer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt i den här artikeln kräver Azure PowerShell Modulversion 5.4.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="create-virtual-networks"></a>Skapa virtuella nätverk

Du måste skapa en resursgrupp för det virtuella nätverket och alla andra resurser som skapats i den här artikeln innan du skapar ett virtuellt nätverk. Skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork1* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Skapa en undernätskonfiguration med [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). I följande exempel skapas en konfiguration av undernät med ett 10.0.0.0/24 adressprefix:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Skriva undernät-konfigurationen till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), vilket skapar undernätet:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Skapa ett virtuellt nätverk med ett 10.1.0.0/16 adressprefix och ett undernät:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Peerkoppla virtuella nätverk

Skapa en peering med [Lägg till AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). Följande exempel motparterna *myVirtualNetwork1* till *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

I utdatan när körs det föregående kommandot visas som den **PeeringState** är *initierade*. Peering förblir i det *initierade* tillstånd förrän du skapar peering från *myVirtualNetwork2* till *myVirtualNetwork1*. Skapa en peering från *myVirtualNetwork2* till *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

I utdatan när körs det föregående kommandot visas som den **PeeringState** är *ansluten*. Azure även ändrat peering tillståndet för den *myVirtualNetwork1 myVirtualNetwork2* peering till *ansluten*. Bekräfta att peering tillståndet för den *myVirtualNetwork1 myVirtualNetwork2* peering ändras till *ansluten* med [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Resurser i ett virtuellt nätverk inte kan kommunicera med resurser i det andra virtuella nätverket förrän den **PeeringState** för peerkopplingar i båda virtuella nätverken är *ansluten*. 

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kommunicera mellan dem i ett senare steg.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVm1* i den *myVirtualNetwork1* virtuellt nätverk. Den `-AsJob` alternativet skapar den virtuella datorn i bakgrunden så du kan fortsätta till nästa steg. När du uppmanas, anger du användarnamn och lösenord som du vill logga in på den virtuella datorn med.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med senare steg tills Azure skapar den virtuella datorn och returnerar utdata till PowerShell.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

Du kan ansluta till en virtuell dators offentlig IP-adress från internet. Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) för att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den virtuella datorn *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbords-session med den *myVm1* virtuell dator från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returnerades från föregående kommando.

```
mstsc /v:<publicIpAddress>
```

En Remote Desktop Protocol (RDP)-fil skapas, hämtas till datorn och öppnas. Ange användarnamn och lösenord (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn), och klicka sedan på **OK** . Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

På den *myVm1* VM, aktivera den kontrollen meddelandet ICMP (Internet Protocol) via Windows-brandväggen så att du kan pinga den här virtuella datorn från *myVm2* i ett senare steg med hjälp av PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Du rekommenderas att inte tillåta ICMP via Windows-brandväggen för Produktionsdistribution om ping används för kommunikation mellan virtuella datorer i den här artikeln.

Du ansluter till den virtuella datorn *myVm2* genom att ange följande kommando från en kommandotolk på den virtuella datorn *myVm1*:

```
mstsc /v:10.1.0.4
```

Eftersom du har aktiverat ping på *myVm1*, du kan nu pinga den IP-adress från en kommandotolk på den *myVm2* VM:

```
ping 10.0.0.4
```

Du får fyra svar. Koppla från RDP-sessionerna till både *myVm1* och *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) att ta bort resursgruppen och alla resurser som den innehåller.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du ansluter två nätverk i samma Azure-regionen med virtuella nätverk peering. Du kan också peerkoppla virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region) och i [olika Azure-prenumerationer](create-peering-different-subscriptions.md#powershell), samt skapa [nav- och ekernätverksdesigner](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peerkoppling. Mer information om peerkoppling av virtuella nätverk finns i [Översikt över peerkoppling av virtuella nätverk](virtual-network-peering-overview.md) och [Hantera peerkopplingar av virtuella nätverk](virtual-network-manage-peering.md).

Du kan [ansluta datorn till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via en VPN-anslutning och interagera med resurser i ett virtuellt nätverk eller i peerkoppla virtuella nätverk. Återanvändbara skript att utföra många av de uppgifter som beskrivs i de virtuella nätverket artiklarna finns [skript exempel](powershell-samples.md).
