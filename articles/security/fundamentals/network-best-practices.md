---
title: Metodtips för nätverkssäkerhet – Microsoft Azure
description: Den här artikeln innehåller en uppsättning metodtips för nätverkssäkerhet med hjälp av inbyggda Azure-funktioner.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 5e155758d19b45d977fcd087bff0ceb85898f8f8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548307"
---
# <a name="azure-best-practices-for-network-security"></a>Metodtips för Azure för nätverkssäkerhet
I den här artikeln beskrivs en samling metodtips för Azure för att förbättra nätverkssäkerheten. Dessa metodtips härleds från vår erfarenhet av Azure-nätverk och erfarenheter från kunder som du själv.

För varje metod förklarar den här artikeln:

* Vad bästa praxis är
* Varför du vill aktivera den bästa metoden
* Vad kan bli resultatet om du inte aktiverar bästa praxis
* Möjliga alternativ till bästa praxis
* Hur du kan lära dig att göra det bästa sättet att öva

Dessa metodtips baseras på ett konsensusåsikt och Azure-plattformsfunktioner och funktionsuppsättningar, eftersom de finns när den här artikeln skrevs. Åsikter och teknik förändras med tiden och denna artikel kommer att uppdateras regelbundet för att återspegla dessa förändringar.

## <a name="use-strong-network-controls"></a>Använda starka nätverkskontroller
Du kan ansluta [virtuella Azure-datorer (VIRTUELLA datorer)](https://azure.microsoft.com/services/virtual-machines/) och -enheter till andra nätverksanslutna enheter genom att placera dem i [virtuella Azure-nätverk](../../virtual-network/index.yml). Det vill säga kan du ansluta virtuella nätverkskort till ett virtuellt nätverk för att tillåta TCP/IP-baserad kommunikation mellan nätverksaktiverade enheter. Virtuella datorer som är anslutna till ett virtuellt Azure-nätverk kan ansluta till enheter i samma virtuella nätverk, olika virtuella nätverk, internet eller dina egna lokala nätverk.

När du planerar nätverket och säkerheten i nätverket rekommenderar vi att du centraliserar:

- Hantering av kärnnätverksfunktioner som ExpressRoute, etablering av virtuella nätverk och undernät samt IP-adressering.
- Styrning av nätverkssäkerhetselement, till exempel nätverksvirlamenta apparatfunktioner som ExpressRoute, etablering av virtuella nätverk och undernät och IP-adressering.

Om du använder en gemensam uppsättning hanteringsverktyg för att övervaka nätverket och säkerheten i nätverket får du tydlig insyn i båda. En enkel, enhetlig säkerhetsstrategi minskar fel eftersom den ökar människors förståelse och tillförlitligheten i automatiseringen.

## <a name="logically-segment-subnets"></a>Logiskt segmentundernät
Virtuella Azure-nätverk liknar LAN i ditt lokala nätverk. Tanken bakom ett virtuellt Azure-nätverk är att du skapar ett nätverk, baserat på ett enda privat IP-adressutrymme, där du kan placera alla dina virtuella Azure-datorer. De privata IP-adressutrymmena finns i intervallen Klass A (10.0.0.0/8), klass B (172.16.0.0/12) och klass C (192.168.0.0/16).

Metodtips för logiskt segmentering av undernät är:

**Bästa praxis**: Tilldela inte tillåta regler med breda intervall (tillåt till exempel 0.0.0.0 till 255.255.255.255.255).  
**Detalj**: Se till att felsökningsprocedurer avskräcker eller förbjuder att du ställer in dessa typer av regler. Dessa tillåter regler leder till en falsk känsla av säkerhet och hittas ofta och utnyttjas av röda team.

**Bästa praxis**: Segmentera det större adressutrymmet i undernät.   
**Detalj**: Använd [CIDR-baserade](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)undertjetteringsprinciper för att skapa dina undernät.

**Bästa praxis**: Skapa nätverksåtkomstkontroller mellan undernät. Routning mellan undernät sker automatiskt och du behöver inte konfigurera routningstabeller manuellt. Som standard finns det inga nätverksåtkomstkontroller mellan undernäten som du skapar i ett virtuellt Azure-nätverk.   
**Detalj**: Använd en [nätverkssäkerhetsgrupp](/azure/virtual-network/virtual-networks-nsg) för att skydda mot oönskad trafik till Azure-undernät. Nätverkssäkerhetsgrupper är enkla, tillståndskänsliga paketinspektionsenheter som använder 5-tuppelmetoden (käll-IP, källport, mål-IP, målport och layer 4-protokoll) för att skapa tillåt/neka regler för nätverkstrafik. Du tillåter eller nekar trafik till och från en enda IP-adress, till och från flera IP-adresser eller till och från hela undernät.

När du använder nätverkssäkerhetsgrupper för nätverksåtkomstkontroll mellan undernät kan du placera resurser som tillhör samma säkerhetszon eller roll i sina egna undernät.

**Bästa praxis:** Undvik små virtuella nätverk och undernät för att säkerställa enkelhet och flexibilitet.   
**Detalj**: De flesta organisationer lägger till mer resurser än vad som ursprungligen planerades, och omfördelningsadresser är arbetsintensiva. Om du använder små undernät läggs ett begränsat säkerhetsvärde till och en nätverkssäkerhetsgrupp mappas till varje undernät läggs omkostnader till. Definiera undernät brett för att säkerställa att du har flexibilitet för tillväxt.

**Bästa praxis**: Förenkla hantering av nätverkssäkerhetsgrupper genom att definiera [programsäkerhetsgrupper](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Detalj**: Definiera en programsäkerhetsgrupp för listor med IP-adresser som du tror kan ändras i framtiden eller användas i många nätverkssäkerhetsgrupper. Var noga med att namnge Programsäkerhetsgrupper tydligt så att andra kan förstå deras innehåll och syfte.

## <a name="adopt-a-zero-trust-approach"></a>Anta en Zero Trust-strategi
Perimeterbaserade nätverk fungerar utifrån antagandet att alla system i ett nätverk kan lita på. Men dagens anställda får tillgång till organisationens resurser var som helst på en mängd olika enheter och appar, vilket gör perimetersäkerhetskontroller irrelevanta. Principer för åtkomstkontroll som bara fokuserar på vem som kan komma åt en resurs räcker inte. För att kunna styra balansen mellan säkerhet och produktivitet måste säkerhetsadministratörer också ta hänsyn till *hur* en resurs används.

Nätverk måste utvecklas från traditionella försvar eftersom nätverk kan vara sårbara för överträdelser: en angripare kan äventyra en enda slutpunkt inom den betrodda gränsen och sedan snabbt utöka ett fotfäste över hela nätverket. [Zero Trust-nätverk](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminerar begreppet förtroende baserat på nätverksplats inom en perimeter. I stället använder Zero Trust-arkitekturer anspråk på enhet och användare för att utfärda åtkomst till organisationsdata och resurser. För nya initiativ kan du anta Zero Trust-metoder som validerar förtroendet vid tidpunkten för åtkomsten.

Metodtips är:

**Bästa praxis:** Ge villkorlig åtkomst till resurser baserat på enhet, identitet, säkerhet, nätverksplats med mera.  
**Detalj:** [Azure AD Villkorlig åtkomst](/azure/active-directory/conditional-access/overview) kan du tillämpa rätt åtkomstkontroller genom att implementera beslut om automatisk åtkomstkontroll baserat på de nödvändiga villkoren. Mer information finns i [Hantera åtkomst till Azure-hantering med villkorlig åtkomst](../../role-based-access-control/conditional-access-azure-management.md).

**Bästa praxis:** Aktivera portåtkomst först efter godkännande av arbetsflödet.  
**Detalj:** Du kan använda [just-in-time VM-åtkomst i Azure Security Center](../../security-center/security-center-just-in-time.md) för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till virtuella datorer när det behövs.

**Bästa praxis**: Bevilja tillfälliga behörigheter för att utföra privilegierade uppgifter, vilket förhindrar att skadliga eller obehöriga användare får åtkomst efter att behörigheterna har upphört att gälla. Åtkomst beviljas endast när användare behöver den.  
**Detalj:** Använd just-in-time-åtkomst i Azure AD Privileged Identity Management eller i en tredjepartslösning för att bevilja behörigheter för att utföra privilegierade uppgifter.

Zero Trust är nästa utveckling inom nätverkssäkerhet. Tillståndet för cyberattacker driver organisationer att ta "anta brott" tänkesätt, men detta tillvägagångssätt bör inte begränsa. Zero Trust-nätverk skyddar företagets data och resurser samtidigt som organisationer kan bygga en modern arbetsplats med hjälp av teknik som gör det möjligt för anställda att vara produktiva när som helst, var som helst, på något sätt.

## <a name="control-routing-behavior"></a>Kontrollera routningsbeteende
När du placerar en virtuell dator i ett virtuellt Azure-nätverk kan den virtuella datorn ansluta till alla andra virtuella datorer i samma virtuella nätverk, även om de andra virtuella datorerna finns i olika undernät. Detta är möjligt eftersom en samling systemvägar som är aktiverade som standard tillåter den här typen av kommunikation. Dessa standardvägar tillåter virtuella datorer i samma virtuella nätverk att initiera anslutningar med varandra och med Internet (endast för utgående kommunikation till Internet).

Även om standardsystemvägarna är användbara för många distributionsscenarier finns det tillfällen då du vill anpassa routningskonfigurationen för distributionerna. Du kan konfigurera nästa hopp-adress för att nå specifika destinationer.

Vi rekommenderar att du [konfigurerar användardefinierade vägar](../../virtual-network/virtual-networks-udr-overview.md) när du distribuerar en säkerhetsinstallation för ett virtuellt nätverk. Vi talar om detta i ett senare avsnitt med titeln [säkra dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Användardefinierade vägar krävs inte och standardsystemvägarna fungerar vanligtvis.
>
>

## <a name="use-virtual-network-appliances"></a>Använda virtuella nätverksenheter
Nätverkssäkerhetsgrupper och användardefinierad routning kan ge ett visst mått på nätverkssäkerhet vid nätverks- och transportlager i [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model). Men i vissa situationer, du vill eller behöver aktivera säkerhet på höga nivåer i stacken. I sådana situationer rekommenderar vi att du distribuerar virtuella nätverkssäkerhetsenheter som tillhandahålls av Azure-partner.

Azure-nätverkssäkerhetsenheter kan ge bättre säkerhet än vad kontroller på nätverksnivå ger. Nätverkssäkerhetsfunktioner för virtuella nätverkssäkerhetsenheter inkluderar:


* Brandväggar
* Förebyggande av intrång/intrångsskydd
* Hantering av sårbarhet
* Programkontroll
* Identifiering av nätverksbaserad avvikelse
* Webbfiltrering
* Antivirus
* Botnet skydd

Om du vill hitta tillgängliga azure-virtuella nätverkssäkerhetsinstallationer går du till [Azure Marketplace](https://azure.microsoft.com/marketplace/) och söker efter "säkerhet" och "nätverkssäkerhet".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Distribuera perimeternätverk för säkerhetszoner
Ett [perimeternätverk](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (kallas även DMZ) är ett fysiskt eller logiskt nätverkssegment som ger ytterligare ett säkerhetslager mellan dina tillgångar och internet. Specialiserade nätverksåtkomstkontrollenheter i utkanten av ett perimeternätverk tillåter endast önskad trafik till ditt virtuella nätverk.

Perimeternätverk är användbara eftersom du kan fokusera din nätverksåtkomstkontrollhantering, övervakning, loggning och rapportering på enheterna i utkanten av ditt virtuella Azure-nätverk. Ett perimeternätverk är där du vanligtvis aktiverar ddos-förebyggande åtgärder (Distributed Denial of Service), intrångsidentifiering/intrångsskydd (IDS/IPS), brandväggsregler och principer, webbfiltrering, nätverksantimalware med mera. Nätverkssäkerhetsenheterna sitter mellan internet och ditt virtuella Azure-nätverk och har ett gränssnitt i båda nätverken.

Även om detta är den grundläggande utformningen av ett perimeternätverk, det finns många olika mönster, som back-to-back, tri-homed, och multi-homed.

Baserat på zero trust-konceptet som nämndes tidigare rekommenderar vi att du överväger att använda ett perimeternätverk för alla högsäkerhetsdistributioner för att förbättra nivån på nätverkssäkerhet och åtkomstkontroll för dina Azure-resurser. Du kan använda Azure eller en tredjepartslösning för att tillhandahålla ytterligare ett säkerhetslager mellan dina tillgångar och internet:

- Azure-inbyggda kontroller. [Azure Firewall](/azure/firewall/overview) och [brandväggen för webbprogram i Application Gateway](../../application-gateway/features.md#web-application-firewall) erbjuder grundläggande säkerhet med en helt tillståndskänslig brandvägg som en tjänst, inbyggd hög tillgänglighet, obegränsad molnskalbarhet, FQDN-filtrering, stöd för OWASP-kärnregeluppsättningar och enkel installation och konfiguration.
- Erbjudanden från tredje part. Sök på [Azure Marketplace](https://azuremarketplace.microsoft.com/) efter nästa generations brandvägg (NGFW) och andra erbjudanden från tredje part som tillhandahåller välbekanta säkerhetsverktyg och avsevärt förbättrade nivåer av nätverkssäkerhet. Konfigurationen kan vara mer komplex, men ett erbjudande från tredje part kan göra att du kan använda befintliga funktioner och kunskaper.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Undvik exponering för Internet med dedikerade WAN-länkar
Många organisationer har valt hybrid-IT-vägen. Med hybrid-IT finns vissa av företagets informationsresurser i Azure och andra förblir lokala. I många fall körs vissa komponenter i en tjänst i Azure medan andra komponenter finns kvar lokalt.

I ett hybrid-IT-scenario finns det vanligtvis någon typ av anslutning mellan lokala anslutningar. Med anslutning mellan lokala anslutningar kan företaget ansluta sina lokala nätverk till virtuella Azure-nätverk. Det finns två anslutningslösningar över flera platser:

* [Plats-till-plats VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Det är en pålitlig, pålitlig och etablerad teknik, men anslutningen sker via Internet. Bandbredden är begränsad till högst cirka 1,25 Gbit/s. Site-to-site VPN är ett önskvärt alternativ i vissa scenarier.
* **Azure ExpressRoute**. Vi rekommenderar att du använder [ExpressRoute](../../expressroute/expressroute-introduction.md) för din anslutning över lokal anslutning. Med ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning som tillhandahålls av en anslutningsprovider. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster som Azure, Office 365 och Dynamics 365. ExpressRoute är en dedikerad WAN-länk mellan din lokala plats eller en Microsoft Exchange-värdleverantör. Eftersom detta är en telco-anslutning, dina data inte reser över Internet, så det är inte utsatt för de potentiella riskerna med internetkommunikation.

Platsen för din ExpressRoute-anslutning kan påverka brandväggskapacitet, skalbarhet, tillförlitlighet och nätverkstrafiksynlighet. Du måste identifiera var ExpressRoute ska avslutas i befintliga (lokala) nätverk. Du kan:

- Avsluta utanför brandväggen (perimeternätverksparadigmet) om du behöver insyn i trafiken, om du behöver fortsätta med en befintlig praxis att isolera datacenter eller om du bara lägger extranätsresurser på Azure.
- Avsluta inuti brandväggen (paradigmet för nätverkstillägg). Detta är standardrekommendationen. I alla andra fall rekommenderar vi att du behandlar Azure som ett n:e datacenter.

## <a name="optimize-uptime-and-performance"></a>Optimera drifttid och prestanda
Om en tjänst är nere kan information inte nås. Om prestanda är så dåliga att data är oåtkomliga kan du anse att data är otillgängliga. Ur ett säkerhetsperspektiv måste du göra allt du kan för att se till att dina tjänster har optimal drifttid och prestanda.

En populär och effektiv metod för att förbättra tillgänglighet och prestanda är belastningsutjämning. Belastningsutjämning är en metod för att distribuera nätverkstrafik mellan servrar som ingår i en tjänst. Om du till exempel har frontend-webbservrar som en del av tjänsten kan du använda belastningsutjämning för att distribuera trafiken mellan dina flera frontend-webbservrar.

Den här trafikdistributionen ökar tillgängligheten eftersom om en av webbservrarna blir otillgänglig slutar belastningsutjämnaren att skicka trafik till den servern och omdirigerar den till de servrar som fortfarande är online. Belastningsutjämning hjälper också prestanda, eftersom processorn, nätverket och minnet för serveringsbegäranden fördelas över alla belastningsbalanserade servrar.

Vi rekommenderar att du använder belastningsutjämning när du kan och när det är lämpligt för dina tjänster. Följande är scenarier på både Azure virtuellt nätverk nivå och den globala nivån, tillsammans med load-balancing alternativ för varje.

**Scenario**: Du har ett program som:

- Kräver begäranden från samma användar-/klientsession för att nå samma serverdels virtuella dator. Exempel på detta är kundvagnsappar och webbpostservrar.
- Accepterar endast en säker anslutning, så okrypterad kommunikation till servern är inte ett acceptabelt alternativ.
- Kräver att flera HTTP-begäranden på samma långvariga TCP-anslutning dirigeras eller belastningsutjämnas till olika backend-servrar.

**Load-balancing alternativ**: Använd [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction), en HTTP-webbtrafikbelastningsutjämningsfaktor. Application Gateway stöder end-to-end TLS-kryptering och [TLS-avslutning](/azure/application-gateway/application-gateway-introduction) vid gatewayen. Webbservrar kan sedan avlastas från kryptering och dekryptering overhead och trafik som flyter okrypterad till backend-servrar.

**Scenario**: Du måste läsa in aldot för inkommande anslutningar från internet mellan dina servrar i ett virtuellt Azure-nätverk. Scenarier är när du:

- Har tillståndslösa program som accepterar inkommande förfrågningar från Internet.
- Kräver inte klibbiga sessioner eller TLS avlastning. Sticky sessioner är en metod som används med Application Load Balancing, för att uppnå servertillhörighet.

**Load-balancing alternativ**: Använd Azure-portalen för att [skapa en extern belastningsutjämnare](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) som sprider inkommande begäranden över flera virtuella datorer för att ge en högre nivå av tillgänglighet.

**Scenario**: Du måste läsa in balansanslutningar från virtuella datorer som inte finns på internet. I de flesta fall initieras de anslutningar som accepteras för belastningsutjämning av enheter i ett virtuellt Azure-nätverk, till exempel SQL Server-instanser eller interna webbservrar.   
**Load-balancing alternativ**: Använd Azure-portalen för att [skapa en intern belastningsutjämnare](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) som sprider inkommande begäranden över flera virtuella datorer för att ge en högre nivå av tillgänglighet.

**Scenario:** Du behöver global belastningsutjämning eftersom du:

- Har en molnlösning som är allmänt fördelad över flera regioner och kräver högsta möjliga drifttidsnivå (tillgänglighet).
- Behöver högsta möjliga drifttid för att se till att tjänsten är tillgänglig även om ett helt datacenter blir otillgängligt.

**Load-balancing alternativ**: Använd Azure Traffic Manager. Traffic Manager gör det möjligt att läsa in balansanslutningar till dina tjänster baserat på användarens plats.

Om användaren till exempel gör en begäran till din tjänst från EU dirigeras anslutningen till dina tjänster i ett EU-datacenter. Den här delen av Traffic Manager globala belastningsutjämning hjälper till att förbättra prestanda eftersom det går snabbare att ansluta till närmaste datacenter än att ansluta till datacenter som är långt borta.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Inaktivera RDP/SSH-åtkomst till virtuella datorer
Det är möjligt att nå virtuella Azure-datorer med hjälp av [RDP (Remote Desktop Protocol)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) och [SSH-protokollet (Secure Shell).](https://en.wikipedia.org/wiki/Secure_Shell) Dessa protokoll möjliggör hantering av virtuella datorer från fjärranslutna platser och är standard i datacenterberäkning.

Det potentiella säkerhetsproblemet med att använda dessa protokoll via Internet är att angripare kan använda [brute force-tekniker](https://en.wikipedia.org/wiki/Brute-force_attack) för att få åtkomst till virtuella Azure-datorer. När angripare får åtkomst kan de använda den virtuella datorn som en startpunkt för att kompromettera andra datorer i det virtuella nätverket eller till och med attackera nätverksanslutna enheter utanför Azure.

Vi rekommenderar att du inaktiverar direkt RDP- och SSH-åtkomst till dina virtuella Azure-datorer från internet. När direkt RDP- och SSH-åtkomst från Internet är inaktiverat har du andra alternativ som du kan använda för att komma åt dessa virtuella datorer för fjärrhantering.

**Scenario**: Aktivera en enskild användare för att ansluta till ett virtuellt Azure-nätverk via internet.   
**Alternativ:** [Punkt-till-plats VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create) är en annan term för en VPN-klient/serveranslutning för fjärråtkomst. När point-to-site-anslutningen har upprättats kan användaren använda RDP eller SSH för att ansluta till alla virtuella datorer som finns i det virtuella Azure-nätverket som användaren är ansluten till via point-to-site VPN. Detta förutsätter att användaren har behörighet att nå dessa virtuella datorer.

Point-to-site VPN är säkrare än direkta RDP- eller SSH-anslutningar eftersom användaren måste autentisera två gånger innan den ansluter till en virtuell dator. Först måste användaren autentisera (och godkännas) för att upprätta punkt-till-plats-VPN-anslutningen. För det andra måste användaren autentisera (och godkännas) för att upprätta RDP- eller SSH-sessionen.

**Scenario**: Gör det möjligt för användare i det lokala nätverket att ansluta till virtuella datorer i ditt virtuella Azure-nätverk.   
**Alternativ:** En [plats-till-plats VPN](/azure/vpn-gateway/vpn-gateway-site-to-site-create) ansluter ett helt nätverk till ett annat nätverk via Internet. Du kan använda ett VPN från plats till plats för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk. Användare i det lokala nätverket ansluter med hjälp av RDP- eller SSH-protokollet via VPN-anslutningen för plats till plats. Du behöver inte tillåta direkt RDP eller SSH-åtkomst via Internet.

**Scenario**: Använd en dedikerad WAN-länk för att tillhandahålla funktioner som liknar vpn från plats till plats.   
**Alternativ**: Använd [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Det ger funktioner som liknar plats-till-plats-VPN. De viktigaste skillnaderna är:

- Den dedikerade WAN-länken korsar inte internet.
- Dedikerade WAN-länkar är vanligtvis mer stabila och presterar bättre.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk
Använd slutpunkter för virtuella nätverkstjänster för att utöka det privata adressutrymmet för det virtuella nätverket och identiteten för det virtuella nätverket till Azure-tjänsterna via en direktanslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt virtuella nätverk till Azure-tjänsten finns alltid kvar i Microsoft Azure-stamnätet.

Tjänstslutpunkter har följande fördelar:

- **Förbättrad säkerhets för dina Azure-tjänstresurser**: Med tjänstslutpunkter kan Azure-tjänstresurser skyddas i ditt virtuella nätverk. När tjänstresurser skyddas i ett virtuellt nätverk ökar säkerheten genom att den offentliga internetåtkomsten till resurserna tas bort helt, så att endast trafik från ditt virtuella nätverk tillåts.
- **Optimal routning för Azure-tjänsttrafik från ditt virtuella nätverk:** Alla vägar i ditt virtuella nätverk som tvingar internettrafik till dina lokala och/eller virtuella enheter, så kallade tvingande tunnel, tvingar också Azure-tjänsttrafik att ta samma väg som internettrafiken. Med tjänstslutpunkter får du optimal routning för Azure-trafiken.

  Slutpunkter tar alltid servicetrafik direkt från ditt virtuella nätverk till tjänsten i Azure-stamnätet. Genom att hålla trafiken på Azure-stamnätet kan du fortsätta att granska och övervaka utgående internettrafik från dina virtuella nätverk, genom påtvingad tunnel, utan att påverka servicetrafiken. Läs mer om [användardefinierade rutter och påtvingad tunnel.](../../virtual-network/virtual-networks-udr-overview.md)

- **Enkelt att konfigurera med mindre hanteringskostnader:** Du behöver inte längre reserverade, offentliga IP-adresser i dina virtuella nätverk för att skydda Azure-resurser via en IP-brandvägg. Det behövs inga NAT- eller gatewayenheter för att konfigurera tjänstslutpunkterna. Tjänstslutpunkter konfigureras via ett enkelt klick på ett undernät. Det finns inga ytterligare omkostnader för att underhålla slutpunkterna.

Mer information om tjänstslutpunkter och Azure-tjänster och regioner som tjänstslutpunkter är tillgängliga för finns i Slutpunkter för [virtuella nätverkstjänst .](../../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="next-steps"></a>Nästa steg
Se metodtips och mönster för [Azure-säkerhet](best-practices-and-patterns.md) för fler metodtips för säkerhet som ska användas när du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.
