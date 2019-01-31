---
title: Skapa och hantera Azure VPN-gateway med hjälp av PowerShell | Microsoft Docs
description: Självstudie – Skapa och hantera en VPN-gateway med Azure PowerShell-modulen
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/14/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 17c8a55c27a276fa1e2e04ebb9f748fa6d59a9dc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55204073"
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Skapa och hantera en VPN-gateway med Azure PowerShell-modulen

Azure VPN-gatewayer ger anslutningar mellan olika platser, t.ex. mellan kundens lokaler och Azure. Den här självstudien beskriver grundläggande distributionsobjekt i Azure VPN-gatewayen, till exempel att skapa och hantera en VPN-gateway. Lär dig att:

> [!div class="checklist"]
> * Skapa en VPN-gateway
> * Ändra storlek på en VPN-gateway
> * Återställ en VPN gateway

Följande diagram visar det virtuella nätverket och den VPN-gateway som skapats som en del av den här självstudien.

![Virtuella nätverk och VPN-gateway](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell och Azure PowerShell

[!INCLUDE [working with cloudshell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien version 5.3 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="common-network-parameter-values"></a>Gemensamma parametervärden för nätverk

Ändra värdena baserat på din miljö och nätverkskonfiguration.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas först. I följande exempel skapas en resursgrupp med namnet *TestRG1* i regionen *Östra USA*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Azure VPN-gatewayen ger anslutning mellan olika platser och P2S VPN-serverfunktioner för det virtuella nätverket. Lägg till VPN-gatewayen i ett befintligt virtuellt nätverk eller skapa ett nytt virtuellt nätverk och gatewayen. I det här exemplet skapas ett nytt virtuellt nätverk med tre undernät: Frontend, Backend och GatewaySubnet med hjälp av [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) och [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Begär en offentlig IP-adress för VPN-gatewayen

Azure VPN-gatewayer kommunicerar med dina lokala VPN-enheter via Internet för att utföra IKE-förhandlingar (Internet Key Exchange) och upprätta IPsec-tunnlar. Skapa och tilldela en offentlig IP-adress till VPN-gatewayen enligt exemplet nedan med [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) och [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> För närvarande kan du endast använda en dynamisk offentlig IP-adress för gatewayen. Statiska IP-adresser stöds inte på Azure VPN-gatewayer.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>Skapa VPN-gateway

Det kan ta minst 45 minuter att skapa en VPN-gateway. När gatewayen har skapats kan du skapa en anslutning mellan ditt virtuella nätverk och ett annat virtuellt nätverk. Du kan också skapa en anslutning mellan ditt virtuella nätverk och en lokal plats. Skapa en VPN-gateway med cmdleten [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Värden för nyckelparameter:
* GatewayType: Använd **Vpn** för anslutningar från plats till plats och mellan virtuella nätverk
* VpnType: Använd **RouteBased** för att interagera med ett bredare urval av VPN-enheter och fler routningsfunktioner
* GatewaySku: **VpnGw1** är standard. Ändra till VpnGw2 eller VpnGw3 om du behöver högre dataflöden eller fler anslutningar. Se [Gateway SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för mer information.

När gatewayen har skapats kan du skapa en anslutning mellan ditt virtuella nätverk och ett annat virtuellt nätverk, eller skapa en anslutning mellan ditt virtuella nätverk och en lokal plats. Du kan också konfigurera en P2S-anslutning till ditt virtuella nätverk från en klientdator.

## <a name="resize-vpn-gateway"></a>Ändra storlek på VPN-gatewayen

Du kan ändra VPN-gatewayens SKU när gatewayen har skapats. Olika gateway-SKU:er stöder olika specifikationer som t.ex. dataflöden, antal anslutningar osv. I följande exempel används [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) till att ändra storlek på din gateway från VpnGw1 till VpnGw2. Se [Gateway SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för mer information.

```azurepowershell-interactive
$gateway = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Att ändra storlek på en VPN-gateway tar också cirka 30 till 45 minuter, även om den här åtgärden **inte** avbryter eller tar bort befintliga anslutningar och konfigurationer.

## <a name="reset-vpn-gateway"></a>Återställa VPN-gatewayen

Som en del av felsökningsstegen kan du återställa din Azure VPN-gateway för att tvinga VPN-gatewayen att starta om konfigurationerna för IPsec/IKE-tunneln. Använd [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway) till att återställa din gateway.

```azurepowershell-interactive
$gateway = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Mer information finns i [Återställa en VPN-gateway](vpn-gateway-resetgw-classic.md).

## <a name="get-the-gateway-public-ip-address"></a>Hämta den offentliga IP-adressen för gatewayen

Om du känner till namnet på den offentliga IP-adressen kan du använda [Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-6.8.1) till att visa den offentliga IP-adress som är tilldelad till gatewayen.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>Ta bort VPN-gatewayen

En fullständig konfiguration av anslutningar mellan olika platser och mellan virtuella nätverk kräver flera resurstyper förutom VPN-gatewayen. Ta bort de anslutningar som är associerade med VPN-gatewayen innan du tar bort själva gatewayen. När gatewayen har tagits bort kan du ta bort den offentliga IP-adressen för gatewayen. Se [Ta bort en VPN-gateway](vpn-gateway-delete-vnet-gateway-powershell.md) för detaljerade anvisningar.

Om gatewayen ingår i en prototyp- eller Proof of Concept-distribution, kan du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) till att ta bort resursgruppen, VPN-gatewayen och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om grundläggande skapande av VPN-gatewayer och hantering, till exempel att:

> [!div class="checklist"]
> * Skapa en VPN-gateway
> * Ändra storlek på en VPN-gateway
> * Återställ en VPN gateway

Gå vidare till följande självstudier för att lära dig mer om anslutningar mellan servrar och mellan virtuella nätverk, samt P2S-anslutningar.

> [!div class="nextstepaction"]
> * [Skapa anslutningar mellan servrar](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Skapa anslutningar mellan virtuella nätverk](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Skapa P2S-anslutningar](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
