---
title: Om VPN Gateway| Microsoft Docs
description: "Lär dig om VPN Gateway-anslutningar för Azure Virtual Networks."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c934f78e514230958fad8b2aa9be4d2e56a3a835
ms.openlocfilehash: f74d531006a2c2cc4b12aac846c20c30317cc013


---
# <a name="about-vpn-gateway"></a>Om VPN Gateway
En virtuell nätverksgateway används för att skicka nätverkstrafik mellan virtuella Azure-nätverk och lokala platser, samt mellan virtuella nätverk i Azure (VNet till VNet). När du konfigurerar en VPN-gateway måste du skapa och konfigurera en gateway för virtuellt nätverk och en gateway för virtuell nätverksanslutning.

När du skapar en resurs för gatewayen för virtuella nätverk i distributionsmodellen för Resource Manager anger du flera inställningar. En av inställningarna som krävs är ”-GatewayType”. Det finns två typer av virtuella nätverksgatewayer: Vpn och ExpressRoute. 

När nätverkstrafik skickas på en dedikerad privat anslutning använder du gatewaytypen ”ExpressRoute”. Det kallas även för en ExpressRoute-gateway. När nätverkstrafik skickas krypterat via en offentlig anslutning använder du gateway-typen ”Vpn”. Detta kallas för en VPN-gateway. Plats-till-plats-, punkt-till-plats- och VNet-VNet-anslutningar använder allihop en VPN-gateway.

Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType ExpressRoute och en som använder -GatewayType Vpn. Den här artikeln fokuserar huvudsakligen på VPN-gatewayar. Mer information om ExpressRoute finns i [Teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Priser
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>Gateway-SKU:er
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Se [Gateway-SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er för VPN-gateway.

### <a name="estimated-aggregate-throughput-by-sku"></a>Beräknat aggregerat dataflöde av SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="configuring-a-vpn-gateway"></a>Konfigurera en VPN-gateway
När du konfigurerar en VPN-gateway varierar instruktionerna som du följer beroende på vilken distributionsmodell du använde för att skapa det virtuella nätverket. Om du exempelvis har skapat ditt VNet med den klassiska distributionsmodellen, ska du använda riktlinjerna och instruktionerna för den modellen när du skapar och konfigurerar dina VPN Gateway-inställningar. Mer information om distributionsmodellerna finns i [Förstå Resource Manager- och klassiska distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md).

En anslutning för VPN-gateway är beroende av flera resurser som är konfigurerade med specifika inställningar. De flesta resurserna kan konfigureras separat, även om de måste konfigureras i en viss ordning i vissa fall. Du kan börja skapa och konfigurera resurser med hjälp av ett konfigurationsverktyg, till exempel Azure Portal. Du kan senare bestämma dig för att växla till ett annat verktyg, som PowerShell, för att konfigurera ytterligare resurser eller ändra befintliga resurser i tillämpliga fall. För närvarande går det inte att konfigurera alla resurser och resursinställningar på Azure Portal. Anvisningarna i artiklarna för varje anslutningstopologi anger om ett specifikt konfigurationsverktyg behövs. Information om enskilda resurser och inställningar för VPN-gateway finns [Om inställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md).

Följande avsnitt innehåller tabeller med följande:

* tillgänglig distributionsmodell
* tillgängliga konfigurationsverktyg
* länkar som tar dig direkt till en artikel om en sådan finns

Använd diagrammen och beskrivningarna för att välja den anslutningstopologi som passar dina behov. Diagrammen visar de viktigaste grundläggande topologierna, men du kan skapa mer komplexa konfigurationer och använda diagrammen som utgångspunkt.

## <a name="site-to-site-and-multi-site"></a>Plats-till-plats och flera platser
### <a name="site-to-site"></a>Plats-till-plats
En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats en offentlig IP-adress och som inte finns bakom en NAT. S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer.   

![S2S-anslutning](./media/vpn-gateway-about-vpngateways/demos2s.png "plats-till-plats")

### <a name="multi-site"></a>Flera platser
Du kan skapa och konfigurera en VPN-gatewayanslutning mellan ditt VNet och flera lokala nätverk. När du arbetar med flera anslutningar måste du använda en RouteBased VPN-typ (dynamisk gateway för klassiska virtuella nätverk). Eftersom ett VNet bara kan ha en VPN-gateway delar alla anslutningar genom gatewayen på den tillgängliga bandbredden. Det här kallas ofta en ”anslutning till flera platser”.

![Anslutning till flera platser](./media/vpn-gateway-about-vpngateways/demomulti.png "flera platser")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Distributionsmodeller och metoder för plats-till-plats och flera platser
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="vnet-to-vnet"></a>VNet-till-VNet
Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på nästan samma sätt som du ansluter ett VNet till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan till och med kombinera VNet-till-VNet-kommunikation med anslutningskonfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

De virtuella nätverk som du ansluter kan finnas:

* i samma eller olika regioner
* i samma eller olika prenumerationer 
* i samma eller olika distributionsmodeller

![VNet till VNet-anslutning](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-till-vnet")

#### <a name="connections-between-deployment-models"></a>Anslutningar mellan distributionsmodeller
Azure har för närvarande två distributionsmodeller: klassisk och Resource Manager. Om du har använt Azure ett tag har du förmodligen virtuella Azure-datorer och instansroller som kör i ett klassiskt VNet. Dina nyare virtuella datorer och rollinstanser kanske körs i ett VNet som skapats i Resource Manager. Du kan skapa en anslutning mellan virtuella nätverk så att resurserna i ett VNet kan kommunicera direkt med resurserna i ett annat.

#### <a name="vnet-peering"></a>VNet-peering
Du kan använda VNet-peering för att skapa anslutningen, förutsatt att ditt virtuella nätverk uppfyller vissa krav. Ingen VNet-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Distributionsmodeller och metoder för VNet-till-VNet
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="point-to-site"></a>Punkt-till-plats
Med en VPN-gatewayanslutning med P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. Du kan upprätta VPN-anslutningen genom att starta den från klientdatorn. Detta är en användbar lösning när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett VNet. P2S-anslutningar kan användas tillsammans med S2S-anslutningar via samma VPN-gateway, under förutsättning att alla konfigurationskrav för båda anslutningarna är kompatibla.

![Punkt till plats-anslutning](./media/vpn-gateway-about-vpngateways/demop2s.png "punkt-till-plats")

### <a name="deployment-models-and-methods-for-point-to-site"></a>Distributionsmodeller och metoder för punkt-till-plats
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="expressroute"></a>ExpressRoute
[!INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

I en ExpressRoute-anslutning konfigureras en virtuell nätverksgateway med gatewaytypen ”ExpressRoute” istället för ”Vpn”. Mer information om ExpressRoute finns i [Teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a>Plats-till-plats- och samexisterande ExpressRoute-anslutningar
ExpressRoute är en direkt, dedikerad anslutning från ditt WAN (inte över offentliga Internet) till Microsoft Services, inklusive Azure. VPN-trafiken från plats-till-plats transporteras krypterat över offentligt Internet. Det finns flera fördelar med att kunna konfigurera VPN-anslutningar för plats-till-plats- och ExpressRoute-anslutningar för samma virtuella nätverk.

Du kan konfigurera ett VPN för plats-till-plats som en säker redundansväxlingsväg för ExpressRoute, eller använda VPN för plats-till-plats för att ansluta till platser som inte finns i ditt nätverk, men som ansluts via ExpressRoute. Lägg märke till att detta kräver två VNet-gateways för samma virtuella nätverk, en med -GatewayType Vpn och en annan med -GatewayType ExpressRoute.

![Samexistera anslutning](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Distributionsmodeller och metoder för S2S och ExpressRoute
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="next-steps"></a>Nästa steg
Planera konfigurationen av din VPN-gateway. Se [VPN Gateway Planning and Design](vpn-gateway-plan-design.md) (Planering och design för VPN-gateway) och [Connecting your on-premises network to Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md) (Ansluta ditt lokala nätverk till Azure).




<!--HONumber=Jan17_HO1-->


