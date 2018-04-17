---
title: Azure-säkerhet och efterlevnad modell - Storbritannien officiella tre skikt Web Applications Automation
description: Azure-säkerhet och efterlevnad modell - Storbritannien officiella tre skikt Web Applications Automation
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: bb0a667c28e4ed0be3e67a7d89f10903be2c9d2a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-security-and-compliance-blueprint---uk-offical-three-tier-web-applications-automation"></a>Azure-säkerhet och efterlevnad modell - Storbritannien officiella tre skikt Web Applications Automation

## <a name="overview"></a>Översikt

 Den här artikeln innehåller vägledning och automation skript för att leverera en Microsoft Azure webbaserade trelagers-arkitektur för hantering av många arbetsbelastningar som klassificeras som officiella i Storbritannien.

 Med hjälp av en infrastruktur som kod hanterar uppsättningen av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mallar distribuerar en miljö som justerar till den Storbritannien National Cyber Security Centre (NCSC) 14 [moln säkerhetsprinciper](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) och Center för Internet Security (CIS) [kritiska säkerhetsåtgärder](https://www.cisecurity.org/critical-controls.cfm).

 NCSC rekommenderar sina moln säkerhetsprinciper används av kunder att utvärdera säkerhetsegenskaperna för tjänsten och för att förstå divisionen av ansvar mellan kunden och leverantören. Vi har samlat informationen mot var och en av dessa principer som hjälper dig att förstå delningen av ansvarsområden.

 Den här arkitekturen och motsvarande Azure Resource Manager-mallar som stöds av Microsoft-whitepaper [14 molnet säkerhetsåtgärder för ett visst moln med Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Det här dokumentet kataloger hur Azure-tjänster överensstämmer med den Storbritannien NCSC 14 molnet säkerhetsprinciper, så att organisationer snabb åtkomst till sin förmåga att uppfylla skyldigheterna kompatibilitet med molnbaserade tjänster globalt och i Storbritannien på Microsoft Azure moln.

 Den här mallen distribuerar infrastrukturen för arbetsbelastningen. Programkod och ge support affärsnivå och nivå av programvara måste installeras och konfigureras. Detaljerade stegvisa anvisningar finns [här](https://aka.ms/ukwebappblueprintrepo).

 Om du inte har en Azure-prenumeration och du kan registrera dig snabbt och enkelt - [Kom igång med Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagram över arkitektur och komponenter

 Azure-mallar leverera en trelagers-web programarkitektur i en Azure-molnmiljö som stöder Storbritannien officiella arbetsbelastningar. Arkitekturen ger en säker hybridmiljö som utökar ett lokalt nätverk till Azure ger webbaserad arbetsbelastningar som kan nås på ett säkert sätt med företagsanvändare eller från internet.

![ALT-text](images/diagram.png?raw=true "Azure Storbritannien officiella tre nivå-arkitektur")

 Den här lösningen använder följande Azure-tjänster. Information om arkitektur för distribution finns i den [distributionsarkitektur](#deployment-architecture) avsnitt.

((1) /16 virtuellt nätverk - operativa VNet
- (3) /24 undernät - 3-skikts (webb-, Biz, Data)
- (1) minst/27 undernät - lägger till
- ((1) minst/27 undernät - Gateway-undernät
- ((1) /29 undernät - programmet Gateway-undernät
- Använder som standard (Azure-tillhandahållna) DNS
- Peering aktiverad till hantering av virtuellt nätverk
- Nätverkssäkerhetsgrupp (NSG) för att hantera trafikflöde

((1) /24 virtuellt nätverk – hantering av virtuellt nätverk
- ((1) minst/27 undernät
- Använder (2) lägger till DNS- och (1) Azure DNS-poster
- Aktiverade till operativa VNet-peering
- Nätverkssäkerhetsgrupp (NSG) för att hantera trafikflöde

(1) Programgateway
- Brandvägg - aktiverad
- Brandvägg läge - förebyggande
- Regelsamlingen: OWASP 3.0
- HTTP-lyssnaren på Port 80
- Anslutningen/trafik regleras genom NSG
- Offentlig IP-adress slutpunkt definierats (Azure)

(1) VPN - väg-baserad plats-2-plats IPSec VPN-tunnel
- Offentlig IP-adress slutpunkt definierats (Azure)
- Anslutningen/trafik regleras genom NSG
- (1) lokal nätverksgateway (lokala slutpunkten)
- (1) azure nätverksgateway (Azure slutpunkten)

(9) virtuella datorer – alla virtuella datorer distribueras med Azure IaaS program mot skadlig kod DSC-inställningar

- (2) active Directory Domain Services-domänkontrollanter (Windows Server 2012 R2)
  - (2) DNS-Server rollerna - 1 per VM
  - (2) nätverkskort anslutet till operativa VNet - 1 per VM
  - Båda är ansluten till domänen i domänen som definierats i mallen
    - Domäner som skapas som en del av distributionen


- (1) Jumpbox (Skyddsmiljö Host) Management VM
  - 1 nätverkskortet för hantering av virtuella nätverk med en offentlig IP-adress
    - NSG används för att begränsa trafik (in/ut) till särskilda källor
  - Inte ansluten till domänen


- (2) virtuella datorer web-nivå
  - (2) IIS-serverroller - 1 per VM
  - (2) nätverkskort anslutet till operativa VNet - 1 per VM
  - Inte ansluten till domänen


- (2) biz nivå virtuella datorer
  - (2) nätverkskort anslutet till operativa VNet - 1 per VM
  - Inte ansluten till domänen


- (2) data-Tier virtuella datorer
  - (2) nätverkskort anslutet till operativa VNet - 1 per VM
  - Inte ansluten till domänen

Tillgänglighetsuppsättningar
- (1) Ange active Directory-domänkontrollant VM - 2 virtuella datorer
- (1) Ange Webbnivå VM - 2 virtuella datorer
- (1) Ange biz nivå VM - 2 virtuella datorer
- (1) ange data-Tier VM - 2 virtuella datorer

Belastningsutjämnare
- (1) web nivå belastningsutjämnare
- (1) biz nivå belastningsutjämnare
- (1) data nivå belastningsutjämnare

Lagring
- (14) totala Storage-konton
  - Active Directory Domain Controller Tillgänglighetsuppsättning
    - (2) primära lokalt Redundant lagring (LRS) konton - 1 för varje virtuell dator  
    - (1) diagnostik lokalt Redundant lagringskonto (LRS) för lägger till Tillgänglighetsuppsättningen
  - Hantering av Jumpbox VM
    - (1) primära lokalt Redundant lagring (LRS) konto för Jumpbox VM
    - (1) diagnostik lokalt Redundant lagringskonto (LRS) för Jumpbox VM
  - Web nivå virtuella datorer
    - (2) primära lokalt Redundant lagring (LRS) konton - 1 för varje virtuell dator  
    - (1) diagnostik lokalt Redundant lagringskonto (LRS) för den Tillgänglighetsuppsättning för skiktet
  - Biz nivå virtuella datorer
    - (2) primära lokalt Redundant lagring (LRS) konton - 1 för varje virtuell dator  
    - (1) diagnostik lokalt Redundant lagringskonto (LRS) för Biz nivå Tillgänglighetsuppsättningen
  - Data-Tier virtuella datorer
    - (2) primära lokalt Redundant lagring (LRS) konton - 1 för varje virtuell dator  
    - (1) diagnostik lokalt Redundant lagringskonto (LRS) för Data nivå Tillgänglighetsuppsättningen

### <a name="deployment-architecture"></a>Arkitektur för distribution:

**Lokalt nätverk**: ett privat LAN-nätverk som implementerats i en organisation.

**Produktion VNet**: I produktion [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (virtuella nätverk) är värd för programmet och andra operativa resurser som körs i Azure. Varje virtuellt nätverk kan innehålla flera undernät som används för att identifiera och hantering av nätverkstrafik.

**Web nivå**: hanterar inkommande HTTP-begäranden. Svar returneras via det här skiktet.

**Affärsnivå**: implementerar affärsprocesser och andra funktionella logik för systemet.

**Databasen nivå**: tillhandahåller beständiga datalagring med [SQL Server alltid på Tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx) för hög tillgänglighet. Kunder kan använda [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) som en PaaS-alternativ.

**Gateway**: den [VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) tillhandahåller anslutningen mellan routrar i det lokala nätverket och produktion VNet.

**Internet-Gateway och offentliga IP-adressen**: internet-gateway exponerar programtjänster för användare via internet. Trafik att komma åt dessa tjänster skyddas med en [Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) erbjudande Layer 7 Routning och belastningsutjämning med web application firewall (Brandvägg)-skydd.

**Hantering av VNet**: detta [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) innehåller resurser som implementerar hantering och övervakning av funktioner för de arbetsbelastningar som körs i produktion VNet.

**Jumpbox**: kallas även en [skyddsmiljö värden](https://en.wikipedia.org/wiki/Bastion_host), vilket är en säker virtuell dator i nätverket som administratörer använder för att ansluta till virtuella datorer i produktion VNet. Jumpboxen har en NSG som endast tillåter fjärrtrafik från offentliga IP-adresser på en säker lista. Källan för trafiken måste definieras i NSG: N för att tillåta fjärråtkomst fjärrskrivbord (RDP)-trafik. Hantering av produktionsresurser är via RDP med hjälp av en skyddad Jumpbox-VM.

**Användardefinierade vägar**: [användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) används för att definiera flödet av IP-trafik i virtuella Azure-nätverk.

**Nätverket är Peerkopplat Vnet**: I produktion och hantering av virtuella nätverk är anslutna via [VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Dessa Vnet fortfarande hanteras som separata resurser, men visas som en gäller alla anslutningar för dessa virtuella datorer. Dessa nätverk kommunicerar direkt med privata IP-adresser. VNet-peering regleras Vnet som i samma Azure-Region.

**Nätverkssäkerhetsgrupper**: [NSG: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) innehåller åtkomstkontrollistor som tillåter eller nekar trafik i ett virtuellt nätverk. NSG: er kan användas för att skydda trafik i ett undernät eller individuella VM-nivå.

**Active Directory Domain Services (AD DS)**: den här arkitekturen ger ett dedikerat [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) distribution.

**Loggning och granskning**: [Azure-aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) samlar in åtgärder som vidtas på resurserna i din prenumeration, till exempel vem som initierat åtgärden, när åtgärden utfördes, status för åtgärden och värdena för andra egenskaper som kan hjälpa dig undersöka igen. Azure-aktivitetsloggen är en Azure-plattformen som samlar in alla åtgärder på en prenumeration. Loggar kan arkiveras eller exporteras om det behövs.

**Övervakning och aviseringar**: [Azure Nätverksbevakaren](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) är platform-tjänsten tillhandahåller nätverket paketinsamling, flödet loggning, topologi verktyg och diagnostik för nätverket traffics inom din Vnet.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: serverarbetsbelastningar grupperas i en [Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Den här konfigurationen säkerställer att under en planerad eller oplanerad underhållshändelse minst en virtuell dator ska vara tillgänglig och uppfyller 99,95% SLA för Azure.

### <a name="logging-and-audit"></a>Loggning och granskning

**Övervaka**: [Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) är platform-tjänst som tillhandahåller en enda källa för övervakning av aktivitetsloggen, mätvärden och diagnostiska loggar alla dina Azure-resurser. Azure Övervakare kan konfigureras för att visualisera, fråga, vidarebefordra, arkivera och agera på mått och loggar som kommer från resurser i Azure. Du rekommenderas att åtkomstkontroll till resurser baserat används för att skydda granskningsloggen för att säkerställa att användarna inte har möjlighet att modifiera loggarna.

**Aktivitetsloggar**: konfigurera [Azure aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) att ge insikt om de åtgärder som utfördes på resurser i din prenumeration.

**Diagnostikloggar**: [diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) är alla loggar som orsakat av en resurs. Dessa loggar kan omfatta Windows-händelsesystemloggar, blob, tabell och loggar för kön.

**Brandväggen loggar**: Programgateway ger fullständig diagnostik- och åtkomst-loggarna. Brandväggsloggar är tillgängliga för Application Gateway-resurser som har WAF aktiverat.

**Logga arkivering**: loggen datalagring kan konfigureras för att skriva till en centraliserad Azure storage konto för arkivering och definierade kvarhållningsperioden. Loggar kan bearbetas med hjälp av Azure logganalys eller av tredje part SIEM-system.

### <a name="identity"></a>Identitet

**Active Directory Domain Services**: den här arkitekturen ger en distribution av Active Directory Domain Services i Azure. Specifika rekommendationer för att implementera Active Directory i Azure finns i följande artiklar:

[Utöka Active Directory Domain Services (AD DS) till Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Active Directory-Integration**: som ett alternativ till en dedikerad AD DS-arkitektur kunder kan vilja använda [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) integrering eller [Active Directory i Azure som är ansluten till ett lokalt skogen](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Säkerhet

**Hantering av säkerhet**: den här modell gör att administratörer kan ansluta till hanteringsserver VNet och Jumpbox med RDP från en betrodd källa. Nätverkstrafik för hantering av virtuellt nätverk kontrolleras med hjälp av NSG: er. Åtkomst till port 3389 är begränsad till trafik från en betrodd IP-adressintervall som kan komma åt det undernät som innehåller Jumpbox.

Kunder kan också överväga att använda en [förbättrad administrativ säkerhetsmodell](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) att skydda miljön vid anslutning till management VNet och Jumpbox. Vi rekommenderar för ökad säkerhet kunder att använda en [arbetsstation för privilegierad åtkomst](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) och RDGateway konfiguration. Användningen av virtuella nätverksenheter och offentliga och privata DMZs erbjuder ytterligare förbättringar av säkerhet.

**Att skydda nätverket**: [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG: er) rekommenderas för varje undernät att tillhandahålla en andra nivå av skydd mot inkommande trafik som kringgår en felaktigt konfigurerad eller inaktiverad gateway. Exempel: [Resource Manager-mall för att distribuera en NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Skydda offentliga slutpunkter**: internet-gateway exponerar programtjänster för användare via internet. Trafik att komma åt dessa tjänster skyddas med en [Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), som tillhandahåller en brandvägg för webbaserade program och HTTPS-protokollet.

**IP-adressintervall**: IP-adressintervall i arkitekturen finns föreslagna intervall. Kunder bör överväga sina egna miljö och använda lämpliga områden.

**Hybridanslutning**: molnbaserad arbetsbelastningar är anslutna till det lokala datacentret via IPSEC VPN med hjälp av Azure VPN-Gateway. Kunder bör se till att de använder en lämplig VPN-Gateway för att ansluta till Azure. Exempel: [VPN-Gateway Resource Manager-mall](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Kunder som använder storskaliga, verksamhetskritiska arbetsbelastningar med stordata krav kanske vill ha en hybrid arkitektur med [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) privata nätverksanslutningen till Microsoft-molntjänster.

**Uppdelning av säkerhetsskäl**: denna Referensarkitektur avgränsar Vnet för hanteringsåtgärder och verksamheten. Tillåt hantering, inklusive trafiken ingående och utgående begränsningar med hjälp av NSG: er mellan nätverkssegment följande separat Vnet och undernät [Microsoft cloud services och nätverkssäkerhet](https://docs.microsoft.com/azure/best-practices-network-security) bästa praxis.

**Resurshantering**: Azure-resurser som virtuella datorer, virtuella nätverk och belastningsutjämnare som hanteras genom att gruppera dem tillsammans i [Azure-resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource). Åtkomstkontroll resursroller kan tilldelas till varje resursgrupp för att begränsa åtkomsten till endast behöriga användare.

**Komma åt Åtkomstkontrollsbegränsningar**: Använd [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (RBAC) för att hantera resurser i appen med [anpassade roller](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) RBAC kan användas för att begränsa åtgärder som DevOps kan utföra på varje nivå. När du beviljar behörighet, använda den [principen om minsta behörighet](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Logga alla administrativa åtgärder och utför regelbundna granskningar för att säkerställa att inga ändringar i konfigurationen har planerats.

**Internetåtkomst**: denna Referensarkitektur utnyttjar [Azure Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) som internet facing gateway och läsa in belastningsutjämning. Vissa kunder kan också överväga att använda virtuella nätverksinstallationer från tredje part för ytterligare skyddslager för nätverk säkerhet som ett alternativ till den [Azure Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Azure Security Center**: den [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) innehåller en central för säkerhetsstatusen för resurserna i prenumerationen och ger rekommendationer som kan förhindra angripna resurser. Det kan också användas för att aktivera mer detaljerad principer. Till exempel kan principer tillämpas på specifika resursgrupper som gör att företag kan anpassa dess hållningsdata risker. Du rekommenderas att kunder som aktiverar Azure Security Center i sina Azure-prenumeration.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC Cloud Security principer efterlevnad dokumentation

Tjänsten kronornas kommersiella (agency som fungerar för att förbättra kommersiell och inköp av regering) förnyas klassificeringen av Microsoft i omfånget enterprise molntjänster G moln v6, som omfattar alla dess erbjudanden på officiella nivå. Information om Azure och G moln finns i den [Azure Storbritannien G-Cloud security assessment sammanfattning](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Det här utkastet justeras mot 14 moln-säkerhetsprinciper som finns dokumenterade i NCSC [moln säkerhetsprinciper](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) för att säkerställa en miljö som har stöd för arbetsbelastningar som klassificeras som Storbritannien officiella.

Den [kunden ansvar matrisen](https://aka.ms/blueprintuk-gcrm) (Excel-arbetsbok) visar alla 14 molnet säkerhetsprinciper och matrisen för varje princip (eller princip kapitel) anger om principen implementeringen ansvarar för Microsoft, kund, eller delas mellan två.

Den [princip implementering matrisen](https://aka.ms/ukwebappblueprintpim) (Excel-arbetsbok) visar alla 14 molnet säkerhetsprinciper och matrisen anger, för varje princip (eller princip kapitel) som är utsedd ansvar för en kund i kunden Ansvarsområden matris, 1) om utkast automation implementerar principen och 2) en beskrivning av hur implementeringen överensstämmer med princip requirement(s). Det här innehållet är också tillgänglig [här](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

Dessutom publicerade Cloud Security Alliance (CSA) matrisen molntjänster kontroll för att stödja kunder vid utvärderingen av molntjänstleverantörer och identifiera frågor bör besvaras innan du fortsätter till molntjänster. Svar Microsoft Azure besvaras CSA konsensus Assessment initiativ frågeformulär ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), som beskriver hur Microsoft adresser föreslagna principerna.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Det finns två sätt som distribution kan användas för att distribuera det här utkastet automation. Den första metoden använder ett PowerShell-skript, medan den andra metoden använder Azure-portalen för att distribuera den referens för arkitekturen. Detaljerade stegvisa anvisningar finns [här](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast i informativt syfte. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UTTRYCKLIGA ELLER UNDERFÖRSTÅDDA, AVSEENDE INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls ”som-är”. Information och åsikter som uttrycks i detta dokument, inklusive Webbadresser och andra webbplatsreferenser, kan ändras utan föregående meddelande. Kunder som det här dokumentet ansvar använder den.
 - Det här dokumentet innehåller inte kunder inga juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller -lösningar.
 - Kunderna får kopiera och använda det här dokumentet som intern referens.
 - Vissa rekommendationerna i det här dokumentet kan resultera i ökade data, nätverk eller beräkning Resursanvändning i Azure och kan öka kostnaderna för en kund Azure licens eller prenumeration.
 - Den här arkitekturen är avsett att utgöra grunden för kunder att justera sina särskilda krav och ska inte användas som – i en produktionsmiljö.
 - Det här dokumentet har utvecklats som referens och ska inte användas för att definiera alla metoder som en kund kan uppfylla specifika efterlevnadskrav och -förordningar. Kunder bör söka juridiskt stöd från sin organisation på godkända kund-implementeringar.
