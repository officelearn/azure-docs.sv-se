---
title: Vanliga frågor och svar – Azure ExpressRoute | Microsoft-dokument
description: Vanliga frågor och svar om ExpressRoute innehåller information om Azure-tjänster, kostnader, data och anslutningar, SLA, leverantörer och platser, bandbredd och ytterligare teknisk information.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: jaredro
ms.openlocfilehash: 845c53ec970777901ae8d1c0abf5032ac705d3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264926"
---
# <a name="expressroute-faq"></a>Vanliga frågor och svar för ExpressRoute

## <a name="what-is-expressroute"></a>Vad är ExpressRoute?

ExpressRoute är en Azure-tjänst som låter dig skapa privata anslutningar mellan Microsoft-datacenter och infrastruktur som finns i dina lokaler eller i en samlokaliseringsfunktion. ExpressRoute-anslutningar går inte över det offentliga Internet och erbjuder högre säkerhet, tillförlitlighet och hastigheter med lägre svarstider än typiska anslutningar via Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Vilka är fördelarna med att använda ExpressRoute och privata nätverksanslutningar?

ExpressRoute-anslutningar går inte via offentligt Internet. De erbjuder högre säkerhet, tillförlitlighet och hastigheter, med lägre och konsekventa svarstider än typiska anslutningar via Internet. I vissa fall kan användning av ExpressRoute-anslutningar för att överföra data mellan lokala enheter och Azure ge betydande kostnadsfördelar.

### <a name="where-is-the-service-available"></a>Var är tjänsten tillgänglig?

