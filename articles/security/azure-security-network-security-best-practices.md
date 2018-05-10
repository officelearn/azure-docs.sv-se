---
title: Azure-nätverk säkerhetsmetoder | Microsoft Docs
description: Den här artikeln innehåller en uppsättning av bästa praxis för säkerhet med inbyggda funktioner i Azure.
services: security
documentationcenter: na
author: TomShinder
manager: mbaldwin
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 0aaf49aaa31a022e040fc7019a2f115f92555010
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="azure-network-security-best-practices"></a>Säkerhetsmetoder för Azure-nätverk
Microsoft Azure kan du ansluta virtuella datorer och enheter till andra nätverksenheter genom att placera dem på Azure-nätverk. Ett virtuellt Azure-nätverk är en konstruktion som gör det möjligt att ansluta virtuella nätverkskort till ett virtuellt nätverk till att tillåta TCP/IP-baserade kommunikation mellan nätverksenheter som aktiveras. Azure virtuella datorer som är anslutna till ett virtuellt Azure-nätverk kan ansluta till enheter på samma Azure virtuella nätverk, olika virtuella Azure-nätverk, på Internet eller även på din egen lokala nätverk.

Den här artikeln beskrivs en samling Azure-nätverk säkerhetsmetoder. Följande rekommendationer härleds från våra erfarenhet av Azure-nätverk och erfarenheter från kunder som dig själv.

För varje bör den här artikeln:

* Vad som är bästa praxis
* Varför du vill aktivera den bästa praxis
* Vad kan vara resultatet om du inte aktivera bästa praxis
* Möjliga alternativ till bästa praxis
* Hur du kan lära dig att aktivera bästa praxis

Den här artikeln Azure Network säkerhetsmetoder baseras på en konsensus åsikt och Azure plattformsfunktioner och funktioner, som de finns på den tid som den här artikeln skrevs. Åsikter och tekniker ändras med tiden och den här artikeln kommer att uppdateras regelbundet så att dessa ändringar.

Azure Network Metodtips om säkerhet i den här artikeln omfattar:

* Logiskt segmentet undernät
* Styra dirigeringsbeteendet
* Aktivera Tvingad tunneltrafik
* Använda virtuella nätverksenheter
* Distribuera DMZs för säkerhet zonindelning
* Undvika exponering till Internet med fast WAN-länkar
* Optimera drifttid och prestanda
* Använd globala belastningsutjämning
* Inaktivera RDP-åtkomst till virtuella Azure-datorer
* Aktivera Azure Security Center
* Utöka ditt datacenter till Azure

## <a name="logically-segment-subnets"></a>Logiskt segmentet undernät
[Virtuella Azure-nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) liknar ett lokalt nätverk i ditt lokala nätverk. Syftet med ett virtuellt Azure-nätverk är att du skapar ett enda privat IP-adress-baserade nätverk där du kan placera alla dina [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/). De privata IP-adressutrymmena tillgängliga i klass A (10.0.0.0/8) klass B (172.16.0.0/12) och klass C (192.168.0.0/16) intervall.

Liknar vad du gör lokalt, bör du segmentera större adressutrymme i undernät. Du kan använda [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) baserat undernät principer för att skapa dina undernät.

Routning mellan undernät sker automatiskt och du behöver inte konfigurera routningstabeller manuellt. Standardinställningen är dock att det finns inga nätverk åtkomstkontroller mellan undernät som du skapar i Azure Virtual Network. För att skapa nätverk åtkomstkontroller mellan undernät, måste du placera något mellan undernäten.

En av de saker som du kan använda för att utföra den här uppgiften är en [Nätverkssäkerhetsgruppen](../virtual-network/virtual-networks-nsg.md) (NSG). NSG: er är enkla tillståndskänslig paket inspektion enheter som använder 5-tuppeln (den käll-IP, källport, mål-IP, målport och protokoll i Internetskiktet 4) metod för att skapa Tillåt/neka regler för nätverkstrafik. Du kan tillåta eller neka trafik till och från en enda IP-adress till och från flera IP-adresser eller till och från hela undernät.

