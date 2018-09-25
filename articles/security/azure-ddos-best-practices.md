---
title: Azure DDoS Protection bästa praxis och referensarkitekturer | Microsoft Docs
description: Läs mer om hur du kan använda loggningsdata för att få djupa insikter om ditt program.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: 29aea0171c767b2188e3eac23b1d61ffbda8e284
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986858"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Azure DDoS Protection: Bästa praxis och referensarkitekturer

Den här artikeln är för IT-beslutsfattare och security personal. Det förväntar sig att du är bekant med Azure, nätverk och säkerhet.

Designa för distribuerade överbelastningsattacker (DDoS) kräver återhämtning planering och design för en mängd olika fellägen. Den här artikeln innehåller metodtips för att utforma program i Azure för skydd mot DDoS-attacker.

## <a name="types-of-attacks"></a>Typer av attacker

DDoS är en typ av angrepp som används för att få slut på resurser. Målet är att påverka programmets tillgänglighet och dess förmåga att hantera legitima begäranden. Attacker blir allt mer sofistikerade och större i storlek och effekt. DDoS-attacker kan riktas till valfri slutpunkt som kan nås offentligt via internet.

Azure tillhandahåller kontinuerligt skydd mot DDoS-attacker. Det här skyddet är integrerad i Azure-plattformen som standard och utan extra kostnad. 

Förutom grundläggande DDoS-skydd i plattformen, [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) innehåller avancerade DDoS-skyddsfunktioner mot nätverksattacker. Det är automatiskt justerade för att skydda din specifika Azure-resurser. Skydd är enkelt att använda när du skapar nya virtuella nätverk. Det kan också göras när du har skapat och kräver inga ändringar i programmet eller resursen.

![Rollen Azure DDoS Protection har i att skydda kunder och ett virtuellt nätverk från en angripare](media/azure-ddos-best-practices/image1.png)

DDoS-attacker kan klassificeras i tre kategorier: Överför, protokoll och resursen.

### <a name="volumetric-attacks"></a>Överför attacker

Överför attacker är den vanligaste typen av DDoS-attacker. Överför attacker är brute force-assaults som är riktade till nätverks- och lager. De försöker att få slut på resurser som nätverkslänkar. 

Dessa attacker använder ofta flera infekterade system för att översvämma nätverk lager med till synes legitima trafik. De använder nätverkslager protokoll som ICMP Internet Control Message Protocol (), User Datagram Protocol (UDP) och Transmission Control Protocol (TCP).

