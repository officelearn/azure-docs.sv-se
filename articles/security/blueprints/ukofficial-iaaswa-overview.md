---
title: Azure-säkerhet och efterlevnad skissen - Trelagers-IaaS-webbprogram för UK-OFFICIAL
description: Azure-säkerhet och efterlevnad skissen - Trelagers-IaaS-webbprogram för UK-OFFICIAL
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 04de32b2df630eea918c786a7f065f404f4d8dca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164528"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Azure-säkerhet och efterlevnad skissen - Trelagers-IaaS-webbprogram för UK-OFFICIAL

## <a name="overview"></a>Översikt

 Den här artikeln innehåller vägledning och automation-skript för att leverera en Microsoft Azure webbprogram med tre nivåer baserat arkitektur lämpliga för att hantera många arbetsbelastningar som klassificeras som officiella i Storbritannien.

 Med en infrastruktur som kod hanterar uppsättningen [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mallarna distribuerar en miljö som är knutet till de Storbritannien nationella Cyberhot Security Center (NCSC) 14 [Molnsäkerhetsprinciper](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) Center för Internet Security (CIS) och [kritiskt säkerhetskontroller](https://www.cisecurity.org/critical-controls.cfm).

 NCSC rekommenderar sina Molnsäkerhetsprinciper används av kunder för att utvärdera säkerhetsegenskaperna för tjänsten och för att förstå divisionen av ansvar mellan kunden och leverantören. Vi har samlat informationen mot alla dessa principer för att förstå delningen av ansvarsområden.

 Den här arkitekturen och motsvarande Azure Resource Manager-mallar stöds av Microsoft-faktabladet [14 molnet säkerhetskontroller för Storbritannien cloud Using Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Det här dokumentet produktkataloger hur Azure-tjänster som överensstämmer med den Storbritannien NCSC 14 Molnsäkerhetsprinciper, vilket innebär att organisationer snabb åtkomst till sina möjligheter att uppfylla skyldigheterna efterlevnad med molnbaserade tjänster globalt och i Storbritannien på Microsoft Azure moln.

 Den här mallen distribuerar infrastrukturen för arbetsbelastningen. Programkod och stöd för affärsnivå och programvara för nivå måste vara installerat och konfigurerat. Distribution av detaljerade instruktioner finns [här](https://aka.ms/ukwebappblueprintrepo).

 Om du inte har en Azure-prenumeration och du kan registrera dig snabbt och enkelt - [Kom igång med Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

 Azure-mallar leverera en programarkitektur för webbprogram med tre nivåer i en miljö för Azure-molnet som har stöd för Storbritannien officiella arbetsbelastningar. Arkitekturen ger en säker hybridmiljö som utökar ett lokalt nätverk till Azure så att webbaserad arbetsbelastningar som kan nås på ett säkert sätt genom att företagsanvändare eller från internet.

![IaaS-webbprogram för tre nivåer för UK-OFFICIAL referens för Arkitekturdiagram](images/ukofficial-iaaswa-architecture.png?raw=true "Trelagers-IaaS-webbprogram för Storbritannien officiella Arkitekturdiagram för referens")

 Den här lösningen använder följande Azure-tjänster. Information om distributionsarkitekturen finns i den [distributionsarkitektur](#deployment-architecture) avsnittet.

(1) /16 Virtual Network - Operational VNet
- (3) /24 undernät – 3 nivåer (Web, Biz, Data)
- (1) /27 undernät - lägger till
- ((1) /27 undernät - Gateway-undernät
- ((1) /29 undernät - Programgatewayens undernät
- Använder standard (medföljer Azure) DNS
- Peering aktiverad till hantering av virtuellt nätverk
- Nätverkssäkerhetsgrupp (NSG) för att hantera flödet i nätverkstrafiken

((1) /24 virtuellt nätverk – hantering av virtuellt nätverk
- ((1) /27 undernät
- (2) lägger till DNS-och (1) Azure DNS-poster
- Aktiverat för att använda VNet-peering
- Nätverkssäkerhetsgrupp (NSG) för att hantera flödet i nätverkstrafiken

(1) Application Gateway
- WAF - aktiverat
- WAF-läge – skydd
- Regeluppsättning: OWASP 3.0
- HTTP-lyssnaren på Port 80
- Anslutning/trafik regleras genom NSG
- Offentlig IP-adress slutpunkt definierats (Azure)

(1) VPN - väg-baserad plats-2-plats IPSec VPN-tunnel
- Offentlig IP-adress slutpunkt definierats (Azure)
- Anslutning/trafik regleras genom NSG
- (1) lokal nätverksgateway (lokala slutpunkt)
- (1) azure-nätverksgatewayen (Azure-slutpunkt)

(9) virtuella datorer – alla virtuella datorer distribueras med Azure IaaS program mot skadlig kod DSC-inställningar

- (2) active Directory Domain Services-domänkontrollanter (Windows Server 2012 R2)
  - (2) DNS Server-roller – 1 per virtuell dator
  - (2) nätverkskort är anslutna till operativa VNet - 1 per virtuell dator
  - Båda är domänansluten till den domän som definierats i mallen
    - Domän som skapades som en del av distributionen


- (1) Jumpbox (Skyddsmiljö-värd) Management VM
  - 1 nätverkskortet för hantering av virtuella nätverk med offentliga IP-adress
    - NSG används för att begränsa trafik (in/ut) till särskilda källor
  - Inte ansluten till domänen


- (2) virtuella datorer web-nivå
  - (2) IIS Server Roles - 1 per VM
  - (2) nätverkskort är anslutna till operativa VNet - 1 per virtuell dator
  - Inte ansluten till domänen


- (2) biz nivån virtuella datorer
  - (2) nätverkskort är anslutna till operativa VNet - 1 per virtuell dator
  - Inte ansluten till domänen


- (2) data Webbnivåns virtuella datorer
  - (2) nätverkskort är anslutna till operativa VNet - 1 per virtuell dator
  - Inte ansluten till domänen

Tillgänglighetsuppsättningar
- (1) active Directory-domänkontrollant VM konfigurerats – 2 virtuella datorer
- (1) Webbnivå VM konfigurerats – 2 virtuella datorer
- (1) biz nivån VM konfigurerats – 2 virtuella datorer
- (1) datanivå VM konfigurerats – 2 virtuella datorer

Lastbalanserare
- (1) belastningsutjämnaren på Webbnivå
- (1) biz belastningsutjämnaren på Webbnivå
- (1) belastningsutjämnaren på Webbnivå data

Storage
- (14) den totala mängden Storage-konton
  - Active Directory Domain Controller Tillgänglighetsuppsättning
    - (2) primära konton för lokalt Redundant lagring (LRS) - 1 för varje virtuell dator  
    - (1) diagnostik lokalt Redundant lagring (LRS)-konto för lägger till Tillgänglighetsuppsättningen
  - Jumpbox hanterings-VM
    - (1) primär lokalt Redundant lagring (LRS) hänsyn till Jumpbox VM
    - (1) lokalt Redundant lagring (LRS)-konto för Jumpbox VM-diagnostik
  - Web nivån virtuella datorer
    - (2) primära konton för lokalt Redundant lagring (LRS) - 1 för varje virtuell dator  
    - (1) diagnostik lokalt Redundant lagring (LRS)-konto för den Tillgänglighetsuppsättning för nivån
  - Biz nivån virtuella datorer
    - (2) primära konton för lokalt Redundant lagring (LRS) - 1 för varje virtuell dator  
    - (1) diagnostik lokalt Redundant lagring (LRS)-konto för Biz nivå Tillgänglighetsuppsättningen
  - Data nivån virtuella datorer
    - (2) primära konton för lokalt Redundant lagring (LRS) - 1 för varje virtuell dator  
    - (1) diagnostik lokalt Redundant lagring (LRS)-konto för Data nivå Tillgänglighetsuppsättningen

### <a name="deployment-architecture"></a>Distributionsarkitektur:

**Lokalt nätverk**: Ett privat lokalt nätverk som implementerats i en organisation.

**Produktion VNet**: Produktion [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (virtuellt nätverk) är värd för programmet och andra operativa resurser som körs i Azure. Varje virtuellt nätverk kan innehålla flera undernät som används för att isolera och hantering av nätverkstrafik.

**Webb-nivå**: Hanterar inkommande HTTP-begäranden. Svar returneras via den här nivån.

**Affärsnivå**: Implementerar affärsprocesser och andra funktionella logik för systemet.

**Databasen nivån**: Ger beständig lagring, med hjälp av [SQL Server Always On-Tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx) för hög tillgänglighet. Kunder kan använda [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) som en PaaS-alternativ.

**Gateway**: Den [VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) tillhandahåller anslutningen mellan routrarna i det lokala nätverket och virtuellt nätverk för produktion.

**Internet-Gateway och offentlig IP-adress**: Internet-gateway exponerar programtjänster för användare via internet. Trafik som kommer åt tjänsterna skyddas med ett [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) erbjuda Layer 7-Routning och belastningsutjämning med web application firewall (WAF)-skydd.

**Hantering av virtuellt nätverk**: Detta [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) innehåller resurser som implementerar hanterings- och övervakningsfunktioner för de arbetsbelastningar som körs i produktion VNet.

**Jumpbox**: Kallas även en [skyddsmiljö-värd](https://en.wikipedia.org/wiki/Bastion_host), vilket är en säker virtuell dator i nätverket som administratörer använder för att ansluta till virtuella datorer i produktion VNet. Jumpboxen har en NSG som endast tillåter fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i NSG: N för att tillåta (RDP) trafik för fjärrskrivbordet. Hantering av produktionsresurser är via RDP med en säker Jumpbox VM.

**Användardefinierade vägar**: [Användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) används för att definiera flödet av IP-trafik i virtuella Azure-nätverk.

**Network Peerkopplade virtuella nätverk**: Produktions- och hantering av virtuella nätverk är anslutna med [VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
De här virtuella nätverken hanteras fortfarande som separata resurser, men visas som en för alla anslutningar för dessa virtuella datorer. Dessa nätverk som kommunicerar med varandra direkt med hjälp av privata IP-adresser. VNet-peering är föremål för de virtuella nätverken är i samma Azure-Region.

**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller åtkomstkontrollistor som tillåter eller nekar trafik inom ett virtuellt nätverk. NSG: er kan användas för att skydda trafik på ett undernät eller individuella VM-nivå.

**Active Directory Domain Services (AD DS)**: Den här arkitekturen ger en dedikerad [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) distribution.

**Loggning och granskning**: [Azure-aktivitetslogg](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) insamlingar åtgärder vidtas på resurserna i prenumerationen som vem som initierade åtgärden när åtgärden utfördes, status för åtgärden och värdena för andra egenskaper som kan hjälpa dig undersöka den åtgärden. Azure-aktivitetslogg är en tjänst i Azure-plattformen som samlar in alla åtgärder på en prenumeration. Loggar kan arkiveras eller exporteras om det behövs.

**Nätverket övervakning och avisering**: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) är en plattformstjänst som tillhandahåller nätverk-infångade, flödesloggar, topologi verktyg och diagnostik för nätverk så inom dina virtuella nätverk.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Server-arbetsbelastningar är grupperade i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Den här konfigurationen hjälper till att säkerställa att under en planerad eller oplanerad underhållshändelse minst en virtuell dator ska vara tillgängliga och uppfylla 99,95% serviceavtalet för Azure.

### <a name="logging-and-audit"></a>Loggning och granskning

**Övervakning av**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) är plattformstjänst som tillhandahåller en enda källa för övervakning av aktivitetsloggen, mått och diagnostikloggar för alla dina Azure-resurser. Azure Monitor kan konfigureras för att visualisera, fråga, vidarebefordra, arkivera och vidta åtgärder för mått och loggar från resurser i Azure. Du rekommenderas att resursbaserade åtkomstkontroll används för att skydda granskningsloggen för att säkerställa att användarna inte har möjlighet att ändra loggarna.

**Aktivitetsloggar**: Konfigurera [Azure-aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) att ge insikt i de åtgärder som utförts på resurser i din prenumeration.

**Diagnostikloggar**: [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) är alla loggar som genereras av en resurs. Dessa loggar kan innehålla Windows-händelsesystemloggar, blob, tabell och kö loggar.

**Brandväggen loggar**: Application Gateway ger fullständig diagnostik och åtkomst till loggar. Brandväggsloggar är tillgängliga för Application Gateway-resurser som har WAF aktiverat.

**Arkivera loggen**: Datalagring i loggen kan konfigureras för att skriva till en centraliserad Azure-lagringskonto för arkivering och en definierad kvarhållningsperiod. Loggar kan bearbetas med hjälp av Azure Log Analytics eller genom att SIEM-system från tredje part.

### <a name="identity"></a>Identitet

**Active Directory Domain Services**: Den här arkitekturen ger en Active Directory Domain Services-distribution i Azure. Specifika rekommendationer för att implementera Active Directory i Azure finns i följande artiklar:

[Utöka Active Directory Domain Services (AD DS) till Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Active Directory-integrering**: Som ett alternativ till en dedikerad AD DS-arkitektur kan kunder kanske vill använda [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity) integration eller [Active Directory i Azure som är anslutna till en lokal skog](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Säkerhet

**Hantering av säkerhet**: Den här skissen kan administratörer ansluta till hanteringen VNet och Jumpbox med RDP från en betrodd källa. Nätverkstrafik för management VNet kontrolleras med hjälp av NSG: er. Åtkomst till port 3389 är begränsad till trafik från en betrodd IP-adressintervall som har åtkomst till undernätet som innehåller Jumpbox.

Kunder kan också överväga att använda en [förbättrad administrativ säkerhetsmodell](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) att skydda miljön när du ansluter till hanteringen VNet och Jumpbox. Vi rekommenderar att kunder använder för ökad säkerhet en [arbetsstation för privilegierad åtkomst](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) och RDGateway-konfiguration. Användningen av nätverkets virtuella installationer och offentliga och privata DMZ-miljöer erbjuder ytterligare säkerhetsförbättringar.

**Skydda nätverket**: [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) rekommenderas för varje undernät att tillhandahålla en andra skyddsnivå mot inkommande trafik som kringgår en felaktigt konfigurerad eller inaktiverad gateway. Exempel – [Resource Manager-mall för att distribuera en NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Skydda offentliga slutpunkter**: Internet-gateway exponerar programtjänster för användare via internet. Trafik som kommer åt tjänsterna skyddas med ett [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), som tillhandahåller en brandvägg för webbaserade program och HTTPS-protokollet.

**IP-intervall**: IP-adressintervall i arkitekturen är föreslagna intervall. Kunder bör du överväga att deras egen miljö och Använd lämpliga områden.

**Hybridanslutning**: Molnbaserad arbetsbelastningar är anslutna till det lokala datacentret via IPSEC VPN med hjälp av Azure VPN Gateway. Kunder bör se till att de använder en lämplig VPN-Gateway för att ansluta till Azure. Exempel – [VPN Gateway Resource Manager-mall](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Kunder som kör storskaliga, verksamhetskritiska arbetsbelastningar med stordatakrav kanske vill ha en hybrid arkitektur med [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) för privat nätverksanslutning till Microsoft-molntjänster.

**Problemseparering**: Denna Referensarkitektur skiljer de virtuella nätverken för hanteringsåtgärder och verksamheten. Separata virtuella nätverk och undernät tillåter hantering, inklusive trafik ingående och utgående begränsningar med hjälp av NSG: er mellan nätverkssegment följa [Microsofts molntjänster och nätverkssäkerhet](https://docs.microsoft.com/azure/best-practices-network-security) bästa praxis.

**Resurshantering**: Azure-resurser som virtuella datorer, virtuella nätverk och belastningsutjämnare som hanteras av grupperas tillsammans i [Azure-resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Åtkomstkontroll resursroller kan tilldelas till varje resursgrupp för att begränsa åtkomsten till endast auktoriserade användare.

**Komma åt Kontrollbegränsningar**: Använd [Role-Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) för att hantera resurserna i ditt program med hjälp av [anpassade roller](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) RBAC kan användas för att begränsa de åtgärder som DevOps kan utföra på varje nivå. När du beviljar behörighet använder den [principen om lägsta behörighet](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Logga alla administrativa åtgärder och utför regelbundna granskningar för att säkerställa att inga ändringar i konfigurationen har planerats.

**Internetåtkomst**: Den här referensarkitekturen använder [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) som internetuppkopplad gateway och load balancer. Vissa kunder kan också överväga att använda virtuella nätverksutrustning från tredje part för ytterligare skyddslager för nätverk säkerhet som ett alternativ till den [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Azure Security Center**: Den [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) tillhandahåller en central vy över säkerhetsstatusen för resurserna i prenumerationen och tillhandahåller rekommendationer som skyddar mot resurser som har komprometterats. Det kan också användas för att aktivera mer detaljerade principer. Till exempel kan principer tillämpas på specifika resursgrupper, vilket gör att företag kan anpassa dess efterlevnadsstatus risker. Vi rekommenderar att kunder som aktiverar Azure Security Center i sin Azure-prenumeration.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentation för NCSC Cloud Security principer för efterlevnad

Den kommersiella krona-tjänsten (en myndighet som fungerar för att förbättra kommersiell och inköp av regeringen) förnyas klassificeringen av Microsofts omfattande enterprise molntjänster till G-Cloud v6, som omfattar alla erbjudanden på OFFICIAL-nivå. Information om Azure och G-Cloud finns i den [Azure UK G-Cloud security utvärderingssammanfattning](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Den här skissen stämmer överens med de 14 molnsäkerhetsprinciper som finns dokumenterade i NCSC [Molnsäkerhetsprinciper](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) för att säkerställa en miljö som har stöd för arbetsbelastningar som klassificeras som UK-OFFICIAL.

Den [kundens ansvar matrisen](https://aka.ms/ukofficial-crm) (Excel-arbetsboken) visar en lista över alla 14 molnsäkerhetsprinciper och matrisen för varje princip (eller princip kapitel) anger om implementeringen av principen är ansvar Microsoft, kunden, eller delas mellan två.

Den [princip implementering matrisen](https://aka.ms/ukofficial-iaaswa-pim) (Excel-arbetsboken) visar alla 14 molnsäkerhetsprinciper och matrisen anger, för varje princip (eller princip kapitel) som är utsedd ett kund-ansvar i kunden Ansvarsområden matris, 1) om skissen automation implementerar principen och 2) en beskrivning av hur implementeringen ska justeras med unika krav.

Dessutom publicerade Cloud Security Alliance (CSA) Cloud Control matrisen stöd till kunder i utvärderingen av molnleverantörer och för att identifiera frågor som ska besvaras innan du går till molntjänster. I svaret, Microsoft Azure besvarade CSA konsensus utvärdering initiativ enkäten ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), som beskriver hur Microsoft behandlar föreslagna principerna.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Det finns två metoder som distribution kan användas för att distribuera den här skissen automation. Den första metoden använder ett PowerShell-skript, medan den andra metoden använder Azure-portalen om du vill distribuera referensarkitekturen. Distribution av detaljerade instruktioner finns [här](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informationssyfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, VAD GÄLLER INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som – är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder i det här dokumentet bär risken för användningen av den.
 - Det här dokumentet ger inte kunder med inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan leda till ökad data, nätverk och beräkning Resursanvändning i Azure och öka kostnaderna för en kunds Azure-licens eller prenumeration.
 - Den här arkitekturen är avsedd att fungera som en grund för kunder att anpassa sig till sina specifika krav och ska inte användas som-är i en produktionsmiljö.
 - Det här dokumentet har utvecklats som en referens och ska inte användas för att definiera alla innebär som en kund kan uppfylla specifika efterlevnadskrav och föreskrifter. Kunder bör söka juridiskt stöd från organisationen på godkända kundimplementeringar.
