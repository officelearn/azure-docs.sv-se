---
title: Bästa praxis för att konfigurera nätverk för arbetsbelastningar migreras till Azure | Microsoft Docs
description: När du har migrerat till Azure, hämta Metodtips för att konfigurera nätverk för migrerade arbetsbelastningar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 1493eb6978b00771aa8ed4d8cfc28c37a9dde5b6
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139756"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>Bästa praxis för att konfigurera nätverk för arbetsbelastningar migreras till Azure

Som du planerar och utformar för migrering, förutom migrering, är en av de viktigaste stegen design och implementering av Azure-nätverk. Den här artikeln beskriver Metodtips för nätverk när du migrerar till IaaS och PaaS-implementeringar i Azure.

> [!IMPORTANT]
> Bästa praxis och yttranden som beskrivs i den här artikeln baseras på Azure-plattformen och bearbeta funktioner som är tillgängliga vid tidpunkten för skrivning. Funktioner och möjligheter ändras med tiden. Inte alla rekommendationer som kan tillämpas för din distribution, så Välj dem som fungerar för dig.


## <a name="design-virtual-networks"></a>Utforma virtuella nätverk

Azure ger virtuella nätverk (Vnet):
- Azure-resurser kommunicerar privat, direkt och säkert med varandra över virtuella nätverk.
- Du kan konfigurera slutpunkten anslutningar i virtuella nätverk för virtuella datorer och tjänster som kräver internet-kommunikation.
- Ett virtuellt nätverk är en logisk avgränsning av Azure-molnet som är dedikerad för din prenumeration.
- Du kan implementera flera virtuella nätverk inom varje Azure-prenumeration och Azure-region.
- Varje virtuellt nätverk är isolerat från andra virtuella nätverk.
- Virtuella nätverk kan innehålla privata och offentliga IP-adresser som definieras i [RFC 1918](https://tools.ietf.org/html/rfc1918), uttryckt i CIDR-notation. Offentliga IP-adresser kan inte nås direkt från internet.
- Virtuella nätverk kan ansluta till varandra med VNet-peering. Anslutna virtuella nätverk kan vara i samma eller olika regioner. Därmed kan resurser i ett virtuellt nätverk ansluta till resurser i andra virtuella nätverk.
- Som standard Azure dirigerar trafik mellan undernät i ett virtuellt nätverk, anslutna virtuella nätverk, lokala nätverk och internet.


Det finns ett antal saker som du behöver tänka på när planera din VNet-topologi, inklusive hur du ordnar IP-adress blanksteg, hur du implementerar ett nätverk av typen hub-spoke, segmentera Vnet i undernät, ställa in DNS och implementera Azure tillgänglighetszoner.

## <a name="best-practice-plan-ip-addressing"></a>Bästa praxis: Planera IP-adressering

När du skapar virtuella nätverk som en del av migreringen, är det viktigt att planera din VNet IP-adressutrymme.

- Du bör tilldela ett adressutrymme som inte är större än ett CIDR-intervall för /16 för varje virtuellt nätverk. Virtuella nätverk kan användningen av 65536 IP-adresser och tilldela ett prefix som är mindre än/16 skulle leda till förlust av IP-adresser. Det är viktigt att inte slösa bort IP-adresser, även om de finns i de privata intervall som definieras av RFC 1918.
- Virtuella nätverkets adressutrymme får inte överlappa med lokala nätverksintervall.
- Network Address Translation (NAT) bör inte användas.
- Överlappande adresser kan orsaka nätverk som inte kan anslutas och routning som inte fungerar korrekt. Om nätverk överlappar behöver du ändra designen på nätverket eller använda network address translation (NAT).

**Lära sig mer:**

- [Få en översikt](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) av virtuella Azure-nätverk.
- [Läs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq) nätverk vanliga frågor och svar.
- [Lär dig mer om](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) nätverk begränsningar.


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>Bästa praxis: Implementera en topologi för nav-eker-nätverk

En hub-spoke för nätverket isolerar arbetsbelastningar samtidigt som tjänster, till exempel identitet och säkerhet.
- Hubben är ett virtuellt Azure nätverk som fungerar som en central punkt för anslutning.
- Ekrarna är virtuella nätverk som ansluter till det virtuella hubbnätverket med hjälp av VNet-peering.
- Delade tjänster distribueras i hubben medan individuella arbetsbelastningar distribueras som ekrar. 

Tänk också på följande:
- Implementera en topologi med nav och ekrar i Azure centraliserar vanliga tjänster, till exempel anslutningar till lokala nätverk, brandväggar och isolering mellan virtuella nätverk. Det virtuella hubbnätverket ger en central plats för anslutning till lokala nätverk och en plats för värd services användning av arbetsbelastningar som finns i virtuella ekernätverken.
- En konfiguration med nav och ekrar används vanligtvis av större företag. Mindre nätverk kan du välja en enklare design att spara på kostnaderna och komplexiteten.
- Virtuella nätverk ekrar kan användas till att isolera arbetsbelastningar, med varje eker som hanteras separat från andra ekrar. Varje arbetsbelastning kan innehålla flera nivåer och flera undernät som är anslutna med Azure-belastningsutjämnare.
- NAV och ekrar virtuella nätverk kan implementeras i olika resursgrupper och även i olika prenumerationer. När du peerkoppla virtuella nätverk i olika prenumerationer kan prenumerationerna associeras för samma eller olika, Azure Active Directory (AD)-klienterna. Det möjliggör decentraliserad hantering av varje arbetsbelastning, samtidigt som tjänster i navnätverket.

![Ändringshantering](./media/migrate-best-practices-networking/hub-spoke.png)
*topologi för NAV och ekrar*

**Lära sig mer:**

- [Läs mer om](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) en topologi med nav och ekrar.
- Få nätverksrekommendationer för att köra Azure [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm#network-recommendations) och [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm#network-recommendations) virtuella datorer.
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) VNet-peering.


## <a name="best-practice-design-subnets"></a>Bästa praxis: utforma undernät

För att ge isolering inom ett virtuellt nätverk, segmentera det i en eller flera undernät och allokera en del av det virtuella nätverkets adressutrymme till varje undernät.
- Du kan skapa flera undernät inom varje virtuellt nätverk.
- Som standard nätverkstrafik Azure dirigerar mellan alla undernät i ett virtuellt nätverk.
- Undernät baserat på ditt beslut baserat på dina tekniska och organisatoriska krav.  
- Du skapar undernät med CIDR-notering.
- När du bestämmer dig för nätverkets adressintervall för undernät, är det viktigt att notera att Azure behåller fem IP-adresser från varje undernät som inte får återanvändas. Till exempel om du skapar det minsta tillgängliga undernätet/29 (med åtta IP-adresser), behåller Azure fem adresser, så du behöver bara tre användbara adresser som kan tilldelas till värdar i undernätet.
- I de flesta fall rekommenderas använda /28 som minsta undernätet.

### <a name="example"></a>Exempel

Tabellen visar ett exempel på ett virtuellt nätverk med ett adressutrymme för 10.245.16.0/20 uppdelat i undernät för en planerad migrering.

**Undernät** | **CIDR** | **Adresser** | **Använd**
--- | --- | --- | ---
DEV-FE-EUS2 | 10.245.16.0/22 | 1019 | Klientdel/virtuella datorer på webbnivå
DEV-APP-EUS2 | 10.245.20.0/22 | 1019 | App-nivå virtuella datorer
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | Virtuella

**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation) utforma undernät.
- [Lär dig hur](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) ett fiktivt företag (Contoso) förberedda sin nätverksinfrastruktur för migrering.


