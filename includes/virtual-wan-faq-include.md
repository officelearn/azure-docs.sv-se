---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/18/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d059cab5668eef8d4dafc1442ca9749a7dcf8c9d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162522"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Vad är skillnaden mellan en virtuell nätverksgateway i Azure (VPN Gateway) och en Azure Virtual WAN-vpngateway?

Virtual WAN tillhandahåller storskalig plats-till-plats-anslutning och byggs för dataflöde, skalbarhet och användarvänlighet. Automatiskt tillhandahållande av CPE-grenenheter och anslutning till Azure Virtual WAN. Enheterna finns tillgängliga från ett växande ekosystem av SD-WAN- och VPN-partner.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Vilken enhetsprovider (Virtual WAN-partner) stöds vid start? 

Just nu stöds den helt automatiserade Virtual WAN-upplevelsen i Citrix och Riverbed. Mer information finns i [Virtual WAN-partner](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Måste jag använda en önskad partnerenhet?

Nej. Du kan använda valfri VPN-kompatibel enhet som följer förhandsversionskraven för IKEv2 IPsec-support.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hur automatiserar Virtual WAN-partner anslutningsmöjligheter med Azure Virtual WAN?

Programvarudefinierade anslutningslösningar hanterar vanligtvis gren-enheter med hjälp av en kontrollant eller ett enhetsetableringscenter. Kontrollanten kan automatisera anslutningar till Azure Virtual WAN med hjälp av Azure API:er. Läs mer i informationen om [automatisering för virtuell WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Ändrar Virtual WAN några befintliga anslutningsfunktioner?   

Inga befintliga anslutningsfunktioner i Azure ändras.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Finns nya Resource Manager-resurser tillgängliga för Virtual WAN?
  
Ja, Virtual WAN introducerar ny Resource Manager-resurser. Mer information finns i [Översikt](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Finns det några särskilda krav för att ta del av förhandsversionen? 

Du måste registrera prenumerationen i förhandsversionen innan du kan konfigurera ett Azure Virtual WAN. Skicka ett e-postmeddelande till <azurevirtualwan@microsoft.com> med ditt prenumerations-ID om du vill registrera dig. Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats. Tills du får bekräftelse att prenumerationen har registrerats kan du inte arbeta med Virtual WAN i portalen.

Överväganden:

* Förhandsversionen är begränsad till de offentliga Azure-regionerna.
* Upp till 100 anslutningar stöds per virtuell hubb. Varje anslutning består av två tunnlar i konfigurationen aktiv-aktiv. Tunnlarna avslutas i en Azure Virtual Hub-vpngateway.
* Överväg att använda den här förhandsversionen om:
  * Du vill distribuera samlad bandbredd på mindre än 1 Gbit/s per virtuell hubb.
  * Du har en VPN-enhet som stöder routningsbaserad konfiguration och IKEv2 IPsec-anslutningar.
  * Du vill prova att använda SD-WAN och operativa grenenheter från startpartner (Riverbed och Citrix).<br>eller<br>Du vill konfigurera gren-till-gren- och gren-till-Azure-anslutningar som inkluderar konfigurationshantering av de lokala enheterna. (Konfigurera själv)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Stöds Global VNet-peering med Azure Virtual WAN? 

 Nej.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Kan ekernätverk som är anslutna till en virtuell hubb kommunicera med varandra?

Ja. Du kan utföra VNet-peering direkt mellan ekrar som är ansluta till en virtuell hubb. Mer information finns i [Virtual Network-peering](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan jag distribuera och använda virtuell installation av mitt favoritnätverk (i ett virtuellt NVA-nätverk) med Azure Virtual WAN?

Ja, du kan ansluta till virtuell installation av ditt virtuella favoritnätverk (NVA) till Azure Virtual WAN, men du behöver routningsfunktioner i hubben som finns i produktvägledningen. Alla ekrar som är anslutna till NVA MNet måste dessutom anslutas till den virtuella hubben. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Kan ett NVA-nätverk ha en virtuell nätverksgateway?

Nej. Det virtuella NVA-nätverket kan inte ha en virtuell nätverksgateway om den är ansluten till den virtuella hubben. 

### <a name="is-there-support-for-bgp"></a>Stöds BGP?

Ja, BGP stöds. För att säkerställa att routningar från ett virtuellt NVA-nätverk annonseras på ett lämpligt sätt måste ekrar inaktivera BGP om de är anslutna till ett virtuellt NVA-nätverk, som i sin tur är anslutet till en virtuell hubb. Anslut dessutom ekernätverken till den virtuella hubben.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kan jag dirigera trafik med hjälp av UDR i den virtuella hubben?

Detta finns i vår produktvägledning. Håll ögonen öppna!

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Finns det licens- eller prisinformation om Virtual WAN?
 
Det tillkommer inga kostnader under förhandsversionen. Aktuella [Azure VPN- och utgående priser](https://azure.microsoft.com/pricing/details/vpn-gateway/) gäller under förhandsversionen.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hur registreras nya partner som inte visas i startpartnerlistan?

Skicka ett e-postmeddelande till azurevirtualwan@microsoft.com. En perfekt partner är en partner som har en enhet som kan etableras för IKEv2 IPSec-anslutning.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Är det möjligt att konstruera Azure Virtual WAN med en Resource Manager-mall?

Vi arbetar på det. För tillfället är tjänsten REST-och portaldriven.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Tillåts gren-till-gren-anslutningar i Virtual WAN?

Ja, det finns stöd för gren-till-gren-anslutningar i Virtual WAN.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Kan gren-till-gren-trafik användas i Azure Virtual WAN?

Ja.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Hur skiljer sig Virtual WAN från den befintliga Azure Virtual Network-gatewayen?

Det virtuella privata nätverkets Virtual Network-gateway är begränsad till 30 tunnlar. För anslutningar bör du använda Virtual WAN för ett storskaligt virtuellt privat nätverk. I den offentliga förhandsversionen är detta begränsat till 100 anslutningar med 1 Gbit/s i hubben.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Kräver detta Virtual WAN ExpressRoute från varje plats?

Nej, Virtual WAN kräver inte ExpressRoute från varje plats. Virtual WAN använder IPsec-standardanslutning mellan platser via Internet-länkar från enheten till en Azure Virtual WAN-hubb.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Finns det någon gräns för nätverksdataflöde vid användning av Azure Virtual WAN?

I den offentliga förhandsversionen är antalet grenar begränsat till 100 anslutningar per hubb/region och totalt 1 G i hubben.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Tillåter Virtual WAN att den lokala enheten använder flera Internetleverantörer samtidigt eller är det alltid en enda VPN-tunnel?

Ja, du kan ha tunnlar i aktiv-aktiv (2 tunnlar = 1 Azure Virtual WAN-anslutning) från en enda gren beroende på grenenheten.

### <a name="how-is-traffic-is-routed-on-the-azure-backbone"></a>Hur dirigeras trafiken i Azure?

Trafiken följer följande mönster: grenenhet ->ISP->Microsoft Edge->Microsoft DC->Microsoft Edge->ISP->grenenhet.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Vad behöver du på varje plats i den här modellen? Bara en Internetanslutning?

Ja. En Internetanslutning och fysisk enhet, helst från våra integrerade partner. Såvida du inte vill hantera konfigurationen och anslutningen till Azure manuellt från önskad enhet.