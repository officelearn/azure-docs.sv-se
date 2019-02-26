---
title: Säkerhetsbegrepp och kraven i Azure | Microsoft Docs
description: Den här artikeln innehåller grundläggande förklaringar om grundbegreppen nätverk säkerhet och krav och information om Azure-erbjudanden i var och en av dessa områden.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 38a69327e5aaa758dc70ca950956d0bdc8001a4f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818093"
---
# <a name="azure-network-security-overview"></a>Översikt över Azure network security

Nätverkssäkerhet kan definieras som skyddar resurser från obehörig åtkomst och attacker genom att använda kontroller för nätverkstrafik. Målet är att säkerställa att endast behöriga trafik är tillåten. Azure innehåller en stabil nätverksinfrastruktur för att stödja din program- och anslutningskrav för tjänsten. Nätverksanslutningen är mellan resurser i Azure, mellan lokala och Azure-värdbaserade resurser, och till och från internet och Azure.

Den här artikeln beskriver några av de alternativ som Azure erbjuder i området för nätverkssäkerhet. Du kan lära dig om:

* Azure-nätverk
* Åtkomstkontroll för nätverk
* Azure Firewall
* Skydda åtkomst och mellan lokala fjärranslutningar
* Tillgänglighet
* Namnmatchning
* Nätverksarkitektur i perimeternätverk (DMZ)
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Övervakning och hotidentifiering

## <a name="azure-networking"></a>Azure-nätverk

Azure kräver virtuella datorer som ska anslutas till en Azure-nätverk. Ett virtuellt nätverk är en logisk konstruktion som bygger på den fysiska Azure nätverksresurserna. Varje virtuellt nätverk är isolerat från andra virtuella nätverk. Detta säkerställer att trafik i dina distributioner inte är tillgänglig för andra Azure-kunder.

Läs mer:

* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Åtkomstkontroll för nätverk

Åtkomstkontroll för nätverk är handling för att begränsa anslutning till och från specifika enheter eller undernät inom ett virtuellt nätverk. Målet med åtkomstkontrollen för nätverk är att begränsa åtkomst till dina virtuella datorer och tjänster till godkända användare och enheter. Åtkomstkontroller baseras på beslut att tillåta eller neka anslutningar till och från din virtuella dator eller tjänst.

Azure har stöd för flera typer av åtkomstkontrollen för nätverk, till exempel:

* Layer-Klusternätverkets kontroll
* Dirigera kontroll och Tvingad tunneltrafik
* Virtuella funktioner för nätverkssäkerhet

### <a name="network-layer-control"></a>Layer-Klusternätverkets kontroll

En säker distribution kräver vissa mått på åtkomstkontroll för nätverk. Målet med åtkomstkontrollen för nätverk är att begränsa kommunikationen för virtuell dator till de nödvändiga system. Andra kommunikation tillåts.

>[!NOTE]
Storage-brandväggar beskrivs i den [Säkerhetsöversikt för Azure storage](security-storage-overview.md) artikel

#### <a name="network-security-rules-nsgs"></a>Nätverkssäkerhetsregler (NSG)

Om du behöver grundläggande nivå nätverksåtkomstkontroll (baserat på IP-adress och TCP eller UDP-protokoll), kan du använda Nätverkssäkerhetsgrupper (NSG). En NSG är en grundläggande, tillståndskänslig, paket filtrering brandvägg och gör att du kan styra åtkomst baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple). NSG: er är funktioner för att förenkla hanteringen och minska risken för konfiguration av misstag:

* **Förhöjda säkerhetsregler** förenkla NSG Regeldefinitionen och låter dig skapa avancerade regler i stället för att behöva skapa flera enkla regler för att uppnå samma resultat.
* **Tjänsttaggar** skapas Microsoft etiketter som representerar en grupp med IP-adresser. De uppdateras dynamiskt om du vill inkludera IP-adressintervall som uppfyller villkoren som definierar inkludering i etiketten. Om du vill skapa en regel som gäller för alla Azure-lagring i region Öst använda du till exempel Storage.EastUS
* **Programsäkerhetsgrupper** kan du distribuera resurser till programgrupper och styra åtkomsten till dessa resurser genom att skapa regler som använder dessa programgrupper. Om du har webservers som distribuerats till 'Webservers' programgruppen skapa du till exempel en regel som gäller en NSG som tillåter 443 trafik från Internet till alla system i programgruppen 'Webservers'.

