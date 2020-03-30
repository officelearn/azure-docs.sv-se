---
title: Vanliga frågor – lösning för nätverksprestandaövervakare i Azure | Microsoft-dokument
description: Den här artikeln fångar de vanligaste frågorna om Network Performance Monitor i Azure. NPM (Network Performance Monitor) hjälper dig att övervaka nätverkets prestanda i nära realtid och upptäcka och hitta flaskhalsar i nätverksprestanda.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 443e4b44633e949dd9bd55df1ec7d18ca93d6e04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096224"
---
# <a name="network-performance-monitor-solution-faq"></a>Vanliga frågor och svar om lösningen för nätverksprestanda

![Symbol för Övervakare för nätverksprestanda](media/network-performance-monitor-faq/npm-symbol.png)

I den här artikeln fångas vanliga frågor och svar (Vanliga frågor) om NPM (Network Performance Monitor) i Azure

[Network Performance Monitor](/azure/networking/network-monitoring-overview) är en molnbaserad [lösning för övervakning av hybridnätverk](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) som hjälper dig att övervaka nätverksprestanda mellan olika punkter i nätverksinfrastrukturen. Det hjälper dig också att övervaka nätverksanslutning till [tjänst- och programslutpunkter](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) och [övervaka prestanda för Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

Network Performance Monitor identifierar nätverksproblem som trafiksvartning, routningsfel och problem som konventionella nätverksövervakningsmetoder inte kan identifiera. Lösningen genererar aviseringar och meddelar dig när ett tröskelvärde överskrids för en nätverkslänk. Detta säkerställer att problem med nätverksprestanda upptäcks i god tid och att källan till problemet kan ringas in till ett visst nätverkssegment eller enhet. 

Mer information om de olika funktioner som stöds av [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) finns tillgänglig online.

## <a name="set-up-and-configure-agents"></a>Konfigurera agenter för konfiguration och konfiguration

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Vilka är plattformskraven för de noder som ska användas för övervakning av NPM?
Nedan listas plattformskraven för NPM:s olika funktioner:

- NPM:s kapacitet för prestandaövervakaren och tjänstanslutningsövervakaren stöder både Windows-server- och Windows-operativsystem/klientoperativsystem. Windows Server OS-versioner som stöds är 2008 SP1 eller senare. Windows-skrivbord/klientversioner som stöds är Windows 10, Windows 8.1, Windows 8 och Windows 7. 
- NPM:s ExpressRoute Monitor-funktion stöder endast Windows-server (2008 SP1 eller senare) operativsystem.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Kan jag använda Linux-datorer som övervakningsnoder i NPM?
Möjligheten att övervaka nätverk med Linux-baserade noder är för närvarande i förhandsversion. Kontakta din Kontohanterare för att få veta mer. Linux-agenter tillhandahåller endast övervakningsfunktioner för NPM:s prestandaövervakare och är inte tillgängliga för funktionerna Service Connectivity Monitor och ExpressRoute Monitor

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Vilka storlekskrav ställs för de noder som ska användas för övervakning av NPM?
För att köra NPM-lösningen på virtuella nod-datorer för att övervaka nätverk bör noderna ha minst 500 MB minne och en kärna. Du behöver inte använda separata noder för att köra NPM. Lösningen kan köras på noder som har andra arbetsbelastningar som körs på den. Lösningen har möjlighet att stoppa övervakningsprocessen om den använder mer än 5% CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Ska jag ansluta mina noder som Direct-agent eller systemcenter operationshanteraren om jag vill använda NPM?
Både prestandaövervakaren och serviceanslutningsövervakaren stöder noder [som är anslutna som direktagenter](../../azure-monitor/platform/agent-windows.md) och anslutna via Operations [Manager](../../azure-monitor/platform/om-agents.md).

För ExpressRoute Monitor-funktionen bör Azure-noderna endast anslutas som direct-agenter. Azure-noder, som är anslutna via Operations Manager stöds inte. För lokala noder stöds noderna som är anslutna som direktagenter och via Operations Manager för övervakning av en ExpressRoute-krets.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Vilket protokoll mellan TCP och ICMP bör väljas för övervakning?
Om du övervakar nätverket med Windows serverbaserade noder rekommenderar vi att du använder TCP som övervakningsprotokoll eftersom det ger bättre noggrannhet. 

ICMP rekommenderas för Windows-skrivbord/klientoperativsystembaserade noder. Den här plattformen tillåter inte att TCP-data skickas över råa sockets, som NPM använder för att identifiera nätverkstopologi.

Du kan få mer information om de relativa fördelarna med varje protokoll [här](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Hur konfigurerar jag en nod för att stödja övervakning med TCP-protokollet?
För noden som stöder övervakning med TCP-protokoll: 
* Kontrollera att nodplattformen är Windows Server (2008 SP1 eller senare).
* Kör [EnableRules.ps1](https://aka.ms/npmpowershellscript) Powershell-skriptet på noden. Se [instruktioner](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) för mer information.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Hur ändrar jag TCP-porten som används av NPM för övervakning?
Du kan ändra TCP-porten som används av NPM för övervakning genom att köra [Skriptet EnableRules.ps1.](https://aka.ms/npmpowershellscript) Du måste ange det portnummer som du tänker använda som parameter. Om du till exempel vill aktivera TCP på `EnableRules.ps1 8060`port 8060 kör du . Se till att du använder samma TCP-port på alla noder som används för övervakning.

Skriptet konfigurerar endast Windows-brandväggen lokalt. Om du har regler för nätverksbrandvägg eller NSG -regler (Network Security Group) kontrollerar du att de tillåter den trafik som är avsedd för TCP-porten som används av NPM.

### <a name="how-many-agents-should-i-use"></a>Hur många agenter ska jag använda?
Du bör använda minst en agent för varje undernät som du vill övervaka.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Vad är det maximala antalet agenter jag kan använda eller jag ser fel ".... du har nått din konfigurationsgräns"?
NPM begränsar antalet IPs till 5000 IPs per arbetsyta. Om en nod har både IPv4- och IPv6-adresser räknas detta som två IP-adresser för den noden. Därför skulle denna gräns på 5000 IPs bestämma den övre gränsen för antalet agenter. Du kan ta bort de inaktiva agenterna från fliken Noder i NPM >> Konfigurera. NPM upprätthåller också historik över alla IP-adresser som någonsin tilldelats den virtuella datorn som är värd för agenten och var och en räknas som separat IP som bidrar till den övre gränsen på 5000 IP-adresser. Om du vill frigöra INTERNET-adresser för arbetsytan kan du använda sidan Noder för att ta bort de IPs som inte används.

## <a name="monitoring"></a>Övervakning

### <a name="how-are-loss-and-latency-calculated"></a>Hur beräknas förlust och latens
Källagenter skickar antingen TCP SYN-begäranden (om TCP väljs som protokoll för övervakning) eller ICMP ECHO-begäranden (om ICMP väljs som protokoll för övervakning) till mål-IP med jämna mellanrum för att säkerställa att alla sökvägar mellan käll-destination IP kombination är täckta. Procentandelen mottagna paket och paket tur-och-retur-tid mäts för att beräkna förlust och svarstid för varje sökväg. Dessa data aggregeras över avsökningsintervallet och över alla sökvägar för att få de aggregerade värdena för förlust och svarstid för IP-kombinationen för det specifika avsökningsintervallet.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Med vilken frekvens skickar källagenten paket till målet för övervakning?
För prestandaövervakaren och ExpressRoute Monitor-funktionerna skickar källan paket var femte sekund och registrerar nätverksmätningarna. Dessa data aggregeras över ett 3-minuters avsökningsintervall för att beräkna medelvärdena och toppvärdena för förlust och svarstid. För tjänstens anslutningsövervakare bestäms frekvensen för att skicka paketen för nätverksmätning av den frekvens som användaren angett för det specifika testet när testet konfigureras.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Hur många paket skickas för övervakning?
Antalet paket som skickas av källagenten till destinationen i en avsökning är anpassningsbart och bestäms av vår egen algoritm, som kan vara olika för olika nätverkstopologier. Mer antal nätverkssökvägar mellan käll-mål IP-kombinationen, mer är antalet paket som skickas. Systemet säkerställer att alla sökvägar mellan ip-kombinationen för källa och mål täcks.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Hur upptäcker NPM nätverkstopologi mellan källa och mål?
NPM använder en egen algoritm baserad på Traceroute för att identifiera alla sökvägar och hopp mellan källa och mål.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Tillhandahåller NPM information om routnings- och växlingsnivå 
Även om NPM kan identifiera alla möjliga vägar mellan källagenten och målet, ger det inte insyn i vilken väg som togs av paketen som skickades av dina specifika arbetsbelastningar. Lösningen kan hjälpa dig att identifiera sökvägar och underliggande nätverkshopp, som lägger till mer svarstid än förväntat.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Varför är några av banorna ohälsosamma?
Det kan finnas olika nätverkssökvägar mellan käll- och mål-IP-adresser och varje sökväg kan ha olika värden för förlust och svarstid. NPM markerar dessa banor som felaktiga (betecknade med röd färg) för vilka värdena för förlust och/eller svarstid är större än respektive tröskelvärde som angetts i övervakningskonfigurationen.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Vad betyder ett hopp i röd färg i nätverkstopologikartan?
Om ett hopp är rött betyder det att det är en del av minst en felsökväg. NPM markerar bara banorna som felaktiga, det segregerar inte hälsostatusen för varje sökväg. För att identifiera de besvärliga hoppen kan du visa svarstiden för hopp för hopp och segregera de som lägger till mer än förväntat svarstid.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Hur fungerar fellokalisering i Prestandaövervakaren?
NPM använder en probabilistisk mekanism för att tilldela felsannolikheter till varje nätverkssökväg, nätverkssegment och det ingående nätverkshoppet baserat på antalet felaktiga sökvägar som de är en del av. När nätverkssegment och hopp blir en del av fler felvägar ökar den felsannolikhet som är associerad med dem. Den här algoritmen fungerar bäst när du har många noder med NPM-agent ansluten till varandra eftersom detta ökar datapunkterna för beräkning av felsannolikheter.

### <a name="how-can-i-create-alerts-in-npm"></a>Hur skapar jag aviseringar i NPM?
Se [avsnittet aviseringar i dokumentationen](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) för steg-för-steg-instruktioner.

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Vilka är standardfrågorna för Logganalys för aviseringar
Fråga för prestandaövervakare

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Fråga för tjänstanslutningsövervakning

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
ExpressRoute-övervakarfrågor: Fråga om kretsar

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

Privat peering

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Microsoft-peering

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

Vanlig fråga   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Kan NPM övervaka routrar och servrar som enskilda enheter?
NPM identifierar endast IP- och värdnamnet för underliggande nätverkshopp (växlar, routrar, servrar osv.) mellan käll- och mål-IP-adresser. Den identifierar också svarstiden mellan dessa identifierade hopp. Det övervakar inte individuellt dessa underliggande humle.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Kan NPM användas för att övervaka nätverksanslutningen mellan Azure och AWS?
Ja. Mer information finns i artikeln [Monitor Azure, AWS och lokala nätverk som använder NPM.](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Är ExpressRoute bandbreddsanvändning inkommande eller utgående?
Bandbreddsanvändning är summan av inkommande och utgående bandbredd. Det uttrycks i Bitar/sek.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Kan vi få inkommande och utgående bandbredd information för ExpressRoute?
Inkommande och utgående värden för både primär och sekundär bandbredd kan fångas in.

För information om MS-peering-nivå använder du följande fråga i Loggsökning

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
För information på privat peering-nivå använder du följande fråga i Loggsökning

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
För information om kretsnivå, använd nedanstående fråga i Loggsökning

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Vilka regioner stöds för NPM:s prestandaövervakare?
NPM kan övervaka anslutningen mellan nätverk i alla delar av världen, från en arbetsyta som finns i en av de [regioner som stöds](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Vilka regioner stöds för NPM:s serviceanslutningsövervakare?
NPM kan övervaka anslutningen till tjänster i alla delar av världen, från en arbetsyta som finns i en av de [regioner som stöds](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Vilka regioner stöds för NPM:s ExpressRoute-övervakare?
NPM kan övervaka dina ExpressRoute-kretsar som finns i alla Azure-regioner. För att kunna gå ombord till NPM behöver du en Log Analytics-arbetsyta som måste finnas i en av de [regioner som stöds](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Felsöka

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Varför markeras några av hoppen som oidentifierade i nätverkstopologivyn?
NPM använder en modifierad version av traceroute för att identifiera topologin från källagenten till målet. Ett oidentifierat hopp representerar att nätverkshoppet inte svarade på källagentens traceroute-begäran. Om tre på varandra följande nätverkshopp inte svarar på agentens traceroute markerar lösningen de hopp som inte svarar som oidentifierade och försöker inte upptäcka fler hopp.

Ett hopp kanske inte svarar på ett traceroute i ett eller flera av nedanstående scenarier:

* Routrarna har konfigurerats för att inte avslöja sin identitet.
* Nätverksenheterna tillåter inte ICMP_TTL_EXCEEDED trafik.
* En brandvägg blockerar ICMP_TTL_EXCEEDED svar från nätverksenheten.

När någon av slutpunkterna finns i Azure visar traceroute oidentifierade hopp eftersom Azure Infrastructure inte avslöjar identitet för traceroute. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Jag får varningar för ohälsosamma tester men jag ser inte de höga värdena i NPM förlust och latens diagram. Hur kontrollerar jag vad som är ohälsosamt?
NPM höjer en avisering om svarstid mellan källa och mål överskrider tröskelvärdet för alla vägar mellan dem. Vissa nätverk har flera sökvägar som förbinder samma källa och mål. NPM höjer en avisering är någon sökväg är felfritt. Förlusten och svarstiden som visas i diagrammen är medelvärdet för alla banor, varför det kanske inte visar det exakta värdet för en enda bana. Om du vill förstå var tröskelvärdet har överskridits letar du efter kolumnen "SubType" i aviseringen. Om problemet orsakas av en sökväg kommer subtype-värdet att vara NetworkPath (för prestandaövervakarens tester), EndpointPath (för serviceanslutningsövervakarestester) och ExpressRoutePath (för ExpressRotue Monitor-tester). 

Exempel på fråga för att hitta är sökvägen är felfritt:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Varför visar mitt test ohälsosamt men topologin inte 
NPM övervakar förlust från till, svarstid och topologi med olika intervall. Förlust och latens mäts en gång var femte sekund och aggregeras var tredje minut (för Prestandaövervakaren och Express Route Monitor) medan topologin beräknas med traceroute en gång var 10:e minut. Mellan 3:44 och 4:04 kan till exempel topologin uppdateras tre gånger (3:44, 3:54, 4:04), men förlust och latens uppdateras ungefär sju gånger (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). Topologin som genereras vid 3:54 kommer att återges för förlust och svarstid som beräknas till 3:56, 3:59 och 4:02. Anta att du får en varning om att din ER-krets var ohälsosam vid 3:59. Du loggar in på NPM och försöker ställa in topologitiden till 3:59. NPM kommer att återge topologin som genereras vid 3:54. Om du vill förstå den senast kända topologin för nätverket jämför du fälten TimeProcessed (tid då förlust och svarstid beräknades) och TracerouteCompletedTime(tid då topologin beräknades). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Vad är skillnaden mellan fälten E2EMedianLatency och AvgHopLatencyList i tabellen NetworkMonitoring
E2EMedianLatency är latensen uppdateras var tredje minut efter aggregering resultaten av tcp ping tester, medan AvgHopLatencyList uppdateras var 10 minuter baserat på traceroute. Om du vill förstå exakt vid vilken tidpunkt E2EMedianLatency beräknades använder du fältet TimeProcessed. Om du vill förstå den exakta tidpunkten då traceroute slutfördes och uppdaterade AvgHopLatencyList använder du fältet TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Varför skiljer sig antalet latency-tal för hopp från HopLatencyValues 
HopLatencyValues är källa till slutpunkt.
Till exempel: Humle - A,B,C. AvgHopLatency - 10,15,20. Detta innebär källa till En latens = 10, källa till B latens = 15 och källa till C latens är 20. Användargränssnittet beräknar A-B-hoppfördröjningen som 5 i topologin

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Lösningen visar 100% förlust men det finns anslutning mellan källan och målet
Detta kan inträffa om antingen värdbrandväggen eller den mellanliggande brandväggen (nätverksbrandväggen eller Azure NSG) blockerar kommunikationen mellan källagenten och målet över den port som används för övervakning av NPM (som standard är porten 8084, såvida inte kunden har ändrat detta).

* Om du vill kontrollera att värdbrandväggen inte blockerar kommunikationen på den port som krävs visar du käll- och målnodernas hälsostatus från följande vy: Network Performance Monitor -> Configuration -> Noder. 
  Om de är ohälsosamma kan du visa instruktionerna och vidta korrigerande åtgärder. Om noderna är felfria går du vidare till steg b. nedan.
* Om du vill kontrollera att en mellanliggande nätverksbrandvägg eller Azure NSG inte blockerar kommunikationen på den port som krävs använder du psping-verktyget från tredje part med hjälp av instruktionerna nedan:
  * psping verktyget finns att ladda ner [här](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Kör följande kommando från källnoden.
    * psping -n 15 \<målnod\>IPAddress :pnummer Som standard NPM använder 8084 port. Om du uttryckligen har ändrat detta med hjälp av EnableRules.ps1-skriptet anger du det anpassade portnummer som du använder). Detta är en ping från Azure-dator till lokalt
* Kontrollera om pingarna lyckas. Om inte, indikerar det att en mellanliggande nätverksbrandvägg eller Azure NSG blockerar trafiken på den här porten.
* Kör nu kommandot från målnod till källnod-IP.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Det finns förlust från noden A till B, men inte från nod B till A. Varför?
Eftersom nätverksvägarna mellan A till B kan skilja sig från nätverksvägarna mellan B och A kan olika värden för förlust och svarstid observeras.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Varför upptäcks inte alla mina ExpressRoute-kretsar och peering-anslutningar?
NPM upptäcker nu ExpressRoute-kretsar och peering-anslutningar i alla prenumerationer som användaren har åtkomst till. Välj alla prenumerationer där dina Express Route-resurser är länkade och aktivera övervakning för varje upptäckt resurs. NPM söker efter anslutningsobjekt när du upptäcker en privat peering, så kontrollera om ett VNET är associerat med din peering.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>ER Monitor-funktionen har ett diagnostiskt meddelande "Trafiken passerar inte genom någon krets". Vad innebär det?

Det kan finnas ett scenario där det finns en felfri anslutning mellan de lokala och Azure-noderna men trafiken går inte över ExpressRoute-kretsen som konfigurerats för att övervakas av NPM. 

Detta kan inträffa i följande fall:

* ER-kretsen är nere.
* Flödesfiltren konfigureras på ett sådant sätt att de prioriterar andra vägar (till exempel en VPN-anslutning eller en annan ExpressRoute-krets) över den avsedda ExpressRoute-kretsen. 
* De lokala och Azure-noder som valts för övervakning av ExpressRoute-kretsen i övervakningskonfigurationen har inte anslutning till varandra via den avsedda ExpressRoute-kretsen. Se till att du har valt rätt noder som har anslutning till varandra via ExpressRoute-kretsen som du tänker övervaka.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>När du konfigurerar övervakning av min ExpressRoute-krets identifieras inte Azure-noder.
Detta kan inträffa om Azure-noderna är anslutna via Operations Manager. ExpressRoute Monitor-funktionen stöder endast de Azure-noder som är anslutna som direct-agenter.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Jag kan inte upptäcka av ExpressRoute kretsar i OMS-portalen
Även om NPM kan användas både från Azure-portalen och OMS-portalen, fungerar kretsidentifieringen i ExpressRoute Monitor-funktionen endast via Azure-portalen. När kretsarna har upptäckts via Azure-portalen kan du sedan använda funktionen i någon av de två portalerna. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>I funktionen Service Connectivity Monitor visas tjänstens svarstid, nätverksförlust och svarstid som NA
Detta kan inträffa om en eller flera är sant:

* Tjänsten är nere.
* Noden som används för att kontrollera nätverksanslutningen till tjänsten är nere.
* Målet som anges i testkonfigurationen är felaktigt.
* Noden har ingen nätverksanslutning.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>I tjänsten Anslutningsövervakaren visas en giltig svarstid för tjänsten, men nätverksförlust och svarstid visas som NA
 Detta kan inträffa om en eller flera är sant:

* Om noden som används för att kontrollera nätverksanslutningen till tjänsten är en Windows-klientdator blockerar antingen måltjänsten ICMP-begäranden eller en nätverksbrandvägg blockerar ICMP-begäranden som kommer från noden.
* Kryssrutan Utför nätverksmätningar är tom i testkonfigurationen.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>I tjänsten Anslutningsövervakaren är tjänstens svarstid NA men nätverksförlust samt svarstid är giltiga
Detta kan inträffa om måltjänsten inte är ett webbprogram men testet är konfigurerat som ett webbtest. Redigera testkonfigurationen och välj testtypen som Nätverk i stället för webben.

## <a name="miscellaneous"></a>Övrigt

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Finns det en prestandapåverkan på noden som används för övervakning?
NPM-processen är konfigurerad för att stoppas om den använder mer än 5% av värd-CPU-resurser. Detta för att säkerställa att du kan fortsätta använda noderna för sina vanliga arbetsbelastningar utan att påverka prestanda.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Redigerar NPM brandväggsregler för övervakning?
NPM skapar bara en lokal Windows-brandväggsregel på de noder där Skriptet EnableRules.ps1 Powershell körs så att agenterna kan skapa TCP-anslutningar med varandra på den angivna porten. Lösningen ändrar inga regler för nätverksbrandvägg eller NSG-regler (Network Security Group).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Hur kan jag kontrollera hälsan hos de noder som används för övervakning?
Du kan visa hälsostatus för de noder som används för övervakning från följande vy: Network Performance Monitor -> Configuration -> Noder. Om en nod är felfritt kan du visa felinformationen och vidta den föreslagna åtgärden.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Kan NPM rapportera latensnummer i mikrosekoner?
NPM avrundar svarstidsnumren i användargränssnittet och i millisekunder. Samma data lagras med en högre granularitet (ibland upp till fyra decimaler).

## <a name="next-steps"></a>Nästa steg

- Läs mer om Network Performance Monitor genom att referera till [Network Performance Monitor-lösningen i Azure](../../azure-monitor/insights/network-performance-monitor.md).
