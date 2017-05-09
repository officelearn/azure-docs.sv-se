---
title: "Översikt över VPN Gateway: Skapa VPN-anslutningar till virtuella Azure-nätverk på olika platser | Microsoft Docs"
description: "Den här översikten över VPN Gateway beskriver hur du kan ansluta till virtuella Azure-nätverk med hjälp av en VPN-anslutning via Internet. Översikten innehåller också diagram över grundläggande anslutningskonfigurationer."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: afef396c17c62f5b980ac1ffdbfc7a7db7a8cdd2
ms.contentlocale: sv-se
ms.lasthandoff: 04/27/2017


---
# <a name="about-vpn-gateway"></a>Om VPN Gateway
Om du vill skicka nätverkstrafik mellan ett virtuellt Azure-nätverk och lokala platser måste du skapa en virtuell nätverksgateway för ditt virtuella nätverk. En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik över en offentlig anslutning. Du kan också använda VPN-gatewayer för att skicka trafik mellan virtuella Azure-nätverk över Microsoft-nätverket.

Det finns två typer av virtuella nätverksgatewayer: ExpressRoute och Vpn. När du skapar en virtuell nätverksgateway anger du vilken typ av gateway som du vill skapa. En VPN-gateway är en virtuell nätverksgateway som använder gatewaytypen Vpn. 

Varje virtuellt nätverk kan ha två virtuella nätverksgatewayer, men bara en av varje typ. Du kan skapa flera anslutningar till en enda VPN-gateway beroende på de inställningar som du väljer. Ett exempel på detta är en konfiguration med anslutning till flera platser. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar, inklusive punkt-till-plats-VPN, den bandbredd som är tillgänglig för gatewayen.

## <a name="configuring-a-vpn-gateway"></a>Konfigurera en VPN-gateway
En anslutning för VPN-gateway är beroende av flera resurser som är konfigurerade med specifika inställningar. De flesta resurserna kan konfigureras separat, även om de måste konfigureras i en viss ordning i vissa fall.

###<a name="settings"></a>Inställningar
De inställningar som du väljer för varje resurs är viktiga för att skapa en lyckad anslutning. Information om enskilda resurser och inställningar för VPN-gateway finns [Om inställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md). Här finns information om gatewaytyper, VPN-typer, anslutningstyper, gateway-undernät, lokala nätverksgatewayer och andra resursinställningar som du kan använda.

###<a name="deployment-tools"></a>Distributionsverktyg
Du kan börja skapa och konfigurera resurser med hjälp av ett konfigurationsverktyg, till exempel Azure Portal. Du kan senare bestämma dig för att växla till ett annat verktyg, som PowerShell, för att konfigurera ytterligare resurser eller ändra befintliga resurser i tillämpliga fall. För närvarande går det inte att konfigurera alla resurser och resursinställningar på Azure Portal. Anvisningarna i artiklarna för varje anslutningstopologi anger om ett specifikt konfigurationsverktyg behövs. 