## <a name="best-practice-set-up-a-dns-server"></a>Bästa praxis: Konfigurera en DNS-server

Azure lägger till en DNS-server som standard när du distribuerar ett virtuellt nätverk. På så sätt kan du snabbt skapa virtuella nätverk och distribuera resurser. Den här DNS-servern tillhandahåller dock endast tjänster till resurserna på det virtuella nätverket. Om du vill ansluta flera virtuella nätverk tillsammans eller ansluta till en lokal server från virtuella nätverk behöver du ytterligare name resolution funktioner. Du kanske till exempel behöver Active Directory för DNS-namnmatchning mellan virtuella nätverk. Om du vill göra detta måste distribuera du dina egna anpassade DNS-server i Azure.

- DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till de rekursiva matchare i Azure. På så sätt kan du matcha värdnamn inom det virtuella nätverket. Exempelvis kan en domänkontrollant som kör i Azure svara på DNS-frågor för egna domäner och vidarebefordra alla frågor till Azure.
- DNS-vidarebefordran kan virtuella datorer finns i både lokala resurser (via domänkontrollanten) och Azure-tillhandahållen värdnamn (med vidarebefordraren). Åtkomst till de rekursiva matchare i Azure anges med hjälp av den virtuella IP-adressen 168.63.129.16.
- Vidarebefordran av DNS kan du även gör DNS-matchning mellan virtuella nätverk, och att lokala datorer för att matcha värdnamn som tillhandahålls av Azure.
    - För att lösa ett VM-värdnamn, DNS-server-dator måste finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värd-namnfrågor till Azure.
    - Eftersom DNS-suffix är olika i varje virtuellt nätverk kan använda du regler för villkorlig vidarebefordran för att skicka DNS-frågor till rätt VNet för matchning.
- När du använder en egen DNS-servrar, kan du ange flera DNS-servrar för varje virtuellt nätverk. Du kan också ange flera DNS-servrar per nätverksgränssnitt (för Azure Resource Manager) eller per molntjänst (för den klassiska distributionsmodellen).
- DNS-servrar som angetts för en nätverkstjänst för gränssnittet eller molnet företräde framför DNS-servrar som angetts för det virtuella nätverket.
- Du kan ange DNS-servrar för ett virtuellt nätverk och ett nätverksgränssnitt i Azure Resource Manager-distributionsmodellen, men den bästa metoden är att använda inställningen för endast virtuella nätverk.

    ![DNS-servrar](./media/migrate-best-practices-networking/dns2.png) *DNS-servrar för det virtuella nätverket*

**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) namnmatchning när du använder en egen DNS-server.
- [Lär dig mer om](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions) DNS namngivningsregler och begränsningar.


## <a name="best-practice-set-up-availability-zones"></a>Bästa praxis: Konfigurera tillgänglighetszoner

Tillgänglighetszoner öka hög tillgänglighet för att skydda dina appar och data från datacenter havererar.

- Tillgänglighetszoner är unika, fysiska platser inom en Azure-region.
- Varje zon består av en eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverkstjänster.
- För att säkerställa återhämtning, finns det minst tre separata zoner i alla aktiverade regioner.
- Fysisk avgränsning av tillgänglighetszoner inom en region skyddar program och data mot datacenterproblem.
- Zonredundant tjänster replikera dina program och data i olika tillgänglighetszoner och skydda mot enskilda felpunkter. – Azure erbjuder ett serviceavtal på 99,99% drifttid för virtuella datorer med tillgänglighetszoner.

    ![Tillgänglighetszon](./media/migrate-best-practices-networking/availability-zone.png) *tillgänglighetszon*

