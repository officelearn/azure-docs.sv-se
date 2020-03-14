---
title: Vanliga frågor och svar – Azure ExpressRoute | Microsoft Docs
description: ExpressRoute vanliga frågor och svar innehåller information om stöd för Azure-tjänster, kostnad, Data och anslutningar, serviceavtal, leverantörer och platser, bandbredd och ytterligare teknisk information.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264926"
---
# <a name="expressroute-faq"></a>Vanliga frågor och svar för ExpressRoute

## <a name="what-is-expressroute"></a>Vad är ExpressRoute?

ExpressRoute är en Azure-tjänst som låter dig skapa privata anslutningar mellan Microsofts datacenter och infrastruktur som finns lokalt eller i en delade miljö. ExpressRoute-anslutningar inte går via det offentliga Internet och är högre säkerhet, tillförlitlighet och hastighet med kortare svarstider än vanliga anslutningar via Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Vilka är fördelarna med att använda ExpressRoute och privata nätverksanslutningar?

ExpressRoute-anslutningar går inte via offentligt Internet. De ger högre säkerhet, tillförlitlighet och hastighet, med lägre och konsekvent svarstider än vanliga anslutningar via Internet. I vissa fall kan använda ExpressRoute-anslutningar för att överföra data mellan lokala enheter och Azure kan ge betydande kostnadsfördelar.

### <a name="where-is-the-service-available"></a>Där är tjänsten?