Med NSG: er för network access control mellan undernät kan du placera resurser som tillhör samma säkerhetszon eller roll i deras egna undernät. Tänk till exempel på en enkel 3-nivåprogram som har en webbnivå, ett program logik-nivå och en databasnivå. Du kan placera virtuella datorer som tillhör vart och ett av dessa nivåer i sina egna undernät. Sedan kan du använda NSG: er för att styra trafik mellan undernäten:

* Web nivå virtuella datorer bara kan upprätta anslutningar till programmet logik datorer och kan bara godkänna anslutningar från Internet
* Programmet logik virtuella datorer bara kan upprätta anslutningar med databasnivå och kan bara godkänna anslutningar från webbnivån
* Databasen nivå virtuella datorer kan inte upprätta anslutning med något utanför sin egen undernät och kan bara godkänna anslutningar från programmet logik nivån

Läs mer om Nätverkssäkerhetsgrupper och hur du kan använda dem för att logiskt segmentera dina virtuella Azure-nätverk i [vad är en Nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md) (NSG).

## <a name="control-routing-behavior"></a>Styra dirigeringsbeteendet
När du placerar en virtuell dator på ett Azure Virtual Network, märker du att den virtuella datorn kan ansluta till någon annan virtuell dator i samma Azure virtuella nätverk, även om de virtuella datorerna är i olika undernät. Detta är möjligt eftersom det är en uppsättning systemvägar som är aktiverad som standard som gör att den här typen av kommunikation. Dessa standardvägar att virtuella datorer i Azure samma virtuella nätverk ska initiera anslutningarna med varandra och med Internet (för utgående kommunikation till endast Internet).

System standardvägar är användbara för flera olika distributionsscenarier, finns men det tillfällen när du vill anpassa routningskonfiguration för din distribution. Dessa anpassningar kan du konfigurera nästa hoppadress att nå specifika mål.

Vi rekommenderar att du konfigurerar användardefinierade vägar när du distribuerar ett virtuellt nätverk postsäkerhet som beskrivs i senare bästa praxis.

> [!NOTE]
> krävs inte användardefinierade vägar och systemvägar standard fungerar i de flesta fall.
>
>

Du kan lära dig mer om användaren definierade vägar och hur du konfigurerar dem genom att läsa artikeln [vad är användardefinierade vägar och IP-vidarebefordran](../virtual-network/virtual-networks-udr-overview.md).

## <a name="enable-forced-tunneling"></a>Aktivera Tvingad tunneltrafik
För att bättre förstå Tvingad tunneling, är det bra att förstå vilka ”delade tunnlar”.
De vanligaste exemplet med delade tunnlar visas med VPN-anslutningar. Anta att du upprättar en VPN-anslutning från hotellrummet till företagets nätverk. Den här anslutningen kan du komma åt företagets resurser och all kommunikation till företagsnätverket gå via VPN-tunnel.

Vad händer när du vill ansluta till resurser på Internet? När delade tunnlar aktiveras gå anslutningarna direkt till Internet och inte via VPN-tunnel. Vissa säkerhetsexperter Överväg att detta ska vara en möjlig risk och rekommenderar därför att delade tunnlar är inaktiverad och alla anslutningar. Anslutningar som är avsedda för Internet och anslutningar som är avsedda för företagsresurser ska gå via VPN-tunnel. Fördelen med att göra detta är att tvingas sedan anslutningar till Internet via företagsnätverket säkerhetsenheter som skulle vara fallet om VPN-klienten är ansluten till Internet utanför VPN-tunnel.

Nu ska vi ta den tillbaka till virtuella datorer på ett Azure Virtual Network. Standardvägar för ett Azure Virtual Network kan virtuella datorer för att initiera trafik till Internet. Detta kan för utgöra en säkerhetsrisk eftersom dessa utgående anslutningar kan öka risken för angrepp på en virtuell dator och kan användas av angripare.
Därför rekommenderar vi att du aktiverar Tvingad tunneling på virtuella datorer när du har anslutning mellan ditt Azure-nätverk och ditt lokala nätverk. Mellan lokala beskrivs anslutningen senare i det här Azure nätverk bästa praxis dokumentet.

Om du inte har en plats-anslutning, kontrollerar du att du dra nytta av Nätverkssäkerhetsgrupper (beskrivs tidigare) eller Azure virtual network säkerhetsenheter (beskrivs bredvid) för att förhindra utgående anslutningar till Internet från din Azure-virtuella Datorer.

