---
title: Kontrol lera inkommande trafik i Azure Australien
description: En guide för att kontrol lera inkommande trafik i Azure Australien för att uppfylla de australiska myndighets kraven för säkra Internet-gatewayer
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 808a615885129af1be9b7fdcdb64d5a8c5a25e40
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571658"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Kontrol lera inkommande trafik i Azure Australien

Ett kärn element i att skydda IKT-system är att kontrol lera nätverks trafiken. Trafiken bör begränsas till det enda som krävs för att ett system ska fungera för att minska risken för problem.

Den här guiden ger information om hur inkommande (inkommande) nätverks trafik fungerar i Azure och rekommendationer för att implementera nätverks säkerhets kontroller för ett Internet-anslutet system.

Nätverket styrs av konsument vägledningen för australisk cyberhot Security Centre (ACSC) och avsikten med ACSCs information säkerhets hand bok (ISM).

## <a name="requirements"></a>Krav

De övergripande säkerhets kraven för samväldet samordnings system definieras i ISM-systemet. För att under lätta samverkande entiteter vid implementering av nätverks [säkerhet har ACSC publicerat ACSC Protect: Att implementera nätverks segmentering och särskiljande](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm), och för att hjälpa till med att skydda system i moln miljöer, har ACSC publicerat [Cloud Computing Security för klienter](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf).

De här guiderna beskriver kontexten för att implementera nätverks säkerhet och kontroll av trafiken och innehåller praktiska rekommendationer för nätverks design och-konfiguration.

