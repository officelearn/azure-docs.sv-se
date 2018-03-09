---
title: "Ansluta virtuella nätverk med virtuella nätverk peering - PowerShell | Microsoft Docs"
description: "Lär dig mer om att ansluta virtuella nätverk med virtuella nätverk peering."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Ansluta virtuella nätverk med virtuella nätverk peering med hjälp av PowerShell

Du kan ansluta virtuella nätverk till varandra med virtuella nätverk peering. När virtuella nätverk är peerkopplat kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna som fanns i samma virtuella nätverk. Den här artikeln beskriver skapandet och peering av två virtuella nätverk. Lär dig att:

> [!div class="checklist"]
> * Skapa två virtuella nätverk
> * Skapa en peering mellan virtuella nätverk
> * Testa peering

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver Azure PowerShell Modulversion 3,6 eller senare i den här artikeln. Kör ` Get-Module -ListAvailable AzureRM` att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

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

Skapa en konfiguration av undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). I följande exempel skapas en konfiguration av undernät med ett 10.0.0.0/24 adressprefix:

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

Adressprefixet för den *myVirtualNetwork2* virtuellt nätverk inte överlappa adressprefixet för den *myVirtualNetwork1* virtuellt nätverk. Du kan inte peer-virtuella nätverk med överlappande adressprefix.

## <a name="peer-virtual-networks"></a>Peer-virtuella nätverk

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

Peerkopplingar mellan två virtuella nätverk, men är inte transitiva. I så fall till exempel om du ville peer *myVirtualNetwork2* till *myVirtualNetwork3*, måste du skapa en ytterligare peering mellan virtuella nätverk *myVirtualNetwork2* och *myVirtualNetwork3*. Även om *myVirtualNetwork1* peerkopplat med *myVirtualNetwork2*, resurser inom *myVirtualNetwork1* kan bara komma åt resurser i  *myVirtualNetwork3* om *myVirtualNetwork1* också är peerkopplat med *myVirtualNetwork3*. 

Innan peering produktion virtuella nätverk, rekommenderas att du noggrant bekanta dig med de [peering översikt](virtual-network-peering-overview.md), [hantera peering](virtual-network-manage-peering.md), och [gränser för virtuellt nätverk ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Även om den här artikeln beskrivs en peering mellan två virtuella nätverk på samma prenumeration och plats, kan du också peer virtuella nätverk i [olika regioner](#register) och [olika Azure-prenumerationer](create-peering-different-subscriptions.md#powershell). Du kan också skapa [NAV och ekrar nätverk Designer](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peering.

## <a name="test-peering"></a>Testa peering

Distribuera en virtuell dator till varje undernät och sedan kommunicera mellan de virtuella datorerna för att testa nätverkskommunikation mellan virtuella datorer i olika virtuella nätverk via en peering. 

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kontrollera kommunikationen mellan dem i ett senare steg.

Skapa en virtuell dator med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVm1* i den *myVirtualNetwork1* virtuellt nätverk. Den `-AsJob` alternativet skapar den virtuella datorn i bakgrunden så du kan fortsätta till nästa steg. När du uppmanas, anger du användarnamn och lösenord som du vill logga in på den virtuella datorn med.

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

Azure automatiskt ska tilldelas 10.0.0.4 som privata IP-adressen för den virtuella datorn, eftersom 10.0.0.4 är den första tillgängliga IP-adressen i *Undernät1* av *myVirtualNetwork1*. 

Skapa en virtuell dator i den *myVirtualNetwork2* virtuellt nätverk.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Det tar några minuter att skapa den virtuella datorn. Även om inte returnerade utdata och Azure utsetts 10.1.0.4 privata IP-adressen för den virtuella datorn, eftersom 10.1.0.4 är den första tillgängliga IP-adressen i *Undernät1* av *myVirtualNetwork2*. 

Fortsätt inte med senare steg tills Azure skapar den virtuella datorn och returnerar utdata till PowerShell.

### <a name="test-virtual-machine-communication"></a>Testa virtuell dator-kommunikation

Du kan ansluta till en virtuell dators offentlig IP-adress från Internet. Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den *myVm1* virtuell dator:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbords-session med den *myVm1* virtuell dator från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returneras från det föregående kommandot.

```
mstsc /v:<publicIpAddress>
```

En Remote Desktop Protocol (RDP)-fil skapas, hämtas till datorn och öppnas. Ange användarnamn och lösenord (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn), och klicka sedan på  **OK**. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

Från en kommandotolk, aktivera ping via Windows-brandväggen så att du kan pinga den virtuella datorn från *myVm2* i ett senare steg.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Även om ping används i den här artikeln för att testa, rekommenderas inte att tillåta ICMP via Windows-brandväggen för Produktionsdistribution.

Att ansluta till den *myVm2* virtuella datorn, anger du följande kommando från en kommandotolk den *myVm1* virtuell dator:

```
mstsc /v:10.1.0.4
```

Eftersom du har aktiverat ping på *myVm1*, du kan nu pinga den IP-adress från en kommandotolk på den *myVm2* virtuell dator:

```
ping 10.0.0.4
```

Du får fyra svar. Om du pinga den virtuella datorns namn (*myVm1*), i stället för dess IP-adress pingningen misslyckas eftersom *myVm1* är en okänd värdnamnet. Azures standard namnmatchningen fungerar mellan virtuella datorer i samma virtuella nätverk, men inte mellan virtuella datorer i olika virtuella nätverk. Om du vill matcha namnen på virtuella nätverk, måste du [Distribuera DNS-servern](virtual-networks-name-resolution-for-vms-and-role-instances.md) eller använda [privata Azure DNS-domäner](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Koppla från din RDP-sessioner till både *myVm1* och *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) att ta bort resursgruppen och alla resurser som den innehåller.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>Registrera sig för globalt virtuella nätverk peering förhandsversion**

VNET-peering i samma region är allmänt sett tillgängligt. Peering virtuella nätverk i olika regioner är för närvarande under förhandsgranskning. Se [virtuella nätverk uppdateringar](https://azure.microsoft.com/updates/?product=virtual-network) för tillgängliga regioner. To-peer-virtuella nätverk över regioner, måste du först registrera för förhandsversionen av genom att utföra följande steg (inom prenumerationen varje virtuella nätverket som du vill peer):

1. Registrera prenumerationen som varje virtuellt nätverk som du vill peer gäller i förhandsgranskningen genom att ange följande kommandon:

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. Bekräfta att du är registrerad för förhandsversionen av genom att ange följande kommando:

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Om du försöker peer-virtuella nätverk i olika regioner innan den **RegistrationState** utdata efter att ange föregående kommando är **registrerad** för båda prenumerationer peering misslyckas .

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du ansluter två nätverk med virtuella nätverk peering. Du kan [ansluta datorn till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via en VPN-anslutning och interagera med resurser i ett virtuellt nätverk eller i peerkoppla virtuella nätverk.

Fortsätta att skriptexempel för återanvändbara skript att utföra många av de uppgifter som beskrivs i artiklarna virtuellt nätverk.

> [!div class="nextstepaction"]
> [Skriptexempel för virtuellt nätverk](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
