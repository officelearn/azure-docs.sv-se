---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cb2302637efb16fc31bd420bf8c4ead19d7f598d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384966"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Behöver användaren ha nav och talat med SD-WAN/VPN-enheter för att kunna använda Azure Virtual WAN?

Virtual WAN innehåller många funktioner som är inbyggda i en enda glasruta, till exempel VPN-anslutning på plats/plats till plats, användar-/P2S-anslutning, ExpressRoute-anslutning, virtual network-anslutning, VPN ExpressRoute Interconnectivity, VNET to VNET transitiv anslutning, centraliserad routning, Azure-brandvägg och brandväggshanterare, övervakning, ExpressRoute-kryptering och många andra funktioner. Du behöver inte ha alla dessa användningsfall för att börja använda Virtual WAN. Du kan helt enkelt komma igång med bara ett användningsfall. Virtual WAN-arkitekturen är en hubb- och ekerarkitektur med inbyggd skala och prestanda där grenar (VPN/SD-WAN-enheter), användare (Azure VPN-klienter, openVPN- eller IKEv2-klienter), ExpressRoute-kretsar, virtuella nätverk fungerar som ekrar till virtuella hubbar. Alla hubbar är anslutna i fullt nät i ett virtuellt standard WAN-minne, vilket gör det enkelt för användaren att använda Microsofts stamnät för alla (alla ekrar) anslutning. För hubb- och talade med SD-WAN/VPN-enheter kan användare antingen manuellt konfigurera den i Azure Virtual WAN-portalen eller använda Den virtuella WAN-partner-CPE (SD-WAN/VPN) för att konfigurera anslutning till Azure. Virtuella WAN-partner tillhandahåller automatisering för anslutning som är möjligheten att exportera enhetsinformationen till Azure, hämta Azure-konfigurationen och upprätta anslutning till Azure Virtual WAN-hubben. För punkt-till-plats-/användar-VPN-anslutning stöder vi [Azure VPN-klient,](https://go.microsoft.com/fwlink/?linkid=2117554)OpenVPN- eller IKEv2-klient. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Vilken klient stöder Azure Virtual WAN User VPN (Point-to-site)?

Virtual WAN stöder [Azure VPN-klient,](https://go.microsoft.com/fwlink/?linkid=2117554)OpenVPN-klient eller alla IKEv2-klienter. Azure AD-autentisering stöds med Azure VPN Client.Ett minimum av Windows 10-klient-OS version 17763.0 eller senare krävs.  OpenVPN-klienter kan stödja certifikatbaserad autentisering. När cert-baserad auth har valts på gatewayen visas ovpn-filen som ska hämtas till enheten. Både certifikat och RADIUS-auth stöds med IKEv2. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>För User VPN (Point-to-site)- Varför är P2S-klientpoolen uppdelad i två vägar?

Varje gateway har två instanser, uppdelningen sker så att varje gateway-instans självständigt kan allokera klient-IP-adresser för anslutna klienter och trafik från det virtuella nätverket dirigeras tillbaka till rätt gateway-instans för att undvika instanshopp mellan gateway.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hur lägger jag till DNS-servrar för P2S-klienter?

Det finns två alternativ för att lägga till DNS-servrar för P2S-klienterna.

1. Öppna en supportbiljett med Microsoft och be dem lägga till dina DNS-servrar i navet
2. Eller, om du använder Azure VPN-klienten för Windows 10, kan du ändra den nedladdade profil XML-filen och lägga till ** \<dnsservers \<>dnsserver> \</dnsserver>\</dnsservers>** taggar innan du importerar den.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>För User VPN (Point-to-site)- hur många klienter stöds?

Varje Användare VPN P2S-gateway har två instanser och varje instans stöder upp till vissa användare när skalningsenheten ändras. Skalenhet 1-3 stöder 500 anslutningar, scale unit 4-6 stöder 1000 anslutningar, scale unit 7-10 stöder 5000 anslutningar och skalenhet 11+ stöder upp till 10 000 anslutningar. Låt oss till exempel säga att användaren väljer 1 skalenhet. Varje skalningsenhet skulle innebära en aktiv aktiv gateway som distribueras och var och en av instanserna (i det här fallet 2) skulle stödja upp till 500 anslutningar. Eftersom du kan få 500 anslutningar * 2 per gateway betyder det inte att du planerar för 1000 istället för 500 för den här skalenheten eftersom instanser kan behöva servas under vilken anslutningen för de extra 500 kan avbrytas om du överskrider det rekommenderade anslutningsantalet.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Vad är skillnaden mellan en virtuell Azure-nätverksgateway (VPN Gateway) och en Azure Virtual WAN VPN-gateway?

Virtual WAN tillhandahåller storskalig plats-till-plats-anslutning och byggs för dataflöde, skalbarhet och användarvänlighet. När du ansluter en plats till en Virtuell WAN VPN-gateway skiljer den sig från en vanlig virtuell nätverksgateway som använder en gatewaytyp "VPN". På samma sätt, när du ansluter en ExpressRoute-krets till en virtuell WAN-hubb, använder den en annan resurs för ExpressRoute-gatewayen än den vanliga virtuella nätverksgatewayen som använder gatewaytypen "ExpressRoute". Virtual WAN stöder upp till 20 Gbit/s aggregerat dataflöde både för VPN och ExpressRoute. Virtual WAN har också automatisering för anslutning med ett ekosystem av CPE-grenenhetspartner. CPE-grenenheter har inbyggd automatisering som automatiskt etablerar och ansluter till Azure Virtual WAN. Enheterna finns tillgängliga från ett växande ekosystem av SD-WAN- och VPN-partner. Se [listan över föredragna partner](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Hur skiljer sig Virtual WAN från en virtuell Azure-nätverksgateway?

En VPN för virtuell nätverksgateway är begränsad till 30 tunnlar. För anslutningar bör du använda Virtual WAN för ett storskaligt virtuellt privat nätverk. Du kan ansluta upp till 1 000 filialanslutningar per region (virtuellt nav) med sammanlagt 20 Gbit/s per hubb. En anslutning är en aktiv-aktiv-tunnel från den lokala VPN-enheten till den virtuella hubben. Du kan ha ett nav per region, vilket innebär att du kan ansluta mer än 1 000 grenar över hubbar.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Vad är en virtuell WAN Gateway-skalningsenhet
En skalningsenhet är en enhet som definierats för att välja ett aggregerat dataflöde för en gateway i virtuellt nav. 1 skalenhet vpn = 500 Mbit/s . 1 skalenhet expressroute = 2 Gbit/s. Exempel: 10 skalenliga enheten för VPN skulle innebära 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Vilka enhetsleverantörer (Virtual WAN-partner) stöds?

Just nu stöds den helt automatiserade Virtual WAN-upplevelsen av många partner. Mer information finns i [Virtual WAN-partner](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Vilka är automatiseringsstegen för virtuella WAN-partner?

Information om automatiseringssteg för partner finns i avsnittet om [automatisering för virtuella WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Måste jag använda en önskad partnerenhet?

Nej. Du kan använda valfri VPN-kompatibel enhet som följer Azure-kraven för IKEv2/IKEv1 IPsec-stöd.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hur automatiserar Virtual WAN-partner anslutningsmöjligheter med Azure Virtual WAN?

Programvarudefinierade anslutningslösningar hanterar vanligtvis gren-enheter med hjälp av en kontrollant eller ett enhetsetableringscenter. Kontrollanten kan automatisera anslutningar till Azure Virtual WAN med hjälp av Azure API:er. Automatiseringen omfattar överföring av filialinformation, nedladdning av Azure-konfigurationen, konfigurera IPSec-tunnlar i Virtuella Azure-hubbar och automatiskt konfigurera anslutningsformulär för filialenheten till Azure Virtual WAN. När du har hundratals grenar är det enkelt att ansluta med Virtual WAN CPE Partners eftersom introduktionsupplevelsen tar bort behovet av att konfigurera, konfigurera och hantera storskalig IPsec-anslutning. Mer information finns i [Virtual WAN-partnerautomatisering](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hur stöder Virtual WAN SD-WAN-enheter?

Virtuella WAN-partner automatiserar IPsec-anslutning till Azure VPN-slutpunkter. Om Virtual WAN-partnern är en SD-WAN-provider är det underförstått att SD-WAN-styrenheten hanterar automatisering och IPsec-anslutning till Azure VPN-slutpunkter. Om SD-WAN-enheten kräver en egen slutpunkt i stället för Azure VPN för alla egna SD-WAN-funktioner kan du distribuera SD-WAN-slutpunkten i ett Azure VNet och samexistera med Azure Virtual WAN.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Ändrar Virtual WAN några befintliga anslutningsfunktioner?

Inga befintliga anslutningsfunktioner i Azure ändras.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Finns nya Resource Manager-resurser tillgängliga för Virtual WAN?
  
Ja, Virtual WAN introducerar ny Resource Manager-resurser. Mer information finns i [Översikt](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hur många VPN-enheter kan ansluta till en enda hubb?

Upp till 1 000 anslutningar stöds per virtuellt nav. Varje anslutning består av fyra länkar och varje länkanslutning stöder två tunnlar som är i en aktiv-aktiv konfiguration. Tunnlarna avslutas i en virtuell Azure-hubbportway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan den lokala VPN-enheten ansluta till flera hubbar?

Ja. Trafikflödet, när det påbörjas, är från den lokala enheten till närmaste Microsoft-nätverkskant och sedan till den virtuella hubben.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan jag distribuera och använda virtuell installation av mitt favoritnätverk (i ett virtuellt NVA-nätverk) med Azure Virtual WAN?

Ja, du kan ansluta önskat NVA-VNet (virtuell nätverksinstallation) till Azure Virtual WAN. Anslut först NVA-VNet till hubben med en virtuell hubbnätverksanslutning. Skapa sedan en virtuell hubbväg med nästa hopp som pekar på den virtuella installationen. Du kan använda flera vägar till den virtuella hubbvägstabellen. Alla ekrar som är anslutna till NVA-VNet måste dessutom vara anslutna till den virtuella hubben för att säkerställa att VNet-ekervägarna sprids till lokala system.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kan jag skapa en virtuell nätverksinstallation i den virtuella hubben?

Det går inte att distribuera en virtuell nätverksinstallation (NVA) i ett virtuellt nav. Du kan dock skapa den i ett eker-virtuellt nätverk som är anslutet till det virtuella navet och aktivera en väg i navet för att dirigera trafik för mål-VNet via NVA IP-adressen (för nätverkskortet).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kan ett eker-virtuellt nätverk ha en virtuell nätverksgateway?

Nej. Det eker-virtuella nätverket kan inte ha en virtuell nätverksgateway om den är ansluten till den virtuella hubben.

### <a name="is-there-support-for-bgp"></a>Stöds BGP?

Ja, BGP stöds. När du skapar en VPN-plats kan du ange BGP-parametrarna i den. Detta innebär att alla anslutningar som skapas i Azure för den platsen kommer att aktiveras för BGP. Dessutom, om du hade ett virtuellt nätverk med en NVA, och om detta NVA VNet var anslutet till en virtuell WAN-hubb, för att säkerställa att vägar från ett NVA-nätverk annonseras på rätt sätt, måste ekrar som är kopplade till NVA VNet inaktivera BGP. Dessutom ansluter du dessa ekrar virtuella nätverk till det virtuella navet virtuella nätverk för att säkerställa att ekriska virtuella nätverksvägar sprids till lokala system.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kan jag dirigera trafik med hjälp av UDR i den virtuella hubben?

Ja, du kan dirigera trafik till ett virtuellt nätverk med hjälp av en virtuell hubbvägstabell. På så sätt kan du ange vägar för virtuella målnätverk i Azure via en specifik IP-adress (vanligtvis av NVA NIC).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Finns det licens- eller prisinformation om Virtual WAN?

Ja. Se sidan med [prissättning](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Hur beräknar jag priset för ett nav?

* Du skulle betala för tjänsterna i navet. Låt oss till exempel säga att du har 10 grenar eller lokala enheter som kräver att ansluta till Azure Virtual WAN skulle innebära att ansluta till VPN-slutpunkter i navet. Låt oss säga att detta är VPN av 1 skala enhet = 500 Mbps, detta debiteras på $ 0.361/hr. Varje anslutning debiteras på $ 0,05 / timme. För 10 anslutningar, skulle den totala avgiften för service / hr vara $ 0,361 + $.5/hr. Dataavgifter för trafik som lämnar Azure gäller.

* Det tillkommer ytterligare navavgift. Se sidan med [prissättning](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Om du hade ExpressRoute gateway på grund av ExpressRoute-kretsar som ansluter till ett virtuellt nav, skulle du betala för skalenhetspriset. Varje skalningsenhet i ER är 2 Gbit/s och varje anslutningsenhet debiteras i samma takt som VPN-anslutningsenheten.

* Om du hade Spoke VNETs ansluten till navet, peering avgifter på Spoke VNETs fortfarande gäller. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hur registreras nya partner som inte visas i startpartnerlistan?

Alla virtuella WAN-API:er är öppna API: er. Du kan gå igenom dokumentationen för att bedöma teknisk genomförbarhet. Om du har några frågor, azurevirtualwan@microsoft.comskicka ett mail till . En perfekt partner är en partner som har en enhet som kan etableras för IKEv1 eller IKEv2 IPSec-anslutning.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Vad händer om en enhet som jag använder inte finns i virtual WAN-partnerlistan? Kan jag fortfarande använda den för att ansluta till Azure Virtual WAN VPN?

Ja så länge enheten stöder IPsec IKEv1 eller IKEv2. Virtuella WAN-partner automatiserar anslutningen från enheten till Azure VPN-slutpunkter. Detta innebär att du automatiserar steg som "överföring av filialinformation", "IPsec och konfiguration" och "anslutning". Eftersom enheten inte kommer från ett virtuellt WAN-partnerekosystem måste du göra det tunga lyftet med att manuellt ta Azure-konfigurationen och uppdatera enheten för att konfigurera IPsec-anslutning.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Är det möjligt att konstruera Azure Virtual WAN med en Resource Manager-mall?

En enkel konfiguration av en virtuell WAN med en hubb och en vpnsite kan skapas med hjälp av en [snabbstartsmall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN är främst en REST- eller portaldriven tjänst.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Stöds Global VNet-peering med Azure Virtual WAN? 

Du kan ansluta ett virtuellt nätverk i en annan region än ditt virtuella WAN.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kan eker virtuella nätverk som är anslutna till ett virtuellt nav kommunicera med varandra (V2V Transit)?

Ja. Virtual WAN stöder Vnet till Vnet transitiv anslutning via virtual WAN-hubben som virtuella nätverken är anslutna till. I Virtual WAN-terminologin refererar vi till dessa sökvägar som "lokal virtuell WAN-VNet-transit" för virtuella nätverk som är anslutna till en Virtuell Wan Hub inom en enda region och "global Virtual WAN VNet-transit" för virtuella nätverk som är anslutna via flera virtuella WAN-hubbar i två eller flera regioner. VNet-transit stöder upp till 3 Gbit/s dataflöde under offentlig förhandsversion. Dataflödet kommer att utökas när den globala transiten går GA.

För närvarande kräver V2V-överföringsförhandsgranskningen att en VPN GW distribueras i en virtuell hubb för att utlösa routningselementen som ska startas. Den här VPN GW används inte för V2V-transitsökvägen. Detta är en känd begränsning och kommer att tas bort vid tidpunkten för V2V GA. Du kan ta bort VPN Gateway i naven(erna) när den har startats helt eftersom den inte behövs för V2V-transitfunktioner. 

För vissa scenarier kan ekrarna virtuella nätverk också vara direkt peered med varandra med hjälp av [Virtual Network Peering](../articles/virtual-network/virtual-network-peering-overview.md) utöver lokala eller globala Virtual WAN VNet transitering. I det här fallet har Vnet Peering företräde framför den transitiva anslutningen via virtual WAN-hubben. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Vad är en filialanslutning till Azure Virtual WAN?

En anslutning från en filialenhet till Azure Virtual WAN stöder upp till fyra länkar. En länk är den fysiska anslutningslänken på filialplatsen (till exempel: ATT, Verizon etc.). Varje länkanslutning består av två aktiva/aktiva IPsec-tunnlar.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Tillåts gren-till-gren-anslutningar i Virtual WAN?

Ja, det finns stöd för gren-till-gren-anslutningar i Virtual WAN för VPN och VPN till ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Går trafik från mellan mellangren till gren genom Azure Virtual WAN?

Ja.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Kräver Virtual WAN ExpressRoute från varje plats?

Nej, Virtual WAN kräver inte ExpressRoute från varje plats. Den använder standard IPsec-anslutning från plats till plats via internetlänkar från enheten till en Azure Virtual WAN-hubb. Dina platser kan anslutas till ett providernätverk med hjälp av en ExpressRoute-krets. För webbplatser som är anslutna med ExpressRoute i ett virtuellt nav kan platser ha gren för att förgrena trafikflödet mellan VPN och ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Finns det någon gräns för nätverksdataflöde vid användning av Azure Virtual WAN?

Antalet grenar är begränsat till 1 000 anslutningar per hubb/region och totalt 20 Gbit/s i navet. Du kan ha 1 hubb per region.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Hur många VPN-anslutningar stöder en Virtual WAN-hubb?

En Azure Virtual WAN-hubb kan stödja upp till 1 000 S2S-anslutningar, 10 000 P2S-anslutningar och 4 ExpressRoute-anslutningar samtidigt.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Vad är det totala VPN-dataflödet för en VPN-tunnel och en anslutning?

Det totala VPN-dataflödet för ett nav är upp till 20 Gbit/s baserat på den valda skalenheten. Dataflöde delas av alla befintliga anslutningar. Varje tunnel i en anslutning kan ha stöd för upp till 1 Gbit/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Jag ser inte inställningen 20 Gbit/s för det virtuella navet i portalen. Hur konfigurerar jag det?

Navigera till VPN-gatewayen inuti ett nav på portalen och klicka på skalenheten för att ändra den till rätt inställning.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Tillåter Virtual WAN den lokala enheten att använda flera Internet-adresser parallellt, eller är det alltid en enda VPN-tunnel?

En anslutning som kommer in i en virtuell WAN VPN är alltid en aktiv-aktiv tunnel (för återhämtning inom samma nav/region) med hjälp av en länk som är tillgänglig på grenen. Den här länken kan vara en IsP-länk på den lokala filialen. Virtual WAN "VPNSite" ger möjlighet att lägga till länkinformation till webbplatsen. Om du har flera Internet-adresser på grenen och var och en av Internetleverantörerna tillhandahöll en länk kan den informationen konfigureras i VPN-platsinformationen i Azure. Att hantera redundans över Internet-adresser på grenen är dock helt en grencentrerad routning.

### <a name="what-is-global-transit-architecture"></a>Vad är global transitarkitektur?

Information om global transitarkitektur finns i [Global transitnätverksarkitektur och Virtuellt WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hur dirigeras trafiken i Azures stamnät?

Trafiken följer mönstret: grenenhet ->ISP->Microsoft-nätverk edge->Microsoft DC (hub VNet)->Microsoft-nätverkskant->ISP->grenenhet

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Vad behöver du på varje plats i den här modellen? Bara en Internetanslutning?

Ja. En internetuppkoppling och fysisk enhet som stöder IPsec, helst från våra integrerade [Virtual WAN-partners.](../articles/virtual-wan/virtual-wan-locations-partners.md) Du kan också manuellt hantera konfigurationen och anslutningen till Azure från önskad enhet.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hur aktiverar jag standardväg (0.0.0.0/0) i en anslutning (VPN, ExpressRoute eller Virtual Network):

En virtuell hubb kan sprida en inlärd standardväg till en virtuell VPN/ExpressRoute-anslutning för nätverk/plats till plats om flaggan är aktiverad på anslutningen. Den här flaggan visas när användaren redigerar en virtuell nätverksanslutning, en VPN-anslutning eller en ExpressRoute-anslutning. Som standard inaktiveras den här flaggan när en plats eller en ExpressRoute-krets är ansluten till ett nav. Den är aktiverad som standard när en virtuell nätverksanslutning läggs till för att ansluta ett virtuellt nätverk till ett virtuellt nav. Standardvägen kommer inte från hubben Virtuellt WAN. Standardvägen sprids om den redan har lärts av den virtuella WAN-hubben som ett resultat av att en brandvägg distribueras i navet eller om en annan ansluten plats har aktiverat tunneldrivning.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Vilka är skillnaderna mellan de virtuella WAN-typerna (Basic och Standard)?

Med TYPEN "Grundläggande" WAN kan du skapa en grundläggande hubb (SKU = Basic). Med en WAN-typ av standard kan du skapa standardhubb (SKU = Standard). Grundläggande hubbar är begränsade till VPN-funktioner från plats till plats. Med standardhubbar kan du låta ExpressRoute, User VPN (P2S), full mesh hub och VNet-to-VNet transit genom naven. Du betalar en basavgift på 0,25 USD/timme för standardhubbar och en databehandlingsavgift för överföring via naven under VNet-till-VNet-anslutning samt databearbetning för hubb-till navtrafik. Mer information finns i [Grundläggande och standard virtuella WANs](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Pris för prissättning finns på sidan [Prissättning.](https://azure.microsoft.com/pricing/details/virtual-wan/)