###<a name="deployment-model"></a>Distributionsmodell
När du konfigurerar en VPN-gateway varierar instruktionerna som du följer beroende på vilken distributionsmodell du använde för att skapa det virtuella nätverket. Om du exempelvis har skapat ditt VNet med den klassiska distributionsmodellen, ska du använda riktlinjerna och instruktionerna för den modellen när du skapar och konfigurerar dina VPN Gateway-inställningar. Mer information om distributionsmodellerna finns i [Förstå Resource Manager- och klassiska distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="diagrams"></a>Diagram för anslutningstopologi
Det är viktigt att känna till att det finns olika konfigurationer för VPN-gatewayanslutningar. Du måste bestämma vilken konfiguration som passar bäst för dina behov. I avsnitten nedan kan du se information och topologidiagram om följande VPN-gatewayanslutningar: följande avsnitt innehåller tabeller som visar:

* tillgänglig distributionsmodell
* tillgängliga konfigurationsverktyg
* länkar som tar dig direkt till en artikel om en sådan finns

Använd diagrammen och beskrivningarna för att välja den anslutningstopologi som passar dina behov. Diagrammen visar de viktigaste grundläggande topologierna, men du kan skapa mer komplexa konfigurationer och använda diagrammen som utgångspunkt.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Plats-till-plats och flera platser (IPsec/IKE VPN-tunnel)
### <a name="S2S"></a>Plats-till-plats-anslutning
En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats en offentlig IP-adress och som inte finns bakom en NAT. S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer.   

![Exempel på Azure VPN Gateway-anslutningar för plats-till-plats](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Anslutning till flera platser
Den här typen av anslutning är en variant av plats-till-plats-anslutningen. Du skapar fler än en VPN-anslutning från din virtuella nätverksgateway, vanligtvis sker anslutningen till flera lokala platser. När du arbetar med flera anslutningar måste du använda en RouteBased VPN-typ (kallas även en ”dynamisk gateway” för klassiska virtuella nätverk). Eftersom varje virtuellt nätverk bara kan ha en VPN-gateway delar alla anslutningar via gatewayen på den tillgängliga bandbredden. Det här kallas ofta en ”anslutning till flera platser”.

![Exempel på Azure VPN Gateway-anslutningar för flera platser](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Distributionsmodeller och metoder för plats-till-plats och flera platser
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Punkt-till-plats (VPN över SSTP)
Med en VPN-gatewayanslutning med P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. Du kan upprätta VPN-anslutningen genom att starta den från klientdatorn. Detta är en användbar lösning när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett VNet. P2S-anslutningar kan användas tillsammans med S2S-anslutningar via samma VPN-gateway, under förutsättning att alla konfigurationskrav för båda anslutningarna är kompatibla.

![Exempel på Azure VPN-Gateway-anslutningar för punkt-till-plats](./media/vpn-gateway-about-vpngateways/vpngateway-point-to-site-connection-diagram.png)

### <a name="deployment-models-and-methods-for-point-to-site"></a>Distributionsmodeller och metoder för punkt-till-plats
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Anslutningar mellan virtuella nätverk (IPsec/IKE VPN-tunnel)
Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på nästan samma sätt som du ansluter ett VNet till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan till och med kombinera VNet-till-VNet-kommunikation med anslutningskonfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

De virtuella nätverk som du ansluter kan finnas:

* i samma eller olika regioner
* i samma eller olika prenumerationer 
* i samma eller olika distributionsmodeller

![Exempel på VNet-till-VNet-anslutningar med Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

###<a name="connections-between-deployment-models"></a>Anslutningar mellan distributionsmodeller
Azure har för närvarande två distributionsmodeller: klassisk och Resource Manager. Om du har använt Azure ett tag har du förmodligen virtuella Azure-datorer och instansroller som kör i ett klassiskt VNet. Dina nyare virtuella datorer och rollinstanser kanske körs i ett VNet som skapats i Resource Manager. Du kan skapa en anslutning mellan virtuella nätverk så att resurserna i ett VNet kan kommunicera direkt med resurserna i ett annat.

###<a name="vnet-peering"></a>VNet-peering
Du kan använda VNet-peering för att skapa anslutningen, förutsatt att ditt virtuella nätverk uppfyller vissa krav. Ingen VNet-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

###<a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Distributionsmodeller och metoder för VNet-till-VNet
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (dedicerad privat anslutning)
Med Microsoft Azure ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en dedikerad privat anslutning från en anslutningsleverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning.

ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

En ExpressRoute-anslutning använder inte en VPN-gateway, men en virtuell nätverksgateway används som en del i den obligatoriska konfigurationen. I en ExpressRoute-anslutning konfigureras den virtuella nätverksgatewayen med gatewaytypen ”ExpressRoute” istället för ”Vpn”. Mer information om ExpressRoute finns i [Teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Plats-till-plats- och samexisterande ExpressRoute-anslutningar
ExpressRoute är en direkt, dedikerad anslutning från ditt WAN (inte över offentliga Internet) till Microsoft Services, inklusive Azure. VPN-trafiken från plats-till-plats transporteras krypterat över offentligt Internet. Det finns flera fördelar med att kunna konfigurera VPN-anslutningar för plats-till-plats- och ExpressRoute-anslutningar för samma virtuella nätverk.

Du kan konfigurera ett VPN för plats-till-plats som en säker redundansväxlingsväg för ExpressRoute, eller använda VPN för plats-till-plats för att ansluta till platser som inte finns i ditt nätverk, men som ansluts via ExpressRoute. Den här konfigurationen kräver två virtuella nätverksgateways för samma virtuella nätverk, en med gatewaytypen Vpn och en annan med gatewaytypen ExpressRoute.

![Exempel på samtidiga ExpressRoute- och VPN Gateway-anslutningar](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Distributionsmodeller och metoder för S2S och ExpressRoute
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Priser
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>Gateway-SKU:er
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Se [Gateway-SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er för VPN-gateway.

### <a name="estimated-aggregate-throughput-by-sku"></a>Beräknat aggregerat dataflöde av SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Vanliga frågor om VPN-gateway finns i [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Nästa steg
- Planera konfigurationen av din VPN-gateway. Se [Planering och design för VPN-gateway](vpn-gateway-plan-design.md).
- Mer information finns i avsnittet [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md).
- Läs [Prenumerations- och tjänstbegränsningar](../azure-subscription-service-limits.md#networking-limits).


