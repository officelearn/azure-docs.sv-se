---
title: 'Planering och design för anslutningar mellan platser: Azure VPN Gateway | Microsoft Docs'
description: Lär dig mer om VPN-Gateway planering och design för mellan lokala, hybrid och VNet-till-VNet-anslutningar
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/27/2017
ms.author: yushwang
ms.openlocfilehash: 7802061ba09a30ca34ed3804ace846118c5edb9b
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235378"
---
# <a name="planning-and-design-for-vpn-gateway"></a>Planering och design för VPN Gateway

Planering och utformning av din på flera platser och VNet-till-VNet-konfigurationer kan vara antingen enkla eller komplicerade, beroende på nätverkets behov. Den här artikeln beskriver grundläggande överväganden för planering och design.

## <a name="planning"></a>Planera

### <a name="compare"></a>Anslutningsalternativ för flera platser

Om du vill ansluta din lokala platser på ett säkert sätt till ett virtuellt nätverk har tre olika sätt att göra detta: Plats-till-plats, punkt-till-plats och ExpressRoute. Jämför olika anslutningar mellan lokala anslutningar som är tillgängliga. Vilket alternativ som väljs kan lita på olika överväganden, till exempel:

* Vilken typ av dataflöde kräver din lösning?
* Vill du kommunicera över offentligt Internet via en säker VPN eller via en privat anslutning?
* Har du en offentlig IP-adress som kan användas?
* Planerar du att använda någon VPN-enhet? I så fall, är den kompatibel?
* Ansluter du bara några få datorer eller vill du ha en permanent anslutning för platsen?
* Vilken typ av VPN-gateway krävs för lösningen som du vill skapa?
* Vilken gateway-SKU bör du använda?

### <a name="planningtable"></a>Planeringstabell

Tabellen nedan kan hjälpa dig att bestämma det bästa anslutningsalternativet för din lösning.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>Gateway-SKU:er

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Arbetsflöde

I följande lista beskrivs vanliga arbetsflödet för molnanslutning:

1. Utforma och planera din topologi för anslutning och ange-adressutrymmen för alla nätverk som du vill ansluta till.
2. Skapa ett Azure-nätverk. 
3. Skapa en VPN-gateway för det virtuella nätverket.
4. Skapa och konfigurera anslutningar till lokala nätverk eller andra virtuella nätverk (om det behövs).
5. Skapa och konfigurera en punkt-till-plats-anslutning för din Azure VPN-gateway (vid behov).

## <a name="design"></a>Design
### <a name="topologies"></a>Anslutningstopologier

Starta genom att studera diagrammen i den [om VPN Gateway](vpn-gateway-about-vpngateways.md) artikeln. Den här artikeln innehåller grundläggande diagram, distributionsmodeller för varje topologi och tillgängliga distributionsverktyg som du kan använda för att distribuera din konfiguration.

### <a name="designbasics"></a>Design-grunderna

I följande avsnitt beskrivs grunderna för VPN-gateway. 

#### <a name="servicelimits"></a>Begränsningar för nätverk-tjänster

