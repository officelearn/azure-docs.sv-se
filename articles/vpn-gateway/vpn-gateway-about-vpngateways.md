---
title: Om Azure VPN Gateway
description: Läs mer om vad en VPN-gateway är och hur du kan använda en VPN-gateway för anslutning till ett virtuellt Azure-nätverk. Du kan bland annat använda IPsec/IKE-lösningar mellan olika platser eller VNet, eller från punkter till lokala VPN.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: c4a406961444845fef783c47942924b01b7aa646
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241460"
---
# <a name="what-is-vpn-gateway"></a>Vad är en VPN-gateway?

En VPN-gateway är en viss typ av virtuell nätverksgateway som används till att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en lokal plats via internet. Du kan också använda en VPN-gateway till att skicka krypterad trafik mellan virtuella Azure-nätverk via Microsofts nätverk. Varje virtuellt nätverk kan bara ha en VPN-gateway. Du kan dock skapa flera anslutningar till samma VPN-gateway. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar på den tillgängliga bandbredden.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Vad är en virtuell nätverksgateway?

En virtuell nätverksgateway består av två eller flera virtuella datorer som distribueras till ett visst undernät som du skapar som kallas *gateway-undernätet*. Virtuella virtuella virtuella nätverksgateway-datorer innehåller routningstabeller och kör specifika gatewaytjänster. Dessa virtuella datorer skapas när du skapar den virtuella nätverksgatewayen. Du kan inte konfigurera de virtuella datorerna som ingår i den virtuella nätverksgatewayen direkt.

