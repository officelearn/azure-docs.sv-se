---
title: Designa elastiska lösningar med Azure DDoS Protection
description: Läs mer om hur du kan använda loggningsdata för att få djup insikt om ditt program.
services: security
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 8d3fc809999508bf3d49c3765c90017e89e80fa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624044"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection - Designa elastiska lösningar

Denna artikel är för IT beslutsfattare och säkerhetspersonal. Det förväntar sig att du är bekant med Azure, nätverk och säkerhet.
DDoS är en typ av attack som försöker uttömma programresurser. Målet är att påverka programmets tillgänglighet och dess förmåga att hantera legitima förfrågningar. Attacker blir mer sofistikerade och större i storlek och effekt. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet. Design för ddos-återhämtning (Distributed Denial of Service) kräver planering och design för en mängd olika fellägen. Azure ger kontinuerligt skydd mot DDoS-attacker. Det här skyddet är integrerat i Azure-plattformen som standard och utan extra kostnad.

Förutom det grundläggande DDoS-skyddet i plattformen tillhandahåller [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) avancerade DDoS-begränsningsfunktioner mot nätverksattacker. Den är automatiskt inställd för att skydda dina specifika Azure-resurser. Skydd är enkelt att aktivera när nya virtuella nätverk skapas. Det kan också göras när du har skapat och kräver inga program- eller resursändringar.

