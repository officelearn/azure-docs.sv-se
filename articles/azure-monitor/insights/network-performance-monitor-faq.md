---
title: Vanliga frågor och svar - Övervakare av nätverksprestanda lösning i Azure | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om NPM i Azure. Nätverket prestanda Övervakare (NPM) hjälper dig att övervaka prestanda för dina nätverk i nära realtid för att identifiera och leta upp network flaskhalsar i prestanda.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: agummadi
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/12/2018
ms.author: vinynigam
ms.openlocfilehash: d216a26dc01ae3a6946c57138bb124b41f50a151
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546257"
---
# <a name="network-performance-monitor-solution-faq"></a>Vanliga frågor och svar Network Performance Monitor-lösningen

![Network Performance Monitor symbol](media/network-performance-monitor-faq/npm-symbol.png)

Den här artikeln innehåller vanliga frågor (FAQ) om nätverket prestanda Övervakare (NPM) i Azure

[Övervakaren av nätverksprestanda](/azure/networking/network-monitoring-overview) är en molnbaserad [hybrid nätverksövervakning](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) som hjälper dig att övervaka nätverksprestanda mellan olika platser i din nätverksinfrastruktur. Du kan också övervaka nätverksanslutningar till [slutpunkter för tjänsten och programmet](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) och [övervaka prestanda för Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

Övervakare av nätverksprestanda identifierar nätverksproblem som trafik blackholing, routning fel och problem som konventionella nätverk övervakning metoder som inte kan identifiera. Lösningen genererar aviseringar och meddelar dig när ett tröskelvärde överskrids för en nätverkslänk. Detta säkerställer att problem med nätverksprestanda upptäcks i god tid och att källan till problemet kan ringas in till ett visst nätverkssegment eller enhet. 

Mer information om de olika funktionerna som stöds av [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview) finns tillgänglig online.

## <a name="set-up-and-configure-agents"></a>Installera och konfigurera agenter

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Vilka är plattformskraven på för noder som ska användas för övervakning av NPM?
Nedan visas Plattformskrav för NPM-olika funktioner:

- NPM-Prestandaövervakaren och funktioner för övervakning av tjänstens anslutning har stöd för både Windows-server (2008 SP1 eller senare) och Windows skrivbord/client-operativsystem (Windows 10, Windows 8.1, Windows 8 och Windows 7). 
- NPM-ExpressRoute-övervakning funktionen stöder endast Windows server (2008 SP1 eller senare) operativsystem.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Kan jag använda Linux-datorer som övervakning av noder i NPM?
Möjlighet att övervaka nätverk med hjälp av Linux-baserade noder förhandsvisas just nu. Nå till din Kontoansvariga för vill veta mer. När du har angett arbetsyte-ID ska vi gå vidare och aktivera funktionen. Linux-agenter anger övervakningsfunktionen endast för NPM-Prestandaövervakaren kapaciteten och är inte tillgängliga för funktioner för övervakning av tjänstens anslutning och ExpressRoute-övervakning

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Vad är storlekskraven noder som ska användas för övervakning av NPM?
För att köra NPM-lösningen på noden virtuella datorer för att övervaka nätverk, bör du vara minst 500 MB minne och kärna noder. Du behöver inte använda separata noder för att köra NPM. Lösningen kan köras på noder som har andra arbetsbelastningar som körs på den. Lösningen har möjlighet att stoppa övervakningsprocessen om det använder mer än 5% CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Om du vill använda NPM ska jag ansluta min noder som direktagent eller via System Center Operations Manager?
Både prestanda och funktioner för övervakning av tjänstens anslutning stöder noder [ansluten som direkta agenter](../../azure-monitor/platform/agent-windows.md) samt [är anslutna via Operations Manager](../../azure-monitor/platform/om-agents.md).

För ExpressRoute-övervakning funktionen måste Azure-noder vara anslutna som direkta agenter endast. Azure-noder som är anslutna via Operations Manager stöds inte. Noder som är anslutna som direkta agenter samt genom Operations Manager stöds för lokala noder för övervakning av en ExpressRoute-krets.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Vilket protokoll bland TCP och ICMP bör väljas för övervakning av?
Om du övervakar ditt nätverk med hjälp av Windows server-baserade noder, rekommenderar vi att du använder TCP som protokoll för övervakning, eftersom det ger bättre precision. 

ICMP rekommenderas för Windows skrivbord/client systembaserad noder. Den här plattformen tillåter inte att TCP-data som ska skickas över råa sockets, NPM använder för att identifiera nätverkets topologi.

Du kan visa mer information om de relativa fördelarna med varje protokoll [här](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Hur kan jag konfigurera en nod för övervakning med hjälp av TCP-protokollet?
För att noden har stöd för övervakning med TCP-protokoll: 
* Kontrollera att noden plattformen är Windows Server (2008 SP1 eller senare).
* Kör [EnableRules.ps1](https://aka.ms/npmpowershellscript) Powershell-skript på noden. Se [instruktioner](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) för mer information.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Hur kan jag ändra TCP-port som används av NPM för att övervaka?
Du kan ändra den TCP-port som används av NPM för övervakning, genom att köra den [EnableRules.ps1](https://aka.ms/npmpowershellscript) skript. Du måste ange det portnummer som du tänker använda som en parameter. Till exempel om du vill aktivera TCP på port 8060 köra `EnableRules.ps1 8060`. Kontrollera att du använder samma TCP-port på alla noder som används för övervakning.

Skriptet konfigurerar bara Windows-brandväggen lokalt. Om du har nätverksbrandvägg eller regler för Nätverkssäkerhetsgrupp (NSG) kan du se till att de tillåter trafik till TCP-port som används av NPM.

### <a name="how-many-agents-should-i-use"></a>Hur många agenter ska jag använda?
Du bör använda minst en agent för varje undernät som du vill övervaka.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--you-have-reached-your-configuration-limit"></a>Vad är det maximala antalet agenter som jag kan använda eller fel visas ”... du har nått konfigurationsgränsen”?
NPM begränsar antalet IP-adresser till 5000 IP-adresser per arbetsyta. Om en nod har både IPv4 och IPv6-adresser, räknas det som 2 IP-adresser för noden. Den här gräns på 5000 IP-adresser skulle därför bestämma den övre gränsen för antalet agenter. Du kan ta bort inaktiva agenter från noder-fliken i NPM >> Konfigurera. NPM sparar också historik över alla IP-adresser som någonsin har tilldelats till den virtuella datorn som är värd för agenten och dessa också räknas som separata IP-adresser som bidrar till den övre gränsen på 5000 IP-adresser. Att frigöra IP-adresser för arbetsytan och du kan använda sidan noder att ta bort IP-adresser som inte används.

## <a name="monitoring"></a>Övervakning

### <a name="how-are-loss-and-latency-calculated"></a>Hur beräknas förlust och fördröjning
Källa agenter skickar antingen TCP SYN begäranden (om du väljer TCP som protokoll för övervakning av) eller ICMP-ekobegäranden (om du väljer ICMP som protokoll för att övervaka) till mål-IP med jämna mellanrum för att se till att alla sökvägar mellan källa-mål-IP kombination omfattas. Procentandel mottagna paket och paket tidsfördröjningen mäts för att beräkna förlust och svarstid för varje sökväg. Dessa data sammanställs via avsökningsintervallet och över alla sökvägar för att hämta de sammanställda värdena av förlust och fördröjning för IP-kombination för viss avsökningsintervallet.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Hur ofta käll-agenten skickar paket till målet för övervakning?
För Övervakare av nätverksprestanda-och ExpressRoute-övervakning källan och skickar paket var femte sekund och registrerar nätverksmätningar. Dessa data slås samman under ett avsökningsintervall för 3 minuter att beräkna de genomsnittliga och högsta värdena för förlust och fördröjning. För övervakning av tjänstens anslutning funktionen bestäms frekvensen för att skicka paket för nätverksmätning av den frekvens som anges av användaren för den specifika testning när du konfigurerar testet.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Hur många paket skickas för att övervaka?
Antalet paket som skickas av agenten källa till mål i en avsökning är anpassningsbar och beslutar om vår upphovsrättsskyddade algoritm som kan vara olika för olika nätverkstopologier. Mer antalet sökvägar på nätverket mellan källa-mål-IP-kombination, mer är antalet paket som skickas. Systemet garanterar att alla sökvägar mellan källa-mål-IP-kombinationen omfattas.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Hur identifiera nätverkets topologi mellan källan och målet av NPM?
NPM använder en upphovsrättsskyddade algoritm baserat på Traceroute för att identifiera alla sökvägar och hopp mellan källan och målet.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Tillhandahåller NPM Routning och byta nivå information 
Även om NPM kan identifiera alla vägar mellan agenten källa och mål, ger inte synlighet som vägen har kopplats av paket som skickas av dina specifika arbetsbelastningar. Lösningen kan hjälpa dig att identifiera sökvägar och underliggande nätverkssteg som lägger till mer fördröjning än du förväntade dig.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Varför är vissa av sökvägarna feltillstånd?
Olika nätverkssökvägar kan finnas mellan källa och mål IP-adresser och varje sökväg kan ha ett annat värde för förlust och fördröjning. NPM markerar de sökvägarna som skadad (betecknat med röd färg) där värdena för förlust och/eller svarstiden är större än det respektive tröskelvärdet som angetts i konfigurationen av övervakningen.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Vad ett hopp i röd färg en obestämd i topologin Nätverkskarta?
Om ett hopp är röd, innebär det att det är en del av minst en defekt sökväg. NPM markerar endast sökvägar som skadad, det särskilja inte hälsotillståndet för varje sökväg. Du kan visa hopp för hopp-fördröjning och särskilja de som att lägga till fler än förväntade svarstiden för att identifiera problematiska hopp.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Hur fungerar lokalisering av fel i Prestandaövervakaren?
NPM används en mekanism för avsnittet om sannolikhetsbunden tilldelas varje nätverkssökvägen, nätverkssegment, fel-sannolikhet och de ingående nätverkssteg baserat på antal defekta sökvägar är en del av. När blir en del av större antal defekta sökvägar nätverkssegment och hopp, ökar fault-sannolikheten kopplade till standardrisknivåer. Den här algoritmen fungerar bäst när du har många noder med NPM-agenten som är anslutna till varandra när detta ökar datapunkter för att beräkna fault-sannolikhet.

### <a name="how-can-i-create-alerts-in-npm"></a>Hur kan jag skapa aviseringar i NPM?
Referera till [aviseringar avsnittet i dokumentationen](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) stegvisa instruktioner.

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>NPM kan övervaka routrar och servrar som enskilda enheter?
NPM identifierar bara namnet på IP- och värden för underliggande nätverkssteg (växlar, routrar, servrar, osv.) mellan källa och mål IP-adresser. Den identifierar också fördröjning mellan dessa identifierade hopp. Det övervakar individuellt inte dessa underliggande hopp.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>NPM användas för att övervaka Nätverksanslutningar mellan Azure och AWS?
Ja. Finns i artikeln [övervaka Azure, AWS och lokala nätverk med NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) mer information.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Är ExpressRoute bandbreddsanvändning inkommande eller utgående?
Användning av nätverksbandbredd är summan av inkommande och utgående bandbredd. Det uttrycks i bit/s.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Kan vi få information om inkommande och utgående bandbredd för ExpressRoute?
Inkommande och utgående värden för både primär och sekundär bandbredd kan hämtas.

För peering nivåinformation använder den nedan nämnda frågan i Loggsökning

    NetworkMonitoring 
    | where SubType == "ExpressRoutePeeringUtilization"
    | project CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Kretsen nivåinformation, använda den nedanför nämnda fråga 

    NetworkMonitoring 
    | where SubType == "ExpressRouteCircuitUtilization"
    | project CircuitName,PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Vilka regioner har stöd för NPM-Prestandaövervakaren?
NPM kan övervaka anslutningen mellan nätverk i någon del av världen, från en arbetsyta som är värd för en av de [regioner som stöds](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Vilka regioner har stöd för övervakning av NPM-tjänstens anslutning?
NPM kan övervaka anslutningen till tjänster i andra delar av världen, från en arbetsyta som är värd för en av de [regioner som stöds](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Vilka regioner har stöd för NPM-ExpressRoute-övervakning?
NPM kan övervaka din ExpressRoute-kretsar som finns i alla Azure-regioner. Att publicera till NPM, behöver du en Log Analytics-arbetsyta som måste finnas i något av de [regioner som stöds](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Felsöka

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Varför är vissa av hopp som markerats som oidentifierade i nätverket topologiska vyn?
NPM använder en modifierad version av traceroute för att identifiera topologin från agenten källan till målet. En oidentifierat hopp representerar nätverk hopp inte svarade på käll-agentens traceroute begäran. Om 3 på varandra följande nätverkssteg inte svarar på agentens traceroute, lösningen markerar inte svarar hopp som oidentifierade och försöker inte identifiera mer hopp.

Ett hopp svarar inte på en traceroute i en eller flera av de nedan scenarier:

* Routrarna har konfigurerats för att inte visa sin identitet.
* Nätverksenheter som inte tillåter att ICMP_TTL_EXCEEDED trafik.
* En brandvägg blockerar ICMP_TTL_EXCEEDED svaret från nätverksenheten.

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Lösningen visar 100% förlust men det finns en anslutning mellan källa och mål
Detta kan inträffa om av värdens brandvägg eller mellanliggande brandvägg (nätverksbrandvägg eller Azure NSG) blockerar kommunikation mellan agenten källan och målet via porten som används för övervakning av NPM (som standard porten är 8084, såvida inte den kunden har ändrats för detta).

* Visa hälsotillståndet för käll- och målnoder från följande vy för att kontrollera att värdens brandvägg inte blockerar kommunikation på porten som krävs: Network Performance Monitor -> konfigurationsserver -> noder. 
  Om de inte är felfria instruktioner och vidta åtgärder. Om noderna fungerar felfritt, gå vidare till steg b. nedan.
* För att kontrollera att en mellanliggande brandvägg eller en Azure-Nätverkssäkerhetsgrupper inte blockerar kommunikation på den begärda porten, använder du tredje parts PsPing verktyget med i instruktionerna nedan:
  * psping verktyget är tillgänglig för hämtning [här](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Kör följande kommando från Källnoden.
    * psping -n 15 \<målnoden IPAddress\>: portNumber NPM som standard använder 8084 port. Om du uttryckligen har ändrat detta med hjälp av skriptet EnableRules.ps1, ange anpassat portnummer som du använder). Det här är en ping från Azure-datorer till en lokal
* Kontrollera om ping är klar. Annars kan du sedan indikerar att en mellanliggande brandvägg eller en Azure-Nätverkssäkerhetsgrupp blockerar trafik på den här porten.
* Nu kan köra kommandot från målnoden som källa för nod-IP.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Det finns förlust från nod A till B, men inte från nod B till A. Varför?
Eftersom nätverkssökvägar mellan A till B kan skilja sig från nätverkssökvägar mellan B till A, kan olika värden för förlust och fördröjning observeras.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Varför är min ExpressRoute-kretsar och peering-anslutningarna inte identifieras?
NPM identifieras nu ExpressRoute-kretsar och peering-anslutningar i alla prenumerationer som användaren har åtkomst till. Välj alla prenumerationer där resurserna i Express Route kopplas och aktivera övervakning för varje identifierad resurs. NPM letar efter anslutningsobjekt när identifiering av en privat peering, så kontrollera om ett virtuellt nätverk är associerat med din peering.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Funktionen för ER övervakaren har en diagnostikmeddelande ”trafiken passerar inte genom alla kretsar”. Vad betyder det?

Det kan finnas ett scenario där det finns en felfri anslutning mellan lokala platser och Azure-noder men trafiken inte ska via ExpressRoute-krets som har konfigurerats för att övervakas av NPM. 

Detta kan inträffa om:

* ER kretsen har stoppats.
* Route-filter är konfigurerade på ett sådant sätt att de ger prioritet till andra vägar (till exempel en VPN-anslutning eller en annan ExpressRoute-krets) över avsedd ExpressRoute-kretsen. 
* Lokala och Azure-noder valt för att övervaka ExpressRoute-kretsen i konfigurationen av övervakningen har inte anslutning till varandra över avsedd ExpressRoute-kretsen. Se till att du har valt rätt noder som har anslutning till varandra via ExpressRoute-krets som du vill övervaka.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>När du konfigurerar övervakning av min ExpressRoute-krets, identifieras inte Azure-noder.
Detta kan inträffa om Azure-noder är anslutna via Operations Manager. ExpressRoute-övervakning-funktionen stöder bara dessa Azure-noder som är ansluten som direkta agenter.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Jag kan inte identifiera av ExpressRoute-kretsar i OMS-portalen
Även om NPM kan användas både från Azure portal samt OMS-portalen, fungerar krets identifiering i funktionen för ExpressRoute-övervakning endast via Azure portal. När kretsarna identifieras via Azure-portalen, kan du sedan använda funktionen i någon av de två portalerna. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>I funktionen för övervakning av tjänstens anslutning visas den tjänstens svarstid, nätverksförluster, samt svarstid som NA
Detta kan inträffa om en eller flera är sant:

* Tjänsten är igång.
* Noden som används för att kontrollera nätverksanslutningen till tjänsten har stoppats.
* Målet som angetts i testkonfigurationen är felaktig.
* Noden har inte någon nätverksanslutning.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>I funktionen för övervakning av tjänstens anslutning, visas en giltig tjänstens svarstid men nätverksförluster samt svarstid visas som NA
 Detta kan inträffa om en eller flera är sant:

* Om noden som används för att kontrollera nätverksanslutningen till tjänsten är en Windows-klientdator, Måltjänsten blockerar ICMP-begäranden, eller en brandvägg blockerar ICMP-begäranden som kommer från noden.
* Kryssrutan utför nätverk mätning av faktisk användning har inte angetts i testkonfigurationen.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>Tjänstens svarstid är NA i funktionen för övervakning av tjänstens anslutning, men nätverksförluster samt svarstid är giltiga
Detta kan inträffa om Måltjänsten är inte ett webbprogram men testet har konfigurerats som ett webbtest. Redigera testkonfigurationen och välj testtypen som nätverk istället för webben.

## <a name="miscellaneous"></a>Övrigt

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Finns det en prestandapåverkan på noden som används för övervakning?
NPM-processen har konfigurerats för att stoppa om det använder mer än 5% av processorresurserna värden. Detta är att säkerställa att du kan fortsätta att använda noderna för sina vanliga arbetsbelastningar utan att påverka prestanda.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Kan du redigera brandväggsregler för att övervaka NPM?
NPM skapar bara en lokal Windows-brandväggsregel på noderna där EnableRules.ps1 Powershell-skriptet körs så att agenterna ska skapa TCP-anslutningar med varandra på den angivna porten. Lösningen ändras inte någon brandvägg eller regler för Nätverkssäkerhetsgrupp (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Hur kan jag kontrollera hälsotillståndet för noderna som används för övervakning?
Du kan visa hälsostatus noder som används för att övervaka från följande vy: Network Performance Monitor -> konfigurationsserver -> noder. Om en nod är felaktiga kan du visa felinformationen och vidta rekommenderad åtgärd.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>NPM kan rapportera latens på mikrosekunder?
NPM Avrundar talen svarstid i Användargränssnittet och i millisekunder. Samma data lagras i en mer detaljrikedom (ibland upp till fyra decimaler).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Övervakare av nätverksprestanda genom att referera till [Network Performance Monitor-lösning i Azure](../../azure-monitor/insights/network-performance-monitor.md).
