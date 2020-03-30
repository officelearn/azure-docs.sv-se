---
title: Om ExpressRoute-gateways för virtuella nätverk – Azure| Microsoft-dokument
description: Lär dig mer om virtuella nätverksgateways för ExpressRoute. Den här artikeln innehåller information om gateway SKU:er och typer.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281423"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Om ExpressRoute-gateways för virtuella nätverk

Om du vill ansluta ditt virtuella Azure-nätverk och ditt lokala nätverk via ExpressRoute måste du först skapa en virtuell nätverksgateway. En virtuell nätverksgateway tjänar två syften: utbyta IP-vägar mellan nätverken och dirigera nätverkstrafik. I den här artikeln beskrivs gatewaytyper, gateway-SKU:er och uppskattade prestanda av SKU. I den här artikeln beskrivs också ExpressRoute [FastPath](#fastpath), en funktion som gör att nätverkstrafiken från det lokala nätverket kan kringgå den virtuella nätverksgatewayen för att förbättra prestanda.

## <a name="gateway-types"></a>Gateway-typer

När du skapar en virtuell nätverksgateway måste du ange flera inställningar. En av de nödvändiga inställningarna, '-GatewayType', anger om gatewayen används för ExpressRoute eller VPN-trafik. De två gatewaytyperna är:

* **Vpn** - För att skicka krypterad trafik över det offentliga Internet använder du gatewaytypen "Vpn". Detta kallas också för en VPN-gateway. Plats-till-plats-, punkt-till-plats- och VNet-VNet-anslutningar använder allihop en VPN-gateway.

* **ExpressRoute** - Om du vill skicka nätverkstrafik på en privat anslutning använder du gatewaytypen "ExpressRoute". Detta kallas också en ExpressRoute-gateway och är den typ av gateway som används när expressroute konfigureras.

Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType Vpn och en som använder -GatewayType ExpressRoute.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU:er
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Om du vill uppgradera din gateway till en mer kraftfull gateway SKU, i de flesta fall kan du använda "Ändra storlek-AzVirtualNetworkGateway" PowerShell cmdlet. Detta kommer att fungera för uppgraderingar till Standard och HighPerformance SKU: er. Men för att uppgradera till UltraPerformance SKU måste du återskapa gatewayen. Återskapa en gateway medför driftstopp.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Uppskattade prestanda efter gateway SKU
I följande tabell visas gatewaytyperna och de uppskattade prestandana. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Programmets prestanda beror på flera faktorer, till exempel svarstiden från slutna till slutna dagar, och antalet trafikflöden som programmet öppnas. Siffrorna i tabellen representerar den övre gränsen som programmet teoretiskt kan uppnå i en idealisk miljö.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Gateway-undernät 

Innan du skapar en ExpressRoute-gateway måste du skapa ett gateway-undernät. Gateway-undernätet innehåller de IP-adresser som virtuella virtuella nätverksgateway virtuella datorer och tjänster använder. När du skapar din virtuella nätverksgateway distribueras virtuella gateway-datorer till gateway-undernätet och konfigureras med de önskade ExpressRoute-gatewayinställningarna. Distribuera aldrig något annat (till exempel ytterligare virtuella datorer) till gateway-undernätet. Gateway-undernätet måste ha namnet "GatewaySubnet" för att fungera korrekt. Namngivning av gateway-undernätet GatewaySubnet gör att Azure vet att detta är undernätet för att distribuera virtuella virtuella nätverksgateway-datorer och -tjänster till.

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

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Zonsanta gateway-SKU:er

Du kan också distribuera ExpressRoute-gateways i Azure-tillgänglighetszoner. Detta separerar fysiskt och logiskt dem i olika tillgänglighetszoner, vilket skyddar din lokala nätverksanslutning till Azure från fel på zonnivå.

![Zonuppsagd ExpressRoute-gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zonundanta gateways använder specifika nya gateway-SKU:er för ExpressRoute-gateway.

* ErGw1AZ (ErGw1AZ)
* ErGw2AZ (ErGw2AZ)
* ErGw3AZ (ErGw3AZ)

De nya sku:erna för gateway stöder även andra distributionsalternativ som bäst matchar dina behov. När du skapar en virtuell nätverksgateway med hjälp av de nya gateway-SKU:erna har du också möjlighet att distribuera gatewayen i en viss zon. Detta kallas en zoninkörd gateway. När du distribuerar en zon gateway distribueras alla instanser av gatewayen i samma tillgänglighetszon.

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

ExpressRoute virtuellt nätverk gateway är utformad för att utbyta nätverksvägar och dirigera nätverkstrafik. FastPath är utformat för att förbättra datasökvägens prestanda mellan det lokala nätverket och det virtuella nätverket. När det är aktiverat skickar FastPath nätverkstrafik direkt till virtuella datorer i det virtuella nätverket och kringgår gatewayen.

Mer information om FastPath, inklusive begränsningar och krav, finns i [Om FastPath](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API:er och PowerShell-cmdlets
Ytterligare tekniska resurser och specifika syntaxkrav när du använder REST-API:er och PowerShell-cmdletar för konfigurationer av virtuella nätverksgatewayer finns på följande sidor:

| **Klassisk** | **Resource Manager** |
| --- | --- |
| [Powershell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Powershell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga anslutningskonfigurationer finns i [Översikt över ExpressRoute](expressroute-introduction.md).

Mer information om hur du skapar ExpressRoute-gateways finns i [Skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Mer information om hur du konfigurerar zonundant gateways finns i [Skapa en zonundant virtuell nätverksgateway](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Mer information om FastPath finns i [Om FastPath](about-fastpath.md).