- Du kan planera och skapa hög tillgänglighet i din migreringsarkitektur genom att samordna beräkning, lagring, nätverk och dataresurser inom en zon och replikera dem i andra zoner. Azure-tjänster som stöd för tillgänglighetszoner indelade i två kategorier:
    - Zonindelade tjänster: du kan associera en resurs med en viss zon. För managed disks, IP-adresser för exempel virtuella datorer).
    - Zonredundant tjänster: resursen replikerar automatiskt i olika zoner. Till exempel zonredundant lagring, Azure SQL Database.
- Du kan distribuera en standard Azure belastningsutjämnade med internet-riktade arbetsbelastningar eller app-nivåerna för att ge feltolerans för zonindelade.

    ![Belastningsutjämnare](./media/migrate-best-practices-networking/load-balancer.png) *belastningsutjämnare*

**Lära sig mer:**
-   [Få en översikt](https://docs.microsoft.com/azure/availability-zones/az-overview) av tillgänglighetszoner.



## <a name="design-hybrid-cloud-networking"></a>Design hybridmolnet nätverk

Det är viktigt att ansluta lokala företagets nätverk till Azure för en lyckad migrering. Detta skapar en ständig-anslutning kallas ett hybridmoln nätverk, där erbjuds från Azure cloud till företagsanvändare. Det finns två alternativ för att skapa den här typen av nätverk:

- **Plats-till-plats-VPN:** upprättar du en plats-till-plats-anslutning mellan kompatibla lokala VPN-enheten och en Azure VPN-gateway som distribueras i ett virtuellt nätverk. Någon behörighet till den lokala resursen kan komma åt virtuella nätverk. Plats-till-plats-kommunikation skickas via en krypterad tunnel via internet. 
- **Azure ExpressRoute:** du upprätta en Azure ExpressRoute-anslutning mellan ditt lokala nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat och trafik Gå inte via internet.

**Lära sig mer:**

- [Läs mer](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn) om hybridmoln nätverk.

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>Bästa praxis: Implementera en högtillgänglig plats-till-plats-VPN

Om du vill implementera en plats-till-plats-VPN kan ställa du in en VPN-gateway i Azure.
- En VPN-gateway är en viss typ av VNet-gateway som används för att skicka krypterad trafik mellan ett virtuellt Azure nätverk och en lokal plats via det offentliga Internet.
- Du kan också använda en VPN-gateway för att skicka krypterad trafik mellan virtuella Azure-nätverk över Microsoft-nätverket.
- Varje virtuellt nätverk kan bara ha en VPN-gateway.
- Du kan skapa flera anslutningar till samma VPN-gateway. När du skapar flera anslutningar, delar alla VPN-tunnlar den tillgängliga bandbredden.
- Varje Azure VPN-gateway består av två instanser i en aktiv-standby-konfiguration.
    - För planerat underhåll eller unplaned störningar i den aktiva instansen, redundans och väntelägesinstansen tar över automatiskt och återupptar plats-till-plats eller VNet-till-VNet-anslutning. 
    - Övergången orsakar ett kort avbrott.
    - Vid ett planerat underhåll återställs anslutningen inom 10 till 15 sekunder.
    - Oplanerat avbrott tar är återställa anslutningen för längre tid, cirka en 1,5 minuter i värsta fall.
    - Punkt-till-plats (P2S) VPN-klientanslutningar till gatewayen kopplas och användarna måste ansluta på nytt från klientdatorer.

När du konfigurerar en plats-till-plats-VPN kan du göra följande:
 - Du behöver ett virtuellt nätverk som adressintervall inte överlappar det lokala nätverket som det virtuella privata nätverket ansluter.
 - Du skapar ett gateway-undernät i nätverket.
 - Du skapar en VPN-gateway, ange gateway-typ (VPN) och om gatewayen är principbaserad eller routningsbaserad. En RouteBased VPN rekommenderas som mer kapacitet och framtidssäkrar.
 - Du skapar en lokal nätverksgateway på plats och konfigurera den lokala VPN-enheten. 
 - Du skapar en växling vid fel plats-till-plats VPN-anslutning mellan VNet-gateway och den lokala enheten. Om du använder ruttbaserad VPN får aktivt-passivt eller aktiv-aktiv-anslutningar till Azure. Ruttbaserad också stöder både plats-till-plats (från valfri dator) och anslutningar för punkt-till-plats (från en enda dator) samtidigt.
 - Du kan ange vilken gateway-SKU som du vill använda. Detta beror på din arbetsbelastningskraven, genomflöden, funktioner och serviceavtal.
 - BGP (border gateway protocol) är en valfri funktion som du kan använda med Azure ExpressRoute och routningsbaserade VPN-gatewayer för att sprida lokala BGP-vägar till dina virtuella nätverk.

![VPN](./media/migrate-best-practices-networking/vpn.png)
*plats-till-plats-VPN*
 
**Lära sig mer:**

- [Granska](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) kompatibla lokala VPN-enheter.
- [Få en översikt](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateway) VPN-gatewayer.
- [Lär dig mer om](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable) VPN-anslutningar med hög tillgänglighet.
- [Lär dig mer om](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) planerar och utformar en VPN-gateway.
- [Granska](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) VPN gateway-inställningar.
- [Granska](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) gateway SKU: er.
- [Läs mer om](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview) konfigurerar BGP med Azure VPN-gatewayer.


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>Bästa praxis: Konfigurera en gateway för VPN-gatewayer

När du skapar en VPN-gateway i Azure måste du använda ett särskilt undernät med namnet GatewaySubnet. När du skapar det här undernätet Obs dessa metodtips:

- Prefixlängden för gateway-undernätet kan ha maximalt prefixlängden 29 (till exempel 10.119.255.248/29). Aktuella rekommendationen är att du använder prefixlängden 27 (till exempel 10.119.255.224/27).
- När du definierar adressutrymmet för gatewayundernätet kan använda den sista delen av virtuella nätverkets adressutrymme.
- När du använder Azure-GatewaySubnet, aldrig distribuera virtuella datorer eller andra enheter, till exempel Application Gateway till gateway-undernätet.
- Tilldela inte en nätverkssäkerhetsgrupp (NSG) till det här undernätet. Det gör att gatewayen slutar att fungera.

**Lära sig mer:**
- [Använd det här verktyget](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed) fastställa IP-adressutrymme.

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>Bästa praxis: Implementera Azure virtuellt WAN-nätverk för avdelningskontor

Flera VPN-anslutningar är Azure virtuellt WAN-nätverk en nätverkstjänst som tillhandahåller optimerade och automatiserade gren till grenen anslutningar via Azure.
- Med Virtual WAN kan du ansluta och konfigurera platsspecifika enheter så att de kommunicerar med Azure. Detta kan göras manuellt eller genom att använda önskade providern enheter via en partner som virtuellt WAN-nätverk.
- Om du använder prioriterade providern enheter får enkel användning, anslutningar och konfiguration hantering.
- Azure WAN inbyggda instrumentpanelen innehåller omedelbara felsökning insikter som sparar tid och ger ett enkelt sätt att spåra storskaliga plats-till-plats-anslutning. 

**Läs mer:**
[Lär dig mer om](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) Azure virtuellt WAN-nätverk.

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>Bästa praxis: Implementera ExpressRoute för verksamhetskritiska kritiska anslutningar

Tjänsten Azure ExpressRoute kan du utöka din lokala infrastruktur till Microsoft cloud genom att skapa privata anslutningar mellan virtuella Azure-datacentret och lokala nätverk.
- ExpressRoute-anslutningar kan vara alla-till-alla (IP VPN) nätverk, ett mellan punkter med Ethernet-nätverk eller via en anslutningsleverantör. De går inte via offentliga internet.
- ExpressRoute-anslutningar har högre säkerhet, tillförlitlighet och högre hastigheter (upp till 10 Gbit/s), tillsammans med konsekvent svarstid.
- ExpressRoute är användbart för virtuella datacenter som kunder kan få fördelarna med regler för efterlevnad som är associerade med privata anslutningar.
- Du kan ansluta direkt till Microsoft-routrarna vid 100Gbps för större bandbreddsbehov ExpressRoute Direct.
- ExpressRoute använder BGP för att utbyta vägar mellan lokala nätverk, Azure-instanser och Microsoft offentliga adresser.

Distribuera ExpressRoute-anslutningar vanligtvis innebär att engagera sig i en ExpressRoute-leverantör. För en Snabbstart, det är vanligt att först använda en plats-till-plats-VPN för att upprätta en anslutning mellan virtuella datacenter och lokala resurser och migrera sedan till en ExpressRoute-anslutning när en fysisk gränssnittet med din tjänstleverantör är upprätta.

**Lära sig mer:**
- [Få en översikt](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) över ExpressRoute.
- [Lär dig mer om](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about) ExpressRoute direkt.

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>Bästa praxis: Optimera ExpressRoute-routning med BGP-communities

När du har flera ExpressRoute-kretsar måste ha du mer än en sökväg för att ansluta till Microsoft. Därför kan icke-optimal routning uppstå och trafiken kan ta en längre sökväg till Microsoft, och Microsoft till ditt nätverk. Ju längre nätverkssökvägen är, desto högre latens. Svarstiden har direkt inverkan på prestanda och användarupplevelse i appen.

#### <a name="example"></a>Exempel

Låt oss ta ett exempel.

- Du har två kontor i USA, ett i Los Angeles och ett i New York.
- Dina kontor är anslutna i WAN, vilket kan vara antingen ditt eget stamnät eller leverantörens IP VPN.
- Du har två ExpressRoute-kretsar, en i USA, västra och en i USA, östra som även är anslutna i WAN-nätverket. Naturligtvis har du två sökvägar för att ansluta till Microsoft-nätverket.


 
**Problemet** nu anta att du har en Azure-distribution (till exempel Azure App Service) i både västra och östra USA.
- Du vill att användare i varje office för att komma åt sin närmaste Azure-tjänster för en optimal upplevelse.
- Därför att du vill ansluta användare i Los Angeles till Azure i västra USA och användare i New York till Azure i östra USA.
- Detta fungerar för ostkustanvändarna, men inte för västra. Problemet är följande:
    - På varje ExpressRoute-krets annonserar vi både prefix i Azure i östra USA (23.100.0.0/16) och Azure i västra USA (13.100.0.0/16).
    - Utan att känna till vilket prefix är från vilken region prefixen inte behandlas på olika sätt.
    - Ditt WAN-nätverk kan anta att båda prefixen är närmare östra USA än västra USA och därför dirigera användare från båda kontoren till ExpressRoute-kretsen i östra USA, vilket ger mindre än optimal upplevelse för användare i Los Angeles-kontoret.

![VPN](./media/migrate-best-practices-networking/bgp1.png)
*BGP-communities deoptimerats anslutning*

**Lösning**

För att optimera routningen för båda kontoren, som du behöver veta vilket prefix är från Azure i västra USA och som kommer från Azure i östra USA. Du kan koda den här informationen med hjälp av BGP community-värden.
- Du tilldelar ett unikt BGP community-värde till varje Azure-region. Till exempel 12076: 51004 för USA, östra; 12076: 51006 för västra USA.
- Nu när det är tydligt vilket prefix som hör till vilken Azure-region kan du konfigurera en önskad ExpressRoute-krets.
- Eftersom du använder BGP till att utbyta routningsinformation kan du använda BGP: s lokala inställningar som påverkar routningen.
- I vårt exempel tilldela du ett högre lokalt inställningsvärde för 13.100.0.0/16 i västra USA än i östra USA och på samma sätt ett högre lokalt inställningsvärde för 23.100.0.0/16 i östra USA än i västra USA. 
- Den här konfigurationen garanterar att när både sökvägar till Microsoft, användarna i Los Angeles ska ansluta till Azure i västra USA västra-krets och användarna New York ansluta till Azure i östra USA Öst-krets. Routning är optimerad på båda sidorna.

![VPN](./media/migrate-best-practices-networking/bgp2.png)
*BGP-communities optimerade anslutning*


**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing) optimera Routning

