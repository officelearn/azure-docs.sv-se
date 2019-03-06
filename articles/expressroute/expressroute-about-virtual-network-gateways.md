---
title: Om ExpressRoute-gatewayer för virtuellt nätverk – Azure | Microsoft Docs
description: 'Läs mer om virtuella nätverksgatewayer för ExpressRoute. Den här artikeln innehåller information om gateway SKU: er och typer.'
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: d9c607114d6c6c56c25303a88dcc11f4ab804eb4
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404356"
---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Om virtuella nätverksgatewayer för ExpressRoute
En virtuell nätverksgateway används för att skicka nätverkstrafik mellan Azure-nätverk och lokala platser. Du kan använda en virtuell nätverksgateway för ExpressRoute-trafik eller VPN-trafik. Den här artikeln fokuserar på virtuella nätverksgatewayer för ExpressRoute och innehåller information om SKU: er, uppskattade prestanda av SKU och gateway-typer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gateway-types"></a>Gateway-typer

När du skapar en virtuell nätverksgateway kan behöva du ange flera inställningar. En av de obligatoriska inställningarna ”-GatewayType”, anger om gatewayen ska användas för ExpressRoute eller VPN-trafik. Två gatewaytyper är:

* **VPN** – om du vill skicka krypterad trafik via det offentliga Internet och du använder du gatewaytypen 'Vpn ”. Detta kallas även för en VPN-gateway. Plats-till-plats-, punkt-till-plats- och VNet-VNet-anslutningar använder allihop en VPN-gateway.

* **ExpressRoute** – om du vill skicka nätverkstrafik på en privat anslutning du använder du gatewaytypen ”ExpressRoute”. Detta kallas också till en ExpressRoute-gateway och är typ av gateway som används när du konfigurerar ExpressRoute.

Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType Vpn och en som använder -GatewayType ExpressRoute.

## <a name="gwsku"></a>Gateway-SKU:er
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Om du vill uppgradera din gateway till en kraftfullare gateway-SKU, kan du använda 'Storleksändring AzVirtualNetworkGateway' PowerShell-cmdleten i de flesta fall. Detta fungerar för uppgradering till Standard och HighPerformance SKU: er. För att uppgradera till UltraPerformance SKU, kommer du dock behöva återskapa gatewayen. Återskapa en gateway medför driftavbrott.

### <a name="aggthroughput"></a>Uppskattade prestanda efter gateway-SKU
I följande tabell visar gateway-typerna och uppskattade prestanda. Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Programprestanda beror på flera faktorer, till exempel svarstiden slutpunkt till slutpunkt och antal trafikflöden programmet öppnas. Tal i tabellen representerar den övre gränsen som programmet teoretiskt sett kan uppnå i en perfekt miljö.
>
>

### <a name="zrgw"></a>Zonredundant gateway SKU: er

Du kan också distribuera ExpressRoute-gatewayer i Tillgänglighetszoner i Azure. Detta fysisk och logiskt delar upp dem i olika Tillgänglighetszoner, skydda din lokala nätverksanslutning till Azure från zonen på servernivå fel.

![Zonredundant ExpressRoute-gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Zonredundant gateways använda specifika nya gateway SKU: er för ExpressRoute-gateway.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

De nya gateway-SKU: er har också stöd för andra distributionsalternativ för att matcha dina behov bäst. När du skapar en virtuell nätverksgateway med hjälp av den nya gatewayen SKU: er kan möjlighet du också att distribuera en gateway i en viss zon. Detta kallas för en zonindelad gateway. När du distribuerar en zonindelad gateway distribueras alla instanser av en gateway i samma Tillgänglighetszon.

## <a name="resources"></a>REST API: er och PowerShell-cmdlets
Ytterligare tekniska resurser och specifik syntax krav när du använder REST API: er och PowerShell-cmdletar för gateway-konfigurationer för virtuella nätverk finns i följande sidor:

| **Klassisk** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [REST-API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST-API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Nästa steg
Se [översikt över ExpressRoute](expressroute-introduction.md) för mer information om tillgängliga anslutningskonfigurationer.

Se [skapar en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) för mer information om hur du skapar ExpressRoute-gatewayer.

Se [skapa en zonredundant virtuell nätverksgateway](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) för mer information om hur du konfigurerar zonredundant gatewayer.
