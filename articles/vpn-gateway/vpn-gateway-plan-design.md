---
title: 'Planering och design för anslutningar mellan platser: Azure VPN-Gateway | Microsoft Docs'
description: Lär dig mer om VPN-Gateway planering och design för anslutningar mellan platser, hybrid och VNet-till-VNet-anslutningar
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: d5aaab83-4e74-4484-8bf0-cc465811e757
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2017
ms.author: cherylmc
ms.openlocfilehash: 0ebc3ef4a64432e993dd6ed69766bb64544fe433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884386"
---
# <a name="planning-and-design-for-vpn-gateway"></a>Planering och design för VPN-gateway

Planera och utforma din mellan platser och VNet-till-VNet-konfigurationer kan vara antingen enkla eller komplexa, beroende på dina nätverk behov. Den här artikeln vägleder dig genom grundläggande överväganden för planering och design.

## <a name="planning"></a>Planera

### <a name="compare"></a>Alternativ för nätverksanslutning mellan platser

Om du vill ansluta din lokala platser på ett säkert sätt till ett virtuellt nätverk du har tre olika sätt att göra det: plats-till-plats, punkt-till-plats och ExpressRoute. Jämför olika mellan lokala anslutningar som är tillgängliga. Det alternativ du väljer kan bero på olika överväganden som:

* Vilken typ av dataflöde kräver din lösning?
* Vill du kommunicera över offentligt Internet via en säker VPN eller via en privat anslutning?
* Har du en offentlig IP-adress som kan användas?
* Planerar du att använda någon VPN-enhet? I så fall, är den kompatibel?
* Ansluter du bara några få datorer eller vill du ha en permanent anslutning för platsen?
* Vilken typ av VPN-gateway krävs för lösningen som du vill skapa?
* Vilken gateway SKU bör du använda?

### <a name="planningtable"></a>Planera tabell

I följande tabell kan hjälpa dig att bestämma det bästa alternativet för anslutning för din lösning.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Arbetsflöde

I följande lista beskrivs vanliga arbetsflödet för molnet anslutning:

1. Utforma och planera din topologi för anslutningen och ange-adressutrymmen för alla nätverk som du vill ansluta till.
2. Skapa ett virtuellt Azure-nätverk. 
3. Skapa en VPN-gateway för det virtuella nätverket.
4. Skapa och konfigurera anslutningar till lokala nätverk eller andra virtuella nätverk (vid behov).
5. Skapa och konfigurera en punkt-till-plats-anslutning för din Azure VPN-gateway (vid behov).

## <a name="design"></a>Design
### <a name="topologies"></a>Topologier för anslutning

Starta genom att studera diagrammen i den [om VPN-Gateway](vpn-gateway-about-vpngateways.md) artikel. Artikeln innehåller vanliga diagram distributionsmodeller för varje topologi och tillgänglig distribution-verktyg som du kan använda för att distribuera din konfiguration.

### <a name="designbasics"></a>Design-grunderna

I följande avsnitt beskrivs grunderna för VPN-gateway. 

#### <a name="servicelimits"></a>Nätverk tjänster gränser

