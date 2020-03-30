---
title: 'Azure VPN Gateway: konfigurationsinställningar'
description: Lär dig mer om VPN Gateway-inställningar för Azure-gateways för virtuella nätverk.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: d7a2040748d170b4e536df59947ea811f149d931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244867"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Om konfigurationsinställningar för VPN Gateway

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik mellan det virtuella nätverket och din lokala plats via en offentlig anslutning. Du kan också använda en VPN-gateway för att skicka trafik mellan virtuella nätverk över Azure-stamnätet.

En VPN-gatewayanslutning är beroende av konfigurationen av flera resurser, som var och en innehåller konfigurerbara inställningar. I avsnitten i den här artikeln beskrivs de resurser och inställningar som relaterar till en VPN-gateway för ett virtuellt nätverk som skapats i Resource Manager-distributionsmodellen. Du hittar beskrivningar och topologidiagram för varje anslutningslösning i artikeln [Om VPN Gateway.](vpn-gateway-about-vpngateways.md)

Värdena i den här artikeln tillämpar VPN-gateways (virtuella nätverksgateways som använder Vpn -GatewayType). Den här artikeln täcker inte alla gatewaytyper eller zonundanta gateways.

* Värden som gäller för -GatewayType 'ExpressRoute finns i [Virtual Network Gateways för ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* För zonundanta gateways finns i [Om zonundant gateways](about-zone-redundant-vnet-gateways.md).

* För Virtuellt WAN, se [Om VirtuellT WAN](../virtual-wan/virtual-wan-about.md).



## <a name="gateway-types"></a><a name="gwtype"></a>Gateway-typer

Varje virtuellt nätverk kan bara ha en virtuell nätverksgateway av varje typ. När du skapar en virtuell nätverksgateway måste du se till att gatewaytypen är korrekt för konfigurationen.

De tillgängliga värdena för -GatewayType är:

* Vpn
* ExpressRoute

En VPN-gateway `-GatewayType` kräver *Vpn*.

Exempel:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurera en gateway-SKU

#### <a name="azure-portal"></a>Azure Portal

Om du använder Azure-portalen för att skapa en Virtuell Resource Manager-gateway kan du välja gateway-SKU med hjälp av listrutan. De alternativ som du visas med motsvarar den gatewaytyp och VPN-typ som du väljer.

#### <a name="powershell"></a>PowerShell

Följande PowerShell-exempel anger `-GatewaySku` som VpnGw1. När du använder PowerShell för att skapa en gateway måste du först skapa IP-konfigurationen och sedan använda en variabel för att referera till den. I det här exemplet är konfigurationsvariabeln $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Ändra storlek på eller ändra en SKU

Om du har en VPN-gateway och vill använda en annan gateway SKU, är dina alternativ att antingen ändra storlek på din gateway SKU eller att byta till en annan SKU. När du byter till en annan gateway SKU tar du bort den befintliga gatewayen helt och bygger en ny. Det kan ta upp till 45 minuter att skapa en gateway. I jämförelse, när du ändrar storlek på en gateway SKU, finns det inte mycket driftstopp eftersom du inte behöver ta bort och återskapa gatewayen. Om du har möjlighet att ändra storlek på gateway-SKU:n i stället för att ändra den, vill du göra det. Det finns dock regler för storleksändring:

1. Med undantag för Basic SKU kan du ändra storlek på en VPN-gateway SKU till en annan VPN-gateway SKU inom samma generation (Generation1 eller Generation2). Till exempel kan VpnGw1 av Generation1 storleksas till VpnGw2 av Generation1 men inte till VpnGw2 av Generation2.
2. När du arbetar med gamla gatewayen-SKU: er, kan du ändra storlek mellan Basic, Standard och HighPerformance SKU: er.
3. Du **kan inte** ändra storlek på från Basic/Standard/HighPerformance SKU:er till VPNGw SKU:er. Du måste i stället [byta](#change) till de nya SKU:erna.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Så här ändrar du storlek på en gateway

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Så här byter du från en gammal (äldre) SKU till en ny SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Anslutningstyper

I resurshanterarens distributionsmodell kräver varje konfiguration en specifik anslutningstyp för virtuell nätverksgateway. Tillgängliga PowerShell-värden i Resource Manager för `-ConnectionType` är:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

I följande PowerShell-exempel skapar vi en S2S-anslutning som kräver anslutningstypen *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>VPN-typer

När du skapar den virtuella nätverksgatewayen för en VPN-gatewaykonfiguration måste du ange en VPN-typ. Vilken VPN-typ du väljer beror på vilken anslutningstopologi du vill skapa. En P2S-anslutning kräver till exempel en RouteBased VPN-typ. En VPN-typ kan också bero på vilken maskinvara du använder. S2S-konfigurationer kräver en VPN-enhet. Vissa VPN-enheter stöder bara en viss VPN-typ.

Den VPN-typ du väljer måste uppfylla alla anslutningskrav för den lösning du vill skapa. Om du till exempel vill skapa en S2S VPN-gatewayanslutning och en P2S VPN-gatewayanslutning för samma virtuella nätverk, använder du VPN-typen *RouteBased* eftersom P2S kräver en RouteBased VPN-typ. Du måste också kontrollera att din VPN-enhet stödde en RouteBased VPN-anslutning. 

När en virtuell nätverksgateway har skapats kan du inte ändra VPN-typen. Du måste ta bort den virtuella nätverksgatewayen och skapa en ny. Det finns två typer av VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

I följande PowerShell-exempel `-VpnType` anges som *RouteBased*. När du skapar en gateway måste du kontrollera att -VpnType är rätt för din konfiguration.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Gateway-krav

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Gateway-undernät 

Innan du skapar en VPN-gateway måste du skapa ett gateway-undernät. Gateway-undernätet innehåller de IP-adresser som virtuella virtuella nätverksgateway virtuella datorer och tjänster använder. När du skapar din virtuella nätverksgateway distribueras virtuella gateway-datorer till gateway-undernätet och konfigureras med de nödvändiga VPN-gatewayinställningarna. Distribuera aldrig något annat (till exempel ytterligare virtuella datorer) till gateway-undernätet. Gateway-undernätet måste ha namnet "GatewaySubnet" för att fungera korrekt. Namngivning av gateway-undernätet GatewaySubnet gör att Azure vet att detta är undernätet för att distribuera virtuella virtuella nätverksgateway-datorer och -tjänster till.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i gateway-undernätet allokeras till gateway-virtuella datorer och gatewaytjänster. Vissa konfigurationer kräver fler IP-adresser än andra. 

När du planerar gateway-undernätsstorleken läser du dokumentationen för den konfiguration som du planerar att skapa. ExpressRoute/VPN Gateway-samtidigkonfigurationen kräver till exempel ett större gateway-undernät än de flesta andra konfigurationer. Dessutom kanske du vill se till att gateway-undernätet innehåller tillräckligt med IP-adresser för att hantera eventuella framtida ytterligare konfigurationer. Även om du kan skapa ett gateway-undernät så litet som /29 rekommenderar vi att du skapar ett gateway-undernät på /27 eller större (/27, /26 osv.) om du har det tillgängliga adressutrymmet för att göra det. Detta kommer att rymma de flesta konfigurationer.

I följande PowerShell-exempel för Resurshanteraren visar ett gateway-undernät med namnet GatewaySubnet. Du kan se CIDR-notationen anger en /27, vilket ger tillräckligt många IP-adresser för de flesta konfigurationer som för närvarande finns.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Lokala nätverksgatewayer

 En lokal nätverksgateway skiljer sig från en virtuell nätverksgateway. När du skapar en VPN-gateway-konfiguration representerar den lokala nätverksgatewayen vanligtvis din lokala plats. I den klassiska distributionsmodellen kallades den lokala nätverksgatewayen för Lokal plats.

Du ger den lokala nätverksgatewayen ett namn, den offentliga IP-adressen för den lokala VPN-enheten och anger de adressprefix som finns på den lokala platsen. Azure tittar på måladressprefixen för nätverkstrafik, konsulterar den konfiguration som du har angett för din lokala nätverksgateway och dirigerar paket därefter. Du kan också ange lokala nätverksgateways för VNet-till-VNet-konfigurationer som använder en VPN-gatewayanslutning.

I följande PowerShell-exempel skapas en ny lokal nätverksgateway:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Ibland måste du ändra inställningarna för den lokala nätverksgatewayen. Till exempel när du lägger till eller ändrar adressintervallet, eller om IP-adressen för VPN-enheten ändras. Se [Ändra inställningar för lokal nätverksgateway med PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST API:er, PowerShell-cmdlets och CLI

Ytterligare tekniska resurser och specifika syntaxkrav när du använder REST-API:er, PowerShell-cmdlets eller Azure CLI för VPN Gateway-konfigurationer finns på följande sidor:

| **Klassisk** | **Resource Manager** |
| --- | --- |
| [Powershell](/powershell/module/az.network/#networking) |[Powershell](/powershell/module/az.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Stöds inte | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga anslutningskonfigurationer finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).
