---
title: VPN gateway-inställningar för Azure-anslutningar på olika platser | Microsoft Docs
description: Läs mer om VPN Gateway-inställningar för Azures virtuella nätverksgatewayar.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: cherylmc
ms.openlocfilehash: 3bf3dd325af48f99e109f651628883d8f946fdc8
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310146"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Om konfigurationsinställningar för VPN-Gateway

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik mellan ditt virtuella nätverk och din lokala plats via en offentlig anslutning. Du kan också använda en VPN-gateway för att skicka trafik mellan virtuella nätverk över Azures stamnätverk.

En anslutning för VPN-gateway är beroende av konfigurationen av flera resurser som innehåller konfigurerbara inställningar. Avsnitt i den här artikeln beskrivs de resurser och inställningar som är relaterade till en VPN-gateway för ett virtuellt nätverk som skapats i Resource Manager-distributionsmodellen. Du hittar beskrivningar och Topologidiagram för varje anslutning lösning i den [om VPN Gateway](vpn-gateway-about-vpngateways.md) artikeln.

>[!NOTE]
> Värdena i den här artikeln gäller VPN-gatewayer (vnet-gateways som använder - GatewayType Vpn). Den här artikeln beskriver inte alla gateway-typer eller zonredundant gatewayer.
>
>* Värden som gäller för - GatewayType ”ExpressRoute”, se [virtuella Nätverksgatewayer för ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).
>* Zonredundant gateways, se [om zonredundant gatewayer](about-zone-redundant-vnet-gateways.md).
>* Virtuellt WAN-nätverk, finns i [om virtuellt WAN](../virtual-wan/virtual-wan-about.md). 
>

## <a name="gwtype"></a>Gateway-typer

Varje virtuellt nätverk kan bara ha en vnet-gateway för varje typ. När du skapar en virtuell nätverksgateway måste du se till att gateway-typen är rätt för din konfiguration.

De tillgängliga värdena för - GatewayType är:

* Vpn
* ExpressRoute

En VPN-gateway kräver den `-GatewayType` *Vpn*.

Exempel:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurera en gateway-SKU

#### <a name="azure-portal"></a>Azure Portal

Om du använder Azure-portalen för att skapa en virtuell nätverksgateway för Resource Manager kan välja du gatewayen-SKU med hjälp av listmenyn. Alternativen visas motsvarar den typ av Gateway och VPN-typ som du väljer.

#### <a name="powershell"></a>PowerShell

Följande PowerShell-exempel anger den `-GatewaySku` som VpnGw1. När du använder PowerShell för att skapa en gateway, måste du först skapa IP-konfigurationen och sedan använda en variabel för att referera till den. I det här exemplet är konfigurationsvariabeln $gwipconfig.

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Ändra storlek på eller ändra en SKU

Om du har en VPN-gateway och du vill använda en annan gateway-SKU, är alternativen att antingen ändra storlek på gateway-SKU eller ändra till en annan SKU. När du ändrar till en annan gateway-SKU du ta bort den befintliga gatewayen helt och hållet och skapa en ny. Det kan ta upp till 45 minuter att skapa. Jämförelse när du ändrar storlek på en gateway-SKU, har du mycket lite driftstopp eftersom du inte har att ta bort och återskapa gatewayen. Om du har möjlighet att ändra storlek på gateway-SKU i stället för att ändra den kommer du att göra detta. Men det finns regler om storleksändring:

