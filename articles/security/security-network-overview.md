---
title: Säkerhetsbegrepp och kraven i Azure | Microsoft Docs
description: Den här artikeln innehåller grundläggande information om grundläggande koncept för säkerhet och krav och information om Azure erbjuder i dessa olika områden.
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
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: fbd589aedb955ee4bd61dc0ec754d8713a98179a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365635"
---
# <a name="azure-network-security-overview"></a>Översikt över säkerheten i Azure-nätverk
Azure innehåller en stabil nätverksinfrastruktur för att stödja dina program och tjänsten anslutningskrav. Nätverksanslutningen är möjlig mellan resurser i Azure, mellan lokala och Azure värdbaserade resurser, och till och från internet och Azure.

Syftet med den här artikeln är att förklara vad Azure erbjuder i området för nätverkssäkerhet. Du kan lära dig om förutom grundläggande information om grundläggande koncept för säkerhet och krav:

* Azure-nätverk
* Åtkomstkontrollen för nätverk
* Säker åtkomst och mellan platser för fjärranslutningar
* Tillgänglighet
* Namnmatchning
* Perimeternätverk nätverksarkitekturen (DMZ)
* Övervakning och hotidentifiering

## <a name="azure-networking"></a>Azure-nätverk
Virtuella datorer måste nätverksanslutningen. För att uppfylla detta krav, kräver Azure virtuella datorer måste vara ansluten till Azure Virtual Network. Ett virtuellt nätverk är en logisk konstruktion som bygger på fysisk Azure nätverksinfrastruktur. Varje logiskt virtuella nätverk är isolerad från andra virtuella nätverk. Detta säkerställer att nätverkstrafik i din distribution inte är tillgänglig för andra Azure-kunder.

Läs mer:

* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Åtkomstkontrollen för nätverk
Åtkomstkontrollen för nätverk är att begränsa anslutning till och från specifika enheter och undernät i ett virtuellt nätverk. Målet med åtkomstkontrollen för nätverk är att begränsa åtkomst till virtuella datorer och tjänster till godkända användare och enheter. Åtkomstkontroller baseras på beslut att tillåta eller neka anslutningar till och från en virtuell dator eller tjänst.

Azure har stöd för flera typer av åtkomstkontrollen för nätverket som:

* Kontrollen av nätverkets lager
* Dirigera kontroll och Tvingad tunneltrafik
* Säkerhetsenheter för virtuellt nätverk

### <a name="network-layer-control"></a>Kontrollen av nätverkets lager
En säker distribution kräver vissa mått av åtkomstkontrollen för nätverket. Målet med åtkomstkontrollen för nätverk är att begränsa virtuella kommunikation till system som behövs. Andra kommunikation tillåts.

Om du behöver grundläggande nivån nätverksåtkomstkontroll (baserat på IP-adress och TCP- eller UDP-protokoll) kan använda du Nätverkssäkerhetsgrupper (NSG: er). En NSG är en grundläggande, stateful, paket filtrering brandväggen och du kan styra åtkomsten baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple). NSG: er ger inte programmet layer inspektion eller autentiserad åtkomstkontroller.

Läs mer:

* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md)

### <a name="route-control-and-forced-tunneling"></a>Dirigera kontroll och Tvingad tunneltrafik
Möjlighet att styra dirigeringsbeteendet på dina virtuella nätverk är viktigt. Om routning är felaktigt konfigurerad, kan program och tjänster som finns på den virtuella datorn ansluta till obehöriga enheter, inklusive system ägs och drivs av potentiella angripare.

Azure nätverk stöder möjligheten att anpassa dirigeringsbeteendet för nätverkstrafik i ditt virtuella nätverk. På så sätt kan du ändra standardvärdet routning tabellposter i ditt virtuella nätverk. Kontroll av dirigeringsbeteendet hjälper dig att se till att all trafik från en viss enhet eller grupp av enheter anländer till eller lämnar det virtuella nätverket via en viss plats.

