---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 09fe8396b6f0033a2c01d1ef056060a855b23d0a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761444"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Vad är skillnaden mellan en virtuell Azure-nätverksgateway (VPN Gateway) och en virtuell WAN-gateway i Azure?

Virtual WAN tillhandahåller storskalig plats-till-plats-anslutning och byggs för dataflöde, skalbarhet och användarvänlighet. När du ansluter en plats till en virtuell WAN-Gateway för virtuella nätverk skiljer den sig från en vanlig virtuell nätverksgateway som använder en gateway-typ "VPN". När du ansluter en ExpressRoute-krets till en virtuell WAN-hubb används en annan resurs för ExpressRoute-gatewayen än den vanliga virtuella Nätverksgatewayen som använder Gateway-typen ExpressRoute. Virtuellt WAN stöder upp till 20 Gbit/s aggregerade data flöden både för VPN och ExpressRoute. Det virtuella WAN-nätverket har även stöd för anslutning med ett eko system med CPE-avdelnings enhets partner. CPE-avdelnings enheter har inbyggd automatisering som automatiskt etablerar och ansluter till Azure Virtual WAN. Enheterna finns tillgängliga från ett växande ekosystem av SD-WAN- och VPN-partner. Se [listan över föredragna partner](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Hur skiljer sig det virtuella WAN-nätverket från en virtuell Azure-nätverksgateway?

En virtuell nätverksgateway VPN är begränsad till 30 tunnlar. För anslutningar bör du använda Virtual WAN för ett storskaligt virtuellt privat nätverk. Du kan ansluta upp till 1 000 förgrenings anslutningar per region (virtuell hubb) med en agg regering på 20 Gbit/s per hubb. En anslutning är en aktiv-aktiv-tunnel från den lokala VPN-enheten till den virtuella hubben. Du kan ha en hubb per region, vilket innebär att du kan ansluta fler än 1 000 grenar över hubbar.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Vad är en skalnings enhet för virtuell WAN-Gateway
En skalnings enhet är en enhet som definieras för att välja ett sammanställt data flöde för en gateway i virtuell hubb. 1 skalnings enhet för VPN = 500 Mbit/s. 1 skalnings enhet av ExpressRoute = 2 Gbit/s. Exempel: 10 skalnings enhet för VPN skulle innebära 500 Mbit/s * 10 = 5 Gbit/s

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Vilka enhets leverantörer (virtuella WAN-partner) stöds?

Just nu stöds den helt automatiserade Virtual WAN-upplevelsen av många partner. Mer information finns i [Virtual WAN-partner](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Vilka är automatiseringsstegen för virtuella WAN-partner?

Information om automatiseringssteg för partner finns i avsnittet om [automatisering för virtuella WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Måste jag använda en önskad partnerenhet?

Nej. Du kan använda valfri VPN-kompatibel enhet som följer Azure-kraven för IKEv2/IKEv1 IPsec-stöd.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hur automatiserar Virtual WAN-partner anslutningsmöjligheter med Azure Virtual WAN?

Programvarudefinierade anslutningslösningar hanterar vanligtvis gren-enheter med hjälp av en kontrollant eller ett enhetsetableringscenter. Kontrollanten kan automatisera anslutningar till Azure Virtual WAN med hjälp av Azure API:er. Automation inkluderar överföring av gren information, hämtning av Azure-konfiguration, konfiguration av IPSec-tunnlar i virtuella Azure-nav och automatisk inställning av anslutning från gren enheten till Azure Virtual WAN. När du har hundratals grenar är det enkelt att ansluta med hjälp av virtuella WAN-partners för att skapa, konfigurera och hantera storskalig IPsec-anslutning. Läs mer i informationen om [automatisering för virtuell WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hur fungerar virtuella WAN-enheter som stöder SD-WAN-enheter?

Virtuella WAN-partners automatiserar IPsec-anslutningen till Azure VPN-slutpunkter. Om den virtuella WAN-partnern är en SD-WAN-Provider, är det underförstådd att den SD-WAN-styrenheten hanterar automatisering och IPsec-anslutning till Azure VPN-slutpunkter. Om SD-WAN-enheten kräver sin egen slut punkt i stället för Azure VPN för alla patentskyddade SD-WAN-funktioner, kan du distribuera en slut punkt för SD-WAN i ett Azure VNet och samverka med Azure Virtual WAN.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Ändrar Virtual WAN några befintliga anslutningsfunktioner?

Inga befintliga anslutningsfunktioner i Azure ändras.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Finns nya Resource Manager-resurser tillgängliga för Virtual WAN?
  
Ja, Virtual WAN introducerar ny Resource Manager-resurser. Mer information finns i [Översikt](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hur många VPN-enheter kan ansluta till en enda hubb?

Upp till 1 000 anslutningar stöds per virtuell hubb. Varje anslutning består av fyra länkar och varje länk anslutning stöder två tunnlar som finns i en aktiv-aktiv-konfiguration. Tunnlarna avslutas i ett virtuellt Azure-nav vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan den lokala VPN-enheten ansluta till flera hubbar?

Ja. Trafikflöde, vid början, kommer från den lokala enheten till närmaste Microsoft-nätverksanslutning och sedan till den virtuella hubben.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan jag distribuera och använda virtuell installation av mitt favoritnätverk (i ett virtuellt NVA-nätverk) med Azure Virtual WAN?

Ja, du kan ansluta önskat NVA-VNet (virtuell nätverksinstallation) till Azure Virtual WAN. Anslut först NVA-VNet till hubben med en virtuell hubbnätverksanslutning. Skapa sedan en virtuell hubb-väg med nästa hopp som pekar på den virtuella enheten. Du kan använda flera vägar för den virtuella hubbens routningstabell. Alla ekrar som är anslutna till NVA-VNet måste dessutom vara anslutna till den virtuella hubben för att säkerställa att VNet-ekervägarna sprids till lokala system.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kan jag skapa en virtuell nätverks installation inuti den virtuella hubben?

Det går inte att distribuera en virtuell nätverks installation (NVA) i en virtuell hubb. Du kan dock skapa det i ett eker VNet som är anslutet till den virtuella hubben och aktivera en väg i hubben för att dirigera trafik för VNet-VNet via NVA IP-adress (av NÄTVERKSKORTet).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kan en eker VNet ha en virtuell nätverksgateway?

Nej. VNet VNet kan inte ha en virtuell nätverksgateway om den är ansluten till den virtuella hubben.

### <a name="is-there-support-for-bgp"></a>Stöds BGP?

Ja, BGP stöds. När du skapar en VPN-webbplats kan du ange BGP-parametrar i den. Det innebär att alla anslutningar som har skapats i Azure för platsen aktive ras för BGP. Om du har ett VNet med en NVA, och om det här NVA-VNet har kopplats till en virtuell WAN-hubb, för att säkerställa att vägar från ett NVA VNet annonseras på lämpligt sätt, måste ekrar som är kopplade till NVA VNet inaktivera BGP. Anslut dessutom de här eker-virtuella nätverk till virtuella hubb-VNet för att se till att virtuella ekrar i ekrar sprids till lokala system.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kan jag dirigera trafik med hjälp av UDR i den virtuella hubben?

Ja, du kan dirigera trafik till ett VNet med en väg tabell för virtuell hubb. På så sätt kan du ange vägar för mål-virtuella nätverk i Azure via en speciell IP-adress (vanligt vis av NVA NIC).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Finns det licens- eller prisinformation om Virtual WAN?

Ja. Se sidan med [prissättning](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Hur gör jag för att beräkna priset för en hubb?

* Du betalar för tjänsterna i hubben. Anta till exempel att du har 10 grenar eller lokala enheter som kräver anslutning till Azure Virtual WAN, vilket innebär att ansluta till VPN-slutpunkter i hubben. Här kan du säga att det här är VPN för 1 skalnings enhet = 500 Mbit/s. Detta debiteras på $0.361/tim. Varje anslutning debiteras till $0,05/timme. För 10 anslutningar blir den totala avgiften för tjänst/timme $0,361 + $. 5/timme. Data avgifter för trafik som lämnar Azure gäller.

* Det finns ytterligare hubb avgifter. Se sidan med [prissättning](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Om du hade ExpressRoute-gatewayen på grund av ExpressRoute-kretsar som ansluter till en virtuell hubb betalar du för skalnings enhets priset. Varje skalnings enhet i ER är 2 Gbit/s och varje anslutnings enhet debiteras enligt samma taxa som VPN-anslutnings enheten.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hur registreras nya partner som inte visas i startpartnerlistan?

Alla virtuella WAN-API: er är öppna API. Du kan gå igenom dokumentationen för att utvärdera teknisk genomförbarhet. Om du har någon fråga skickar du ett e-postmeddelande till azurevirtualwan@microsoft.com. En perfekt partner är en partner som har en enhet som kan etableras för IKEv1 eller IKEv2 IPSec-anslutning.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Vad händer om en enhet jag använder inte finns med i listan över virtuella WAN-partner? Kan jag fortfarande använda den för att ansluta till Azure Virtual WAN VPN?

Ja så länge enheten stöder IPsec IKEv1 eller IKEv2. Virtuella WAN-partner automatiserar anslutningen från enheten till Azure VPN-slutpunkter. Detta innebär att automatisera steg som "gren information upload", "IPsec and Configuration" och "anslutning". Eftersom din enhet inte kommer från ett eko system med en virtuell WAN-partner måste du göra den tunga genom att manuellt ta Azure-konfigurationen och uppdatera enheten för att konfigurera IPsec-anslutning.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Är det möjligt att konstruera Azure Virtual WAN med en Resource Manager-mall?

En enkel konfiguration av ett virtuellt WAN med en hubb och en vpnsite kan skapas med en [snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Det virtuella WAN-nätverket är i första hand en REST-eller Portal driven tjänst.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Stöds Global VNet-peering med Azure Virtual WAN? 

Du kan ansluta ett VNet i en annan region än ditt virtuella WAN-nätverk.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kan eker-virtuella nätverk som är anslutna till en virtuell hubb kommunicera med varandra (V2V-överföring)?

Ja. Standard Virtual WAN stöder VNet till transitivt VNET-anslutning via det virtuella WAN-hubb som virtuella nätverk är anslutet till. I den virtuella WAN-terminologin hänvisar vi till dessa sökvägar som "lokal virtuell WAN VNet-överföring" för virtuella nätverk som är ansluten till en virtuell WAN-hubb inom en region och "global virtuell WAN-överföring av virtuella WAN-nätverk" för virtuella nätverk som är ansluten via flera virtuella WAN-hubbar över två eller flera områdena. VNet-transitering har stöd för upp till 3 Gbit/s av data flödet under offentlig för hands version Data flödet kommer att expanderas när global överföring går GA.

Obs! för hands versionen av V2V-förhands granskning kräver för närvarande att en VPN-anslutning som ska distribueras i en virtuell hubb utlöses. Detta VPN-V2V används inte för överförings vägen för. Detta är en känd begränsning och kommer att tas bort vid tidpunkten för V2V GA. Du kan ta bort VPN Gateway i hubbarna när den har startats helt eftersom den inte behövs för V2V-överförings funktioner. 

I vissa fall kan eker-virtuella nätverk också vara direkt peer-kopplad till varandra med hjälp av [Virtual Network-peering](../articles/virtual-network/virtual-network-peering-overview.md) förutom lokal eller global virtuell WAN-överföring. I detta fall prioriteras VNet-peering över den transitiva anslutningen via den virtuella WAN-hubben. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Vad är en förgrenings anslutning till Azure Virtual WAN?

En anslutning från en förgrenings enhet till Azure Virtual WAN stöder upp till fyra länkar. En länk är den fysiska anslutnings länken på gren platsen (till exempel: till, Verizon osv.). Varje länk anslutning består av två aktiva/aktiva IPsec-tunnlar.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Tillåts gren-till-gren-anslutningar i Virtual WAN?

Ja, det finns stöd för gren-till-gren-anslutningar i Virtual WAN för VPN och VPN till ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Passerar gren-till-gren-trafik via det virtuella Azure-nätverket?

Ja.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Kräver Virtual WAN ExpressRoute från varje plats?

Nej, Virtual WAN kräver inte ExpressRoute från varje plats. Den använder standard-IPsec plats-till-plats-anslutning via Internet länkar från enheten till en Azure Virtual WAN-hubb. Dina platser kan anslutas till ett providernätverk med hjälp av en ExpressRoute-krets. För platser som är anslutna med ExpressRoute i en virtuell hubb kan platser ha gren till gren trafik flöde mellan VPN och ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Finns det någon gräns för nätverksdataflöde vid användning av Azure Virtual WAN?

Antalet grenar är begränsat till 1000 anslutningar per hubb/region och totalt 20 Gbit/s i hubben. Du kan ha 1 hubb per region.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Hur många VPN-anslutningar stöder en virtuell WAN-hubb?

En virtuell Azure-hubb i Azure har stöd för upp till 1 000 S2S-anslutningar, 10 000 P2S-anslutningar och 4 ExpressRoute-anslutningar samtidigt.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Vad är det totala VPN-dataflödet för en VPN-tunnel och en anslutning?

Det totala VPN-dataflödet för en hubb är upp till 20 Gbit/s baserat på vald skalnings enhet. Data flödet delas av alla befintliga anslutningar. Varje tunnel i en anslutning har stöd för upp till 1 Gbit/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Jag ser inte inställningen 20 Gbit/s för den virtuella hubben i portalen. Hur gör jag för att konfigurera?

Navigera till VPN-gatewayen i ett nav på portalen och klicka på skalnings enheten för att ändra den till lämplig inställning.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Tillåter Virtual WAN att den lokala enheten använder flera olika Internet leverantörer parallellt, eller är alltid en enda VPN-tunnel?

En anslutning som kommer till ett virtuellt WAN-nätverk är alltid en aktiv-aktiv tunnel (för återhämtning inom samma hubb/region) med en länk som är tillgänglig på grenen. Länken kan vara en ISP-länk på den lokala grenen. Virtuella WAN-VPNSite ger möjlighet att lägga till länk information till platsen. Om du har flera Internet leverantörer i grenen och var och en av Internet leverantörerna tillhandahöll en länk, kan den informationen konfigureras i VPN-platsens information i Azure. Att hantera redundans över Internet leverantörer i grenen är dock helt en förgrenings driven väg åtgärd.

### <a name="what-is-global-transit-architecture"></a>Vad är global transiterings arkitektur?

Information om global överförings arkitektur finns i [Global överföring av nätverks arkitektur och virtuellt WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hur dirigeras trafiken i Azures stamnät?

Trafiken följer mönstret: Branch enhet-> ISP-> Microsoft Network Edge-> Microsoft DC (hubb VNet)-> Microsoft Network Edge-> ISP-> gren enhet

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Vad behöver du på varje plats i den här modellen? Bara en Internetanslutning?

Ja. En Internet anslutning och en fysisk enhet som stöder IPsec, helst från våra integrerade [virtuella WAN-partner](../articles/virtual-wan/virtual-wan-locations-partners.md). Du kan också manuellt hantera konfigurationen och anslutningen till Azure från den önskade enheten.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hur gör jag för att aktivera standard väg (0.0.0.0/0) i en anslutning (VPN, ExpressRoute eller Virtual Network):

En virtuell hubb kan sprida en inlärd standard väg till en VPN-/ExpressRoute-anslutning för virtuella nätverk/plats-till-plats om flaggan är aktive rad på anslutningen. Den här flaggan visas när användaren redigerar en virtuell nätverks anslutning, en VPN-anslutning eller en ExpressRoute-anslutning. Som standard inaktive ras den här flaggan när en plats eller en ExpressRoute-krets är ansluten till en hubb. Den aktive ras som standard när en virtuell nätverks anslutning läggs till för att ansluta ett VNet till en virtuell hubb. Standard vägen kommer inte från den virtuella WAN-hubben. standard vägen sprids om den redan har belärts av den virtuella WAN-hubben som ett resultat av distributionen av en brand vägg i hubben, eller om en annan ansluten plats har Tvingad tunnel trafik aktive rad.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Vad är skillnaderna mellan de virtuella WAN-typerna (Basic och standard)?

Med WAN-typen Basic kan du skapa en grundläggande hubb (SKU = Basic). Med en standard-WAN-typ kan du skapa standard hubb (SKU = standard). Grundläggande NAV är begränsade till plats-till-plats-VPN-funktioner. Med standard nav kan du ha ExpressRoute, User VPN (P2S), en komplett näthubb och VNet-till-VNet-överföring via hubbarna. Du betalar en bas avgift på $0,25/timme för standard nav och en data bearbetnings avgift för överföring via hubbarna under VNet-till-VNet-anslutning, samt data bearbetning för hubb till hubb trafik. Mer information finns i [grundläggande och standard-virtuella WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Information om priser finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-wan/) .