Se den här sidan för tjänstplats och tillgänglighet: [ExpressRoute-partner och platser](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hur kan jag använda ExpressRoute för att ansluta till Microsoft om jag inte har partnerskap med en av ExpressRoute-carrier-partnerna?

Du kan välja en regional bärare och landa Ethernet-anslutningar till en av de platser som stöds utbyte leverantör. Du kan sedan peer med Microsoft på providern plats. Kontrollera det sista avsnittet av [ExpressRoute-partner och platser](expressroute-locations.md) för att se om din tjänsteleverantör finns på någon av utbytesplatserna. Du kan sedan beställa en ExpressRoute-krets via tjänsteleverantören för att ansluta till Azure.

### <a name="how-much-does-expressroute-cost"></a>Hur mycket kostar ExpressRoute?

Kontrollera [prisinformation](https://azure.microsoft.com/pricing/details/expressroute/) för prisinformation.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Om jag betalar för en ExpressRoute-krets med en viss bandbredd, måste VPN-anslutningen jag köper från min nättjänstleverantör vara samma hastighet?

Nej. Du kan köpa en VPN-anslutning med valfri hastighet från din tjänsteleverantör. Anslutningen till Azure är dock begränsad till expressroutekretsbandbredden som du köper.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Om jag betalar för en ExpressRoute krets av en viss bandbredd, har jag möjlighet att brista upp till högre hastigheter om det behövs?

Ja. ExpressRoute-kretsar är konfigurerade så att du kan spränga upp till två gånger bandbreddsgränsen som du skaffade utan extra kostnad. Kontrollera med din tjänsteleverantör om de stöder den här funktionen. Detta är inte för en längre tid och är inte garanterad.  Om trafiken flödar genom en ExpressRoute Gateway är bandbredden för sku fast och kan inte brista.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kan jag använda samma privata nätverksanslutning med virtuella nätverk och andra Azure-tjänster samtidigt?

Ja. En ExpressRoute-krets, när den väl har konfigurerats, gör att du kan komma åt tjänster inom ett virtuellt nätverk och andra Azure-tjänster samtidigt. Du ansluter till virtuella nätverk via den privata peering-sökvägen och till andra tjänster via Microsofts peering-sökväg.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Hur annonseras virtuella nätverk på ExpressRoute Private Peering?

ExpressRoute-gatewayen annonserar *adressutrymmen* för Azure VNet, du kan inte inkludera/utesluta på undernätsnivå. Det är alltid VNet-adressutrymmet som annonseras. Dessutom, om VNet Peering används och peered VNet har "Använd Remote Gateway" aktiverat, kommer adressutrymmet för det peer-peer-virtuella nätverket också att annonseras.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Hur många prefix kan annonseras från ett virtuella nätverk till lokalt på ExpressRoute Private Peering?

Det finns högst 200 prefix som annonseras på en enda ExpressRoute-anslutning, eller via VNet-peering med gatewaytransas. Om du till exempel har 199 adressutrymmen på ett enda virtuella nätverk som är anslutet till en ExpressRoute-krets, kommer alla 199 av dessa prefix att annonseras till lokalt. Alternativt, om du har ett VNet aktiverat för att tillåta gateway transit med 1 adressutrymme och 150 ekrar virtuella nätverk aktiverat med alternativet "Tillåt fjärrgateway", kommer det virtuella nätverket som distribueras med gatewayen att annonsera 151 prefix till lokala.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Vad händer om jag överskrider prefixgränsen för en ExpressRoute-anslutning?

Anslutningen mellan ExpressRoute-kretsen och gatewayen (och peer-ed-virtuella nätverk med gatewaytransit, om tillämpligt) kommer att gå ner. Det kommer att återupprättas när prefixgränsen inte längre överskrids.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Kan jag filtrera rutter som kommer från mitt lokala nätverk?

Det enda sättet att filtrera/inkludera vägar finns på den lokala kantroutern. Användardefinierade vägar kan läggas till i det virtuella nätverket för att påverka specifik routning, men detta kommer att vara statiskt och inte en del av BGP-annonsen.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Erbjuder ExpressRoute ett servicenivåavtal (SLA)?

Mer information finns på sidan [ExpressRoute SLA.](https://azure.microsoft.com/support/legal/sla/)

## <a name="supported-services"></a>Tjänster som stöds

ExpressRoute stöder [tre routningsdomäner](expressroute-circuit-peerings.md) för olika typer av tjänster: privat peering, Microsoft-peering och offentlig peering (föråldrad).

### <a name="private-peering"></a>Privat peering

**Stöds:**

* Virtuella nätverk, inklusive alla virtuella datorer och molntjänster

### <a name="microsoft-peering"></a>Microsoft-peering

Om din ExpressRoute-krets är aktiverad för Azure Microsoft-peering kan du komma åt de [offentliga IP-adressintervall](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) som används i Azure över kretsen. Azure Microsoft-peering ger åtkomst till tjänster som för närvarande finns på Azure (med geo-begränsningar beroende på kretsens SKU). Om du vill verifiera tillgängligheten för en viss tjänst kan du läsa dokumentationen för den tjänsten för att se om det finns ett reserverat intervall som publicerats för den tjänsten. Slå sedan upp IP-intervallen för måltjänsten och jämför med intervallen i [Azure IP Ranges and Service Tags – Public Cloud XML-fil](https://www.microsoft.com/download/details.aspx?id=56519). Alternativt kan du öppna en supportbiljett för tjänsten i fråga för förtydligande.

**Stöds:**

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Power BI - Finns via en Regional Azure-community, se [här](https://docs.microsoft.com/power-bi/service-admin-where-is-my-tenant-located) för hur du hittar regionen för din Power BI-klientorganisation.
* Azure Active Directory
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azure Global Services-community)
* Offentliga Azure-IP-adresser för IaaS (virtuella datorer, virtuella nätverksgateways, belastningsutjämnare osv.)  
* De flesta andra Azure-tjänster stöds också. Kontrollera direkt med den tjänst som du vill använda för att verifiera supporten.

**Stöds inte:**

* CDN
* Azure Front Door
* Multifaktorautentiseringsserver (äldre)
* Traffic Manager

### <a name="public-peering"></a>Offentlig peering

Offentlig peering har inaktiverats på nya ExpressRoute-kretsar. Azure-tjänster är nu tillgängliga på Microsoft-peering. Om du har skapat en krets som skapades innan den offentliga peeringen blev inaktuell kan du välja att använda Microsoft-peering eller offentlig peering, beroende på vilka tjänster du vill ha.

Mer information och konfigurationssteg för offentlig peering finns i [ExpressRoute public peering](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Varför jag ser statusen Annonserade offentliga prefix som "Validering behövs" när microsoft-peering konfigureras?

Microsoft verifierar om de angivna prefixen för annonserade offentliga prefix och peer ASN (eller "Kundens ASN") har tilldelats dig i Internet-routningsregistret. Om du får de offentliga prefixen från en annan entitet och om tilldelningen inte registreras med routningsregistret slutförs inte den automatiska valideringen och kräver manuell validering. Om den automatiska valideringen misslyckas visas meddelandet "Validering behövs".

Om meddelandet "Validering behövs" samlar du in de dokument som visar att de offentliga prefixen tilldelas din organisation av den entitet som anges som ägare till prefixen i routningsregistret och skickar dessa dokument för manuell validering av öppna en supportbiljett som visas nedan.

![](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Stöds Dynamics 365 på ExpressRoute?

Dynamics 365- och Common Data Service-miljöer (CDS) finns på Azure och därför drar kunderna nytta av det underliggande ExpressRoute-stödet för Azure-resurser. Du kan ansluta till dess tjänstslutpunkter om routerfiltret innehåller de Azure-regioner som dina Dynamics 365/CDS-miljöer finns i.

> [!NOTE]
> [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium) krävs **inte** för Dynamics 365-anslutning via Azure ExpressRoute.

## <a name="data-and-connections"></a>Data och anslutningar

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Finns det gränser för hur mycket data som jag kan överföra med ExpressRoute?

Vi sätter ingen gräns för mängden dataöverföring. Se [prisinformation](https://azure.microsoft.com/pricing/details/expressroute/) för information om bandbreddspriser.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Vilka anslutningshastigheter stöds av ExpressRoute?

Bandbredd som stöds erbjuder:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Vilka tjänsteleverantörer är tillgängliga?

Se [ExpressRoute-partner och platser](expressroute-locations.md) för listan över tjänsteleverantörer och platser.

## <a name="technical-details"></a>Teknisk information

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Vilka är de tekniska kraven för att ansluta min lokala plats till Azure?

Se [Sida för Krav för Krav.](expressroute-prerequisites.md)

### <a name="are-connections-to-expressroute-redundant"></a>Är anslutningar till ExpressRoute överflödiga?

Ja. Varje ExpressRoute-krets har ett redundant par korsanslutningar konfigurerade för att ge hög tillgänglighet.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Kommer jag att förlora anslutningen om en av mina ExpressRoute-länkar misslyckas?

Du kommer inte att förlora anslutningen om en av korsanslutningarna misslyckas. En redundant anslutning är tillgänglig för att stödja belastningen på nätverket och ge hög tillgänglighet för din ExpressRoute-krets. Du kan dessutom skapa en krets på en annan peering-plats för att uppnå återhämtning på kretsnivå.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hur implementerar jag redundans på privat peering?

Flera ExpressRoute-kretsar från olika peering-platser kan anslutas till samma virtuella nätverk för att ge hög tillgänglighet om en enda krets blir otillgänglig. Du kan sedan [tilldela högre vikter](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection) till den lokala anslutningen till förmån föredrar en viss krets. Vi rekommenderar starkt att kunderna ställer in minst två ExpressRoute-kretsar för att undvika enstaka felpunkter. 

Se [här](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) för design för hög tillgänglighet och [här](https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering) för att utforma för katastrofåterställning.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hur implementerar jag redundans på Microsoft peering?

Det rekommenderas starkt när kunder använder Microsoft-peering för att komma åt offentliga Azure-tjänster som Azure Storage eller Azure SQL, samt kunder som använder Microsoft-peering för Office 365 som de implementerar flera kretsar i olika peering platser för att undvika enstaka felpunkter. Kunder kan antingen annonsera samma prefix på båda kretsarna och använda [AS PATH prepending](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending) eller annonsera olika prefix för att bestämma sökvägen från lokala.

Se [här](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) för design för hög tillgänglighet.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hur säkerställer jag hög tillgänglighet i ett virtuellt nätverk som är anslutet till ExpressRoute?

Du kan uppnå hög tillgänglighet genom att ansluta ExpressRoute-kretsar på olika peering-platser (till exempel Singapore, Singapore2) till ditt virtuella nätverk. Om en ExpressRoute-krets går ner, kommer anslutningen att växla över till en annan ExpressRoute-krets. Som standard dirigeras trafik som lämnar det virtuella nätverket baserat på ECMP (Equal Cost Multi-path Routing). Du kan använda Anslutningsvikt för att föredra en krets framför en annan. Mer information finns i [Optimera ExpressRoute Routning](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hur säkerställer jag att min trafik som är avsedd för Azure Public-tjänster som Azure Storage och Azure SQL på Microsoft-peering eller offentlig peering är att föredra på ExpressRoute-sökvägen?

Du måste implementera *attributet Lokal inställning* på routern/routern för att säkerställa att sökvägen från lokalt till Azure alltid är att föredra på dina ExpressRoute-kretsar.

Se ytterligare information [här](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#path-selection-on-microsoft-and-public-peerings) om BGP-sökvägsval och vanliga routerkonfigurationer. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Om jag inte är samlokal och min tjänsteleverantör erbjuder point-to-point-anslutning måste jag beställa två fysiska anslutningar mellan mitt lokala nätverk och Microsoft?

Om din tjänsteleverantör kan upprätta två virtuella Ethernet-kretsar via den fysiska anslutningen behöver du bara en fysisk anslutning. Den fysiska anslutningen (till exempel en optisk fiber) avslutas på en layer 1 -enhet (L1) (se bilden). De två virtuella Ethernet-kretsarna är märkta med olika VLAN-ID:er, en för primärkretsen och en för sekundär. Dessa VLAN-ID:er finns i det yttre 802.1Q Ethernet-huvudet. Det inre 802.1Q Ethernet-huvudet (visas inte) mappas till en specifik [ExpressRoute-routningsdomän](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kan jag utöka ett av mina VPN till Azure med ExpressRoute?

Nej. Vi stöder inte anslutningstillägg för lager 2 till Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Kan jag ha mer än en ExpressRoute-krets i mitt abonnemang?

Ja. Du kan ha mer än en ExpressRoute-krets i din prenumeration. Standardgränsen är inställd på 10. Du kan kontakta Microsoft Support för att öka gränsen om det behövs.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kan jag ha ExpressRoute-kretsar från olika tjänsteleverantörer?

Ja. Du kan ha ExpressRoute-kretsar med många tjänsteleverantörer. Varje ExpressRoute-krets är endast associerad med en tjänsteleverantör. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Jag ser två ExpressRoute peering platser i samma tunnelbana, till exempel, Singapore och Singapore2. Vilken peering-plats ska jag välja att skapa min ExpressRoute-krets?
Om din tjänsteleverantör erbjuder ExpressRoute på båda platserna kan du arbeta med din leverantör och välja någon av platserna för att konfigurera ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Kan jag ha flera ExpressRoute-kretsar i samma tunnelbana? Kan jag länka dem till samma virtuella nätverk?

Ja. Du kan ha flera ExpressRoute-kretsar med samma eller olika tjänsteleverantörer. Om tunnelbanan har flera ExpressRoute-peering-platser och kretsarna skapas på olika peering-platser kan du länka dem till samma virtuella nätverk. Om kretsarna skapas på samma peering-plats kan du länka upp till 4 kretsar till samma virtuella nätverk.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hur ansluter jag mina virtuella nätverk till en ExpressRoute-krets

De grundläggande stegen är:

* Upprätta en ExpressRoute-krets och låt tjänsteleverantören aktivera den.
* Du, eller providern, måste konfigurera BGP-peering(s).
* Länka det virtuella nätverket till ExpressRoute-kretsen.

Mer information finns i [ExpressRoute-arbetsflöden för kretsetablering och kretstillstånd](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Finns det anslutningsgränser för min ExpressRoute-krets?

Ja. I artikeln [ExpressRoute-partner och platser](expressroute-locations.md) finns en översikt över anslutningsgränserna för en ExpressRoute-krets. Anslutningen för en ExpressRoute-krets är begränsad till en enda geopolitisk region. Anslutningen kan utökas till över geopolitiska regioner genom att aktivera ExpressRoute premium-funktionen.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kan jag länka till mer än ett virtuellt nätverk till en ExpressRoute-krets?

Ja. Du kan ha upp till 10 virtuella nätverksanslutningar på en standard ExpressRoute-krets och upp till 100 på en [premium ExpressRoute-krets](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Jag har flera Azure-prenumerationer som innehåller virtuella nätverk. Kan jag ansluta virtuella nätverk som finns i separata prenumerationer till en enda ExpressRoute-krets?

Ja. Du kan länka upp till 10 virtuella nätverk i samma prenumeration som kretsen eller olika prenumerationer med en enda ExpressRoute-krets. Den här gränsen kan ökas genom att aktivera ExpressRoute premium-funktionen.

Mer information finns i [Dela en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Jag har flera Azure-prenumerationer kopplade till olika Azure Active Directory-klienter eller Enterprise Agreement-registreringar. Kan jag ansluta virtuella nätverk som finns i separata klienter och registreringar till en enda ExpressRoute-krets som inte finns i samma klient eller registrering?

Ja. ExpressRoute-auktoriseringar kan spänna över prenumerations-, klient- och registreringsgränser utan ytterligare konfiguration krävs. 

Mer information finns i [Dela en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Är virtuella nätverk anslutna till samma krets isolerade från varandra?

Nej. Från ett routningsperspektiv är alla virtuella nätverk som är länkade till samma ExpressRoute-krets en del av samma routningsdomän och är inte isolerade från varandra. Om du behöver ruttisolering måste du skapa en separat ExpressRoute-krets.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kan jag ha ett virtuellt nätverk anslutet till mer än en ExpressRoute-krets?

Ja. Du kan länka ett enda virtuellt nätverk med upp till fyra ExpressRoute-kretsar på antingen samma eller olika peering-platser. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag ansluta till Internet från mina virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Om du inte har annonserat standardvägar (0.0.0.0/0) eller Internet-routningsprefix via BGP-sessionen kan du ansluta till Internet från ett virtuellt nätverk som är länkat till en ExpressRoute-krets.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag blockera Internetanslutning till virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Du kan annonsera standardvägar (0.0.0.0/0) för att blockera all Internetanslutning till virtuella datorer som distribueras inom ett virtuellt nätverk och dirigera all trafik ut via ExpressRoute-kretsen.

Om du annonserar standardvägar tvingar vi trafik till tjänster som erbjuds via Microsoft-peering (till exempel Azure-lagring och SQL DB) tillbaka till dina lokaler. Du måste konfigurera dina routrar för att returnera trafik till Azure via Microsofts peering-sökväg eller via Internet. Om du har aktiverat en tjänstslutpunkt för tjänsten tvingas inte trafiken till tjänsten till din lokal. Trafiken finns kvar i Azure-stamnätet. Mer information om tjänstslutpunkter finns i [slutpunkter för virtuella nätverkstjänster](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Kan virtuella nätverk som är länkade till samma ExpressRoute-krets prata med varandra?

Ja. Virtuella datorer som distribueras i virtuella nätverk som är anslutna till samma ExpressRoute-krets kan kommunicera med varandra.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Kan jag använda anslutning från plats till plats för virtuella nätverk tillsammans med ExpressRoute?

Ja. ExpressRoute kan samexistera med virtuella VPN från plats till plats. Se [Konfigurera ExpressRoute- och plats-till-plats-samtidiga anslutningar](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Varför finns det en offentlig IP-adress som är associerad med ExpressRoute-gatewayen i ett virtuellt nätverk?

Den offentliga IP-adressen används endast för intern hantering och utgör inte en säkerhetsexponering för ditt virtuella nätverk.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Finns det gränser för hur många rutter jag kan annonsera?

Ja. Vi accepterar upp till 4000 ruttprefix för privat peering och 200 för Microsoft-peering. Du kan öka detta till 10 000 rutter för privat peering om du aktiverar Premium-funktionen ExpressRoute.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Finns det begränsningar för IP-intervall jag kan annonsera över BGP-sessionen?

Vi accepterar inte privata prefix (RFC1918) för Microsoft peering BGP-sessionen. Vi accepterar alla prefixstorlek (upp till / 32) på både Microsoft och den privata peering.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Vad händer om jag överskrider BGP-gränserna?

BGP-sessioner kommer att tas bort. De återställs när prefixantalet går under gränsen.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Vad är ExpressRoute BGP håll tid? Kan den justeras?

Väntetiden är 180. Meddelandena skickas var 60:e sekund. Det här är fasta inställningar på Microsoft-sidan som inte kan ändras. Det är möjligt för dig att konfigurera olika timers, och BGP-sessionsparametrarna kommer att förhandlas fram därefter.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Kan jag ändra bandbredden för en ExpressRoute-krets?

Ja, du kan försöka öka bandbredden för din ExpressRoute-krets i Azure-portalen eller genom att använda PowerShell. Om det finns kapacitet tillgänglig på den fysiska porten där kretsen skapades, lyckas ändringen. 

Om ändringen misslyckas betyder det att det antingen inte finns tillräckligt med kapacitet kvar på den aktuella porten och du måste skapa en ny ExpressRoute-krets med den högre bandbredden, eller att det inte finns någon ytterligare kapacitet på den platsen, i vilket fall du inte kan öka Bandbredd. 

Du måste också följa upp med din anslutningsleverantör för att säkerställa att de uppdaterar gasreglagen i sina nätverk för att stödja bandbreddsökningen. Du kan dock inte minska bandbredden på din ExpressRoute-krets. Du måste skapa en ny ExpressRoute krets med lägre bandbredd och ta bort den gamla kretsen.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hur ändrar jag bandbredden för en ExpressRoute-krets?

Du kan uppdatera bandbredden för ExpressRoute-kretsen med REST API- eller PowerShell-cmdlet.

## <a name="expressroute-premium"></a>ExpressRoute-premie

### <a name="what-is-expressroute-premium"></a>Vad är ExpressRoute premium?

ExpressRoute premium är en samling av följande funktioner:

* Utökad routningstabellgräns från 4 000 vägar till 10 000 vägar för privat peering.
* Ökat antal virtuella nätverk och ExpressRoute Global Reach-anslutningar som kan aktiveras på en ExpressRoute-krets (standard är 10). Mer information finns i tabellen [ExpressRoute Limits.](#limits)
* Anslutning till Office 365
* Global anslutning via Microsofts kärnnätverk. Du kan nu länka ett VNet i en geopolitisk region med en ExpressRoute-krets i en annan region.<br>
    **Exempel:**

    *  Du kan länka ett virtuella nätverk som skapats i Europa Väst till en ExpressRoute-krets som skapats i Silicon Valley. 
    *  På Microsoft-peering annonseras prefix från andra geopolitiska regioner så att du kan ansluta till till exempel SQL Azure i Europa Väst från en krets i Silicon Valley.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Hur många virtuella nätverk och ExpressRoute Global Reach-anslutningar kan jag aktivera på en ExpressRoute-krets om jag har aktiverat ExpressRoute premium?

Följande tabeller visar ExpressRoute-gränserna och antalet virtuella nätverk och ExpressRoute Global Reach-anslutningar per ExpressRoute-krets:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hur aktiverar jag ExpressRoute premium?

ExpressRoute premiumfunktioner kan aktiveras när funktionen är aktiverad och kan stängas av genom att uppdatera kretstillståndet. Du kan aktivera ExpressRoute premium vid skapande av kretser, eller kan anropa REST API / PowerShell-cmdlet.

### <a name="how-do-i-disable-expressroute-premium"></a>Hur inaktiverar jag ExpressRoute premium?

Du kan inaktivera ExpressRoute premium genom att anropa REST API eller PowerShell-cmdlet. Du måste se till att du har skalat upp dina anslutningsbehov för att uppfylla standardgränserna innan du inaktiverar ExpressRoute premium. Om din användning skalas utöver standardgränserna misslyckas begäran om att inaktivera ExpressRoute-premium.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kan jag välja vilka funktioner jag vill ha i premiumfunktionen?

Nej. Du kan inte välja funktioner. Vi aktiverar alla funktioner när du aktiverar ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Hur mycket kostar ExpressRoute premium kostnad?

Se [prisinformation](https://azure.microsoft.com/pricing/details/expressroute/) för kostnad.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Betalar jag för ExpressRoute-premie utöver vanliga ExpressRoute-avgifter?

Ja. ExpressRoute-premiumavgifter tillkommer utöver ExpressRoute-kretsladdningar och avgifter som krävs av anslutningsleverantören.

## <a name="expressroute-local"></a>ExpressRoute Lokal
### <a name="what-is-expressroute-local"></a>Vad är ExpressRoute Local?
ExpressRoute Local är en SKU av ExpressRoute krets, förutom Standard SKU och Premium SKU. En viktig funktion i Lokal är att en lokal krets på en ExpressRoute-peering-plats ger dig endast åtkomst till en eller två Azure-regioner i eller i närheten av samma tunnelbana. Däremot ger en standardkrets dig åtkomst till alla Azure-regioner i ett geopolitiskt område och en Premium-krets till alla Azure-regioner globalt. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Vilka är fördelarna med ExpressRoute Local?
Även om du behöver betala dataöverföring för din Standard- eller Premium ExpressRoute-krets betalar du inte dataöverföring för utgång separat för din ExpressRoute Local-krets. Med andra ord inkluderar priset på ExpressRoute Local avgifter för dataöverföring. ExpressRoute Local är en mer ekonomisk lösning om du har enorma mängder data att överföra och du kan överföra dina data via en privat anslutning till en ExpressRoute-peering-plats nära dina önskade Azure-regioner. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Vilka funktioner är tillgängliga och vad finns inte på ExpressRoute Local?
Jämfört med en Standard ExpressRoute-krets har en lokal krets samma uppsättning funktioner förutom:
* Omfattningen av åtkomsten till Azure-regioner enligt beskrivningen ovan
* ExpressRoute Global Reach är inte tillgängligt på Lokal

ExpressRoute Local har också samma begränsningar för resurser (t.ex. antalet virtuella nätverk per krets) som standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Var är ExpressRoute Local tillgängligt och vilka Azure-regioner mappas varje peering-plats till?
ExpressRoute Local är tillgängligt på peering-platserna där en eller två Azure-regioner ligger i närheten. Den är inte tillgänglig på en peering-plats där det inte finns någon Azure-region i den staten eller provinsen eller landet. Se de exakta mappningarna på [sidan Platser](expressroute-locations-providers.md).  

## <a name="expressroute-for-office-365"></a>ExpressRoute för Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Hur skapar jag en ExpressRoute-krets för att ansluta till Office 365-tjänster?

1. Gå igenom [sidan Förutsättningarna för ExpressRoute](expressroute-prerequisites.md) för att se till att du uppfyller kraven.
2. För att säkerställa att dina anslutningsbehov uppfylls kan du läsa listan över tjänsteleverantörer och platser i artikeln [ExpressRoute-partner och platser.](expressroute-locations.md)
3. Planera dina kapacitetskrav genom att granska [Nätverksplanering och prestandajustering för Office 365](https://aka.ms/tune/).
4. Följ stegen i arbetsflödena för att konfigurera [ExpressRoute-arbetsflöden för anslutningsinställningar för kretsetablering och kretstillstånd](expressroute-workflows.md).

> [!IMPORTANT]
> Kontrollera att du har aktiverat ExpressRoute premium-tillägg när du konfigurerar anslutningen till Office 365-tjänster.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services"></a>Kan mina befintliga ExpressRoute-kretsar stödja anslutning till Office 365-tjänster?

Ja. Din befintliga ExpressRoute-krets kan konfigureras för anslutning till Office 365-tjänster. Kontrollera att du har tillräcklig kapacitet för att ansluta till Office 365-tjänster och att du har aktiverat premiumtillägg. [Nätverksplanering och prestandajustering för Office 365](https://aka.ms/tune/) hjälper dig att planera dina anslutningsbehov. Se också [Skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Vilka Office 365-tjänster kan nås via en ExpressRoute-anslutning?

Se [sidan Office 365-url:er och IP-adressintervall](https://aka.ms/o365endpoints) för en uppdaterad lista över tjänster som stöds via ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Hur mycket kostar ExpressRoute för Office 365-tjänster?

Office 365-tjänster kräver att premiumtillägg aktiveras. Se [prisinformationssidan](https://azure.microsoft.com/pricing/details/expressroute/) för kostnader.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Vilka regioner stöds ExpressRoute för Office 365 i?

Mer information finns i [ExpressRoute-partner och platser.](expressroute-locations.md)

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kan jag komma åt Office 365 via Internet, även om ExpressRoute har konfigurerats för min organisation?

Ja. Slutpunkter för Office 365-tjänsten kan nås via Internet, även om ExpressRoute har konfigurerats för nätverket. Kontrollera med organisationens nätverksteam om nätverket på din plats är konfigurerat för att ansluta till Office 365-tjänster via ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hur kan jag planera för hög tillgänglighet för Office 365-nätverkstrafik på Azure ExpressRoute?
Se rekommendationen för [Hög tillgänglighet och redundans med Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Kan jag komma åt GCC-tjänster (Office 365 US Government Community) via en Azure US Government ExpressRoute-krets?

Ja. Slutpunkter för Office 365 GCC-tjänsten kan nås via Azure US Government ExpressRoute. Du måste dock först öppna en supportbiljett på Azure-portalen för att tillhandahålla de prefix som du tänker annonsera till Microsoft. Anslutningen till Office 365 GCC-tjänster upprättas när supportbiljetten har lösts. 

## <a name="route-filters-for-microsoft-peering"></a>Flödesfilter för Microsoft-peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Jag slår på Microsoft peering för första gången, vilka vägar kommer jag att se?

Du kommer inte att se några rutter. Du måste bifoga ett ruttfilter till din krets för att starta prefixannonser. Instruktioner finns i [Konfigurera flödesfilter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Jag vände på Microsoft peering och nu försöker jag välja Exchange Online, men det ger mig ett fel att jag inte har rätt att göra det.

När du använder flödesfilter kan alla kunder aktivera Microsoft-peering. För att kunna använda Office 365-tjänster måste du dock fortfarande få behörighet av Office 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Jag aktiverade Microsoft-peering före den 1 augusti 2017, hur kan jag dra nytta av ruttfilter?

Din befintliga krets fortsätter annonsera prefixen för Office 365. Om du vill lägga till offentliga Azure-prefixannonser för annonser över samma Microsoft-peering kan du skapa ett flödesfilter, välja de tjänster du behöver annonseras (inklusive de Office 365-tjänster du behöver) och bifoga filtret till din Microsoft-peering. Instruktioner finns i [Konfigurera flödesfilter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Jag har Microsoft peering på en plats, nu försöker jag aktivera den på en annan plats och jag ser inga prefix.

* Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla tjänstprefix annonserade via Microsoft-peering, även om vägfilter inte har definierats.

* Microsoft-peering av ExpressRoute-kretsar som är konfigurerade den 1 augusti 2017 eller senare kommer inte att ha några prefix som annonseras förrän ett flödesfilter är kopplat till kretsen. Du ser inga prefix som standard.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global räckvidd

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
