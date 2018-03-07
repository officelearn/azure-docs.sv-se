---
title: "Översikt över VPN Gateway: Skapa VPN-anslutningar till virtuella Azure-nätverk på olika platser | Microsoft Docs"
description: "Den här artikeln beskriver vad en VPN-gateway är och visar hur du kan ansluta till virtuella Azure-nätverk med hjälp av en VPN-anslutning via Internet. Översikten innehåller också diagram över grundläggande anslutningskonfigurationer."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: cherylmc
ms.openlocfilehash: dadddeaac2a7856f8e249db981b018de070e1f3f
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="about-vpn-gateway"></a>Om VPN Gateway

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik över en offentlig anslutning till en lokal plats. Du kan också använda VPN-gatewayer för att skicka krypterad trafik mellan virtuella Azure-nätverk över Microsoft-nätverket. Om du vill skicka krypterad nätverkstrafik mellan ett virtuellt Azure-nätverk och lokala platser måste du skapa en virtuell nätverksgateway för ditt VPN.

Varje virtuellt nätverk kan ha endast en VPN-gateway, men du kan skapa flera anslutningar till samma VPN-gateway. Ett exempel på detta är en konfiguration med anslutning till flera platser. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar, inklusive punkt-till-plats-VPN, den bandbredd som är tillgänglig för gatewayen.

## <a name="whatis"></a>Vad är en virtuell nätverksgateway?

En virtuell nätverksgateway består av två eller flera virtuella datorer som distribueras till ett specifikt undernät med namnet GatewaySubnet. De virtuella datorerna som finns i GatewaySubnet skapas när du skapar den virtuella nätverksgatewayen. Virtuella datorer för virtuell nätverksgateway är konfigurerade för att innehålla routningstabeller och gateway-tjänster som är specifika för gatewayen. Du kan konfigurera de virtuella datorer som är en del av den virtuella nätverksgatewayen direkt och du bör aldrig distribuera ytterligare resurser till GatewaySubnet.

När du skapar en virtuell nätverksgateway med hjälp av gatewaytypen 'Vpn ”, skapas en viss typ av virtuell nätverksgateway som krypterar trafiken: en VPN-gateway. Det kan ta upp till 45 minuter att skapa en VPN-gateway. Detta beror på att de virtuella datorerna för VPN-gateway distribueras till GatewaySubnet och konfigureras med de inställningar som du angett. Den Gateway-SKU som du väljer avgör hur kraftfulla de virtuella datorerna är.

## <a name="configuring"></a>Konfigurera en VPN-gateway

En anslutning för VPN-gateway är beroende av flera resurser som är konfigurerade med specifika inställningar. De flesta resurserna kan konfigureras separat, även om de måste konfigureras i en viss ordning i vissa fall.

### <a name="settings"></a>Inställningar

De inställningar som du väljer för varje resurs är viktiga för att skapa en lyckad anslutning. Information om enskilda resurser och inställningar för VPN-gateway finns [Om inställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md). Den här artikeln innehåller information om gatewaytyper, VPN-typer, anslutningstyper, gatewayundernät, lokala nätverksgatewayer och andra resursinställningar som du kan använda.

### <a name="tools"></a>Distributionsverktyg

Du kan börja skapa och konfigurera resurser med hjälp av ett konfigurationsverktyg, till exempel Azure Portal. Du kan senare välja att byta till ett annat verktyg, som PowerShell, för att konfigurera ytterligare resurser eller ändra befintliga resurser om det behövs. För närvarande går det inte att konfigurera alla resurser och resursinställningar på Azure Portal. Anvisningarna i artiklarna för varje anslutningstopologi anger om ett specifikt konfigurationsverktyg behövs. 

### <a name="models"></a>Distributionsmodell

När du konfigurerar en VPN-gateway varierar instruktionerna som du följer beroende på vilken distributionsmodell du använde för att skapa det virtuella nätverket. Om du exempelvis har skapat ditt VNet med den klassiska distributionsmodellen, ska du använda riktlinjerna och instruktionerna för den modellen när du skapar och konfigurerar dina VPN Gateway-inställningar. Mer information om distributionsmodellerna finns i [Förstå Resource Manager- och klassiska distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="planningtable"></a>Planeringstabell

Tabellen nedan kan hjälpa dig att bestämma det bästa anslutningsalternativet för din lösning.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]


## <a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

## <a name="diagrams"></a>Diagram för anslutningstopologi

Det är viktigt att känna till att det finns olika konfigurationer för VPN-gatewayanslutningar. Du måste bestämma vilken konfiguration som passar bäst för dina behov. I avsnitten nedan kan du se information och topologidiagram om följande VPN-gatewayanslutningar: följande avsnitt innehåller tabeller som visar:

* tillgänglig distributionsmodell
* tillgängliga konfigurationsverktyg
* länkar som tar dig direkt till en artikel om en sådan finns

Använd diagrammen och beskrivningarna för att välja den anslutningstopologi som passar dina behov. Diagrammen visar de viktigaste grundläggande topologierna, men du kan skapa mer komplexa konfigurationer och använda diagrammen som utgångspunkt.

## <a name="s2smulti"></a>Plats-till-plats och flera platser (IPsec/IKE VPN-tunnel)

