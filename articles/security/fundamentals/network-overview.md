---
title: Nätverkssäkerhetskoncept och -krav i Azure | Microsoft-dokument
description: Den här artikeln innehåller grundläggande förklaringar om grundläggande nätverkssäkerhetskoncept och -krav samt information om vad Azure erbjuder i vart och ett av dessa områden.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 2293618b0685fe71ae553a95797fe8bfe1fe968c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749949"
---
# <a name="azure-network-security-overview"></a>Översikt över Azure-nätverkssäkerhet

Nätverkssäkerhet kan definieras som processen att skydda resurser från obehörig åtkomst eller angrepp genom att tillämpa kontroller på nätverkstrafik. Målet är att säkerställa att endast legitim trafik tillåts. Azure innehåller en robust nätverksinfrastruktur för att stödja dina krav på program- och tjänstanslutning. Nätverksanslutning är möjlig mellan resurser som finns i Azure, mellan lokala och Azure-värdbaserade resurser och till och från Internet och Azure.

Den här artikeln innehåller några av de alternativ som Azure erbjuder inom området nätverkssäkerhet. Du kan läsa mer om:

* Azure-nätverk
* Åtkomstkontroll för nätverk
* Azure Firewall
* Säker fjärråtkomst och anslutning mellan lokala
* Tillgänglighet
* Namnmatchning
* Perimeternätverksarkitektur (DMZ)
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Övervakning och upptäckt av hot

## <a name="azure-networking"></a>Azure-nätverk

Azure kräver att virtuella datorer är anslutna till ett Virtuellt Azure-nätverk. Ett virtuellt nätverk är en logisk konstruktion som bygger på den fysiska Azure-nätverksstrukturen. Varje virtuellt nätverk är isolerat från alla andra virtuella nätverk. Detta säkerställer att nätverkstrafik i dina distributioner inte är tillgänglig för andra Azure-kunder.

Läs mer:

* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Åtkomstkontroll för nätverk

Nätverksåtkomstkontroll är att begränsa anslutningen till och från specifika enheter eller undernät i ett virtuellt nätverk. Målet med nätverksåtkomstkontrollen är att begränsa åtkomsten till dina virtuella datorer och tjänster till godkända användare och enheter. Åtkomstkontroller baseras på beslut om att tillåta eller neka anslutningar till och från den virtuella datorn eller tjänsten.

Azure stöder flera typer av nätverksåtkomstkontroll, till exempel:

* Kontroll av nätverkslager
* Ruttkontroll och påtvingad tunnelning
* Virtuella nätverkssäkerhetsenheter

### <a name="network-layer-control"></a>Kontroll av nätverkslager

Alla säkra distributioner kräver ett visst mått på nätverksåtkomstkontroll. Målet med nätverksåtkomstkontroll är att begränsa kommunikationen för virtuella datorer till de nödvändiga systemen. Andra kommunikationsförsök blockeras.

> [!NOTE]
> Lagringsbrandväggen beskrivs i översiktsartikeln för [Azure-lagringssäkerhet](storage-overview.md)

#### <a name="network-security-rules-nsgs"></a>Regler för nätverkssäkerhet (NSG)

Om du behöver grundläggande åtkomstkontroll på nätverksnivå (baserat på IP-adress och TCP- eller UDP-protokollen) kan du använda NSG (Network Security Groups). En NSG är en grundläggande, tillståndskänslig, paketfiltrering brandvägg, och det gör att du kan styra åtkomst baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple). NSG inkluderar funktioner för att förenkla hanteringen och minska risken för konfigurationsfel:

* **Utökade säkerhetsregler** förenklar NSG-regeldefinitionen och gör att du kan skapa komplexa regler i stället för att behöva skapa flera enkla regler för att uppnå samma resultat.
* **Tjänsttaggar** är Microsoft-skapade etiketter som representerar en grupp IP-adresser. De uppdateras dynamiskt för att inkludera IP-intervall som uppfyller de villkor som definierar inkludering i etiketten. Om du till exempel vill skapa en regel som gäller för all Azure-lagring i östra regionen kan du använda Storage.EastUS
* **Med programsäkerhetsgrupper** kan du distribuera resurser till programgrupper och kontrollera åtkomsten till dessa resurser genom att skapa regler som använder dessa programgrupper. Om du till exempel har webbservrar distribuerade till programgruppen "Webbservrar" kan du skapa en regel som tillämpar en NSG som tillåter 443-trafik från Internet till alla system i programgruppen "Webbservrar".

