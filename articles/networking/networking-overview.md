---
title: Översikt över Azure-nätverkstjänster
description: Lär dig mer om nätverkstjänster i Azure och deras funktioner – anslutningstjänster, programskyddstjänster, programleveranstjänster och nätverksövervakning.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: c6b845eda1df39ccf5e4b2b1d6a615f3bc932b66
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474933"
---
# <a name="azure-networking-services-overview"></a>Översikt över Azure-nätverkstjänster

Nätverkstjänsterna i Azure tillhandahåller en mängd olika nätverksfunktioner som kan användas tillsammans eller separat. Klicka på någon av följande nyckelfunktioner om du vill veta mer om dem:
- [**Anslutningstjänster:**](#connect)Anslut Azure-resurser och lokala resurser med någon eller en kombination av dessa nätverkstjänster i Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Virtual Network NAT Gateway, Azure DNS, Peering Service och Azure Bastion.
- [**Programskyddstjänster**](#protect) Skydda dina program med någon eller en kombination av dessa nätverkstjänster i Azure - Private Link, DDoS-skydd, brandvägg, nätverkssäkerhetsgrupper, brandvägg för webbprogram och slutpunkter för virtuella nätverk.
- [**Tjänster för leverans av ansökningar**](#deliver) Leverera program i Azure-nätverket med någon eller en kombination av dessa nätverkstjänster i Azure - Content Delivery Network (CDN), Azure Front Door Service, Traffic Manager, Application Gateway, Internet Analyzer och Load Balancer.
- [**Nätverksövervakning**](#monitor) – Övervaka dina nätverksresurser med någon eller en kombination av dessa nätverkstjänster i Azure – Network Watcher, ExpressRoute Monitor, Azure Monitor eller VNet Terminal Access Point (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Anslutningstjänster
 
I det här avsnittet beskrivs tjänster som tillhandahåller anslutning mellan Azure-resurser, anslutning från ett lokalt nätverk till Azure-resurser och gren för att förgrena sig anslutning i Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Virtual Network NAT Gateway, Azure DNS, Azure Peering service och Azure Bastion.

|Tjänst|Varför använda?|Scenarier|
|---|---|---|
|[Virtuellt nätverk](#vnet)|Gör det möjligt för Azure-resurser att kommunicera säkert med varandra, internet och lokala nätverk.| <p>[Filtrera nätverkstrafik](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Dirigera nätverkstrafik](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Begränsa nätverksåtkomst till resurser](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Ansluta virtuella nätverk](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Utökar dina lokala nätverk till Microsoft-molnet via en privat anslutning som underlättas av en anslutningsleverantör.|<p>[Skapa och ändra en ExpressRoute-krets](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Skapa och ändra peering för en ExpressRoute-krets](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Länka ett VNet till en ExpressRoute-krets](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Konfigurera och hantera flödesfilter för ExpressRoute-kretsar](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Skickar krypterad trafik mellan ett virtuellt Azure-nätverk och en lokal plats via det offentliga Internet.|<p>[Plats-till-plats-anslutningar](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Anslutningar mellan virtuella nätverk](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Punkt-till-plats-anslutningar](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Virtuellt WAN](#virtualwan)|Optimerar och automatiserar filialanslutning till och via Azure. Azure-regioner fungerar som nav som du kan välja att ansluta dina grenar till.|<p>[Anslutning mellan plats och plats](../virtual-wan/virtual-wan-site-to-site-portal.md), [ExpressRoute-anslutningar](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Värdar DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastruktur.|<p>[Använd Azure DNS som värd för din domän](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Skapa DNS-poster för en webbapp](../dns/dns-web-sites-custom-domain.md)</p> <p>[Skapa en aliaspost för Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Skapa en aliaspost för offentlig IP-adress](../dns/tutorial-alias-pip.md)</p> <p>[Skapa en aliaspost för zonresurspost](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Konfigurera säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure-portalen via TLS. När du ansluter via Azure Bastion behöver dina virtuella datorer inte en offentlig IP-adress|<p>[Skapa en Azure Bastion-värd](../bastion/bastion-create-host-portal.md)</p><p>[Ansluta med SSH till en virtuell Linux-dator](../bastion/bastion-connect-vm-ssh.md)</p><p>[Ansluta med RDP till en Virtuell Windows-dator](../bastion/bastion-connect-vm-rdp.md)</p>|
|[NAT-gateway för virtuellt nätverk](#nat)|Skapa en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator.|<p>[Skapa en NAT-gateway](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure Peering-tjänst (förhandsversion)](#azurepeeringservice)|Samarbeta med tjänsteleverantörer för optimal och tillförlitlig routning till Microsoft-molnet via det offentliga nätverket.|<p>[Registrera Azure Peering-tjänst](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Virtuellt nätverk

Azure Virtual Network (VNet) är den grundläggande byggstenen för ditt privata nätverk i Azure. Du kan använda ett virtuella nätverk för att:
- **Kommunicera mellan Azure-resurser:** Du kan distribuera virtuella datorer och flera andra typer av Azure-resurser till ett virtuellt nätverk, till exempel Azure App Service-miljöer, Azure Kubernetes Service (AKS) och Azure Virtual Machine Scale Sets. En fullständig lista över Azure-resurser som du kan distribuera till ett virtuellt nätverk finns i [Tjänstintegration för virtuella nätverk](../virtual-network/virtual-network-for-azure-services.md).
- **Kommunicera mellan varandra:** Du kan ansluta virtuella nätverk till varandra, vilket gör att resurser i antingen virtuellt nätverk för att kommunicera med varandra, med hjälp av virtuell nätverks peering. De virtuella nätverken du ansluter kan finnas i samma, eller olika, Azure-regioner. Mer information finns i [Virtual Network peering](../virtual-network/virtual-network-peering-overview.md).
- **Kommunicera till internet:** Alla resurser i ett virtuella nätverk kan kommunicera utgående till Internet, som standard. Du kan kommunicera inkommande trafik till en resurs genom att tilldela en offentlig IP-adress eller en offentlig lastbalanserare. Du kan också använda [offentliga IP-adresser](../virtual-network/virtual-network-public-ip-address.md) eller offentliga [belastningsutjämnar för](../load-balancer/load-balancer-overview.md) att hantera dina utgående anslutningar.
- **Kommunicera med lokala nätverk:** Du kan ansluta dina lokala datorer och nätverk till ett virtuellt nätverk med HJÄLP AV [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-introduction.md).

Mer information finns i [Vad är Virtuellt Azure-nätverk?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
Med ExpressRoute kan du utöka dina lokala nätverk till Microsoft-molnet via en privat anslutning som underlättas av en anslutningsleverantör. Den här anslutningen är privat. Trafiken går inte via Internet. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och Dynamics 365.  Mer information finns i [Vad är ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway hjälper dig att skapa krypterade korslokala anslutningar till ditt virtuella nätverk från lokala platser eller skapa krypterade anslutningar mellan virtuella nätverk. Det finns olika konfigurationer tillgängliga för VPN Gateway-anslutningar, till exempel plats-till-plats, punkt-till-plats eller VNet till VNet.
Följande diagram illustrerar flera VPN-anslutningar från plats till plats till samma virtuella nätverk.

![Azure VPN Gateway-anslutningar på plats till plats](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Mer information om olika typer av VPN-anslutningar finns i [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtuellt WAN
Azure Virtual WAN är en nätverkstjänst som tillhandahåller optimerad och automatiserad filialanslutning till och via Azure. Azure-regioner fungerar som nav som du kan välja att ansluta dina grenar till. Du kan utnyttja Azure-stamnätet för att även ansluta grenar och njuta av branch-to-VNet-anslutning. Azure Virtual WAN samlar många Azure-molnanslutningstjänster, till exempel plats-till-plats-VPN, ExpressRoute, point-to-site-användar-VPN till ett enda operativt gränssnitt. Anslutning till Virtuella Azure-nätverk upprättas med hjälp av virtuella nätverksanslutningar. Mer information finns i [Vad är virtuellt WAN i Azure?](../virtual-wan/virtual-wan-about.md).

![Virtual WAN-diagram](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. Mer information finns i [Vad är Azure DNS?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion-tjänsten är en ny fullständigt plattformshanterad PaaS-tjänst som du etablerar i ditt virtuella nätverk. Det ger säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure-portalen via TLS. När du ansluter via Azure Bastion behöver dina virtuella datorer inte någon offentlig IP-adress. Mer information finns i [Vad är Azure Bastion?](../bastion/bastion-overview.md).

![Azure Bastion-arkitektur](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>NAT-gateway för virtuellt nätverk
Virtual Network NAT (översättning av nätverksadresser) förenklar internetanslutningen endast utgående för virtuella nätverk. När den konfigureras i ett undernät använder alla utgående anslutningar dina angivna statiska offentliga IP-adresser. Utgående anslutning är möjlig utan belastningsutjämnare eller offentliga IP-adresser som är direkt kopplade till virtuella datorer. Mer information finns i [Vad är NAT-gateway för virtuellt nätverk?](../virtual-network/nat-overview.md) 

![NAT-gateway för virtuellt nätverk](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Azure-peering-tjänst
Azure Peering-tjänsten förbättrar kundanslutningen till Microsofts molntjänster som Office 365, Dynamics 365, SaaS-tjänster (Software as a Service), Azure eller alla Microsoft-tjänster som är tillgängliga via det offentliga internet. Mer information finns i [Vad är Azure Peering Service?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Programskyddstjänster

I det här avsnittet beskrivs nätverkstjänster i Azure som skyddar dina nätverksresurser – Skydda dina program med hjälp av någon eller en kombination av dessa nätverkstjänster i Azure – Privat länk, DDoS-skydd, Brandvägg, Nätverkssäkerhetsgrupper, brandvägg för webbprogram och slutpunkter för virtuella nätverk.

|Tjänst|Varför använda?|Scenario|
|---|---|---|
|[DDoS-skydd](#ddosprotection) |Hög tillgänglighet för dina program med skydd mot överskjutande IP-trafikavgifter|[Hantera Azure DDoS-skydd](../virtual-network/manage-ddos-protection.md)|
|[Brandvägg för webbprogram](#waf)|<p>[Azure WAF med Application Gateway](../web-application-firewall/ag/ag-overview.md) ger regionalt skydd till enheter i offentligt och privat adressutrymme</p><p>[Azure WAF med ytterdörr](../web-application-firewall/afds/afds-overview.md) ger skydd vid nätverkskanten till offentliga slutpunkter.</p>|<p>[Konfigurera botskyddsregler](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Konfigurera anpassad svarskod](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Konfigurera IP-begränsningsregler](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Konfigurera hastighetsbegränsningsregel](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet.|<p>[Distribuera en Azure-brandvägg i ett Vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Distribuera en Azure-brandvägg i ett hybridnätverk](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrera inkommande trafik med Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Nätverkssäkerhetsgrupper](#nsg)|Fullständig detaljerad distribuerad slutnodkontroll vid VM/undernät för alla nätverkstrafikflöden|[Filtrera nätverkstrafik med hjälp av nätverkssäkerhetsgrupper](../virtual-network/tutorial-filter-network-traffic.md)|
|[Tjänstslutpunkter för virtuellt nätverk](#serviceendpoints)|Gör att du kan begränsa nätverksåtkomsten till vissa Azure-tjänstresurser till ett virtuellt nätverksundernät|[Begränsa nätverksåtkomst till PaaS-resurser](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Private Link](#privatelink)|Gör att du kan komma åt Azure PaaS Services (till exempel Azure Storage och SQL Database) och Azure värd kundägda /partnertjänster via en privat slutpunkt i ditt virtuella nätverk.|<p>[Skapa en privat slutpunkt](../private-link/create-private-endpoint-portal.md)</p><p>[Skapa en Private Link-tjänst](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) ger motåtgärder mot de mest sofistikerade DDoS-hoten. Tjänsten ger förbättrade DDoS-begränsningsfunktioner för dina program och resurser som distribueras i dina virtuella nätverk. Dessutom har kunder som använder Azure DDoS Protection tillgång till DDoS Rapid Response-stöd för att engagera DDoS-experter under en aktiv attack.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Brandvägg för webbprogram

[Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) skyddar dina webbprogram från vanliga webbinploaringar och säkerhetsproblem som SQL-insprutning och skript över flera webbplatser. Azure WAF tillhandahåller skydd utanför boxskyddet från OWASP-topp 10-sårbarheter via hanterade regler. Dessutom kan kunder också konfigurera anpassade regler, som är kundhanterade regler för att ge ytterligare skydd baserat på källans IP-intervall, och begära attribut som rubriker, cookies, formulärdatafält eller frågesträngparametrar.

Kunder kan välja att distribuera [Azure WAF med Application Gateway](../application-gateway/waf-overview.md) som ger regionalt skydd till enheter i offentliga och privata adressutrymmen. Kunder kan också välja att distribuera [Azure WAF med Ytterdörren](../frontdoor/waf-overview.md) som ger skydd vid nätverkskanten till offentliga slutpunkter.

![Brandvägg för webbaserade program](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Med Azure-brandväggen kan du centralt skapa, framtvinga och logga principer för program- och nätverksanslutning över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk. 

Mer information om Azure-brandväggen finns i [dokumentationen till Azure Firewall](../firewall/overview.md).

![Översikt över brandväggar](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Nätverkssäkerhetsgrupper
Du kan filtrera nätverkstrafik till och från Azure-resurser i ett virtuellt nätverk i Azure med en nätverkssäkerhetsgrupp. Mer information finns i [Säkerhetsöversikt](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Tjänstslutpunkter
Med tjänstslutpunkter för Virtual Network (VNet) får du ett utökat privat adressutrymme för det virtuella nätverket och identiteten för ditt VNet till Azure-tjänsterna, via en direktanslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket. Mer information finns i [slutpunkter för virtuella nätverkstjänster.](../virtual-network/virtual-network-service-endpoints-overview.md)

![Tjänstslutpunkter för virtuellt nätverk](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Med Azure Private Link](../private-link/private-link-overview.md) kan du komma åt Azure PaaS Services (till exempel Azure Storage och SQL Database) och Azure-värd för kundägda/partnertjänster via en privat slutpunkt i ditt virtuella nätverk.
Trafiken mellan det virtuella nätverket och tjänsten färdas i Microsofts stamnätsnätverk. Att exponera din tjänst för det offentliga internet är inte längre nödvändigt. Du kan skapa din egen privata länktjänst i ditt virtuella nätverk och leverera den till dina kunder.

![Översikt över privata slutpunkter](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Tjänster för leverans av ansökningar

I det här avsnittet beskrivs nätverkstjänster i Azure som hjälper till att leverera program – Network Watcher, ExpressRoute Monitor, Azure Monitor eller VNet Terminal Access Point (TAP).

|Tjänst|Varför använda?|Scenario|
|---|---|---|
|[Nätverk för innehållsleverans](#cdn)|Levererar innehåll med hög bandbredd till användarna. CDN lagrar cachelagrat innehåll på edge-servrar på POP-platser (point-of-presence) som ligger nära slutanvändare, för att minimera svarstiden|<p>[Lägga till CDN i en webbapp](../cdn/cdn-add-to-web-app.md)</p> <p>[- Komma åt lagringsblobar med en anpassad Azure CDN-domän via HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Lägga till en anpassad domän i din Azure CDN-slutpunkt](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Konfigurera HTTPS på en anpassad Azure CDN-domän](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Gör att du kan definiera, hantera och övervaka den globala routningen för din webbtrafik genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet.|<p>[Lägga till en anpassad domän i din Azure Front Door Service](../frontdoor/front-door-custom-domain.md)</p> <p>[Konfigurera HTTPS på en anpassad Front Door-domän](../frontdoor/front-door-custom-domain-https.md)</p><p>[Konfigurera princip för brandvägg för geofiltrering av webbprogram](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|Distribuerar trafik baserat på DNS till tjänster i globala Azure-regioner, samtidigt som hög tillgänglighet och svarstider ger hög tillgänglighet och svarstider|<p> [Dirigera trafik för låg latens](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Dirigera trafik till en slutpunkt för prioritet](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Styra trafiken med viktade slutpunkter](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Rutttrafik baserat på slutpunktens geografiska läge](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Dirigera trafik utifrån användarens undernät](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Lastbalanserare](#loadbalancer)|Ger regional belastningsutjämning genom att dirigera trafik över tillgänglighetszoner och till dina virtuella nätverk. Ger intern belastningsutjämning genom att dirigera trafik över och mellan dina resurser för att skapa ditt regionala program.|<p> [Belastningsutjämna Internettrafik till virtuella datorer](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Belastningsjämnad trafik över virtuella datorer i ett virtuellt nätverk](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Portera vidarebefordra trafik till en viss port på specifika virtuella datorer](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Konfigurera regler för belastningsutjämning och utgående](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar.|<p>[Dirigera webbtrafik med Azure Application Gateway](../application-gateway/quick-create-portal.md)</p><p>[Självstudiekurs: Konfigurera en programgateway med TLS-avslutning med Azure-portalen](../application-gateway/create-ssl-portal.md)</p><p>[Skapa en programgateway med webbadressbaserad omdirigering](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Nätverk för innehållsleverans
Med CDN får utvecklare en global lösning för snabb innehållsleverans med hög bandbredd till användarna eftersom innehållet cachelagras på fysiska noder på strategiska platser runt om i världen. Mer information om Azure CDN finns i [Azure Content Delivery Network](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure-klientdörrstjänst
Med Azure Front Door Service kan du definiera, hantera och övervaka global routning av din webbtrafik, genom att optimera för bästa prestanda och omedelbar global redundans för hög tillgänglighet. Med Front Door kan du transformera dina globala (för flera regioner) konsument- och företagsprogram till robusta, högpresterande och anpassade moderna program, API:er och innehåll som når en global publik med Azure. Mer information finns i [Azure Front Door](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager är en lastbalanserare för DNS-baserad trafik som hjälper dig att distribuera trafiken optimalt till tjänster i globala Azure-regioner, med hög tillgänglighet och korta svarstider. Traffic Manager innehåller en rad trafikdirigeringsmetoder för att distribuera trafik, till exempel prioritet, viktad, prestanda, geografiskt, multivärde eller undernät. Mer information om trafikroutningsmetoder finns i [Routningsmetoder för Trafikhanteraren](../traffic-manager/traffic-manager-routing-methods.md).

I följande diagram visas slutpunktsprioritetbaserad routning med Traffic Manager:

![Azure Traffic Manager 'Prioritet' trafik-routning metod](./media/networking-overview/priority.png)

Mer information om Traffic Manager finns i [Vad är Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Lastbalanserare
Azure Load Balancer ger högpresterande, låg latens Layer 4 belastningsutjämning för alla UDP och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsbalanserade slutpunkter. Du kan definiera regler för att mappa inkommande anslutningar till backend-pooldestinationer med hjälp av hälsosökningsalternativ för TCP och HTTP för att hantera tjänstens tillgänglighet. Mer information om belastningsutjämnare finns i [översiktsartikeln för belastningsutjämnaren.](../load-balancer/load-balancer-overview.md)

Följande bild visar ett Internetface-program med flera nivåer som använder både externa och interna belastningsutjämnare:

![Azure Load Balancer exempel](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway är en lastbalanserare för webbtrafik som gör det möjligt för dig att hantera trafik till dina webbappar. Det är en ADC (Application Delivery Controller) som en tjänst som erbjuder olika belastningsutjämningsfunktioner för lager 7 för dina program. Mer information finns i [Vad är Azure Application Gateway?](../application-gateway/overview.md).

I följande diagram visas url-sökvägsbaserad routning med Application Gateway.

![Exempel på programgateway](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Nätövervakningstjänster
I det här avsnittet beskrivs nätverkstjänster i Azure som hjälper dig att övervaka dina nätverksresurser – Network Watcher, ExpressRoute Monitor, Azure Monitor och Virtual Network TAP.

|Tjänst|Varför använda?|Scenario|
|---|---|---|
|[Network Watcher](#networkwatcher)|Hjälper till att övervaka och felsöka anslutningsproblem, hjälper till att diagnostisera VPN-, NSG- och routningsproblem, samla in paket på den virtuella datorn, automatiserar utlösande diagnostikverktyg med Hjälp av Azure Functions och Logic Apps|<p>[Diagnostisera problem med VM-trafikfilter](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnostisera problem med VM-routning](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Övervaka kommunikation mellan virtuella datorer](../network-watcher/connection-monitor.md)</p><p>[Diagnostisera kommunikationsproblem mellan nätverk](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Logga nätverkstrafik till och från en virtuell dator](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute-övervakare](#expressroutemonitor)|Ger realtidsövervakning av nätverksprestanda, tillgänglighet och användning, hjälper till med automatisk identifiering av nätverkstopologi, ger snabbare felisolering, upptäcker tillfälliga nätverksproblem, hjälper till att analysera historiska nätverksprestandaegenskaper, stöder flerabonnemang|<p>[Konfigurera övervakare av nätverksprestanda för ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Övervakning, mått och aviseringar i ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Hjälper dig att förstå hur dina program fungerar och identifierar proaktivt problem som påverkar dem och vilka resurser de är beroende av.|<p>[Mått och aviseringar i Traffic Manager](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Azure-diagnostik för standardbelastningsutjämning](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Övervaka Azure Firewall-loggar och mått](../firewall/tutorial-diagnostics.md)</p><p>[Övervakning och loggning i Azures brandvägg för webbaserade program](../frontdoor/waf-front-door-monitor.md)</p>|
|[TRYCK PÅ Virtuellt nätverk](#vnettap)|Tillhandahåller kontinuerlig strömning av nätverkstrafik för virtuella datorer till paketinsamlare, möjliggör lösningar för hantering av nätverks- och programprestanda och säkerhetsanalysverktyg|[Skapa en VNet TAP-resurs](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
I Azure Network Watcher finns verktyg för att övervaka, diagnostisera, visa mått samt aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk. Mer information finns i [Vad är Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute-övervakare
Mer information om hur du visar ExpressRoute-kretsmått, diagnostikloggar och aviseringar finns i [ExpressRoute-övervakning, mått och aviseringar](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor maximerar programmens tillgänglighet och prestanda genom att leverera en heltäckande lösning för att samla in, analysera och arbeta med telemetri från dina molnmiljöer och lokala miljöer. Det hjälper dig att förstå hur dina program fungerar och identifierar proaktivt problem som påverkar dem och de resurser som de förlitar sig på. Mer information finns i [Översikt över Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TRYCK PÅ Virtuellt nätverk
Med Azure-virtuellt nätverk TAP (Terminal Access Point) kan du kontinuerligt strömma din virtuella datornätverkstrafik till ett nätverkspaketinsamlare eller analysverktyg. Insamlaren eller analysverktyget tillhandahålls av en [virtuell nätverksinstallationspartner.](https://azure.microsoft.com/solutions/network-appliances/) 

Följande bild visar hur virtuellt nätverk TAP fungerar. 

![Så här fungerar virtuellt nätverk TAP](./media/networking-overview/virtual-network-tap-architecture.png)

Mer information finns i [Vad är virtuellt nätverk TAP](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Nästa steg

- Skapa ditt första virtuella nätverk och anslut några virtuella datorer till det genom att slutföra stegen i artikeln [Skapa ditt första virtuella nätverk.](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
- Anslut datorn till ett virtuella nätverk genom att slutföra stegen i [artikeln Konfigurera en anslutningsartikel för punkt till plats](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Belastningsutjämnad Internettrafik till offentliga servrar genom att slutföra stegen i artikeln [Skapa en Internet-vänd belastningsutjämnad.](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
 
 
   