Bläddra igenom tabellerna för att visa [nätverk services gränser](../azure-subscription-service-limits.md#networking-limits). De gränser som anges kan påverka din design.

#### <a name="subnets"></a>Om undernät

När du skapar anslutningar måste du överväga att dina adressintervall för undernätet. Du kan inte ha överlappande undernät-adressintervall. Ett annat överlappande undernät är när ett virtuellt nätverk eller en lokal plats innehåller samma adressutrymmet som innehåller den andra platsen. Det innebär att du måste ditt nätverk tekniker för dina lokala nätverk att få ett intervall som du kan använda för din Azure-IP-adressering utrymme/undernät. Du behöver adressutrymmet som inte används i det lokala nätverket.

Undvika överlappande undernät är också viktigt när du arbetar med VNet-till-VNet-anslutningar. Om dina undernät överlappar varandra och en IP-adress finns i både skicka och målets virtuella nätverk, misslyckas VNet-till-VNet-anslutningar. Azure kan inte skicka data till det andra virtuella nätverket eftersom måladressen är en del av det sändande virtuella nätverket.

VPN-gatewayer kräver ett specifikt undernät som kallas ett gateway-undernät. Alla gateway-undernät måste ha namnet GatewaySubnet för att fungera korrekt. Se till att inte namnger gateway-undernätet ett annat namn och distribuera inte virtuella datorer eller något annat till gateway-undernätet. Se [Gatewayundernät](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Om lokala nätverksgatewayer

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. I den klassiska distributionsmodellen kallas den lokala nätverksgatewayen en lokal nätverksplats. När du konfigurerar en lokal nätverksgateway kan du ge den ett namn Ange den offentliga IP-adressen för den lokala VPN-enhet och ange de adressprefix som finns på en lokal plats. Azure tittar på måladressprefixen för nätverkstrafiken, konsultationer den konfiguration som du har angett för den lokala nätverksgatewayen och dirigerar paket på lämpligt sätt. Du kan ändra adressprefix efter behov. Mer information finns i [lokala nätverksgatewayer](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Om gateway-typer

Det är viktigt att välja rätt gatewaytyp för topologin. Om du väljer fel typ fungerar din gateway inte. Gateway-typen anger hur gatewayen ansluter och är en obligatorisk konfigurationsinställning i Resource Manager-distributionsmodellen.

Gatewaytyper är:

* Vpn
* ExpressRoute

#### <a name="connectiontype"></a>Om anslutningstyper

Varje konfiguration kräver en viss anslutningstyp. Anslutningstyperna är:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Om VPN-typer

Varje konfiguration kräver en viss typ av VPN. Om du kombinerar två konfigurationer, till exempel om du skapar en plats-till-plats-anslutning och en punkt-till-plats-anslutning till samma VNet, måste du använda en VPN-typ som uppfyller båda anslutningskraven.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Följande tabeller visar VPN-typ som det mappas till varje anslutningskonfigurationen. Kontrollera att VPN-typ för din gateway matchar den konfiguration som du vill skapa. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>VPN-enheter för plats-till-plats-anslutningar

Om du vill konfigurera en plats-till-plats-anslutning, oavsett distributionsmodell, behöver du följande objekt:

* En VPN-enhet som är kompatibla med Azure VPN-gatewayer
* En offentlig IPv4-adress som inte ligger bakom en NAT

Du behöver du har erfarenhet av att konfigurera din VPN-enhet eller låta någon som kan konfigurera enheten åt dig.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Överväg att tvingande dirigering Routning

Du kan konfigurera Tvingad tunneltrafik för de flesta konfigurationer. Tvingad tunneltrafik kan du omdirigera eller ”tvinga” all internetriktad trafik tillbaka till din lokala plats via en plats-till-plats-VPN-tunnel för kontroll och granskning. Det här är en kritisk säkerhetskrav för de flesta företag IT principer. 

Utan Tvingad tunneltrafik Internet-bunden trafik från dina virtuella datorer i Azure alltid går igenom från Azure nätverksinfrastrukturen direkt ut till Internet, utan alternativet så att du kan granska eller granska trafiken. Obehörig Internetåtkomst kan leda till avslöjande av information eller andra typer av säkerhetsproblem.

En tvingad tunneltrafik anslutning kan konfigureras i båda distributionsmodellerna och med hjälp av olika verktyg. Mer information finns i [konfigurera Tvingad tunneltrafik](vpn-gateway-forced-tunneling-rm.md).

**Tvingad tunneltrafik diagram**

![Azure VPN-Gateway Tvingad tunneltrafik diagram](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Nästa steg

Se den [VPN Gateway vanliga frågor och svar](vpn-gateway-vpn-faq.md) och [om VPN Gateway](vpn-gateway-about-vpngateways.md) artiklar för mer information som hjälper dig med din design.

Mer information om specifika gateway-inställningar finns i [om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md).