NSG ger inte programlagerinspektion eller autentiserade åtkomstkontroller.

Läs mer:

* [Säkerhetsgrupper för nätverk](../../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC precis i tid VM-åtkomst

[Azure Security Center](../../security-center/security-center-intro.md) kan hantera NSG:erna på virtuella datorer och låsa åtkomsten till den virtuella datorn tills en användare med lämplig rollbaserad åtkomstkontroll [RBAC-behörigheter](/azure/role-based-access-control/overview) begär åtkomst. När användaren har auktoriserat ASC gör ändringar i NSGs för att tillåta åtkomst till valda portar för den angivna tiden. När tiden går ut återställs NSG:erna till sitt tidigare säkra tillstånd.

Läs mer:

* [Azure Security Center Just in Time Access](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Tjänstslutpunkter

Tjänstslutpunkter är ett annat sätt att tillämpa kontroll över din trafik. Du kan begränsa kommunikationen med tjänster som stöds till bara dina virtuella nätverk via en direktanslutning. Trafik från ditt virtuella nätverk till den angivna Azure-tjänsten finns kvar i Microsoft Azure-stamnätet.  

Läs mer:

* [Tjänstslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Ruttkontroll och påtvingad tunnelning

Möjligheten att styra routningsbeteendet i dina virtuella nätverk är avgörande. Om routning har konfigurerats felaktigt kan program och tjänster som finns på den virtuella datorn ansluta till obehöriga enheter, inklusive system som ägs och drivs av potentiella angripare.

Azure-nätverk stöder möjligheten att anpassa routningsbeteendet för nätverkstrafik i dina virtuella nätverk. På så sätt kan du ändra standardposterna för routningstabeller i det virtuella nätverket. Kontroll av routningsbeteende hjälper dig att se till att all trafik från en viss enhet eller grupp av enheter kommer in i eller lämnar ditt virtuella nätverk via en viss plats.

Du kan till exempel ha en virtuell nätverkssäkerhetsinstallation i det virtuella nätverket. Du vill vara säker på att all trafik till och från det virtuella nätverket går igenom den virtuella säkerhetsinstallationen. Du kan göra detta genom att konfigurera [användardefinierade rutter](../../virtual-network/virtual-networks-udr-overview.md) (UDRs) i Azure.

[Påtvingad tunnelning](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts initiera en anslutning till enheter på Internet. Observera att detta skiljer sig från att acceptera inkommande anslutningar och sedan svara på dem. Front-end webbservrar måste svara på förfrågningar från Internet-värdar, så internet-sourced trafik tillåts inkommande till dessa webbservrar och webbservrar får svara.

Vad du inte vill tillåta är en frontend-webbserver för att initiera en utgående begäran. Sådana begäranden kan utgöra en säkerhetsrisk eftersom dessa anslutningar kan användas för att hämta skadlig kod. Även om du vill att dessa frontend-servrar ska initiera utgående förfrågningar till Internet, kanske du vill tvinga dem att gå igenom dina lokala webb proxyservrar. På så sätt kan du dra nytta av URL-filtrering och loggning.

I stället skulle du vilja använda påtvingad tunnel för att förhindra detta. När du aktiverar tvångstunneler tvingas alla anslutningar till internet via din lokala gateway. Du kan konfigurera tvångstunneler genom att dra nytta av UDRs.

Läs mer:

* [Vad är användardefinierade vägar och IP-vidarebefordran](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuella nätverkssäkerhetsenheter

Medan NSGs, UDRs och påtvingad tunnelning ger dig en säkerhetsnivå i nätverks- och transportlager i [OSI-modellen,](https://en.wikipedia.org/wiki/OSI_model)kanske du också vill aktivera säkerhet på högre nivåer än nätverket.

Dina säkerhetskrav kan till exempel omfatta:

* Autentisering och auktorisering innan du tillåter åtkomst till ditt program
* Intrångsdetektering och intrångshantering
* Kontroll av programlager för protokoll på hög nivå
* URL-filtrering
* Antivirus på nätverksnivå och antimalware
* Skydd mot robotar
* Kontroll av programåtkomst
* Ytterligare DDoS-skydd (ovanför DDoS-skyddet som tillhandahålls av Azure-strukturen själv)

Du kan komma åt dessa förbättrade nätverkssäkerhetsfunktioner med hjälp av en Azure-partnerlösning. Du kan hitta de senaste azure-partnernätverkssäkerhetslösningarna genom att besöka [Azure Marketplace](https://azure.microsoft.com/marketplace/)och söka efter "säkerhet" och "nätverkssäkerhet".

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en helt tillståndskänslig brandvägg som en tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. Några funktioner inkluderar:

* Hög tillgänglighet
* Molnskalbarhet
* Programmets FQDN-filtreringsregler
* Regler för filtrering av nätverkstrafik

Läs mer:

* [Översikt över Azure-brandväggen](/azure/firewall/overview)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Säker fjärråtkomst och anslutning mellan lokala

Installation, konfiguration och hantering av dina Azure-resurser måste göras på distans. Dessutom kanske du vill distribuera [hybrid-IT-lösningar](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) som har komponenter lokalt och i det offentliga Azure-molnet. Dessa scenarier kräver säker fjärråtkomst.

Azure-nätverk stöder följande scenarier för säker fjärråtkomst:

* Ansluta enskilda arbetsstationer till ett virtuellt nätverk
* Ansluta ditt lokala nätverk till ett virtuellt nätverk med en VPN
* Ansluta ditt lokala nätverk till ett virtuellt nätverk med en dedikerad WAN-länk
* Ansluta virtuella nätverk till varandra

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Ansluta enskilda arbetsstationer till ett virtuellt nätverk

Du kanske vill aktivera enskilda utvecklare eller driftpersonal för att hantera virtuella datorer och tjänster i Azure. Anta till exempel att du behöver åtkomst till en virtuell dator i ett virtuellt nätverk. Men din säkerhetsprincipen tillåter inte RDP- eller SSH-fjärråtkomst till enskilda virtuella datorer. I det här fallet kan du använda en [punkt-till-plats VPN-anslutning.](../../vpn-gateway/point-to-site-about.md)

Med VPN-anslutningen på punkt till plats kan du konfigurera en privat och säker anslutning mellan användaren och det virtuella nätverket. När VPN-anslutningen upprättas kan användaren RDP eller SSH via VPN-länken till valfri virtuell dator i det virtuella nätverket. (Detta förutsätter att användaren kan autentisera och är auktoriserad.) VPN-stöd på punkt till plats:

* Secure Socket Tunneling Protocol (SSTP), ett eget SSL-baserat VPN-protokoll. En SSL VPN-lösning kan penetrera brandväggar, eftersom de flesta brandväggar öppnar TCP-port 443, som SSL använder. SSTP stöds endast på Windows-enheter. Azure stöder alla versioner av Windows som har SSTP (Windows 7 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Läs mer:

* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Ansluta ditt lokala nätverk till ett virtuellt nätverk med en VPN

Du kanske vill ansluta hela företagsnätverket, eller delar av det, till ett virtuellt nätverk. Detta är vanligt i hybrid-IT-scenarier, där organisationer [utökar sina lokala datacenter till Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). I många fall är organisationer värdar för delar av en tjänst i Azure och delar lokalt. De kan till exempel göra det när en lösning innehåller frontend-webbservrar i Azure och backend-databaser lokalt. Dessa typer av "inter-premises" anslutningar gör också hanteringen av Azure-lokaliserade resurser säkrare och aktiverar scenarier som att utöka Active Directory-domänkontrollanter till Azure.

Ett sätt att åstadkomma detta är att använda en [plats-till-plats VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Skillnaden mellan en plats-till-plats VPN och en punkt-till-plats VPN är att den senare ansluter en enda enhet till ett virtuellt nätverk. En vpn från plats till plats ansluter ett helt nätverk (till exempel ditt lokala nätverk) till ett virtuellt nätverk. Plats-till-plats VPN till ett virtuellt nätverk använder vpn-protokollet för mycket säkert IPsec-tunnelläge.

Läs mer:

* [Skapa ett resurshanterarens virtuella nätverk med en plats-till-plats-VPN-anslutning med Azure-portalen](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Om VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Ansluta ditt lokala nätverk till ett virtuellt nätverk med en dedikerad WAN-länk

VPN-anslutningar mellan platser och plats till plats är effektiva för att möjliggöra anslutning mellan lokala anslutningar. Vissa organisationer anser dock att de har följande nackdelar:

* VPN-anslutningar flyttar data över internet. Detta exponerar dessa anslutningar för potentiella säkerhetsproblem med att flytta data över ett offentligt nätverk. Dessutom kan tillförlitlighet och tillgänglighet för internetanslutningar inte garanteras.
* VPN-anslutningar till virtuella nätverk kanske inte har bandbredd för vissa program och ändamål, eftersom de max ut på cirka 200 Mbps.

Organisationer som behöver högsta möjliga säkerhet och tillgänglighet för sina anslutningsövergripande anslutningar använder vanligtvis dedikerade WAN-länkar för att ansluta till fjärrplatser. Azure ger dig möjlighet att använda en dedikerad WAN-länk som du kan använda för att ansluta ditt lokala nätverk till ett virtuellt nätverk. Azure ExpressRoute, Express-dirigera direkt och Express-dirigera global räckvidd aktiverar detta.

Läs mer:

* [Teknisk översikt för ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute direkt](../../expressroute/expressroute-erdirect-about.md)
* [Expressrutt global räckvidd](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Ansluta virtuella nätverk till varandra

Det är möjligt att använda många virtuella nätverk för dina distributioner. Det finns olika skäl till varför du kan göra detta. Du kanske vill förenkla hanteringen, eller så kanske du vill ha ökad säkerhet. Oavsett motivationen för att placera resurser på olika virtuella nätverk kan det finnas tillfällen när du vill att resurser i vart och ett av nätverken ska ansluta till varandra.

Ett alternativ är att tjänster i ett virtuellt nätverk ansluter till tjänster i ett annat virtuellt nätverk, genom att "loopa tillbaka" via Internet. Anslutningen startar på ett virtuellt nätverk, går via Internet och kommer sedan tillbaka till det virtuella målnätverket. Det här alternativet visar anslutningen till de säkerhetsproblem som är inneboende i all internetbaserad kommunikation.

Ett bättre alternativ kan vara att skapa en plats-till-plats-VPN som ansluter mellan två virtuella nätverk. Den här metoden använder samma [IPSec-tunnellägesprotokoll](https://technet.microsoft.com/library/cc786385.aspx) som den gränsöverskridande VPN-anslutning mellan lokala och plats som nämns ovan.

Fördelen med den här metoden är att VPN-anslutningen upprättas över Azure-nätverksstrukturen, i stället för att ansluta via Internet. Detta ger dig ett extra lager av säkerhet, jämfört med plats-till-plats VPN som ansluter via Internet.

Läs mer:

* [Konfigurera en VNet-till-VNet-anslutning med hjälp av Azure Resource Manager och PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ett annat sätt att ansluta dina virtuella nätverk är [VNET-peering](../../virtual-network/virtual-network-peering-overview.md). Med den här funktionen kan du ansluta två Azure-nätverk så att kommunikationen mellan dem sker via Microsofts stamnätsinfrastruktur utan att den någonsin går över Internet. VNET-peering kan ansluta två VNETs inom samma region eller två VNETs över Azure-regioner. NSG kan användas för att begränsa anslutningen mellan olika undernät eller system.

## <a name="availability"></a>Tillgänglighet

Tillgänglighet är en viktig komponent i alla säkerhetsprogram. Om användarna och systemen inte kan komma åt vad de behöver för att komma åt via nätverket kan tjänsten betraktas som komprometterade. Azure har nätverkstekniker som stöder följande mekanismer med hög tillgänglighet:

* HTTP-baserad belastningsutjämning
* Belastningsutjämning på nätverksnivå
* Global belastningsutjämning

Belastningsutjämning är en mekanism som är utformad för att lika distribuera anslutningar mellan flera enheter. Målen för lastbalansering är:

* För att öka tillgängligheten. När du laddar balansanslutningar på flera enheter kan en eller flera enheter bli otillgängliga utan att tjänsten äventyras. Tjänsterna som körs på de återstående onlineenheterna kan fortsätta att visa innehållet från tjänsten.
* För att öka prestanda. När du laddar balansanslutningar på flera enheter behöver en enda enhet inte hantera all bearbetning. I stället sprids bearbetnings- och minneskraven för att betjäna innehållet på flera enheter.

### <a name="http-based-load-balancing"></a>HTTP-baserad belastningsutjämning

Organisationer som kör webbaserade tjänster vill ofta ha en HTTP-baserad belastningsutjämnare framför dessa webbtjänster. Detta bidrar till att säkerställa adekvata prestandanivåer och hög tillgänglighet. Traditionella, nätverksbaserade belastningsutjämnare är beroende av protokoll för nätverks- och transportlager. HTTP-baserade belastningsutjämnare fattar däremot beslut baserat på HTTP-protokollets egenskaper.

Azure Application Gateway tillhandahåller HTTP-baserad belastningsutjämning för dina webbaserade tjänster. Application Gateway stöder:

* Cookie-baserad sessionstillhörighet. Den här funktionen säkerställer att anslutningar som upprättats till en av servrarna bakom den belastningsutjämnaren förblir intakt mellan klienten och servern. Detta säkerställer stabila transaktioner.
* SSL avlastning. När en klient ansluter till belastningsutjämnaren krypteras den sessionen med hjälp av HTTPS-protokollet (SSL). För att öka prestanda kan du dock använda HTTP-protokollet (okrypterad) för att ansluta mellan belastningsutjämnaren och webbservern bakom belastningsutjämnaren. Detta kallas "SSL-avlastning", eftersom webbservrarna bakom belastningsutjämnaren inte upplever processorkostnaderna som är involverade i kryptering. Webbservrarna kan därför servicebegäranden snabbare.
* URL-baserad innehållsroutning. Den här funktionen gör det möjligt för belastningsutjämnaren att fatta beslut om var anslutningarna ska vidarebefordras baserat på mål-URL:en. Detta ger mycket mer flexibilitet än lösningar som fattar belastningsutjämningsbeslut baserat på IP-adresser.

Läs mer:

* [Översikt över Application Gateway](/azure/application-gateway/application-gateway-introduction)

### <a name="network-level-load-balancing"></a>Belastningsutjämning på nätverksnivå

I motsats till HTTP-baserad belastningsutjämning fattar belastningsutjämning på nätverksnivå beslut baserat på IP-adress- och portnummer (TCP eller UDP).
Du kan få fördelarna med belastningsutjämning på nätverksnivå i Azure med hjälp av Azure Load Balancer. Några viktiga egenskaper hos belastningsutjämnaren är:

* Belastningsutjämning på nätverksnivå baserat på IP-adress och portnummer.
* Stöd för alla protokoll för programlager.
* Belastningsutjämning till Azure virtuella datorer och molntjänstrollinstanser.
* Kan användas för både internet-vända (extern belastningsutjämning) och icke-internet inför (intern belastningsutjämning) applikationer och virtuella datorer.
* Slutpunktsövervakning, som används för att avgöra om någon av tjänsterna bakom belastningsutjämnaren har blivit otillgänglig.

Läs mer:

* [Internet-vänd belastningsutjämnare mellan flera virtuella datorer eller tjänster](/azure/load-balancer/load-balancer-internet-overview)
* [Översikt över intern belastningsutjämnare](/azure/load-balancer/load-balancer-internal-overview)

### <a name="global-load-balancing"></a>Global belastningsutjämning

Vissa organisationer vill ha högsta möjliga tillgänglighetsnivå. Ett sätt att nå det här målet är att vara värd för program i globalt distribuerade datacenter. När ett program finns i datacenter som finns över hela världen är det möjligt för en hel geopolitisk region att bli otillgänglig och fortfarande ha programmet igång.

Den här belastningsutjämningsstrategin kan också ge prestandafördelar. Du kan dirigera begäranden om tjänsten till det datacenter som är närmast den enhet som gör begäran.

I Azure kan du få fördelarna med global belastningsutjämning med hjälp av Azure Traffic Manager.

Läs mer:

* [Vad är Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Namnmatchning

Namnmatchning är en viktig funktion för alla tjänster som du är värd för i Azure. Från ett säkerhetsperspektiv kan kompromettering av namnmatchningsfunktionen leda till att en angripare omdirigerar begäranden från dina webbplatser till en angripares webbplats. Säker namnmatchning är ett krav för alla dina molnbaserade tjänster.

Det finns två typer av namnmatchning som du måste åtgärda:

* Intern namnmatchning. Detta används av tjänster i dina virtuella nätverk, dina lokala nätverk eller båda. Namn som används för intern namnmatchning är inte tillgängliga via Internet. För optimal säkerhet är det viktigt att ditt interna namnmatchningsschema inte är tillgängligt för externa användare.
* Extern namnmatchning. Detta används av personer och enheter utanför dina lokala nätverk och virtuella nätverk. Dessa är de namn som är synliga för Internet och som används för direkt anslutning till dina molnbaserade tjänster.

För intern namnmatchning har du två alternativ:

* En DNS-server för virtuellt nätverk. När du skapar ett nytt virtuellt nätverk skapas en DNS-server åt dig. Den här DNS-servern kan matcha namnen på datorerna i det virtuella nätverket. Den här DNS-servern kan inte konfigureras, hanteras av Azure fabric manager och kan därför hjälpa dig att skydda din lösning för namnmatchning.
* Ta med din egen DNS-server. Du har möjlighet att sätta en DNS-server som du själv väljer på ditt virtuella nätverk. Den här DNS-servern kan vara en Active Directory-integrerad DNS-server eller en dedikerad DNS-serverlösning som tillhandahålls av en Azure-partner som du kan hämta från Azure Marketplace.

Läs mer:

* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)
* [Hantera DNS-servrar som används av ett virtuellt nätverk](../../virtual-network/manage-virtual-network.md#change-dns-servers)

För extern namnmatchning har du två alternativ:

* Vara värd för din egen externa DNS-server lokalt.
* Vara värd för din egen externa DNS-server hos en tjänsteleverantör.

Många stora organisationer är värd för sina egna DNS-servrar lokalt. De kan göra detta eftersom de har nätverk expertis och global närvaro för att göra det.

I de flesta fall är det bättre att vara värd för dina DNS-namnmatchningstjänster hos en tjänsteleverantör. Dessa tjänsteleverantörer har nätverksexpertis och global närvaro för att säkerställa mycket hög tillgänglighet för dina namnmatchningstjänster. Tillgänglighet är viktigt för DNS-tjänster, för om dina namnmatchningstjänster misslyckas kommer ingen att kunna nå dina internetinriktade tjänster.

Azure ger dig en mycket tillgänglig och högpresterande extern DNS-lösning i form av Azure DNS. Den här lösningen för extern namnmatchning drar nytta av den globala Azure DNS-infrastrukturen. Det gör att du kan vara värd för din domän i Azure, med samma autentiseringsuppgifter, API: er, verktyg och fakturering som dina andra Azure-tjänster. Som en del av Azure ärver den också de starka säkerhetskontroller som är inbyggda i plattformen.

Läs mer:

* [Översikt över Azure DNS](../../dns/dns-overview.md)
* [Med privata Azure DNS-zoner](../../dns/private-dns-overview.md) kan du konfigurera privata DNS-namn för Azure-resurser i stället för de automatiskt tilldelade namnen utan att du behöver lägga till en anpassad DNS-lösning.

## <a name="perimeter-network-architecture"></a>Arkitektur för perimeternätverk

Många stora organisationer använder perimeternätverk för att segmentera sina nätverk och skapa en buffertzon mellan internet och deras tjänster. Perimeterdelen av nätverket anses vara en lågsäkerhetszon och inga värdefulla tillgångar placeras i det nätverkssegmentet. Du ser vanligtvis nätverkssäkerhetsenheter som har ett nätverksgränssnitt i perimeternätverkssegmentet. Ett annat nätverksgränssnitt är anslutet till ett nätverk som har virtuella datorer och tjänster som accepterar inkommande anslutningar från Internet.

Du kan utforma perimeternätverk på flera olika sätt. Beslutet att distribuera ett perimeternätverk och sedan vilken typ av perimeternätverk som ska användas om du bestämmer dig för att använda ett, beror på dina nätverkssäkerhetskrav.

Läs mer:

* [Microsofts molntjänster och nätverkssäkerhet](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Distribuerade överbelastningsattacker (DDoS) är några av de största tillgänglighets- och säkerhetsproblemen för kunder som flyttar sina program till molnet. En DDoS-attack försöker uttömma ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet.
Microsoft tillhandahåller DDoS-skydd som kallas **Basic** som en del av Azure-plattformen. Detta kommer utan kostnad och inkluderar alltid på övervakning och realtidsreducering av gemensamma attacker på nätverksnivå. Förutom de skydd som ingår i DDoS-skydd **Basic** kan du aktivera **standardalternativet.** DDoS Protection Standard funktioner inkluderar:

* **Integrering av inbyggd plattform:** Inbyggt integrerat i Azure. Inkluderar konfiguration via Azure-portalen. DDoS Protection Standard förstår dina resurser och resurskonfiguration.
* **Nyckelskydd:** Förenklad konfiguration skyddar omedelbart alla resurser i ett virtuellt nätverk så snart DDoS Protection Standard är aktiverad. Ingen åtgärd eller användardefinition krävs. DDoS Protection Standard omedelbart och automatiskt mildrar attacken, när den upptäcks.
* **Trafikövervakning alltid:** Ditt program trafikmönster övervakas 24 timmar om dygnet, 7 dagar i veckan, letar efter indikatorer på DDoS-attacker. Begränsning utförs när skyddsprinciper överskrids.
* **Rapporter om begränsning av angrepp** Attack Mitigation Reports använder aggregerade nätverksflödesdata för att ge detaljerad information om attacker som är riktade mot dina resurser.
* **Flödesloggar för attackreducering** Attack Mitigation Flow Logs kan du granska den tappade trafiken, vidarebefordrad trafik och andra attackdata i nära realtid under en aktiv DDoS-attack.
* **Adaptiv justering:** Intelligent trafikprofilering lär sig programmets trafik över tid och väljer och uppdaterar den profil som passar bäst för din tjänst. Profilen justeras när trafiken ändras över tid. Layer 3 till layer 7 skydd: Ger full stack DDoS-skydd, när den används med ett webbprogram brandvägg.
* **Omfattande begränsningsskala:** Över 60 olika attacktyper kan mildras, med global kapacitet, för att skydda mot de största kända DDoS-attackerna.
* **Attack mått:** Sammanfattade mått från varje attack är tillgängliga via Azure Monitor.
* **Varning för angrepp:** Aviseringar kan konfigureras i början och stoppet av en attack och under attackens varaktighet med hjälp av inbyggda attackmått. Aviseringar integreras i din operativa programvara som Microsoft Azure Monitor-loggar, Splunk, Azure Storage, E-post och Azure-portalen.
* **Kostnadsgaranti:**  Dataöverföring och utskalningstjänstkrediter för dokumenterade DDoS-attacker.
* **DDoS Snabb responsiv** DDoS Protection Standard-kunder har nu tillgång till Rapid Response-teamet under en aktiv attack. DRR kan hjälpa till med attackutredning, anpassade mildrande åtgärder under en attack och analys efter attacken.


Läs mer:

* [DDOS-skydd översikt](../../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Med Azure Front Door Service kan du definiera, hantera och övervaka den globala routningen av din webbtrafik. Det optimerar trafikens routing för bästa prestanda och hög tillgänglighet. Med Azure Front Door kan du skapa anpassade regler för brandväggen för webbaserade program (WAF) vid åtkomstkontroll, för att skydda din HTTP/HTTPS-arbetsbelastning från utnyttjande som baseras på klienternas IP-adresser, landskod och HTTP-parametrar. Dessutom, Ytterdörren kan du också skapa hastighetsbegränsande regler för att bekämpa skadlig bot trafik, det inkluderar SSL avlastning och per-HTTP/HTTPS-begäran, program-lager bearbetning.

Själva Front Door-plattformen är skyddad av Azure DDoS Protection Basic. Om du behöver mer skydd kan Azure DDoS Protection Standard aktiveras på dina virtuella nätverk och skydda resurser från nätverkslagerattacker (TCP/UDP) med hjälp av automatiska justeringar och åtgärder. Ytterdörren är ett lager 7 omvänd proxy, det tillåter bara webbtrafik att passera till back end servrar och blockera andra typer av trafik som standard.

Läs mer:

* Om du vill ha mer information om hela uppsättningen azure-dörrsfunktioner kan du gå igenom [översikten över Front Door](../../frontdoor/front-door-overview.md) i Azure

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Azure Traffic Manager är en lastbalanserare för DNS-baserad trafik som hjälper dig att distribuera trafiken optimalt till tjänster i globala Azure-regioner, med hög tillgänglighet och korta svarstider. Traffic Manager använder DNS för att dirigera klientbegäranden till den lämpligaste tjänstslutpunkten baserat på en trafikdirigeringsmetod och slutpunkternas hälsostatus. En slutpunkt är en Internetansluten tjänst i eller utanför Azure. Traffic Manager övervakar slutpunkterna och dirigerar inte trafik till slutpunkter som inte är tillgängliga.

Läs mer:

* [Översikt över Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Övervakning och upptäckt av hot

Azure tillhandahåller funktioner som hjälper dig på det här nyckelområdet med tidig identifiering, övervakning och insamling och granskning av nätverkstrafik.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher kan hjälpa dig att felsöka och tillhandahåller en helt ny uppsättning verktyg för att hjälpa till med identifiering av säkerhetsproblem.

[Security Group View](../../network-watcher/network-watcher-security-group-view-overview.md) hjälper till med granskning och säkerhetsefterlevnad för virtuella datorer. Använd den här funktionen om du vill utföra programmatiska granskningar och jämföra de originalprinciper som organisationen definierar med gällande regler för var och en av dina virtuella datorer. Detta kan hjälpa dig att identifiera alla konfigurationsdrift.

[Med paketinsamling](../../network-watcher/network-watcher-packet-capture-overview.md) kan du fånga nätverkstrafik till och från den virtuella datorn. Du kan samla in nätverksstatistik och felsöka programproblem, som kan vara ovärderliga i undersökningen av nätverksintrång. Du kan också använda den här funktionen tillsammans med Azure Functions för att starta nätverksinsamlingar som svar på specifika Azure-aviseringar.

Mer information om Network Watcher och hur du börjar testa några av funktionerna i dina labb finns i [Översikt över övervakning av Azure-nätverksbevakaren](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> De senaste meddelandena om tjänstens tillgänglighet och status finns på [sidan Azure-uppdateringar](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center hjälper dig att förebygga, identifiera och svara på hot och ger dig ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Den ger integrerad säkerhetsövervakning och principhantering i dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå obemärkt förbi och fungerar med en stor uppsättning säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka nätverkssäkerheten genom att:

* Tillhandahålla rekommendationer för nätverkssäkerhet.
* Övervaka tillståndet för nätverkssäkerhetskonfigurationen.
* Varna dig för nätverksbaserade hot, både på slutpunkts- och nätverksnivå.

Läs mer:

* [Introduktion till Azure Security Center](../../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>TRYCK PÅ Virtuellt nätverk

Med Azure-virtuellt nätverk TAP (Terminal Access Point) kan du kontinuerligt strömma din virtuella datornätverkstrafik till ett nätverkspaketinsamlare eller analysverktyg. Insamlaren eller analysverktyget tillhandahålls av en virtuell nätverksinstallationspartner. Du kan använda samma TAP-resurs för virtuellt nätverk för att aggregera trafik från flera nätverksgränssnitt i samma eller olika prenumerationer.

Läs mer:

* [Virtual Network TAP](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Loggning

Loggning på nätverksnivå är en nyckelfunktion för alla nätverkssäkerhetsscenarier. I Azure kan du logga information som erhållits för NSG:er för att få loggningsinformation på nätverksnivå. Med NSG-loggning får du information från:

* [Aktivitetsloggar](../../azure-monitor/platform/platform-logs-overview.md). Använd dessa loggar för att visa alla åtgärder som skickas till dina Azure-prenumerationer. Dessa loggar är aktiverade som standard och kan användas i Azure-portalen. De var tidigare kända som revision eller operativa loggar.
* Händelseloggar. Dessa loggar ger information om vilka NSG-regler som tillämpades.
* Motloggar. Med dessa loggar får du veta hur många gånger varje NSG-regel tillämpades för att neka eller tillåta trafik.

Du kan också använda [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), ett kraftfullt datavisualiseringsverktyg, för att visa och analysera dessa loggar.
Läs mer:

* [Azure Monitor-loggar för nätverkssäkerhetsgrupper (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md)
