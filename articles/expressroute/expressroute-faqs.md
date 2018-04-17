---
title: Azure ExpressRoute vanliga frågor och svar | Microsoft Docs
description: ExpressRoute vanliga frågor och svar innehåller information om stöd för Azure-tjänster, kostnad, Data och anslutningar, SERVICENIVÅAVTAL, leverantörer och platser, bandbredd och ytterligare teknisk information.
documentationcenter: na
services: expressroute
author: cherylmc
manager: jeconnoc
editor: ''
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cherylmc
ms.openlocfilehash: 4c61cdc504306d4e28d1d8c03fce4e479a0bfd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="expressroute-faq"></a>Vanliga frågor och svar för ExpressRoute

## <a name="what-is-expressroute"></a>Vad är ExpressRoute?

ExpressRoute är en Azure-tjänst som låter dig skapa privata anslutningar mellan Microsoft-datacenter och den infrastruktur som är lokalt eller i en samordning anläggning. ExpressRoute-anslutningar inte gå via det offentliga Internet och ger högre säkerhet, tillförlitlighet och hastighet med lägre svarstider än vanliga anslutningar över Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Vilka är fördelarna med att använda ExpressRoute- och privata nätverksanslutningar?

ExpressRoute-anslutningar går inte via offentligt Internet. De ger högre säkerhet, tillförlitlighet och hastighet med lägre och konsekvent svarstider än vanliga anslutningar över Internet. I vissa fall kan använda ExpressRoute-anslutningar för att överföra data mellan lokala enheter och Azure kan ge betydande kostnadsfördelar.

### <a name="where-is-the-service-available"></a>Där är tjänsten?

