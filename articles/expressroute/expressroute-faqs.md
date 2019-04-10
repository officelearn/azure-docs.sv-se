---
title: Vanliga frågor och svar – Azure ExpressRoute | Microsoft Docs
description: ExpressRoute vanliga frågor och svar innehåller information om stöd för Azure-tjänster, kostnad, Data och anslutningar, serviceavtal, leverantörer och platser, bandbredd och ytterligare teknisk information.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: f3f013f2e3090b54846ebba94ef54506275d6311
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282873"
---
# <a name="expressroute-faq"></a>Vanliga frågor och svar för ExpressRoute

## <a name="what-is-expressroute"></a>Vad är ExpressRoute?

ExpressRoute är en Azure-tjänst som låter dig skapa privata anslutningar mellan Microsofts datacenter och infrastruktur som finns lokalt eller i en delade miljö. ExpressRoute-anslutningar inte går via det offentliga Internet och är högre säkerhet, tillförlitlighet och hastighet med kortare svarstider än vanliga anslutningar via Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Vilka är fördelarna med att använda ExpressRoute och privata nätverksanslutningar?

ExpressRoute-anslutningar går inte via offentligt Internet. De ger högre säkerhet, tillförlitlighet och hastighet, med lägre och konsekvent svarstider än vanliga anslutningar via Internet. I vissa fall kan använda ExpressRoute-anslutningar för att överföra data mellan lokala enheter och Azure kan ge betydande kostnadsfördelar.

### <a name="where-is-the-service-available"></a>Där är tjänsten?

