---
title: Handlingsplan för säkerhet och efterlevnad i Azure – IaaS-webbprogram på tre nivåer för Storbritannien och EUT
description: Handlingsplan för säkerhet och efterlevnad i Azure – IaaS-webbprogram på tre nivåer för Storbritannien och EUT
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 602e4356ccd9eb45855462a7a25e0966dc176b4f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899944"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Handlingsplan för säkerhet och efterlevnad i Azure – IaaS-webbprogram på tre nivåer för Storbritannien och EUT

## <a name="overview"></a>Översikt

 Den här artikeln innehåller vägledning och automatiserings skript för att leverera en Microsoft Azure av en webbaserad arkitektur på tre nivåer som är lämplig för hantering av många arbets belastningar som klassificeras som officiella i Storbritannien.

 Med hjälp av en infrastruktur som kod metod distribuerar uppsättningen [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mallar en miljö som motsvarar de nationella cyberhot Security Center (NCSC) 14- [moln säkerhets principer](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) och Center for Internet Security (CIS) [ Kritiska säkerhets kontroller](https://www.cisecurity.org/critical-controls.cfm).

 NCSC rekommenderar att deras moln säkerhets principer används av kunder för att utvärdera tjänstens säkerhets egenskaper och för att hjälpa till att förstå ansvars fördelningen mellan kunden och leverantören. Vi har tillhandahållit information om var och en av dessa principer för att hjälpa dig att förstå delning av ansvars områden.

 Den här arkitekturen och motsvarande Azure Resource Manager mallar stöds av Microsoft whitepaper, [14 Cloud Security-kontroller för brittiskt moln med Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). I den här dokument katalogen kan Azure-tjänster anpassas efter de brittiska NCSC 14-molnets säkerhets principer, vilket gör det möjligt för organisationer att snabbt spåra sin förmåga att uppfylla sina krav på efterlevnad med molnbaserade tjänster globalt och i Storbritannien på Microsoft Azure kunde.

 Den här mallen distribuerar infrastrukturen för arbets belastningen. Program kod och stöd för affärs nivå och program vara för datanivå måste installeras och konfigureras. Detaljerade distributions anvisningar finns [här](https://aka.ms/ukwebappblueprintrepo).

 Om du inte har en Azure-prenumeration kan du registrera dig snabbt och enkelt – [Kom igång med Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Arkitektur diagram och-komponenter

 Azure-mallarna levererar en webb program arkitektur på tre nivåer i en Azure-molnbaserad miljö som stöder de officiella arbets belastningarna i Storbritannien. Arkitekturen ger en säker hybrid miljö som utökar ett lokalt nätverk till Azure, vilket gör det möjligt att komma åt webbaserade arbets belastningar på ett säkert sätt av företags användare eller från Internet.

![IaaS-webbprogram på tre nivåer för det officiella referens arkitektur diagrammet för Storbritannien](images/ukofficial-iaaswa-architecture.png?raw=true "IaaS-webbprogram på tre nivåer för det officiella referens arkitektur diagrammet för Storbritannien")

 Den här lösningen använder följande Azure-tjänster. Information om distributions arkitekturen finns i avsnittet [distributions arkitektur](#deployment-architecture) .

(1)/16 Virtual Network-operationellt VNet
- (3)/24 undernät – 3-nivå (webb, biz, data)
- (1)/27 undernät – lägger till
- (1)/27 undernät – Gateway-undernät
- (1)/29 undernät – Application Gateway undernät
- Använder standard-DNS (Azure-tillhandahöll)
- Peering har Aktiver ATS för Management VNet
- Nätverks säkerhets grupp (NSG) för att hantera trafikflöde

(1)/24 Virtual Network-Management VNet
- (1)/27 undernät
- Använder (2) lägger till DNS-och (1) Azure DNS poster
- Peering har Aktiver ATS för VNet i drift
- Nätverks säkerhets grupp (NSG) för att hantera trafikflöde

(1) Application Gateway
- WAF – aktive rad
- WAF läge – förebyggande
- Regel uppsättning: OWASP 3,0
- HTTP-lyssnare på port 80
- Anslutning/trafik som regleras via NSG
- Den offentliga IP-adressens slut punkt som definierats (Azure)

(1) VPN-Route-baserad, plats-2-plats IPSec VPN-tunnel
- Den offentliga IP-adressens slut punkt som definierats (Azure)
- Anslutning/trafik som regleras via NSG
- (1) lokal nätverksgateway (lokal plats slut punkt)
- (1) Azure-nätverksgateway (Azure-slutpunkt)

(9) Virtual Machines – alla virtuella datorer distribueras med Azure IaaSs DSC-inställningar för program mot skadlig kod

- (2) Active Directory Domain Services domänkontrollanter (Windows Server 2012 R2)
  - (2) DNS-serverrollen – 1 per virtuell dator
  - (2) nätverkskort som är anslutna till drift-VNet-1 per virtuell dator
  - Båda är domänanslutna till den domän som definierats i mallen
    - Domän som skapats som en del av distributionen


- (1) (skydds Host) hantering av virtuell dator
  - 1 nätverkskort i hanterings-VNet med offentlig IP-adress
    - NSG används för att begränsa trafik (in/ut) till vissa källor
  - Inte domänansluten


- (2) virtuella webb skikt
  - (2) IIS-serverrollen – 1 per virtuell dator
  - (2) nätverkskort som är anslutna till drift-VNet-1 per virtuell dator
  - Inte domänansluten


- (2) virtuella datorer i biz-nivå
  - (2) nätverkskort som är anslutna till drift-VNet-1 per virtuell dator
  - Inte domänansluten


- (2) virtuella datorer med data skikt
  - (2) nätverkskort som är anslutna till drift-VNet-1 per virtuell dator
  - Inte domänansluten

Tillgänglighetsuppsättningar
- (1) Active Directory-domän kontrollant VM Set-2 virtuella datorer
- (1) VM-uppsättning för virtuella datorer med 2 virtuella datorer
- (1) BIZ-nivå VM Set-2 VM: ar
- (1) datanivå VM Set-2 VM: ar

Load Balancer
- (1) Load Balancer på webb nivå
- (1) BIZ-nivå Load Balancer
- (1) datanivå Load Balancer

Storage
- (14) totalt antal lagrings konton
  - Tillgänglighets uppsättning för Active Directory-domän Controller
    - (2) primära lokalt redundanta lagrings konton (LRS)-1 för varje virtuell dator  
    - (1) diagnostiskt lokalt redundant lagrings konto (LRS) för Lägg till tillgänglighets uppsättning
  - Virtuell dator för hanterings hopp
    - (1) primärt lokalt redundant lagrings konto (LRS) för den virtuella hopp datorn
    - (1) diagnostiskt lokalt redundant lagrings konto (LRS) för den virtuella datorn i hoppet
  - Virtuella webb skikt
    - (2) primära lokalt redundanta lagrings konton (LRS)-1 för varje virtuell dator  
    - (1) diagnostiskt lokalt redundant lagrings konto (LRS) för tillgänglighets uppsättningen för webb nivån
  - Virtuella BIZ-skikt
    - (2) primära lokalt redundanta lagrings konton (LRS)-1 för varje virtuell dator  
    - (1) diagnostiskt lokalt redundant lagrings konto (LRS) för tillgänglighets uppsättningen för BIZ-nivån
  - Virtuella datorer för data skikt
    - (2) primära lokalt redundanta lagrings konton (LRS)-1 för varje virtuell dator  
    - (1) diagnostiskt lokalt redundant lagrings konto (LRS) för data skiktets tillgänglighets uppsättning

### <a name="deployment-architecture"></a>Distributions arkitektur:

**Lokalt nätverk**: Ett privat lokalt nätverk som implementerats i en organisation.

**Virtuellt nätverk för produktion**: Produktionens [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (Virtual Network) är värd för programmet och andra drift resurser som körs i Azure. Varje VNet kan innehålla flera undernät som används för att isolera och hantera nätverks trafik.

**Webb nivå**: Hanterar inkommande HTTP-begäranden. Svaren returneras via den här nivån.

**Affärs nivå**: Implementerar affärs processer och annan funktionell logik för systemet.

**Databas nivå**: Tillhandahåller beständig data lagring med [SQL Server Always on-tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx) för hög tillgänglighet. Kunder kan använda [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) som ett PaaS alternativ.

**Gateway**: [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) tillhandahåller anslutning mellan routrarna i det lokala nätverket och produktions-VNet.

**Internet-gateway och offentlig IP-adress**: Internet-gatewayen exponerar program tjänster för användare via Internet. Trafik åtkomst till dessa tjänster skyddas med hjälp av en [Application Gateway](../../application-gateway/overview.md) som erbjuder hantering av Layer 7-Routning och belastnings utjämning med brand vägg för webbaserade program (WAF).

**Hanterings-VNet**: Detta [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) innehåller resurser som implementerar hanterings-och övervaknings funktioner för arbets belastningar som körs i produktions-VNet.

**Hopp**: Kallas även för en [skydds-värd](https://en.wikipedia.org/wiki/Bastion_host), som är en säker virtuell dator i nätverket som administratörer använder för att ansluta till virtuella datorer i produktions-VNet. Jumpboxen har en NSG som endast tillåter fjärrtrafik från offentliga IP-adresser på en säker lista. För att tillåta fjärr skrivbords trafik måste källa för trafiken definieras i NSG. Hantering av produktions resurser sker via RDP med hjälp av en skyddad virtuell hoppsida-dator.

**Användardefinierade vägar**: [Användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) används för att definiera flödet av IP-trafik i Azure virtuella nätverk.

**Nätverksansluten virtuella nätverk**: Virtuella nätverk för produktion och hantering är anslutna med [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)-peering.
Dessa virtuella nätverk hanteras fortfarande som separata resurser, men visas som en för alla anslutningar för dessa virtuella datorer. Dessa nätverk kommunicerar med varandra direkt med hjälp av privata IP-adresser. VNet-peering omfattas av virtuella nätverk som finns i samma Azure-region.

**Nätverks säkerhets grupper**: [NSG: er](../../virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller Access Control listor som tillåter eller nekar trafik i ett VNet. NSG: er kan användas för att skydda trafik i ett undernät eller på en individuell VM-nivå.

**Active Directory Domain Services (AD DS)** : Den här arkitekturen tillhandahåller en dedikerad [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) -distribution.

**Loggning och granskning**: [Azure aktivitets logg](../../azure-monitor/platform/activity-logs-overview.md) fångar upp åtgärder som vidtas på resurserna i din prenumeration, till exempel vem som initierade åtgärden, när åtgärden utfördes, status för åtgärden och värdena för andra egenskaper som kan hjälpa dig att undersöka åtgärden. Azure aktivitets logg är en Azure-plattform som fångar upp alla åtgärder för en prenumeration. Loggarna kan arkiveras eller exporteras om det behövs.

**Nätverks övervakning och aviseringar**: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) är en plattforms tjänst som tillhandahåller insamling av nätverks paket, flödes loggning, verktyg för topologi och diagnostik för nätverks trafik i din virtuella nätverk.

## <a name="guidance-and-recommendations"></a>Vägledning och rekommendationer

### <a name="business-continuity"></a>Verksamhetskontinuitet

**Hög tillgänglighet**: Server arbets belastningarna grupperas i en [tillgänglighets uppsättning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att säkerställa hög tillgänglighet för virtuella datorer i Azure. Den här konfigurationen hjälper till att säkerställa att under en planerad eller oplanerad underhålls händelse är minst en virtuell dator tillgänglig och uppfyller 99,95% Azure SLA.

### <a name="logging-and-audit"></a>Loggning och granskning

**Övervakning**: [Azure Monitor](../../azure-monitor/overview.md) är plattforms tjänsten som tillhandahåller en enda källa för att övervaka aktivitets loggen, måtten och diagnostikloggar för alla dina Azure-resurser. Azure Monitor kan konfigureras för att visualisera, fråga, cirkulera, arkivera och agera på de mått och loggar som kommer från resurser i Azure. Det rekommenderas att resursbaserade Access Control används för att skydda gransknings loggen för att se till att användarna inte har möjlighet att ändra loggarna.

**Aktivitets loggar**: Konfigurera [Azures aktivitets loggar](../../azure-monitor/platform/activity-logs-overview.md) för att ge inblick i de åtgärder som utfördes på resurser i din prenumeration.

**Diagnostikloggar**: [Diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-overview.md) är alla loggar som genereras av en resurs. Loggarna kan innehålla Windows Event System-loggar, BLOB-, tabell-och Queue-loggar.

**Brand Väggs loggar**: Application Gateway tillhandahåller fullständiga diagnostik-och åtkomst loggar. Brandväggsloggar är tillgängliga för Application Gateway-resurser som har WAF aktiverat.

**Logg arkivering**: Logg data lagringen kan konfigureras att skriva till ett centraliserat Azure Storage-konto för arkivering och en definierad kvarhållningsperiod. Loggar kan bearbetas med hjälp av Azure Monitor loggar eller av SIEM-system från tredje part.

### <a name="identity"></a>Identitet

**Active Directory Domain Services**: Den här arkitekturen ger en Active Directory Domain Services distribution i Azure. Specifika rekommendationer för att implementera Active Directory i Azure finns i följande artiklar:

[Utöka Active Directory Domain Services (AD DS) till Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

[Rikt linjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Active Directory-integrering**: Som ett alternativ till en dedikerad AD DS-arkitektur kan kunderna vilja använda [Azure Active Directory](/azure/architecture/reference-architectures/identity) -integrering eller [Active Directory i Azure som är ansluten till en lokal skog](/azure/architecture/reference-architectures/identity).

### <a name="security"></a>Säkerhet

**Hanterings säkerhet**: Den här skissen gör det möjligt för administratörer att ansluta till det virtuella hanterings nätverket och gå via RDP från en betrodd källa. Nätverks trafiken för det virtuella hanterings nätverket styrs med NSG: er. Åtkomst till port 3389 är begränsad till trafik från ett betrott IP-intervall som har åtkomst till det undernät som innehåller hoppet.

Kunder kan också överväga att använda en [förbättrad säkerhets modell](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) för att skydda miljön när de ansluter till det virtuella hanterings nätverket och den andra hoppen. Vi rekommenderar att för förbättrade säkerhets kunder använder en [privilegie rad åtkomst arbets Station](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) och RDGateway-konfiguration. Användningen av virtuella nätverks installationer och offentliga/privata DMZs kommer att erbjuda ytterligare säkerhets förbättringar.

**Skydda nätverket**: [Nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (NSG: er) rekommenderas för varje undernät för att tillhandahålla en andra skydds nivå mot inkommande trafik som kringgår en felaktigt konfigurerad eller inaktive rad Gateway. Exempel – [Resource Manager-mall för att distribuera en NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Säkra offentliga slut punkter**: Internet-gatewayen exponerar program tjänster för användare via Internet. Trafik åtkomst till dessa tjänster skyddas med hjälp av en [Application Gateway](../../application-gateway/overview.md), vilket ger en brand vägg för webb program och HTTPS-protokoll.

**IP-intervall**: IP-intervallen i arkitekturen är föreslagna intervall. Kunderna bör överväga sin egen miljö och använda lämpliga intervall.

**Hybrid anslutning**: Molnbaserade arbets belastningar är anslutna till det lokala data centret via IPSEC VPN med hjälp av Azure-VPN Gateway. Kunderna bör se till att de använder en lämplig VPN Gateway för att ansluta till Azure. Exempel – [VPN gateway Resource Manager-mall](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Kunder som kör storskaliga affärs kritiska arbets belastningar med Big data-krav kan vilja överväga en hybrid nätverks arkitektur med [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) för privat nätverks anslutning till Microsofts moln tjänster.

**Separering av problem**: Den här referens arkitekturen separerar virtuella nätverk för hanterings åtgärder och affärs åtgärder. Separata virtuella nätverk och undernät tillåter trafik hantering, inklusive trafik ingångs-och utgångs begränsningar, genom att använda NSG: er mellan nätverks segment som följer [Microsofts moln tjänster och](/azure/architecture/vdc/networking-virtual-datacenter) Metod tips för nätverks säkerhet.

**Resurs hantering**: Azure-resurser, till exempel virtuella datorer, virtuella nätverk och belastningsutjämnare hanteras genom att gruppera dem tillsammans i [Azure-resurs grupper](../../azure-resource-manager/resource-group-overview.md). Resursbaserade Access Control roller kan sedan tilldelas till varje resurs grupp för att begränsa åtkomsten till endast behöriga användare.

**Access Control begränsningar**: Använd [rollbaserad Access Control](../../role-based-access-control/role-assignments-portal.md) (RBAC) för att hantera resurserna i ditt program med hjälp av [anpassade roller](../../role-based-access-control/custom-roles.md) RBAC kan användas för att begränsa de åtgärder som DevOps kan utföra på varje nivå. När du beviljar behörigheter använder du [principen för minsta behörighet](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Logga alla administrativa åtgärder och utför regelbundna granskningar för att säkerställa att inga ändringar i konfigurationen har planerats.

**Internet åtkomst**: Den här referens arkitekturen använder [Azure Application Gateway](../../application-gateway/overview.md) som gateway för Internet och belastnings utjämning. Vissa kunder kan också överväga att använda virtuella nätverk från tredje part för ytterligare lager av nätverks säkerhet som ett alternativ till [Azure Application Gateway](../../application-gateway/overview.md).

**Azure Security Center**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) tillhandahåller en central vy över säkerhets status för resurser i prenumerationen och ger rekommendationer som förhindrar komprometterade resurser. Det kan också användas för att aktivera mer detaljerade principer. Principer kan till exempel tillämpas på vissa resurs grupper, vilket gör det möjligt för företaget att skräddarsy sin position till risk. Vi rekommenderar att kunderna aktiverar Azure Security Center i sin Azure-prenumeration.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentation om NCSC Cloud Security princips Compliance

Den kommersiella tjänsten för Kron tjänster (ett organ som arbetar för att förbättra företagets handels-och anskaffnings aktivitet) har förnyat klassificeringen av Microsofts moln tjänster i företags klass till G-Cloud V6, som omfattar alla sina erbjudanden på den officiella nivån. Information om Azure och G-Cloud finns i sammanfattningen av [Azures utvärdering av säkerhets utvärderingen](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Den här skissen motsvarar de 14 moln säkerhets principer som dokumenteras i NCSC [Cloud Security-principer](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) för att säkerställa en miljö som stöder arbets belastningar som klassificeras som Storbritannien-officiellt.

I [matrisen för kund ansvar](https://aka.ms/ukofficial-crm) (Excel-arbetsbok) visas alla 14 moln säkerhets principer och matrisen för varje princip (eller princip kapitel), om princip implementeringen är ansvar hos Microsoft, kunden eller delas mellan de två.

I [matrisen för princip implementering](https://aka.ms/ukofficial-iaaswa-pim) (Excel-arbetsbok) visas alla 14 moln säkerhets principer, och matrisen anger för varje princip (eller princip kapitel) som är ett kund ansvar i matrisen för kund ansvar, 1) om skiss automatiseringen implementerar principen och 2) en beskrivning av hur implementeringen överensstämmer med princip kraven (s).

Dessutom publicerade Cloud Security Alliance (CSA) moln kontroll mat ris som stöd för kunder i utvärderingen av moln leverantörer och för att identifiera frågor som ska besvaras innan flytt till moln tjänster. Microsoft Azure besvarade CSA-enkäten för CSA bedömning av initiativ ([CSA-CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), som beskriver hur Microsoft hanterar de föreslagna principerna.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Det finns två metoder som distributions användare kan använda för att distribuera den här skiss automatiseringen. Den första metoden använder ett PowerShell-skript, medan den andra metoden använder Azure Portal för att distribuera referens arkitekturen. Detaljerade distributions anvisningar finns [här](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Ansvarsfriskrivning

 - Det här dokumentet är endast avsett för information. MICROSOFT LÄMNAR INGA GARANTIER, UTTRYCKLIGA, UNDERFÖRSTÅDDA ELLER LAGSTADGADE, ENLIGT INFORMATIONEN I DET HÄR DOKUMENTET. Detta dokument tillhandahålls "i befintligt skick". Information och vyer som uttrycks i detta dokument, inklusive URL: er och andra webbplats referenser, kan ändras utan föregående meddelande. Kunder som läser det här dokumentet bär risken för att använda det.
 - Detta dokument ger inte kunderna några juridiska rättigheter till någon immateriell egendom i någon Microsoft-produkt eller-lösning.
 - Kunder får kopiera och använda det här dokumentet för intern referens.
 - Vissa rekommendationer i det här dokumentet kan leda till ökad data-, nätverks-eller beräknings resursanvändning i Azure och kan öka en kunds Azure-licens eller prenumerations kostnader.
 - Den här arkitekturen är avsedd att fungera som grund för kunderna att anpassa sig efter sina särskilda krav och bör inte användas i en produktions miljö.
 - Det här dokumentet utvecklas som en referens och bör inte användas för att definiera alla medel som en kund kan uppfylla särskilda krav och föreskrifter för efterlevnad. Kunderna bör söka juridisk support från organisationen om godkända kund implementeringar.