Se den här sidan för tjänst lokalisering och tillgänglighet: [ExpressRoute partners och platser](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hur kan jag använda ExpressRoute för att ansluta till Microsoft om jag inte har partnerskap med en ExpressRoute-operatör-partner?

Du kan välja ett regionalt flygbolag och hamnar Ethernet-anslutningar till en av stöds exchange providerplatser. Du kan sedan peerkopplas med Microsoft på providerplatsen. Kontrol lera det sista avsnittet av [ExpressRoute-partners och platser](expressroute-locations.md) för att se om din tjänst leverantör finns på någon av Exchange-platserna. Du kan sedan beställa en ExpressRoute-krets via tjänstleverantör för att ansluta till Azure.

### <a name="how-much-does-expressroute-cost"></a>Hur mycket kostar ExpressRoute?

Se [pris](https://azure.microsoft.com/pricing/details/expressroute/) information för pris information.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Om jag betalar för en ExpressRoute-krets för en viss bandbredd kan jag köpa från min nätverkstjänstleverantör VPN-anslutningen måste vara samma hastighet?

Nej. Du kan köpa en VPN-anslutning av alla från din tjänstprovider. Anslutningen till Azure är dock begränsad till ExpressRoute-kretsens bandbredd som du köper.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Om jag betala för en ExpressRoute-krets för en viss bandbredd finns möjlighet att utöka upp till högre hastigheter vid behov?

Ja. ExpressRoute-kretsar har konfigurerats så att du kan tillhandahålla upp till två gånger den Bandbreddsgräns du skaffat utan ytterligare kostnad. Kontrollera med din tjänstleverantör för att se om de stöder denna funktion. Detta är inte en längre tids period och är inte garanterad.  Om trafiken flödar genom en ExpressRoute-Gateway, är bandbredden för SKU: n fast och inte burstbar.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kan jag använda samma privat nätverksanslutning med virtuellt nätverk och andra Azure-tjänster samtidigt?

Ja. En ExpressRoute-krets när har konfigurerat, kan du komma åt tjänster inom ett virtuellt nätverk och andra Azure-tjänster samtidigt. Du kan ansluta till virtuella nätverk över den privata peering-sökvägen och till andra tjänster över med Microsofts peeringsökväg.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Hur annonseras virtuella nätverk på ExpressRoute privata peering?

ExpressRoute-gatewayen annonserar *adress utrymmet* för det virtuella Azure-nätverket. du kan inte inkludera/exkludera på under näts nivån. Det är alltid det virtuella nätverkets adress utrymme som annonseras. Om VNet-peering används och peer-allokerat VNet har aktiverat "Använd fjärran sluten Gateway", annonseras även adress utrymmet för peer-allokerat VNet.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Hur många prefix kan annonseras från ett VNet till lokalt på ExpressRoute privata peering?

Det finns högst 200 prefix som annonseras på en enskild ExpressRoute-anslutning eller via VNet-peering med hjälp av Gateway-överföring. Om du till exempel har 199 adress utrymmen på ett enskilt VNet som är anslutet till en ExpressRoute-krets, annonseras alla 199 av dessa prefix till lokalt. Alternativt, om du har ett VNet aktiverat för att tillåta Gateway-överföring med 1 adress utrymme och 150 ekrar som är aktiverade med alternativet "Tillåt fjärran sluten Gateway", kommer det virtuella nätverk som distribueras med gatewayen att annonsera 151-prefix till lokalt.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Vad händer om jag överskrider prefixlängden för en ExpressRoute-anslutning?

Anslutningen mellan ExpressRoute-kretsen och gatewayen (och peer-virtuella nätverk som använder Gateway-överföring, om tillämpligt) går nedåt. Den återupprättas när prefixlängden inte längre överskrids.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Kan jag filtrera vägar som kommer från mitt lokala nätverk?

Det enda sättet att filtrera/inkludera vägar finns på den lokala Edge-routern. Användardefinierade vägar kan läggas till i VNet för att påverka viss routning, men detta kommer att vara statiskt och inte en del av BGP-annonsen.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Erbjuder ett serviceavtal (SLA) i ExpressRoute?

Mer information finns på sidan om [service avtal för ExpressRoute](https://azure.microsoft.com/support/legal/sla/) .

## <a name="supported-services"></a>Tjänster som stöds

ExpressRoute stöder [tre routningsdomäner](expressroute-circuit-peerings.md) för olika typer av tjänster: privat peering, Microsoft-peering och offentlig peering (inaktuell).

### <a name="private-peering"></a>Privat peering

**Tillåtna**

* Virtuella nätverk, inklusive alla virtuella datorer och molntjänster

### <a name="microsoft-peering"></a>Microsoft-peering

Om din ExpressRoute-krets är aktive rad för Microsoft-peering för Azure kan du komma åt de [offentliga IP-adressintervall](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) som används i Azure över kretsen. Azure Microsoft-peering ger till gång till tjänster som finns i Azure (med geo-begränsningar beroende på din krets-SKU). Om du vill verifiera tillgänglighet för en speciell tjänst kan du kontrol lera dokumentationen för tjänsten för att se om det finns ett reserverat intervall publicerat för tjänsten. Leta sedan upp IP-intervallen för mål tjänsten och jämför med intervallen som anges i [Azure IP-intervallen och service märken – offentlig moln-XML-fil](https://www.microsoft.com/download/details.aspx?id=56519). Du kan också öppna ett support ärende för tjänsten i fråga för att klargöra.

**Tillåtna**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI – tillgängligt via en regional Azure-community, finns [här](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) för hur du kan ta reda på den region där din Power BI-klient finns.
* Azure Active Directory
* [Virtuellt Windows-skrivbord](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azures globala tjänster community)
* Offentliga Azure-IP-adresser för IaaS (Virtual Machines, Virtual Network gatewayer, belastnings utjämning osv.)  
* De flesta andra Azure-tjänster stöds också. Kontrollera direkt med den tjänst som du vill använda för att verifiera support.

**Stöds inte:**

* CDN
* Azure Front Door
* Multi-Factor Authentication-Server (bakåtkompatibelt)
* Traffic Manager

### <a name="public-peering"></a>Offentlig peering

Offentlig peering har inaktiverats på nya ExpressRoute-kretsar. Azure-tjänster är nu tillgängliga på Microsoft-peering. Om du har skapat en krets som har skapats innan offentlig peering är inaktuell, kan du välja att använda Microsoft peering eller offentlig peering, beroende på vilka tjänster du vill ha.

Mer information och konfigurations steg för offentlig peering finns i [ExpressRoute offentlig peering](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Varför ser jag status "annonserade offentliga prefix" som "verifiering krävs" när jag konfigurerar Microsoft-peering?

Microsoft kontrollerar om de angivna annonserade offentliga prefixen och peer-ASN (eller kund-ASN) är tilldelade till dig i Internet-routningstabellen. Om du hämtar de offentliga prefixen från en annan entitet och om tilldelningen inte är registrerad i routningstabellen, slutförs inte den automatiska verifieringen och kräver manuell verifiering. Om den automatiska valideringen Miss lyckas visas meddelandet "validering krävs".

Om du ser meddelandet "validering krävs" samlar du in dokumenten som visar att de offentliga prefixen har tilldelats till din organisation av den entitet som anges som ägare till prefixen i routningstabellen och skickar dessa dokument för manuell verifiering med öppna ett support ärende som visas nedan.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Stöds Dynamics 365 på ExpressRoute?

Dynamics 365-och Common Data Service-miljöer (CD) är värdbaserade på Azure och därför drar kunderna nytta av det underliggande ExpressRoute-stödet för Azure-resurser. Du kan ansluta till dess tjänst slut punkter om ditt router-filter innehåller Azure-regionerna som dina Dynamics 365/CD-miljöer finns i.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) krävs **inte** för Dynamics 365-anslutning via Azure ExpressRoute.

## <a name="data-and-connections"></a>Data och anslutningar

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Finns det någon gräns för mängden data som kan överföra med ExpressRoute?

Vi anger inte en gräns för mängden data som överförs. Se [pris information](https://azure.microsoft.com/pricing/details/expressroute/) för information om bandbredds taxa.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Vilka anslutningshastigheter stöds av ExpressRoute?

Bandbredd erbjudanden som stöds:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Vilka leverantörer som är tillgängliga?

Se [ExpressRoute-partner och platser](expressroute-locations.md) för att få en lista över tjänst leverantörer och platser.

## <a name="technical-details"></a>Teknisk information

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Vilka är de tekniska kraven för att ansluta min lokala plats till Azure?

Mer information finns på [sidan för ExpressRoute](expressroute-prerequisites.md) -krav.

### <a name="are-connections-to-expressroute-redundant"></a>Är anslutningar till ExpressRoute redundant?

Ja. Varje ExpressRoute-krets har ett redundant par med mellan anslutningar som konfigurerats för att tillhandahålla hög tillgänglighet.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Kommer jag att förlora anslutningen om någon av länkarna ExpressRoute misslyckas?

Du kommer inte att förlora anslutningen om en av flera anslutningar misslyckas. En redundant anslutning är tillgänglig för att stödja belastningen på nätverket och ge hög tillgänglighet för ExpressRoute-kretsen. Du kan också skapa en krets i en annan peering plats att uppnå återhämtning krets på servernivå.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hur gör jag för att implementera redundans på privat peering?

Flera ExpressRoute-kretsar från olika peering-platser kan anslutas till samma virtuella nätverk för att tillhandahålla hög tillgänglighet om en enda krets blir otillgänglig. Du kan sedan [tilldela högre vikter](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) till den lokala anslutningen för att kunna prioritera en speciell krets. Vi rekommenderar starkt att kunderna installerar minst två ExpressRoute-kretsar för att undvika enskilda felpunkter. 

Se [här](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) för att utforma för hög tillgänglighet [och för att utforma](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) för haveri beredskap.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hur implementerar jag redundans på Microsoft-peering?

Vi rekommenderar starkt att du använder Microsoft-peering för att få åtkomst till Azures offentliga tjänster som Azure Storage eller Azure SQL, samt kunder som använder Microsoft-peering för Office 365 som de implementerar flera kretsar i olika peering platser för att undvika enskilda felpunkter. Kunder kan antingen annonsera samma prefix på båda kretsarna och använda [som sökväg förväntat](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) eller annonsera olika prefix för att fastställa sökväg lokalt.

Se [här](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) för att utforma för hög tillgänglighet.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hur jag för att säkerställa hög tillgänglighet i ett virtuellt nätverk som är anslutet till ExpressRoute?

Du kan uppnå hög tillgänglighet genom att ansluta ExpressRoute-kretsar i olika peering-platser (till exempel Singapore, Singapore2) till ditt virtuella nätverk. Om en ExpressRoute-krets kraschar, misslyckas anslutningen över till en annan ExpressRoute-krets. Som standard dirigeras trafik som lämnar det virtuella nätverket baserat på lika med kostnaden Multipath routning (ECMP). Du kan använda Anslutningsvikten för att föredra en kanal till en annan. Mer information finns i [optimera ExpressRoute-routning](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hur gör jag för att se till att min trafik som är avsedd för Azures offentliga tjänster som Azure Storage och Azure SQL på Microsoft peering eller offentlig peering föredras på ExpressRoute-sökvägen?

Du måste implementera attributet *lokal inställning* på din router (er) för att säkerställa att sökvägen från lokal till Azure alltid prioriteras på dina ExpressRoute-kretsar.

Se ytterligare information [här](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) om val av BGP-sökvägar och vanliga konfigurationer av routrar. 

### <a name="onep2plink"></a>Måste jag beställa två fysiska anslutningar mellan mitt lokala nätverk och Microsoft om jag inte är samordnad i ett moln utbyte och min tjänst leverantör har en punkt-till-punkt-anslutning?

Om din tjänstleverantör kan upprätta två virtuella Ethernet-kretsar över den fysiska anslutningen, behöver du bara en fysisk anslutning. Den fysiska anslutningen (till exempel en optisk fiber) avslutas på en nivå 1 (L1)-enhet (se bilden). De två virtuella Ethernet-kretsarna märks med olika VLAN-ID, en för den primära kretsen och en för sekundärt. Dessa VLAN-ID som finns i den yttre 802.1Q Ethernet-huvudet. Det inre 802.1 Q Ethernet-huvudet (visas inte) mappas till en speciell [ExpressRoute-routningsdomän](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kan jag förlänga en av Mina virtuella lokala nätverk till Azure med ExpressRoute?

Nej. Vi stöder inte tillägg av layer 2-anslutning till Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Kan jag ha fler än en ExpressRoute-krets i min prenumeration?

Ja. Du kan ha fler än en ExpressRoute-krets i din prenumeration. Standardgränsen är inställd på 10. Om det behövs kan du kontakta Microsoft Support om du vill utöka gränsen.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kan jag ha ExpressRoute-kretsar från olika leverantörer?

Ja. Du kan ha ExpressRoute-kretsar med många leverantörer av tjänster. Varje ExpressRoute-krets är associerad med endast en tjänstleverantör. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Jag ser två ExpressRoute-peeringplatser i samma metro, till exempel Singapore och Singapore2. Vilka peeringplats ska jag välja att skapa min ExpressRoute-krets?
Om din tjänstleverantör erbjuder ExpressRoute på båda platserna, du samarbeta med leverantören och välj någon av platsernas att ställa in ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Kan jag har flera ExpressRoute-kretsar i samma metro? Kan jag koppla dem till samma virtuella nätverk?

Ja. Du kan ha flera ExpressRoute-kretsar med samma eller olika leverantörer. Om metro har flera ExpressRoute-peeringplatser och kretsarna som skapas på olika peering-platser, kan du länka dem till samma virtuella nätverk. Om kretsarna skapas på samma peering-plats kan du länka upp till 4 kretsar till samma virtuella nätverk.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hur ansluter jag mitt virtuella nätverk till en ExpressRoute-krets

De grundläggande stegen är:

* Upprätta en ExpressRoute-krets och ha tjänstleverantören aktivera den.
* Du eller providern, måste konfigurera BGP peering (s).
* Länka det virtuella nätverket till ExpressRoute-kretsen.

Mer information finns i [ExpressRoute-arbetsflöden för krets etablering och krets tillstånd](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Finns det anslutning gränser för min ExpressRoute-krets?

Ja. Artikeln [ExpressRoute partners och platser](expressroute-locations.md) ger en översikt över anslutnings gränserna för en ExpressRoute-krets. Anslutningen för en ExpressRoute-krets är begränsad till en enda geopolitisk region. Anslutningen kan utökas mellan geopolitiska regioner genom att aktivera ExpressRoute premium-funktion.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kan jag länkar till mer än ett virtuellt nätverk till en ExpressRoute-krets?

Ja. Du kan ha upp till 10 virtuella nätverks anslutningar på en standard ExpressRoute-krets och upp till 100 på en [Premium ExpressRoute-krets](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Jag har flera Azure-prenumerationer som innehåller virtuella nätverk. Kan jag ansluta virtuella nätverk som finns i olika prenumerationer till en enda ExpressRoute-krets?

Ja. Du kan länka upp till 10 virtuella nätverk i samma prenumeration som kretsen eller olika prenumerationer med en enda ExpressRoute-krets. Den här gränsen kan ökas genom att aktivera ExpressRoute premium-funktion.

Mer information finns i [dela en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Jag har flera Azure-prenumerationer som är kopplad till olika klienter i Azure Active Directory eller Enterprise-avtal registreringar. Kan jag ansluta virtuella nätverk som finns i separata klienter och registreringar till en enda ExpressRoute-krets inte i samma klient eller registrering?

Ja. ExpressRoute-auktoriseringar kan sträcka sig över gränser för prenumerationen, klienten och registrering utan någon ytterligare konfiguration krävs. 

Mer information finns i [dela en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Virtuella nätverk är anslutna till samma krets isoleras från varandra?

Nej. Från en routning perspektiv alla virtuella nätverk som är länkade till samma ExpressRoute-kretsen är en del av samma routningsdomän och är inte isolerade från varandra. Om du behöver dirigera isolering, måste du skapa en separat ExpressRoute-krets.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kan jag ha ett virtuellt nätverk som är anslutna till flera ExpressRoute-krets?

Ja. Du kan länka ett enda virtuellt nätverk med upp till fyra ExpressRoute-kretsar i antingen samma eller olika peering-platser. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag ansluta till Internet från mitt virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Om du inte har annonserat standardvägar (0.0.0.0/0) eller Internet route prefix via BGP-sessionen, kan du ansluta till Internet från ett virtuellt nätverk som är länkad till en ExpressRoute-krets.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag blockera Internet-anslutning till virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Du annonserar ut standardrutter (0.0.0.0/0) för att blockera alla Internet-anslutning till virtuella datorer som distribueras inom ett virtuellt nätverk och dirigera all trafik ut via ExpressRoute-kretsen.

Om du annonserar ut standardrutter tvinga vi trafik till tjänster som erbjuds via Microsoft-peering (till exempel Azure storage och SQL DB) tillbaka till ditt lokala nätverk. Du måste konfigurera dina routrar för att returnera trafik till Azure via Microsofts peeringsökväg eller via Internet. Om du har aktiverat en tjänstslutpunkt för tjänsten kan tvingas trafik till tjänsten inte till ditt lokala nätverk. Trafiken kvar i Azure-stamnätverket. Mer information om tjänst slut punkter finns i [tjänst slut punkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Virtuella nätverk som är länkade till samma ExpressRoute-kretsen kan kommunicera med varandra?

Ja. Virtuella datorer som distribueras i virtuella nätverk som är anslutna till samma ExpressRoute-krets kan kommunicera med varandra.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Kan jag använda plats-till-plats-anslutning för virtuella nätverk tillsammans med ExpressRoute?

Ja. ExpressRoute kan samexistera med plats-till-plats-VPN. Se [Konfigurera ExpressRoute-och plats-till-plats-sambefintliga anslutningar](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Varför finns det en offentlig IP-adress som är associerade med ExpressRoute-gateway i ett virtuellt nätverk?

Offentliga IP-adress används endast för interna och utgör inte en säkerhetsrisk för det virtuella nätverket.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Finns det någon gräns för hur många vägar som jag kan annonsera?

Ja. Vi kan acceptera upp till 4 000 prefix som vägen för privat peering och 200 för Microsoft-peering. Du kan öka detta till 10 000 vägar för privat peering om du aktiverar ExpressRoute premium-funktion.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Finns det några begränsningar för IP-adressintervall som jag kan annonsera i BGP-sessionen?

Vi accepterar inte privata-prefix (RFC1918) för Microsoft-peering BGP-sessionen. Vi accepterar alla prefixlängd (upp till/32) på både Microsoft och den privata peeringen.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Vad händer om jag överskrider BGP begränsar?

BGP-sessioner kommer att tas bort. De kommer att återställas när antalet prefix sjunker till under gränsen.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Vad är ExpressRoute BGP driftstid? Kan den justeras?

Hold-tid är 180. Keep-alive-meddelanden skickas var 60: e sekund. Dessa är fasta inställningar på Microsoft-sida som inte kan ändras. Det är möjligt för dig att konfigurera olika timers och parametrar för BGP-sessionen förhandlas därefter.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Kan jag ändra bandbredden för en ExpressRoute-krets?

Ja, du kan försöka att öka bandbredden för ExpressRoute-kretsen i Azure portal eller med hjälp av PowerShell. Om det finns kapacitet på den fysiska porten där din krets skapades, lyckas din ändring. 

Om din ändring misslyckas, betyder det antingen det finns inte tillräckligt med kapacitet kvar på den aktuella porten och du måste skapa en ny ExpressRoute-krets med högre bandbredd, eller att det finns inga ytterligare kapacitet på den platsen, i vilket fall du kommer inte att kunna öka den bandbredd. 

Du måste också följa upp din anslutningsleverantör för att säkerställa att de uppdaterar begränsningar i sina nätverk för ökade bandbredden. Du kan dock minska bandbredden för ExpressRoute-kretsen. Du måste skapa en ny ExpressRoute-krets med låg bandbredd och ta bort den gamla kretsen.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hur ändrar jag bandbredden för en ExpressRoute-krets?

Du kan uppdatera bandbredden för ExpressRoute-krets med hjälp av REST API eller PowerShell-cmdlet.

## <a name="expressroute-premium"></a>ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>Vad är ExpressRoute premium?

ExpressRoute premium är en samling med följande funktioner:

* Ökad routning tabell gräns från 4000 vägar till 10 000 vägar för privat peering.
* Ökat antal virtuella nätverk och Global räckvidd i ExpressRoute-anslutningar som kan aktiveras för en ExpressRoute-krets (standardvärdet är 10). Mer information finns i [ExpressRoute Limits](#limits) -tabellen.
* Anslutning till Office 365
* Global anslutbarhet via Microsoft core nätverket. Du kan nu länka ett virtuellt nätverk i en geopolitisk region med en ExpressRoute-krets i en annan region.<br>
    **Fler**

    *  Du kan länka ett virtuellt nätverk som skapats i västra Europa till en ExpressRoute-krets som skapats i Silicon Valley. 
    *  På Microsoft-peering, annonseras prefix från andra geopolitiska regioner, så att du kan ansluta till, till exempel SQL Azure i västra Europa från en krets i Silicon Valley.


### <a name="limits"></a>Hur många virtuella nätverk-och ExpressRoute Global Reach-anslutningar kan jag aktivera på en ExpressRoute-krets om jag har aktiverat ExpressRoute Premium?

Följande tabeller visar ExpressRoute-gränser och antalet virtuella nätverk och Global räckvidd i ExpressRoute-anslutningar per ExpressRoute-krets:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hur aktiverar jag ExpressRoute premium?

ExpressRoute premium-funktioner kan aktiveras när funktionen är aktiverad och kan stängas av genom att uppdatera tillståndet för kretsen. Du kan aktivera ExpressRoute premium vid tidpunkten för skapandet av kretsen eller kan anropa REST API / PowerShell-cmdlet.

### <a name="how-do-i-disable-expressroute-premium"></a>Hur inaktiverar jag ExpressRoute premium?

Du kan inaktivera ExpressRoute premium genom att anropa REST API eller PowerShell-cmdlet. Du måste se till att du har skalat anslutning behöver uppfylla standardgränserna innan du inaktiverar ExpressRoute premium. Om din användning skalas bortom standardgränserna, misslyckas denna begäran att inaktivera ExpressRoute premium.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kan jag välja de funktioner som jag vill från funktionsuppsättningen premium?

Nej. Du kan inte välja funktionerna. Vi möjliggör alla funktioner när du aktiverar ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Hur mycket kostar ExpressRoute premium?

Se [pris information](https://azure.microsoft.com/pricing/details/expressroute/) för kostnad.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Måste jag betala för ExpressRoute premium utöver avgifterna för standard ExpressRoute?

Ja. ExpressRoute premium-avgifter gäller utöver avgifterna för ExpressRoute-krets och avgifter som krävs av anslutningsprovidern.

## <a name="expressroute-local"></a>ExpressRoute-lokal
### <a name="what-is-expressroute-local"></a>Vad är ExpressRoute Local?
ExpressRoute Local är en SKU av ExpressRoute-kretsen, förutom standard-SKU: n och Premium-SKU: n. En viktig funktion i lokal är att en lokal krets på en ExpressRoute-peering-plats ger dig åtkomst endast till en eller två Azure-regioner i eller nära samma tunnelbane linje. En standard-krets ger däremot till gång till alla Azure-regioner i ett politiskt område och en Premium-krets till alla Azure-regioner globalt. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Vilka är fördelarna med ExpressRoute Local?
Du måste betala utgående data överföring för din standard-eller Premium ExpressRoute-krets, men du betalar inte utgående data överföring separat för din ExpressRoute-lokala krets. Priset för ExpressRoute Local inkluderar med andra ord avgifter för data överföring. ExpressRoute Local är en mer ekonomisk lösning om du har en enorm mängd data som ska överföras och du kan ta med dina data över en privat anslutning till en ExpressRoute-peering-plats nära önskade Azure-regioner. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Vilka funktioner är tillgängliga och vilka som inte finns på ExpressRoute lokala?
Jämfört med en vanlig ExpressRoute-krets har en lokal krets samma uppsättning funktioner, förutom:
* Omfattning av åtkomst till Azure-regioner enligt beskrivningen ovan
* ExpressRoute-Global Reach finns inte på den lokala datorn

ExpressRoute Local har också samma begränsningar för resurser (t. ex. antalet virtuella nätverk per krets) som standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Var finns ExpressRoute lokalt och vilka Azure-regioner är varje peering-plats mappad till?
ExpressRoute Local är tillgängligt på peering-platserna där en eller två Azure-regioner stängs av. Den är inte tillgänglig på en peering-plats där det inte finns någon Azure-region i detta tillstånd eller region eller land. Se exakta mappningar på [sidan platser](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>ExpressRoute för Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Hur skapar jag en ExpressRoute-krets för att ansluta till Office 365-tjänster?

1. Granska [sidan ExpressRoute-krav](expressroute-prerequisites.md) för att se till att du uppfyller kraven.
2. För att säkerställa att dina anslutnings behov är uppfyllda granskar du listan över tjänst leverantörer och platser i artikeln [ExpressRoute partners och platser](expressroute-locations.md) .
3. Planera dina kapacitets krav genom att granska [nätverks planering och prestanda justering för Office 365](https://aka.ms/tune/).
4. Följ stegen som listas i arbets flödena för att konfigurera anslutnings [ExpressRoute arbets flöden för krets etablering och krets tillstånd](expressroute-workflows.md).

> [!IMPORTANT]
> Kontrollera att du har aktiverat ExpressRoute premium-tillägget när du konfigurerar anslutningen till Office 365-tjänster.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Kan mina befintliga ExpressRoute-kretsar stödja anslutning till Office 365-tjänster?

Ja. Befintliga ExpressRoute-kretsen kan konfigureras för att stödja anslutningen till Office 365-tjänster. Se till att du har tillräcklig kapacitet för att ansluta till Office 365-tjänster och att du har aktiverat premium-tillägget. [Nätverks planering och prestanda justering för Office 365](https://aka.ms/tune/) hjälper dig att planera dina anslutnings behov. Se även [skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Vilka Office 365 tjänster kan nås via en ExpressRoute-anslutning?

Se sidan [Office 365-URL: er och IP-adressintervall](https://aka.ms/o365endpoints) för en uppdaterad lista över tjänster som stöds över ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Hur mycket kostar ExpressRoute för Office 365-tjänster kostnad?

Office 365-tjänster kräver premium-tillägget är aktiverat. Se [pris informations sidan](https://azure.microsoft.com/pricing/details/expressroute/) för kostnader.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Vilka regioner stöds ExpressRoute för Office 365 i?

Mer information finns i [ExpressRoute-partners och-platser](expressroute-locations.md) .

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kan jag komma åt Office 365 via Internet, även om ExpressRoute har konfigurerats för min organisation?

Ja. Office 365-Tjänsteslutpunkter kan nås via Internet, även om ExpressRoute har konfigurerats för ditt nätverk. Kontakta din organisations nätverksteam om nätverket på din plats är konfigurerad för att ansluta till Office 365-tjänster via ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hur kan jag planera för hög tillgänglighet för Office 365-nätverkstrafik på Azure ExpressRoute?
Se rekommendationer för [hög tillgänglighet och redundans med Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Kan jag komma åt Office 365 US Government Community (GCC) tjänster via en Azure US Government ExpressRoute-krets?

Ja. Office 365 GCC Tjänsteslutpunkter kan nås via Azure US Government ExpressRoute. Men måste du först öppna ett supportärende på Azure portal för att tillhandahålla de prefix som du planerar att annonsera till Microsoft. Anslutningen till Office 365 GCC tjänster upprättas när supportärende har åtgärdats. 

## <a name="route-filters-for-microsoft-peering"></a>Routningsfilter för Microsoft-peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Jag är att aktivera Microsoft-peering för första gången, vilka vägar ser jag?

Du kommer inte se några vägar. Du måste koppla ett flödesfilter till kretsen att starta prefix annonser. Instruktioner finns i [Konfigurera väg filter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Jag aktiverade för Microsoft-peering och nu försöker jag väljer Exchange Online, men det ge mig ett fel att jag inte har behörighet att utföra den.

När du använder flödesfilter kan kan alla kunder aktivera Microsoft-peering. För att använda Office 365-tjänster behöver du dock fortfarande att få behörighet av Office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Jag har aktiverat Microsoft-peering före den 1 augusti 2017, hur kan jag dra nytta av flödesfilter?

Din befintliga krets fortsätter att annonsera om prefixen för Office 365. Om du vill lägga till offentliga Azure-prefix över samma Microsoft-peering kan du skapa ett flödes filter, välja de tjänster som du behöver annonseras (inklusive de Office 365-tjänster du behöver) och koppla filtret till din Microsoft-peering. Instruktioner finns i [Konfigurera väg filter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Jag har Microsoft-peering på en plats, nu jag försöker aktivera den på en annan plats och jag ser inte alla prefix.

* Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service-prefix som annonseras via Microsoft-peering, även om flödesfilter inte har definierats.

* Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett flödesfilter är kopplad till kretsen. Inga prefix visas som standard.

## <a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