1. Du kan ändra storlek mellan VpnGw1, VpnGw2 och VpnGw3 SKU: er.
2. När du arbetar med gamla gatewayen-SKU: er, kan du ändra storlek mellan Basic, Standard och HighPerformance SKU: er.
3. Du **kan inte** ändra från HighPerformance-Basic/Standard SKU: er till nya VpnGw3-VpnGw1/VpnGw2 SKU: er. Du måste i stället [ändra](#change) till de nya SKU: er.

#### <a name="resizegwsku"></a>Ändra storlek på en gateway

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>Ändra från en gammal (gamla) SKU till en ny SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Anslutningstyper

Varje konfiguration kräver anslutningstypen för gatewayen ett specifikt virtuellt nätverk i Resource Manager-distributionsmodellen. Tillgängliga PowerShell-värden i Resource Manager för `-ConnectionType` är:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

I följande PowerShell-exempel skapar vi en S2S-anslutning som kräver anslutningstypen *IPsec*.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>VPN-typer

Du måste ange en VPN-typ när du skapar den virtuella nätverksgatewayen för en VPN-gateway-konfiguration. VPN-typ som du väljer beror på den anslutningstopologi som du vill skapa. Till exempel kräver en P2S-anslutning en RouteBased VPN-typ. En VPN-typ kan också vara beroende av den maskinvara som du använder. S2S-konfigurationer kräver en VPN-enhet. Vissa VPN-enheter har endast stöd för en viss typ av VPN.

VPN-typ som du väljer måste uppfylla alla anslutning krav för lösningen du vill skapa. Om du vill skapa en S2S VPN gateway-anslutning och en P2S VPN-gatewayanslutning för samma virtuella nätverk kan du exempelvis använder VPN-typ *RouteBased* eftersom P2S kräver en RouteBased VPN-typ. Du kan också behöva kontrollera att VPN-enheten stöds en RouteBased VPN-anslutning. 

Du kan inte ändra den VPN-typ när du har skapat en virtuell nätverksgateway. Du måste ta bort den virtuella nätverksgatewayen och skapa en ny. Det finns två typer av VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Följande PowerShell-exempel anger den `-VpnType` som *RouteBased*. När du skapar en gateway måste du kontrollera att -VpnType är rätt för din konfiguration.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Gateway-krav

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Gateway-undernät

Innan du skapar en VPN-gateway, måste du skapa ett gateway-undernät. Gateway-undernätet innehåller IP-adresser som den virtuella nätverksgatewayen virtuella datorer och tjänster. När du skapar din virtuella nätverksgateway Gatewaydatorer distribueras till gateway-undernätet och konfigurerad med inställningarna som krävs VPN-gateway. Du måste distribuera aldrig något annat (till exempel ytterligare VM) till gateway-undernätet. Gateway-undernätet måste ha namnet ”GatewaySubnet” ska fungera korrekt. Namnge gateway-undernätet ”GatewaySubnet” kan Azure vet att det här är att distribuera virtuell nätverksgateway virtuella datorer och tjänster till undernätet.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i gatewayundernätet allokeras till virtuella Gatewaydatorer och gateway-tjänster. Vissa konfigurationer kräver fler IP-adresser än andra. Titta på anvisningarna för den konfiguration som du vill skapa och verifiera att gateway-undernätet som du vill skapa uppfyller dessa krav. Dessutom kan du kontrollera att gatewayundernätet innehåller tillräckligt med IP-adresser för att hantera möjliga framtida ytterligare konfigurationer. Du kan skapa ett gateway-undernät som är så litet som/29, rekommenderar vi att du skapar ett gateway-undernät på/28 eller större (/ 28, / 27, /26 osv.). På så sätt kan om du lägger till funktioner i framtiden kommer du inte behöver plocka din gateway och sedan ta bort och återskapa gateway-undernätet så att fler IP-adresser.

I följande Resource Managers PowerShell-exempel visar ett gateway-undernät med namnet GatewaySubnet. Du kan se att CIDR-noteringen anger en/27, vilket gör att tillräckligt många IP-adresser för de flesta konfigurationer som finns för närvarande.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Lokala nätverksgatewayer

När du skapar en VPN-gateway-konfiguration, representerar den lokala nätverksgatewayen ofta din lokala plats. I den klassiska distributionsmodellen kallades den lokala nätverksgatewayen för Lokal plats. 

Du och ge den lokala nätverksgatewayen ett namn, offentlig IP-adressen för den lokala VPN-enheten och ange IP-adressprefixen som finns på den lokala platsen. Azure tittar på måladressprefixen för nätverkstrafiken, konsultationer den konfiguration som du har angett för din lokala nätverksgateway och dirigerar paket på lämpligt sätt. Du kan också ange lokala nätverksgatewayer för VNet-till-VNet-konfigurationer som använder en VPN-gatewayanslutning.

I följande PowerShell-exempel skapas en ny lokal nätverksgateway:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Ibland behöver ändra inställningarna för lokala gateway. Till exempel när du lägger till eller ändra adressintervallet, eller om IP-adressen för VPN-enhet ändras. Se [ändra gateway-inställningar för lokalt nätverk med hjälp av PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>REST API: er, PowerShell-cmdlets och CLI

Ytterligare tekniska resurser och specifik syntax krav när du använder REST API: er, PowerShell-cmdletar eller Azure CLI för VPN-Gateway-konfigurationer finns i följande sidor:

| **Klassisk** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/azurerm.network/#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [REST-API](https://msdn.microsoft.com/library/jj154113) |[REST-API](/rest/api/network/virtualnetworkgateways) |
| Stöds inte | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Nästa steg

Läs mer om tillgängliga anslutningskonfigurationer [om VPN Gateway](vpn-gateway-about-vpngateways.md).
