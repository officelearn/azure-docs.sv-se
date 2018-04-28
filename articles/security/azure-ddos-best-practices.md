---
title: Metodtips för Azure DDoS-skydd & Referensarkitektur | Microsoft Docs
description: Läs mer om hur du kan använda loggning av data och få djupa insikter om ditt program.
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
ms.date: 04/20/2018
ms.author: barclayn
ms.openlocfilehash: 4b2d785f5b9095a2decfc65ec46808ff6f65c38e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Azure DDoS-skydd: Metodtips och referera till arkitektur

Det här dokumentet är avsett för IT-beslutsfattare och säkerhet personal. Om du är bekant med Azure, nätverk och säkerhet förväntas.

Utformning för distribuerade DOS-attacker (DDoS) kräver återhämtning planering och utformning av olika feltillstånd. Detta dokument ger bästa praxis för att utforma program i Azure för återhämtning mot DDoS-attacker.

## <a name="types-of-attacks"></a>Typer av attacker

DDoS är en typ av angrepp som används för att försöka att få slut på resurser. Målet är att påverka programmets tillgänglighet och möjlighet att hantera legitima begäranden. Attacker blir mer förfinade och större i storlek och effekt. DDoS-attacker kan vara mål för valfri slutpunkt som är offentligt tillgänglig via Internet.