Till exempel kanske en virtuell nätverksenhet för säkerhet på det virtuella nätverket. Du vill kontrollera att all trafik till och från det virtuella nätverket går genom den virtuella postsäkerhet. Du kan göra detta genom att konfigurera [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) (udr: er) i Azure.

[Tvingad tunneltrafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts att initiera en anslutning till enheter på internet. Observera att detta skiljer sig från att acceptera inkommande anslutningar och sedan svara på dem. Frontend-webbservrar måste svara på begäranden från internet-värdar och så internet-källkod trafik tillåts inkommande till dessa servrar och webbservrar tillåts att svara.

Vad du inte vill tillåta är en klientwebbserver för att starta en utgående begäran. Sådana begäranden kan utgöra en säkerhetsrisk eftersom dessa anslutningar som kan användas för att hämta skadlig kod. Även om du vill att dessa servrar att initiera utgående förfrågningar till internet, kanske du vill tvinga dem att gå igenom din lokala webbproxyservrar. På så sätt kan du dra nytta av URL-filtrering och loggning.

Du kan i stället att använda Tvingad tunneltrafik för att förhindra detta. När du aktiverar Tvingad tunneling tvingas alla anslutningar till internet via ditt lokala gateway. Du kan konfigurera Tvingad tunneling genom att utnyttja udr: er.

Läs mer:

* [Vad är användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Säkerhetsenheter för virtuellt nätverk
Medan NSG: er, udr: er och Tvingad tunneling ger en hög säkerhetsnivå vid nätverks- och lager i den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), du kanske också vill aktivera säkerhet på programnivå som är högre än nätverket.

Till exempel kan ditt säkerhetskrav innehålla:

* Autentisering och auktorisering innan åtkomst beviljas till ditt program
* Identifiering av adressintrång och intrångsidentifiering svar
* Programmet layer-kontroll för övergripande protokoll
* URL-filtrering
* Nätverket nivån antivirus eller antimalware
* Skydd mot bot
* Åtkomstkontroll för programmet
* Ytterligare DDoS-skydd (ovanför DDoS-skydd som tillhandahålls av Azure-strukturen själva)

Du kan komma åt dessa förbättrade funktioner för nätverkssäkerhet med hjälp av en Azure partnerlösning. Du hittar den senaste Azure partnernätverk säkerhetslösningar genom att besöka den [Azure Marketplace](https://azure.microsoft.com/marketplace/), och söka efter ”säkerhet” och ”nätverkssäkerhet”.

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Säker åtkomst och mellan platser för fjärranslutningar
Installation, konfiguration och hantering av Azure-resurser behöver göras via fjärranslutning. Dessutom kanske du vill distribuera [hybrid IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) lösningar som har komponenter på lokalt och i det offentliga Azure-molnet. Dessa scenarier kräver säker fjärråtkomst.

Azure nätverk stöder följande scenarion för säker fjärråtkomst:

* Enskilda arbetsstationer kan anslutas till ett virtuellt nätverk
* Ansluta det lokala nätverket till ett virtuellt nätverk med en VPN-anslutning
* Ansluta det lokala nätverket till ett virtuellt nätverk med en fast WAN-länk
* Ansluta virtuella nätverk till varandra

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Enskilda arbetsstationer kan anslutas till ett virtuellt nätverk
Du kanske vill aktivera enskilda utvecklare eller åtgärder personal att hantera virtuella datorer och tjänster i Azure. Anta exempelvis att du behöver åtkomst till en virtuell dator på ett virtuellt nätverk. Men säkerhetsprinciperna tillåter inte RDP eller SSH fjärråtkomst till enskilda virtuella datorer. I det här fallet kan du använda en punkt-till-plats VPN-anslutning.

Punkt-till-plats VPN-anslutning använder den [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) protokollet så att du kan skapa en privat och säker anslutning mellan användaren och det virtuella nätverket. När VPN-anslutningen har upprättats kan användaren via RDP eller SSH via VPN-anslutning till en virtuell dator på det virtuella nätverket. (Detta förutsätter att användaren kan autentiseras och auktoriseras.)

Läs mer:

* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Ansluta det lokala nätverket till ett virtuellt nätverk med en VPN-anslutning
Du kanske vill ansluta hela företagsnätverket, eller delar av det, till ett virtuellt nätverk. Detta är vanligt i hybrid IT scenarier där företag [utöka sina lokala datacenter till Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). I många fall värd organisationer delar av en tjänst i Azure och delar på lokalt. De kan till exempel göra det när en lösning innehåller frontend-webbservrar i Azure och backend-databaser lokalt. Dessa typer av ”anslutningar mellan platser” Se också hantering av Azure finns resurser mer säker och aktivera scenarier, till exempel utöka Active Directory-domänkontrollanter i Azure.

Ett sätt att uppnå detta är att använda en [plats-till-plats VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Skillnaden mellan en plats-till-plats-VPN och en punkt-till-plats-VPN är att denna ansluter en enhet till ett virtuellt nätverk. En plats-till-plats-VPN ansluter hela nätverket (till exempel ditt lokala nätverk) till ett virtuellt nätverk. Plats-till-plats VPN till ett virtuellt nätverk används mycket säkert IPSec-tunnelläge VPN-protokoll.

Läs mer:

* [Skapa ett VNet Resource Manager med en plats-till-plats VPN-anslutning med hjälp av Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Planering och design för VPN-gateway](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Ansluta det lokala nätverket till ett virtuellt nätverk med en fast WAN-länk
Punkt-till-plats och plats-till-plats VPN-anslutningar är effektiv för att aktivera korsanslutningar. Dock anser vissa organisationer att det har följande nackdelar:

* VPN-anslutningar kan du flytta data över internet. Detta visar anslutningarna till potentiella säkerhetsproblem med flytta data över ett offentligt nätverk. Dessutom kan tillförlitlighet och tillgänglighet för internet-anslutningar inte garanteras.
* VPN-anslutningar till virtuella nätverk kanske inte bandbredd för vissa program och syften som de högsta ut vid runt 200 Mbit/s.

Organisationer som behöver den högsta nivån av säkerhet och tillgänglighet för sina anslutningar mellan platser normalt använda dedicerade WAN-länkar för att ansluta till fjärranslutna platser. Azure tillhandahåller möjligheten att använda en fast WAN-länk som du kan använda för att ansluta det lokala nätverket till ett virtuellt nätverk. Detta gör att Azure ExpressRoute.

Läs mer:

* [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-virtual-networks-to-each-other"></a>Ansluta virtuella nätverk till varandra
Det är möjligt att använda flera virtuella nätverk för din distribution. Det finns olika skäl varför du kan göra detta. Du kanske vill förenkla hanteringen eller så kanske du vill öka säkerheten. Oavsett skälet för att publicera resurser på olika virtuella nätverk, kan det finnas tillfällen när du vill att resurser på varje nätverk att ansluta till varandra.

Ett alternativ är för tjänster i ett virtuellt nätverk att ansluta till tjänster på ett annat virtuellt nätverk genom att ”loopa tillbaka” via internet. Anslutningen startar i ett virtuellt nätverk går via internet och sedan kommer tillbaka till det virtuella målnätverket. Det här alternativet visar anslutningen till säkerhetsproblem som är inbyggd i alla internet-baserad kommunikation.

Ett bättre alternativ kan vara att skapa en plats-till-plats-VPN som ansluter mellan två virtuella nätverk. Den här metoden använder samma [IPSec-tunnelläge](https://technet.microsoft.com/library/cc786385.aspx) protokoll som nämns ovan mellan lokala plats-till-plats VPN-anslutning.

Fördelen med den här metoden är att VPN-anslutningen har upprättats över Azure nätverksinfrastruktur, i stället för att ansluta via internet. Detta ger dig ett extra lager av säkerhet, jämfört med plats-till-plats VPN som ansluter via internet.

Läs mer:

* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Tillgänglighet
Tillgänglighet är en viktig del av security-programmet. Om dina användare och datorer inte kan komma åt de behöver komma åt över nätverket, tjänsten kan ses komprometteras. Azure har nätverkstekniker som stöder följande metoder för hög tillgänglighet:

* HTTP-baserade belastningsutjämning
* Nivån för belastningsutjämning
* Globala belastningsutjämning

Belastningsutjämning är en mekanism avsedd att fördela jämnt anslutningar mellan flera enheter. Målen med belastningsutjämning är:

* Att öka tillgängligheten. När du läser in saldo anslutningar på flera enheter, kan en eller flera av enheterna som vara tillgänglig utan att kompromissa med tjänsten. De tjänster som körs på de återstående online enheterna kan fortsätta att hantera innehåll från tjänsten.
* Att öka prestandan. När du läser in saldo anslutningar på flera enheter är inte en enskild enhet hanteras all bearbetning. I stället krav och minnesresurser betjänar innehållet sprids över flera enheter.

### <a name="http-based-load-balancing"></a>HTTP-baserade belastningsutjämning
Organisationer som kör webbaserade tjänster ofta vilja ha en HTTP-baserad belastningsutjämnare framför dessa webbtjänster. På så sätt försäkrar du dig lämpliga nivåer av prestanda och hög tillgänglighet. Traditionell, nätverksbaserade belastningsutjämnare förlitar sig på nätverket och transport layer-protokoll. HTTP-baserad belastningsutjämnare, se å andra sidan beslut baserat på egenskaperna för HTTP-protokollet.

Azure Application Gateway ger belastningsutjämning för dina webbaserade tjänster i HTTP-baserade. Programgateway stöder:

* Cookie-baserad session tillhörighet. Den här funktionen ser till att anslutningar till en server bakom som belastningsutjämnare intakt mellan klienten och servern. Detta säkerställer att stabiliteten för transaktioner.
* SSL-avlastning. När en klient ansluter med belastningsutjämnaren, krypteras den aktuella sessionen med hjälp av HTTPS (SSL)-protokollet. För att öka prestanda kan du dock använda protokollet HTTP (okrypterat) för att ansluta mellan belastningsutjämnaren och webbservern bakom belastningsutjämnaren. Detta kallas ”SSL-avlastning”, eftersom webbservrar bakom belastningsutjämnaren inte drabbas av processor overhead ingår i kryptering. Webbservrar kan därför serviceförfrågningar snabbare.
* URL-baserade innehåll routning. Den här funktionen gör det möjligt för belastningsutjämnaren att fatta beslut om var ska vidarebefordra anslutningar baserat på mål-URL. Detta ger mycket större flexibilitet än lösningar som gör att läsa in belastningsutjämning beslut baserat på IP-adresser.

Läs mer:

* [Översikt över Gateway](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Nivån för belastningsutjämning
Till skillnad från HTTP-baserade belastningsutjämning, fattar belastningsutjämning i nivån beslut som baseras på IP-adressen och porten (TCP eller UDP) nummer.
Du kan få fördelarna med nivå belastningsutjämning i Azure med hjälp av Azure belastningsutjämnare. Några viktiga egenskaper för belastningsutjämnare är:

* Belastningsutjämning i nivån baserat på IP-adress och port nummer.
* Stöd för alla protokoll på applikationsnivå.
* Om belastningsutjämning till Azure virtuella datorer och cloud services rollinstanser.
* Kan användas för både internet-riktade (extern belastningsutjämning) och icke-internet facing (intern belastningsutjämning) program och virtuella datorer.
* Slutpunkten som övervakning, som används för att avgöra om någon av dessa tjänster bakom belastningsutjämnaren har blivit tillgänglig.

Läs mer:

* [Internetriktade belastningsutjämnare mellan flera virtuella datorer eller tjänster](../load-balancer/load-balancer-internet-overview.md)
* [Översikt över interna belastningsutjämnare](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globala belastningsutjämning
Vissa organisationer vill den högsta nivån för tillgänglighet som möjligt. Ett sätt att uppnå det här målet är som värd för program i globalt distribuerade datacenter. När ett program finns i datacenter finns i hela världen, är det möjligt för en hel geopolitiska region att bli tillgänglig och fortfarande har programmet och körs.

Den här strategin för belastningsutjämning kan också ge prestandafördelarna. Du kan dirigera begäranden för tjänsten till datacenter som ligger närmast den enhet som begäran kommer ifrån.

I Azure, kan du få fördelarna med globala belastningsutjämning med hjälp av Azure Traffic Manager.

Läs mer:

* [Vad är Traffic Manager?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Namnmatchning
Namnmatchning är en viktig funktion för alla tjänster som du har i Azure. Från ett säkerhetsperspektiv kan röjande av funktionen name resolution leda till en angripare omdirigera begäranden från dina platser till en angripares webbplats. Säker namnmatchning är ett krav för alla molnbaserade tjänster.

Det finns två typer av namnmatchning som du behöver adressera:

* Intern namnmatchning. Det här används av tjänsterna i ditt virtuella nätverk, ditt lokala nätverk eller båda. Namn som används för intern namnmatchning för är inte tillgängliga via internet. För optimal säkerhet är det viktigt att ett internt namn upplösning schema inte är tillgänglig för externa användare.
* Externa namnmatchning. Detta används av användare och enheter utanför ditt lokala nätverk och virtuella nätverk. Dessa är de namn som är synlig på internet, och används för att dirigera anslutning till dina molnbaserade tjänster.

För intern namnmatchning har du två alternativ:

* En DNS-server för virtuellt nätverk. När du skapar ett nytt virtuellt nätverk, skapas en DNS-server åt dig. Den här DNS-servern kan matcha namnen på de datorer som finns i det virtuella nätverket. DNS-servern kan inte konfigureras, hanteras av hanteraren för Azure-strukturen och därför kan hjälpa dig att skydda din name resolution lösning.
* Skapa DNS-servern. Du har möjlighet att placera en DNS-server om du väljer själv i ditt virtuella nätverk. Den här DNS-servern kan vara en Active Directory-integrerade DNS-server eller en dedikerad DNS-server-lösning som tillhandahålls av en Azure-partner som du kan hämta från Azure Marketplace.

Läs mer:

* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)
* [Hantera DNS-servrar som används av ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#change-dns-servers)

För externa namnmatchning har du två alternativ:

* Värd för dina egna externa DNS-server lokalt.
* Värd för externa DNS-servern med en tjänstprovider.

Många stora organisationer värd sina egna DNS-servrar lokalt. De kan göra detta eftersom de har expertisen nätverk och global närvaro gör.

I de flesta fall är det bättre att vara värd för din DNS-namnmatchningstjänster med en tjänstleverantör. Dessa leverantörer har expertisen nätverk och global närvaro så mycket hög tillgänglighet för din namnmatchningstjänster. Tillgänglighet är nödvändigt för DNS-tjänster, eftersom om din namnmatchningstjänster misslyckas ingen kommer att kunna komma åt dina internet facing tjänster.

Azure tillhandahåller du med hög tillgänglighet och performant externa DNS-lösning i form av Azure DNS. Externa name resolution lösningen drar nytta av hela världen Azure DNS-infrastruktur. På så sätt kan du vara värd för din domän i Azure, med samma autentiseringsuppgifter, API: er, verktyg och fakturering som andra Azure-tjänster. Som en del av Azure ärver också starkt säkerhetsåtgärder som är inbyggda i plattformen.

Läs mer:

* [Översikt över Azure DNS](../dns/dns-overview.md)

## <a name="perimeter-network-architecture"></a>Perimeternätverk nätverksarkitektur
Många stora organisationer använder perimeternätverk för att segmentera sina nätverk och skapa en buffert zon mellan internet och sina tjänster. Perimeter-delen av nätverket anses vara en zon med låg säkerhet och inga värdefulla tillgångar placeras i det nätverkssegmentet. Du ser vanligtvis säkerhet nätverksenheter som har ett nätverksgränssnitt på perimeternätverket nätverkssegment. Ett annat nätverksgränssnitt är ansluten till ett nätverk som har virtuella datorer och tjänster som accepterar inkommande anslutningar från internet.

Du kan utforma perimeternätverk i ett antal olika sätt. Beslutet att distribuera ett perimeternätverk och vilken typ av perimeternätverk nätverk ska användas om du väljer att använda en, beror på ditt nätverkssäkerhetskrav.

Läs mer:

* [Microsoft-molntjänster och nätverkssäkerhet](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Övervakning och hotidentifiering

Azure tillhandahåller funktioner som hjälper dig i den här nyckeln området med tidig upptäckt, övervakning, och samla in och granska nätverkstrafik.

### <a name="azure-network-watcher"></a>Azure Nätverksbevakaren
Azure Nätverksbevakaren kan hjälpa dig att felsöka, och innehåller en helt ny uppsättning av verktyg för att underlätta identifieringen av säkerhetsproblem.

[Säkerhetsgrupp visa ](../network-watcher/network-watcher-security-group-view-overview.md) kan hjälpa dig med gransknings- och kompatibilitet för virtuella datorer. Använd denna funktion för att utföra programmässiga revisioner, jämföra baslinje-principer som definierats av organisationen för att effektivt regler för var och en av dina virtuella datorer. Detta kan hjälpa dig att identifiera eventuella konfigurationsavvikelser.

[Paketinsamling](../network-watcher/network-watcher-packet-capture-overview.md) kan du avbilda nätverkstrafik till och från den virtuella datorn. Du kan samla in nätverksstatistik och felsöka programproblem som kan vara ovärderlig i undersökning av intrång i nätverket. Du kan också använda den här funktionen tillsammans med Azure Functions för att starta nätverksinsamlingar som svar på specifika Azure-aviseringar.

Mer information om Nätverksbevakaren och hur du startar testerna några av funktionerna i din labs finns [Azure nätverksbevakaren översikt över övervakning](../network-watcher/network-watcher-monitoring-overview.md).

>[!NOTE]
De senaste meddelanden på tillgänglighet och status för den här tjänsten kontrollerar den [Azure uppdateringar](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center
Azure Security Center hjälper dig att förebygga, upptäcka och åtgärda hot och ger du ökad insyn i, och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, och upptäcka hot som annars kanske skulle förbli oupptäckta, fungerar med en stor mängd lösningar för informationssäkerhet.

Security Center hjälper dig att optimera och övervaka nätverkssäkerhet av:

* Att erbjuda säkerhetsrekommendationer för nätverk.
* Övervaka status för din nätverkskonfiguration för säkerhet.
* Varna dig för nätverket baserat hot, både på slutpunkten och nätverk.

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)


### <a name="logging"></a>Loggning
Loggning på en nivå är en funktion för alla scenariot säkerhet för. I Azure, kan du logga information som erhålls för NSG: er att hämta nätverksnivån loggar information. Med NSG loggning får du information från:

* [Aktivitetsloggar](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Använd de här loggarna om du vill visa alla åtgärder som skickats till din Azure-prenumerationer. Dessa loggar är aktiverade som standard och kan användas i Azure-portalen. De tidigare kallas granskningsläge eller operativa loggar.
* Händelseloggar. Dessa loggar finns information om vilka NSG-regler har tillämpats.
* Räknarloggar. Dessa loggar att du vet hur många gånger varje NSG regeln har tillämpats för att neka eller Tillåt trafiken.

Du kan också använda [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), ett verktyg för kraftfulla visualisering, visa och analysera dessa loggar.

Läs mer:

* [Log Analytics för Nätverkssäkerhetsgrupper (NSG: er)](../virtual-network/virtual-network-nsg-manage-log.md)