## <a name="securing-vnets"></a>Skydda virtuella nätverk

Ansvaret för att skydda virtuella nätverk som delas mellan Microsoft och dig. Microsoft tillhandahåller många funktioner, samt tjänster som hjälper till att skydda resurser. När du utformar säkerhet för virtuella nätverk, finns det ett antal metodtips som du bör följa, inklusive implementera ett perimeternätverk, med hjälp av filtrering och säkerhetsgrupper, skydda åtkomst till resurser och IP-adresser och implementera attack skydd.

**Lära sig mer:**

- [Få en översikt](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices) bra metoder för nätverkssäkerhet.
- [Lär dig hur du](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security) design för säkra nätverk.

## <a name="best-practice-implement-an-azure-perimeter-network"></a>Bästa praxis: Implementera en Azure perimeternätverk

Även om Microsoft investerar kraftigt i skyddar molninfrastrukturen, måste du också skydda dina molntjänster och resursgrupper. En flerskiktiga strategi inom säkerhet ger det bästa skyddet. Sätta ett perimeternätverk är en viktig del av den defense-strategin.

- Ett perimeternätverk skyddar interna nätverksresurser från ett icke betrott nätverk. 
- Det är den yttersta lagret som är exponerade mot internet. Den allmänt sitter mellan internet och företagsinfrastruktur, vanligtvis med någon form av skydd på båda sidorna. 
- I en typisk företags-nätverkstopologi spikas kraftigt grundläggande infrastruktur på perimetrar med flera lager av säkerhetsenheter. Gränsen för varje nivå består av enheter och tillämpningspunkter för principen.
- Varje lager kan innehålla en kombination av microsoftbaserade nätverkssäkerhetslösningar med brandväggar, Denial of Service (DoS) förebygga, intrång identifiering/intrång protection system (IDS/IPS) och VPN-enheter.
- Genomförande av principer i perimeternätverket kan använda principer för brandväggen, åtkomstkontrollistor (ACL) eller specifika routning.
- När inkommande trafik anländer från internet, har det fångas upp och hanteras av en kombination av lösning för skydd för block attacker och skadlig trafik samtidigt som legitima begäranden till nätverket.
- Inkommande trafik kan vidarebefordra direkt till resurser i perimeternätverket. Nätverksresursen perimeternätverket kan sedan kommunicera med andra resurser som är djupare i nätverket, flytta vidarebefordra trafik till nätverk efter valideringen.