Azure tillhandahåller kontinuerlig skydd mot DDoS-attacker. Det här skyddet är integrerad i Azure-plattformen som standard och utan extra kostnad. Förutom grundläggande DDoS-skydd som ges i plattformen har vi också ett nytt erbjudande med namnet ”[Azure DDoS-skydd Standard](https://azure.microsoft.com/services/ddos-protection/)”, som ger avancerade funktioner för DDoS-lösning mot nätverksattacker och automatiskt justerade för att skydda specifika Azure-resurser. Skydd är enkel att aktivera under genereringen av nya virtuella nätverk. Det kan även utföras när och kräver inga ändringar i programmet eller resursen.

![](media/azure-ddos-best-practices/image1.png)

DDoS-attacker kan brett delas in i tre (3) i olika kategorier

### <a name="volumetric-attacks"></a>Överför attacker

Överför attacker är de vanligaste typerna av DDoS-attacker. Överför attacker är brute force-assaults som är riktade till nätverks- och lager. Försök att få slut på resurser sådana nätverkslänkar. Angreppen används ofta flera infekterade system för att göra nätverket lager med en stor mängd till synes legitima trafik. Olika nätverksprotokoll som kontrollen meddelandet ICMP (Internet Protocol), User Datagram Protocol (UDP) och Transmission Control Protocol (TCP) används i den här typen av attacker.

De vanligaste nätverksnivån DDoS-attacker är TCP SYN översvämning, ICMP echo, UDP översvämning, DNS och NTP förstärkning attack. Den här typen av attacker kan användas inte bara för att stoppa tjänsten, utan även som en smokescreen för nefarious och mer riktad intrång. Ett exempel på en senaste överför attack är den [Memcached utnyttja](https://www.wired.com/story/github-ddos-memcached/) som påverkas GitHub. Angrepp riktade UDP-port 11211 och genererade 1.35 Tb/s attack volym.

### <a name="protocol-attacks"></a>Protokollet attacker

Protokollet attacker mål programprotokoll. Försök att använda alla tillgängliga resurser i infrastrukturen enheter till exempel brandväggar, programservrar och belastningsutjämnare. Protokollet attacker använda paket som är felaktig eller innehåller protokollet avvikelser. Angreppen fungerar genom att skicka större antal öppna förfrågningar, servrar och andra kommunikation enheter svar och vänta tills ett svar i paketet. Målet försöker besvara de öppna vilket till slut medför riktade systemet kraschar.

De vanligaste exempel på ett protokoll-baserade DDoS-attacker är TCP SYN översvämning. I det här angreppet upprepade TCP SYN begäranden är riktad mot ett mål och kan användas för att överbelasta den. Målet är att göra svarar inte. 2016 Dyn avbrott, förutom att en attack på programnivå också bestod av TCP SYN översvämningar riktad port 53 Dyns DNS-servrar.

### <a name="resource-attacks"></a>Resurs-attacker

Resursen attacker mål Applikationsnivån. De utlöser backend-processer för att överbelasta målsystemet. Resurs-attacker missbruk trafik som ser ut normal men som utför processorintensiva frågor till servern. Mängden trafik som behövs för att få slut på resurser är relativt lägre än för den andra typen av attacker. Trafik i en resurs-attack är identiska med legitima trafik, vilket gör det svårt att identifiera. De vanligaste attacker som resursen finns på HTTP/HTTPS- och DNS-tjänster.

## <a name="shared-responsibility-in-the-cloud"></a>Delat ansvar i molnet

En detaljerad strategi för skydd krävs för att bekämpa ökande typer och nivå av attacker. Säkerhet är ett delat ansvar mellan kunden och Microsoft. Microsoft refererar till detta som en [delat ansvar modellen](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Bilden nedan visar denna uppdelning av ansvar.

![](media/azure-ddos-best-practices/image2.png)

Azure-kunder dra nytta av granska vår bästa praxis och skapa globalt distribuerade program och testades för felet.

## <a name="fundamental-best-practices"></a>Grundläggande bästa praxis

DDoS-attacker är och ökar. Följande avsnitt ger vägledning för att skapa flexibla DDoS-tjänster på Azure.

### <a name="design-for-security"></a>Utforma för säkerhet

Kunder bör kontrollera säkerheten är en prioritet under hela livscykeln för ett program från designen och implementeringen till distribution och användning. Program kan ha buggar som gör att en relativt låg mängd utformad begär att använda orimligt mycket resurser, vilket resulterar i ett avbrott i tjänsten. Om du vill skydda en tjänst som körs på Microsoft Azure kunder bör ha en god förståelse av deras programarkitektur och fokus bör läggas på den [5 pelare programvara kvalitet](https://docs.microsoft.com/azure/architecture/guide/pillars).
Kunder bör veta vanliga trafikvolymer modellen anslutning mellan programmet och andra program och Tjänsteslutpunkter exponeras för det offentliga Internet.

Dessutom är säkerställer att ett program som inte är känsligt att hantera en denial of service riktad mot själva programmet av avgörande betydelse. Säkerhet och sekretess är inbyggda i Azure-plattformen, från och med den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Av SDL adresser säkerhet i varje utvecklingsfas och säkerställer att Azure uppdateras kontinuerligt så att den blir ännu säkrare.

### <a name="design-for-scalability"></a>Design för skalbarhet

Systemets förmåga att hantera ökad belastning. Kunderna måste utforma sina program för att [skala horisontellt](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) att uppfylla behovet av förstärkt belastning, särskilt i en händelse för DDoS-attacker. Om programmet är beroende av en enda instans av en tjänst, skapas en enskild felpunkt. Etablering av flera instanser förbättrar både flexibilitet och skalbarhet.

För [Azure App Service](../app-service/app-service-value-prop-what-is.md), Välj en [Apptjänstplan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) som erbjuder flera instanser. Konfigurera var och en av dina roller som ska användas för Azure Cloud Services [flera instanser](../cloud-services/cloud-services-choose-me.md). För [Azure virtuella datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), se till att VM-arkitektur innehåller mer än en virtuell dator och att varje virtuell dator ska ingå i en [tillgänglighetsuppsättning](../virtual-machines/virtual-machines-windows-manage-availability.md). Vi rekommenderar att du använder [Skalningsuppsättningar i virtuella](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för autoskalning funktioner.

### <a name="defense-in-depth"></a>Skydd på djupet

Tanken bakom skydd på djupet är att hantera risker med olika defensiva strategier. Skikta säkerhetsskyddet i ett program minskar risken för ett angrepp. Vi rekommenderar att kunder implementera säker Designer för programmen via de inbyggda funktionerna i Azure-plattformen.

Till exempel ökar risken för angrepp med storlek eller ytan på programmet. Ytan minskning rekommenderas via vitlistning att stänga exponerade IP adress utrymme & lyssningsportar som inte behövs på belastningsutjämnare ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[App Gateway](../application-gateway/application-gateway-create-probe-portal.md)) eller via [Nätverkssäkerhetsgrupper (NSG).](../virtual-network/virtual-networks-nsg.md)
Du kan använda [tjänsten taggar](/virtual-network/security-overview.md) och [programmet säkerhetsgrupper](/virtual-network/security-overview.md) minimera komplexitet för att skapa en regel för säkerhet och konfigurera nätverkssäkerhet som en naturlig förlängning av ett program.

Kunderna bör distribuera Azure-tjänster i en [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) när det är möjligt. Detta gör tjänsten resurser för att kommunicera via privata IP-adresser. Azure-tjänstens trafik från ett virtuellt nätverk använder offentliga IP-adresser som käll-IP-adresser som standard. Med hjälp av [tjänstens slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) växlar för att använda virtuella privata nätverksadresser som käll-IP-adresser vid åtkomst till Azure-tjänsten från ett virtuellt nätverk-tjänstens trafik.

Ofta visas kundens lokala resurser komma angripna tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure, rekommenderar vi att du minimerar risken för lokala resurser för det offentliga Internet. Du kan använda skala och avancerade DDoS-skydd-funktionerna i Azure genom att distribuera din välkända offentliga enheter i Azure. Eftersom dessa offentligt tillgänglig enheter är ofta mål för DDoS-attacker är minskar att placera dem i Azure inverkan på din lokala resurser.

## <a name="azure-ddos-protection"></a>Azure DDoS-skydd

Azure har två DDoS Tjänsterbjudanden som ger skydd mot attacker (Layer 3 och 4) - DDoS grundläggande skydd och DDoS-standard. 

### <a name="azure-ddos-basic-protection"></a>Azure basic DDoS-skydd

Grundläggande skydd är integrerat i Azure som standard utan extra kostnad. Ger skydd mot vanliga layer nätverksattacker genom att alltid på trafik övervaknings- och realtid minskning full skala och kapacitet för Azures globalt distribuerade nätverk. Grundläggande DDoS-skydd kräver konfiguration eller programmet användarändringar. Alla Azure-tjänster som inklusive PaaS-tjänster som Azure DNS skyddas av grundläggande DDoS-skydd.

![](media/azure-ddos-best-practices/image3.png)

Azures grundläggande DDoS-skydd består av både program-och maskinvara. En plan för kontroll av programvara avgör när, var och vilken typ av trafik bör vara styrd via maskinvara apparater som analysera och ta bort attack trafik. Plan för åtkomstkontroll gör detta beslut baserat på en infrastruktur för hela DDoS-skydd *princip*, som är statiskt värdet och tillämpas universellt alla Azure-kunder.

Exempelvis DDoS-skydd principen anger på vilka trafikvolym skyddet ska vara *utlöses* (det vill säga den klienttrafik ska vidarebefordras genom skrubbningsjobb installationer), och därefter, hur den skrubbningsjobb installationer bör *minimera* angrepp.

Azures DDoS-skydd grundläggande service är inriktad på infrastruktur och skydd av Azure-plattformen. Det minskar trafik när den överskrider en hastighet som är viktiga att det kan påverka flera kunder i en miljö med flera innehavare. Den ger inte aviseringar eller per kund anpassade principer.

### <a name="azure-ddos-standard-protection"></a>Azure standard DDoS-skydd

Standard skydd ger utökade funktioner för DDoS-lösning och justerade automatiskt för att skydda specifika Azure-resurser i ett virtuellt nätverk. Skydd är enkel att aktivera på alla nya eller befintliga virtuella nätverk och kräver inget program eller resursändringar. Den har flera fördelar jämfört med tjänsten grundläggande bland annat loggning, varningar och telemetri. Nedan anges de viktiga skillnaderna för Azure DDoS-skydd Standard tjänsten.

#### <a name="adaptive-realtime-tuning"></a>Anpassningsbar realtid justera

Azure DDoS-skydd grundläggande service hjälper dig att skydda kunder men bara skyddar dem för att förhindra att påverka andra kunder. Om exempelvis en tjänst har etablerats för en typisk volym av legitima inkommande trafik som är mindre än den *utlösaren hastighet* för hela infrastrukturen DDoS-skydd för en DDoS-attack på resurser som kunden kan gå oupptäckta. Generellt komplexa uppbyggnad senaste attacker (till exempel flera vector DDoS) samt programspecifika beteenden för innehavare-anropet efter per kund, anpassade skyddsprinciper.
Detta åstadkoms med hjälp av två insikter:

1. Automatisk inlärning (per-IP) per kunden layer 3/4 trafikmönster och
2. Minimera falska positiva identifieringar med tanke på att Azures skala tillåter att det skulle kunna ta emot trafik mycket.

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>DDoS-skydd telemetri, övervakning och aviseringar

Med DDoS-skydd Standard använda omfattande telemetri via [Azure-Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) under en DDoS-attack. Aviseringar kan konfigureras för alla Azure-Monitor-mått som exponeras av DDoS-skydd. Loggning kan integreras med Splunk (Azure Event Hubs) och Azure logganalys Azure Storage för avancerad analys via Azure-Monitor diagnostik-gränssnittet.

##### <a name="ddos-mitigation-policies"></a>Principer för DDoS-lösning

I Azure-portalen klickar du på **övervakaren** \> **mått**. På den **mått** , väljer resursgruppen resurstypen för offentlig IP-adress och din Azure offentliga IP-adress. DDoS mått visas under rutan tillgängliga mått.

DDoS-skydd Standard gäller **tre automatiskt justerade minskning principer (TCP SYN, TCP och UDP)** för varje offentlig IP-adress för den skyddade resursen i virtuella nätverk som har DDoS aktiverat. Du kan visa tröskelvärdena som principen genom att välja mått **'Inkommande paket som utlöser DDoS minskning'**.

![](media/azure-ddos-best-practices/image7.png)

Tröskelvärden för principen är automatiskt konfigurerade via vårt maskininlärning baserat nätverk trafik profilering. DDoS minskning inträffar för en IP-adress angripet bara när principen tröskelvärde har överskridits.

##### <a name="under-ddos-attack"></a>Under DDoS-attack

Om den offentliga IP-adressen är angripna, växlar mått 'under DDoS attacker eller inte ”-värdet till 1 vi utföra lösning för attack trafiken.

![](media/azure-ddos-best-practices/image8.png)

Vi rekommenderar att du konfigurerar en avisering på det här måttet som ska meddelas när det finns en aktiv DDoS minskning utförs på din offentliga IP-adress.

Mer information finns i [hantera Azure DDoS-skydd Standard med hjälp av Azure portal](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="resource-attacks"></a>Resurs-attacker

Specifika för resursen attacker på programnivån, kunder bör du konfigurera Web Application Firewall (Brandvägg) för att säkra webbprogram. Brandvägg kontrollerar om inkommande webbtrafik för att blockera SQL injektioner, globala webbplatsskript, DDoS och andra layer 7-attacker. Azure tillhandahåller [Brandvägg som en funktion för Programgateway](../application-gateway/application-gateway-web-application-firewall-overview.md) för centraliserad skydd av dina webbapplikationer från vanliga kryphål och säkerhetsproblem. Av Brandvägg erbjudanden är tillgängliga från Azure-partners som kan vara lämpligt för dina behov via den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Även Web Application brandväggar är sårbar för angrepp med Överför & tillstånd uttömning. Vi rekommenderar starkt aktivera DDoS-Standard skydd på det virtuella nätverket Brandvägg att skydda mot Överför & protocol-attacker. Mer information finns i avsnittet referens arkitektur.

### <a name="protection-planning"></a>Planera för skydd

Planering och förberedelser är viktigt att förstå hur en systemet utför under en DDoS-attack. Den här planering och förberedelser kommer också hjälpa dig att utforma en åtgärdsplan för incidenthantering.

Kunder bör se till att DDoS-skydd Standard är aktiverat på det virtuella nätverket Internet facing endpoints. Konfigurera aviseringar för DDoS hjälper till att hålla en konstant watchful öga på alla potentiella attacker på din infrastruktur. Kunder bör du övervaka sina program oberoende av varandra. De behöver för att förstå normal beteendet för programmet. Åtgärder vidtas om programmet inte fungerar inte som förväntat under en DDoS-attack.

#### <a name="ddos-attacks-orchestration"></a>DDoS-attacker orchestration

Det är en bra idé att testa din antaganden om hur dina tjänster svara på en attack innan det sker genom att utföra regelbundna simulering. Verifiera att tjänsterna eller programmen fortsätter att fungera som förväntat och det inte finns några avbrott slutanvändarens upplevelse under testningen. Identifiera brister från både teknik och processen synvinkel och infoga i strategi för DDoS-svar. En allmän rekommendation är att utföra dessa tester i fristående miljöer eller vid låg belastning för att minimera inverkan på produktionsmiljön.

Vi samarbetar med [BreakingPoint moln](https://www.ixiacom.com/products/breakingpoint-cloud) för att skapa ett gränssnitt där Azure-kunder kan skapa trafik mot DDoS-skydd aktiverat offentliga slutpunkter för simulering. [Brytpunkt molnet](https://www.ixiacom.com/products/breakingpoint-cloud) simuleringen kan du:

- Kontrollera hur Microsoft Azure DDoS-skydd skyddar dina Azure-resurser från DDoS-attacker

- Optimera incidenter processen under tiden för DDoS-attack

- Dokumentet DDoS-kompatibilitet

- Träna din network security team

Cybersecurity är en outtröttliga slaget kräver konstant innovation i skydd. Azures DDoS-Standard skydd är en avancerade erbjuder avancerade erbjudande för kunder med en effektiv lösning för att minska allt mer komplexa DDoS-attacker.

## <a name="components-of-a-ddos-response-strategy"></a>Komponenter i en strategi för DDoS-svar

I de flesta fall när en DDoS-attack är riktad mot dina Azure-resurser finns minimala åtgärder från en slutanvändare synvinkel. Fortfarande säkerställer införliva DDoS lösning som en del av organisationens incidenter strategi minimal påverkan på kontinuitet för företag.

### <a name="microsoft-threat-intelligence"></a>Microsoft hotinformation

Microsoft har en omfattande hot intelligence-nätverk som använder kollektiva kunskap om en utökad säkerhet-community som stöder Microsoft online services, Microsoft-partner och relationer inom gruppen Internet security. Som en kritisk infrastruktur provider Microsoft tar emot tidiga varningar om hot, tar det grundläggande hotinformation lärt dig från andra Microsoft online services och från Microsofts globala kunder. Alla Microsofts hotinformation ingår tillbaka till sina Azure DDoS-skydd-produkter.

Utöver detta utför Microsofts Digital Crimes Unit (DCU) stötande strategier mot botnät och en gemensam källa med kommandot och kontroll för DDoS-attacker.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Utföra en riskbedömning av Azure-resurser

Det är viktigt att förstå vilka risken från en DDoS-attack med jämna mellanrum. Kunder bör regelbundet fråga själva: vilka nya allmänt tillgängliga Azure-resurser behöver skydd? Är det enda fel i tjänsten? Hur kan tjänster vara isolerad för att begränsa effekten av en attack när fortfarande gör tillgängliga tjänster för giltiga kunder? Finns det virtuella nätverk där DDoS-skydd Standard ska aktiveras, inte men? Är Mina tjänster aktiva med redundans över flera regioner?

### <a name="customer-ddos-response-team"></a>Kunden DDoS svarsgrupp

Att skapa en DDoS svarsgrupp är ett viktigt steg i att säkerställa effektiv och snabba svar på en attack. Kunderna måste identifiera olika kontakter i organisationen som ska övervaka planering och genomförande. DDoS svarsgrupp ska ha goda kunskaper om tjänsten Azure DDoS-skydd Standard och bör vara lgh i identifierar & begränsande angreppet att koordinera med olika interna och externa kunder, inklusive Microsoft support-teamet efter behov.

Microsoft rekommenderar införliva DDoS svarsgrupp planerings- och simulering övningarna, inklusive testning, som en normal del av tjänstetillgänglighet och affärskontinuitet planera skala. 

### <a name="during-an-attack"></a>Under en attack

Azure DDoS-skydd Standard kommer att identifiera och åtgärda DDoS-attacker utan användarens ingripande. Om du vill få information när det finns en aktiv lösning för en skyddad offentliga IP-adress, kan du [konfigurera en varning](../virtual-network/ddos-protection-manage-portal.md) på måttet ”under DDoS attacker eller inte”. Du kan ytterligare granskning och skapa aviseringar efter behov för DDoS-mätvärden att förstå skalan för attack, trafik som förloras osv.

#### <a name="when-to-contact-microsoft-support"></a>När du kontaktar Microsoft support

- Om under en DDoS-attack prestanda hos den skyddade resursen försämras kraftigt eller resursen är inte tillgänglig.

- Om du tror att fungerar tjänsten DDoS-skydd inte som förväntat. DDoS-skydd-tjänsten kommer bara att initiera minskning om den nedan villkor uppfylls:

    - Måttet ' princip för att utlösa DDoS-lösning (SYN TCP/TCP/UDP) är lägre än den trafik som tas emot på den skyddade offentliga IP-resursen.

- Om du vet ska du ha en planerad viral händelse som leder till signifikant ökning av nätverkstrafiken.

- Om en aktör har riskerar att starta en DDoS-attack mot dina resurser.

Skapa en allvarlighetsgrad A för affärskritiska påverka problem, [stöder biljett](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Bokför attack steg

Det är alltid en bra strategi för att göra en postmortem efter en attack och justera strategi för DDoS-svar efter behov. Saker att tänka på:

- Var det eventuella avbrott i tjänsten eller slutanvändarens upplevelse på grund av bristande skalbar arkitektur?

- Haft mest vilka program eller tjänster?

- Hur effektiva var strategi för DDoS-svar och hur den förbättras ytterligare?

Om du misstänker att du är under en DDoS-attack eskalera via din vanliga kanaler för Azure-supporten.

## <a name="ddos-protection-reference-architectures"></a>DDoS-skydd referensarkitekturer

DDoS-skydd Standard är utformad [för tjänster som har distribuerats i ett virtuellt nätverk.](../virtual-network/virtual-network-for-azure-services.md) Andra tjänster standard grundläggande DDoS-skydd gäller för. De specifika referensarkitekturer som beskrivs nedan ordnas efter scenarier med arkitektur mönster grupperas tillsammans.

### <a name="virtual-machine-windowslinux-workloads"></a>Arbetsbelastningar på virtuella datorer (Windows-/ Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Programmet körs på Läs in den belastningsutjämnade virtuella datorer

Denna referensarkitektur innehåller en uppsättning beprövade metoder för att köra flera virtuella Windows-datorer (VM) i en skaluppsättning bakom en belastningsutjämnare för att förbättra tillgängligheten och skalbarheten. Den här arkitekturen kan användas för tillståndslösa arbetsbelastningar, till exempel en webbserver.

![](media/azure-ddos-best-practices/image9.png)

En arbetsbelastning distribueras över flera VM-instanser i den här arkitekturen. Det finns en offentlig IP-adress och Internet-trafiken distribueras till de virtuella datorerna med hjälp av en belastningsutjämnare. DDoS-skydd Standard är aktiverat på belastningsutjämnaren Azure (internet) som har den offentliga IP som är associerade med den virtuella nätverk.

Belastningsutjämnaren distribuerar inkommande Internet-förfrågningar till VM-instanser. Skalningsuppsättningar kan antalet VMs skalas in eller ut manuellt eller automatiskt baserat på fördefinierade regler. Detta är viktigt om resursen ligger under DDoS-attacker. Referera till den här [artikel](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm), mer information om denna Referensarkitektur.

#### <a name="applications-running-on-windows-n-tier"></a>Program som körs på Windows N-nivå

Det finns många sätt att implementera en arkitektur på N-nivå. Diagrammet visar ett typiskt webbprogram med 3 nivåer. Den här arkitekturen bygger på [kör Utjämning av nätverksbelastning VMs för skalbarhet och tillgänglighet](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webb- och företagsnivåer använder belastningsutjämnade virtuella datorer.

![](media/azure-ddos-best-practices/image10.png)

I ovanstående arkitekturen aktiveras DDoS-skydd Standard på det virtuella nätverket. Alla offentliga IP-adresser i det virtuella nätverket får nivå 3/Layer4 DDoS-skydd. För Layer 7 skydd ska Programgateway i Brandvägg SKU distribueras. Referera till [detta](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier) artikel, mer information om denna Referensarkitektur.

#### <a name="paas-web-application"></a>PaaS-webbprogram

Denna Referensarkitektur visar kör ett program i Azure App Service i en region. Den här arkitekturen visar anges av beprövade metoder för ett webbprogram som använder [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) och [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Vänteläge region ställs in för redundans-scenarier.

![](media/azure-ddos-best-practices/image11.png)

Traffic Manager dirigerar inkommande förfrågningar till Application Gateway i någon av regionerna. Under normal drift dirigerar begäran till programmet gatewayen på den aktiva regionen. Om den regionen blir otillgänglig, Traffic Manager kan inte över Programgateway i vänteläge region.

All trafik från Internet till webbprogrammet dirigeras till den [Programgateway offentliga IP-adressen](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) via Traffic Manager. I det här scenariot app service (webbprogram) själva är inte direkt externt riktade mot och skyddas av Application Gateway. Det rekommenderas att konfigurera programmet Gateway Brandvägg SKU (förhindra läge) att skydda mot attacker Layer 7 (HTTP/HTTPS/Web Socket). Dessutom Web apps är konfigurerade att [ta emot trafik från Programgatewayen](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-adress.

Referera till [detta](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) artikel, mer information om denna Referensarkitektur.

### <a name="mitigation-for-non-web-paas-services"></a>Lösning för icke-PaaS-webbtjänster

#### <a name="hdinsight-on-azure"></a>HDInsight på Azure

Denna Referensarkitektur visar hur du konfigurerar DDoS-skydd Std. för [HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/) på Azure. Du måste se till att HDInsight-kluster som är kopplad till ett virtuellt nätverk och DDoS-skydd är aktiverat på det virtuella nätverket.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

I den här arkitekturen vidarebefordras trafiken till HDInsight-kluster från internet till offentliga IP-adresser som är associerade med belastningsutjämnaren HDInsight gateway. Gateway-belastningsutjämnaren skickar sedan trafiken till huvudnoderna eller arbetsnoderna direkt. Angivna DDoS-skydd Standard är aktiverat på det virtuella nätverket i HDInsight, alla offentliga IP-adresser i det virtuella nätverket får nivå 3/Layer4 DDoS-skydd. Ovanstående Referensarkitektur kan kombineras med N-nivå/flera-Region referens arkitekturerna.

Mer information om denna Referensarkitektur avser [utöka Azure HDInsight med hjälp av ett Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentation.

### <a name="azure-api-management"></a>Azure API management

Denna Referensarkitektur skyddar offentlig slutpunkt på den [API management](../api-management/api-management-key-concepts.md) resurspublicerings API: er för organisationens externa kunder. APIM ska distribueras i ett externt virtuellt nätverk för att aktivera DDoS-skydd.

![](media/azure-ddos-best-practices/image15.png)

API Management gateway & developer-portalen genom att konfigurera den externt virtuellt nätverk är tillgängliga från internet via en offentlig belastningsutjämnare. I den här arkitekturen aktiveras DDoS-skydd Standard på APIM virtuellt nätverk externt virtuellt nätverk. Trafik dirigeras till offentliga IP-adressen för APIM som är skyddade mot nätverksattacker nivå 3/Layer4 från internet. Du kan konfigurera en Programgateway i Brandvägg läge för att skydda mot Layer 7 HTTP/HTTPs-attacker.

Lista över ytterligare tjänster som distribueras i ett virtuellt nätverk och kan konfigureras för DDoS-skydd Standard upprätthålls [här](../virtual-network/virtual-network-for-azure-services.md). DDoS-skydd Standard stöder endast Azure Resource Manager-resurser. *Inmatat programmet Service miljö (ASE) distribution i ett VNET med en offentlig IP-adress stöds inte internt.* Information om hur du skyddar ASE miljö finns i det här avsnittet.

## <a name="next-steps"></a>Nästa steg

* [Azure produktsidan med DDoS-skydd](https://azure.microsoft.com/services/ddos-protection/)

* [Blogg för Azure DDoS-skydd](http://aka.ms/ddosblog)

* [Dokumentation för Azure DDoS-skydd ](../virtual-network/ddos-protection-overview.md)
