---
title: Azure DDoS-skydd metodtips och referera till arkitekturer | Microsoft Docs
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
ms.openlocfilehash: 042dd4876a63e5881e67456b449570b01cb967a5
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Azure DDoS-skydd: Metodtips och referera till arkitekturer

Den här artikeln används för IT-beslutsfattare och säkerhet personal. Den förväntar sig att du är bekant med Azure, nätverk och säkerhet.

Utformning för distribuerade DOS-attacker (DDoS) kräver återhämtning planering och utformning av olika feltillstånd. Den här artikeln innehåller metodtips för att utforma program i Azure för återhämtning mot DDoS-attacker.

## <a name="types-of-attacks"></a>Typer av attacker

DDoS är en typ av angrepp som försöker få slut på resurser. Målet är att påverka programmets tillgänglighet och dess förmåga att hantera legitima begäranden. Attacker blir mer förfinade och större i storlek och effekt. DDoS-attacker kan vara mål för valfri slutpunkt som är offentligt tillgänglig via internet.

Azure tillhandahåller kontinuerlig skydd mot DDoS-attacker. Det här skyddet är integrerat i Azure-plattformen som standard och utan extra kostnad. 

Förutom grundläggande DDoS-skydd i plattformen, [Azure DDoS-skydd Standard](https://azure.microsoft.com/services/ddos-protection/) ger avancerade funktioner för DDoS-minskning mot nätverksattacker. Det är automatiskt justerade för att skydda specifika Azure-resurser. Skydd är enkel att aktivera under genereringen av nya virtuella nätverk. Det kan även utföras när den har skapats och kräver inga ändringar i programmet eller resursen.

![Rollen för Azure DDoS-skydd för att skydda kunder och ett virtuellt nätverk från en angripare](media/azure-ddos-best-practices/image1.png)

DDoS-attacker kan delas in i tre kategorier: Överför, protokoll och resurs.

### <a name="volumetric-attacks"></a>Överför attacker

Överför attacker är den vanligaste typen DDoS-attacker. Överför attacker är brute force-assaults som är riktade till nätverks- och lager. De försöker få slut på resurser som nätverkslänkar. 

Angreppen använder ofta flera infekterade system för att översvämma nätverket lager med till synes legitima trafik. De använder nätverk protokollen som kontrollen meddelandet ICMP (Internet Protocol), User Datagram Protocol (UDP) och Transmission Control Protocol (TCP).

De vanligaste nätverksnivån DDoS-attacker är TCP SYN överbelasta ICMP echo, UDP översvämning, DNS, och NTP förstärkning attacker. Den här typen av attacker kan användas inte bara för att stoppa tjänsten, utan även som en smokescreen för nefarious och mer riktad intrång. Ett exempel på en senaste överför attack är den [Memcached utnyttja](https://www.wired.com/story/github-ddos-memcached/) som påverkade GitHub. Angrepp riktade UDP-port 11211 och genererade 1.35 Tb/s attack volym.

### <a name="protocol-attacks"></a>Protokollet attacker

Protokollet attacker mål programprotokoll. De försöker använda alla tillgängliga resurser i infrastrukturen enheter, till exempel brandväggar, programservrar och belastningsutjämnare. Protokollet attacker använda paket som är felaktig eller innehåller protokollet avvikelser. Angreppen fungerar genom att skicka stort antal öppna begäranden att servrar och andra kommunikationsenheter besvara och vänta på svar paket. Målet försöker besvara öppna begäranden, vilket till slut medför att systemet kraschar.

De vanligaste exempel på ett protokoll-baserade DDoS-attacker är TCP SYN översvämning. I det här angreppet försöker TCP SYN begäranden upprepade överväldigande ett mål. Målet är att göra svarar inte. 2016 Dyn avbrott, förutom att angreppet programnivå bestod av TCP SYN floods den aktuella porten 53 Dyns DNS-servrar.

### <a name="resource-attacks"></a>Resurs-attacker

Resursen attacker mål Applikationsnivån. De utlöser backend-processer för att överbelasta ett system. Resurs-attacker missbruk trafik som ser ut normal men som utför processorintensiva frågor till servern. Mängden trafik som behövs för att få slut på resurser som är lägre än den andra typen av attacker. Trafik i en resurs-attack är identiska med legitima trafik, vilket gör det svårt att identifiera. De vanligaste attacker som resursen finns på HTTP/HTTPS- och DNS-tjänster.

## <a name="shared-responsibility-in-the-cloud"></a>Delat ansvar i molnet

Skydd på djupet hjälper bekämpa ökande och vilken nivå av attacker. Säkerhet är ett delat ansvar mellan kunden och Microsoft. Microsoft kallar det en [delat ansvar modellen](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Följande bild visar den här uppdelningen av ansvar:

![Ansvarsområden av kunden och Azure](media/azure-ddos-best-practices/image2.png)

Azure-kunder dra nytta av att granska bästa praxis från Microsoft och skapa globalt distribuerade program som designas och testas för fel.

## <a name="fundamental-best-practices"></a>Grundläggande bästa praxis

Följande avsnitt innehåller normativ vägledning för att skapa DDoS-flexibel tjänster i Azure.

### <a name="design-for-security"></a>Utforma för säkerhet

Se till att säkerheten är en prioritet under hela livscykeln för ett program från designen och implementeringen till distribution och användning. Program kan ha buggar som gör att en relativt låg volymen av begäranden att använda orimligt mycket resurser, vilket resulterar i ett avbrott i tjänsten. 

För att skydda en tjänst som körs på Microsoft Azure, bör du ha en god förståelse av din programarkitektur och fokusera på den [fem pelare programvara kvalitet](https://docs.microsoft.com/azure/architecture/guide/pillars).
Du bör känna till vanliga trafikvolymer modellen anslutning mellan programmet och andra program och tjänsten på de slutpunkter som exponeras för det offentliga internet.

Det är viktigt att säkerställa att ett program är flexibel att hantera en denial of service som är riktad till programmet. Säkerhet och sekretess är inbyggda i Azure-plattformen, från och med den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Av SDL adresser säkerhet i varje utvecklingsfas och säkerställer att Azure uppdateras kontinuerligt så att den blir ännu säkrare.

### <a name="design-for-scalability"></a>Design för skalbarhet

Skalbarhet är hur bra ett system kan hantera en ökad belastning. Du måste skapa dina program [skala horisontellt](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) att uppfylla behovet av en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om programmet är beroende av en enda instans av en tjänst, skapas en enskild felpunkt. Flera instanser etablering systemet mer flexibel och skalbar mer.

För [Azure App Service](../app-service/app-service-value-prop-what-is.md), Välj en [programtjänstplanen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) som erbjuder flera instanser. Konfigurera var och en av dina roller som ska användas för Azure Cloud Services [flera instanser](../cloud-services/cloud-services-choose-me.md). För [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), se till att din arkitektur för virtuell dator (VM) innehåller fler än en virtuell dator och att varje virtuell dator ska ingå i en [tillgänglighetsuppsättning](../virtual-machines/virtual-machines-windows-manage-availability.md). Vi rekommenderar att du använder [skalningsuppsättningar i virtuella](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för autoskalning funktioner.

### <a name="defense-in-depth"></a>Skydd på djupet

Tanken bakom skydd på djupet är att hantera risker med hjälp av olika defensiva strategier. Skikta säkerhetsskyddet i ett program minskar risken för ett angrepp. Vi rekommenderar att du implementera säker Designer för dina program med hjälp av de inbyggda funktionerna i Azure-plattformen.

Till exempel risken för angrepp ökar med storleken (*yta området*) för programmet. Du kan minska risken genom för att stänga ned exponerade IP-adressutrymmet vitlistning och lyssningsportar som inte behövs på belastningsutjämnare ([Azure belastningsutjämnare](../load-balancer/load-balancer-get-started-internet-portal.md) och [Azure Programgateway](../application-gateway/application-gateway-create-probe-portal.md)). [Nätverkssäkerhetsgrupper (NSG: er)](../virtual-network/virtual-networks-nsg.md) är ett annat sätt att minska risken för angrepp.
Du kan använda [tjänsten taggar](/virtual-network/security-overview.md) och [programmet säkerhetsgrupper](/virtual-network/security-overview.md) att minimera komplexitet för att skapa säkerhetsregler och konfigurera nätverkssäkerhet, som en naturlig förlängning av ett program.

Du bör distribuera Azure-tjänster i en [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) när det är möjligt. Detta gör tjänsten resurser för att kommunicera via privata IP-adresser. Azure-tjänstens trafik från ett virtuellt nätverk använder offentliga IP-adresser som käll-IP-adresser som standard. Med hjälp av [tjänstens slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) växlar trafik från tjänsten om du vill använda virtuella privata nätverksadresser som käll-IP-adresser när de försöker komma åt Azure-tjänsten från ett virtuellt nätverk.

Vi kan ofta se kundernas lokala resurser komma angripna tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure, rekommenderar vi att du minimerar risken för lokala resurser för det offentliga internet. Du kan använda skala och avancerade DDoS-skydd-funktionerna i Azure genom att distribuera din välkända offentliga enheter i Azure. Eftersom dessa enheter som är offentligt tillgänglig ofta ett mål för DDoS-attacker kan minskar placera dem i Azure inverkan på din lokala resurser.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-erbjudanden för DDoS-skydd

Azure har två DDoS Tjänsterbjudanden som ger skydd mot nätverksattacker (Layer 3 och 4): DDoS-skydd Basic och DDoS-skydd Standard. 

### <a name="ddos-protection-basic"></a>Basic för DDoS-skydd

Grundläggande skydd är integrerat i Azure som standard utan extra kostnad. Ger skydd mot vanliga nätverksnivån attacker via alltid i trafik övervaknings- och realtid minskning skala och kapacitet för globalt distribuerade Azure-nätverk. DDoS-skydd grundläggande kräver konfiguration eller programmet användarändringar. DDoS-skydd grundläggande skyddar alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.

![Mappa representation av Azure-nätverk med texten ”globala DDoS minskning förekomst” och ”inledande DDoS minskning kapacitet”](media/azure-ddos-best-practices/image3.png)

Grundläggande DDoS-skydd i Azure består av både program-och maskinvara. En plan för kontroll av programvara avgör när, var och vilken typ av trafik bör vara styrd via maskinvara apparater som analysera och ta bort attack trafik. Plan för åtkomstkontroll gör detta beslut baserat på en infrastruktur för hela DDoS-skydd *princip*. Den här principen statiskt ställs och tillämpas universellt alla Azure-kunder.

Till exempel DDoS-skydd-principen anger på vilka trafikvolym skyddet ska vara *utlöses.* (Det vill säga den klienttrafik ska vidarebefordras genom skrubbningsjobb installationer.) Principen anger sedan hur bakgrundsrensning installationer bör *minimera* angrepp.

Tjänsten Azure DDoS-skydd Basic är inriktad på infrastrukturen och skydd av Azure-plattformen. Det minskar trafik när den överskrider en hastighet som är viktiga att det kan påverka flera kunder i en flerklientsmiljö. Den ger inte aviseringar eller per kund anpassade principer.

### <a name="ddos-protection-standard"></a>Standard för DDoS-skydd

Standard protection innehåller funktioner för DDoS-lösning. Det är automatiskt justerade för att skydda specifika Azure-resurser i ett virtuellt nätverk. Skydd är enkel att aktivera på alla nya eller befintliga virtuella nätverk och det krävs inga ändringar i programmet eller resursen. Den har flera fördelar jämfört med grundläggande tjänsten, inklusive loggning, varningar och telemetri. I följande avsnitt beskrivs viktiga funktioner i tjänsten Azure DDoS-skydd Standard.

#### <a name="adaptive-real-time-tuning"></a>Anpassningsbar realtid justera

Tjänsten Azure DDoS-skydd Basic hjälper kunder och förhindra påverkan till andra kunder. Om exempelvis en tjänst har etablerats för en typisk volym av legitima inkommande trafik som är mindre än den *utlösaren hastighet* för den infrastruktur hela DDoS-skydd för en DDoS-attack på resurser som kunden kan gå oupptäckta. Generellt komplexitet senaste attacker (till exempel flera vector DDoS) och programspecifika beteenden för klienter kräver per kund, anpassade skyddsprinciper. Tjänsten genomför den här anpassningen genom att använda två insikter:

- Automatisk inlärning av (per-IP) per kund trafikmönster för Layer 3 och 4.

- Minimera falska positiva identifieringar som överväger att skala Azure tillåter att det skulle kunna ta emot en stor mängd trafik.

![Diagram över hur DDoS-skydd Standard fungerar med ”Principgenerering” inringad](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS-skydd telemetri, övervakning och avisering

DDoS-skydd Standard exponerar omfattande telemetri via [Azure-Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) under en DDoS-attack. Du kan konfigurera aviseringar för någon av de Azure-Monitor-mätvärden som använder DDoS-skydd. Du kan integrera loggning med Splunk (Azure Event Hubs) och Azure logganalys Azure Storage för avancerad analys via Azure-Monitor diagnostik-gränssnittet.

##### <a name="ddos-mitigation-policies"></a>Principer för DDoS-lösning

Välj i Azure-portalen **övervakaren** > **mått**. I den **mått** fönstret Välj resursgruppen, Välj en resurstyp i **offentliga IP-adressen**, och välj din Azure offentliga IP-adress. DDoS mått visas i den **tillgängliga mått** fönstret.

DDoS-skydd Standard gäller tre automatiskt justerade minskning principer (TCP SYN, TCP och UDP) för varje offentlig IP-adress för den skyddade resursen i det virtuella nätverket som har DDoS aktiverat. Du kan visa tröskelvärdena som principen genom att välja mått **inkommande paket som utlöser DDoS minskning**.

![Tillgängliga mått och mått diagram](media/azure-ddos-best-practices/image7.png)

Tröskelvärden för principen är automatiskt konfigurerade via profilering av machine learning-baserat nätverk trafik. DDoS minskning inträffar för en IP-adress angripet bara när principen tröskelvärde har överskridits.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Mått för en IP-adress under DDoS-attack

Om den offentliga IP-adressen är under attacker, värdet för måttet **Under DDoS-attacker eller inte** ändras till 1 som DDoS-skydd utför lösning för attack trafiken.

![”Under DDoS attacker eller inte” mått och diagram](media/azure-ddos-best-practices/image8.png)

Vi rekommenderar att du konfigurerar en avisering på det här måttet. Du får sedan ett meddelande när det finns en aktiv DDoS minskning utförs på din offentliga IP-adress.

Mer information finns i [hantera Azure DDoS-skydd Standard med hjälp av Azure portal](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Brandvägg för webbaserade program för resurs-attacker

Specifika för resursen attacker på programnivån, bör du konfigurera en brandvägg för webbaserade program (Brandvägg) för att säkra webbprogram. En Brandvägg kontrollerar om inkommande webbtrafik om du vill blockera SQL injektioner webbplatser skript, DDoS och andra lager 7-attacker. Azure tillhandahåller [Brandvägg som en funktion för Programgateway](../application-gateway/application-gateway-web-application-firewall-overview.md) för centraliserad skydd av dina webbapplikationer från vanliga kryphål och säkerhetsproblem. Det finns andra Brandvägg erbjudanden från Azure-partners som kan vara lämpligt för dina behov via den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Även web application brandväggar är sårbar för angrepp med överför och state uttömning. Vi rekommenderar starkt att aktivera DDoS-skydd Standard på det virtuella nätverket Brandvägg för att skydda mot överför och protokollet attacker. Mer information finns i [DDoS-skydd referensarkitekturer](#ddos-protection-reference-architectures) avsnitt.

### <a name="protection-planning"></a>Planera för skydd

Planering och förberedelser är viktigt att förstå hur en systemet utför under en DDoS-attack. Designa en incidenthantering åtgärdsplanen är del av denna åtgärd.

Om du har DDoS-skydd Standard, se till att den är aktiverad på det virtuella nätverket mot internet-slutpunkter. Konfigurera aviseringar för DDoS hjälper dig att se hela tiden för alla potentiella attacker på din infrastruktur. 

Du bör övervaka dina program oberoende av varandra. Förstå normal beteendet för ett program. Förbereda för att fungera om programmet inte fungerar inte som förväntat under en DDoS-attack.

#### <a name="testing-through-simulations"></a>Testa via simulering

Det är en bra idé att testa din antaganden om hur dina tjänster kommer att besvara ett angrepp genom att utföra regelbundna simulering. Verifiera att tjänsterna eller programmen fortsätter att fungera som förväntat och det inte finns några avbrott användarupplevelsen under testningen. Identifiera brister från både teknik- och processen synvinkel och lägga till dem i strategi för DDoS-svar. Vi rekommenderar att du utför dessa tester i mellanlagringsmiljöer eller vid låg belastning för att minimera inverkan på produktionsmiljön.

Vi samarbetar med [BreakingPoint moln](https://www.ixiacom.com/products/breakingpoint-cloud) att skapa ett gränssnitt där Azure-kunder kan skapa trafik mot DDoS-skydd-aktiverade offentliga slutpunkter för simulering. Du kan använda den [BreakingPoint moln](https://www.ixiacom.com/products/breakingpoint-cloud) simuleringen till:

- Validera hur Azure DDoS-skydd skyddar dina Azure-resurser från DDoS-attacker.

- Optimera incidenter processen under tiden för DDoS-attacker.

- Dokumentera DDoS-kompatibilitet.

- Träna din network security team.

Cybersecurity kräver konstant innovation i skydd. Azure DDoS Standard-skydd är en tillstånd-med den senaste erbjudande med en effektiv lösning för att minska allt mer komplexa DDoS-attacker.

## <a name="components-of-a-ddos-response-strategy"></a>Komponenter i en strategi för DDoS-svar

En DDoS-attack som riktas mot Azure-resurser vanligtvis kräver minimala åtgärder från en användare synvinkel. Fortfarande införliva DDoS lösning som en del av en strategi för incidenter hjälper till att minimera påverkan på kontinuitet för företag.

### <a name="microsoft-threat-intelligence"></a>Microsoft hotinformation

Microsoft har ett omfattande hot intelligence-nätverk. Det här nätverket använder kollektiva kunskap om en utökad säkerhet-community som stöder Microsoft online services, Microsoft-partner och relationer inom gruppen internet security. 

Microsoft tar emot tidiga varningar om hot som en kritisk infrastruktur-provider. Microsoft samlar in hotinformation från dess onlinetjänster och dess globala kundbas. Microsoft inkluderar alla den här hotinformation tillbaka i Azure DDoS-skydd-produkter.

Microsoft Digital Crimes Unit (DCU) utför också stötande strategier mot botnät. Botnät är en gemensam källa med kommandot och kontroll för DDoS-attacker.

### <a name="risk-evaluation-of-your-azure-resources"></a>Riskbedömning av Azure-resurser

Det är viktigt att förstå vilka risken från en DDoS-attack med jämna mellanrum. Regelbundet fråga dig själv: 
- Vilka nya allmänt tillgängliga Azure-resurser behöver skydd?

- Finns det en enskild felpunkt i tjänsten? 

- Hur kan tjänster vara isolerad för att begränsa effekten av en attack när fortfarande gör tillgängliga tjänster för giltiga kunder?

- Finns det virtuella nätverk där DDoS-skydd Standard ska aktiveras, men inte? 

- Är Mina tjänster aktiva med redundans över flera regioner?

### <a name="customer-ddos-response-team"></a>Kunden DDoS svarsgrupp

Att skapa en DDoS svarsgrupp är ett viktigt steg i att svara på en attack snabbt och effektivt sätt. Identifiera kontakter i organisationen som ska övervaka planering och genomförande. DDoS-svar teamet bör grundligt förstå tjänsten Azure DDoS-skydd Standard. Kontrollera att gruppen kan identifiera och åtgärda ett angrepp genom samordning med interna och externa kunder, inklusive Microsoft support-teamet.

För arbetsgruppen DDoS svar rekommenderar vi att du använder simuleringen övningarna som en normal del av tjänstetillgänglighet och affärskontinuitet planering. De här övningarna bör innehålla skalningstester.

### <a name="alerts-during-an-attack"></a>Aviseringar under en attack

Azure DDoS-skydd Standard identifierar och minskar DDoS-attacker utan användarens ingripande. Om du vill få information när det finns en aktiv lösning för en skyddad offentliga IP-adress, kan du [konfigurera en varning](../virtual-network/ddos-protection-manage-portal.md) på måttet **Under DDoS-attacker eller inte**. Du kan välja att skapa aviseringar för DDoS-mätvärden att förstå skalan för angrepp, trafik som tas bort och annan information.

#### <a name="when-to-contact-microsoft-support"></a>När du kontaktar Microsoft support

- Du upptäcker att prestandan hos den skyddade resursen är kraftigt försämrad eller resursen är inte tillgänglig under en DDoS-attack.

- Du tror att tjänsten DDoS-skydd inte fungerar som förväntat. 

  DDoS-skydd-tjänsten startar minskning endast om måttet **princip för att utlösa DDoS-lösning (SYN TCP/TCP/UDP)** är lägre än den trafik som tas emot på den skyddade offentliga IP-resursen.

- Du planerar en viral händelse som ökar avsevärt trafik på nätverket.

- En aktör har riskerar att starta en DDoS-attack mot dina resurser.

För attacker som kan påverka affärskritiska, skapa en allvarlighetsgrad A [stöder biljett](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Efter attack steg

Det är alltid en bra strategi för att göra en postmortem efter en attack och justera strategi för DDoS-svar efter behov. Saker att tänka på:

- Var det eventuella avbrott i tjänsten eller användare får på grund av bristande skalbar arkitektur?

- Haft mest vilka program eller tjänster?

- Hur effektiva var strategi för DDoS-svar och hur kan den förbättras?

Om du misstänker att du ligger under en DDoS-attack, eskalera via din vanliga kanaler för Azure-supporten.

## <a name="ddos-protection-reference-architectures"></a>DDoS-skydd referensarkitekturer

DDoS-skydd Standard är utformad [för tjänster som har distribuerats i ett virtuellt nätverk](../virtual-network/virtual-network-for-azure-services.md). DDoS-skydd grundläggande standardtjänsten gäller för andra tjänster. Följande referensarkitekturer ordnas efter scenarier med arkitektur mönster grupperas tillsammans.

### <a name="virtual-machine-windowslinux-workloads"></a>Arbetsbelastningar på virtuella datorer (Windows-/ Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Program som körs på belastningsutjämnade virtuella datorer

Denna Referensarkitektur visar en uppsättning beprövade metoder för att köra flera virtuella Windows-datorer i skaluppsättningen bakom en belastningsutjämnare för bättre tillgänglighet och skalbarhet. Den här arkitekturen kan användas för tillståndslösa arbetsbelastningar, till exempel en webbserver.

![Diagram över Referensarkitektur för ett program som körs på belastningsutjämnade virtuella datorer](media/azure-ddos-best-practices/image9.png)

En arbetsbelastning distribueras över flera VM-instanser i den här arkitekturen. Det finns en offentlig IP-adress och internet-trafiken fördelas till virtuella datorer via en belastningsutjämnare. DDoS-skydd Standard är aktiverat på belastningsutjämnaren Azure (internet) som har den offentliga IP som är associerade med den virtuella nätverk.

Belastningsutjämnaren distribuerar inkommande internet-förfrågningar till VM-instanser. Skaluppsättningar för den virtuella datorn kan antalet VMs skalas in eller ut manuellt eller automatiskt baserat på fördefinierade regler. Detta är viktigt om resursen ligger under DDoS-attacker. Mer information om denna Referensarkitektur finns [i den här artikeln](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Program som körs på Windows N-nivå

Det finns många sätt att implementera en arkitektur på N-nivå. I följande diagram visas en typisk trelagers-webbprogram. Den här arkitekturen bygger på artikeln [kör Utjämning av nätverksbelastning VMs för skalbarhet och tillgänglighet](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webb- och företagsnivåer använder belastningsutjämnade virtuella datorer.

![Diagram över Referensarkitektur för ett program som körs på Windows N-nivå](media/azure-ddos-best-practices/image10.png)

I den här arkitekturen aktiveras DDoS-skydd Standard på det virtuella nätverket. Alla offentliga IP-adresser i det virtuella nätverket hämta DDoS-skydd för Layer 3 och 4. Distribuera Programgateway i Brandvägg SKU för Layer 7 skydd. Mer information om denna Referensarkitektur finns [i den här artikeln](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS-webbprogram

Denna Referensarkitektur visar kör ett program i Azure App Service i en region. Den här arkitekturen visar en uppsättning beprövade metoder för ett webbprogram som använder [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) och [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
En vänteläge region ställs in för redundans-scenarier.

![Diagram över Referensarkitektur för ett PaaS-webbprogram](media/azure-ddos-best-practices/image11.png)

Azure Traffic Manager dirigerar inkommande förfrågningar till Application Gateway i någon av regionerna. Under normal drift dirigerar begäran till Application Gateway i det aktiva området. Om den regionen blir otillgänglig, Traffic Manager kan inte över Programgateway i vänteläge region.

All trafik från internet till webbprogrammet dirigeras till den [Programgateway offentliga IP-adressen](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) via Traffic Manager. I det här scenariot app service (webbprogram) själva är inte direkt externt riktade mot och skyddas av Application Gateway. 

Vi rekommenderar att du konfigurerar programmet Gateway Brandvägg SKU (förhindra läge) för att skydda mot angrepp på lager 7 (WebSocket-HTTP/HTTPS). Dessutom webbprogram har konfigurerats för att [accepterar endast trafik från Programgatewayen](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-adress.

Mer information om denna Referensarkitektur finns [i den här artikeln](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Lösning för icke-PaaS-webbtjänster

#### <a name="hdinsight-on-azure"></a>HDInsight på Azure

Denna Referensarkitektur visar hur du konfigurerar DDoS-skydd Standard för en [Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/). Se till att HDInsight-klustret är länkat till ett virtuellt nätverk och att DDoS-skydd är aktiverat på det virtuella nätverket.

![”HDInsight” och ”inställningar” rutor med inställningarna för virtuella nätverk](media/azure-ddos-best-practices/image12.png)

![Markering för att aktivera DDoS-skydd](media/azure-ddos-best-practices/image13.png)

I den här arkitekturen vidarebefordras trafik till HDInsight-kluster från internet till offentliga IP-adresser som är associerade med belastningsutjämnaren HDInsight gateway. Gateway-belastningsutjämnaren skickar sedan trafiken till huvudnoderna eller arbetsnoderna direkt. DDoS-skydd Standard är aktiverad på det virtuella nätverket i HDInsight, hämta alla offentliga IP-adresser i det virtuella nätverket DDoS-skydd för Layer 3 och 4. Den här referensen för arkitekturen kan kombineras med N-nivå och referensarkitekturer för flera regioner.

Mer information om den här referensen för arkitekturen finns i [utöka Azure HDInsight med hjälp av ett Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentation.

### <a name="azure-api-management"></a>Azure API Management

Denna Referensarkitektur skyddar offentlig slutpunkt på den [Azure API Management](../api-management/api-management-key-concepts.md) resurspublicerings API: er för kunder utanför organisationen. Distribuera API-hantering i ett externt virtuellt nätverk för att aktivera DDoS-skydd.

![Diagram över Referensarkitektur för API Management](media/azure-ddos-best-practices/image15.png)

API Management gateway och developer portal är tillgänglig från internet via en offentlig belastningsutjämnare när du konfigurerar externt virtuellt nätverk. I den här arkitekturen är DDoS-skydd Standard aktiverat på det externa virtuella nätverket för API-hantering. Trafik dirigeras från internet till offentliga IP-adressen för API-hantering som är skyddade mot nätverksattacker på nivå 3 och 4. Du kan konfigurera Programgateway i Brandvägg läge för att skydda mot Layer 7 HTTP/HTTPS-attacker.

En lista över ytterligare tjänster som distribueras i ett virtuellt nätverk och kan konfigureras för DDoS-skydd Standard finns [i den här artikeln](../virtual-network/virtual-network-for-azure-services.md). DDoS-skydd Standard stöder bara Azure Resource Manager-resurser. 

> [!NOTE]
> Inmatat distribution av Apptjänst-miljö för PowerApps i ett virtuellt nätverk med en offentlig IP-adress stöds inte internt. Mer information om hur du skyddar Apptjänst-miljö finns i det här avsnittet.

## <a name="next-steps"></a>Nästa steg

* [Azure produktsidan för DDoS-skydd](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS-skydd-blogg](http://aka.ms/ddosblog)

* [Dokumentation för Azure DDoS-skydd](../virtual-network/ddos-protection-overview.md)