NSG: er ger inte programmet layer inspektion eller autentiserad åtkomstkontroller.

Läs mer:

* [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC just-in-time-åtkomst till virtuell dator

[Azure security center](../security-center/security-center-intro.md) kan hantera Nätverkssäkerhetsgrupper på virtuella datorer och låsa åtkomsten till den virtuella datorn förrän en användare med den lämpliga rollbaserad åtkomstkontrollen [RBAC](../role-based-access-control/overview.md) behörigheter begär åtkomst. När användaren har är gör auktoriserade ASC ändringar i NSG: er att tillåta åtkomst till valda portar för den tid som anges. När tiden upphör att gälla återställs NSG: erna till sina tidigare skyddat tillstånd.

Läs mer:

* [Azure Security Center Just-in-Time-åtkomst](../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Tjänstslutpunkter

Tjänstslutpunkter är ett annat sätt att få kontroll över din trafik. Du kan begränsa kommunikationen med tjänster som stöds till bara dina virtuella nätverk via en direktanslutning. Trafik från ditt VNet till den angivna Azure-tjänsten finns kvar i Microsoft Azure-stamnätverket.  

Läs mer:

* [Serviceslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md#securing-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Dirigera kontroll och Tvingad tunneltrafik

Möjlighet att styra routning beteende på dina virtuella nätverk är viktigt. Om routning är felaktigt konfigurerad, kan program och tjänster som finns på den virtuella datorn ansluta till obehöriga enheter, inklusive system ägs och drivs av potentiella angripare.

Azure-nätverk stöder möjligheten att anpassa beteendet routning för nätverkstrafik på dina virtuella nätverk. På så sätt kan du ändra standardvärdet routning tabellposter i ditt virtuella nätverk. Kontroll över routning beteende kan du se till att all trafik från en viss enhet eller enhetsgrupp anländer till eller lämnar det virtuella nätverket via en specifik plats.

Du kan till exempel har en virtuell nätverkssäkerhetsinstallation i det virtuella nätverket. Du vill se till att all trafik till och från ditt virtuella nätverk går igenom den virtuella säkerhetsapparat. Du kan göra detta genom att konfigurera [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) (Udr) i Azure.

[Tvingad tunneltrafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts att initiera en anslutning till enheter på internet. Observera att detta skiljer sig från att acceptera inkommande anslutningar och sedan svara på dem. Frontend-webbservrar måste svara på begäranden från internet-värdar och så internet källkod trafik tillåts inkommande till dessa webbservrar och webbservrarna ska kunna svara.

Vad du inte vill tillåta är en klientwebbserver för att starta en utgående begäran. Sådana begäranden kan representera en säkerhetsrisk eftersom dessa anslutningar kan användas för att hämta skadlig kod. Även om du vill att dessa frontservrar initierar utgående begäranden till internet, kan du tvinga dem att gå igenom dina lokala webbproxyservrar. På så sätt kan du dra nytta av URL-filtrering och loggning.

I stället kan du använda Tvingad tunneltrafik för att förhindra detta. När du aktiverar Tvingad tunneltrafik kan tvingas alla anslutningar till internet via en lokal gateway. Du kan konfigurera Tvingad tunneltrafik genom att utnyttja udr: er.

Läs mer:

* [Vad är användardefinierade vägar och IP-vidarebefordring](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuella funktioner för nätverkssäkerhet

NSG, udr-vägar och Tvingad tunneltrafik tillhandahåller en säkerhetsnivå för Nätverks- och transport-lager av den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), du kanske också vill aktivera säkerhet på programnivå som är högre än nätverket.

Till exempel kan dina säkerhetskrav innehålla:

* Autentisering och auktorisering innan åtkomst ges till ditt program
* Intrångsdetektion och intrång svar
* Application layer-kontroll för övergripande protokoll
* URL-filtrering
* Nätverk på virus och skadlig kod
* Anti bot-skydd
* Åtkomstkontroll för programmet
* Ytterligare DDoS-skydd (ovanför DDoS-skydd som tillhandahålls av Azure-strukturen själva)

Du kan komma åt de här förbättrade funktioner för nätverkssäkerhet med hjälp av en Azure-partner-lösning. Du hittar det senaste Azure-partner-nätverket säkerhetslösningar genom att besöka den [Azure Marketplace](https://azure.microsoft.com/marketplace/), och söka efter ”säkerhet” och ”nätverkssäkerhet”.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en helt tillståndskänslig brandvägg som en tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. Vissa funktioner:

* Hög tillgänglighet
* Skalbarhet i molnet
* Programmets FQDN-filtreringsregler
* Regler för filtrering av nätverkstrafik

Läs mer:

* [Översikt över Azure brandvägg](../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Skydda åtkomst och mellan lokala fjärranslutningar

Installation, konfiguration och hantering av Azure-resurser behöver göras via en fjärranslutning. Dessutom kanske du vill distribuera [hybrid-IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) lösningar som har komponenter lokalt och i det offentliga Azure-molnet. De här scenarierna kräver säker fjärråtkomst.

Azure-nätverk stöder de följande scenarierna för säker fjärråtkomst:

* Ansluta enskilda arbetsstationer till ett virtuellt nätverk
* Ansluta ditt lokala nätverk till ett virtuellt nätverk med en VPN-anslutning
* Ansluta ditt lokala nätverk till ett virtuellt nätverk med en dedikerad WAN-länk
* Ansluta virtuella nätverk till varandra

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Ansluta enskilda arbetsstationer till ett virtuellt nätverk

Du kanske vill aktivera enskilda utvecklare eller anställda att hantera virtuella datorer och tjänster i Azure. Anta exempelvis att du behöver åtkomst till en virtuell dator i ett virtuellt nätverk. Men säkerhetsprinciperna tillåter inte RDP eller SSH fjärråtkomst till enskilda virtuella datorer. I det här fallet kan du använda en [punkt-till-plats VPN](../vpn-gateway/point-to-site-about.md) anslutning.

Punkt-till-plats VPN-anslutningen kan du skapa en privat och säker anslutning mellan användaren och det virtuella nätverket. När VPN-anslutningen har upprättats kan användaren kan RDP eller SSH via VPN-anslutning till en virtuell dator på det virtuella nätverket. (Detta förutsätter att användaren kan autentiseras och auktoriseras.) Punkt-till-plats-VPN stöder:

* Secure Socket Tunneling Protocol (SSTP), en SSL-baserad VPN-protokoll. En SSL VPN-lösning kan ta sig förbi brandväggar, eftersom de flesta brandväggar öppnar TCP-port 443, som använder SSL. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Läs mer:

* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Ansluta ditt lokala nätverk till ett virtuellt nätverk med en VPN-anslutning

Du kanske vill ansluta hela företagsnätverket, eller delar av det, till ett virtuellt nätverk. Detta är vanligt i hybrid-IT scenarier där organisationer [utöka sina lokala datacenter till Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). I många fall vara delar av en tjänst i Azure, och delar en lokal värd för organisationer. De kan till exempel göra det när en lösning innehåller frontend-webbservrar i Azure och backend-databaser på plats. Dessa typer av ”anslutningar på flera platser” också göra hantering av Azure finns resurser blir säkrare och aktivera scenarier, till exempel utöka Active Directory-domänkontrollanter i Azure.

Ett sätt att åstadkomma detta är att använda en [plats-till-plats VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Skillnaden mellan en plats-till-plats-VPN och en punkt-till-plats-VPN är att det senare ansluter en enhet till ett virtuellt nätverk. En plats-till-plats-VPN ansluter hela nätverket (till exempel ditt lokala nätverk) till ett virtuellt nätverk. Plats-till-plats VPN: er till ett virtuellt nätverk använder mycket säkra IPSec-tunnelläge VPN-protokoll.

Läs mer:

* [Skapa ett VNet i Resource Manager med en plats-till-plats VPN-anslutning med Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Om VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Ansluta ditt lokala nätverk till ett virtuellt nätverk med en dedikerad WAN-länk

Punkt-till-plats och plats-till-plats VPN-anslutningar är effektiva för att aktivera anslutning mellan olika platser. Men anses vissa organisationer ha nackdelar:

* VPN-anslutningar kan du flytta data via internet. Detta visar dessa anslutningar till potentiella säkerhetsproblem med flytta data via ett offentligt nätverk. Dessutom kan tillförlitlighet och tillgänglighet för internet-anslutningar inte garanteras.
* VPN-anslutningar till virtuella nätverk kanske inte har bandbredden för vissa program och betecknas som de maximala ut med cirka 200 Mbit/s.

Organisationer som behöver den högsta möjliga säkerhet och tillgänglighet för sina anslutningar mellan lokala normalt använda dedicerade WAN-länkar för att ansluta till fjärranslutna platser. Azure ger dig möjlighet att använda en dedikerad WAN-länken som du kan använda för att ansluta ditt lokala nätverk till ett virtuellt nätverk. Azure ExpressRoute, med hjälp av Express route direct och Express route global räckvidd aktivera det här alternativet.

Läs mer:

* [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md)
* [ExpressRoute direkt](../expressroute/expressroute-erdirect-about.md)
* [Express route global räckvidd](..//expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Ansluta virtuella nätverk till varandra

Det är möjligt att använda flera virtuella nätverk för dina distributioner. Det finns olika orsaker till varför du kan göra detta. Du kanske vill förenkla hanteringen eller du ökad säkerhet. Oavsett skälet till att placera resurser på olika virtuella nätverk, kan det finnas tillfällen när du vill att resurser på var och en av de nätverk som ska anslutas till varandra.

Ett alternativ är för tjänster på ett virtuellt nätverk för att ansluta till tjänster på ett annat virtuellt nätverk genom att ”loopa tillbaka” via internet. Anslutningen startar på ett virtuellt nätverk, går via internet och sedan kommer tillbaka till det virtuella nätverket för målet. Det här alternativet visar anslutningen till säkerhetsfrågorna i alla internet-baserad kommunikation.

Ett bättre alternativ kan vara att skapa en plats-till-plats-VPN som ansluter mellan två virtuella nätverk. Den här metoden använder samma [IPSec-tunnelläge](https://technet.microsoft.com/library/cc786385.aspx) protokoll som nämns ovan mellan lokala plats-till-plats VPN-anslutningen.

Fördelen med den här metoden är att VPN-anslutningen har upprättats över Azure nätverksinfrastruktur, i stället för att ansluta via internet. Detta ger dig ett extra lager av säkerhet, jämfört med plats-till-plats-VPN som ansluter via internet.

Läs mer:

* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ett annat sätt att ansluta dina virtuella nätverk är [VNET-peering](../virtual-network/virtual-network-peering-overview.md). Den här funktionen kan du ansluta två Azure-nätverk så att kommunikationen mellan dem sker via Microsoft-stamnätsinfrastrukturen utan det någonsin gå via Internet. VNET-peering kan ansluta två virtuella nätverk inom samma region eller två virtuella nätverk i Azure-regioner. NSG: er kan användas för att begränsa anslutning mellan olika undernät eller system.

## <a name="availability"></a>Tillgänglighet

Tillgänglighet är en viktig del av alla säkerhetsprogram. Om dina användare och system inte kan komma åt vad de behöver att komma åt över nätverket, tjänsten kan ses äventyras. Azure har nätverkstekniker som har stöd för följande mekanismer för hög tillgänglighet:

* HTTP-baserade belastningsutjämning
* Nivå för belastningsutjämning
* Global belastningsbalansering

Utjämning av nätverksbelastning är en mekanism som utformats för att fördela jämnt anslutningar mellan flera enheter. Målen för Utjämning av nätverksbelastning är:

* Att öka tillgängligheten. När du läser in saldo anslutningar på flera enheter, kan en eller flera av enheterna som vara tillgänglig utan att kompromissa med tjänsten. De tjänster som körs på de återstående online enheterna kan fortsätta att leverera innehåll från tjänsten.
* Att öka prestandan. När du läser in saldo anslutningar på flera enheter har inte en enskild enhet att hantera all bearbetning. I stället och minnesresurser kraven för att betjäna innehållet är fördelade på flera enheter.

### <a name="http-based-load-balancing"></a>HTTP-baserade belastningsutjämning

Organisationer som kör webbaserade tjänster ofta vilja ha en HTTP-baserad belastningsutjämnare framför de webbtjänsterna. Detta hjälper till att säkerställa tillräcklig nivåer av prestanda och hög tillgänglighet. Traditionella, nätverksbaserade belastningsutjämnare är beroende av nätverks- och transport layer-protokoll. HTTP-baserad belastningsutjämnare, å andra sidan, fatta beslut utifrån egenskaperna för HTTP-protokollet.

Azure Application Gateway erbjuder belastningsutjämning för dina webbtjänster i HTTP-baserade. Application Gateway stöder:

* Cookie-baserad sessionstillhörighet. Den här funktionen ser till att anslutningar till en av servrarna bakom den belastningsutjämnaren förblir intakta mellan klienten och servern. Detta säkerställer stabiliteten för transaktioner.
* SSL-avlastning. När en klient ansluter med belastningsutjämnaren, är den aktuella sessionen krypterad med hjälp av HTTPS (SSL)-protokollet. För att öka prestanda använda du dock HTTP (okrypterat)-protokollet för att ansluta mellan belastningsutjämnare och webbservern bakom belastningsutjämnaren. Detta kallas ”SSL-avlastning”, eftersom webbservrar bakom belastningsutjämnaren inte stöter på processor arbetet med kryptering. Webbservrar kan därför hanterar begäranden snabbt.
* URL-baserad innehållsroutning. Den här funktionen gör det möjligt för belastningsutjämnaren för att fatta beslut om var du vill vidarebefordra anslutningar baserat på mål-URL. Detta ger mycket bättre flexibilitet än lösningar som gör att läsa in belastningsutjämning beslut baserat på IP-adresser.

Läs mer:

* [Översikt över Application Gateway](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Nivå för belastningsutjämning

Till skillnad från HTTP-baserade belastningsutjämning, gör belastningsutjämning i nivån beslut baserat på IP-adress och port (TCP eller UDP) nummer.
Du kan få fördelarna med nivån för belastningsutjämning i Azure med hjälp av Azure Load Balancer. Några viktiga egenskaper för belastningsutjämnare är:

* Belastningsutjämning i nivån baserat på IP-adress och port nummer.
* Stöd för alla application layer protocol.
* Belastningsutjämnar virtuella datorer i Azure och instanser av molntjänstroll.
* Kan användas för både webbservergrupper på internet (extern belastningsutjämning) och icke-internet (intern belastningsutjämning) program och virtuella datorer som riktas mot.
* Slutpunkt för övervakning, som används för att avgöra om någon av tjänsterna bakom belastningsutjämnaren har blivit otillgänglig.

Läs mer:

* [Internetuppkopplad belastningsutjämnare mellan flera virtuella datorer eller tjänster](../load-balancer/load-balancer-internet-overview.md)
* [Översikt över intern belastningsutjämnare](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Global belastningsbalansering

Vissa organisationer vill det möjligt för högsta möjliga tillgänglighet. Ett sätt att nå detta mål är att hantera program i globalt distribuerade datacenter. När ett program finns i datacenter runtom i världen, är det möjligt för en hel geopolitiska region blir otillgänglig och fortfarande ha programmet upp och körs.

Den här strategin för belastningsutjämning kan även ge prestandafördelarna. Du kan dirigera begäranden för tjänsten till de datacenter som ligger närmast den enhet som ansvarar för att göra begäran.

I Azure, kan du få fördelarna med globala belastningsutjämning med hjälp av Azure Traffic Manager.

Läs mer:

* [Vad är Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Namnmatchning

Namnmatchningen är en viktig funktion för alla tjänster du ha i Azure. Från ett säkerhetsperspektiv kan kompromettering av funktionen name resolution leda till en angripare omdirigera begäranden från dina webbplatser till en angripares webbplats. Säker namnmatchningen är ett krav för alla dina tjänster i molnet.

Det finns två typer av namnmatchning som du måste uppfylla:

* Intern namnmatchning. Det här används av tjänsterna i ditt virtuella nätverk eller den lokala nätverk. Namn som används för intern namnmatchning är inte tillgängliga via internet. För optimal säkerhet är det viktigt att dina interna namnet lösning schema inte är tillgänglig för externa användare.
* Externa namnmatchning. Detta används av personer och enheter utanför ditt lokala nätverk och virtuella nätverk. Det här är de namn som är synlig på internet och som används för att dirigera anslutning till dina molnbaserade tjänster.

För intern namnmatchning har du två alternativ:

* En DNS-server för virtuellt nätverk. När du skapar ett nytt virtuellt nätverk, skapas en DNS-server åt dig. Den här DNS-servern kan matcha namnen på de datorer som finns i det virtuella nätverket. Den här DNS-servern kan inte konfigureras, hanteras av hanteraren för Azure-infrastrukturen och kan därför hjälpa dig att skydda din name resolution-lösning.
* Ta med din egen DNS-server. Du har möjlighet att placera en DNS-server om du väljer själv i det virtuella nätverket. Den här DNS-servern kan vara en Active Directory-integrerad DNS-server eller en särskild DNS serverlösning som tillhandahålls av en Azure-partner som du kan hämta från Azure Marketplace.

Läs mer:

* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)
* [Hantera DNS-servrar som används av ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#change-dns-servers)

För externa namnmatchning har du två alternativ:

* Vara värd för dina egna externa DNS-server lokalt.
* Vara värd för externa DNS-servern med en tjänstleverantör.

Stora organisationer vara värd för sina egna DNS-servrar på plats. De kan göra detta eftersom de har nätverk expertis och global närvaro gör.

I de flesta fall är det bättre att vara värd för dina DNS-tjänst med en tjänstleverantör. Dessa leverantörer har nätverk expertis och global närvaro så mycket hög tillgänglighet för din tjänst. Tillgänglighet är nödvändigt för DNS-tjänster, eftersom om din namnmatchningstjänster misslyckas, ingen kommer att kunna nå tjänster som riktas mot internet.

Azure ger dig en hög tillgänglighet och hög prestanda externa DNS-lösning i form av Azure DNS. Den här lösningen för matchning av externa namn drar nytta av över hela världen Azure DNS-infrastrukturen. Det kan du använda din domän i Azure, med samma autentiseringsuppgifter, API: er, verktyg och fakturering som för dina andra Azure-tjänster. Som en del av Azure ärver även de starka säkerhetskontroller som är inbyggd i plattformen.

Läs mer:

* [Översikt över Azure DNS](../dns/dns-overview.md)
* [Azure privata DNS-zoner](../dns/private-dns-overview.md) kan du konfigurera i stället automatiskt tilldelade namnen utan att behöva lägga till en anpassad DNS-lösning för privata DNS-namn för Azure-resurser.

## <a name="perimeter-network-architecture"></a>Nätverksarkitektur i perimeternätverket

Stora organisationer använda perimeternätverk för att segmentera sina nätverk och skapa buffertzoner mellan internet och sina tjänster. Perimeter-delen av nätverket anses en zon med låg säkerhet och inga värdefulla tillgångar är placerade i det nätverkssegmentet. Normalt ser du security nätverksenheter som har ett nätverksgränssnitt i perimeternätverket nätverkssegment. Ett annat nätverksgränssnitt är ansluten till ett nätverk som har virtuella datorer och tjänster som tar emot inkommande anslutningar från internet.

Du kan utforma perimeternätverk i ett antal olika sätt. Beslut att distribuera ett perimeternätverk och vilken typ av perimeternätverket nätverk ska användas om du vill använda en, beror på dina krav på säkerhet.

Läs mer:

* [Microsoft-molntjänster och nätverkssäkerhet](../best-practices-network-security.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Distribuerade överbelastningsattacker (DDoS) är några av de största tillgänglighets- och säkerhetsproblemen för kunder som flyttar sina program till molnet. DDoS-attacker försöker att få slut på ett programs resurser, som gör programmet tillgängligt för behöriga användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet.
Microsoft tillhandahåller DDoS protection kallas **grundläggande** som en del av Azure-plattformen. Detta kommer utan kostnad och omfattar alltid på övervaknings- och i realtid minskning av vanliga på nätverksnivå. Förutom de skydd som ingår i DDoS protection **grundläggande** kan du aktivera den **Standard** alternativet. DDoS Protection standardfunktioner omfattar:

* **Inbyggd plattformsintegrering:** Inbyggt i Azure. Innehåller konfiguration via Azure portal. DDoS Protection Standard förstår dina resurser och resurskonfigurationer.
* **NYCKELFÄRDIGT skydd:** Förenklad konfiguration skyddar direkt alla resurser i ett virtuellt nätverk när DDoS Protection Standard är aktiverat. Det krävs inga åtgärder eller användaren definition. DDoS Protection Standard attacken omedelbart och automatiskt, när den identifieras.
* **Ständigt aktiverad övervakning:** Din mönster i programtrafiken övervakas 24 timmar per dag, 7 dagar i veckan letar du efter indikatorer för DDoS-attacker. Minskning utförs när appskyddsprinciper överskrids.
* **Angrepp minskning rapporter** Attack minskning rapporter använder aggregerade flow nätverksdata som ger detaljerad information om attacker som är riktad mot dina resurser.
* **Angrepp minskning Flow loggar** Attack minskning Flow loggar kan du granska förlorad trafik vidarebefordras trafiken och andra attacker data i realtid under ett pågående DDoS-angrepp.
* **Anpassningsbar justering:** Intelligent trafik profilering lär sig programmets trafik över tid, och väljer och uppdaterar den profil som är mest lämplig för din tjänst. Profilen justerar allt trafik ändras med tiden. Nivå 3 till nivå 7-skydd: Ger fullständig stack DDoS-skydd, när det används med en brandvägg för webbaserade program.
* **Omfattande minskning skala:** Över 60 olika angreppstyper kan undvikas med globala kapacitet att skydda mot största kända DDoS-attacker.
* **Attack mått:** Summerade mått från varje attack är tillgängliga via Azure Monitor.
* **Attack varning:** Aviseringar kan konfigureras vid start och stopp av ett angrepp, och med hjälp av inbyggda attack mätvärden över den attack varaktighet. Aviseringar integrera i din operational programvara som Microsoft Azure Log Analytics, Splunk, Azure Storage, e-post och Azure-portalen.
* **Kostnad garanti:**  Överföring av replikeringsdata och programmet skalbar servicekrediter för dokumenterad DDoS-attacker.
* **DDoS snabba dynamiska** DDoS Protection Standard-kunder har nu tillgång till Rapid Response team under ett pågående angrepp. DRR kan hjälpa dig med attack undersökningar, anpassade åtgärder under en attack och efter attack analys.


Läs mer:

* [DDOS protection-översikt](../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Azure ytterdörren-tjänsten kan du definiera, hantera och övervaka globala routning av Internet-trafik. Detta optimerar routning av din nätverkstrafik för bästa prestanda och hög tillgänglighet. Med Azure Front Door kan du skapa anpassade regler för brandväggen för webbaserade program (WAF) vid åtkomstkontroll, för att skydda din HTTP/HTTPS-arbetsbelastning från utnyttjande som baseras på klienternas IP-adresser, landskod och HTTP-parametrar. Dessutom ytterdörren också kan du skapa begränsar regler för att beprövad skadlig robottrafik frekvensbegränsningen, den innehåller SSL-avlastning och per-HTTP/HTTPS-begäran, programnivå-bearbetning.

Själva ytterdörren plattformen skyddas av Azure DDoS Protection Basic. Om du behöver mer skydd kan Azure DDoS Protection Standard aktiveras på dina virtuella nätverk och skydda resurser från nätverkslagerattacker (TCP/UDP) med hjälp av automatiska justeringar och åtgärder. Är ytterdörren en omvänd proxy för layer 7, tillåter endast Internet-trafik passerar genom för att säkerhetskopiera sluta servrar och blockera andra typer av trafik som standard.

Läs mer:

* Mer information om hela uppsättningen med Azure främre luckan är funktioner som du kan granska den [ytterdörren för Azure-översikt](../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Azure Traffic manager

Azure Traffic Manager är en belastningsutjämnare för DNS-baserad trafik som hjälper dig att distribuera trafiken optimalt till tjänster i globala Azure-regioner, med hög tillgänglighet och korta svarstider. Traffic Manager använder DNS för att dirigera klientbegäranden till den lämpligaste tjänstslutpunkten baserat på en trafikdirigeringsmetod och slutpunkternas hälsostatus. En slutpunkt är en Internetansluten tjänst i eller utanför Azure. Traffic manager övervakar slutpunkter och dirigera inte trafik till alla slutpunkter som inte är tillgängliga.

Läs mer:

* [Azure Traffic manager-översikt](../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Övervakning och hotidentifiering

Azure tillhandahåller funktioner som hjälper dig i det här viktiga området med tidig upptäckt, övervakning, och samla in och granska nätverkstrafik.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher kan hjälpa dig att felsöka, och ger en helt ny uppsättning av verktyg som hjälper till med identifiering av säkerhetsproblem.

[Säkerhetsgrupp visa](../network-watcher/network-watcher-security-group-view-overview.md) hjälper till med gransknings- och kompatibiliteten för virtuella datorer. Använd denna funktion för att utföra programmässiga granskningar, jämföra baslinje-principer som definierats av din organisation att gällande regler för var och en av dina virtuella datorer. Detta kan hjälpa dig att identifiera eventuella konfigurationsförändringar.

[Paketfångsten](../network-watcher/network-watcher-packet-capture-overview.md) låter dig fånga nätverkstrafik till och från den virtuella datorn. Du kan samla in nätverksstatistik och felsöka programproblem som kan vara ovärderliga i undersökningen av intrång i nätverket. Du kan också använda den här funktionen tillsammans med Azure Functions för att starta nätverksinsamlingar som svar på specifika Azure-aviseringar.

Läs mer om Network Watcher och hur du startar testerna några av funktionerna i dina labb [Azure network watcher Övervakningsöversikt](../network-watcher/network-watcher-monitoring-overview.md).

>[!NOTE]
Aktuell information om tillgänglighet och status för den här tjänsten, kontrollera den [uppdateringar i Azure-sidan](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center hjälper dig att förhindra, upptäcka och svara på hot och ger dig ökad insyn i, och kontroll över säkerheten hos dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer, upptäcka hot som kan annars oupptäckta och fungerar med ett stort antal säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka nätverkssäkerhet genom att:

* Att tillhandahålla säkerhetsrekommendationer för nätverk.
* Övervaka tillståndet i konfigurationen av din nätverkssäkerhet.
* Varna dig för nätverk baserade hot, både på slutpunkten och nätverk.

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>Virtual Network TAP

Azure-nätverk TRYCK (Terminal åtkomstpunkt) kan du kontinuerligt stream dina VM-nätverkstrafik till en insamlare eller analytics verktyg för nätverkspaket. Insamlare eller analysverktyg tillhandahålls av en partner för virtuell installation av nätverket. Du kan använda samma virtuella nätverk trycker du på resursen att samla trafik från flera nätverksgränssnitt i samma eller olika prenumerationer.

Läs mer:

* [Virtuell nätverks-TAP](../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Loggning

Loggning på nätverksnivå är en viktig funktion för alla scenarier för nätverk säkerhet. I Azure, kan du logga information som erhålls för NSG: er att hämta nätverksnivån loggar information. Med NSG loggning får du information från:

* [Aktivitetsloggar](../azure-monitor/platform/activity-logs-overview.md). Använd de här loggarna om du vill visa alla åtgärder som har skickats till din Azure-prenumerationer. Dessa loggar är aktiverade som standard och kan användas i Azure-portalen. De tidigare kallas gransknings- eller driftloggar.
* Händelseloggar. De här loggarna ger information om vilka NSG-regler har tillämpats.
* Räknarloggar. Dessa loggar att du vet hur många gånger varje NSG-regel har tillämpats för att neka eller tillåta trafik.

Du kan också använda [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), ett kraftfullt datavisualiseringsverktyg, visa och analysera dessa loggar.
Läs mer:

* [Log Analytics för Nätverkssäkerhetsgrupper (NSG)](../virtual-network/virtual-network-nsg-manage-log.md)