Följande bild visar ett exempel på ett enda undernät perimeternätverk i ett företagsnätverk, med två säkerhetsgränser.

![VPN](./media/migrate-best-practices-networking/perimeter.png)
*Perimeter nätverksdistribution*

**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) distribuera ett perimeternätverk mellan Azure och ditt lokala datacenter.


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>Bästa praxis: Filter VNet-trafik med NSG: er

Nätverkssäkerhetsgrupper (NSG) innehåller flera inkommande och utgående säkerhetsregler som filtrerar trafik till och från resurser. Filtrering kan vara av källa och mål-IP-adress, port och protokoll. 
- Nätverkssäkerhetsgrupper innehåller säkerhetsregler som tillåter eller nekar inkommande trafik till (eller utgående nätverkstrafik från) flera typer av Azure-resurser. För varje regel kan du ange källa och mål, port och protokoll.
- NSG-reglerna utvärderas efter prioritet med fem-tuppel-information (källa, källport, mål, målport och protokoll) att tillåta eller neka trafik.
- En flödespost skapas för befintliga anslutningar. Kommunikation tillåts eller nekas baserat på flödespostens anslutningsstatus.
- En post i flödet kan en NSG tillståndskänsliga. Exempel: Om du anger en utgående säkerhetsregel till en adress via port 80, behöver du inte en inkommande säkerhetsregel att svara på utgående trafik. Du behöver bara ange en inkommande säkerhetsregel om kommunikationen initieras externt.
- Även det motsatta gäller. Om inkommande trafik tillåts via en port, behöver du inte ange en utgående Säkerhetsregel för svar på trafik via porten.
- Befintliga anslutningar avbryts inte när du tar bort en säkerhetsregel som aktiverats flödet. Trafikflöden avbryts när anslutningar har stoppats och ingen trafik flödar i båda riktningarna för minst ett par minuter.
- När du skapar NSG: er kan skapa så lite som möjligt, men så många som är nödvändiga.

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>Bästa praxis: skydda Nord-Syd och öst-väst-trafik

