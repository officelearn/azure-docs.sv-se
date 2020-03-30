---
title: Skapa en zon redundant virtuell nätverksgateway i Azure-tillgänglighetszoner
description: Distribuera VPN Gateway- och ExpressRoute-gateways i tillgänglighetszoner
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77150220"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Skapa en zon redundant virtuell nätverksgateway i Azure-tillgänglighetszoner

Du kan distribuera VPN- och ExpressRoute-gateways i Azure-tillgänglighetszoner. Det ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribution av gatewayer i Azure-tillgänglighetszoner skiljer gatewayerna åt fysiskt och logiskt i en region, samtidigt som din lokala nätverksanslutning till Azure skyddas mot fel på zonnivå. Information finns i [Om zon redundanta virtuella nätverksgateways](about-zone-redundant-vnet-gateways.md) och [Om Azure-tillgänglighetszoner](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. Deklarera dina variabler

Deklarera de variabler som du vill använda. Använd följande exempel och ersätt värdena med dina egna om det behövs. Om du stänger powershell/cloud shell-sessionen när som helst under övningen kopierar och klistrar du bara in värdena igen för att deklarera variablerna igen. När du anger plats kontrollerar du att det område du anger stöds. Mer information finns i [vanliga frågor](#faq)och svar .

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. Skapa det virtuella nätverket

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Skapa ett virtuellt nätverk.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Lägg till gateway-undernätet

Gateway-undernätet innehåller de reserverade IP-adresser som de virtuella nätverksgatewaytjänsterna använder. Använd följande exempel för att lägga till och ange ett gateway-undernät:

Lägg till gateway-undernätet.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Ange gateway-undernätskonfigurationen för det virtuella nätverket.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. Begära en offentlig IP-adress
 
I det här steget väljer du de instruktioner som gäller för den gateway som du vill skapa. Valet av zoner för distribution av gateways beror på de zoner som angetts för den offentliga IP-adressen.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>För zonundanta gateways

Begär en offentlig IP-adress med en **Standard** PublicIpaddress SKU och ange inte någon zon. I det här fallet kommer standardbaserad OFFENTLIG IP-adress som skapas att vara en zonundant offentlig IP.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>För zon gateways

Begär en offentlig IP-adress med en **Standard** PublicIpaddress SKU. Ange zonen (1, 2 eller 3). Alla gateway-instanser kommer att distribueras i den här zonen.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>För regionala gateways

Begär en offentlig IP-adress med en **Basic** PublicIpaddress SKU. I det här fallet distribueras gatewayen som en regional gateway och har ingen zon-redundans inbyggd i gatewayen. Gateway-instanserna skapas i alla zoner.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. Skapa IP-konfigurationen

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. Skapa gatewayen

Skapa den virtuella nätverksgatewayen.

### <a name="for-expressroute"></a>För ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>För VPN-gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>Faq

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Vad ändras när jag distribuerar dessa nya SKU:er?

Från ditt perspektiv kan du distribuera dina gateways med zonredundans. Det innebär att alla instanser av gateways kommer att distribueras över Azure-tillgänglighetszoner och varje tillgänglighetszon är en annan fel- och uppdateringsdomän. Detta gör dina gateways mer tillförlitliga, tillgängliga och motståndskraftiga mot zonfel.

### <a name="can-i-use-the-azure-portal"></a>Kan jag använda Azure-portalen?

Ja, du kan använda Azure-portalen för att distribuera de nya SKU:erna. Dessa nya SKU:er visas dock bara i de Azure-regioner som har Azure-tillgänglighetszoner.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Vilka regioner är tillgängliga för mig att använda de nya SKU:erna?

Se [Tillgänglighetszoner](../availability-zones/az-overview.md#services-support-by-region) för den senaste listan över tillgängliga regioner.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra/migrera/uppgradera mina befintliga virtuella nätverksgateways till zonupptriga gateways eller zon gateways?

Migrera dina befintliga virtuella nätverksgateways till zonupptriga gateways eller zonbaserade gateways stöds för närvarande inte. Du kan dock ta bort din befintliga gateway och återskapa en zonundant eller zonbaserad gateway.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera både VPN- och Express Route-gateways i samma virtuella nätverk?

Samexistens mellan både VPN- och Express Route-gateways i samma virtuella nätverk stöds. Du bör dock reservera ett /27 IP-adressintervall för gateway-undernätet.
