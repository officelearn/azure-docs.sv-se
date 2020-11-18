---
title: Självstudie – Skapa och hantera en gateway med Azure VPN Gateway
description: Följ den här självstudien för att lära dig att skapa, distribuera och hantera en Azure-VPN Gateway med hjälp av PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: b70a3fe4884ef209e57fbb954c27aa83486b5c98
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661009"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Självstudie: skapa och hantera en VPN-gateway med PowerShell

Azure VPN-gatewayer ger anslutningar mellan olika platser, t.ex. mellan kundens lokaler och Azure. Den här självstudien beskriver grundläggande distributionsobjekt i Azure VPN-gatewayen, till exempel att skapa och hantera en VPN-gateway. Lär dig att:

> [!div class="checklist"]
> * Skapa en VPN-gateway
> * Visa den offentliga IP-adressen
> * Ändra storlek på en VPN-gateway
> * Återställ en VPN gateway

Följande diagram visar det virtuella nätverket och den VPN-gateway som skapats som en del av den här självstudien.

:::image type="content" source="./media/vpn-gateway-tutorial-create-gateway-powershell/gateway-diagram.png" alt-text="Diagram över VNet-och VPN-gateway":::

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Gemensamma parametervärden för nätverk

Nedan visas de parameter värden som används för den här självstudien. Variablerna i exemplen översätts till följande:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Ändra värdena nedan baserat på din miljö och nätverkskonfiguration. Kopiera och klistra sedan in variablerna för den här självstudien. Om tidsgränsen uppnås för Cloud Shell-sessionen, eller om du måste använda ett annat PowerShell-fönster, kopierar du och klistrar in variablerna i den nya sessionen och fortsätter självstudien.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
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

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas först. I följande exempel skapas en resursgrupp med namnet *TestRG1* i regionen *USA, östra*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Azure VPN-gatewayen ger anslutning mellan olika platser och P2S VPN-serverfunktioner för det virtuella nätverket. Lägg till VPN-gatewayen i ett befintligt virtuellt nätverk eller skapa ett nytt virtuellt nätverk och gatewayen. Observera att exemplet anger namnet på Gateway-undernätet specifikt. Du måste alltid ange namnet på Gateway-undernätet som "GatewaySubnet" för att det ska fungera korrekt. Det här exemplet skapar ett nytt virtuellt nätverk med tre undernät: frontend, backend och GatewaySubnet med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) och [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Begär en offentlig IP-adress för VPN-gatewayen

Azure VPN-gatewayer kommunicerar med dina lokala VPN-enheter via Internet för att utföra IKE-förhandlingar (Internet Key Exchange) och upprätta IPsec-tunnlar. Skapa och tilldela en offentlig IP-adress till VPN-gatewayen enligt exemplet nedan med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) och [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> För närvarande kan du endast använda en dynamisk offentlig IP-adress för gatewayen. Statiska IP-adresser stöds inte på Azure VPN-gatewayer.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Skapa en VPN-gateway

Det kan ta minst 45 minuter att skapa en VPN-gateway. När gatewayen har skapats kan du skapa en anslutning mellan ditt virtuella nätverk och ett annat virtuellt nätverk. Du kan också skapa en anslutning mellan ditt virtuella nätverk och en lokal plats. Skapa en VPN-gateway med cmdleten [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Värden för nyckelparameter:
* GatewayType: Använd **Vpn** för anslutningar från plats till plats och mellan virtuella nätverk
* VpnType: Använd **RouteBased** för att interagera med ett bredare urval av VPN-enheter och fler routningsfunktioner
* GatewaySku: **VpnGw1** är standard. ändra den till en annan VpnGw-SKU om du behöver högre data flöden eller fler anslutningar. Se [Gateway SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för mer information.

Om du använder TryIt kan det ta lång tid att kontakta sessionen. Det är ok. Gatewayen kommer fortfarande att skapas.

När gatewayen har skapats kan du skapa en anslutning mellan ditt virtuella nätverk och ett annat virtuellt nätverk, eller skapa en anslutning mellan ditt virtuella nätverk och en lokal plats. Du kan också konfigurera en P2S-anslutning till ditt virtuella nätverk från en klientdator.

## <a name="view-the-gateway-public-ip-address"></a>Se den offentliga IP-adressen för gatewayen

Om du känner till namnet på den offentliga IP-adressen kan du använda [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) till att visa den offentliga IP-adress som är tilldelad till gatewayen.

Om tidsgränsen för sessionen har uppnåtts, kopierar du de gemensamma nätverksparametrarna från början av den här självstudien i den nya sessionen och går vidare och fortsätter.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway

Du kan ändra VPN-gatewayens SKU när gatewayen har skapats. Olika Gateway-SKU: er har stöd för olika specifikationer, till exempel data flöden, antal anslutningar osv. I följande exempel används [ändra storlek-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) för att ändra storlek på gatewayen från VpnGw1 till VpnGw2. Se [Gateway SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för mer information.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Att ändra storlek på en VPN-gateway tar också cirka 30 till 45 minuter, även om den här åtgärden **inte** avbryter eller tar bort befintliga anslutningar och konfigurationer.

## <a name="reset-a-gateway"></a>Återställa en gateway

Som en del av felsökningsstegen kan du återställa din Azure VPN-gateway för att tvinga VPN-gatewayen att starta om konfigurationerna för IPsec/IKE-tunneln. Använd [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) till att återställa din gateway.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Mer information finns i [Återställa en VPN-gateway](./reset-gateway.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du går [vidare till nästa självstudie](./vpn-gateway-create-site-to-site-rm-powershell.md)ska du behålla dessa resurser eftersom de är nödvändiga.

Men om gatewayen ingår i en prototyp-, ett test- eller en Proof of Concept-distribution, kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) till att ta bort resursgruppen, VPN-gatewayen och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om grundläggande skapande av VPN-gatewayer och hantering, till exempel att:

> [!div class="checklist"]
> * Skapa en VPN-gateway
> * Visa den offentliga IP-adressen
> * Ändra storlek på en VPN-gateway
> * Återställ en VPN gateway

Fortsätt sedan med följande självstudie:

> [!div class="nextstepaction"]
> * [Skapa en S2S-anslutning](vpn-gateway-create-site-to-site-rm-powershell.md)