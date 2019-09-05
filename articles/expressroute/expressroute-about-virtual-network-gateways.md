---
title: Om ExpressRoute-gatewayer för virtuellt nätverk – Azure | Microsoft Docs
description: 'Läs mer om virtuella nätverksgatewayer för ExpressRoute. Den här artikeln innehåller information om gateway SKU: er och typer.'
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 5b74e387c6bee58acbbb7bae320a9bc72a4dda1c
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376281"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>ExpressRoute-gateway för virtuella nätverk och FastPath
Om du vill ansluta ditt virtuella Azure-nätverk och ditt lokala nätverk via ExpressRoute måste du först skapa en virtuell nätverksgateway. En virtuell nätverksgateway fungerar i två olika syfte: Exchange IP-vägar mellan nätverken och dirigera nätverks trafik. I den här artikeln beskrivs Gateway-typer, Gateway SKU: er och uppskattade prestanda per SKU. Den här artikeln beskriver också ExpressRoute [FastPath](#fastpath), en funktion som gör att nätverks trafiken från ditt lokala nätverk kan kringgå den virtuella Nätverksgatewayen för att förbättra prestandan.

## <a name="gateway-types"></a>Gateway-typer

När du skapar en virtuell nätverksgateway kan behöva du ange flera inställningar. En av de obligatoriska inställningarna ”-GatewayType”, anger om gatewayen ska användas för ExpressRoute eller VPN-trafik. Två gatewaytyper är:

* **VPN** – om du vill skicka krypterad trafik via det offentliga Internet och du använder du gatewaytypen 'Vpn ”. Detta kallas även för en VPN-gateway. Plats-till-plats-, punkt-till-plats- och VNet-VNet-anslutningar använder allihop en VPN-gateway.

* **ExpressRoute** – om du vill skicka nätverkstrafik på en privat anslutning du använder du gatewaytypen ”ExpressRoute”. Detta kallas också till en ExpressRoute-gateway och är typ av gateway som används när du konfigurerar ExpressRoute.

Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType Vpn och en som använder -GatewayType ExpressRoute.

## <a name="gwsku"></a>Gateway-SKU:er
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Om du vill uppgradera din gateway till en mer kraftfull Gateway-SKU, kan du i de flesta fall använda PowerShell-cmdleten "Restore-AzVirtualNetworkGateway". Detta fungerar för uppgradering till Standard och HighPerformance SKU: er. För att uppgradera till UltraPerformance SKU, kommer du dock behöva återskapa gatewayen. Återskapa en gateway medför driftavbrott.

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

## <a name="fastpath"></a>FastPath
ExpressRoute virtuella nätverksgateway är utformad för att utbyta nätverks vägar och dirigera nätverks trafik. FastPath är utformat för att förbättra data Sök vägens prestanda mellan ditt lokala nätverk och ditt virtuella nätverk. När aktive rad skickar FastPath nätverks trafik direkt till virtuella datorer i det virtuella nätverket, vilket kringgår gatewayen. 

FastPath är endast tillgängligt på [ExpressRoute Direct](expressroute-erdirect-about.md) . Med andra ord kan du bara aktivera den här funktionen om du [ansluter ditt virtuella nätverk](expressroute-howto-linkvnet-arm.md) till en ExpressRoute-krets som skapats på en ExpressRoute Direct-port. FastPath kräver fortfarande att en virtuell nätverksgateway skapas för att utväxla vägar mellan ett virtuellt nätverk och ett lokalt nätverk. Den virtuella Nätverksgatewayen måste vara antingen Ultra Performance eller ErGw3AZ.

FastPath har inte stöd för följande funktioner:
* UDR i Gateway-undernät: om du använder en UDR på Gateway-undernätet för det virtuella nätverket kommer nätverks trafiken från ditt lokala nätverk även att skickas till den virtuella Nätverksgatewayen.
* VNet-peering: om du har andra virtuella nätverk som är peer-anslutna med det som är anslutet till ExpressRoute nätverks trafiken från ditt lokala nätverk till de andra virtuella nätverken (d.v.s. det kallas "ekrar"-virtuella nätverk) kommer att fortsätta att skickas till det virtuella nätverket nyckeln. Lösningen är att ansluta alla virtuella nätverk till ExpressRoute-kretsen direkt.
* Grundläggande belastnings Balander: om du distribuerar en enkel intern belastningsutjämnare i ditt virtuella nätverk eller den Azure PaaS-tjänst som du distribuerar i ditt virtuella nätverk använder en grundläggande intern belastningsutjämnare, nätverks trafiken från ditt lokala nätverk till de virtuella IP-adresserna som finns på Basic Load Balancer skickas till den virtuella Nätverksgatewayen. Lösningen är att uppgradera den grundläggande belastningsutjämnaren till en [standard belastnings utjämning](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview). 
 
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

Mer information om hur du aktiverar FastPath finns i [Länka virtuellt nätverk till ExpressRoute](expressroute-howto-linkvnet-arm.md) . 