Se den här sidan för tjänstlokalisering och tillgänglighet: [ExpressRoute partners och platser](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hur kan jag använda ExpressRoute för att ansluta till Microsoft om jag inte har partnerskap med en partner för ExpressRoute-operatör?

Du kan välja ett regionalt operatör och mark Ethernet-anslutningar till en stöds exchange-providerplatser. Du kan sedan peer med Microsoft på provider-platsen. Kontrollera det sista avsnittet i [ExpressRoute partners och platser](expressroute-locations.md) att se om tjänstleverantören finns i någon av exchange-platser. Du kan sedan beställa en ExpressRoute-krets via leverantören för att ansluta till Azure.

### <a name="how-much-does-expressroute-cost"></a>Hur mycket kostar ExpressRoute?

Kontrollera [prisinformationen](https://azure.microsoft.com/pricing/details/expressroute/) information om priser.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Om jag betala för en viss bandbredd en ExpressRoute-krets VPN-anslutningen I köpa från min nätverksleverantör måste vara samma hastighet?

Nej. Du kan köpa en VPN-anslutning med en hastighet från leverantören. Anslutningen till Azure är dock begränsad till bandbredden för ExpressRoute-krets som du köper.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Om jag betala för en viss bandbredd en ExpressRoute-krets finns möjligheten att upp till högre hastigheter vid behov?

Ja. ExpressRoute-kretsar konfigureras så att du kan burst upp till två gånger gränsen du angav för utan extra kostnad. Kontrollera med leverantören för att se om de stöder den här funktionen.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kan jag använda samma privata nätverksanslutning med virtuella nätverk och andra Azure-tjänster samtidigt?

Ja. En ExpressRoute-krets, anges en gång, kan du komma åt tjänster inom ett virtuellt nätverk och andra Azure-tjänster samtidigt. Du kan ansluta till virtuella nätverk via privat peering sökväg och till andra tjänster över offentlig peering sökvägen.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Erbjuder ExpressRoute en serviceavtalet (SLA)?

Mer information finns i [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) sidan.

## <a name="supported-services"></a>Tjänster som stöds

Har stöd för ExpressRoute [tre routningsdomäner](expressroute-circuit-peerings.md) för olika typer av tjänster.

### <a name="private-peering"></a>Privat peering

* Virtuella nätverk, inklusive alla virtuella datorer och molntjänster

### <a name="public-peering"></a>Offentlig peering

>[!NOTE]
>Microsoft-peering är det bästa sättet att få åtkomst till alla tjänster i Azure.
>

* Power BI
* Dynamics 365 för ekonomi och åtgärder (kallades Dynamics AX Online)
* De flesta av Azure-tjänster stöds. Kontrollera direkt med den tjänst som du vill använda för att verifiera stöd.<br>
  Följande tjänster stöds inte:
    * CDN
    * Visual Studio Team Services belastningen testning
    * Multifaktorautentisering
    * Traffic Manager

### <a name="microsoft-peering"></a>Microsoft-peering

* [Office 365](http://aka.ms/ExpressRouteOffice365)
* Dynamics 365 kunden Engagement-program (kallades CRM Online)
  * Dynamics 365 för försäljning
  * Dynamics 365 för kundservice
  * Dynamics 365 för fältet Service
  * Dynamics 365 för Project Service
* Med hjälp av [vidarebefordra filter](#route-filters-for-microsoft-peering), du får tillgång till samma offentliga tjänster med Microsoft-peering:
  * Power BI
  * Dynamics 365 för ekonomi och åtgärder
  * De flesta av Azure-tjänster stöds. Kontrollera direkt med den tjänst som du vill använda för att verifiera stöd.<br>
  Följande tjänster stöds inte:
    * CDN
    * Visual Studio Team Services belastningen testning
    * Multifaktorautentisering
    * Traffic Manager

## <a name="data-and-connections"></a>Data och anslutningar

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Finns det någon gräns på mängden data som jag kan överföra med ExpressRoute?

Vi Ange inte en gräns på mängden dataöverföring. Referera till [prisinformationen](https://azure.microsoft.com/pricing/details/expressroute/) information om priser för bandbredd.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Vilka anslutningshastigheter stöds av ExpressRoute?

Bandbredd-erbjudanden som stöds:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Vilka leverantörer som är tillgängliga?

Se [ExpressRoute partners och platser](expressroute-locations.md) lista över leverantörer och platser.

## <a name="technical-details"></a>Teknisk information

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Vilka är de tekniska kraven för att ansluta min lokala plats till Azure?

Se [ExpressRoute förutsättningssidan](expressroute-prerequisites.md) krav.

### <a name="are-connections-to-expressroute-redundant"></a>Är anslutningar till ExpressRoute redundant?

Ja. Varje ExpressRoute-kretsen har ett redundant par mellan anslutningar som konfigurerats för att tillhandahålla hög tillgänglighet.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Kommer det att förlora anslutningen om en av länkarna ExpressRoute misslyckas?

Om en av anslutningarna mellan misslyckas kommer du inte förlora anslutningen. En redundant anslutning är tillgänglig för att stödja belastningen på nätverket och ge hög tillgänglighet för ExpressRoute-kretsen. Dessutom kan du skapa en anslutning i en annan peering plats att uppnå krets nivå återhämtning.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hur jag för att säkerställa hög tillgänglighet i ett virtuellt nätverk som är anslutna till ExpressRoute?

Du kan uppnå hög tillgänglighet genom att ansluta ExpressRoute-kretsar i olika peering platser (t.ex. Singapore, Singapore2) till det virtuella nätverket. Om en ExpressRoute-krets kraschar växlar över anslutning till en annan ExpressRoute-krets. Som standard är trafik som lämnar det virtuella nätverket vidarebefordras baserat på lika kostnaden Multipath routning ECMP (). Du kan använda anslutningen vikt för att föredra en krets till en annan. Se [optimera ExpressRoute routning](expressroute-optimize-routing.md) för mer information om anslutning vikt.

### <a name="onep2plink"></a>Om jag vill inte samordnas på en exchange i molnet och leverantören erbjuder Point-to-point-anslutning, behöver jag beställa två fysiska anslutningar mellan min lokala nätverk och Microsoft?

Om leverantören kan upprätta två Ethernet virtuella kretsar över den fysiska anslutningen, behöver du bara en fysisk anslutning. Den fysiska anslutningen (till exempel en optical fiber) avslutas på ett lager 1 (L1)-enhet (se bilden). De två virtuella Ethernet-kretsarna märks med olika VLAN-ID, ett för den primära kretsen och ett för sekundärt. Dessa VLAN-ID som anges i yttre 802.1Q Ethernet-huvudet. Inre 802.1Q Ethernet-huvudet (visas inte) mappas till en specifik [ExpressRoute routningsdomän](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kan jag utöka en min VLAN till Azure med hjälp av ExpressRoute?

Nej. Vi stöder inte tillägg för layer-2 anslutningen till Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Kan jag har fler än en ExpressRoute-krets i min prenumeration?

Ja. Du kan ha fler än en ExpressRoute-krets i din prenumeration. Standardgränsen är inställt på 10. Du kan kontakta Microsoft Support om du vill öka gränsvärdet, om det behövs.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kan jag ha ExpressRoute-kretsar från olika leverantörer?

Ja. Du kan ha ExpressRoute-kretsar med många leverantörer. Varje ExpressRoute-kretsen är associerad med endast en leverantör. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-eg-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Det finns två ExpressRoute peering platser i samma metro, t.ex. Singapore och Singapore2. Vilka peeringplatsen ska välja att skapa min ExpressRoute-krets?
Om tjänstleverantören erbjuder ExpressRoute på båda platser kan du arbeta med leverantören av och välj någon av platsernas att ställa in ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Kan jag har flera ExpressRoute-kretsar i samma metro? Kan jag koppla dem till samma virtuella nätverk?

Ja. Du kan ha flera ExpressRoute-kretsar med samma eller olika leverantörer. Om metro har flera platser för ExpressRoute-peering och kretsarna skapas på olika peering platser, kan du länka dem till samma virtuella nätverk. Om kretsarna som skapas på samma peering plats, kan du länka dem till samma virtuella nätverk.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hur ansluter mitt virtuella nätverk till en ExpressRoute-krets

Stegen är:

* Upprätta en ExpressRoute-krets och ha tjänstleverantör aktivera den.
* Du eller providern, måste konfigurera BGP peering (s).
* Länka det virtuella nätverket till ExpressRoute-kretsen.

Mer information finns i [kretsetablering och kretsstatus i ExpressRoute-arbetsflöden](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Finns det anslutningen gränser för min ExpressRoute-krets?

Ja. Den [ExpressRoute partners och platser](expressroute-locations.md) artikeln ger en översikt av anslutning gränser för en ExpressRoute-krets. Anslutning för en ExpressRoute-krets är begränsad till en enda geopolitiska region. Anslutningen kan utökas så att de korsar geopolitiska regioner genom att aktivera funktionen ExpressRoute premium.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kan jag länkar till mer än ett virtuellt nätverk till en ExpressRoute-krets?

Ja. Du kan ha upp till 10 virtuella nätverk anslutningar på en standard ExpressRoute-krets och upp till 100 i en [premium ExpressRoute-krets](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Jag har flera Azure-prenumerationer som innehåller virtuella nätverk. Kan jag ansluta virtuella nätverk som finns i separata prenumerationer på en enda ExpressRoute-krets?

Ja. Du kan tillåta upp till 10 andra Azure-prenumerationer att använda en enda ExpressRoute-krets. Den här gränsen kan ökas genom att aktivera funktionen ExpressRoute premium.

Mer information finns i [dela en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Jag har flera Azure-prenumerationer som är kopplade till olika Azure Active Directory-klienter eller Enterprise-avtal registreringar. Kan jag ansluta virtuella nätverk som finns i separata klienter och registreringar till en enda ExpressRoute-krets inte i samma klient eller registrering?

Ja. ExpressRoute tillstånd kan sträcka sig över prenumeration, klient och registrering av gränser utan ytterligare konfiguration krävs. 

Mer information finns i [dela en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Virtuella nätverk är anslutna till samma krets isoleras från varandra?

Nej. Alla virtuella nätverk som är kopplade till samma ExpressRoute-kretsen är en del av samma routningsdomän ur Routning och är inte isolerade från varandra. Om du behöver väg isolering, måste du skapa en separat ExpressRoute-krets.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kan jag ha ett virtuellt nätverk som är ansluten till mer än en ExpressRoute-krets?

Ja. Du kan länka ett enda virtuellt nätverk med upp till fyra ExpressRoute-kretsar. De måste beställas via fyra olika [ExpressRoute platser](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag få åtkomst till Internet från mitt virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Om du inte har annonseras standardvägar (0.0.0.0/0) eller Internet väg prefix via BGP-sessionen, kan du ansluta till Internet från ett virtuellt nätverk som är kopplad till en ExpressRoute-krets.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag blockera Internetanslutning till virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Du kan annonserar ut standardrutter (0.0.0.0/0) för att blockera alla Internet-anslutning till virtuella datorer distribueras inom ett virtuellt nätverk och skicka all trafik ut via ExpressRoute-kretsen.

Om du annonserar ut standardrutter tvinga vi trafik till tjänster som erbjuds via offentlig peering (till exempel Azure storage och SQL-databas) tillbaka till din lokala. Du måste konfigurera dina routrar för att returnera trafik till Azure via offentlig peering sökvägen eller via Internet. Om du har aktiverat en tjänstslutpunkt (förhandsversion) för tjänsten kan behöva din lokala inte trafiken till tjänsten. Trafiken fortfarande i Azure stamnät-nätverket. Läs mer om Tjänsteslutpunkter i [slutpunkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Virtuella nätverk som är kopplade till samma ExpressRoute-kretsen kan kommunicera med varandra?

Ja. Virtuella datorer i virtuella nätverk som är anslutna till samma ExpressRoute-kretsen kan kommunicera med varandra.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Kan jag använda plats-till-plats-anslutning för virtuella nätverk tillsammans med ExpressRoute?

Ja. ExpressRoute kan samexistera med plats-till-plats-VPN.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>Kan jag flytta ett virtuellt nätverk från plats-till-plats / punkt-till-plats-konfiguration för att använda ExpressRoute?

Ja. Du måste skapa en ExpressRoute-gateway i det virtuella nätverket. Det finns ett litet driftstopp som är associerade med processen.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Varför är det en offentlig IP-adress som är associerade med ExpressRoute-gateway på ett virtuellt nätverk?

Den offentliga IP-adressen används för interna hantering och utgör inte en sårbart för det virtuella nätverket.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>Vad behöver jag att ansluta till Azure storage via ExpressRoute?

Du måste upprätta en ExpressRoute-krets och konfigurera flöden för offentlig peering.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Finns det någon gräns för antalet vägar som jag kan annonsera?

Ja. Vi kan acceptera upp till 4000 route-prefix för privat peering och 200 för offentlig peering och Microsoft-peering. Du kan öka till 10 000 vägar för privat peering om du aktiverar funktionen ExpressRoute premium.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Finns det några begränsningar för IP-adressintervall som jag kan annonsera över BGP-sessionen?

Vi tar inte emot privata prefix (RFC1918) i den offentliga och Microsoft BGP-peeringsessionen.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Vad händer om jag överskrider BGP begränsar?

BGP-sessioner kommer att tas bort. De kommer att återställas när antalet prefix hamnar under gränsen.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Vad är driftstid ExpressRoute BGP? Kan den justeras?

Tid för bevarande är 180. Keep-alive-meddelanden skickas var 60: e sekund. Dessa är fasta inställningar på Microsoft-sida som inte kan ändras. Det är möjligt att konfigurera olika timers och parametrarna BGP-sessionen förhandlas därefter.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Jag kan inte aktivera Windows som körs på min Azure Virtual Machines när jag annonserar standardvägen (0.0.0.0/0) till min virtuella nätverk. Hur ska jag åtgärda detta?

Följande steg hjälpa Azure att identifiera aktiveringsbegäran:

1. Upprätta offentlig peering för ExpressRoute-kretsen.
2. Utföra en DNS-sökning och hitta IP-adressen för **kms.core.windows.net**
3. Nyckelhanteringstjänsten måste identifiera att aktiveringsbegäran kommer från Azure och kontrollera begäran. Utför något av följande tre uppgifter:

   * Vidarebefordra trafik till IP-adressen som du hämtade i steg 2 till Azure via offentlig peering i ditt lokala nätverk.
   * Har din anmärkningar providern hår-PIN-kod trafik tillbaka till Azure via offentlig peering.
   * Skapa en användardefinierad väg som pekar på IP-adress som har Internetåtkomst som nästa hopp och tillämpa det på adressundernät där de virtuella datorerna är.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Kan jag ändra bandbredden för en ExpressRoute-krets?

Ja, kan du försöka att öka bandbredden för ExpressRoute-krets i Azure-portalen eller med hjälp av PowerShell. Om det finns kapacitet på den fysiska porten där kretsen har skapats, lyckas ändringen. 

Om din ändring misslyckas, innebär antingen det finns inte tillräckligt med kapacitet kvar på den aktuella porten och du behöver skapa en ny ExpressRoute-krets med högre bandbredd eller att det inte finns någon ytterligare kapacitet på den platsen, i vilket fall du inte att öka den bandbredd. 

Du måste även ha uppföljning med anslutningsleverantören för uppdatering av begränsningar i sina nätverk som stöd för ökad bandbredd. Du kan emellertid minska bandbredden för ExpressRoute-kretsen. Du måste skapa en ny ExpressRoute-krets med låg bandbredd och ta bort den gamla kretsen.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hur ändrar bandbredden för en ExpressRoute-krets?

Du kan uppdatera bandbredden för ExpressRoute-kretsen med hjälp av REST API eller PowerShell-cmdlet.

## <a name="expressroute-premium"></a>ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>Vad är premium ExpressRoute?

ExpressRoute premium är en samling av följande funktioner:

* Ökat routning tabell från 4000 vägar till 10 000 vägar för privat peering.
* Ökat antal Vnet som kan anslutas till ExpressRoute-kretsen (standardvärdet är 10). Mer information finns i [ExpressRoute gränser](#limits) tabell.
* Anslutningen till Office 365 och Dynamics 365.
* Globala anslutningar via Microsoft Kärnnätverket. Du kan nu koppla ett virtuellt nätverk i ett geopolitiska region med en ExpressRoute-krets i en annan region.<br>
    **Exempel:**

    *  Du kan länka ett VNet som skapats i västra Europa till en ExpressRoute-krets som skapats i Silicon dal. 
    *  På offentlig peering annonseras prefix från andra geopolitiska regioner så att du kan ansluta till, exempelvis SQL Azure i västra Europa från en krets i Silicon dal.


### <a name="limits"></a>Hur många Vnet kan jag länka till en ExpressRoute-krets om du har aktiverat ExpressRoute premium?

Följande tabeller visar gränserna som ExpressRoute och antalet Vnet per ExpressRoute-krets:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hur aktiverar ExpressRoute premium?

ExpressRoute premium-funktioner kan aktiveras när funktionen är aktiverad och kan stängas av genom att uppdatera tillståndet krets. Du kan aktivera ExpressRoute premium vid krets skapandet eller kan anropa REST-API / PowerShell-cmdlet.

### <a name="how-do-i-disable-expressroute-premium"></a>Hur inaktiverar ExpressRoute premium?

Du kan inaktivera ExpressRoute premium genom att anropa REST-API eller PowerShell-cmdlet. Du måste se till att du har skalats anslutningen behöver uppfyller gränserna som standard innan du inaktiverar ExpressRoute premium. Om din användning skalas utöver gränserna som standard, misslyckas denna begäran att inaktivera ExpressRoute premium.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kan jag välja funktioner som jag vill funktionsuppsättningen premium?

Nej. Du kan inte välja funktionerna. Vi aktiverar alla funktioner när du aktiverar ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Hur mycket kostar ExpressRoute premium?

Referera till [prisinformationen](https://azure.microsoft.com/pricing/details/expressroute/) för kostnaden.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Betalar jag för ExpressRoute premium utöver standard ExpressRoute avgifter?

Ja. ExpressRoute premium avgifter gäller ovanpå ExpressRoute-kretsen avgifter och kostnader som krävs av providern för anslutningen.

## <a name="expressroute-for-office-365-and-dynamics-365"></a>ExpressRoute för Office 365 och Dynamics 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-dynamics-365"></a>Hur skapar jag en ExpressRoute-krets att ansluta till Office 365-tjänster och Dynamics 365?

1. Granska de [ExpressRoute förutsättningssidan](expressroute-prerequisites.md) att se till att du uppfyller kraven.
2. För att säkerställa att din anslutningsbehov är uppfyllda, granska listan över leverantörer och platser i den [ExpressRoute partners och platser](expressroute-locations.md) artikel.
3. Planera dina kapacitetskrav genom att granska [planering och prestandajustering för Office 365](http://aka.ms/tune/).
4. Följ stegen i arbetsflöden för att konfigurera anslutningar [kretsetablering och kretsstatus i ExpressRoute-arbetsflöden](expressroute-workflows.md).

> [!IMPORTANT]
> Kontrollera att du har aktiverat ExpressRoute premium tillägg när du konfigurerar anslutningen till Office 365-tjänster och Dynamics 365.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-dynamics-365"></a>Behöver jag aktivera offentlig Azure-peering att ansluta till Office 365-tjänster och Dynamics 365?

Nej, du behöver bara aktivera Microsoft-Peering. Autentiseringstrafik till Azure AD skickas via Microsoft-Peering. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>Mina befintliga ExpressRoute-kretsar stöder anslutning till Office 365-tjänster och Dynamics 365?

Ja. Befintlig ExpressRoute-krets kan konfigureras för att stödja anslutning till Office 365-tjänster. Kontrollera att du har tillräcklig kapacitet för att ansluta till Office 365-tjänster och att du har aktiverat premium-tillägg. [Nätverksplanering och prestandajustering för Office 365](http://aka.ms/tune/) hjälper dig planera anslutningen måste. Se även [skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Vilka Office 365 tjänster kan nås via en ExpressRoute-anslutning?

Referera till [Office 365-URL: er och IP-adressintervall](http://aka.ms/o365endpoints) sida för en uppdaterad lista över tjänster som stöds via ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-and-dynamics-365-cost"></a>Hur mycket kostar ExpressRoute för Office 365-tjänster och Dynamics 365 kostnaden?

Office 365-tjänster och Dynamics 365 kräver premium-tillägg som ska aktiveras. Finns det [informationssida med priser](https://azure.microsoft.com/pricing/details/expressroute/) för kostnader.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Vilka regioner som stöds ExpressRoute för Office 365 i?

Se [ExpressRoute partners och platser](expressroute-locations.md) information.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kan jag använda Office 365 via Internet, även om ExpressRoute har konfigurerats för min organisation?

Ja. Office 365 slutpunkter kan nås via Internet, även om ExpressRoute har konfigurerats för ditt nätverk. Kontakta din organisations nätverk team om nätverket på din plats har konfigurerats för att ansluta till Office 365-tjänster via ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Hur kan jag planera för hög tillgänglighet för Office 365-nätverkstrafik på Azure ExpressRoute?
Se rekommendation för [hög tillgänglighet och redundans med Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Kan jag använda Office 365 US Government Community (GCC) tjänster via en Azure US Government ExpressRoute-krets?

Ja. Office 365 GCC slutpunkter kan nås via Azure US Government expressroute har skapats. Men måste du först öppna ett supportärende i Azure portal för att ange de prefix som du avser att annonsera till Microsoft. Anslutningen till Office 365 GCC tjänster upprättas när supportärende är löst. 

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>Dynamics 365 för åtgärder (kallades Dynamics AX Online) tillgänglig via en ExpressRoute-anslutning?

Ja. [Dynamics 365 för åtgärder](https://www.microsoft.com/dynamics365/operations) finns på Azure. Du kan aktivera offentlig Azure-peering i ExpressRoute-krets att ansluta till den.

## <a name="route-filters-for-microsoft-peering"></a>Filter för routning för Microsoft-peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Jag aktivera Microsoft-peering för första gången, vilka vägar visas?

Alla vägar visas inte. Du måste koppla ett filter för vägen till kretsen att starta prefix annonser. Instruktioner finns i [konfigurera route filter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Jag aktiverade för Microsoft-peering och nu jag försöker markera Exchange Online, men det ge mig ett fel som jag inte är behörig att göra detta.

När du använder filter routning, kan en kund aktivera Microsoft-peering. Men för förbrukning av Office 365-tjänster behöver du fortfarande få behörighet av Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Behöver jag få behörighet för att aktivera Dynamics 365 över Microsoft-peering?

Nej, du behöver inte tillståndet för Dynamics 365. Du kan skapa en regel och välja Dynamics 365 community utan behörighet.

### <a name="i-enabled-microsoft-peering-prior-to-august-1st-2017-how-can-i-take-advantage-of-route-filters"></a>Jag har aktiverat Microsoft peering före den 1 augusti 2017 hur kan jag dra nytta av filter Routning?

Befintliga kretsen fortsätter annonserar prefix för Office 365 och Dynamics 365. Om du vill lägga till Azure offentliga prefix annonser över samma Microsoft peering kan du skapa ett filter för vägen, Välj de tjänster du behöver annonserade (inklusive Office 365-tjänster som du behöver och Dynamics 365) och bifoga filtret i Microsoft peering. Instruktioner finns i [konfigurera route filter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Jag har Microsoft peering på en plats, nu jag försöker aktivera det på en annan plats och jag ser inte alla prefix.

* Microsoft-peering i ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service prefix annonserade via Microsoft peering, även om filter routning inte har definierats.

* Microsoft-peering i ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett filter för flödet är kopplat till kretsen. Inget prefix visas som standard.
