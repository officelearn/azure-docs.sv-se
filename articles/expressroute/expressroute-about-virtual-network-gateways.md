---
title: Om ExpressRoute virtuella nätverksgateway – Azure | Microsoft Docs
description: 'Läs om virtuella nätverks-gatewayer för ExpressRoute. Den här artikeln innehåller information om Gateway-SKU: er och typer.'
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: f12f92294a9c30ddedea3c433ff65de4a635fd4d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889641"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>ExpressRoute-gateway för virtuella nätverk och FastPath
Om du vill ansluta ditt virtuella Azure-nätverk och ditt lokala nätverk via ExpressRoute måste du först skapa en virtuell nätverksgateway. En virtuell nätverksgateway fungerar i två olika syfte: Exchange IP-vägar mellan nätverken och dirigera nätverks trafik. I den här artikeln beskrivs Gateway-typer, Gateway SKU: er och uppskattade prestanda per SKU. Den här artikeln beskriver också ExpressRoute [FastPath](#fastpath), en funktion som gör att nätverks trafiken från ditt lokala nätverk kan kringgå den virtuella Nätverksgatewayen för att förbättra prestandan.

## <a name="gateway-types"></a>Gateway-typer

När du skapar en virtuell nätverksgateway måste du ange flera inställningar. En av de obligatoriska inställningarna,-GatewayType, anger om gatewayen används för ExpressRoute eller VPN-trafik. De två Gateway-typerna är:

* **VPN** – om du vill skicka krypterad trafik via det offentliga Internet använder du Gateway-typen VPN. Detta kallas även för en VPN-gateway. Plats-till-plats-, punkt-till-plats- och VNet-VNet-anslutningar använder allihop en VPN-gateway.

* **ExpressRoute** – om du vill skicka nätverks trafik på en privat anslutning använder du Gateway-typen "ExpressRoute". Detta kallas även för en ExpressRoute-gateway och är den typ av gateway som används när du konfigurerar ExpressRoute.

Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType Vpn och en som använder -GatewayType ExpressRoute.

## <a name="gwsku"></a>Gateway-SKU:er
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Om du vill uppgradera din gateway till en mer kraftfull Gateway-SKU, kan du i de flesta fall använda PowerShell-cmdleten "Restore-AzVirtualNetworkGateway". Detta fungerar för uppgraderingar till standard-och HighPerformance-SKU: er. Men för att uppgradera till UltraPerformance SKU måste du återskapa gatewayen. Att återskapa en gateway medför avbrott.

### <a name="aggthroughput"></a>Beräknade prestanda per Gateway-SKU
I följande tabell visas de olika Gateway-typerna och de uppskattade prestanda. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Program prestandan beror på flera faktorer, till exempel svars tiden från slut punkt till slut punkt och antalet trafik flöden som programmet öppnar. Talen i tabellen representerar den övre gränsen som programmet kan åstadkomma teoretiskt i en perfekt miljö.
>
>

## <a name="gwsub"></a>Gateway-undernät

Innan du skapar en ExpressRoute-Gateway måste du skapa ett Gateway-undernät. Gateway-undernätet innehåller de IP-adresser som de virtuella nätverks-gatewayens virtuella datorer och tjänster använder. När du skapar en virtuell nätverksgateway distribueras virtuella gateway-datorer till gateway-undernätet och konfigureras med nödvändiga ExpressRoute Gateway-inställningar. Distribuera aldrig något annat (till exempel ytterligare virtuella datorer) till gateway-undernätet. Gateway-undernätet måste ha namnet GatewaySubnet för att fungera korrekt. Att namnge Gateway-undernätet "GatewaySubnet" låter Azure veta att det här är under nätet för att distribuera virtuella nätverksgateway VM och tjänster till.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i Gateway-undernätet tilldelas till gateway-VM: ar och gateway-tjänsterna. Vissa konfigurationer kräver fler IP-adresser än andra. 

När du planerar storleken på Gateway-undernätet läser du dokumentationen för den konfiguration som du planerar att skapa. Till exempel kräver ExpressRoute-och VPN Gateway-konfigurationer som är samtidigt ett större Gateway-undernät än de flesta andra konfigurationer. Dessutom kanske du vill kontrol lera att Gateway-undernätet innehåller tillräckligt med IP-adresser för att kunna hantera framtida ytterligare konfigurationer. Även om du kan skapa ett Gateway-undernät så litet som/29, rekommenderar vi att du skapar ett Gateway-undernät på/27 eller större (/27,/26 osv.) om du har det tillgängliga adress utrymmet. Detta kommer att hantera de flesta konfigurationer.

Följande PowerShell-exempel i Resource Manager visar ett Gateway-undernät med namnet GatewaySubnet. Du kan se CIDR-noteringen anger en/27, vilket gör det möjligt för tillräckligt med IP-adresser för de flesta konfigurationer som för närvarande finns.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zrgw"></a>Zon – redundanta Gateway-SKU: er

Du kan också distribuera ExpressRoute-gatewayer i Azure-tillgänglighetszoner. Detta fysiskt och logiskt särskiljer dem till olika Tillgänglighetszoner, vilket skyddar din lokala nätverks anslutning till Azure från felaktiga zon nivåer.

![Zone-redundant ExpressRoute-Gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zone – redundanta gateways använder vissa nya gateway-SKU: er för ExpressRoute-Gateway.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nya gateway-SKU: er stöder också andra distributions alternativ för att passa dina behov bäst. När du skapar en virtuell nätverksgateway med hjälp av nya gateway-SKU: er, kan du också välja att distribuera gatewayen i en speciell zon. Detta kallas en zonindelade-Gateway. När du distribuerar en zonindelade-Gateway distribueras alla instanser av gatewayen i samma tillgänglighets zon.

## <a name="fastpath"></a>FastPath
ExpressRoute virtuella nätverksgateway är utformad för att utbyta nätverks vägar och dirigera nätverks trafik. FastPath är utformat för att förbättra data Sök vägens prestanda mellan ditt lokala nätverk och ditt virtuella nätverk. När aktive rad skickar FastPath nätverks trafik direkt till virtuella datorer i det virtuella nätverket, vilket kringgår gatewayen. 

FastPath finns på alla ExpressRoute-kretsar. En virtuell nätverksgateway krävs fortfarande för att skapa vägar mellan virtuella nätverk och lokalt nätverk. Den virtuella Nätverksgatewayen måste vara antingen Ultra Performance eller ErGw3AZ.

FastPath har inte stöd för följande funktioner:
* UDR i Gateway-undernät: om du använder en UDR på Gateway-undernätet för det virtuella nätverket kommer nätverks trafiken från ditt lokala nätverk även att skickas till den virtuella Nätverksgatewayen.
* VNet-peering: om du har andra virtuella nätverk som är peer-anslutna med det som är anslutet till ExpressRoute nätverks trafiken från ditt lokala nätverk till de andra virtuella nätverken (d.v.s. det kallas "ekrar"-virtuella nätverk) kommer att fortsätta att skickas till det virtuella nätverket nyckeln. Lösningen är att ansluta alla virtuella nätverk till ExpressRoute-kretsen direkt.
* Grundläggande Load Balancer: om du distribuerar en enkel intern belastningsutjämnare i det virtuella nätverket eller den Azure PaaS-tjänst som du distribuerar i ditt virtuella nätverk använder en enkel intern belastningsutjämnare, nätverks trafiken från ditt lokala nätverk till de virtuella IP-adresserna som finns på Basic Load Balancer skickas till den virtuella Nätverksgatewayen. Lösningen är att uppgradera den grundläggande belastningsutjämnaren till en [standard belastnings utjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).
* Privat länk: om du ansluter till en [privat slut punkt](../private-link/private-link-overview.md) i det virtuella nätverket från ditt lokala nätverk, kommer anslutningen att gå via den virtuella Nätverksgatewayen.
 
## <a name="resources"></a>REST-API: er och PowerShell-cmdletar
För ytterligare tekniska resurser och särskilda syntax krav för att använda REST-API: er och PowerShell-cmdletar för konfigurationer för virtuella nätverksgateway, se följande sidor:

| **Klassisk** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Nästa steg
Mer information om tillgängliga anslutnings konfigurationer finns i [Översikt över ExpressRoute](expressroute-introduction.md) .

Mer information om hur du skapar ExpressRoute-gatewayer finns i [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) .

Mer information om hur du konfigurerar zoner med redundanta gateways finns i [skapa en zon – redundant virtuell](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) nätverksgateway.

Mer information om hur du aktiverar FastPath finns i [Länka virtuellt nätverk till ExpressRoute](expressroute-howto-linkvnet-arm.md) . 