### <a name="S2S"></a>Plats-till-plats-anslutning

En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer. A S2S-anslutningar kräver en lokal VPN-enhet som tilldelats en offentlig IP-adress och som inte finns bakom en NAT. Om du vill ha information om att välja en VPN-enhet kan du läsa [vanliga frågor och svar om VPN Gateway – VPN-enheter](/vpn-gateway-vpn-faq.md#s2s).

![Exempel på Azure VPN Gateway-anslutningar för plats-till-plats](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Anslutning till flera platser

Den här typen av anslutning är en variant av plats-till-plats-anslutningen. Du skapar fler än en VPN-anslutning från din virtuella nätverksgateway, vanligtvis sker anslutningen till flera lokala platser. När du arbetar med flera anslutningar måste du använda en RouteBased VPN-typ (kallas även en ”dynamisk gateway” för klassiska virtuella nätverk). Eftersom varje virtuellt nätverk bara kan ha en VPN-gateway delar alla anslutningar via gatewayen på den tillgängliga bandbredden. Det här kallas ofta en ”anslutning till flera platser”.

![Exempel på Azure VPN Gateway-anslutningar för flera platser](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Distributionsmodeller och metoder för plats-till-plats och flera platser

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Punkt-till-plats (VPN via IKEv2 eller SSTP)

Med en VPN-gatewayanslutning för punkt-till-plats (P2S) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som behöver ansluta till virtuella Azure-nätverk från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk.

Till skillnad från S2S-anslutningar, kräver P2S-anslutningar en lokal offentlig IP-adress eller en VPN-enhet. P2S-anslutningar kan användas tillsammans med S2S-anslutningar via samma VPN-gateway, under förutsättning att alla konfigurationskrav för båda anslutningarna är kompatibla. Mer information om punkt-till-plats-anslutningar finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).


![Exempel på Azure VPN-Gateway-anslutningar för punkt-till-plats](./media/vpn-gateway-about-vpngateways/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Distributionsmodeller och -metoder för P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Anslutningar mellan virtuella nätverk (IPsec/IKE VPN-tunnel)

Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på nästan samma sätt som du ansluter ett VNet till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan till och med kombinera VNet-till-VNet-kommunikation med anslutningskonfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

De virtuella nätverk som du ansluter kan finnas:

* i samma eller olika regioner
* i samma eller olika prenumerationer 
* i samma eller olika distributionsmodeller

![Exempel på VNet-till-VNet-anslutningar med Azure VPN Gateway](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Anslutningar mellan distributionsmodeller

Azure har för närvarande två distributionsmodeller: klassisk och Resource Manager. Om du har använt Azure ett tag har du förmodligen virtuella Azure-datorer och instansroller som kör i ett klassiskt VNet. Dina nyare virtuella datorer och rollinstanser kanske körs i ett VNet som skapats i Resource Manager. Du kan skapa en anslutning mellan virtuella nätverk så att resurserna i ett VNet kan kommunicera direkt med resurserna i ett annat.

### <a name="vnet-peering"></a>VNET-peering

Du kan använda VNet-peering för att skapa anslutningen, förutsatt att ditt virtuella nätverk uppfyller vissa krav. Ingen VNet-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Distributionsmodeller och metoder för VNet-till-VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (privat anslutning)

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning.

ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

En ExpressRoute-anslutning använder inte en VPN-gateway, men en virtuell nätverksgateway används som en del i den obligatoriska konfigurationen. I en ExpressRoute-anslutning konfigureras den virtuella nätverksgatewayen med gatewaytypen ”ExpressRoute” istället för ”Vpn”. Mer information om ExpressRoute finns i [Teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Plats-till-plats- och samexisterande ExpressRoute-anslutningar

ExpressRoute är en direkt, privat anslutning från ditt WAN (inte över offentliga internet) till Microsoft-tjänster, däribland Azure. VPN-trafiken från plats-till-plats transporteras krypterat över offentligt Internet. Det finns flera fördelar med att kunna konfigurera VPN-anslutningar för plats-till-plats- och ExpressRoute-anslutningar för samma virtuella nätverk.

Du kan konfigurera ett VPN för plats-till-plats som en säker redundansväxlingsväg för ExpressRoute, eller använda VPN för plats-till-plats för att ansluta till platser som inte finns i ditt nätverk, men som ansluts via ExpressRoute. Den här konfigurationen kräver två virtuella nätverksgateways för samma virtuella nätverk, en med gatewaytypen Vpn och en annan med gatewaytypen ExpressRoute.

![Exempel på samtidiga ExpressRoute- och VPN Gateway-anslutningar](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Distributionsmodeller och -metoder för samtidig S2S och ExpressRoute

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Prissättning

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Se [Gateway-SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er för VPN-gateway.

## <a name="faq"></a>Vanliga frågor och svar

Vanliga frågor om VPN-gateway finns i [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md).

## <a name="next-steps"></a>Nästa steg

- Planera konfigurationen av din VPN-gateway. Se [Planering och design för VPN-gateway](vpn-gateway-plan-design.md).
- Mer information finns i avsnittet [Vanliga frågor och svar om VPN-gateway](vpn-gateway-vpn-faq.md).
- Läs [Prenumerations- och tjänstbegränsningar](../azure-subscription-service-limits.md#networking-limits).
- Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