När du skyddar virtuella nätverk, är det viktigt att tänka på attackvektorer.
- Med hjälp av endast undernät NSG: er förenklar din miljö, men endast säkrar trafik till undernätet. Detta kallas för Nord-Syd trafik.
- Trafik mellan virtuella datorer i samma undernät som kallas öst-väst-trafik.
- Det är viktigt att använda båda typerna av skydd, så att om en hackare får åtkomst utifrån de kommer att stoppa vid försök att ansluta datorer som finns i samma undernät.

### <a name="use-service-tags-on-nsgs"></a>Använda servicetaggar på NSG: er

En tjänsttagg representerar en grupp med IP-adressprefix. Med hjälp av en tjänsttagg hjälper till att minska komplexiteten när du skapar NSG-regler.
- Du kan använda servicetaggar i stället för specifika IP-adresser när du skapar regler.
- Microsoft hanterar adressprefix som är associerade med en tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.
- Du kan inte skapa egna tjänsttaggar eller ange vilka IP-adresser som ingår i en tagg.

Tjänsttaggar Ta manuellt arbete från tilldelning av en regel till grupper i Azure-tjänster. Till exempel om du vill tillåta ett virtuellt nätverk undernät som innehåller servrar för Webbåtkomst till en Azure SQL Database du kan skapa en regel för utgående trafik till port 1433, och använda den **Sql** servicetagg.
- Detta **Sql** taggen anger adressprefix för tjänsterna Azure SQL Database och Azure SQL Data Warehouse.
- Om du anger **Sql** som värde, trafik tillåts eller nekas till Sql.
- Om du bara vill tillåta åtkomst till **Sql** i en viss region kan du ange den regionen. Till exempel om du vill tillåta endast åtkomst till Azure SQL Database i regionen östra USA, du kan ange **Sql.EastUS** som tjänsttagg.
- Taggen representerar tjänsten, men inte specifika instanser av tjänsten. Till exempel taggen representerar tjänsten Azure SQL Database, men representerar inte en viss SQL-databas eller en server.
- Alla adressprefix som representeras av den här taggen är också representerade av taggen **Internet**.


**Lära sig mer:**

- [Läs mer om](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview) NSG: er.
- [Granska](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) tjänsttaggar som är tillgängliga för NSG: er.


## <a name="best-practice-use-application-security-groups"></a>Bästa praxis: använda programsäkerhetsgrupper

Programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som en naturlig förlängning av en app.

- Du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på programsäkerhetsgrupper.
- Programsäkerhetsgrupper kan du återanvända din säkerhetsprincip i stor skala utan manuellt underhåll av explicita IP-adresser.
- Programmet säkerhetsgrupper hanterar komplexiteten med explicita IP-adresser och flera regeluppsättningar så att du kan fokusera på affärslogiken. 

### <a name="example"></a>Exempel

![Programsäkerhetsgruppen](./media/migrate-best-practices-networking/asg.png)
*Application security exempel*

**Nätverksgränssnitt** | **Programsäkerhetsgruppen**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- Varje nätverksgränssnitt som hör till endast en programsäkerhetsgrupp i vårt exempel, men i själva verket ett gränssnitt kan tillhöra flera grupper, i enlighet med Azure-gränser.
- Ingen av nätverksgränssnitt har någon associerad NSG. NSG1 är kopplad till både undernät och innehåller följande regler.

    **Regelnamn** | **Syfte** | **Detaljer**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | Tillåta trafik från internet till webbservrar. Inkommande trafik från internet nekas av DenyAllInbound standardsäkerhetsregel, så det behövs ingen ytterligare regel för AsgLogic eller AsgDb programsäkerhetsgrupper. | Prioritet: 100<br/><br/> Källa: internet<br/><br/> Källport: *<br/><br/> Mål: AsgWeb<br/><br/> Målport: 80<br/><br/> Protokoll: TCP<br/><br/> Åtkomst: Tillåt.
    Deny-Database-All | Säkerhet för Standardregeln allowvnetinbound tillåter all kommunikation mellan resurser i samma virtuella nätverk, den här regeln krävs för att neka trafik från alla resurser. | Prioritet: 120<br/><br/> Källa: *<br/><br/> Källport: *<br/><br/> Mål: AsgDb<br/><br/> Målport: 1433<br/><br/> Protokoll: alla<br/><br/> Åtkomst: neka.
    Allow-Database-BusinessLogic | Tillåta trafik från AsgLogic programsäkerhetsgruppen till AsgDb programsäkerhetsgruppen. Prioritet för den här regeln är högre än neka databasen alla-regeln och bearbetas före den regeln, så att trafik från AsgLogic programsäkerhetsgruppen tillåts, och all annan trafik blockeras. | Prioritet: 110<br/><br/> Källa: AsgLogic<br/><br/> Källport: *<br/><br/> Mål: AsgDb<br/><br/> Målport: 1433<br/><br/> Protokoll: TCP<br/><br/> Åtkomst: Tillåt.

- Reglerna som definierar en programsäkerhetsgrupp som källan eller målet tillämpas bara på nätverksgränssnitt som är medlemmar i programsäkerhetsgruppen. Om nätverksgränssnittet inte är medlem i en programsäkerhetsgrupp tillämpas inte regeln på nätverksgränssnittet, även om nätverkssäkerhetsgruppen är associerad med undernätet.

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) programsäkerhetsgrupper.


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>Bästa praxis: säker åtkomst till PaaS som använder tjänstslutpunkter för virtuellt nätverk

Tjänstslutpunkter i virtuella nätverket utöka dina privata adressutrymme för virtuellt nätverk och identitet till Azure-tjänster via en direktanslutning.

