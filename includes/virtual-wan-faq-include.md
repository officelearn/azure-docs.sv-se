---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/18/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3fa261c6cb76cb95cffc602e5018e480afbb5dae
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891107"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Vad är skillnaden mellan en virtuell nätverksgateway i Azure (VPN Gateway) och en Azure Virtual WAN-vpngateway?

Virtual WAN tillhandahåller storskalig plats-till-plats-anslutning och byggs för dataflöde, skalbarhet och användarvänlighet. Funktionen med ExpressRoute- och punkt-till-plats-anslutning är för närvarande i förhandsversion. CPE grenen enheter autoprovision och Anslut till Azure virtuellt WAN-nätverk. Enheterna finns tillgängliga från ett växande ekosystem av SD-WAN- och VPN-partner. Se [listan över föredragna partner](https://go.microsoft.com/fwlink/p/?linkid=2019615).

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

Ja, BGP stöds. När du skapar en VPN-plats kan ange du BGP-parametrarna i den. Detta kan innebära att alla anslutningar som skapats i Azure för den platsen ska aktiveras för BGP. Även måste om du har ett virtuellt nätverk med en NVA, och om det här NVA virtuella nätverket har kopplats till ett virtuellt WAN-nätverk NAV, för att säkerställa att vägarna från en NVA VNet annonseras korrekt, ekrar som är kopplade till NVA-VNet inaktivera BGP. Dessutom kan ansluta dessa eker virtuella nätverk till det virtuella hubbnätverket så eker VNet vägar har spridits till lokala system.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kan jag dirigera trafik med hjälp av UDR i den virtuella hubben?

Ja, du kan dirigera trafik till ett virtuellt nätverk med hjälp av routningstabellen för den virtuella hubben.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Finns det licens- eller prisinformation om Virtual WAN?
 
Ja. Se sidan med [prissättning](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Hur jag för att beräkna priset för en hubb?
 
Du betalar för tjänsten i hubben. Till exempel skulle 10 grenar eller lokala enheter som kräver för att ansluta till Azure virtuellt WAN ansluta till VPN-slutpunkter i hubben. Kan anta att det här är VPN över 1 skalningsenhet = 500 Mbit/s, det kostar $0.361/ timme. Varje anslutning som ingår debiteras enligt 0,08 USD/timme. För 10 anslutningar, blir den totala kostnaden för tjänsten/tim $0.361 + $. 8 / HR. Data tillkommer för trafiken lämnar azure. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hur registreras nya partner som inte visas i startpartnerlistan?

Skicka ett e-postmeddelande till azurevirtualwan@microsoft.com. En perfekt partner är en partner som har en enhet som kan etableras för IKEv1 eller IKEv2 IPSec-anslutning.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Vad händer om en enhet i använder är inte i listan över virtuella WAN-partner? Kan jag fortfarande använda det för att ansluta till Azure virtuellt WAN VPN?

Ja så länge enheten har stöd för IPsec-IKEv1 eller IKEv2. Virtuellt WAN partner automatisera anslutningen mellan enheten och Azure VPN-slutpunkter. Detta innebär automatiserar steg, till exempel ”ladda upp information gren', 'IPsec och configuration” och ”anslutning”. Eftersom enheten inte är från ett virtuellt WAN-nätverk partnerekosystem, behöver du tungrodda manuellt med Azure-konfiguration och uppdatera din enhet för att konfigurera IPsec-anslutning. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Är det möjligt att konstruera Azure Virtual WAN med en Resource Manager-mall?

En enkel konfiguration av ett virtuellt WAN-nätverk med en hubb och en vpnsite kan skapas med en [Azure-Snabbstart mallen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN är främst en REST- eller portaldriven tjänst.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Tillåts gren-till-gren-anslutningar i Virtual WAN?

Ja, det finns stöd för gren-till-gren-anslutningar i Virtual WAN för VPN och VPN till ExpressRoute. VPN plats-till-plats är allmänt tillgängligt, men ExpressRoute och punkt-till-plats är för närvarande i förhandsversion.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Kan gren-till-gren-trafik användas i Azure Virtual WAN?

Ja.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Hur skiljer sig Virtual WAN från den befintliga Azure Virtual Network-gatewayen?

Det virtuella privata nätverkets Virtual Network-gateway är begränsad till 30 tunnlar. För anslutningar bör du använda Virtual WAN för ett storskaligt virtuellt privat nätverk. Du kan ansluta upp till 1 000 grenanslutningar med 2 Gb/s i hubben för alla regioner förutom regionen Västra centrala. För regionen Västra centrala är 20 Gbit/s är tillgängligt. Vi kommer att lansera 20 Gbit/s till ytterligare regioner i framtiden. En anslutning är en aktiv-aktiv-tunnel från den lokala VPN-enheten till den virtuella hubben. Du kan ha en hubb per region, vilket innebär att du kan ansluta fler än 1 000 grenar mellan hubbar.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hur virtuellt WAN-nätverk stöder SD-WAN-enheter?

Virtuellt WAN partner automatisera IPsec-anslutning till Azure VPN-slutpunkter. Om du använder virtuella WAN partnern som en SD-WAN-provider är den underförstådda att kontrollanten SD-WAN hanterar automation och IPsec-anslutning för Azure VPN-slutpunkter. Om SD-WAN kräver sin egen slutpunkt i stället för Azure VPN för eventuella upphovsrättsskyddad SD-WAN-funktioner, kan du distribuera SD-WAN-slutpunkten i ett virtuellt Azure nätverk och samexistera med Azure virtuellt WAN-nätverk.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Kräver detta Virtual WAN ExpressRoute från varje plats?

Nej, Virtual WAN kräver inte ExpressRoute från varje plats. Virtual WAN använder IPsec-standardanslutning mellan platser via Internet-länkar från enheten till en Azure Virtual WAN-hubb. Dina platser kan anslutas till ett providernätverk med hjälp av en ExpressRoute-krets. För platser som är anslutna via ExpressRoute i den virtuella hubben (i förhandsversion) kan platser ha gren-till-gren-trafikflöde mellan VPN och ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Finns det någon gräns för nätverksdataflöde vid användning av Azure Virtual WAN?

Antalet grenar är begränsat till 1 000 anslutningar per hubb/region och totalt 2 G i hubben. Undantaget är USA, västra centrala, som har högst 20 Gbit/s. Vi kommer att lansera 20 Gbit/s till andra regioner i framtiden.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Tillåter Virtual WAN att den lokala enheten använder flera Internetleverantörer samtidigt eller är det alltid en enda VPN-tunnel?

En anslutning kommer till virtuellt WAN VPN är alltid en aktiv-aktiv-tunnel (för ökad flexibilitet i samma hubbregion) med hjälp av en länk som är tillgängliga på grenen. Den här länken kan vara en ISP-länk på den lokala grenen. Virtuellt WAN ger inte någon särskild logik för att ställa in flera Internetleverantören parallellt; Hantera redundans i ISP på grenen är helt en gren-centric nätverksåtgärd. Du kan använda din favorit SD-WAN-lösning för att göra bana markering på grenen.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hur dirigeras trafiken i Azures stamnät?

Trafiken följer mönstret: grenen enheter -> ISP -> Microsoft edge -> Microsoft-DC (hubbnätverket) -> Microsoft edge -> ISP -> gren enhet

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Vad behöver du på varje plats i den här modellen? Bara en Internetanslutning?

Ja. En Internetanslutning och en fysisk enhet som har stöd för IPsec, helst från våra integrerade [partner](https://go.microsoft.com/fwlink/p/?linkid=2019615). Du kan manuellt hantera konfigurationen och anslutningen till Azure från önskad enhet.