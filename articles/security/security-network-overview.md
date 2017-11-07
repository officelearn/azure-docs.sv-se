---
title: "Nätverk säkerhetsbegrepp & krav i Azure | Microsoft Docs"
description: " Den här artikeln är det enkelt för dig att förstå vad Microsoft Azure har att erbjuda i området för nätverkssäkerhet. Vi innehåller grundläggande information om grundläggande koncept för säkerhet och krav samt information om vad Azure har att erbjuda i dessa olika områden. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: terrylan
ms.openlocfilehash: 27243856d0c6b70c7515b6bde66b99ef6160eb36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-security-overview"></a>Översikt över säkerheten i Azure-nätverk
Microsoft Azure innehåller en stabil nätverksinfrastruktur för att stödja dina program och tjänsten anslutningskrav. Nätverksanslutningen är möjlig mellan resurser i Azure, mellan lokala och Azure värdbaserade resurser, och till och från Internet och Azure.

Syftet med den här artikeln är att göra det enklare för dig att förstå vad Microsoft Azure har att erbjuda i området för nätverkssäkerhet. Här kan vi ge grundläggande information om grundläggande koncept för säkerhet och krav. Får du reda på vad Azure har att erbjuda i var och en av dessa områden samt länkar för att få en bättre förståelse av intressanta områden.

Översikt över Azure Network Security artikeln fokuserar på följande områden:

* Azure-nätverk
* Åtkomstkontrollen för nätverk
* Säker åtkomst och mellan platser för fjärranslutningar
* Tillgänglighet
* Namnmatchning
* DMZ-arkitektur
* Övervakning och hotidentifiering


## <a name="azure-networking"></a>Azure-nätverk
Virtuella datorer måste nätverksanslutningen. För att uppfylla detta krav, kräver Azure virtuella datorer måste vara ansluten till ett Azure Virtual Network. Ett virtuellt Azure-nätverk är en logisk konstruktion som bygger på fysisk Azure nätverksinfrastruktur. Varje logiskt virtuella Azure-nätverk är isolerat från alla andra virtuella Azure-nätverk. På så sätt kan du se till att nätverkstrafik i din distribution inte är tillgänglig för andra Microsoft Azure-kunder.

Läs mer:

* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Åtkomstkontrollen för nätverk
Åtkomstkontrollen för nätverk är att begränsa anslutning till och från specifika enheter och undernät i ett Azure Virtual Network. Målet med åtkomstkontrollen för nätverk är att begränsa åtkomst till virtuella datorer och tjänster till godkända användare och enheter. Åtkomstkontroller baseras på Tillåt eller neka beslut för anslutningar till och från en virtuell dator eller tjänst.

Azure har stöd för flera typer av åtkomstkontrollen för nätverket som:

* Kontrollen av nätverkets lager
* Dirigera kontroll och Tvingad tunneltrafik
* Säkerhetsenheter för virtuellt nätverk

### <a name="network-layer-control"></a>Kontrollen av nätverkets lager
En säker distribution kräver vissa mått av åtkomstkontrollen för nätverket. Målet med åtkomstkontrollen för nätverk är att begränsa virtuella kommunikation till de nödvändiga system och att andra kommunikationsförsök blockeras.

Om du behöver grundläggande nivån nätverksåtkomstkontroll (baserat på IP-adress och TCP- eller UDP-protokoll), kan du använda Nätverkssäkerhetsgrupper. En Nätverkssäkerhetsgrupp (NSG) är en grundläggande tillståndskänslig paket filtrering brandväggen och du kan styra åtkomsten baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple). NSG: er ger inte programmet layer inspektion eller autentiserad åtkomstkontroller.

Läs mer:

* [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Dirigera kontroll och Tvingad tunneltrafik
Möjlighet att styra dirigeringsbeteendet på dina virtuella Azure-nätverk är en kritisk nätverket säkerhets- och kontroll funktion. Om routning är felaktigt konfigurerad, kan program och tjänster som finns på den virtuella datorn ansluta till obehöriga enheter, inklusive system ägs och drivs av potentiella angripare.

Azure nätverk stöder möjligheten att anpassa dirigeringsbeteendet för nätverkstrafik i dina virtuella Azure-nätverk. På så sätt kan du ändra standard routning tabellposter i ditt virtuella Azure-nätverk. Kontroll av dirigeringsbeteendet hjälper dig att se till att all trafik från en viss enhet eller grupp av enheter anländer till eller lämnar det virtuella nätverket via en viss plats.

Till exempel kanske en virtuell nätverksenhet för säkerhet på ditt Azure-nätverk. Du vill kontrollera att all trafik till och från det virtuella Azure-nätverket går genom den virtuella postsäkerhet. Du kan göra detta genom att konfigurera [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) i Azure.

[Tvingad tunneltrafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts att initiera en anslutning till enheter på Internet. Observera att detta skiljer sig från att acceptera inkommande anslutningar och sedan svara på dem. Frontend-webbservrar måste svara på begäranden från Internet-värdar och så Internet-källkod trafik tillåts inkommande till dessa servrar och webbservrar tillåts att svara.

Vad du inte vill tillåta är en klientwebbserver för att starta en utgående begäran. Sådana begäranden kan utgöra en säkerhetsrisk eftersom dessa anslutningar som kan användas för att hämta skadlig kod. Även om du vill att dessa servrar att initiera utgående förfrågningar till Internet, kanske du vill tvinga dem att gå igenom din lokala webbproxyservrar så att du kan dra nytta av URL-filtrering och loggning.

Du kan i stället att använda Tvingad tunneltrafik för att förhindra detta. När du aktiverar Tvingad tunneling tvingas alla anslutningar till Internet via ditt lokala gateway. Du kan konfigurera Tvingad tunneling genom att utnyttja användardefinierade vägar.

Läs mer:

* [Vad är användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Säkerhetsenheter för virtuellt nätverk
Medan Nätverkssäkerhetsgrupper, användardefinierade vägar och Tvingad tunneling ger en hög säkerhetsnivå vid nätverks- och lager i den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), det kan finnas tillfällen när du vill aktivera säkerhet på programnivå som är högre än nätverket.

Till exempel kan ditt säkerhetskrav innehålla:

* Autentisering och auktorisering innan åtkomst beviljas till ditt program
* Identifiering av adressintrång och intrångsidentifiering svar
* Programmet layer-kontroll för övergripande protokoll
* URL-filtrering
* Nätverket nivån antivirus eller antimalware
* Skydd mot bot
* Åtkomstkontroll för programmet
* Ytterligare DDoS-skydd (ovanför den DDoS-skydd som Azure-strukturen själva)

Du kan komma åt dessa förbättrade funktioner för nätverkssäkerhet med hjälp av en Azure partnerlösning. Du hittar den senaste Azure partnernätverk säkerhetslösningar genom att besöka den [Azure Marketplace](https://azure.microsoft.com/marketplace/) och söka efter ”säkerhet” och ”nätverkssäkerhet”.

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Säker fjärråtkomst och plats-anslutning
Installation, konfiguration och hantering av Azure-resurser behöver göras via fjärranslutning. Dessutom kanske du vill distribuera [hybrid IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) lösningar som har komponenter på lokalt och i det offentliga Azure-molnet. Dessa scenarier kräver säker fjärråtkomst.

Azure nätverk stöder följande scenarion för säker fjärråtkomst:

* Enskilda arbetsstationer kan anslutas till ett virtuellt Azure-nätverk
* Ansluta dina lokala nätverk till ett virtuellt Azure-nätverk med en VPN-anslutning
* Ansluta det lokala nätverket till ett virtuellt Azure-nätverk med en fast WAN-länk
* Ansluta virtuella Azure-nätverk till varandra

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Ansluta enskilda arbetsstationer till Azure-nätverk
Det kan finnas tillfällen när du vill aktivera enskilda utvecklare eller åtgärder personal att hantera virtuella datorer och tjänster i Azure. Till exempel behöver du åtkomst till en virtuell dator på ett Azure Virtual Network och säkerhetsprinciperna tillåter inte RDP eller SSH fjärråtkomst till enskilda virtuella datorer. I det här fallet kan du använda en punkt-till-plats VPN-anslutning.

Punkt-till-plats VPN-anslutning använder den [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) protokollet så att du kan skapa en privat och säker anslutning mellan användaren och Azure Virtual Network. När VPN-anslutningen har upprättats kan kommer användaren att kunna RDP eller SSH via VPN-anslutning till en virtuell dator på Azure Virtual Network (förutsatt att användaren kan autentiseras och auktoriseras).

Läs mer:

* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Ansluta din lokala nätverk till Azure-nätverk med en VPN-anslutning
Du kanske vill ansluta hela företagsnätverket, eller delar av det, till ett Azure Virtual Network. Detta är vanligt i hybrid IT scenarier där företag [utöka sina lokala datacenter till Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). I många fall företag ska vara värd för delar av en tjänst i Azure och delar lokalt, till exempel när en lösning innehåller frontend-webbservrar i Azure och backend-databaser på lokalt. Dessa typer av ”anslutningar mellan platser” Se också hantering av Azure finns resurser mer säker och aktivera scenarier, till exempel utöka Active Directory-domänkontrollanter i Azure.

Ett sätt att uppnå detta är att använda en [plats-till-plats VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Skillnaden mellan en plats-till-plats-VPN och en punkt-till-plats-VPN är att en punkt-till-plats-VPN ansluter en enhet till ett Azure Virtual Network när en plats-till-plats-VPN ansluter hela nätverket (till exempel ditt lokala nätverk) till ett Azure Virtual Network. Plats-till-plats VPN till ett virtuellt Azure-nätverk används mycket säkert IPSec-tunnelläge VPN-protokoll.

Läs mer:

* [Skapa ett VNet Resource Manager med en plats-till-plats VPN-anslutning med hjälp av Azure-portalen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Planering och design för VPN-gateway](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Ansluta din lokala nätverk till Azure-nätverk med en fast WAN-länk
Punkt-till-plats och plats-till-plats VPN-anslutningar är effektiv för att aktivera korsanslutningar. Dock anser vissa organisationer att det har följande nackdelar:

* VPN-anslutningar flytta data via Internet – detta visar anslutningarna till potentiella säkerhetsproblem med flytta data över ett offentligt nätverk. Dessutom kan tillförlitlighet och tillgänglighet för Internet-anslutningar inte garanteras.
* VPN-anslutningar till virtuella Azure-nätverk kan anses bandbredden begränsad för vissa program och syften som de högsta ut vid runt 200 Mbit/s.

Organisationer som behöver den högsta nivån av säkerhet och tillgänglighet för sina anslutningar mellan platser normalt använda dedicerade WAN-länkar för att ansluta till fjärranslutna platser. Azure tillhandahåller möjligheten att använda en fast WAN-länk som du kan använda för att ansluta din lokala nätverk till ett Azure Virtual Network. Detta aktiveras via Azure ExpressRoute.

Läs mer:

* [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Ansluta virtuella Azure-nätverk till varandra
Det är möjligt att använda flera virtuella Azure-nätverk för din distribution. Det finns många orsaker till varför du kan göra detta. Ett av följande kan vara att förenkla hanteringen; en annan kanske av säkerhetsskäl. Oavsett syfte eller motiveringen för att publicera resurser på olika virtuella Azure-nätverk, kan det finnas tillfällen när du vill att resurser på varje nätverk att ansluta till varandra.

Ett alternativ kan vara för tjänster i ett virtuellt Azure-nätverk att ansluta till tjänster på en annan Azure-nätverk genom att ”loopa tillbaka” via Internet. Anslutningen skulle starta i ett Azure Virtual Network, gå via Internet och gå sedan tillbaka till målet Azure Virtual Network. Det här alternativet visar anslutningen till säkerhetsproblem som ingår i alla Internet-baserad kommunikation.

Ett bättre alternativ kan vara att skapa ett Azure Virtual Network-Azure-virtuella nätverk plats-till-plats VPN. Den här Azure virtuella nätverk till Azure virtuella nätverk plats-till-plats VPN använder samma [IPSec-tunnelläge](https://technet.microsoft.com/library/cc786385.aspx) protokoll som nämns ovan mellan lokala plats-till-plats VPN-anslutning.

Fördelen med att använda en Azure virtuella nätverk till Azure virtuella nätverk plats-till-plats VPN är att VPN-anslutningen har upprättats via Azure-nätverk-infrastruktur i stället för att ansluta via Internet. Detta ger dig ett extra säkerhetslager jämfört med plats-till-plats VPN som ansluter via Internet.

Läs mer:

* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Tillgänglighet
Tillgänglighet är en viktig del av security-programmet. Om dina användare och datorer inte kan komma åt de behöver komma åt över nätverket, tjänsten kan ses komprometteras. Azure har nätverkstekniker som stöder följande metoder för hög tillgänglighet:

* HTTP-baserade belastningsutjämning
* Nivån för belastningsutjämning
* Globala belastningsutjämning

Belastningsutjämning är en mekanism avsedd att fördela jämnt anslutningar mellan flera enheter. Målen med belastningsutjämning är:

* Öka tillgänglighet – när du läser in saldo anslutningar på flera enheter, en eller flera av enheterna som kan bli tillgänglig och de tjänster som körs på de återstående online enheterna kan fortsätta att hantera innehåll från tjänsten
* Öka prestanda – när du läser in saldo anslutningar på flera enheter en enskild enhet behöver inte vidta processor träffar. I stället krav och minnesresurser betjänar innehållet sprids över flera enheter.

### <a name="http-based-load-balancing"></a>HTTP-baserade belastningsutjämning
Organisationer som kör webbaserade tjänster ofta vilja ha en HTTP-baserad belastningsutjämnare framför de webbtjänster för att säkerställa tillräcklig nivåer av prestanda och hög tillgänglighet. Till skillnad från traditionella nätverksbaserade belastningsutjämnare, är belastningsutjämning beslut HTTP-baserade belastningsutjämnare baserade på egenskaper i HTTP-protokollet inte om Nätverks- och transport layer-protokoll.

För att ge dig HTTP-baserade belastningsutjämning för dina webbaserade tjänster, ger Azure dig Azure Application Gateway. Azure Application Gateway har stöd för:

* HTTP-baserade belastningsutjämning – belastningen belastningsutjämning beslut fattas baserat på egenskap särskilda HTTP-protokollet
* Cookie-baserad session tillhörighet – den här funktionen ser till att anslutningar till en server bakom som belastningsutjämnare intakt mellan klienten och servern. Detta garanterar stabiliteten för transaktioner.
* SSL-avlastning – när en klientanslutning har upprättats med belastningsutjämnaren, session mellan klienten och belastningsutjämnaren krypteras med hjälp av HTTPS (SSL /) protokollet. Du kan dock ha alternativet för att anslutningen mellan belastningsutjämnaren och webbservern bakom belastningen belastningsutjämnaren använda protokollet HTTP (okrypterat) för att öka prestandan. Detta kallas ”SSL-avlastning” eftersom webbservrar bakom belastningsutjämnaren inte drabbas av processor overhead ingår i kryptering och därför ska kunna tjänstbegäranden snabbare.
* URL-baserade innehåll routing – den här funktionen gör det möjligt för belastningsutjämnaren att fatta beslut om var att vidarebefordra anslutningar baserat på mål-URL. Detta ger mycket större flexibilitet än lösningar som gör att läsa in belastningsutjämning beslut baserat på IP-adresser.

Läs mer:

* [Översikt över Gateway](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Nivån för belastningsutjämning
Till skillnad från HTTP-baserade belastningsutjämning gör belastningsutjämning i nivån belastningen belastningsutjämning beslut baserat på IP-adressen och porten (TCP eller UDP) nummer.
Du kan få fördelarna med nivå belastningsutjämning i Azure med hjälp av Azure belastningsutjämnare. Vissa viktiga egenskaper för Azure belastningsutjämnare är:

* Belastningsutjämning i nivån baserat på IP-adress och port nummer
* Stöd för alla program layer protocol
* Om belastningsutjämning till Azure virtuella datorer och cloud services rollinstanser
* Kan användas för både Internet-riktade (extern belastningsutjämning) och icke-Internet facing (intern belastningsutjämning) program och virtuella datorer
* Slutpunkten som övervakning, som används för att avgöra om någon av dessa tjänster bakom belastningsutjämnaren har blivit tillgänglig

Läs mer:

* [Belastningsutjämnare för Internet mellan flera virtuella datorer eller tjänster](../load-balancer/load-balancer-internet-overview.md)
* [Översikt över interna belastningsutjämnare](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globala belastningsutjämning
Vissa organisationer ska den högsta nivån för tillgänglighet som möjligt. Ett sätt att uppnå det här målet är som värd för program i globalt distribuerade datacenter. När ett program finns i datacenter finns i hela världen, är det möjligt för en hel geopolitiska region att bli tillgänglig och fortfarande har programmet och körs.

Förutom tillgänglighet fördelarna du får genom att lägga upp program i globalt distribuerade datacenter, kan också du få prestandafördelarna. Dessa prestandafördelar kan erhållas med hjälp av en mekanism som dirigerar begäranden för tjänsten till datacenter som ligger närmast den enhet som begäran kommer ifrån.

Globala belastningsutjämning kan ge dig båda av dessa fördelar. I Azure, kan du få fördelarna med globala belastningsutjämning med hjälp av Azure Traffic Manager.

Läs mer:

* [Vad är Traffic Manager?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Namnmatchning
Namnmatchning är en viktig funktion för alla tjänster som du har i Azure. Från ett säkerhetsperspektiv kan röjande av funktionen name resolution leda till en angripare omdirigera begäranden från dina platser till en angripares webbplats. Säker namnmatchning är ett krav för alla molnbaserade tjänster.

Det finns två typer av namnmatchning som du behöver adressera:

* Intern namnmatchning – intern namnmatchning används av tjänsterna i ditt virtuella Azure-nätverk, ditt lokala nätverk eller båda. Namn som används för intern namnmatchning för är inte tillgängliga via Internet. För optimal säkerhet är det viktigt att ett internt namn upplösning schema inte är tillgänglig för externa användare.
* Externa namnmatchning – externa namnmatchning används av användare och enheter utanför din lokala och virtuella Azure-nätverk. Dessa är de namn som är synliga för Internet och som används för direkt anslutning till dina molnbaserade tjänster.

För intern namnmatchning har du två alternativ:

* Ett Azure Virtual Network DNS-server – när du skapar en ny Azure Virtual Network, en DNS-server skapas åt dig. DNS-servern kan matcha namnen på de datorer som finns i det virtuella Azure-nätverket. Den här DNS-servern kan inte konfigureras och hanteras av Azure-strukturen manager, vilket gör det en säker name resolution lösning.
* Ta med din egen DNS-server – du har möjlighet att placera en DNS-server om du väljer själv i ditt virtuella Azure-nätverk. Den här DNS-server kan vara en Active Directory-integrerade DNS-server eller en dedikerad DNS-server-lösning som tillhandahålls av en Azure-partner som du kan hämta från Azure Marketplace.

Läs mer:

* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)
* [Hantera DNS-servrar som används av ett virtuellt nätverk (VNet)](../virtual-network/virtual-network-manage-network.md#dns-servers)

För externa DNS-matchning har du två alternativ:

* Värd för dina egna externa DNS-server lokalt
* Värd för externa DNS-servern med en tjänstleverantör

Många stora företag ska vara värd för sina egna DNS-servrar lokalt. De kan göra detta eftersom de har expertisen nätverk och global närvaro gör.

I de flesta fall är det bättre att vara värd för din DNS-namnmatchningstjänster med en tjänstleverantör. Dessa leverantörer har expertisen nätverk och global närvaro så mycket hög tillgänglighet för din namnmatchningstjänster. Tillgänglighet är nödvändigt för DNS-tjänster, eftersom om din namnmatchningstjänster misslyckas ingen kommer att kunna komma åt dina Internet facing tjänster.

Azure tillhandahåller du hög tillgänglighet och performant externa DNS-lösning i form av Azure DNS. Externa name resolution lösningen drar nytta av hela världen Azure DNS-infrastruktur. På så sätt kan du vara värd för din domän i Azure med samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster. Som en del av Azure ärver också starkt säkerhetsåtgärder som är inbyggda i plattformen.

Läs mer:

* [Översikt över Azure DNS](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>DMZ-arkitektur
Många företag använda DMZs för att segmentera sina nätverk för att skapa en buffert zon mellan Internet och sina tjänster. DMZ del av nätverket anses vara en zon med låg säkerhet och inga värdefulla tillgångar placeras i det nätverkssegmentet. Du ser vanligtvis säkerhet nätverksenheter som har ett nätverksgränssnitt på DMZ segmentet och ett annat nätverksgränssnitt som är ansluten till ett nätverk som har virtuella datorer och tjänster som accepterar inkommande anslutningar från Internet.

Det finns flera varianter av DMZ design och beslut att distribuera en DMZ och sedan vilken typ av DMZ ska användas om du väljer att använda en, baserat på ditt nätverkssäkerhetskrav.

Läs mer:

* [Microsoft-molntjänster och nätverkssäkerhet](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Övervakning och hotidentifiering

Azure tillhandahåller funktioner som hjälper dig i den här nyckeln området med tidig upptäckt, övervakning och möjligheten att samla in och granska nätverkstrafik.

### <a name="azure-network-watcher"></a>Azure Nätverksbevakaren
Azure Nätverksbevakaren innehåller ett stort antal funktioner som hjälp med felsökning samt ger en helt ny uppsättning av verktyg för att hjälpa till med identifiering av säkerhetsproblem.

[Säkerhetsgrupp visa ](/network-watcher/network-watcher-security-group-view-overview.md) hjälper till med gransknings- och kompatibilitet för virtuella datorer och kan användas för att utföra programmässiga granskningar jämföra baslinjer-principer som definierats av organisationen för att effektivt regler för var och en av dina virtuella datorer. Detta kan hjälpa dig att identifiera eventuella konfigurationsavvikelser.

[Paketinsamling](/network-watcher/network-watcher-packet-capture-overview.md) kan du avbilda nätverkstrafik till och från den virtuella datorn. Förutom hjälpa genom att du kan samla in nätverksstatistik och med felsökning av problem med programmet vara paketinsamling ovärderlig i undersökning av intrång i nätverket. Du kan också använda den här funktionen tillsammans med Azure Functions för att starta nätverksinsamlingar som svar på specifika Azure-aviseringar.

Mer information om Azure Nätverksbevakaren och hur du kan börja testa vissa funktioner i din labs ta en titt på den [Azure nätverksbevakaren översikt över övervakning](/network-watcher/network-watcher-monitoring-overview.md)

>[!NOTE]
Azure nätverksbevakaren är fortfarande i förhandsversion så att den inte kanske har samma nivå av tillgänglighet och tillförlitlighet som viktiga tjänster som är i allmänhet tillgänglighet. Vissa funktioner kanske inte stöds, kan ha begränsad kapacitet och kanske inte tillgänglig på alla platser i Azure. De senaste meddelanden på tillgänglighet och status för den här tjänsten kontrollerar den [Azure uppdateringar](https://azure.microsoft.com/updates/?product=network-watcher)

### <a name="azure-security-center"></a>Azure Security Center
Security Center hjälper dig att förebygga, upptäcka och åtgärda hot och ger du ökad insyn i, och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, och upptäcka hot som annars kanske skulle förbli oupptäckta, fungerar med en stor mängd lösningar för informationssäkerhet.

Azure Security Center hjälper dig att optimera och övervaka nätverkssäkerhet av:

* Att tillhandahålla säkerhetsrekommendationer för nätverk
* Övervaka status för din nätverkskonfiguration för säkerhet
* Varna dig för nätverksbaserade hot både på slutpunkten och nätverk

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)


### <a name="logging"></a>Loggning
Loggning på en nivå är en funktion för alla scenariot säkerhet för. I Azure, kan du logga information som erhålls för Nätverkssäkerhetsgrupper att hämta nätverksnivån loggar information. Med NSG loggning får du information från:

* [Aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) – de här loggarna används för att visa alla åtgärder som skickats till din Azure-prenumerationer. Dessa loggar är aktiverade som standard och kan användas i Azure-portalen. De tidigare kallas ”granskningsloggar” eller ”Arbetsloggarna”.
* Händelseloggar – de här loggarna ger information om vilka NSG-regler har tillämpats.
* Räknare loggar – de här loggarna talar om hur många gånger varje NSG regeln har tillämpats för att neka eller Tillåt trafiken.

Du kan också använda [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), ett verktyg för kraftfulla visualisering, visa och analysera dessa loggar.

Läs mer:

* [Log Analytics för Nätverkssäkerhetsgrupper (NSG: er)](../virtual-network/virtual-network-nsg-manage-log.md)