![Rollen för Azure DDoS Protection för att skydda kunder och ett virtuellt nätverk från en angripare](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Grundläggande bästa praxis

Följande avsnitt ger beskrivande vägledning för att skapa DDoS-elastiska tjänster på Azure.

### <a name="design-for-security"></a>Design för säkerhet

Se till att säkerhet är en prioritet under hela livscykeln för ett program, från design och implementering till distribution och åtgärder. Program kan ha buggar som tillåter en relativt låg mängd begäranden att använda en orimlig mängd resurser, vilket resulterar i ett avbrott i tjänsten. 

För att skydda en tjänst som körs på Microsoft Azure bör du ha en god förståelse för din programarkitektur och fokusera på de [fem pelarna i programvarukvaliteten](/azure/architecture/guide/pillars).
Du bör känna till vanliga trafikvolymer, anslutningsmodellen mellan programmet och andra program och tjänstslutpunkterna som exponeras för det offentliga internet.

Att se till att ett program är tillräckligt flexibelt för att hantera en överbelastning som är inriktad på själva programmet är viktigast. Säkerhet och sekretess är inbyggda i Azure-plattformen, som börjar med [Livscykeln för säkerhetsutveckling (SDL).](https://www.microsoft.com/sdl/default.aspx) SDL åtgärdar säkerhet i varje utvecklingsfas och säkerställer att Azure uppdateras kontinuerligt för att göra det ännu säkrare.

### <a name="design-for-scalability"></a>Design för skalbarhet

Skalbarhet är hur väl ett system kan hantera ökad belastning. Utforma dina program så att de [kan skalas horisontellt](/azure/architecture/guide/design-principles/scale-out) för att möta efterfrågan på en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om ditt program är beroende av en enda instans av en tjänst skapas en enskild felpunkt. Etablering av flera instanser gör ditt system mer flexibelt och skalbart.

För [Azure App Service](/azure/app-service/app-service-value-prop-what-is)väljer du en App [Service-plan](/azure/app-service/overview-hosting-plans) som erbjuder flera instanser. Konfigurera var och en av dina roller för Azure Cloud Services så att de använder [flera instanser](/azure/cloud-services/cloud-services-choose-me). För [Virtuella Azure-datorer](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ska du se till att arkitekturen virtuell dator (VM) innehåller mer än en virtuell dator och att varje virtuell dator ingår i en [tillgänglighetsuppsättning](/azure/virtual-machines/virtual-machines-windows-manage-availability). Vi rekommenderar att du använder [skaluppsättningar](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) för virtuella datorer för automatisk skalning.

### <a name="defense-in-depth"></a>Skydd på djupet

Tanken bakom försvaret på djupet är att hantera risker genom att använda olika defensiva strategier. Skiktning säkerhetsförsvar i ett program minskar risken för en lyckad attack. Vi rekommenderar att du implementerar säkra designer för dina program med hjälp av de inbyggda funktionerna i Azure-plattformen.

Till exempel ökar risken för angrepp med storleken *(ytan)* för applikationen. Du kan minska ytan genom att använda vitlistning för att stänga av det exponerade IP-adressutrymmet och lyssningsportarna som inte behövs på belastningsutjämnare ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) och [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)). [Nätverkssäkerhetsgrupper (NSG)](/azure/virtual-network/security-overview) är ett annat sätt att minska angreppsytan.
Du kan använda [tjänsttaggar](/azure/virtual-network/security-overview#service-tags) och [programsäkerhetsgrupper](/azure/virtual-network/security-overview#application-security-groups) för att minimera komplexiteten för att skapa säkerhetsregler och konfigurera nätverkssäkerhet, som en naturlig förlängning av ett programs struktur.

Du bör distribuera Azure-tjänster i ett [virtuellt nätverk](/azure/virtual-network/virtual-networks-overview) när det är möjligt. Med den här metoden kan tjänstresurser kommunicera via privata IP-adresser. Azure-tjänsttrafik från ett virtuellt nätverk använder offentliga IP-adresser som käll-IP-adresser som standard. Med hjälp av [tjänstslutpunkter](/azure/virtual-network/virtual-network-service-endpoints-overview) växlar tjänsttrafik för att använda privata virtuella nätverksadresser som käll-IP-adresser när de använder Azure-tjänsten från ett virtuellt nätverk.

Vi ser ofta kunders lokala resurser attackeras tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure rekommenderar vi att du minimerar exponeringen av lokala resurser till det offentliga internet. Du kan använda azures skalnings- och avancerade DDoS-skyddsfunktioner genom att distribuera dina välkända offentliga entiteter i Azure. Eftersom dessa offentligt tillgängliga entiteter ofta är ett mål för DDoS-attacker minskar det påverkan på dina lokala resurser om du placerar dem i Azure.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-erbjudanden för DDoS-skydd

Azure har två DDoS-tjänsterbjudanden som ger skydd mot nätverksattacker (Layer 3 och 4): DDoS Protection Basic och DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>Grundläggande DDoS-skydd

Grundläggande skydd är integrerat i Azure som standard utan extra kostnad. Omfattningen och kapaciteten för det globalt distribuerade Azure-nätverket ger skydd mot vanliga nätverkslagerattacker genom trafikövervakning alltid och begränsning i realtid. DDoS Protection Basic kräver ingen användarkonfiguration eller programändringar. DDoS Protection Basic hjälper till att skydda alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.

![Kartrepresentation av Azure-nätverket, med texten "Global DDoS mitigation presence" och "Leading DDoS mitigation capacity"](./media/ddos-best-practices/image3.png)

Grundläggande DDoS-skydd i Azure består av både programvaru- och maskinvarukomponenter. Ett programkontrollplan bestämmer när, var och vilken typ av trafik som ska styras genom maskinvaruapparater som analyserar och tar bort attacktrafik. Kontrollplanet fattar detta beslut baserat på en infrastrukturomfattande DDoS *Protection-policy*. Den här principen är statiskt inställd och universellt tillämpad på alla Azure-kunder.

DDoS-skyddsprincipen anger till exempel vid vilken trafikvolym skyddet ska *utlösas.* (Det vill säga hyresgästens trafik bör dirigeras genom skurningsapparater.) Principen anger sedan hur skrubbningsapparaterna ska *minska* attacken.

Azure DDoS Protection Basic-tjänsten är inriktad på skydd av infrastruktur och skydd av Azure-plattformen. Det minskar trafiken när den överskrider en hastighet som är så betydande att det kan påverka flera kunder i en miljö med flera trogna. Det ger inte aviseringar eller anpassade principer per kund.

### <a name="ddos-protection-standard"></a>DDoS-skyddsstandard

Standardskydd ger förbättrade DDoS-begränsningsfunktioner. Den är automatiskt inställd för att skydda dina specifika Azure-resurser i ett virtuellt nätverk. Skydd är enkelt att aktivera på alla nya eller befintliga virtuella nätverk, och det kräver inga program- eller resursändringar. Den har flera fördelar jämfört med den grundläggande tjänsten, inklusive loggning, aviseringar och telemetri. I följande avsnitt beskrivs de viktigaste funktionerna i Azure DDoS Protection Standard-tjänsten.

#### <a name="adaptive-real-time-tuning"></a>Adaptiv justering i realtid

Azure DDoS Protection Basic-tjänsten hjälper till att skydda kunder och förhindra påverkan för andra kunder. Om till exempel en tjänst är etablerad för en typisk volym av legitim inkommande trafik som är mindre än *utlösningshastigheten* för den infrastrukturomfattande DDoS-skyddsprincipen, kan en DDoS-attack på kundens resurser gå obemärkt förbi. Mer allmänt kräver komplexiteten i de senaste attackerna (till exempel DDoS med flera vektorer) och de programspecifika beteendena för klienter per kund anpassade skyddsprinciper. Tjänsten utför den här anpassningen med hjälp av två insikter:

- Automatisk inlärning av trafikmönster per kund (per IP) för lager 3 och 4.

- Minimera falska positiva identifieringar, med tanke på att omfattningen av Azure gör det möjligt att absorbera en betydande mängd trafik.

![Diagram över hur DDoS Protection Standard fungerar, med "Policy Generation" inringad](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetri, övervakning och avisering

DDoS Protection Standard exponerar omfattande telemetri via [Azure Monitor](/azure/azure-monitor/overview) under hela en DDoS-attack. Du kan konfigurera aviseringar för något av Azure Monitor-måtten som DDoS Protection använder. Du kan integrera loggning med Splunk (Azure Event Hubs), Azure Monitor-loggar och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

##### <a name="ddos-mitigation-policies"></a>DDoS-begränsningsprinciper

I Azure-portalen väljer du **Övervaka** > **mått**. I fönstret **Mått** väljer du resursgruppen, väljer en resurstyp av **offentlig IP-adress**och väljer din offentliga Azure-IP-adress. DDoS-mått visas i fönstret **Tillgängliga mått.**

DDoS Protection Standard tillämpar tre principer för automatisk begränsning (TCP SYN, TCP och UDP) för varje offentlig IP för den skyddade resursen, i det virtuella nätverket som har DDoS aktiverat. Du kan visa principtrösklarna genom att välja **måttet Inkommande paket för att utlösa DDoS-begränsning**.

![Tillgängliga mått och mått diagram](./media/ddos-best-practices/image7.png)

Principtrösklarna konfigureras automatiskt via maskininlärningsbaserad nätverkstrafikprofilering. DDoS-begränsning sker för en IP-adress som attackeras endast när principtröskeln överskrids.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Mått för en IP-adress under DDoS-attack

Om den offentliga IP-adressen är under attack, värdet för måttet **under DDoS-attack eller inte** ändras till 1 som DDoS Protection utför begränsning på attacktrafiken.

!["Under DDoS-attack eller inte" mått och diagram](./media/ddos-best-practices/image8.png)

Vi rekommenderar att du konfigurerar en avisering om det här måttet. Du får då ett meddelande när det finns en aktiv DDoS-begränsning som utförs på din offentliga IP-adress.

Mer information finns i [Hantera Azure DDoS Protection Standard med Azure-portalen](/azure/virtual-network/ddos-protection-manage-portal).

#### <a name="web-application-firewall-for-resource-attacks"></a>Brandvägg för webbprogram för resursattacker

Specifikt för resursattacker i programlagret bör du konfigurera en brandvägg för webbprogram (WAF) för att skydda webbprogram. En WAF inspekterar inkommande webbtrafik för att blockera SQL-injektioner, skript över flera webbplatser, DDoS och andra Layer 7-attacker. Azure tillhandahåller [WAF som en funktion i Application Gateway](/azure/application-gateway/application-gateway-web-application-firewall-overview) för centraliserat skydd av dina webbprogram från vanliga kryphål och sårbarheter. Det finns andra WAF-erbjudanden tillgängliga från Azure-partner som kan vara mer lämpliga för dina behov via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Även brandväggar för webbprogram är mottagliga för volymetriska och tillståndsutmattningsattacker. Vi rekommenderar starkt att du aktiverar DDoS Protection Standard i det virtuella WAF-nätverket för att skydda mot volymetriska attacker och protokollattacker. Mer information finns i avsnittet [Referensarkitekturer för DDoS-skydd.](#ddos-protection-reference-architectures)

### <a name="protection-planning"></a>Planering av skydd

Planering och förberedelser är avgörande för att förstå hur ett system kommer att fungera under en DDoS-attack. Att utforma en insatsplan för incidenthantering är en del av detta arbete.

Om du har DDoS Protection Standard kontrollerar du att den är aktiverad i det virtuella nätverket av internetvända slutpunkter. Genom att konfigurera DDoS-aviseringar kan du ständigt titta efter eventuella attacker på infrastrukturen. 

Övervaka dina program oberoende av detta. Förstå det normala beteendet för ett program. Förbered dig på att agera om programmet inte beter sig som förväntat under en DDoS-attack.

#### <a name="testing-through-simulations"></a>Testa genom simuleringar

Det är en god praxis att testa dina antaganden om hur dina tjänster kommer att svara på en attack genom att genomföra regelbundna simuleringar. Under testningen verifierar du att dina tjänster eller program fortsätter att fungera som förväntat och att användarupplevelsen inte störs. Identifiera luckor ur både teknik- och processsynpunkt och införliva dem i DDoS-svarsstrategin. Vi rekommenderar att du utför sådana tester i mellanlagringsmiljöer eller under icke-rusningstid för att minimera påverkan på produktionsmiljön.

Vi samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) för att skapa ett gränssnitt där Azure-kunder kan generera trafik mot DDoS Protection-aktiverade offentliga slutpunkter för simuleringar. Du kan använda [BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) Cloud-simuleringen för att:

- Verifiera hur Azure DDoS Protection skyddar dina Azure-resurser från DDoS-attacker.

- Optimera din incidentsvarsprocess under DDoS-attack.

- Dokument DDoS-efterlevnad.

- Träna dina nätverkssäkerhetsteam.

Cybersäkerhet kräver ständig innovation i försvaret. Azure DDoS Standard protection är ett toppmodernt erbjudande med en effektiv lösning för att minska allt mer komplexa DDoS-attacker.

## <a name="components-of-a-ddos-response-strategy"></a>Komponenter i en DDoS-svarsstrategi

En DDoS-attack som riktar sig till Azure-resurser kräver vanligtvis minimal åtgärd från en användarsynpunkt. Att införliva DDoS-begränsning som en del av en incidenthanteringsstrategi hjälper dock till att minimera påverkan på kontinuiteten i verksamheten.

### <a name="microsoft-threat-intelligence"></a>Microsofts hotinformation

Microsoft har ett omfattande nätverk för hotinformation. Det här nätverket använder den samlade kunskapen om en utökad säkerhetsgemenskap som stöder Microsofts onlinetjänster, Microsoft-partner och relationer inom internetsäkerhetsgruppen. 

Som en kritisk infrastrukturleverantör får Microsoft tidiga varningar om hot. Microsoft samlar in hotinformation från sina onlinetjänster och från sin globala kundbas. Microsoft införlivar all denna hotinformation tillbaka i Azure DDoS Protection-produkterna.

Microsoft Digital Crimes Unit (DCU) utför också offensiva strategier mot botnät. Botnät är en vanlig källa till kommando och kontroll för DDoS-attacker.

### <a name="risk-evaluation-of-your-azure-resources"></a>Riskbedömning av dina Azure-resurser

Det är absolut nödvändigt att förstå omfattningen av din risk från en DDoS-attack på en kontinuerlig basis. Fråga dig regelbundet:

- Vilka nya offentligt tillgängliga Azure-resurser behöver skydd?

- Finns det en enda felpunkt i tjänsten? 

- Hur kan tjänster isoleras för att begränsa effekterna av en attack samtidigt som tjänster görs tillgängliga för giltiga kunder?

- Finns det virtuella nätverk där DDoS Protection Standard ska aktiveras men inte? 

- Är mina tjänster aktiva/aktiva med redundans över flera regioner?

### <a name="customer-ddos-response-team"></a>Svarsteam för kund-DDoS

Att skapa ett DDoS-svarsteam är ett viktigt steg för att svara på en attack snabbt och effektivt. Identifiera kontakter i din organisation som övervakar både planering och genomförande. Den här DDoS-svarsteamet bör noggrant förstå Azure DDoS Protection Standard-tjänsten. Se till att teamet kan identifiera och minska en attack genom att samordna med interna och externa kunder, inklusive Microsofts supportteam.

För ditt DDoS-svarsteam rekommenderar vi att du använder simuleringsövningar som en normal del av din tjänsttillgänglighet och kontinuitetsplanering. Dessa övningar bör omfatta skalningstestning.

### <a name="alerts-during-an-attack"></a>Varningar under en attack

Azure DDoS Protection Standard identifierar och minskar DDoS-attacker utan att användaren behöver göra något. Om du vill få ett meddelande när det finns en aktiv begränsning för en skyddad offentlig IP kan du [konfigurera en avisering](/azure/virtual-network/ddos-protection-manage-portal) om måttet **under DDoS-attack eller inte**. Du kan välja att skapa aviseringar för andra DDoS-mått för att förstå omfattningen av attacken, trafik som tas bort och andra detaljer.

#### <a name="when-to-contact-microsoft-support"></a>När du ska kontakta Microsoft-supporten

- Under en DDoS-attack upptäcker du att prestanda för den skyddade resursen försämras allvarligt eller att resursen inte är tillgänglig.

- Du tror att DDoS Protection-tjänsten inte beter sig som förväntat. 

  DDoS Protection-tjänsten startar begränsning endast om **måttvärdesprincipen för att utlösa DDoS mitigation (TCP/TCP SYN/UDP)** är lägre än den trafik som tas emot på den skyddade offentliga IP-resursen.

- Du planerar en viral händelse som avsevärt kommer att öka din nätverkstrafik.

- En skådespelare har hotat att starta en DDoS-attack mot dina resurser.

- Om du behöver tillåta lista ett IP- eller IP-intervall från Azure DDoS Protection Standard. Ett vanligt scenario är att tillåta lista IP om trafiken dirigeras från ett externt moln WAF till Azure. 

Skapa en [supportbiljett](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)för allvarlighetsgrad-A för attacker som har en kritisk inverkan på verksamheten.

### <a name="post-attack-steps"></a>Steg efter attacken

Det är alltid en bra strategi för att göra en postmortem efter en attack och justera DDoS svarsstrategi som behövs. Saker att tänka på:

- Var det några störningar i tjänsten eller användarupplevelsen på grund av brist på skalbar arkitektur?

- Vilka program eller tjänster drabbades mest av?

- Hur effektiv var DDoS-svarsstrategin och hur kan den förbättras?

Om du misstänker att du är under en DDoS-attack eskalerar du via dina vanliga Azure-supportkanaler.

## <a name="ddos-protection-reference-architectures"></a>Referensarkitekturer för DDoS-skydd

DDoS Protection Standard är utformad [för tjänster som distribueras i ett virtuellt nätverk](/azure/virtual-network/virtual-network-for-azure-services). För andra tjänster gäller standardtjänsten DDoS Protection Basic. Följande referensarkitekturer ordnas efter scenarier, med arkitekturmönster grupperade tillsammans.

### <a name="virtual-machine-windowslinux-workloads"></a>Arbetsbelastningar för virtuella datorer (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Program som körs på belastningsbalanserade virtuella datorer

Den här referensarkitekturen visar en uppsättning beprövade metoder för att köra flera virtuella Windows-datorer i en skalningsuppsättning bakom en belastningsutjämnare, för att förbättra tillgängligheten och skalbarheten. Den här arkitekturen kan användas för alla tillståndslösa arbetsbelastningar, till exempel en webbserver.

![Diagram över referensarkitekturen för ett program som körs på belastningsbalanserade virtuella datorer](./media/ddos-best-practices/image9.png)

En arbetsbelastning distribueras över flera VM-instanser i den här arkitekturen. Det finns en enda offentlig IP-adress och internettrafik distribueras till de virtuella datorerna via en belastningsutjämnare. DDoS Protection Standard är aktiverad i det virtuella nätverket för Azure -belastningsutjämnaren (internet) som har den offentliga IP-adressen associerad med den.

Belastningsutjämnaren distribuerar inkommande internetbegäranden till VM-instanserna. Med skalningsuppsättningar för virtuella datorer kan antalet virtuella datorer skalas in eller ut manuellt, eller automatiskt baserat på fördefinierade regler. Detta är viktigt om resursen är under DDoS-attack. Mer information om den här referensarkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Program som körs på Windows N-nivå

Det finns många sätt att implementera en arkitektur på N-nivå. Följande diagram visar ett typiskt webbprogram med tre nivåer. Den här arkitekturen bygger på artikeln [Kör belastningsbalanserade virtuella datorer för skalbarhet och tillgänglighet](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webb- och företagsnivåer använder belastningsutjämnade virtuella datorer.

![Diagram över referensarkitekturen för ett program som körs på Windows N-nivå](./media/ddos-best-practices/image10.png)

I den här arkitekturen är DDoS Protection Standard aktiverat i det virtuella nätverket. Alla offentliga IP-adresser i det virtuella nätverket får DDoS-skydd för Layer 3 och 4. För Layer 7-skydd distribuerar du Application Gateway i WAF SKU. Mer information om den här referensarkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS-webbprogram

Den här referensarkitekturen visar att köra ett Azure App Service-program i en enda region. Den här arkitekturen visar en uppsättning beprövade metoder för ett webbprogram som använder [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) och [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Ett vänteläge har ställts in för redundansscenarier.

![Diagram över referensarkitekturen för ett PaaS-webbprogram](./media/ddos-best-practices/image11.png)

Azure Traffic Manager dirigerar inkommande begäranden till Programgateway i en av regionerna. Under normala operationer dirigeras begäranden till Application Gateway i den aktiva regionen. Om den regionen blir otillgänglig växlar Traffic Manager över till Application Gateway i väntelägesregionen.

All trafik från internet som är avsett för webbprogrammet dirigeras till [den offentliga IP-adressen application gateway](/azure/application-gateway/application-gateway-web-app-overview) via Traffic Manager. I det här fallet är apptjänsten (webbappen) inte direkt externt vänd och skyddas av Application Gateway. 

Vi rekommenderar att du konfigurerar WAF SKU-programmet (prevent mode) för att skydda mot Layer 7-attacker (HTTP/HTTPS/WebSocket). Dessutom är webbappar konfigurerade för att [endast acceptera trafik från IP-adressen för Application Gateway.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)

Mer information om den här referensarkitekturen finns i [den här artikeln](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Begränsning för PaaS-tjänster som inte är webbtjänster

#### <a name="hdinsight-on-azure"></a>HDInsight på Azure

Den här referensarkitekturen visar konfigurera DDoS Protection Standard för ett [Azure HDInsight-kluster](/azure/hdinsight/). Kontrollera att HDInsight-klustret är länkat till ett virtuellt nätverk och att DDoS-skydd är aktiverat i det virtuella nätverket.

![Rutorna "HDInsight" och "Avancerade inställningar" med inställningar för virtuella nätverk](./media/ddos-best-practices/image12.png)

![Val för att aktivera DDoS-skydd](./media/ddos-best-practices/image13.png)

I den här arkitekturen dirigeras trafik som är avsedd för HDInsight-klustret från internet till den offentliga IP som är associerad med belastningsutjämnaren HDInsight gateway. Gatewayens belastningsutjämnare skickar sedan trafiken till huvudnoderna eller arbetarnoderna direkt. Eftersom DDoS Protection Standard är aktiverat i det virtuella HDInsight-nätverket får alla offentliga IP-adresser i det virtuella nätverket DDoS-skydd för Lager 3 och 4. Den här referensarkitekturen kan kombineras med referensarkitekturer på N-nivå och flera regioner.

Mer information om den här referensarkitekturen finns i [Utöka Azure HDInsight med hjälp av en Azure Virtual](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) Network-dokumentation.


> [!NOTE]
> Azure App Service Environment for PowerApps eller API management i ett virtuellt nätverk med en offentlig IP stöds inte internt.

## <a name="next-steps"></a>Nästa steg

* [Delat ansvar i molnet](shared-responsibility.md)

* [Produktsida för Azure DDoS-skydd](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS-skyddsdokumentation](/azure/virtual-network/ddos-protection-overview)
