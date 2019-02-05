---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98ea4d78a473123708be6e371587252acad6ffcd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205122"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Vad är skillnaden mellan en virtuell nätverksgateway i Azure (VPN Gateway) och en Azure Virtual WAN-vpngateway?

Virtual WAN tillhandahåller storskalig plats-till-plats-anslutning och byggs för dataflöde, skalbarhet och användarvänlighet. Funktionen med ExpressRoute- och punkt-till-plats-anslutning är för närvarande i förhandsversion. Automatiskt tillhandahållande av CPE-grenenheter och anslutning till Azure Virtual WAN. Enheterna finns tillgängliga från ett växande ekosystem av SD-WAN- och VPN-partner. Se [listan över föredragna partner](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Vilken enhetsprovider (Virtual WAN-partner) stöds vid start? 

Just nu stöds den helt automatiserade Virtual WAN-upplevelsen av många partner. Mer information finns i [Virtual WAN-partner](https://go.microsoft.com/fwlink/p/?linkid=2019615). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Vilka är automatiseringsstegen för virtuella WAN-partner?

Information om automatiseringssteg för partner finns i avsnittet om [automatisering för virtuella WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Måste jag använda en önskad partnerenhet?

Nej. Du kan använda valfri VPN-kompatibel enhet som följer Azure-kraven för IKEv2/IKEv1 IPsec-stöd.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hur automatiserar Virtual WAN-partner anslutningsmöjligheter med Azure Virtual WAN?

Programvarudefinierade anslutningslösningar hanterar vanligtvis gren-enheter med hjälp av en kontrollant eller ett enhetsetableringscenter. Kontrollanten kan automatisera anslutningar till Azure Virtual WAN med hjälp av Azure API:er. Läs mer i informationen om automatisering för virtuell WAN-partner.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Ändrar Virtual WAN några befintliga anslutningsfunktioner?   

Inga befintliga anslutningsfunktioner i Azure ändras.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Finns nya Resource Manager-resurser tillgängliga för Virtual WAN?
  
Ja, Virtual WAN introducerar ny Resource Manager-resurser. Mer information finns i [Översikt](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hur många VPN-enheter kan ansluta till en enda hubb?

Upp till 1 000 anslutningar stöds per virtuell hubb. Varje anslutning består av två tunnlar i konfigurationen aktiv-aktiv. Tunnlarna avslutas i en Azure Virtual Hub-vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan den lokala VPN-enheten ansluta till flera hubbar?

Ja. När trafikflödet påbörjas sker det från den lokala enheten till närmaste Microsoft Edge och sedan till den virtuella hubben.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Stöds Global VNet-peering med Azure Virtual WAN? 

 Nej.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Kan ekernätverk som är anslutna till en virtuell hubb kommunicera med varandra?

Ja. Ekernätverk kan kommunicera direkt via peering för virtuellt nätverk. Vi stöder dock inte virtuella nätverk som transitivt kommunicerar via hubben. Mer information finns i [Virtual Network-peering](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan jag distribuera och använda virtuell installation av mitt favoritnätverk (i ett virtuellt NVA-nätverk) med Azure Virtual WAN?

Ja, du kan ansluta önskat NVA-VNet (virtuell nätverksinstallation) till Azure Virtual WAN. Anslut först NVA-VNet till hubben med en virtuell hubbnätverksanslutning. Skapa sedan en virtuell hubbväg med ett nexthop som pekar på den virtuella installationen. Du kan använda flera vägar i routningstabellen för den virtuella hubben. Alla ekrar som är anslutna till NVA-VNet måste dessutom vara anslutna till den virtuella hubben för att säkerställa att VNet-ekervägarna sprids till lokala system.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Kan ett NVA-nätverk ha en virtuell nätverksgateway?

Nej. Det virtuella NVA-nätverket kan inte ha en virtuell nätverksgateway om den är ansluten till den virtuella hubben. 

### <a name="is-there-support-for-bgp"></a>Stöds BGP?

Ja, BGP stöds. För att säkerställa att routningar från ett virtuellt NVA-VNet annonseras på ett lämpligt sätt måste ekrar inaktivera BGP om de är anslutna till ett virtuellt NVA-VNet, som i sin tur är anslutet till en virtuell hubb. Anslut dessutom eker-VNet till den virtuella hubben för att säkerställa att VNet-ekervägarna sprids till lokala system.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kan jag dirigera trafik med hjälp av UDR i den virtuella hubben?

Ja, du kan dirigera trafik till ett virtuellt nätverk med hjälp av routningstabellen för den virtuella hubben.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Finns det licens- eller prisinformation om Virtual WAN?
 
Ja. Se sidan med [prissättning](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hur registreras nya partner som inte visas i startpartnerlistan?

Skicka ett e-postmeddelande till azurevirtualwan@microsoft.com. En perfekt partner är en partner som har en enhet som kan etableras för IKEv1 eller IKEv2 IPSec-anslutning.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Är det möjligt att konstruera Azure Virtual WAN med en Resource Manager-mall?

En enkel konfiguration av ett virtuellt WAN med en hubb och en vpnsite kan skapas med hjälp av en [Azure-snabbstartsmall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN är främst en REST- eller portaldriven tjänst.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Tillåts gren-till-gren-anslutningar i Virtual WAN?

Ja, det finns stöd för gren-till-gren-anslutningar i Virtual WAN för VPN och VPN till ExpressRoute. VPN plats-till-plats är allmänt tillgängligt, men ExpressRoute och punkt-till-plats är för närvarande i förhandsversion.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Kan gren-till-gren-trafik användas i Azure Virtual WAN?

Ja.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Hur skiljer sig Virtual WAN från den befintliga Azure Virtual Network-gatewayen?

Det virtuella privata nätverkets Virtual Network-gateway är begränsad till 30 tunnlar. För anslutningar bör du använda Virtual WAN för ett storskaligt virtuellt privat nätverk. Du kan ansluta upp till 1 000 grenanslutningar med 2 Gb/s i hubben för alla regioner förutom regionen Västra centrala. För regionen Västra centrala är 20 Gbit/s är tillgängligt. Vi kommer att lansera 20 Gbit/s till ytterligare regioner i framtiden. En anslutning är en aktiv-aktiv-tunnel från den lokala VPN-enheten till den virtuella hubben. Du kan ha en hubb per region, vilket innebär att du kan ansluta fler än 1 000 grenar mellan hubbar.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Kräver detta Virtual WAN ExpressRoute från varje plats?

Nej, Virtual WAN kräver inte ExpressRoute från varje plats. Virtual WAN använder IPsec-standardanslutning mellan platser via Internet-länkar från enheten till en Azure Virtual WAN-hubb. Dina platser kan anslutas till ett providernätverk med hjälp av en ExpressRoute-krets. För platser som är anslutna via ExpressRoute i den virtuella hubben (i förhandsversion) kan platser ha gren-till-gren-trafikflöde mellan VPN och ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Finns det någon gräns för nätverksdataflöde vid användning av Azure Virtual WAN?

Antalet grenar är begränsat till 1 000 anslutningar per hubb/region och totalt 2 G i hubben. Undantaget är USA, västra centrala, som har högst 20 Gbit/s. Vi kommer att lansera 20 Gbit/s till andra regioner i framtiden.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Tillåter Virtual WAN att den lokala enheten använder flera Internetleverantörer samtidigt eller är det alltid en enda VPN-tunnel?

Ja, du kan ha tunnlar i aktiv-aktiv (2 tunnlar = 1 Azure Virtual WAN-anslutning) från en enda gren beroende på grenenheten.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hur dirigeras trafiken i Azures stamnät?

Trafiken följer följande mönster: grenenhet ->ISP->Microsoft Edge->Microsoft DC->Microsoft Edge->ISP->grenenhet

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Vad behöver du på varje plats i den här modellen? Bara en Internetanslutning?

Ja. En Internetanslutning och fysisk enhet, helst från våra integrerade [partner](https://go.microsoft.com/fwlink/p/?linkid=2019615). Du kan manuellt hantera konfigurationen och anslutningen till Azure från önskad enhet.