En inställning som du konfigurerar för en virtuell nätverksgateway är gatewaytypen. Gateway-typen anger hur den virtuella nätverksgatewayen ska användas och vilka åtgärder gatewayen vidtar. Gatewaytypen "Vpn" anger att den typ av virtuell nätverksgateway som skapas är en VPN-gateway i stället för en ExpressRoute-gateway. Ett virtuellt nätverk kan ha två virtuella nätverksgateways. en VPN-gateway och en ExpressRoute-gateway - som är fallet med [samtidiga](#coexisting) anslutningskonfigurationer. Se [Gatewaytyper](vpn-gateway-about-vpn-gateway-settings.md#gwtype) för mer information.

VPN-gateways kan distribueras i Azure-tillgänglighetszoner. Det ger flexibilitet, skalbarhet och högre tillgänglighet för virtuella nätverksgatewayer. Distribution av gatewayer i Azure-tillgänglighetszoner skiljer gatewayerna åt fysiskt och logiskt i en region, samtidigt som din lokala nätverksanslutning till Azure skyddas mot fel på zonnivå. se [Om zons redundanta virtuella nätverksgatewayer i Azure-tillgänglighetszoner](about-zone-redundant-vnet-gateways.md)

Det kan ta upp till 45 minuter att skapa en virtuell nätverksgateway. När du skapar en virtuell nätverksgateway distribueras de virtuella gatewaydatorerna till gatewayundernätet och konfigureras med de inställningar du anger. När du har skapat en VPN-gateway kan du skapa en VPN-tunnelanslutning med IPsec/IKE mellan denna VPN-gateway och en annan VPN-gateway (VNet-till-VNet), eller en VPN-tunnel med IPsec/IKE mellan VPN-gatewayen och en lokal VPN-enhet (Plats-till-plats). Du kan också skapa en VPN-anslutning från Punkt till plats (VPN via OpenVPN, IKEv2 eller SSTP), som gör att du kan ansluta till ditt virtuella nätverk från en fjärrplats, till exempel från en konferens eller hemifrån.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Konfigurera en VPN gateway

En anslutning för VPN-gateway är beroende av flera resurser som är konfigurerade med specifika inställningar. De flesta av resurserna kan konfigureras separat, men vissa resurser måste konfigureras i en viss ordning.

### <a name="settings"></a><a name="settings"></a>Inställningar

De inställningar som du väljer för varje resurs är viktiga för att skapa en lyckad anslutning. Information om enskilda resurser och inställningar för VPN Gateway finns [Om inställningar för VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md). Den här artikeln innehåller information om gatewaytyper, gateway-SKU:er, VPN-typer, anslutningstyper, gatewayundernät, lokala nätverksgatewayer och andra resursinställningar som du kan använda.

### <a name="deployment-tools"></a><a name="tools"></a>Distributionsverktyg

Du kan börja skapa och konfigurera resurser med hjälp av ett konfigurationsverktyg, till exempel Azure Portal. Du kan senare välja att byta till ett annat verktyg, som PowerShell, för att konfigurera ytterligare resurser eller ändra befintliga resurser om det behövs. För närvarande går det inte att konfigurera alla resurser och resursinställningar på Azure Portal. Anvisningarna i artiklarna för varje anslutningstopologi anger om ett specifikt konfigurationsverktyg behövs. 

### <a name="deployment-model"></a><a name="models"></a>Distributionsmodell

Det finns för närvarande två distributionsmodeller för Azure. När du konfigurerar en VPN-gateway varierar instruktionerna som du följer beroende på vilken distributionsmodell du använde för att skapa det virtuella nätverket. Om du exempelvis har skapat ditt VNet med den klassiska distributionsmodellen, ska du använda riktlinjerna och instruktionerna för den modellen när du skapar och konfigurerar dina VPN Gateway-inställningar. Mer information om distributionsmodellerna finns i [Förstå Resource Manager- och klassiska distributionsmodeller](../azure-resource-manager/management/deployment-models.md).

### <a name="planning-table"></a><a name="planningtable"></a>Planeringstabell

Tabellen nedan kan hjälpa dig att bestämma det bästa anslutningsalternativet för din lösning.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU:er

När du skapar en virtuell nätverksgateway anger du vilken gateway-SKU som du vill använda. Välj den SKU som uppfyller dina krav baserat på typerna av arbetsbelastning, dataflöden, funktioner och serviceavtal.

* Mer information om gateway-SKU:er, inklusive funktioner som stöds, produktion och utvecklingstest och konfigurationssteg, finns i artikeln [VPN Gateway Settings - Gateway SKUs.](vpn-gateway-about-vpn-gateway-settings.md#gwsku)
* Äldre SKU-information finns i [Arbeta med äldre SKU:er](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Gateway-SKU:er efter tunnel, anslutning och dataflöde

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="connection-topology-diagrams"></a><a name="diagrams"></a>Diagram för anslutningstopologi

Det är viktigt att känna till att det finns olika konfigurationer för VPN-gatewayanslutningar. Du måste bestämma vilken konfiguration som passar bäst för dina behov. I avsnitten nedan kan du se information och topologidiagram om följande VPN-gatewayanslutningar: följande avsnitt innehåller tabeller som visar:

* tillgänglig distributionsmodell
* tillgängliga konfigurationsverktyg
* länkar som tar dig direkt till en artikel om en sådan finns

Använd diagrammen och beskrivningarna för att välja den anslutningstopologi som passar dina behov. Diagrammen visar de viktigaste grundläggande topologierna, men du kan skapa mer komplexa konfigurationer och använda diagrammen som utgångspunkt.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Plats-till-plats och flera platser (IPsec/IKE VPN-tunnel)

### <a name="site-to-site"></a><a name="S2S"></a>Plats-till-plats-anslutning

En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer. En S2S-anslutning kräver en VPN-enhet som finns lokalt och som har en offentlig IP-adress tilldelad. Om du vill ha information om att välja en VPN-enhet kan du läsa [vanliga frågor och svar om VPN Gateway – VPN-enheter](vpn-gateway-vpn-faq.md#s2s).

![Exempel på Azure VPN Gateway-anslutningar för plats-till-plats](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a><a name="Multi"></a>Flera webbplatser

Den här typen av anslutning är en variant av plats-till-plats-anslutningen. Du skapar fler än en VPN-anslutning från din virtuella nätverksgateway, vanligtvis sker anslutningen till flera lokala platser. När du arbetar med flera anslutningar måste du använda en RouteBased VPN-typ (kallas även en ”dynamisk gateway” för klassiska virtuella nätverk). Eftersom varje virtuellt nätverk bara kan ha en VPN-gateway delar alla anslutningar via gatewayen på den tillgängliga bandbredden. Den här typen av anslutning kallas ofta för en ”anslutning till flera platser”.

![Exempel på Azure VPN Gateway-anslutningar för flera platser](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Distributionsmodeller och metoder för plats-till-plats och flera platser

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>Punkt-till-plats-VPN

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som behöver ansluta till virtuella Azure-nätverk från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk.

Till skillnad från S2S-anslutningar, kräver P2S-anslutningar en lokal offentlig IP-adress eller en VPN-enhet. P2S-anslutningar kan användas tillsammans med S2S-anslutningar via samma VPN-gateway, under förutsättning att alla konfigurationskrav för båda anslutningarna är kompatibla. Mer information om punkt-till-plats-anslutningar finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).

![Exempel på Azure VPN Gateway-anslutningar från punkt till plats](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Distributionsmodeller och -metoder för P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Anslutningar mellan virtuella nätverk (IPsec/IKE VPN-tunnel)

Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på nästan samma sätt som du ansluter ett VNet till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan till och med kombinera VNet-till-VNet-kommunikation med anslutningskonfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

De virtuella nätverk som du ansluter kan finnas:

* i samma eller olika regioner
* i samma eller olika prenumerationer 
* i samma eller olika distributionsmodeller

![Exempel på VNet-till-VNet-anslutningar med Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Anslutningar mellan distributionsmodeller

Azure har för närvarande två distributionsmodeller: klassisk och Resource Manager. Om du har använt Azure ett tag har du förmodligen virtuella Azure-datorer och instansroller som kör i ett klassiskt VNet. Dina nyare virtuella datorer och rollinstanser kanske körs i ett VNet som skapats i Resource Manager. Du kan skapa en anslutning mellan virtuella nätverk så att resurserna i ett VNet kan kommunicera direkt med resurserna i ett annat.

### <a name="vnet-peering"></a>VNet-peering

Du kan använda VNet-peering för att skapa anslutningen, förutsatt att ditt virtuella nätverk uppfyller vissa krav. Ingen VNet-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Distributionsmodeller och metoder för VNet-till-VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (privat anslutning)

Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning som tillhandahålls av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning.

ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

För ExpressRoute-anslutningar används en virtuell nätverksgateway som en del i den obligatoriska konfigurationen. I en ExpressRoute-anslutning konfigureras den virtuella nätverksgatewayen med gatewaytypen ”ExpressRoute” istället för ”Vpn”. Även om trafiken i en ExpressRoute-krets inte är krypterad som standard så kan du skapa en lösning där du skickar krypterad trafik i en ExpressRoute-krets. Mer information om ExpressRoute finns i [Teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Anslutningar från plats till plats och ExpressRoute samexisterar

ExpressRoute är en direkt, privat anslutning från ditt WAN (inte över offentliga internet) till Microsoft-tjänster, däribland Azure. VPN-trafiken från plats-till-plats transporteras krypterat över offentligt Internet. Det finns flera fördelar med att kunna konfigurera VPN-anslutningar för plats-till-plats- och ExpressRoute-anslutningar för samma virtuella nätverk.

Du kan konfigurera ett VPN för plats-till-plats som en säker redundansväxlingsväg för ExpressRoute, eller använda VPN för plats-till-plats för att ansluta till platser som inte finns i ditt nätverk, men som ansluts via ExpressRoute. Den här konfigurationen kräver två virtuella nätverksgateways för samma virtuella nätverk, en med gatewaytypen Vpn och en annan med gatewaytypen ExpressRoute.

![Exempel på samtidiga ExpressRoute- och VPN Gateway-anslutningar](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Distributionsmodeller och -metoder för samtidig S2S och ExpressRoute

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Prissättning

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Se [Gateway-SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er för VPN Gateway.

## <a name="faq"></a><a name="faq"></a>Faq

Vanliga frågor om VPN Gateway finns i [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Nästa steg

- Mer information finns i avsnittet [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md).
- Läs [Prenumerations- och tjänstbegränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