- Slutpunkter kan du skydda dina kritiska Azure-resurser till dina virtuella nätverk endast. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket.
- Privat adressutrymme för virtuellt nätverk kan finnas överlappande och därför användas inte för att identifiera trafik som kommer från ett virtuellt nätverk.
- När tjänstslutpunkter är aktiverade i ditt virtuella nätverk, kan du skydda Azure-tjänstresurser genom att lägga till en VNet-regel service-resurser. Detta ger bättre säkerhet genom att helt ta bort den offentliga Internetåtkomsten till resurser och tillåter trafik från ditt virtuella nätverk.

![Tjänstslutpunkter](./media/migrate-best-practices-networking/endpoint.png)
*tjänstens slutpunkter*

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) VNet-tjänstslutpunkter.


## <a name="best-practice-control-public-ip-addresses"></a>Bästa praxis: Kontrollera offentliga IP-adresser

Offentliga IP-adresser i Azure kan associeras med virtuella datorer, belastningsutjämnare, programgatewayer och VPN-gatewayer.

- Offentliga IP-adresser kan Internetresurser kommunicerar inbördes till Azure-resurser och Azure-resurser kommunicera utgående till internet.
- Offentliga IP-adresser skapas med en basic eller standard-SKU, med ett antal skillnader. Standard-SKU: er kan tilldelas till alla tjänster, men vanligtvis konfigureras på virtuella datorer, belastningsutjämnare och programgatewayer.
- Det är viktigt att Observera att en grundläggande offentliga IP-adress inte har en NSG som konfigureras automatiskt. Du måste konfigurera en egen och tilldela regler för åtkomstkontroll. Standard-SKU-IP-adresser har en NSG och regler som tilldelas som standard.
- Som bästa praxis bör inte virtuella datorer konfigureras med en offentlig IP-adress.
    - Om du behöver en port som öppnas, ska det endast för webbtjänster, till exempel port 80 eller 443.
    - Standard remote hanteringsportar som SSH (22) och RDP (port 3389) ska anges för att neka, tillsammans med alla andra portar, med hjälp av NSG: er.
- En bättre metod är att placera virtuella datorer bakom en Azure load balancer eller application gateway. Om åtkomst till fjärranslutna hanteringsportar krävs, kan du använda åtkomst till just-in-time-virtuella datorer i Azure Security Center.

**Lära sig mer:**

- [Lär dig mer om](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) offentliga IP-adresser i Azure.
- [Läs mer](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) på just-in-time VM-åtkomst i Azure Security Center.


## <a name="leverage-azure-security-features-for-networking"></a>Utnyttja Azure säkerhetsfunktioner för nätverk

Azure har plattform säkerhetsfunktioner som är enkla att använda och ger omfattande motåtgärder till vanliga nätverksattacker. Dessa inkluderar Azure brandvägg, Brandvägg för webbaserade program och Network Watcher.

## <a name="best-practice-deploy-azure-firewall"></a>Rekommendation: Distribuera Azure-brandväggen

Azure-brandväggen är en hanterad, molnbaserad säkerhet nätverkstjänst som skyddar dina VNet-resurser. Det är en fullständigt administrerad brandvägg-som-tjänst-med inbyggd hög tillgänglighet och skalbarhet i obegränsad molnet.

![Tjänstslutpunkter](./media/migrate-best-practices-networking/firewall.png)
*Azure-brandvägg*

- I Azure-brandväggen kan centralt skapa, tillämpa och logga in principer för program och nätverk-anslutning över prenumerationer och virtuella nätverk.
- Azure-brandväggen använder en statisk offentlig IP-adress för din VNet-resurser utanför brandväggar som tillåter att identifiera trafik från ditt virtuella nätverk.
- Azure-brandväggen är helt integrerat med Azure Monitor för loggning och analyser.
- Som bästa praxis när du skapar brandväggsregler för Azure att använda FQDN-taggar för att skapa regler.
    - Ett FQDN-taggen representerar en grupp med FQDN: er som är associerade med välkända Microsoft-tjänster.
    - Du kan använda en FQDN-tagg för att tillåta nödvändiga utgående nätverkstrafik genom brandväggen.
- Om du vill tillåta manuellt Windows Update-nätverkstrafik via brandväggen, skulle du behöva skapa flera regler. Med hjälp av FQDN taggar, du skapar en regel för programmet och inkluderar uppdateringar för Windows-taggen. Med den här regeln är på plats kan nätverkstrafik till Microsoft Windows Update-slutpunkter flöda via brandväggen.

**Lära sig mer:**