Se den här sidan för tjänstlokalisering och tillgänglighet: [ExpressRoute-partner och platser](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hur kan jag använda ExpressRoute för att ansluta till Microsoft om jag inte har partnerskap med en ExpressRoute-operatör-partner?

Du kan välja ett regionalt flygbolag och hamnar Ethernet-anslutningar till en av stöds exchange providerplatser. Du kan sedan peerkopplas med Microsoft på providerplatsen. Kontrollera det sista avsnittet i [ExpressRoute-partner och platser](expressroute-locations.md) att se om din tjänstleverantör finns på någon av exchange-platser. Du kan sedan beställa en ExpressRoute-krets via tjänstleverantör för att ansluta till Azure.

### <a name="how-much-does-expressroute-cost"></a>Hur mycket kostar ExpressRoute?

Kontrollera [prisinformation](https://azure.microsoft.com/pricing/details/expressroute/) information om priser.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Om jag betalar för en ExpressRoute-krets för en viss bandbredd kan jag köpa från min nätverkstjänstleverantör VPN-anslutningen måste vara samma hastighet?

Nej. Du kan köpa en VPN-anslutning av alla från din tjänstprovider. Anslutningen till Azure är dock begränsad till ExpressRoute-kretsens bandbredd som du köper.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Om jag betala för en ExpressRoute-krets för en viss bandbredd finns möjlighet att utöka upp till högre hastigheter vid behov?

Ja. ExpressRoute-kretsar har konfigurerats så att du kan tillhandahålla upp till två gånger den Bandbreddsgräns du skaffat utan ytterligare kostnad. Kontrollera med din tjänstleverantör för att se om de stöder denna funktion.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kan jag använda samma privat nätverksanslutning med virtuellt nätverk och andra Azure-tjänster samtidigt?

Ja. En ExpressRoute-krets när har konfigurerat, kan du komma åt tjänster inom ett virtuellt nätverk och andra Azure-tjänster samtidigt. Du kan ansluta till virtuella nätverk över den privata peering-sökvägen och till andra tjänster över med Microsofts peeringsökväg.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Erbjuder ett serviceavtal (SLA) i ExpressRoute?

Mer information finns i den [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) sidan.

## <a name="supported-services"></a>Tjänster som stöds

ExpressRoute stöder [tre routningsdomäner](expressroute-circuit-peerings.md) för olika typer av tjänster.

### <a name="private-peering"></a>Privat peering

* Virtuella nätverk, inklusive alla virtuella datorer och molntjänster

### <a name="public-peering"></a>Offentlig peering

>[!NOTE]
>Offentlig peering har inaktiverats på nya ExpressRoute-kretsar. Azure-tjänster är tillgängliga på Microsoft-peering.
>

* Power BI
* Dynamics 365 för finans och åtgärder (tidigare kallat Dynamics AX Online)
* De flesta av de Azure-tjänsterna stöds. Kontrollera direkt med den tjänst som du vill använda för att verifiera support.<br><br>
  **Följande tjänster stöds inte**:
    * CDN
    * Multifaktorautentisering
    * Traffic Manager

### <a name="microsoft-peering"></a>Microsoft-peering

* [Office 365](https://aka.ms/ExpressRouteOffice365)
* Dynamics 365 
* Power BI
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (globala tjänster för Azure-community)
* De flesta av de Azure-tjänsterna stöds. Kontrollera direkt med den tjänst som du vill använda för att verifiera support.<br><br>**Följande tjänster stöds inte**:
    * CDN
    * Multifaktorautentisering
    * Traffic Manager

## <a name="data-and-connections"></a>Data och anslutningar

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Finns det någon gräns för mängden data som kan överföra med ExpressRoute?

Vi anger inte en gräns för mängden data som överförs. Referera till [prisinformation](https://azure.microsoft.com/pricing/details/expressroute/) information om bandbreddsavgifter.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Vilka anslutningshastigheter stöds av ExpressRoute?

Bandbredd erbjudanden som stöds:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Vilka leverantörer som är tillgängliga?

Se [ExpressRoute-partner och platser](expressroute-locations.md) för listan över leverantörer och platser.

## <a name="technical-details"></a>Teknisk information

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Vilka är de tekniska kraven för att ansluta min lokala plats till Azure?

Se [ExpressRoute förutsättningssidan](expressroute-prerequisites.md) krav.

### <a name="are-connections-to-expressroute-redundant"></a>Är anslutningar till ExpressRoute redundant?

Ja. Varje ExpressRoute-krets har ett redundant par med mellan anslutningar som konfigurerats för att tillhandahålla hög tillgänglighet.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Kommer jag att förlora anslutningen om någon av länkarna ExpressRoute misslyckas?

Du kommer inte att förlora anslutningen om en av flera anslutningar misslyckas. En redundant anslutning är tillgänglig för att stödja belastningen på nätverket och ge hög tillgänglighet för ExpressRoute-kretsen. Du kan också skapa en krets i en annan peering plats att uppnå återhämtning krets på servernivå.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hur jag för att säkerställa hög tillgänglighet i ett virtuellt nätverk som är anslutet till ExpressRoute?

Du kan uppnå hög tillgänglighet genom att ansluta ExpressRoute-kretsar i olika peering-platser (till exempel Singapore, Singapore2) till ditt virtuella nätverk. Om en ExpressRoute-krets kraschar, misslyckas anslutningen över till en annan ExpressRoute-krets. Som standard dirigeras trafik som lämnar det virtuella nätverket baserat på lika med kostnaden Multipath routning (ECMP). Du kan använda Anslutningsvikten för att föredra en kanal till en annan. Mer information finns i [optimera ExpressRoute-routning](expressroute-optimize-routing.md).

### <a name="onep2plink"></a>Om jag inte är samordnad i ett molnutbyte och Min-leverantör erbjuder Point-to-point-anslutning, måste jag beställa två fysiska anslutningar mellan min lokala nätverk och Microsoft?

Om din tjänstleverantör kan upprätta två virtuella Ethernet-kretsar över den fysiska anslutningen, behöver du bara en fysisk anslutning. Den fysiska anslutningen (till exempel en optisk fiber) avslutas på en nivå 1 (L1)-enhet (se bilden). De två virtuella Ethernet-kretsarna märks med olika VLAN-ID, en för den primära kretsen och en för sekundärt. Dessa VLAN-ID som finns i den yttre 802.1Q Ethernet-huvudet. Den inre 802.1Q Ethernet-huvudet (visas inte) mappas till ett specifikt [ExpressRoute routningsdomän](expressroute-circuit-peerings.md).

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

Ja. Du kan ha flera ExpressRoute-kretsar med samma eller olika leverantörer. Om metro har flera ExpressRoute-peeringplatser och kretsarna som skapas på olika peering-platser, kan du länka dem till samma virtuella nätverk. Om kretsarna som skapas på samma peering plats, kan du länka dem till samma virtuella nätverk, men bara upp till 4 ExpressRoute-kretsar i varje peeringplats. Du kan till exempel välja peeringplatser ”Singapore” och ”Singapore2” och ansluta kretsar från var och en till samma virtuella nätverk. 

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hur ansluter jag mitt virtuella nätverk till en ExpressRoute-krets

De grundläggande stegen är:

* Upprätta en ExpressRoute-krets och ha tjänstleverantören aktivera den.
* Du eller providern, måste konfigurera BGP peering (s).
* Länka det virtuella nätverket till ExpressRoute-kretsen.

Mer information finns i [ExpressRoute-arbetsflöden för kretsetablering och kretstillstånd](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Finns det anslutning gränser för min ExpressRoute-krets?

Ja. Den [ExpressRoute-partner och platser](expressroute-locations.md) artikeln innehåller en översikt över anslutning gränserna för en ExpressRoute-krets. Anslutningen för en ExpressRoute-krets är begränsad till en enda geopolitisk region. Anslutningen kan utökas mellan geopolitiska regioner genom att aktivera ExpressRoute premium-funktion.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kan jag länkar till mer än ett virtuellt nätverk till en ExpressRoute-krets?

Ja. Du kan ha upp till 10 virtuella nätverk-anslutningar på en standard ExpressRoute-krets och upp till 100 på en [premium ExpressRoute-krets](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Jag har flera Azure-prenumerationer som innehåller virtuella nätverk. Kan jag ansluta virtuella nätverk som finns i olika prenumerationer till en enda ExpressRoute-krets?

Ja. Du kan länka upp till 10 virtuella nätverk i samma prenumeration som kretsen eller olika prenumerationer med hjälp av en enda ExpressRoute-krets. Den här gränsen kan ökas genom att aktivera ExpressRoute premium-funktion.

Mer information finns i [delar en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Jag har flera Azure-prenumerationer som är kopplad till olika klienter i Azure Active Directory eller Enterprise-avtal registreringar. Kan jag ansluta virtuella nätverk som finns i separata klienter och registreringar till en enda ExpressRoute-krets inte i samma klient eller registrering?

Ja. ExpressRoute-auktoriseringar kan sträcka sig över gränser för prenumerationen, klienten och registrering utan någon ytterligare konfiguration krävs. 

Mer information finns i [delar en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Virtuella nätverk är anslutna till samma krets isoleras från varandra?

Nej. Från en routning perspektiv alla virtuella nätverk som är länkade till samma ExpressRoute-kretsen är en del av samma routningsdomän och är inte isolerade från varandra. Om du behöver dirigera isolering, måste du skapa en separat ExpressRoute-krets.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kan jag ha ett virtuellt nätverk som är anslutna till flera ExpressRoute-krets?

Ja. Du kan länka ett enda virtuellt nätverk med upp till fyra ExpressRoute-kretsar. De måste sorteras via fyra olika [ExpressRoute-platser](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag ansluta till Internet från mitt virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Om du inte har annonserat standardvägar (0.0.0.0/0) eller Internet route prefix via BGP-sessionen, kan du ansluta till Internet från ett virtuellt nätverk som är länkad till en ExpressRoute-krets.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag blockera Internet-anslutning till virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Du annonserar ut standardrutter (0.0.0.0/0) för att blockera alla Internet-anslutning till virtuella datorer som distribueras inom ett virtuellt nätverk och dirigera all trafik ut via ExpressRoute-kretsen.

Om du annonserar ut standardrutter tvinga vi trafik till tjänster som erbjuds via Microsoft-peering (till exempel Azure storage och SQL DB) tillbaka till ditt lokala nätverk. Du måste konfigurera dina routrar för att returnera trafik till Azure via Microsofts peeringsökväg eller via Internet. Om du har aktiverat en tjänstslutpunkt för tjänsten kan tvingas trafik till tjänsten inte till ditt lokala nätverk. Trafiken kvar i Azure-stamnätverket. Läs mer om tjänstslutpunkter i [tjänstslutpunkter i virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Virtuella nätverk som är länkade till samma ExpressRoute-kretsen kan kommunicera med varandra?

Ja. Virtuella datorer som distribueras i virtuella nätverk som är anslutna till samma ExpressRoute-krets kan kommunicera med varandra.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Kan jag använda plats-till-plats-anslutning för virtuella nätverk tillsammans med ExpressRoute?

Ja. ExpressRoute kan samexistera med plats-till-plats-VPN. Se [konfigurera ExpressRoute och samexisterande anslutningar för plats-till-plats](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Varför finns det en offentlig IP-adress som är associerade med ExpressRoute-gateway i ett virtuellt nätverk?

Offentliga IP-adress används endast för interna och utgör inte en säkerhetsrisk för det virtuella nätverket.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Finns det någon gräns för hur många vägar som jag kan annonsera?

Ja. Vi kan acceptera upp till 4 000 prefix som vägen för privat peering och 200 för Microsoft-peering. Du kan öka detta till 10 000 vägar för privat peering om du aktiverar ExpressRoute premium-funktion.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Finns det några begränsningar för IP-adressintervall som jag kan annonsera i BGP-sessionen?

Vi accepterar inte privata-prefix (RFC1918) för Microsoft-peering BGP-sessionen.

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
* Ökat antal virtuella nätverk och Global räckvidd i ExpressRoute-anslutningar som kan aktiveras för en ExpressRoute-krets (standardvärdet är 10). Mer information finns i den [ExpressRoute gränser](#limits) tabell.
* Anslutning till Office 365 och Dynamics 365.
* Global anslutbarhet via Microsoft core nätverket. Du kan nu länka ett virtuellt nätverk i en geopolitisk region med en ExpressRoute-krets i en annan region.<br>
    **Exempel:**

    *  Du kan länka ett virtuellt nätverk som skapats i västra Europa till en ExpressRoute-krets som skapats i Silicon Valley. 
    *  På Microsoft-peering, annonseras prefix från andra geopolitiska regioner, så att du kan ansluta till, till exempel SQL Azure i västra Europa från en krets i Silicon Valley.


### <a name="limits"></a>Hur många virtuella nätverk och Global räckvidd i ExpressRoute-anslutningar kan jag aktivera på en ExpressRoute-krets om jag har aktiverat ExpressRoute premium?

Följande tabeller visar ExpressRoute-gränser och antalet virtuella nätverk och Global räckvidd i ExpressRoute-anslutningar per ExpressRoute-krets:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hur aktiverar jag ExpressRoute premium?

ExpressRoute premium-funktioner kan aktiveras när funktionen är aktiverad och kan stängas av genom att uppdatera tillståndet för kretsen. Du kan aktivera ExpressRoute premium vid tidpunkten för skapandet av kretsen eller kan anropa REST API / PowerShell-cmdlet.

### <a name="how-do-i-disable-expressroute-premium"></a>Hur inaktiverar jag ExpressRoute premium?

Du kan inaktivera ExpressRoute premium genom att anropa REST API eller PowerShell-cmdlet. Du måste se till att du har skalat anslutning behöver uppfylla standardgränserna innan du inaktiverar ExpressRoute premium. Om din användning skalas bortom standardgränserna, misslyckas denna begäran att inaktivera ExpressRoute premium.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kan jag välja de funktioner som jag vill från funktionsuppsättningen premium?

Nej. Du kan inte välja funktionerna. Vi möjliggör alla funktioner när du aktiverar ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Hur mycket kostar ExpressRoute premium?

Referera till [prisinformation](https://azure.microsoft.com/pricing/details/expressroute/) kostnad.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Måste jag betala för ExpressRoute premium utöver avgifterna för standard ExpressRoute?

Ja. ExpressRoute premium-avgifter gäller utöver avgifterna för ExpressRoute-krets och avgifter som krävs av anslutningsprovidern.

## <a name="expressroute-for-office-365"></a>ExpressRoute för Office 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services"></a>Hur skapar jag en ExpressRoute-krets för att ansluta till Office 365-tjänster?

1. Granska den [ExpressRoute förutsättningssidan](expressroute-prerequisites.md) att kontrollera att du uppfyller kraven.
2. För att säkerställa att dina anslutningar behov uppfylls, granska listan över leverantörer och platser i den [ExpressRoute-partner och platser](expressroute-locations.md) artikeln.
3. Planera dina kapacitetsbehov genom att granska [nätverksplanering och prestandajustering för Office 365](https://aka.ms/tune/).
4. Följ stegen i arbetsflöden för att konfigurera anslutningen [ExpressRoute-arbetsflöden för kretsetablering och kretstillstånd](expressroute-workflows.md).

> [!IMPORTANT]
> Kontrollera att du har aktiverat ExpressRoute premium-tillägget när du konfigurerar anslutningen till Office 365-tjänster.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>Kan min befintliga ExpressRoute-kretsar stöd för anslutning till Office 365-tjänster och Dynamics 365?

Ja. Befintliga ExpressRoute-kretsen kan konfigureras för att stödja anslutningen till Office 365-tjänster. Se till att du har tillräcklig kapacitet för att ansluta till Office 365-tjänster och att du har aktiverat premium-tillägget. [Nätverksplanering och prestandajustering för Office 365](https://aka.ms/tune/) hjälper dig att planera din anslutning behöver. Se även [skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Vilka Office 365 tjänster kan nås via en ExpressRoute-anslutning?

Referera till [Office 365-URL: er och IP-adressintervall](https://aka.ms/o365endpoints) för en uppdaterad förteckning över tjänster som stöds via ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-cost"></a>Hur mycket kostar ExpressRoute för Office 365-tjänster kostnad?

Office 365-tjänster kräver premium-tillägget är aktiverat. Se den [sidan med prisinformation](https://azure.microsoft.com/pricing/details/expressroute/) för kostnader.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Vilka regioner stöds ExpressRoute för Office 365 i?

Se [ExpressRoute-partner och platser](expressroute-locations.md) information.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kan jag komma åt Office 365 via Internet, även om ExpressRoute har konfigurerats för min organisation?

Ja. Office 365-Tjänsteslutpunkter kan nås via Internet, även om ExpressRoute har konfigurerats för ditt nätverk. Kontakta din organisations nätverksteam om nätverket på din plats är konfigurerad för att ansluta till Office 365-tjänster via ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hur kan jag planera för hög tillgänglighet för Office 365-nätverkstrafik på Azure ExpressRoute?
Visa rekommendationer för [hög tillgänglighet och redundans med Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Kan jag komma åt Office 365 US Government Community (GCC) tjänster via en Azure US Government ExpressRoute-krets?

Ja. Office 365 GCC Tjänsteslutpunkter kan nås via Azure US Government ExpressRoute. Men måste du först öppna ett supportärende på Azure portal för att tillhandahålla de prefix som du planerar att annonsera till Microsoft. Anslutningen till Office 365 GCC tjänster upprättas när supportärende har åtgärdats. 

## <a name="route-filters-for-microsoft-peering"></a>Routningsfilter för Microsoft-peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Jag är att aktivera Microsoft-peering för första gången, vilka vägar ser jag?

Du kommer inte se några vägar. Du måste koppla ett flödesfilter till kretsen att starta prefix annonser. Anvisningar finns i [konfigurera routningsfilter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Jag aktiverade för Microsoft-peering och nu försöker jag väljer Exchange Online, men det ge mig ett fel att jag inte har behörighet att utföra den.

När du använder flödesfilter kan kan alla kunder aktivera Microsoft-peering. För att använda Office 365-tjänster behöver du dock fortfarande att få behörighet av Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Behöver jag få behörighet för att aktivera Dynamics 365 via Microsoft-peering?

Nej, du behöver inte auktorisering för Dynamics 365. Du kan skapa en regel och Välj Dynamics 365-community utan behörighet.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Jag har aktiverat Microsoft-peering före den 1 augusti 2017, hur kan jag dra nytta av flödesfilter?

Din befintliga krets fortsätter annonserar prefixen för Office 365 och Dynamics 365. Om du vill lägga till Azure offentliga prefix annonser över för samma Microsoft-peering kan du skapa ett flödesfilter, Välj de tjänster du behöver annonseras (inklusive Office 365-tjänster som du behöver och Dynamics 365) och koppla filtret till ditt Microsoft- peering. Anvisningar finns i [konfigurera routningsfilter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Jag har Microsoft-peering på en plats, nu jag försöker aktivera den på en annan plats och jag ser inte alla prefix.

* Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service-prefix som annonseras via Microsoft-peering, även om flödesfilter inte har definierats.

* Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett flödesfilter är kopplad till kretsen. Inga prefix visas som standard.

## <a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]