Mer information om Tvingad tunneling och hur du aktiverar det finns [konfigurera Tvingad Tunneling med PowerShell och Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## <a name="use-virtual-network-appliances"></a>Använda virtuella nätverksenheter
Medan Nätverkssäkerhetsgrupper och User-defined routning kan tillhandahålla ett viss mått av nätverkssäkerhet vid nätverks- och lager i den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), kommer det att finnas situationer där du kommer vilja eller behöva aktivera säkerhet på hög nivå i stacken. I sådana situationer rekommenderar vi att du distribuerar virtuella nätverk säkerhetsenheter som tillhandahålls av Azure partners.

Azure security nätverksinstallationer kan ge förbättrad säkerhetsnivåer över vad som tillhandahålls av nätverkskontroller nivå. Nätverket säkerhetsfunktioner som tillhandahålls av virtuellt nätverk säkerhetsenheter bland annat:

* Firewalling
* Intrång identifiering/intrång förebyggande
* Hantering av säkerhetsrisk
* Programkontrollen
* Nätverksbaserade avvikelseidentifiering
* Filtrering av webbprogram
* Antivirusprogram
* Botnät skydd

Om du behöver en högre nivå av nätverkssäkerhet än du kan hämta med administratörsnivå nätverkskontroller sedan rekommenderar vi att du undersöka och distribuera virtuella Azure-nätverket säkerhetsenheter.