- [Få en översikt](https://docs.microsoft.com/azure/firewall/overview) för Azure-brandväggen.
- [Lär dig mer om](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN-taggar.


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>Bästa praxis: distribuera Azure Web Application Firewall (WAF)

Webbprogram är allt för attacker som utnyttjar ofta kända sårbarheter. Kryphål omfattar SQL-inmatningsattacker och cross-site skriptattacker. Att förhindra sådana attacker i programkoden kan vara en utmaning och kan kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. En centraliserad brandvägg hjälper till att göra det mycket enklare säkerhetshantering och hjälper app-administratörer skydda mot intrång. En brandvägg för webbaserade program kan reagera på säkerhetshot snabbare genom att åtgärda kända säkerhetsrisker på en central plats i stället för att skydda enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

Azure Brandvägg för webbaserade program (WAF) är en funktion för Azure application gateway.
- WAF ger ett centraliserat skydd av dina webbappar mot vanliga kryphål och säkerhetsproblem.
- WAF skyddar utan att ändra koden i serverdelen.
- Det kan skydda flera webbprogram samtidigt bakom en Programgateway
- WAF är integrerad med Azure Security Center.
- Du kan anpassa WAF-regler och regelgrupper som passar dina krav för appar.
- Som bästa praxis bör du använda en WAF framför i web-riktade appar, inklusive appar på Azure Virtual Machines eller som en Azure App Service.

**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/application-gateway/waf-overview) WAF.
- [Granska](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration) WAF begränsningar och undantag.


## <a name="best-practice-implement-azure-network-watcher"></a>Bästa praxis: Implementera Azure Network Watcher

Azure Network Watcher innehåller verktyg för att övervaka resurser och kommunikation i ett virtuellt Azure nätverk. Du kan till exempel övervaka kommunikation mellan en virtuell dator och en slutpunkt, t.ex en annan virtuell dator eller FQDN, samt visa resurser och resursrelationer i ett virtuellt nätverk, eller diagnostisera nätverksproblem trafik.

![Network Watcher](./media/migrate-best-practices-networking/network-watcher.png)
*Network Watcher*

- Med Network Watcher kan du övervaka och diagnostisera nätverksproblem utan att logga in på virtuella datorer.
- Du kan utlösa infångade paket genom att ställa in aviseringar och få åtkomst till prestandainformation i realtid på paketnivå. När du ser ett problem, kan du undersöka det i detalj.
- Som bästa praxis bör du använda Network Watcher för att granska NSG-flödesloggar.
    - NSG-flödesloggar i Network Watcher kan du visa information om ingående och utgående IP-trafik via en NSG.
    - Flödesloggar skrivs i json-format.
    - Flödesloggar visar utgående och inkommande flöden på basis av per regel, nätverksgränssnitt (NIC) som flödet gäller, 5-tuppel information om flödet (källa/mål-IP, källa/mål port och protokoll) och om trafik tillåts eller nekas.

**Lära sig mer:**

- [Få en översikt](https://docs.microsoft.com/azure/network-watcher) i Network Watcher.
- [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) om NSG flow loggar.

## <a name="use-partner-tools-in-the-azure-marketplace"></a>Använda partner-verktygen i Azure Marketplace

Du kan använda säkerhetsprodukter från Microsofts partner i viss virtuella nätverksinstallationer (Nva) för mer avancerade nätverkstopologier.

- En NVA är en virtuell dator som utför en nätverksfunktion, till exempel en brandvägg, WAN-optimering eller annan nätverksfunktion.
- Nva: erna stärka VNet-funktioner för säkerhet och nätverk. De kan distribueras för högtillgänglig brandväggar, intrångsskydd, intrångsidentifiering, brandväggar för webbprogram (Waf), WAN-optimering, routning, belastningsutjämning, VPN, certifikathantering, Active Directory och multifaktorautentisering.
- NVA är tillgänglig från flera leverantörer i den [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>Bästa praxis: Implementera brandväggar och nva: er i hubben nätverk

I hubben hanteras normalt perimeternätverk (med åtkomst till internet) via en brandvägg-grupp, en Azure-brandväggen eller med brandväggar för webbprogram (Waf). Överväg följande jämförelser.

**Typ av brandvägg** | **Detaljer**
--- | ---
Waf | Web apps är vanliga och tenderar att drabbas av säkerhetsproblem och potentiella hot.<br/><br/> Waf är utformade för att identifiera attacker mot webbprogram (HTTP/HTTPS), mer specifikt än en allmän brandvägg.<br/><br/> Waf har jämfört med tradition brandväggsteknik, en uppsättning funktioner som skyddar interna servrar från hot.
Azure Firewall | Precis som NVA brandväggen servergrupper använder Azure-brandväggen en gemensam mekanism för administration och en uppsättning regler för anslutningssäkerhet för att skydda arbetsbelastningar som finns i eker-nätverk och vill kontrollera åtkomsten till lokala nätverk.<br/><br/> Brandvägg för Azure har inbyggd skalbarhet.
NVA-brandväggar | Som Azure brandväggen NVA ha brandväggen servergrupper vanliga mekanism för administration och en uppsättning säkerhetsregler för att skydda arbetsbelastningar som finns i eker-nätverk och vill kontrollera åtkomsten till lokala nätverk.<br/><br/> NVA-brandväggar kan skalas manuellt bakom en belastningsutjämnare.<br/><br/> Även om en NVA-brandväggen har mindre särskild programvara än WAF har bredare omfång för programmet att filtrera och granska alla typer av trafik i ut- och ingångsanspråk.<br/><br/> Om du vill använda NVA som du hittar dem på Azure Marketplace.

Vi rekommenderar att du använder en uppsättning Azure brandväggar (eller nva: er) för trafik från internet och en annan för trafik som skickas på plats.
- Använda endast en uppsättning brandväggar för både utgör en säkerhetsrisk, eftersom det ger någon säkerhetsperimeter mellan de två uppsättningarna med nätverkstrafik.
- Med separat brandvägg lager minskar komplexiteten för kontroll av säkerhetsregler och det är tydligt vilka regler som motsvarar vilka inkommande nätverksbegäran.

**Lära sig mer:**
- [Lär dig mer om](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) med nva: er i ett virtuellt Azure nätverk.

## <a name="next-steps"></a>Nästa steg 

Granska andra metodtips:

- [Bästa praxis](migrate-best-practices-security-management.md) för säkerhet och hantering efter migreringen.
- [Bästa praxis](migrate-best-practices-costs.md) för kostnadshantering efter migreringen.
