---
title: Om ExpressRoute-gatewayer för virtuellt nätverk – Azure | Microsoft Docs
description: 'Läs mer om virtuella nätverksgatewayer för ExpressRoute. Den här artikeln innehåller information om gateway SKU: er och typer.'
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357347"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Om ExpressRoute-gatewayer för virtuella nätverk

Om du vill ansluta ditt virtuella Azure-nätverk och ditt lokala nätverk via ExpressRoute måste du först skapa en virtuell nätverksgateway. En virtuell nätverksgateway fungerar i två olika syfte: Exchange IP-vägar mellan nätverken och dirigera nätverks trafik. I den här artikeln beskrivs Gateway-typer, Gateway SKU: er och uppskattade prestanda per SKU. Den här artikeln beskriver också ExpressRoute [FastPath](#fastpath), en funktion som gör att nätverks trafiken från ditt lokala nätverk kan kringgå den virtuella Nätverksgatewayen för att förbättra prestandan.

## <a name="gateway-types"></a>Gateway-typer

När du skapar en virtuell nätverksgateway kan behöva du ange flera inställningar. En av de obligatoriska inställningarna ”-GatewayType”, anger om gatewayen ska användas för ExpressRoute eller VPN-trafik. Två gatewaytyper är:

* **VPN** – om du vill skicka krypterad trafik via det offentliga Internet använder du Gateway-typen VPN. Detta kallas även för en VPN-gateway. Plats-till-plats-, punkt-till-plats- och VNet-VNet-anslutningar använder allihop en VPN-gateway.

* **ExpressRoute** – om du vill skicka nätverks trafik på en privat anslutning använder du Gateway-typen "ExpressRoute". Detta kallas också till en ExpressRoute-gateway och är typ av gateway som används när du konfigurerar ExpressRoute.

Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType Vpn och en som använder -GatewayType ExpressRoute.

## <a name="gwsku"></a>Gateway-SKU:er
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Om du vill uppgradera din gateway till en mer kraftfull Gateway-SKU, kan du i de flesta fall använda PowerShell-cmdleten "Restore-AzVirtualNetworkGateway". Detta fungerar för uppgradering till Standard och HighPerformance SKU: er. För att uppgradera till UltraPerformance SKU, kommer du dock behöva återskapa gatewayen. Återskapa en gateway medför driftavbrott.

### <a name="aggthroughput"></a>Beräknade prestanda per Gateway-SKU
I följande tabell visar gateway-typerna och uppskattade prestanda. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Programprestanda beror på flera faktorer, till exempel svarstiden slutpunkt till slutpunkt och antal trafikflöden programmet öppnas. Tal i tabellen representerar den övre gränsen som programmet teoretiskt sett kan uppnå i en perfekt miljö.
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

Du kan också distribuera ExpressRoute-gatewayer i Tillgänglighetszoner i Azure. Detta fysisk och logiskt delar upp dem i olika Tillgänglighetszoner, skydda din lokala nätverksanslutning till Azure från zonen på servernivå fel.

![Zonredundant ExpressRoute-gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zonredundant gateways använda specifika nya gateway SKU: er för ExpressRoute-gateway.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

De nya gateway-SKU: er har också stöd för andra distributionsalternativ för att matcha dina behov bäst. När du skapar en virtuell nätverksgateway med hjälp av den nya gatewayen SKU: er kan möjlighet du också att distribuera en gateway i en viss zon. Detta kallas för en zonindelad gateway. När du distribuerar en zonindelad gateway distribueras alla instanser av en gateway i samma Tillgänglighetszon.

## <a name="fastpath"></a>FastPath

ExpressRoute virtuella nätverksgateway är utformad för att utbyta nätverks vägar och dirigera nätverks trafik. FastPath är utformat för att förbättra data Sök vägens prestanda mellan ditt lokala nätverk och ditt virtuella nätverk. När aktive rad skickar FastPath nätverks trafik direkt till virtuella datorer i det virtuella nätverket, vilket kringgår gatewayen.

Mer information om FastPath, inklusive begränsningar och krav finns i [om FastPath](about-fastpath.md).

## <a name="resources"></a>REST-API: er och PowerShell-cmdletar
Ytterligare tekniska resurser och specifik syntax krav när du använder REST API: er och PowerShell-cmdletar för gateway-konfigurationer för virtuella nätverk finns i följande sidor:

| **Klassisk** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Nästa steg

Mer information om tillgängliga anslutnings konfigurationer finns i [Översikt över ExpressRoute](expressroute-introduction.md).

Mer information om hur du skapar ExpressRoute-gatewayer finns i [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Mer information om hur du konfigurerar zoner med redundanta gateways finns i [skapa en zon-redundant virtuell](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md)nätverksgateway.

Mer information om FastPath finns i [om FastPath](about-fastpath.md).