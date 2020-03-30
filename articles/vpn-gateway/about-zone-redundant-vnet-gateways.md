---
title: Om zons redundanta virtuella nätverksgatewayer i Azure-tillgänglighetszoner
description: Lär dig mer om VPN Gateway- och ExpressRoute-gateways i tillgänglighetszoner.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: f1bbaab99b6422de4053839e2099869d2d08db95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864306"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Om zons redundanta virtuella nätverksgatewayer i Azure-tillgänglighetszoner

Du kan distribuera VPN- och ExpressRoute-gateways i [Azure-tillgänglighetszoner](../availability-zones/az-overview.md). Det ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribution av gatewayer i Azure-tillgänglighetszoner skiljer gatewayerna åt fysiskt och logiskt i en region, samtidigt som din lokala nätverksanslutning till Azure skyddas mot fel på zonnivå.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Zonredundanta gatewayer

Om du vill distribuera dina virtuella nätverksgateways automatiskt över tillgänglighetszoner kan du använda zonupptriga virtuella nätverksgatewayer. Med zonuppsagbara gateways kan du dra nytta av zonresiliens för att komma åt dina verksamhetskritiska, skalbara tjänster på Azure.

<br>
<br>

![bild av zon redundanta gateways](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Zoninkörsporter

Om du vill distribuera gateways i en viss zon kan du använda zonbaserade gateways. När du distribuerar en zon gateway distribueras alla instanser av gatewayen i samma tillgänglighetszon.

<br>
<br>

![bild av zon gateways](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Gateway-SKU:er

Zonundanta och zonbaserade gateways är tillgängliga som nya gateway-SKU:er. Vi har lagt till nya virtuella nätverksgateway-SKU:er i Azure AZ-regioner. Dessa SKU:er liknar motsvarande befintliga SKU:er för ExpressRoute och VPN Gateway, förutom att de är specifika för zonundanterade och zonbaserade gateways. Du kan identifiera dessa SKU:er med "AZ" i SKU-namnet.

Information om SKU:er för gateway finns i [SKU:er för VPN-gateway](vpn-gateway-about-vpngateways.md#gwsku) och [SKU:er för ExpressRoute gateway](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>Offentliga IP-SKU:er

Zonsanta gateways och zonbaserade gateways förlitar sig båda på Azure public IP-resurs *Standard* SKU. Konfigurationen av Azure public IP-resursen avgör om gatewayen som du distribuerar är zonupptror eller zonindelning. Om du skapar en offentlig IP-resurs med en *grundläggande* SKU, kommer gatewayen inte att ha någon zon redundans och gateway-resurserna kommer att vara regionala.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Zonredundanta gatewayer

När du skapar en offentlig IP-adress med standard **public** IP SKU utan att ange en zon, skiljer sig beteendet beroende på om gatewayen är en VPN-gateway eller en ExpressRoute-gateway. 

* För en VPN-gateway distribueras de två gateway-instanserna i valfri 2 av dessa tre zoner för att tillhandahålla zonredundans. 
* För en ExpressRoute-gateway, eftersom det kan finnas fler än två instanser, kan gatewayen sträcka sig över alla tre zonerna.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Zoninkörsporter

När du skapar en offentlig **Standard** IP-adress med standard-IP-SKU och anger zonen (1, 2 eller 3) kommer alla gatewayinstanser att distribueras i samma zon.

### <a name="regional-gateways"></a><a name="piprg"></a>Regionala gateways

När du skapar en offentlig IP-adress med hjälp av **grundläggande** offentlig IP-SKU distribueras gatewayen som en regional gateway och har ingen zonredundans inbyggd i gatewayen.

## <a name="faq"></a><a name="faq"></a>Faq

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Vad ändras när jag distribuerar dessa nya SKU:er?

Från ditt perspektiv kan du distribuera dina gateways med zonredundans. Det innebär att alla instanser av gateways kommer att distribueras över Azure-tillgänglighetszoner och varje tillgänglighetszon är en annan fel- och uppdateringsdomän. Detta gör dina gateways mer tillförlitliga, tillgängliga och motståndskraftiga mot zonfel.

### <a name="can-i-use-the-azure-portal"></a>Kan jag använda Azure-portalen?

Ja, du kan använda Azure-portalen för att distribuera de nya SKU:erna. Dessa nya SKU:er visas dock bara i de Azure-regioner som har Azure-tillgänglighetszoner.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Vilka regioner är tillgängliga för mig att använda de nya SKU:erna?

De nya SKU:erna är tillgängliga i Azure-regioner som har Azure-tillgänglighetszoner – Centrala USA, Centrala USA, Centrala Europa, Norra Europa, Västeuropa och västra USA 2 regioner, östra USA, östra USA 2, Sydostasien, Östra Japan, Storbritannien syd. Framöver kommer vi att göra zonsantatade gateways tillgängliga för dig i andra offentliga Azure-regioner.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan jag ändra/migrera/uppgradera mina befintliga virtuella nätverksgateways till zonupptriga gateways eller zon gateways?

Migrera dina befintliga virtuella nätverksgateways till zonupptriga gateways eller zonbaserade gateways stöds för närvarande inte. Du kan dock ta bort din befintliga gateway och återskapa en zonundant eller zonbaserad gateway.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan jag distribuera både VPN- och Express Route-gateways i samma virtuella nätverk?

Samexistens mellan både VPN- och Express Route-gateways i samma virtuella nätverk stöds. Du bör dock reservera ett /27 IP-adressintervall för gateway-undernätet.

## <a name="next-steps"></a>Nästa steg

[Skapa en zonredundant virtuell nätverksgateway](create-zone-redundant-vnet-gateway.md)
