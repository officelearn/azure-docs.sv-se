---
title: Metodtips för nätverkssäkerhet – Microsoft Azure
description: Den här artikeln innehåller en uppsättning Metodtips för säkerhet med inbyggda funktioner i Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/05/2019
ms.author: TomSh
ms.openlocfilehash: 78402d3e388f08eae6652859a71c93ff408a5b0d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152978"
---
# <a name="azure-best-practices-for-network-security"></a>Bästa praxis för nätverkssäkerhet för Azure
Den här artikeln beskriver en uppsättning Metodtips för Azure att förbättra nätverkssäkerheten. Dessa metodtips härleds från vår erfarenhet av Azure-nätverk och erfarenheter från kunder som dig själv.

Den här artikeln förklarar för varje skull:

* Vad den bästa metoden är
* Varför du vill aktivera den bästa praxis
* Vad kan vara resultatet om du inte aktivera den bästa metoden
* Möjliga alternativ till den bästa praxis
* Hur du kan lära dig att aktivera ett metodtips

Dessa metodtips baseras på en konsensus-åsikter och funktioner för Azure-plattformen och funktioner, som de finns på den tid som den här artikeln skrevs. Andras åsikter och tekniker som ändras med tiden och den här artikeln kommer att uppdateras regelbundet att återspegla dessa ändringar.