Rulla tabeller för att visa [nätverk services gränser](../azure-subscription-service-limits.md#networking-limits). De gränser som visas kan påverka din design.

#### <a name="subnets"></a>Om undernät

När du skapar anslutningar måste du undernät-intervall. Du kan inte ha överlappande undernät-adressintervall. Ett annat överlappande undernät är när ett virtuellt nätverk eller lokala plats innehåller samma adressutrymme som innehåller den andra platsen. Detta innebär att du måste din nätverket tekniker för dina lokala lokalt nätverk för att dela ut ett intervall som du kan använda för din Azure-IP-adressering utrymme-undernät. Du måste adressutrymme som inte används på det lokala lokala nätverket.

Undvika överlappande undernät är också viktigt när du arbetar med VNet-till-VNet-anslutningar. Om dina undernät överlappar varandra och en IP-adress finns i både skicka och Vnet, misslyckas VNet-till-VNet-anslutningar. Azure kan inte skicka data till andra VNet eftersom måladressen tillhör skicka VNet.

VPN-gatewayer kräver ett specifikt undernät som kallas en gateway-undernätet. Alla gateway-undernät måste ha namnet GatewaySubnet för att fungera korrekt. Se till att inte namnge din gateway-undernätet ett annat namn och inte distribuera virtuella datorer eller något annat till gateway-undernätet. Se [Gateway-undernät](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Om lokala nätverksgatewayer

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. I den klassiska distributionsmodellen kallas den lokala nätverksgatewayen en lokal nätverksplats. När du konfigurerar en lokal nätverksgateway du ge det ett namn, ange den offentliga IP-adressen för lokala VPN-enhet och ange adressprefix i den lokala platsen. Azure tittar på mål-adressprefix för nätverkstrafik, tittar du den konfiguration som du har angett för den lokala nätverksgatewayen och därefter dirigerar paket. Du kan ändra adressprefixen efter behov. Mer information finns i [lokala nätverksgatewayer](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Om gateway-typer

Det är viktigt att välja rätt gateway-typ för din topologi. Om du väljer fel typ fungerar din gateway inte korrekt. Gateway-typen anger hur gatewayen ansluter och är en obligatorisk konfigurationsinställning i Resource Manager-distributionsmodellen.

Gateway-typer är:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>Om anslutningstyper

Varje konfiguration kräver en viss anslutningstyp. Anslutningstyperna är:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Om VPN-typer

Varje konfigurationen kräver en viss typ av VPN. Om du kombinerar två konfigurationer, till exempel om du skapar en plats-till-plats-anslutning och en punkt-till-plats-anslutning till samma VNet, måste du använda en VPN-typ som uppfyller båda anslutningskraven.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Följande tabeller visar VPN-typ som mappas till varje anslutningskonfiguration av. Kontrollera att VPN-typ för din gateway matchar den konfiguration som du vill skapa. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Enheter för VPN för plats-till-plats-anslutningar

Om du vill konfigurera en plats-till-plats-anslutning, oavsett distributionsmodell, behöver du följande:

* En VPN-enhet som är kompatibel med Azure VPN-gatewayer
* En offentlig IPv4-adress som inte finns bakom en NAT

Du måste ha erfarenhet att konfigurera din VPN-enhet eller ha någon som kan konfigurera enheten för dig.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Överväg att framtvingad tunnel Routning

Du kan konfigurera Tvingad tunneling för de flesta konfigurationer. Tvingad tunneling kan du omdirigera eller ”force” alla Internet-bunden trafik tillbaka till din lokala plats via en plats-till-plats VPN-tunnel för kontroll och granskning. Detta är en kritisk säkerhetskraven för de flesta företags IT principer. 

Utan Tvingad tunneling ska Internet-bunden trafik från din virtuella datorer i Azure alltid passerar från Azure nätverksinfrastruktur direkt ut till Internet, utan att du vill granska eller granska trafiken. Obehörig Internetåtkomst kan leda till avslöjande av information och andra typer av säkerhetsintrång.

En anslutning för Tvingad tunneltrafik kan konfigureras i båda distributionsmodellerna och med hjälp av olika verktyg. Mer information finns i [konfigurera Tvingad tunneltrafik](vpn-gateway-forced-tunneling-rm.md).

**Tvingad tunneltrafik diagram**

![Azure VPN-Gateway Tvingad tunneltrafik diagram](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Nästa steg

Finns det [VPN-Gateway FAQ](vpn-gateway-vpn-faq.md) och [om VPN-Gateway](vpn-gateway-about-vpngateways.md) artiklar för mer information som hjälper dig med din design.

Mer information om inställningar för specifika gatewayen finns [om VPN-Gateway inställningar](vpn-gateway-about-vpn-gateway-settings.md).