Mer information om vilka virtuella Azure-nätverket säkerhetsenheter är tillgängliga, och om deras funktioner finns i [Azure Marketplace](https://azure.microsoft.com/marketplace/) och Sök efter ”säkerhet” och ”nätverkssäkerhet”.

## <a name="deploy-dmzs-for-security-zoning"></a>Distribuera DMZs för säkerhet zonindelning
En DMZ eller ”perimeternätverk” är en fysisk eller logisk nätverkssegment som är utformade för att erbjuda ett extra lager av säkerhet mellan dina tillgångar och Internet. Syftet med DMZ är att placera specialiserade access control nätverksenheter i utkanten av nätverket DMZ så att endast önskade trafik tillåts tidigare nätverksenheten för säkerhet och i ditt Azure-nätverk.

DMZs är användbara eftersom du kan fokusera åtkomstkontrollhantering ditt nätverk, övervakning, loggning och rapportering av enheterna i utkanten av det virtuella Azure-nätverket. Här skulle du vanligtvis aktivera DDoS förebyggande, intrång identifiering/intrångsskydd system (ID: N/IP-adresser), brandväggsregler och principer, webbfiltrering, nätverket program mot skadlig kod och mycket mer. Säkerhet nätverksenheter mellan Internet och det virtuella Azure-nätverket och har ett användargränssnitt på båda nätverken.

Det här är den grundläggande utformningen av ett perimeternätverk, men det finns många olika utformningar av perimeternätverk som back-to-back, tre-homed, multi-homed med mera.

Vi rekommenderar för alla distributioner med hög säkerhet du överväga att distribuera en DMZ för att förbättra nivå av nätverkssäkerhet för Azure-resurser.

Mer information om DMZs och hur du distribuerar dem i Azure finns [Microsofts molntjänster och nätverkssäkerhet](../best-practices-network-security.md).

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Undvika exponering till Internet med fast WAN-länkar
Många organisationer har valt Hybrid-IT-vägen. I hybrid-IT finns några av företagets informationstillgångar i Azure, medan andra är lokalt. I många fall, körs vissa komponenter av en tjänst i Azure medan andra komponenter är lokalt.

I hybrid IT-scenariot finns vanligtvis någon typ av korsanslutningar. Detta mellan en lokal anslutning gör det möjligt för företag att ansluta sina lokala nätverk till Azure-nätverk. Det finns två anslutningar mellan lokala lösningar:

* Plats-till-plats VPN
* ExpressRoute

[Plats-till-plats VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) representerar en virtuell privat anslutning mellan ditt lokala nätverk och ett Azure Virtual Network. Den här anslutningen sker via Internet och kan du ”tunnel” information i en krypterad anslutning mellan ditt nätverk och Azure. Plats-till-plats VPN är en säker, mogen teknik som har distribuerats av företag i alla storlekar för åren. Tunnel kryptering utförs med [IPSec-tunnelläge](https://technet.microsoft.com/library/cc786385.aspx).

Plats-till-plats VPN är en betrodd, tillförlitlig och etablerade teknik, trafik i tunneln sker via Internet. Dessutom begränsad bandbredd relativt till högst 200 Mbit/s.

Om du behöver en särskilda nivå av säkerhet och prestanda för anslutningar mellan platser, rekommenderar vi att du använder Azure ExpressRoute för dina korsanslutningar. ExpressRoute är en fast WAN länken mellan din lokala plats eller en värdleverantör för Exchange. Eftersom detta är en anger anslutning är data överföras inte via Internet och därför visas inte för de potentiella riskerna som Internet-kommunikation.

Läs mer om hur Azure ExpressRoute fungerar och hur du distribuerar i [teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="optimize-uptime-and-performance"></a>Optimera drifttid och prestanda
Sekretess, integritet och tillgänglighet (CIA) utgör triad av dagens mest inflytelserik säkerhetsmodell. Sekretess är om kryptering och sekretess, integritet handlar om att se till att data inte ändras av obehöriga personer och tillgänglighet om att se till att behöriga personer har tillgång till informationen om de har behörighet att komma åt. Det gick inte att utföra någon av dessa områden representerar ett potentiellt angrepp i säkerhet.

Tillgängligheten kan betraktas som om drifttid och prestanda. Om en tjänst är nere kan kan inte information nås. Om prestanda är så låg att göra data inte kan användas, kan vi anser att data inte är tillgänglig. Därför från ett säkerhetsperspektiv behöver vi göra allt vi kan kontrollera våra tjänster finns optimala drifttid och prestanda.
En populär och effektiv metod som används för att förbättra tillgänglighet och prestanda är att använda belastningsutjämning. Belastningsutjämning är en metod för att distribuera nätverkstrafik på servrar som är en del av en tjänst. Till exempel om du har frontend-webbservrar som en del av din tjänst, kan du använda belastningsutjämning distribuerar trafik över din flera frontend-webbservrar.

Den här distributionen av trafik ökar tillgängligheten eftersom om en av webbservrarna blir otillgänglig, belastningsutjämnaren slutar att skicka trafik till servern och omdirigerar till de servrar som är online. Belastningsutjämning hjälper också till prestanda eftersom processorn, nätverk och minne omkostnader för begäranden distribueras till alla belastningen belastningsutjämnade servrar.

Vi rekommenderar att du använder att belastningsutjämning när du kan och som passar dina tjänster. Vi ska åtgärda lämpligheten i följande avsnitt: Azure på Azure Virtual Network-nivå, tillhandahåller du med tre huvudsakliga läsa in belastningsutjämning alternativ:

* HTTP-baserade belastningsutjämning
* Extern belastningsutjämning
* Intern belastningsutjämning

## <a name="http-based-load-balancing"></a>HTTP-baserade belastningsutjämning
HTTP-baserade belastningsutjämning baserar beslut om vilken server för att skicka anslutningar med hjälp av egenskaperna för HTTP-protokollet. Azure har en HTTP-belastningsutjämnare som går med namnet Application Gateway.

Vi rekommenderar att du använder Azure Programgateway när:

* Program som kräver begäranden från samma användare-/klientsession för att nå samma virtuella serverdelsdator. Exempel på detta skulle shopping kundvagn appar och e-webbservrar.
* Program som du vill frigöra server webbservergrupper från SSL-avslutning omkostnader genom att utnyttja Application Gateway [SSL-avlastning](https://f5.com/glossary/ssl-offloading) funktion.
* Program, till exempel ett nätverk för innehållsleverans, som kräver flera HTTP-begäranden för att samma tidskrävande TCP-anslutningen ska routas eller belastningsutjämnad till olika serverdels-servrar.

Läs mer om hur Azure Application Gateway fungerar och hur du kan använda den i dina distributioner i [Gateway Programöversikt](../application-gateway/application-gateway-introduction.md).

## <a name="external-load-balancing"></a>Extern belastningsutjämning
Extern belastningsutjämning sker när inkommande anslutningar från Internet belastningsutjämnas mellan servrarna finns i ett Azure Virtual Network. Den externa belastningsutjämnaren med Azure kan ge dig den här funktionen och vi rekommenderar att du använder den när du inte behöver Fäst sessioner eller SSL-avlastning.

Till skillnad från HTTP-baserade belastningsutjämning använder externa belastningsutjämnare information vid nätverks- och lager i modellen för OSI-nätverk för att avgöra vilken server att läsa in saldo anslutning till.

Vi rekommenderar att du använder att externa belastningsutjämning när du har [tillståndslösa program](http://whatis.techtarget.com/definition/stateless-app) accepterar inkommande begäranden från Internet.

Mer information om hur Azure externa belastningsutjämnaren fungerar och hur du distribuerar det, se [komma igång med en Internet Facing belastningsutjämnare i Resource Manager med hjälp av PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="internal-load-balancing"></a>Intern belastningsutjämning
Intern belastningsutjämning liknar extern belastningsutjämning och använder samma metod för att läsa in saldo anslutningar till servrar bakom dem. Den enda skillnaden är att belastningsutjämnaren i det här fallet godkänna anslutningar från virtuella datorer som inte är på Internet. I de flesta fall initieras de anslutningar som accepteras för belastningsutjämning av enheter i ett Azure Virtual Network.

Vi rekommenderar att du använder intern belastningsutjämning för scenarier som har nytta av den här funktionen, till exempel när du behöver läsa in saldo anslutningar till SQL-servrar eller interna servrar.

Mer information om hur Azure intern belastningsutjämning fungerar och hur du kan distribuera finns [komma igång med en intern belastningsutjämnare använder PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md).

## <a name="use-global-load-balancing"></a>Använd globala belastningsutjämning
Offentligt moln databehandling gör det möjligt att distribuera globalt distribuerade program där komponenter som finns i datacenter över hela världen. Detta är möjligt på grund av Azures datacenter för global närvaro på Microsoft Azure. Till skillnad från tekniker som tidigare nämnts för belastningsutjämning, kan globala belastningsutjämning tillgängliggöra tjänster även när hela Datacenter kan bli otillgänglig.

Du kan hämta den här typen av global belastningsutjämning i Azure genom att utnyttja [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/). Traffic Manager gör går att läsa in saldo anslutningar till dina tjänster baserat på plats av användaren.

Om användaren gör en begäran till tjänsten från EU, till exempel omdirigeras anslutningen till dina tjänster som finns i ett EU-datacenter. Den här delen av Traffic Manager globala att läsa in belastningsutjämning bidrar till att förbättra prestanda eftersom ansluter till det närmaste datacentret är snabbare än att ansluta till datacenter som är långt.

På sidan tillgänglighet säkerställer globala belastningsutjämning att din tjänst är tillgänglig även om ett helt datacenter ska bli tillgänglig.

Till exempel ett Azure-datacenter ska bli tillgänglig på grund av miljön orsaker eller på grund av avbrott (till exempel regionala nätverksfel), kan anslutningar till tjänsten skulle dirigeras till den närmaste online datacenter. Det här globala belastningsutjämning åstadkoms genom att dra nytta av DNS-principer som du kan skapa i Traffic Manager.

Vi rekommenderar att du använder Traffic Manager för alla moln lösningar du utvecklar som har spridda omfattning över flera regioner och kräver den högsta nivån av drifttid som möjligt.

Läs mer om Azure Traffic Manager och hur du distribuerar den i [vad är Traffic Manager](../traffic-manager/traffic-manager-overview.md).

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>Inaktivera RDP/SSH-åtkomst till virtuella Azure-datorer
Det går att nå Azure virtuella datorer med hjälp av den [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) och [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH)-protokoll. Dessa protokoll gör det möjligt att hantera virtuella datorer från fjärranslutna platser och är standard i datacenter datoranvändning.

Potentiella säkerhetsproblem med hjälp av dessa protokoll via Internet är att angripare kan använda olika [brute force](https://en.wikipedia.org/wiki/Brute-force_attack) tekniker för att få åtkomst till virtuella datorer i Azure. När angriparna få åtkomst, kan de använda den virtuella datorn som en startpunkt för att kompromissa med andra datorer i ditt virtuella Azure-nätverk eller även angrepp nätverksenheter utanför Azure.

Därför rekommenderar vi att du inaktiverar direkt RDP och SSH-åtkomst till dina Azure virtuella datorer från Internet. När RDP och SSH direktåtkomst från Internet har inaktiverats kan ha andra alternativ som du kan använda för att få åtkomst till dessa virtuella datorer för fjärrhantering:

* Punkt-till-plats VPN
* Plats-till-plats VPN
* ExpressRoute

[Punkt-till-plats VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) är en annan term för en VPN-klient/server-anslutning för fjärråtkomst. En punkt-till-plats-VPN kan en enskild användare att ansluta till ett virtuellt Azure-nätverk via Internet. När punkt-till-plats-anslutning har upprättats, kommer användaren att kunna använda RDP eller SSH för att ansluta till alla virtuella datorer på det virtuella Azure-nätverket som användaren är ansluten till via punkt-till-plats-VPN. Detta förutsätter att användaren har behörighet att nå de virtuella datorerna.

Punkt-till-plats VPN är säkrare än direkt via RDP eller SSH-anslutningar eftersom användaren måste autentiseras två gånger innan du ansluter till en virtuell dator. Först användaren måste autentiseras (och auktoriseras) att upprätta punkt-till-plats VPN-anslutningen; andra: användaren måste autentisera (och auktoriseras) upprätta RDP eller SSH-session.

En [plats-till-plats VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) ansluter hela nätverket till ett annat nätverk via Internet. Du kan använda en plats-till-plats-VPN för att ansluta din lokala nätverk till ett Azure Virtual Network. Om du distribuerar ett plats-till-plats-VPN kan användare på ditt lokala nätverk kommer att kunna ansluta till virtuella datorer i ditt virtuella Azure-nätverk med hjälp av RDP eller SSH-protokollet via plats-till-plats VPN-anslutning och kräver inte att du direkt via RDP eller SSH-åtkomst via Internet.

Du kan också använda en dedikerad WAN-länk för att ge liknande funktioner som plats-till-plats-VPN. De viktigaste skillnaderna är 1. dedikerad WAN-länken passerar inte Internet och 2. fast WAN-länkar är vanligtvis mer stabilt och performant. Azure tillhandahåller en dedikerad WAN-länk-lösning i form av [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="enable-azure-security-center"></a>Aktivera Azure Security Center
Azure Security Center hjälper dig att förebygga, upptäcka och åtgärda hot och ger du ökad insyn i, och kontroll över säkerheten för dina Azure-resurser. Härifrån kan du övervaka och hantera principer för alla Azure-prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Azure Security Center hjälper dig att optimera och övervaka nätverkssäkerhet av:

* Att tillhandahålla säkerhetsrekommendationer för nätverk
* Övervaka status för din nätverkskonfiguration för säkerhet
* Varna dig för nätverksbaserade hot både på slutpunkten och nätverk

Vi rekommenderar starkt att du aktiverar Azure Security Center för alla dina Azure-distributioner.

Läs mer om Azure Security Center och aktivera för dina distributioner i [introduktion till Azure Security Center](../security-center/security-center-intro.md).

## <a name="securely-extend-your-datacenter-into-azure"></a>Utöka ditt datacenter på ett säkert sätt till Azure
Många företagets IT organisationer som vill utöka till molnet i stället för växande sina lokala datacenter. Detta representerar en förlängning av befintliga IT-infrastruktur till det offentliga molnet. Genom att utnyttja mellan lokala anslutningsmöjligheter, är det möjligt att hantera dina virtuella Azure-nätverk som bara ett annat undernät i nätverksinfrastrukturen lokalt.

Det finns dock planerings- och problem som behöver åtgärdas först. Detta är särskilt viktigt i området för nätverkssäkerhet. Något av det bästa sättet att förstå hur du hanterar en sådan design är att se ett exempel.

Microsoft har skapat den [Datacenter tillägget referens Arkitekturdiagram](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) och ge support för säkerhet för att hjälpa dig att förstå hur dessa filnamnstillägget datacenter skulle se ut. Detta ger ett exempel på referensimplementering som du kan använda för att planera och utforma filnamnstillägget säker enterprise datacenter till molnet. Vi rekommenderar att du läser det här dokumentet för att få en uppfattning om de viktigaste komponenterna i en säker lösning.

Om du vill veta mer om hur du utökar ditt datacenter på ett säkert sätt till Azure kan du titta på videon [Utöka ditt Datacenter till Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).