## <a name="use-strong-network-controls"></a>Använd starka nätverkskontroller
Du kan ansluta [virtuella Azure-datorer (VM)](https://azure.microsoft.com/services/virtual-machines/) och till andra nätverksenheter genom att placera dem på [virtuella Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/). Det vill säga kan du ansluta virtuella nätverkskort till ett virtuellt nätverk till att tillåta TCP/IP-baserad kommunikation mellan nätverk-aktiverade enheter. Virtuella datorer är anslutna till en Azure-nätverk kan ansluta till enheter på samma virtuella nätverk, olika virtuella nätverk, internet eller dina egna lokala nätverk.

När du planerar nätverket och säkerheten i nätverket, rekommenderar vi att du centralisera:

- Hantering av core nätverksfunktioner som ExpressRoute, virtuellt nätverk och undernät etablering och IP-adresser.
- Styrning av nätverkselement för säkerhet, till exempel virtuell installation av nätverksfunktioner som ExpressRoute, virtuellt nätverk och undernät etablering och IP-adresser.

Om du använder en gemensam uppsättning verktyg för att övervaka ditt nätverk och säkerheten för ditt nätverk kan få direkt insyn i både. Ett enkelt, enhetligt säkerhetsstrategi minskar risken för fel eftersom det ökar mänskliga kunskaper och tillförlitligheten för automation.

## <a name="logically-segment-subnets"></a>Logiskt segment undernät
Azure-nätverk liknar LAN i ditt lokala nätverk. Tanken bakom Azure-nätverk är att du skapar ett nätverk, baserat på en enda privat IP-adressutrymme, där du kan placera alla virtuella datorer i Azure. De privata IP-adressutrymmena tillgängliga finns i klass A (10.0.0.0/8), klass B (172.16.0.0/12) och klass C (192.168.0.0/16) intervall.

Metodtips för logiskt segmentera undernät är:

**Bästa praxis**: Tilldela inte tillåter regler med bred intervall (till exempel Tillåt 0.0.0.0 via 255.255.255.255).  
**Information om**: Se till att felsökningsanvisningar hindra eller stänga av hur du konfigurerar dessa typer av regler. Dessa är kan regler leda till en falsk känsla av säkerhet och ofta hittades utnyttjas av red team

**Bästa praxis**: Segmentera större adressutrymmet i undernät.   
**Information om**: Använd [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-baserade undernät principer för att skapa dina undernät.

**Bästa praxis**: Skapa åtkomstkontroll för nätverk mellan undernät. Routning mellan undernät sker automatiskt och du behöver inte konfigurera routningstabeller manuellt. Som standard finns inga åtkomstkontroller för nätverk mellan undernät som du skapar på Azure-nätverk.   
**Information om**: Använd en [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md) som skydd mot oönskad trafik i Azure-undernät. Nätverkssäkerhetsgrupper är enkel, tillståndskänsliga paketinspektion enheter som använder metoden 5-tuppel (käll-IP, källport, mål-IP, målport och protocol layer 4) skapa tillåta/neka regler för nätverkstrafik. Du tillåter eller nekar trafik till och från en enskild IP-adress, till och från flera IP-adresser, eller till och från hela undernät.

När du använder nätverkssäkerhetsgrupper för network access control mellan undernät, kan du placera resurser som tillhör samma säkerhetszon eller roll i sina egna undernät.

**Bästa praxis**: Undvik att små virtuella nätverk och undernät så enkelt och flexibelt.   
**Information om**: De flesta organisationer lägga till fler resurser än inledningsvis planerat och omfördelning av adresser är arbete beräkningsintensiva. Använda små undernät mervärde för begränsad säkerhet och mappa en nätverkssäkerhetsgrupp till varje undernät lägger till overhead. Definiera undernät brett för att säkerställa att du har möjlighet att växa.

**Bästa praxis**: Förenkla nätverk säkerhetshantering grupp regeln genom att definiera [Programsäkerhetsgrupper](https://docs.microsoft.com/rest/api/virtualnetwork/applicationsecuritygroups).  
**Information om**: Definiera en Programsäkerhetsgrupp för en lista över IP-adresser som du tror kan ändras i framtiden eller användas i många nätverkssäkerhetsgrupper. Var noga med att namnet programsäkerhet grupper tydligt så andra kan förstå deras innehåll och syftet.

## <a name="adopt-a-zero-trust-approach"></a>Anta en lita på noll-metod
Perimeter-baserade nätverk fungerar på antagandet att alla system i ett nätverk är betrodd. Men dagens medarbetare åtkomst till organisationens resurser från valfri plats på en mängd olika enheter och appar, vilket gör perimeter säkerhetskontroller irrelevanta. Principer för åtkomstkontroll som fokuserar endast på vem som kan komma åt en resurs är inte tillräckligt. Om du vill balans mellan säkerhet och produktivitet, säkerhetsadministratörer måste också ta hänsyn till *hur* en resurs används.

Nätverk som behöver utveckla från traditionella försvar eftersom nätverk kan vara sårbara för överträdelser: en angripare kan påverka en enda slutpunkt inom betrodda gräns och snabbt utöka en fot i hela nätverket. [Noll förtroende](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) nätverk eliminera begreppet förtroende baserat på nätverksplats i ett perimeternätverk. I stället använda noll förtroende arkitekturer enhets- och förtroende-anspråk som förhindrar åtkomst till organisationens data och resurser. Anta noll förtroende metoder som validerar förtroende vid tidpunkten för åtkomstkontroll för nya initiativ.

Bästa praxis är:

**Bästa praxis**: Ge villkorlig åtkomst till resurser baserat på enhet, identitet, assurance, nätverksplats och mycket mer.  
**Information om**: [Azure AD villkorsstyrd åtkomst](../active-directory/conditional-access/overview.md) kan du tillämpa rätt åtkomstkontroller genom att implementera automatiserade besluten om åtkomstkontroll baserat på de nödvändiga villkor. Mer information finns i [hantera åtkomst till Azure-hantering med villkorlig åtkomst](../role-based-access-control/conditional-access-azure-management.md).

**Bästa praxis**: Aktivera portåtkomst förrän arbetsflöde för godkännande.  
**Information om**: Du kan använda [åtkomst till Virtuella just-in-time i Azure Security Center](../security-center/security-center-just-in-time.md) för att låsa inkommande trafik till dina virtuella Azure-datorer minskar exponeringen för attacker samtidigt som det ger enkel åtkomst till att ansluta till virtuella datorer när det behövs.

**Bästa praxis**: Bevilja tillfällig behörigheter att utföra Privilegierade åtgärder, vilket förhindrar skadliga eller obehöriga användare från att få åtkomst när behörigheterna som har upphört att gälla. Åtkomst beviljas endast när användare behöver den.  
**Information om**: Använd just-in-time-åtkomst i Azure AD Privileged Identity Management eller i en lösning från tredje part för att bevilja behörighet att utföra Privilegierade uppgifter.

Noll förtroende är en vidareutveckling i nätverkssäkerhet. Tillståndet för cyberattacker Driver organisationer att dra tänkesätt ”förutsätta intrång”, men den här metoden får inte vara begränsande. Noll förtroende-nätverk kan du skydda företagets data och resurser samtidigt som man säkerställer att organisationer kan skapa en modern arbetsplats med hjälp av tekniker som gör det möjligt för anställda att vara produktiva när som helst, var som helst, på något sätt.

## <a name="control-routing-behavior"></a>Kontrollera funktionssättet för Routning
När du placerar en virtuell dator på Azure-nätverk måste kan den virtuella datorn ansluta till andra virtuella datorer på samma virtuella nätverk, även om andra virtuella datorer finns i olika undernät. Detta är möjligt eftersom en uppsättning systemvägar aktiverad som standard tillåter den här typen av kommunikation. Dessa standardvägar att virtuella datorer på samma virtuella nätverk initierar anslutningar med varandra och med internet (för utgående kommunikation till endast internet).

Standardvägar som system är användbara för flera olika distributionsscenarier, finns men det tillfällen när du vill anpassa routningskonfiguration för dina distributioner. Du kan konfigurera adress för nästa hopp för att nå specifika mål.

Vi rekommenderar att du konfigurerar [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) när du distribuerar en säkerhetsapparat för ett virtuellt nätverk. Vi pratar om detta i ett senare avsnitt som heter [skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Användardefinierade vägar är inte obligatoriska och standardvägar som systemet fungerar normalt.
>
>

## <a name="use-virtual-network-appliances"></a>Använda virtuella nätverksinstallationer
Nätverkssäkerhetsgrupper och användardefinierad routning kan ge en viss grad av nätverkssäkerhet på Nätverks- och plattformsnivå av den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model). Men i vissa situationer kan du vilja eller behöva aktivera säkerhet på hög nivå av stacken. I så fall rekommenderar vi att du distribuerar virtuella nätverket säkerhetsenheter som tillhandahålls av Azure-partner.

Azure funktioner för nätverkssäkerhet kan leverera bättre säkerhet än vad på nätverksnivå kontroller som ger. Nätverk säkerhetsfunktioner för virtuella funktioner för nätverkssäkerhet:


* Brandväggsfunktioner
* Identifiering/intrång intrångsskydd
* Hantering av sårbarhet
* Programkontroll
* Nätverksbaserade avvikelseidentifiering
* Webbfiltrering
* Antivirus
* Botnet-skydd

För att hitta tillgängliga Azure-nätverk säkerhetsenheter, går du till den [Azure Marketplace](https://azure.microsoft.com/marketplace/) och Sök efter ”säkerhet” och ”nätverkssäkerhet”.

## <a name="deploy-perimeter-networks-for-security-zones"></a>Distribuera perimeternätverk för säkerhetszoner
En [perimeternätverk](https://docs.microsoft.com/azure/best-practices-network-security) (även kallat DMZ) är en fysisk eller logisk nätverkssegment som ger ett extra lager av säkerhet mellan dina tillgångar och internet. Särskild åtkomstkontroll nätverksenheter i utkanten av ett perimeternätverk tillåta endast önskad trafik till ditt virtuella nätverk.

Perimeternätverk är användbara eftersom du kan fokusera dina nätverkshantering av åtkomstkontroll, övervakning, loggning och rapportering på enheter i utkanten av Azure-nätverk. Ett perimeternätverk är vanligtvis där du aktivera distribuerade denial of service (DDoS) dataförlustskydd, intrång identifiering/intrång förebyggande system (IDS/IPS), brandväggsregler och principer, webbfiltrering, network program mot skadlig kod och mer. Nätverkssäkerhetsenheter mellan internet och Azure-nätverk och har ett användargränssnitt på båda nätverken.

Det här är den grundläggande utformningen av ett perimeternätverk, men det finns många olika konstruktionerna som sekvens efter varandra, tre-homed och flera IP-adresser.

Baserat på noll förtroende-konceptet som nämnts tidigare, rekommenderar vi att du överväga att använda ett perimeternätverk för alla distributioner med hög säkerhet till nivån på nätverket säkerhet och åtkomstkontroll för dina Azure-resurser. Du kan använda Azure eller en lösning från tredje part för att tillhandahålla ett ytterligare säkerhetslager mellan dina tillgångar och internet:

- Azure interna kontroller. [Azure-brandväggen](../firewall/overview.md) och [Brandvägg för webbaserade program i Application Gateway](../application-gateway/overview.md#web-application-firewall) erbjuder grundläggande säkerhet med en fullständigt administrerad brandvägg som en tjänst, inbyggd hög tillgänglighet, obegränsade molnskalbarhet FQDN filtrering , stöd för OWASP core rule sets och enkel installation och konfiguration.
- Erbjudanden från tredje part. Sök efter den [Azure Marketplace](https://azuremarketplace.microsoft.com/) för nästa generations brandvägg (NGFW) och erbjudanden från tredje part som tillhandahåller välbekanta säkerhetsverktyg och avsevärt förbättrat nivåer av nätverkssäkerhet. Konfigurationen kan vara mer komplexa, men ett erbjudande från tredje part kan tillåta dig att använda befintliga funktioner och kompetens.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Undvika exponering på Internet med fast WAN-länkar
Många organisationer har valt hybrid IT-vägen. Med hybrid-IT, några av företagets informationstillgångar är i Azure och andra lokala. I många fall kan körs vissa delar av en tjänst i Azure medan andra komponenter är på plats.

I en hybrid IT-scenariot finns vanligtvis någon typ av anslutning mellan olika platser. Anslutning mellan olika platser gör det möjligt för företag att ansluta sina lokala nätverk till Azure-nätverk. Det finns två olika platser anslutningslösningar:

* [Plats-till-plats VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Det är en betrodda och tillförlitliga etablerad teknik, men anslutningen sker över internet. Bandbredd är begränsad till högst 200 Mbit/s. Plats-till-plats-VPN är ett önskvärt alternativ i vissa situationer.
* **Azure ExpressRoute**. Vi rekommenderar att du använder [ExpressRoute](../expressroute/expressroute-introduction.md) för dina korsanslutningar. Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning som tillhandahålls av en anslutningsprovider. Med ExpressRoute kan upprätta du anslutningar till Microsofts molntjänster som Azure, Office 365 och Dynamics 365. ExpressRoute är en dedikerad WAN länken mellan din lokala plats eller en värdleverantör för Microsoft Exchange. Eftersom detta är en telco-anslutning kan överföras inte dina data via internet, så det inte är visas de potentiella risker för internet-kommunikation.

Platsen för ExpressRoute-anslutningen kan påverka brandväggen kapacitet, skalbarhet, tillförlitlighet och nätverket trafik synlighet. Du behöver identifiera var du vill avsluta ExpressRoute i nätverk (lokalt). Du kan:

- Avsluta utanför brandväggen (perimeternätverket nätverket paradigmet) om du ha insyn i trafiken, om du vill fortsätta en befintlig praxis och isolera datacenter, eller om det är enbart frågan extranätresurser på Azure.
- Avsluta innanför brandväggen (network tillägget paradigmet). Det här är standardinställningarna. I alla andra fall rekommenderar vi behandla Azure som en n: te datacenter.

## <a name="optimize-uptime-and-performance"></a>Optimera drifttid och prestanda
Om en tjänst är nere kan kan information inte nås. Om prestanda är dålig så att data är oanvändbara du överväga att data inte är tillgänglig. Du måste göra vad du kan för att se till att dina tjänster har optimala drifttid och prestanda från ett säkerhetsperspektiv.

En populära och effektiv metod för att förbättra tillgänglighet och prestanda är belastningsutjämning. Belastningsutjämning är en metod för att distribuera nätverkstrafik mellan servrar som ingår i en tjänst. Till exempel om du har en frontend-webbservrar som en del av din tjänst, kan du använda Utjämning av nätverksbelastning för att distribuera trafiken över din flera frontend-webbservrar.

Den här distributionen av trafiken ökar tillgängligheten eftersom om en av webbservrarna blir otillgänglig, belastningsutjämnaren stoppar skickar trafik till den servern och omdirigerar den till de servrar som fortfarande är online. Utjämning av nätverksbelastning hjälper också till prestanda, eftersom arbetet för processor, nätverk och minne för begäranhantering distribueras över alla belastningsutjämnade servrar.

Vi rekommenderar att du använder att Utjämning av nätverksbelastning varje gång som du kan och som passar dina tjänster. Följande är scenarier på både Azure-nätverk-nivå och global nivå, tillsammans med belastningsutjämning alternativ för var och en.

**Scenario**: Du har ett program som:

- Kräver begäranden från samma användare/klientsession för att nå samma virtuella dator för serverdelen. Exempel på detta shopping appar med shoppingvagnar och e-postwebbservrar.
- Accepterar endast en säker anslutning, så okrypterad kommunikation till servern inte är en acceptabel.
- Kräver flera HTTP-förfrågningar på samma tidskrävande TCP-anslutning ska routas eller belastningsutjämnad till olika serverdels servrar.

**Alternativ för belastningsutjämning**: Använd [Azure Application Gateway](../application-gateway/application-gateway-introduction.md), en belastningsutjämnare för HTTP-webb-trafik. Application Gateway stöder slutpunkt till slutpunkt SSL-kryptering och [SSL-avslutning](../application-gateway/application-gateway-introduction.md) på gatewayen. Sedan kan webbservrar webbservrarna från kryptering och avkryptering och trafik som passerar okrypterade till backend-servrarna.

**Scenario**: Du behöver läsa in belastningsutjämna inkommande anslutningar från internet bland dina servrar som finns i ett Azure-nätverk. Scenarier är när du:

- Ha tillståndslösa program som accepterar inkommande begäranden från internet.
- Inte kräver fästsessioner eller SSL-avlastning. Fästsessioner är en metod som används med belastningsutjämning i programmet, för att uppnå server-tillhörighet.

**Alternativ för belastningsutjämning**: Använda Azure portal för att [skapa en extern belastningsutjämnare](../load-balancer/quickstart-create-basic-load-balancer-portal.md) som sprider förfrågningar mellan flera virtuella datorer för att ge högre tillgänglighet.

**Scenario**: Du måste läsa in saldo anslutningar från virtuella datorer som inte finns på internet. I de flesta fall initieras de anslutningar som är godkända för belastningsutjämning av enheter i Azure-nätverk, till exempel SQL Server-instanser eller interna servrar.   
**Alternativ för belastningsutjämning**: Använda Azure portal för att [skapa en intern belastningsutjämnare](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) som sprider förfrågningar mellan flera virtuella datorer för att ge högre tillgänglighet.

**Scenario**: Du behöver globala belastningsutjämning eftersom du:

- Ha en molnlösning som distribueras över flera regioner och kräver den högsta möjliga drifttid (tillgänglighet) möjligt.
- Måste den högsta möjliga drifttid möjligt att se till att tjänsten är tillgänglig även om ett helt datacenter blir otillgänglig.

**Alternativ för belastningsutjämning**: Använda Azure Traffic Manager. Traffic Manager gör det möjligt att läsa in saldo anslutningar till dina tjänster baserat på användarens plats.

Till exempel om användaren gör en begäran till din tjänst från EU, dirigeras anslutningen till dina tjänster som finns i ett datacenter för Europa. Den här delen av Traffic Manager globala läsa in belastningsutjämning bidrar till att förbättra prestanda eftersom ansluter till det närmaste datacentret är snabbare än att ansluta till datacenter som är långt borta.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Inaktivera RDP/SSH-åtkomst till virtuella datorer
Det går att nå Azure-datorer med hjälp av [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) och [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH)-protokollet. Dessa protokoll Aktivera hantering av virtuella datorer från fjärranslutna platser och är standard i datacenter databehandling.

Potentiella säkerhetsproblem med hjälp av dessa protokoll via internet är att angripare kan använda [råstyrkeattacker](https://en.wikipedia.org/wiki/Brute-force_attack) tekniker för att få åtkomst till Azure-datorer. När angriparen har fått tillgång kan de använda din virtuella dator som en startpunkt för att kompromissa med andra datorer i ditt virtuella nätverk eller även angrepp nätverksanslutna enheter utanför Azure.

Vi rekommenderar att du inaktiverar direkt RDP och SSH-åtkomst till din Azure-datorer från internet. När direkt RDP och SSH-åtkomst från internet har inaktiverats kan ha du andra alternativ som du kan använda för att få åtkomst till dessa virtuella datorer för fjärrhantering.

**Scenario**: Aktivera en enskild användare kan ansluta till ett Azure-nätverk via internet.   
**Alternativet**: [Punkt-till-plats VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) är en annan term för en VPN-klient/server-anslutning för fjärråtkomst. När punkt-till-plats-anslutning har upprättats kan använda användaren RDP eller SSH för att ansluta till virtuella datorer som finns på Azure-nätverket som användaren är anslutna via punkt-till-plats-VPN. Detta förutsätter att användaren har behörighet att nå de virtuella datorer.

Punkt-till-plats-VPN är säkrare än direkt RDP eller SSH-anslutningar eftersom användaren måste autentisera två gånger innan du ansluter till en virtuell dator. Först måste användaren behöver för att autentisera (och auktoriseras) att upprätta en punkt-till-plats VPN-anslutning. Dessutom användaren behöver för att autentisera (och auktoriseras) att upprätta RDP eller SSH-session.

**Scenario**: Användarna i ditt lokala nätverk kan ansluta till virtuella datorer på Azure-nätverk.   
**Alternativet**: En [plats-till-plats VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) ansluter hela nätverket till ett annat nätverk via internet. Du kan använda en plats-till-plats-VPN för att ansluta ditt lokala nätverk till ett Azure-nätverk. Användare i ditt lokala nätverk ansluta med hjälp av RDP eller SSH-protokollet via plats-till-plats VPN-anslutning. Du behöver för att direkt RDP eller SSH-åtkomst via internet.

**Scenario**: Använda en dedikerad WAN-länken för att ge liknande funktioner som plats-till-plats-VPN.   
**Alternativet**: Använd [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Den innehåller liknande funktioner som plats-till-plats-VPN. De viktigaste skillnaderna är:

- Dedikerad WAN-länken via inte internet.
- Dedikerad WAN-länkar är vanligtvis mer stabil och bättre prestanda.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk
Använda tjänstslutpunkter i virtuella nätverk för att utöka ditt privata adressutrymme för virtuellt nätverk och identiteten för ditt virtuella nätverk till Azure-tjänsterna, via en direktanslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt virtuella nätverk till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket.

Tjänstslutpunkter har följande fördelar:

- **Förbättrad säkerhet för dina Azure-tjänstresurser**: Med tjänstslutpunkter kan Azure-tjänstresurser skyddas på ditt virtuella nätverk. Skydda tjänstresurser i ett virtuellt nätverk ger förbättrad säkerhet genom att helt ta bort den offentliga Internetåtkomsten till resurser, så att endast trafik från ditt virtuella nätverk.
- **Optimal routning för Azure-tjänsttrafik från ditt virtuella nätverk**: Alla vägar i ditt virtuella nätverk som tvingar Internettrafik till dina lokala och/eller virtuella enheter, kallas Tvingad tunneltrafik, kan också tvinga Azure-tjänsttrafiken att ta samma väg som Internettrafiken. Med tjänstslutpunkter får du optimal routning för Azure-trafiken.

  Slutpunkter tar alltid tjänsttrafiken direkt från ditt virtuella nätverk till tjänsten i Azure-stamnätverket. Om du behåller trafiken i Azure-stamnätverket kan du fortsätta granska och övervaka utgående Internettrafiken från dina virtuella nätverk, via Tvingad tunneltrafik, utan att påverka tjänsttrafiken. Läs mer om [användardefinierade vägar och Tvingad tunneltrafik](../virtual-network/virtual-networks-udr-overview.md).

- **Enkelt att konfigurera med mindre hanteringskostnader**: Du behöver inte längre reserverade, offentliga IP-adresser i ditt virtuella nätverk för att skydda Azure-resurser via en IP-brandvägg. Det behövs inga NAT- eller gatewayenheter för att konfigurera tjänstslutpunkterna. Tjänstslutpunkter konfigureras via ett enkelt klick på ett undernät. Det finns inga ytterligare kostnader för att underhålla slutpunkterna.

Läs mer om tjänstslutpunkter och Azure-tjänster och regioner som tjänstslutpunkter är tillgängliga för i [tjänstslutpunkter i virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-steps"></a>Nästa steg
Se [säkerhet i Azure-metodtips och mönster](security-best-practices-and-patterns.md) för flera beprövade metoder för att använda när du utforma, distribuera och hantera dina molnlösningar med hjälp av Azure.