Microsoft [Cloud Data Security för innehavare av Microsoft Azures](https://aka.ms/au-irap) guide på sidan australisk i tjänst förtroende portalen fokuserar på vissa Microsoft-tekniker som gör att du kan uppfylla råd i ACSC-publikationer.

Följande viktiga krav, som identifieras i publikationerna från ACSC, är viktiga för att kontrol lera inkommande trafik i Azure:

|Beskrivning|Source|
|---|---|
|**Implementera nätverks segmentering och uppdelning, till exempel en arkitektur på n-nivå, som använder värdbaserade brand väggar och CSP: s nätverks åtkomst kontroller för att begränsa inkommande och utgående VM-nätverksanslutningar till endast de portar/protokoll som krävs.**| _Molnbaserad data behandling för klienter_|
|**Implementera tillräcklig hög bandbredd, låg latens, tillförlitlig nätverks anslutning** mellan klient organisationen (inklusive klientens fjärran vändare) och moln tjänsten för att uppfylla klientens tillgänglighets krav  | _Molnbaserad data behandling för klienter_|
|**Använd teknik på mer än bara nätverks nivån**. Varje värd och nätverk bör vara segmenterade och åtskiljda, där det är möjligt, på den lägsta nivån som kan hanteras i praktiken. I de flesta fall gäller segmentering och uppdelning från data länk skiktet upp till och inklusive program lagret. i känsliga miljöer kan den fysiska isoleringen emellertid vara lämplig. Värdbaserade och nätverks omfattande åtgärder bör distribueras på ett kompletterande sätt och övervakas centralt. Att använda en brand vägg eller säkerhetsinstallation eftersom det bara är tillräckligt med säkerhets mått. |_ACSC skydda: Implementera nätverks segmentering och åtskillnad_|
|**Använd principerna för minsta behörighet och behöver gång till för att gång till veta**. Om en värd, tjänst eller nätverk inte behöver kommunicera med en annan värd, tjänst eller nätverk, bör den inte tillåtas. Om en värd, tjänst eller nätverk bara behöver kommunicera med en annan värd, tjänst eller nätverk som använder vissa portar eller protokoll, bör andra portar eller protokoll inaktive ras. Genom att använda dessa principer i ett nätverk kompletterar du om användar behörigheterna minimeras och den övergripande säkerhets position i miljön ökar avsevärt. |_ACSC skydda: Implementera nätverks segmentering och åtskillnad_|
|**Separera värdar och nätverk utifrån deras känslighet eller kritiskhet för affärs åtgärder**. Separering kan uppnås genom att använda olika maskin vara eller plattformar beroende på olika säkerhets klassificeringar, säkerhets domäner eller tillgänglighets-/integritets krav för vissa värdar eller nätverk. I synnerhet separata hanterings nätverk och Överväg att fysiskt isolera out-of-band-hanterings nätverk för känsliga miljöer. |_ACSC skydda: Implementera nätverks segmentering och åtskillnad_|
|**Identifiera, autentisera och auktorisera åtkomst av alla entiteter till alla andra entiteter**. Alla användare, värdar och tjänster bör ha åtkomst till enbart de användare, värdar och tjänster som krävs för att utföra sina angivna uppgifter eller funktioner. Alla äldre eller lokala tjänster som kringgår eller nedgraderar styrkan hos identifierings-, autentiserings-och godkännande tjänster bör inaktive ras och användningen bör övervakas noga. |_ACSC skydda: Implementera nätverks segmentering och åtskillnad_|
|**Implementera Tillåt lista över nätverks trafik i stället för neka-lista**. Tillåt endast åtkomst för känd bra nätverks trafik (det vill säga det som identifieras, autentiseras och auktoriseras), i stället för att neka åtkomst till känd felaktig nätverks trafik (till exempel blockera en viss adress eller tjänst). Vit listning resulterar i en överlägsen säkerhets princip för att göra svartlistad och förbättrar markant en organisations kapacitet för att upptäcka och utvärdera potentiella nätverks intrång. |_ACSC skydda: Implementera nätverks segmentering och åtskillnad_|
|

Den här artikeln innehåller information och rekommendationer om hur dessa krav kan uppfyllas för system som distribueras i Azure med både infrastruktur som en tjänst (IaaS) och PaaS (Platform as a Service). Du bör också läsa artikeln om att [kontrol lera utgående trafik i Azure Australien](gateway-egress-traffic.md) för att helt förstå styra nätverks trafiken i Azure.

## <a name="architecture"></a>Arkitektur

Om du är involverad i utformningen eller implementeringen av nätverks säkerhet och inkommande trafik kontroller måste du först förstå hur inkommande nätverks trafik fungerar i Azure över både IaaS och PaaS. Det här avsnittet innehåller en översikt över möjliga start punkter där nätverks trafik kan komma åt en resurs som finns i Azure och de säkerhets kontroller som är tillgängliga för att begränsa och kontrol lera trafiken. Var och en av dessa komponenter beskrivs i detalj i de återstående avsnitten i den här hand boken.

### <a name="architecture-components"></a>Arkitektur komponenter

Det arkitektur diagram som visas här beskriver möjliga sökvägar som nätverks trafiken kan vidta för att ansluta till en tjänst som finns i Azure. Dessa komponenter är indelade i Azure, IaaS ingress, PaaSs ingress och säkerhets kontroll, beroende på vilken funktion de tillhandahåller för ingångs trafik.

![Arkitektur](media/ingress-traffic.png)

### <a name="azure-components"></a>Azure-komponenter

|Komponent | Beskrivning|
|---|---|
|**DDoS Protection** | DDoS-attacker (distributed denial of Service) försöker avsluta ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet. Azure innehåller DDoS Protection automatiskt via Azure-plattformen och ger ytterligare funktioner för minskning som kan aktive ras för vissa program för mer detaljerad kontroll.|
| **Traffic Manager** | Azure Traffic Manager är en Domain Name System (DNS) baserad trafikbelastnings utjämning som kan distribuera trafik optimalt till tjänster i Azure-regioner, samtidigt som du får hög tillgänglighet och svars tider. Traffic Manager använder DNS för att dirigera klient begär anden till den lämpligaste slut punkten baserat på en Traffic-routningsmetod och hälso tillståndet för slut punkterna.|
| **ExpressRoute** | ExpressRoute är en dedikerad nätverks anslutning för att konsumera Microsofts moln tjänster. Den tillhandahålls via en anslutnings leverantör och ger högre tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet. En ExpressRoute-krets representerar den logiska anslutningen mellan den lokala infrastrukturen och Microsofts moln tjänster via en anslutnings leverantör.|
| **ExpressRoute privat peering** | ExpressRoute privata peering är en anslutning mellan den lokala miljön och privata virtuella Azure-nätverk. Privat peering ger åtkomst till Azure-tjänster som Virtual Machines som distribueras i ett virtuellt nätverk. De resurser och virtuella nätverk som nås via privat peering betraktas som en utökning av en organisations kärn nätverk. Privat peering ger dubbelriktad anslutning mellan det lokala nätverket och virtuella Azure-nätverk med hjälp av privata IP-adresser.|
| **ExpressRoute Microsoft-peering** | ExpressRoute Microsoft-peering är en anslutning mellan den lokala miljön och Microsoft och offentliga Azure-tjänster. Detta inkluderar anslutning till Office 365-, Dynamics 365-och Azure PaaS-tjänster. Peering har upprättats över offentliga IP-adresser som ägs av organisationen eller anslutnings leverantören. Inga tjänster är tillgängliga via ExpressRoute Microsoft-peering som standard och en organisation måste välja de tjänster som krävs. Den här processen ger sedan anslutning till samma slut punkter som är tillgängliga på Internet.|
|

### <a name="iaas-ingress-components"></a>IaaS ingress-komponenter

|Komponent | Beskrivning|
|---|---|
|**Nätverks gränssnitt** | Ett nätverks gränssnitt är en resurs som finns i Azure. Den är kopplad till en virtuell dator och har tilldelats en privat, icke-Internet-dirigerad IP-adress från det undernät som den är associerad med. Den här IP-adressen är dynamiskt eller statiskt tilldelad via Azure Resource Manager.|
|**Undernät** | Ett undernät är ett IP-adressintervall som skapas inom ett VNet. Flera undernät kan skapas i ett VNet för nätverks segmentering.|
| **Virtual Network (VNet)** | Ett VNet är en grundläggande resurs i Azure som tillhandahåller en plattform och en gränser för att distribuera resurser och aktivera kommunikation. VNet finns i en Azure-region och definierar IP-adressutrymmet och nätverks gränser för inbyggda VNet-resurser som Virtual Machines.|
| **VNet-peering** | VNet-peering är ett konfigurations alternativ i Azure som aktiverar direkt kommunikation mellan två virtuella nätverk utan att det krävs någon Virtual Network Gateway. När peer-kopplats kan de två virtuella nätverk kommunicera direkt och ytterligare konfiguration kan styra användningen av Virtual Network gatewayer och andra överförings alternativ.|
| **Offentlig IP-adress** | En offentlig IP-adress är en resurs som reserverar en av Microsofts ägda offentliga IP-adresser från Internet från det angivna området för användning i det virtuella nätverket. Den kan kopplas till ett särskilt nätverks gränssnitt, vilket gör att resursen kan nås från Internet-, ExpressRoute-och PaaS-system.|
| **ExpressRoute-Gateway** | En ExpressRoute-Gateway är ett objekt i en Virtual Network ger anslutning och routning från Virtual Network till lokala nätverk via privat peering på en ExpressRoute-krets.|
| **VPN Gateway** | En VPN Gateway är ett objekt i en Virtual Network som tillhandahåller en krypterad tunnel från en Virtual Network till ett externt nätverk. Den krypterade tunneln kan vara plats-till-plats för dubbelriktad kommunikation med en lokal miljö, ett annat virtuellt nätverk eller en moln miljö eller punkt-till-plats för kommunikation med en enda slut punkt.|
| **PaaS VNet-integrering** | Många PaaS-funktioner kan distribueras till, eller integreras med, en Virtual Network. Vissa PaaS-funktioner kan helt integreras med ett VNet och vara tillgängliga enbart via privata IP-adresser. Andra, till exempel Azure Load Balancer och Azure Application Gateway, kan ha ett externt gränssnitt med en offentlig IP-adress och ett internt gränssnitt med en privat IP-adress i det virtuella nätverket. I den här instansen kan trafiken intränger i Virtual Network via PaaS-funktionen.|
|

### <a name="paas-ingress-components"></a>PaaS ingress-komponenter

|Komponent | Beskrivning|
|---|---|
|**Värdnamn** | Azure PaaS-funktioner identifieras av ett unikt offentligt värdnamn som tilldelas när resursen skapas. Detta värdnamn registreras sedan i en offentlig DNS-domän, där den kan matchas mot en offentlig IP-adress.|
|**Offentlig IP-adress** | Om du inte har distribuerat i en VNet-integrerad konfiguration nås Azure PaaS-funktioner via en offentlig IP-adress med Internet. Den här adressen kan vara dedikerad till de särskilda resurserna, till exempel en offentlig Load Balancer eller kan associeras med en viss funktion som har en delad start punkt för flera instanser, till exempel Storage eller SQL. Den här offentliga IP-adressen kan nås från Internet, ExpressRoute eller från IaaS offentliga IP-adresser via Azure stamnät nätverket.|
|**Serviceslutpunkter** | Tjänst slut punkter tillhandahåller en direkt privat anslutning från en Virtual Network till en speciell PaaS-funktion. Tjänst slut punkter, som endast är tillgängliga för en delmängd PaaS-funktioner, ger bättre prestanda och säkerhet för resurser i ett VNet-åtkomst till PaaS.|
|

### <a name="security-controls"></a>Säkerhets kontroller

|Komponent | Beskrivning|
|---|---|
|**Nätverks säkerhets grupper (NSG: er)** | NSG: er styr trafik till och från virtuella nätverks resurser i Azure. NSG: er tillämpar regler för de trafikflöde som tillåts eller nekas, vilket omfattar trafik i Azure och mellan Azure och externa nätverk, till exempel lokalt eller Internet. NSG: er tillämpas på undernät i ett virtuellt nätverk eller enskilda nätverks gränssnitt.|
|**PaaS-brandvägg** | Många PaaS-funktioner, t. ex. lagring och SQL har en inbyggd brand vägg för att styra inkommande nätverks trafik till den angivna resursen. Regler kan skapas för att tillåta eller neka anslutningar från vissa IP-adresser och/eller virtuella nätverk.|
|**PaaS-autentisering och Access Control** | Som en del av ett skiktat tillvägagångs sätt för säkerhet ger PaaS-funktioner flera mekanismer för att autentisera användare och kontrol lera behörigheter och åtkomst.|
|**Azure Policy** | Azure Policy är en tjänst i Azure för att skapa, tilldela och hantera principer. Dessa principer använder regler för att styra vilka typer av resurser som kan distribueras och konfigurationen av dessa resurser. Principer kan användas för att genomdriva efterlevnad genom att förhindra att resurser distribueras om de inte uppfyller kraven eller kan användas för övervakning för att rapportera om kompatibilitetsstatus.|
|

## <a name="general-guidance"></a>Allmänna riktlinjer

För att utforma och skapa säkra lösningar i Azure är det viktigt att förstå och kontrol lera nätverks trafiken så att endast identifierad och auktoriserad kommunikation kan ske. Syftet med den här vägledningen och den specifika komponent vägledningen i senare avsnitt är att beskriva de verktyg och tjänster som kan användas för att tillämpa de principer som beskrivs i _ACSC-skydd: Implementera nätverks segmentering och åtskillnad_ mellan Azure-arbetsbelastningar. Här finns information om hur du skapar en virtuell arkitektur för att skydda resurser när det inte går att tillämpa samma traditionella fysiska och nätverks kontroller som är möjliga i en lokal miljö.

### <a name="specific-focus-areas"></a>Vissa fokus områden

* Begränsa antalet start punkter till virtuella nätverk
* Begränsa antalet offentliga IP-adresser
* Överväg att använda en nav-och Ekrars nätverks design för virtuella nätverk som beskrivs i Microsoft Virtual Data Center (VDC)-dokumentationen
* Använda produkter med inbyggda säkerhetsfunktioner för inkommande anslutningar från Internet (till exempel Application Gateway, API Gateway, virtuella nätverks enheter)
* Begränsa kommunikations flöden till PaaS-funktioner till enbart de som behövs för systemfunktioner
* Distribuera PaaS i en VNet-integrerad konfiguration för ökad uppdelning och kontroll
* Konfigurera system för att använda krypterings metoder i enlighet med ACSC-konsument vägledning och ISM
* Använd identitetsbaserade skydd som autentisering och rollbaserad åtkomst kontroll utöver traditionella nätverks kontroller
* Implementera ExpressRoute för anslutning med lokala nätverk
* Implementera VPN för administrativ trafik och integrering med externa nätverk
* Använda Azure Policy för att begränsa regionerna och resurserna till enbart de som är nödvändiga för systemfunktionen
* Använd Azure Policy för att framtvinga grundläggande säkerhets konfiguration för resurser som är tillgängliga för Internet

### <a name="additional-resources"></a>Ytterligare resurser

|Resource | Länka|
|---|---|
|Australiska lagar och principer för regelefterlevnad, inklusive konsument vägledning|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure Virtual Data Center|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|ACSC nätverks segmentering|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|ACSC Cloud Security för klienter| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|ACSC information säkerhets hand bok|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Vägledning för komponenter

Det här avsnittet innehåller ytterligare information om de enskilda komponenterna som är relevanta för att intränga trafik till system som distribueras i Azure. I varje avsnitt beskrivs syftet med den specifika komponenten med länkar till dokumentation och konfigurations guider som kan användas för att hjälpa till med design-och build-aktiviteter.

## <a name="azure"></a>Azure

All kommunikation till resurser i Azure passerar genom Microsofts underhålls nätverks infrastruktur, som tillhandahåller anslutnings-och säkerhetsfunktioner. En mängd skydd placeras automatiskt på plats av Microsoft för att skydda Azure-plattformen och nätverks infrastrukturen och ytterligare funktioner är tillgängliga som tjänster i Azure för att kontrol lera nätverks trafiken och upprätta nätverks segmentering och avgränsning.

### <a name="ddos-protection"></a>DDoS Protection

Resurser som är tillgängliga för Internet är känsliga för DDoS-attacker. För att skydda mot dessa attacker tillhandahåller Azure DDoS-skydd på en Basic-och standard nivå.

Basic aktive ras automatiskt som en del av Azure-plattformen, inklusive Always trafikövervakning och i real tid av vanliga attacker på nätverks nivå, vilket ger samma försvar som används av Microsofts onlinetjänster. Hela skalningen av Azures globala nätverk kan användas för att distribuera och minska angrepps trafiken mellan regioner. Skydd tillhandahålls för IPv4-och IPv6 offentliga IP-adresser i Azure

Standard ger ytterligare funktioner för minskning av de grundläggande tjänst nivåer som är specifika för Azure Virtual Network-resurser. Skydds principer är justerade genom dedikerad trafik övervakning och Machine Learning-algoritmer. Skydd tillhandahålls för IPv4 offentliga IP-adresser för Azure.

|Resource|Länka|
|---|---|
|Översikt över Azure DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Metod tips för Azure DDoS|[https://docs.microsoft.com/azure/security/azure-ddos-best-practices](https://docs.microsoft.com/azure/security/azure-ddos-best-practices)|
|Hantera DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager används för att hantera inkommande trafik genom att kontrol lera vilka slut punkter i ett program som tar emot anslutningar. För att skydda mot förlust av system eller program som är tillgängliga på grund av cyberhot-säkerhetsattack, eller för att återställa tjänster efter en kompromiss, kan Traffic Manager användas för att omdirigera trafik till att fungera, tillgängliga program instanser.

|Resource|Länka|
|---|---|
|Översikt över Traffic Manager | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|Haveri beredskap med hjälp av Azure DNS och Traffic Managers guide | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute kan användas för att upprätta en privat sökväg från en lokal miljö till system som finns i Azure. Den här anslutningen kan ge bättre tillförlitlighet och garanterad prestanda med förbättrad sekretess för nätverks kommunikation. Med Express Route kan samordnings enheter styra inkommande trafik från den lokala miljön och definiera dedikerade adresser som är specifikt för den organisation som ska användas för inkommande brand Väggs regler och åtkomst kontrol listor.

|Resource | Länka|
|---|---|
|Översikt över ExpressRoute | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|ExpressRoute anslutnings modeller | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>ExpressRoute privat peering

Privat peering är en mekanism för att utöka en lokal miljö till Azure med endast privata IP-adresser. Detta gör att samväldet-enheter kan integrera virtuella Azure-nätverk och adress intervall med befintliga lokala system och tjänster. Privat peering garanterar att kommunikation över ExpressRoute endast är till virtuella nätverk som godkänts av organisationen. Om du använder privat peering måste samväldet entiteter implementera virtuella nätverks installationer (NVA) i stället för Azure VPN Gateway för att upprätta säker VPN-kommunikation till dina lokala nätverk enligt vad som krävs av ACSC-konsument vägledningen.

|Resource | Länka|
|---|---|
|Översikt över ExpressRoute privat peering | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute privat peering instruktions guide | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>ExpressRoute Microsoft-peering

Microsoft-peering tillhandahåller en snabb och låg latens anslutning till Microsofts offentliga tjänster utan att behöva gå över på Internet. Detta ger bättre tillförlitlighet, prestanda och sekretess för anslutningar. Genom att använda väg filter kan samväldet entiteter begränsa kommunikationen till de Azure-regioner som de behöver, men det inkluderar tjänster som är värdbaserade av andra organisationer och kan kräva ytterligare filtrerings-eller inspektions möjligheter mellan lokal miljö och Microsoft.

Samväldet entiteter kan använda dedikerade offentliga IP-adresser som upprättats via peering-relationen för att unikt identifiera den lokala miljön för användning i brand väggar och åtkomst kontrol listor i PaaS-funktioner.

Som ett alternativ kan du använda ExpressRoute Microsoft peering som ett Underlay-nätverk för att upprätta en VPN-anslutning via Azure VPN Gateway. I den här modellen finns det ingen aktiv kommunikation från det interna lokala nätverket till Azures offentliga tjänster över ExpressRoute, men säker anslutning till privata virtuella nätverk uppnås i enlighet med ACSC-konsument vägledningen.

|Resource | Länka|
|---|---|
|Översikt över ExpressRoute Microsoft-peering | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute Microsoft peering instruktions guide | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>IaaS ingress

Det här avsnittet innehåller komponent vägledning för att styra ingångs trafik till IaaS-komponenter. IaaS innehåller Virtual Machines och andra beräknings resurser som kan distribueras och hanteras i ett Virtual Network i Azure. För att trafik ska komma till system som distribueras med IaaS måste den ha en start punkt till Virtual Network, som kan upprättas via en offentlig IP-adress, Virtual Network gateway eller Virtual Network peering-relation.

### <a name="network-interface"></a>Nätverksgränssnitt

Nätverks gränssnitt är de ingående punkterna för all trafik till en virtuell dator. Nätverks gränssnitt möjliggör konfiguration av IP-adressering och kan användas för att tillämpa NSG: er eller för att dirigera trafik via en virtuell nätverks installation. Nätverks gränssnitten för Virtual Machines bör planeras och konfigureras på lämpligt sätt för att anpassas efter övergripande nätverks segment och ansvars mål.

|Resource | Länka|
|---|---|
|Skapa, ändra eller ta bort ett nätverks gränssnitt | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|IP-adressering för nätverks gränssnitt | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

Undernät är en viktig komponent för nätverks segmentering och uppdelning i Azure. Undernät kan användas på samma sätt för att ge åtskillnad mellan system. NSG: er kan tillämpas på undernät för att begränsa ingångs överförings flöden till enbart de som behövs för systemfunktionen. Undernät kan användas som både käll-och mål adresser för brand Väggs regler och åtkomst kontrol listor och kan konfigureras för tjänst slut punkter för att tillhandahålla anslutning till PaaS-funktioner.

|Resource | Länka|
|---|---|
|Lägga till, ändra eller ta bort ett virtuellt nätverks under nät | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>Virtual Network (VNet)

Virtuella nätverk är ett av de grundläggande Bygg stenarna för nätverk i Azure. Virtuella nätverk definierar ett IP-adressutrymme och en avgränsning av vägar som ska användas i olika system. Virtuella nätverk är indelade i undernät och alla undernät inom en Virtual Network har en direkt nätverks väg till varandra. Genom att använda Virtual Network gatewayer (ExpressRoute eller VPN) kan system i en Virtual Network göras tillgängliga för lokala och externa miljöer. Att förstå virtuella nätverk och tillhör ande konfigurations parametrar och routning är avgörande för att förstå och kontrol lera inkommande nätverks trafik.

|Resource | Länka|
|---|---|
|Översikt över virtuella nätverk | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|Planera Virtual Networks instruktions guide | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
Skapa en snabb start för Virtual Network | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>VNet-peering

VNet-peering används för att tillhandahålla en direkt kommunikations väg mellan två virtuella nätverk. När peering har upprättats har värdar i en Virtual Network en snabb väg för routning direkt till värdar i en annan Virtual Network. NSG: er gäller fortfarande för trafiken som normala och avancerade konfigurations parametrar kan användas för att definiera om kommunikationen mellan Virtual Network gatewayer eller andra externa system tillåts.

|Resource | Länka|
|---|---|
|Översikt över Virtual Network peering |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|Skapa, ändra eller ta bort en virtuell nätverks-peering | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>Offentlig IP i VNET

Offentliga IP-adresser används för att tillhandahålla en inkommande kommunikations väg till tjänster som distribueras i en Virtual Network. Samväldet samväldet entiteter bör planera tilldelningen av offentliga IP-adresser noggrant och bara tilldela dem till resurser där det finns ett äkta krav. Som en allmän utformning bör offentliga IP-adresser tilldelas till resurser med inbyggda säkerhetsfunktioner som Application Gateway eller virtuella nätverks installationer för att tillhandahålla en säker, kontrollerad offentlig start punkt till en Virtual Network.

|Resource | Länka|
|---|---|
|Översikt över offentliga IP-adresser | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|Skapa, ändra eller ta bort en offentlig IP-adress | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>ExpressRoute-Gateway

ExpressRoute-gatewayer ger en ingångs punkt från den lokala miljön och bör distribueras för att uppfylla säkerhets-, tillgänglighets-, finans-och prestanda krav. ExpressRoute-gatewayer ger en definierad nätverks bandbredd och skapar användnings kostnader efter distributionen. Virtuella nätverk kan bara ha en ExpressRoute-Gateway, men den kan anslutas till flera ExpressRoute-kretsar och kan utnyttjas av flera virtuella nätverk via VNet-peering, vilket gör att flera virtuella nätverk kan dela bandbredd och anslutning. Var försiktig när du konfigurerar routning mellan lokala miljöer och virtuella nätverk med hjälp av ExpressRoute-gatewayer för att säkerställa anslutning från slut punkt till slut punkt med kända, kontrollerade nätverks ingresss punkter. Samväldet samväldet entiteter som använder ExpressRoute Gateway måste också distribuera virtuella nätverks enheter för att upprätta en VPN-anslutning till den lokala miljön för att uppfylla ACSC-konsument vägledningen.

|Resource | Länka|
|---|---|
|Översikt över ExpressRoute Gateway | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|Konfigurera en virtuell nätverksgateway för ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway ger en ingångs punkt på en nätverks plats från ett externt nätverk för att skydda plats-till-plats-eller punkt-till-plats-anslutningar. VPN-gatewayer ger en definierad nätverks bandbredd och skapar användnings kostnader efter distributionen. Samväldet samväldet entiteter som använder VPN Gateway bör se till att de konfigureras i enlighet med ACSC-konsument vägledningen. Virtuella nätverk kan bara ha en VPN Gateway, men detta kan konfigureras med flera tunnlar och kan utnyttjas av flera virtuella nätverk via VNet-peering, vilket gör att flera virtuella nätverk kan dela bandbredd och anslutning. VPN-gatewayer kan upprättas via Internet eller via ExpressRoute via Microsoft-peering.

|Resource | Länka|
|---|---|
|Översikt över VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|Planering och design för VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|VPN Gateway konfiguration för myndigheter i Australien|[IPSEC-konfiguration krävs för myndigheter i Australien](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>PaaS VNet-integrering

Användningen av PaaS kan ge förbättrade funktioner och tillgänglighet och minska hanterings kostnaderna men måste skyddas på lämpligt sätt. För att öka kontrollen, tvinga nätverks segmentering eller tillhandahålla en säker ingångs punkt för program och tjänster, kan många PaaS-funktioner integreras med en Virtual Network.

För att tillhandahålla en säker start punkt kan PaaS-funktioner som Application Gateway konfigureras med ett externt, offentligt gränssnitt och ett internt, Privat gränssnitt för kommunikation med program tjänster. Detta förhindrar att du behöver konfigurera program servrar med offentliga IP-adresser och exponera dem för externa nätverk.

Om du vill använda PaaS som en integrerad del av system-eller program arkitekturen tillhandahåller Microsoft flera mekanismer för att distribuera PaaS till en Virtual Network. Distributions metoden begränsar den inkommande åtkomsten från externa nätverk, till exempel Internet, samtidigt som du får anslutning och integrering med interna system och program. Exempel på detta är App Service miljöer, SQL-hanterade instanser med mera.

|Resource | Länka|
|---|---|
|Integrering av virtuella nätverk för Azure-tjänster | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|Integrera din app med en Azure Virtual Network instruktions guide | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>PaaS ingress

PaaS-funktioner ger möjligheter till ökad kapacitet och förenklad hantering, men introducerar komplicerade kunskaper om adresserings krav för nätverks segmentering och uppdelning. PaaS-funktioner konfigureras vanligt vis med offentliga IP-adresser och är tillgängliga från Internet.  När du skapar system med PaaS-funktioner bör du tänka på att identifiera alla nödvändiga kommunikations flöden mellan komponenter i systemet och nätverks säkerhets regler som har skapats för att endast tillåta denna kommunikation. Som en del av en skydds-och djupgående metod för säkerhet ska PaaS-funktioner konfigureras med kryptering, autentisering och lämpliga åtkomst kontroller och behörigheter.  

### <a name="hostname"></a>Värddatornamn

PaaS-funktioner identifieras unikt via värdnamn så att flera instanser av samma tjänst kan finnas på samma offentliga IP-adress. Unika värdnamn anges när resurser skapas och finns i Microsofts ägda DNS-domäner. Specifika värdnamn för auktoriserade tjänster kan användas i säkerhets verktyg med funktioner på program nivå filtrering. Vissa tjänster kan också konfigureras med anpassade domäner om det behövs.

|Resource | Länka|
|---|---|
|Många offentliga namn områden som används av Azure-tjänster kan hämtas via PowerShell genom att köra kommandot Get-AzureRMEnvironment | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|Konfigurera ett anpassat domän namn för en Azure-moln tjänst | App Services och andra kan ha anpassade domäner[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>Offentlig IP för PaaS

Offentliga IP-adresser för PaaS-funktioner allokeras baserat på den region där tjänsten är värdbaserad eller distribuerad. Om du vill bygga lämpliga nätverks säkerhets regler och topologi för nätverks segmentering och uppdelning som täcker virtuella Azure-nätverk, PaaS och ExpressRoute och Internet anslutning, är det en förståelse för offentlig IP-adressallokering och regioner som är kunna. Azure allokerar IP-adresser från en pool som allokerats till varje Azure-region. Microsoft gör de adresser som används i varje region som är tillgängliga för hämtning, vilket uppdateras i ett regelbundet och kontrollerat sätt. Tjänsterna som är tillgängliga i varje region kan också ofta ändras när nya tjänster släpps eller tjänster distribueras mer sällan. Samväldet-entiteter bör granska detta material regelbundet och kan utnyttja automatisering för att underhålla system efter behov. Vissa IP-adresser för vissa tjänster som finns i varje region kan erhållas genom att kontakta Microsoft support.

|Resource | Länka|
|---|---|
|Microsoft Azure Data Center IP-intervall | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Azure-tjänster per region | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Australien – centrala, Australien-centrala-2, Australien, östra, Australien, sydöstra & Products = all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Tjänstens slutpunkter

Virtual Network tjänstens slut punkter ger en snabb och privat inkommande nätverks anslutning för undernät i en Virtual Network för att använda vissa PaaS-funktioner. För fullständig nätverks segmentering och uppdelning av PaaS-funktionen måste PaaS-funktionen konfigureras för att endast godkänna anslutningar från de virtuella nätverk som behövs. Det är inte alla PaaS-funktioner som har stöd för en kombination av brand Väggs regler som innehåller tjänst slut punkter och traditionella IP-adressbaserade regler, så det bör vara viktigt att förstå det flöde av kommunikation som krävs för programfunktioner och administration så att implementeringen av dessa säkerhets kontroller inte påverkar tjänstens tillgänglighet.

|Resource | Länka|
|---|---|
|Översikt över tjänst slut punkter | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|Självstudie |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>Säkerhet

Att implementera nätverks segmenterings-och särskiljation-kontroller på IaaS-och PaaS-funktionerna uppnås genom att skydda funktionerna och genom att implementera kontrollerade kommunikations vägar från de system som ska kommunicera med funktionen.

Att utforma och skapa lösningar i Azure är en process för att skapa en logisk arkitektur för att förstå, kontrol lera och övervaka nätverks resurser i hela Azure-närvaron. Den här logiska arkitekturen är program vara som definierats inom Azure-plattformen och som är platsen för en fysisk nätverkstopologi som implementeras i traditionella nätverks miljöer.

Den logiska arkitektur som skapas måste ge de funktioner som behövs för användbarhet, men de måste också ge den synlighet och kontroll som krävs för säkerhet och integritet.

Att uppnå detta resultat baseras på implementering av nödvändiga nätverks segment och anslags verktyg, men även för att skydda och tvinga nätverks sto pol Ogin och implementeringen av dessa verktyg.

Informationen i den här guiden kan användas för att identifiera källorna för ingångs trafik som måste tillåtas och hur trafiken kan styras ytterligare eller begränsas.

### <a name="network-security-groups-nsgs"></a>Nätverks säkerhets grupper (NSG: er)

NSG: er används för att ange inkommande och utgående trafik som tillåts för ett undernät eller ett särskilt nätverks gränssnitt. När du konfigurerar NSG: er bör samväldet entiteter använda en vit listning metod där regler har kon figurer ATS för att tillåta nödvändig trafik med en standard regel som kon figurer ATS för att neka all trafik som inte matchar en speciell Permit-instruktion. Försiktighet måste vidtas när du planerar och konfigurerar NSG: er för att säkerställa att all nödvändig inkommande och utgående trafik samlas in på rätt sätt. Detta omfattar att identifiera och förstå alla privata IP-adressintervall som används i virtuella Azure-nätverk och i den lokala miljön, samt särskilda Microsoft-tjänster som Azure Load Balancer-och PaaS hanterings krav. Personer som är involverade i utformningen och implementeringen av nätverks säkerhets grupper bör också förstå användningen av service märken och program säkerhets grupper för att skapa detaljerade, service-och programspecifika säkerhets regler.

|Resource | Länka|
|---|---|
|Översikt över nätverks säkerhet | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|Skapa, ändra eller ta bort en nätverks säkerhets grupp | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>PaaS-brandvägg

En PaaS-brandvägg är en funktion för nätverks åtkomst kontroll som kan tillämpas på vissa PaaS-tjänster. Det tillåter att IP-adressfiltrering eller filtrering från vissa virtuella nätverk konfigureras för att begränsa inkommande trafik till den angivna PaaS-instansen. För PaaS-funktioner som innehåller en brand vägg bör principerna för nätverks åtkomst kontroll konfigureras för att endast tillåta den nödvändiga ingångs trafiken baserat på program krav.  

|Resource | Länka|
|---|---|
|Regler för Azure SQL Database och SQL Data Warehouse IP-brandvägg | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|Lagrings nätverks säkerhet | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS-autentisering och åtkomst kontroll

Beroende på PaaS-kapacitet och dess syfte kan det vara omöjligt eller praktiskt att använda nätverks kontroller för att begränsa åtkomsten. Som en del av säkerhets modellen med skikt för PaaS tillhandahåller Azure en mängd olika mekanismer för autentisering och åtkomst kontroll för att begränsa åtkomsten till en tjänst, även om nätverks trafiken tillåts. Typiska autentiseringsmekanismer för PaaS-funktioner omfattar Azure Active Directory, autentisering på program nivå och delade nycklar eller åtkomst-signaturer. När en användare identifieras på ett säkert sätt kan roller användas för att styra vilka åtgärder användaren kan utföra. Dessa verktyg kan användas som ett alternativ eller som ett kostnads fritt mått för att begränsa åtkomsten till tjänster.

|Resource | Länka|
|---|---|
|Styra och bevilja databas åtkomst till SQL Database och SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Auktorisering för Azure Storage Services | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy är en viktig komponent för att genomdriva och upprätthålla integriteten för Azure-miljöns logiska arkitektur. Med tanke på de olika tjänsterna och inkommande nätverks trafik Sök vägar som är tillgängliga via Azure-tjänster är det viktigt att samväldet entiteter är medvetna om de resurser som finns i deras miljö och de tillgängliga ingångs punkterna i nätverket. För att säkerställa att obehöriga nätverks inträngande punkter inte skapas i Azure-miljön, bör samväldet entiteter använda Azure Policy för att kontrol lera vilka typer av resurser som kan distribueras och konfigurationen av dessa resurser. Praktiska exempel är att begränsa resurser till endast de som godkänts och godkänts för användning, tvinga HTTPS-kryptering på lagring och kräva att NSG: er läggs till i undernät.

|Resource | Länka|
|---|---|
|Översikt över Azure Policy | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|Exempel princip för tillåtna resurs typer | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|Se till att exempel principen för HTTPS Storage-konton|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|Tvinga NSG i en exempel princip för undernät| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [utgående trafik hantering och kontroll av Gateway](gateway-egress-traffic.md) för information om hur du hanterar trafikflöden från din Azure-miljö till andra nätverk med hjälp av dina gateway-komponenter i Azure.