De vanligaste nätverkslagret DDoS-attacker är TCP SYN överbelasta ICMP echo, UDP överbelasta, DNS, och NTP-förstärkning attacker. Den här typen av angrepp kan användas inte bara för att stoppa tjänsten, utan även som en smokescreen för skräppost och mer riktad intrång. Ett exempel på en nyligen överför attack är det [Memcached utnyttja](https://www.wired.com/story/github-ddos-memcached/) som påverkade GitHub. Angrepp riktade UDP-port 11211 och genereras 1.35 Tb/s av attack volym.

### <a name="protocol-attacks"></a>Protokollet attacker

Protokollet attacker target programprotokoll. Försöker de förbrukar alla tillgängliga resurser i infrastrukturen enheter, till exempel brandväggar, programservrar, och belastningsutjämnare. Protokollet attacker använda paket som felaktigt eller innehålla protokollet avvikelser. Dessa attacker driva genom att skicka stora mängder öppna förfrågningar att servrar och andra kommunikationsenheter besvara och vänta tills ett paket-svar. Målet försöker besvara öppna begäranden, vilket till slut medför systemet ska krascha.

Det vanligaste exemplet-protokollsbaserade DDoS-angrepp är TCP SYN mängd. I den här angrepp försöker en följd av begäranden för TCP SYN utnyttjandet ett mål. Målet är att göra svarar inte. 2016 Dyn avbrott, förutom de som angreppet programnivå bestod av TCP SYN floods den riktade porten 53 Dyn's DNS-servrar.

### <a name="resource-attacks"></a>Resurs-attacker

Resursen attacker rikta programnivån. De utlösa backend-processer i syfte att överbelasta ett system. Resurs-attacker missbruk trafik som ser ut normala men som uppfyller processorintensiva frågor till servern. Mängden trafik som behövs för att få slut på resurser är lägre än för den andra typen av attacker. Trafik i en resurs-attack är identiska med legitima trafik, vilket gör det svårt att identifiera. De vanligaste resurs-attackerna finns på HTTP/HTTPS- och DNS-tjänster.

## <a name="shared-responsibility-in-the-cloud"></a>Delat ansvar i molnet

Skydd på djupet hjälper till att bekämpa olika aspekter och grad av attacker. Säkerhet är ett delat ansvar mellan kunden och Microsoft. Microsoft kallar det en [delat ansvar modellen](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Följande bild visar den här delen av ansvarsområden:

![Ansvarsområden av kunden och Azure](media/azure-ddos-best-practices/image2.png)

Azure-kunder dra nytta av granska metodtips och skapa globalt distribuerade program som designas och testas för fel.

## <a name="fundamental-best-practices"></a>Grundläggande Metodtips

I följande avsnitt ger vägledning för att skapa DDoS anpassningsbara tjänster på Azure.

### <a name="design-for-security"></a>Utforma för säkerhet

Kontrollera att säkerhet är en prioritet under hela livscykeln för ett program, från design och implementering till distribution och drift. Program kan ha buggar som gör att en relativt låg volym av begäranden om att använda en ovanligt mängd resurser, vilket resulterar i avbrott i tjänsten. 

För att skydda en tjänst som körs på Microsoft Azure, bör du ha en god förståelse av programarkitekturen och fokusera på den [fem grundpelare för programkvalitet](https://docs.microsoft.com/azure/architecture/guide/pillars).
Du bör känna till vanliga trafikvolymer modellen anslutning mellan programmet och andra program och Tjänsteslutpunkter som exponeras för det offentliga internet.

Se till att ett program är tillräckligt flexibel för att hantera DOS-attacker som riktas mot själva programmet är viktigast. Säkerhet och sekretess är inbyggt i Azure-plattformen, från och med den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL-processen hanterar säkerheten i varje utvecklingsfas och ser till att Azure uppdateras ständigt för att göra det ännu säkrare.

### <a name="design-for-scalability"></a>Design för skalbarhet

Skalbarhet är hur bra ett system kan hantera ökad belastning. Du måste programmen utformas för [skala horisontellt](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) att uppfylla behovet av en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om ditt program är beroende av en enda instans av en tjänst, skapas en enskild felpunkt. Etablering av flera instanser kan systemet mer skalbart och återhämtningsbart mer.

För [Azure App Service](../app-service/app-service-value-prop-what-is.md)väljer en [App Service-plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) som erbjuder flera instanser. Konfigurera var och en av dina roller att använda för Azure Cloud Services, [flera instanser](../cloud-services/cloud-services-choose-me.md). För [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), se till att din virtuella dator (VM)-arkitektur innehåller fler än en virtuell dator och att varje virtuell dator ingår i en [tillgänglighetsuppsättning](../virtual-machines/virtual-machines-windows-manage-availability.md). Vi rekommenderar att du använder [VM-skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för funktioner för automatisk skalning.

### <a name="defense-in-depth"></a>Skydd på djupet

Tanken bakom skydd på djupet är att hantera risker med hjälp av olika skydden strategier. Skikta säkerhetsskyddet i ett program minskar risken för angrepp. Vi rekommenderar att du implementerar säker design för dina program med hjälp av de inbyggda funktionerna i Azure-plattformen.

Till exempel risken för angrepp ökar med storleken (*ytområde*) av programmet. Du kan minska ytan med hjälp av listan över tillåtna program ska stänga exponerade IP-adressutrymmet och lyssningsportar som inte behövs på belastningsutjämnarna ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) och [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Nätverkssäkerhetsgrupper (NSG)](../virtual-network/security-overview.md) är ett annat sätt att minska risken för angrepp.
Du kan använda [tjänsttaggar](../virtual-network/security-overview.md#service-tags) och [programsäkerhetsgrupper](../virtual-network/security-overview.md#application-security-groups) att minska komplexiteten för att skapa säkerhetsregler och konfigurera nätverkssäkerhet, som en naturlig förlängning av ett programs struktur.

Du bör distribuera Azure-tjänster i en [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) när det är möjligt. Detta gör att service-resurser kommunicera via privata IP-adresser. Azure-tjänsttrafiken från ett virtuellt nätverk använder offentliga IP-adresser som käll-IP-adresser som standard. Med hjälp av [tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) växlar för att använda privata vnet-adresser som käll-IP-adresser när de får åtkomst till Azure-tjänsten från ett virtuellt nätverk-tjänstens trafik.

Ofta ser vi kundernas lokala resurser komma angripna tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure, rekommenderar vi att du minimera exponering av lokala resurser till det offentliga internet. Du kan använda den omfattning och avancerade DDoS protection-funktioner i Azure genom att distribuera dina välkänd offentlig entiteter i Azure. Eftersom dessa offentligt tillgänglig entiteter är ofta ett mål för DDoS-attacker, minskar placera dem i Azure påverkan på din lokala resurser.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-erbjudanden för DDoS-skydd

Azure har två DDoS-Tjänsterbjudanden som ger skydd mot nätverksattacker (Layer 3 och 4): DDoS Protection Basic- och DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>DDoS Protection Basic

Grundläggande skydd är integrerad i Azure som standard utan extra kostnad. Ger skydd mot vanliga attacker på nätverkslager via ständig övervakning och i realtid minskning skalbarhet och kapacitet för globalt distribuerade Azure-nätverket. DDoS Protection grundläggande kräver konfiguration eller programmet användarändringar. DDoS Protection Basic kan du skydda alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.

![Mappa representation av Azure-nätverket med texten ”globala DDoS minskning närvaro” och ”inledande DDoS-hanteringskapacitet”](media/azure-ddos-best-practices/image3.png)

Grundläggande DDoS-skydd i Azure består av både program- och komponenter. En programvara kontrollplanet avgör när, var, och vilken typ av trafik bör vara styrd via maskinvaruinstallationer som analyserar och ta bort attack trafik. Kontrollplanet gör detta beslut baserat på en infrastruktur för hela DDoS Protection *princip*. Den här principen ställs statiskt och tillämpas universellt på alla Azure-kunder.

Till exempel DDoS Protection-principen anger på vilka trafikvolym skyddet ska vara *utlöses.* (Det vill säga klientens trafik ska dirigeras via Skrubba installationer.) Principen anger sedan hur ska rensningsjobbet installationer *minimera* angreppet.

Tjänsten Azure DDoS Protection Basic är avsett för skydd av infrastrukturen och skydd av Azure-plattformen. Det minskar trafik när tröskeln för ett pris som är så stort att det kan påverka flera kunder i en miljö med flera innehavare. Det ger inte aviseringar eller per kund anpassade principer.

### <a name="ddos-protection-standard"></a>Standard för DDoS-skydd

Standard protection tillhandahåller funktioner för DDoS-minskning. Det är automatiskt justerade för att skydda din specifika Azure-resurser i ett virtuellt nätverk. Skydd är enkelt att aktivera på alla nya eller befintliga virtuella nätverket och det krävs inga ändringar i programmet eller resursen. Den har flera fördelar jämfört med grundläggande tjänsten, inklusive loggning, aviseringar och telemetri. I följande avsnitt beskrivs de viktigaste funktionerna i tjänsten Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Anpassningsbar justering i realtid

Tjänsten Azure DDoS Protection Basic hjälper att skydda kunder och förhindra påverkan för andra kunder. Exempel: om en tjänst har etablerats för en typisk volym av legitima inkommande trafik som är mindre än den *frekvensutlösare* av infrastruktur hela DDoS Protection-principen, DDoS-attacker på kundens resurser försätts obemärkt förbi. Vanligtvis, komplexitet och deras senaste attacker (till exempel flera vektor DDoS) och de programspecifika beteenden med klienter kräver per kund, anpassade skyddsprinciper. Tjänsten genomför den här anpassningen genom att använda två insights:

- Automatisk inlärning av (per IP) per kund trafikmönster för Layer 3 och 4.

- Minimera falska positiva identifieringar som överväger att skalningen i Azure kan det absorbera en betydande mängd trafik.

![Diagram över hur DDoS Protection Standard fungerar, med ”Principgenerering” inringade](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetri, övervakning och avisering

DDoS Protection Standard exponerar omfattande telemetri via [Azure Monitor](../azure-monitor/overview.md) för hela DDoS-angrepp. Du kan konfigurera aviseringar för någon av de Azure Monitor-mått som DDoS Protection använder. Du kan integrera loggning med Splunk (Azure Event Hubs), Azure Log Analytics och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

##### <a name="ddos-mitigation-policies"></a>Principer för DDoS-minskning

I Azure-portalen väljer du **övervakaren** > **mått**. I den **mått** fönstret, Välj resursgruppen, Välj en resurstyp för **offentliga IP-adressen**, och välj din Azure offentlig IP-adress. DDoS-mått är synliga i den **tillgängliga mått** fönstret.

DDoS Protection Standard gäller tre automatiskt justerade minskning principer (TCP SYN, TCP och UDP) för varje offentlig IP-Adressen för den skyddade resursen i det virtuella nätverket som har aktiverat DDoS. Du kan visa tröskelvärdena som principen genom att välja måttet **inkommande paket att utlösa DDoS-minskning**.

![Tillgängliga mått och diagram med prestandamått](media/azure-ddos-best-practices/image7.png)

Tröskelvärden för principen är automatiskt konfigurerad via profilering för machine learning-baserat nätverk trafik. DDoS-åtgärd utförs för en IP-adress under attack endast när principer tröskelvärden överskrids.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Mått för en IP-adress under DDoS-attack

Om den offentliga IP-adressen är attacker, värdet för måttet **Under DDoS-angrepp eller inte** ändras till 1 när DDoS Protection utför minskning av attack trafiken.

![”Under DDoS angrepp eller inte” mått och diagram](media/azure-ddos-best-practices/image8.png)

Vi rekommenderar att du konfigurerar en avisering på det här måttet. Du kommer sedan att meddelas när det finns en aktiv DDoS-åtgärd som utförs på din offentliga IP-adress.

Mer information finns i [hantera Azure DDoS Protection Standard med hjälp av Azure portal](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Brandvägg för webbaserade program för resurs-attacker

Specifikt för resursen attacker på programnivå, bör du konfigurera en brandvägg för webbaserade program (WAF) för att skydda dina webbappar. En WAF inspekterar inkommande webbtrafik att blockera SQL-inmatningar, skriptkörning över flera webbplatser, DDoS och andra Layer 7-attacker. Azure tillhandahåller [WAF som en funktion i Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md) för centraliserat skydd för dina webbprogram mot vanliga kryphål och säkerhetsproblem. Det finns andra WAF-erbjudanden som är tillgängliga från Azure-partner som kan passa bättre för dina behov via den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Även brandväggar för webbprogram är sårbar för attacker överför och statliga överbelastning. Vi att rekommenderar du aktiverar DDoS Protection Standard i det virtuella nätverket WAF för att skydda mot överför och protocol-attacker. Mer information finns i den [DDoS Protection referensarkitekturer](#ddos-protection-reference-architectures) avsnittet.

### <a name="protection-planning"></a>Planera för skydd

Planering och förberedelser är avgörande att förstå hur ett system utför under en DDoS-attack. Utforma en plan för incidenthantering svar är en del av det.

Om du har DDoS Protection Standard kan du kontrollera att den är aktiverad i det virtuella nätverket för internet-riktade slutpunkter. Konfigurera aviseringar för DDoS hjälper dig att se hela tiden för alla eventuella attacker på din infrastruktur. 

Du bör övervaka dina program oberoende av varandra. Förstå det normala beteendet för ett program. Förbered för att fungera om programmet inte fungerar som förväntat under en DDoS-attack.

#### <a name="testing-through-simulations"></a>Testa via simuleringar

Det är en bra idé att testa dina antaganden om hur dina tjänster kommer att besvara ett angrepp genom att utföra regelbundna simuleringar. Under testningen, verifiera att dina tjänster eller program fortsätter att fungera som förväntat och att det finns inga störningar i användarupplevelsen. Identifiera brister baserat på både teknik- och processen av och lägga till dem i strategi för DDoS-svar. Vi rekommenderar att du utför dessa tester i mellanlagringsmiljöer eller vid låg belastning minimera effekten till produktionsmiljön.

Vi samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) att skapa ett gränssnitt där Azure-kunder kan generera trafik mot DDoS Protection-aktiverade offentliga slutpunkter för simuleringar. Du kan använda den [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) simuleringen till:

- Kontrollera hur Azure DDoS Protection hjälper dig att skydda dina Azure-resurser mot DDoS-attacker.

- Optimera din incidenthanteringsprocess medan under DDoS-attacker.

- Dokumentera DDoS-efterlevnad.

- Träna dina network security team.

Cybersäkerhet kräver konstant innovation i defense. Azure Standard för DDoS protection är en-för-avancerade erbjuder en effektiv lösning för att minimera allt mer komplexa DDoS-attacker.

## <a name="components-of-a-ddos-response-strategy"></a>Komponenterna i en strategi för DDoS-svar

DDoS-attacker som riktas mot Azure-resurser vanligtvis kräver minimala åtgärder från användaren synpunkt. Fortfarande, införliva DDoS lösning som en del av en incidenthantering strategi hjälper till att minimera påverkan på affärskontinuitet.

### <a name="microsoft-threat-intelligence"></a>Microsoft hotinformation

Microsoft har ett omfattande threat intelligence nätverk. Det här nätverket använder en utökad säkerhet-community som har stöd för Microsoft online services, Microsoft-partner och relationer inom gruppen internet security kunskapen. 

Microsoft tar emot tidiga varningar om hot som en viktig infrastruktur-provider. Microsoft samlar in hotinformation från onlinetjänster och sin globala kundbas. Microsoft inkluderar alla den här hotinformationen tillbaka i Azure DDoS Protection-produkter.

Microsoft Digital Crimes Unit (DCU) utför även, stötande strategier mot botnät. Botnät är en gemensam källa till kommando och kontroll för DDoS-attacker.

### <a name="risk-evaluation-of-your-azure-resources"></a>Riskbedömning av dina Azure-resurser

Det är viktigt att förstå vilka risken från en DDoS-attack med jämna mellanrum. Regelbundet fråga dig själv: 
- Vilka nya allmänt tillgängliga Azure-resurser behöver skydd?

- Finns det en enskild felpunkt i tjänsten? 

- Hur kan tjänster är isolerade i begränsa effekten av en attack när fortfarande gör tjänster tillgängliga för giltiga kunder?

- Finns det virtuella nätverk där DDoS Protection Standard ska aktiveras men inte? 

- Är Mina tjänster aktiv/aktiv med redundans i flera regioner?

### <a name="customer-ddos-response-team"></a>Kunden DDoS svarsgrupp

Det är ett viktigt steg i att svara på en attack snabbt och effektivt för att skapa ett team för DDoS-svar. Identifiera kontakter i din organisation som ska övervaka planering och körning. Den här DDoS svarsgrupp bör noggrant förstå tjänsten Azure DDoS Protection Standard. Se till att teamet kan identifiera och åtgärda ett angrepp genom att koordinera med interna och externa kunder, inklusive Microsoft-supporten.

För teamet DDoS-svar rekommenderar vi att du använder simulering övningarna som en normal del av tjänstens tillgänglighet och affärskontinuitet planera. De här övningarna ska inkludera skaltestning.

### <a name="alerts-during-an-attack"></a>Aviseringar under ett angrepp

Azure DDoS Protection Standard identifierar och minskar risken för DDoS-attacker utan användarens ingripande. Om du vill få ett meddelande när det finns en aktiv lösning för en skyddad offentliga IP-adress, kan du [konfigurera en varning](../virtual-network/ddos-protection-manage-portal.md) på måttet **Under DDoS-angrepp eller inte**. Du kan välja att skapa aviseringar för andra mått för DDoS att förstå skalan för angrepp, trafik som förloras och andra uppgifter.

#### <a name="when-to-contact-microsoft-support"></a>När du kontaktar Microsoft support

- När en DDoS-attack hitta att prestandan hos den skyddade resursen är kraftigt försämrad eller resursen är inte tillgänglig.

- Du tycker att tjänsten DDoS Protection inte fungerar som förväntat. 

  Tjänsten DDoS Protection börjar minskning endast om mätvärdet **princip för att utlösa DDoS-åtgärd (SYN TCP/TCP/UDP)** är lägre än den trafik som tas emot på den skyddade offentliga IP-adressresursen.

- Du planerar en viral händelse som ökar avsevärt din nätverkstrafik.

- En aktör har riskerade att starta en DDoS-attack mot dina resurser.

För attacker som har en kritisk inverkan på verksamheten, skapa en allvarlighetsgrad A [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Steg efter angrepp

Det är alltid en bra strategi för att göra en postmortem efter ett angrepp och justera strategi för DDoS-svar efter behov. Saker att tänka på:

- Var det något avbrott på tjänsten eller användare får på grund av bristande skalbar arkitektur?

- Vilka program eller tjänster haft mest?

- Hur effektiva var strategi för DDoS-svar och hur kan den förbättras?

Om du misstänker att du är under en DDoS-attack, eskalera via dina vanliga supportkanaler för Azure.

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection-referensarkitekturer

DDoS Protection Standard har utformats [för tjänster som har distribuerats i ett virtuellt nätverk](../virtual-network/virtual-network-for-azure-services.md). DDoS Protection grundläggande standardtjänsten gäller för andra tjänster. I följande referensarkitekturer ordnas efter scenarier med mönster för molnarkitektur grupperas tillsammans.

### <a name="virtual-machine-windowslinux-workloads"></a>Arbetsbelastningar på virtuella datorer (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Program som körs på belastningsutjämnade virtuella datorer

Denna Referensarkitektur visas en uppsättning beprövade metoder för att köra flera virtuella Windows-datorer i en skaluppsättning bakom en belastningsutjämnare för att förbättra tillgängligheten och skalbarheten. Den här arkitekturen kan användas för tillståndslösa arbetsbelastningar, till exempel en webbserver.

![Diagram över Referensarkitektur för program som körs på belastningsutjämnade virtuella datorer](media/azure-ddos-best-practices/image9.png)

En arbetsbelastning distribueras över flera VM-instanser i den här arkitekturen. Det finns en enda offentlig IP-adress och internet-trafiken ska distribueras till de virtuella datorerna via en belastningsutjämnare. DDoS Protection Standard är aktiverat på det virtuella nätverket för Azure (internet) belastningsutjämnaren som har den offentliga IP-Adressen som är associerade med den.

Belastningsutjämnaren distribuerar inkommande internet-begäranden till de Virtuella datorinstanserna. VM-skalningsuppsättningar gör att antalet virtuella datorer kan skalas in eller ut manuellt eller automatiskt baserat på fördefinierade regler. Detta är viktigt om resursen ligger under DDoS-attacker. Mer information om denna Referensarkitektur finns [i den här artikeln](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Program som körs på Windows N-nivå

Det finns många sätt att implementera en arkitektur på N-nivå. Följande diagram visar ett typiskt webbprogram på tre nivåer. Den här arkitekturen bygger på artikeln [kör belastningsutjämnade virtuella datorer för skalbarhet och tillgänglighet](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webb- och företagsnivåer använder belastningsutjämnade virtuella datorer.

![Diagram över Referensarkitektur för program som körs på Windows N-nivå](media/azure-ddos-best-practices/image10.png)

I den här arkitekturen är DDoS Protection Standard aktiverad på det virtuella nätverket. Alla offentliga IP-adresser i det virtuella nätverket få DDoS-skydd för Layer 3 och 4. Distribuera Application Gateway i WAF SKU för Layer 7-skydd. Mer information om denna Referensarkitektur finns [i den här artikeln](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS-webbprogram

Den här referensarkitekturen visar köra en Azure App Service-App i en enda region. Den här arkitekturen visar en uppsättning beprövade metoder för ett webbprogram som använder [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) och [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
En standby region har ställts in för redundansscenarier.

![Diagram över referensarkitekturen för ett PaaS-webbprogram](media/azure-ddos-best-practices/image11.png)

Med Azure Traffic Manager dirigerar inkommande begäranden till Application Gateway i en av regionerna. Under normal drift dirigerar den begäranden till Application Gateway i den aktiva regionen. Om den regionen blir otillgänglig, Traffic Manager som redundansväxlar till Application Gateway i vänteläge region.

All trafik från internet är avsedd för webbprogrammet dirigeras till den [Application Gateway offentliga IP-adressen](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) via Traffic Manager. I det här scenariot, app service (webbapp) är inte direkt externt riktade och skyddas av Application Gateway. 

Vi rekommenderar att du konfigurerar Application Gateway WAF-SKU (förhindra läge) för att skydda mot attacker med Layer 7 (HTTP/HTTPS/WebSocket). Dessutom webbappar är konfigurerade för [accepterar endast trafik från Programgatewayen](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-adress.

Mer information om denna Referensarkitektur finns [i den här artikeln](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Lösning för icke-PaaS-webbtjänster

#### <a name="hdinsight-on-azure"></a>HDInsight på Azure

Den här referensarkitekturen visar hur du konfigurerar DDoS Protection Standard för en [Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/). Se till att HDInsight-klustret är länkad till ett virtuellt nätverk och att DDoS Protection är aktiverat på det virtuella nätverket.

![”HDInsight” och ”avancerade inställningar” fönster med inställningar för virtuella nätverk](media/azure-ddos-best-practices/image12.png)

![Val för att aktivera DDoS-skydd](media/azure-ddos-best-practices/image13.png)

I den här arkitekturen dirigeras trafiken till HDInsight-kluster från internet till den offentliga IP-Adressen som är associerade med belastningsutjämnaren för HDInsight-gatewayen. Belastningsutjämnare för Programgateway skickar sedan trafiken till huvudnoderna eller arbetsnoderna direkt. Eftersom DDoS Protection Standard är aktiverat på det virtuella nätverket för HDInsight, får alla offentliga IP-adresser i det virtuella nätverket DDoS-skydd för Layer 3 och 4. Den här referensarkitekturen kan kombineras med N-nivå och referensarkitekturer för flera regioner.

Mer information om denna Referensarkitektur finns i den [utöka Azure HDInsight med hjälp av Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentation.


> [!NOTE]
> Azure App Service Environment för PowerApps eller API-hantering i ett virtuellt nätverk med en offentlig IP-adress stöds båda inte.

## <a name="next-steps"></a>Nästa steg

* [Produktsida för Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS Protection-blogg](http://aka.ms/ddosblog)

* [Dokumentation om Azure DDoS Protection](../virtual-network/ddos-protection-overview.md)
