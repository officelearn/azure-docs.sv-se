---
title: Lösning för nätverksprestandaövervakare i Azure | Microsoft-dokument
description: Network Performance Monitor i Azure hjälper dig att övervaka nätverkets prestanda, i nära realtid, för att identifiera och hitta flaskhalsar i nätverksprestanda.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 9660e87f3ee4e1c1c6a270f14928fdd111664e66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480886"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Lösning för Nätverksprestandaövervakare i Azure

![Symbol för Övervakare för nätverksprestanda](./media/network-performance-monitor/npm-symbol.png)


Network Performance Monitor är en molnbaserad lösning för övervakning av hybridnätverk som hjälper dig att övervaka nätverksprestanda mellan olika punkter i nätverksinfrastrukturen. Du kan också övervaka nätverksanslutningar till tjänsten och programslutpunkterna samt övervaka prestanda för Azure ExpressRoute. 

Network Performance Monitor identifierar nätverksproblem som trafiksvartning, routningsfel och problem som konventionella nätverksövervakningsmetoder inte kan identifiera. Lösningen genererar aviseringar och meddelar dig när ett tröskelvärde överskrids för en nätverkslänk. Detta säkerställer att problem med nätverksprestanda upptäcks i god tid och att källan till problemet kan ringas in till ett visst nätverkssegment eller enhet. 

Network Performance Monitor erbjuder tre breda funktioner: 

* [Prestandaövervakare:](network-performance-monitor-performance-monitor.md)Du kan övervaka nätverksanslutningen mellan molndistributioner och lokala platser, flera datacenter och filialkontor och verksamhetskritiska flerkanalsprogram eller mikrotjänster. Med Performance Monitor kan du identifiera nätverksproblem innan användarna klagar.

* [Service Connectivity Monitor:](network-performance-monitor-service-connectivity.md)Du kan övervaka anslutningen från användarna till de tjänster du bryr dig om, avgöra vilken infrastruktur som finns i sökvägen och identifiera var flaskhalsar i nätverket uppstår. Du kan känna till avbrott före användarna och se den exakta platsen för problemen längs nätverkssökvägen. 

    Den här funktionen hjälper dig att utföra tester baserat på HTTP, HTTPS, TCP och ICMP för att övervaka i nära realtid eller historiskt tillgänglighet och svarstid för din tjänst. Du kan också övervaka nätverkets bidrag i paketförlust och svarstid. Med en nätverkstopologikarta kan du isolera nätverksavmattningen. Du kan identifiera problemfläckar som uppstår längs nätverkssökvägen från noden till tjänsten, med svarstidsdata för varje hopp. Med inbyggda tester kan du övervaka nätverksanslutningen till Office 365 och Dynamics CRM utan förkonfiguration. Med den här funktionen kan du övervaka nätverksanslutningen till alla TCP-kompatibla slutpunkter, till exempel webbplatser, SaaS-program, PaaS-program och SQL-databaser.

* [ExpressRoute Monitor](network-performance-monitor-expressroute.md): Övervaka heltäckande anslutning och prestanda mellan dina filialkontor och Azure, via Azure ExpressRoute.  

Mer information om de olika funktioner som stöds av [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) finns tillgänglig online.
 
## <a name="supported-regions"></a>Regioner som stöds
NPM kan övervaka anslutningen mellan nätverk och program i alla delar av världen, från en arbetsyta som finns i någon av följande regioner:
* Europa, norra
* Europa, västra
* Frankrike, centrala
* Kanada, centrala
* USA, västra
* USA, västra centrala
* USA, norra centrala
* USA, södra centrala
* USA, centrala
* USA, östra
* USA, östra 2
* USA, västra 2
* Östra Japan
* Sydostasien
* Sydöstra Australien
* Australien, centrala
* Australien, östra
* Södra Storbritannien
* Asien, östra
* Sydkorea, centrala
* Indien, centrala
* Amerikanska regeringen Virginia
* Kina Öst 2


Listan över regioner som stöds för ExpressRoute Monitor finns i [dokumentationen](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117).


## <a name="set-up-and-configure"></a>Konfigurera och konfigurera

### <a name="install-and-configure-agents"></a>Installera och konfigurera agenter 

Använd de grundläggande processerna för att installera agenter på [Connect Windows-datorer till Azure Monitor](../platform/agent-windows.md) och Connect Operations Manager till Azure [Monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Var kan man installera agenterna 

* **Prestandaövervakare:** Installera Logganalysagenter på minst en nod som är ansluten till varje undernätverk från vilket du vill övervaka nätverksanslutningen till andra undernätverk.

    Om du vill övervaka en nätverkslänk installerar du agenter på båda slutpunkterna för den länken. Om du är osäker på nätverkets topologi installerar du agenterna på servrar med kritiska arbetsbelastningar som du vill övervaka nätverksprestandan mellan. Om du till exempel vill övervaka nätverksanslutningen mellan en webbserver och en server som kör SQL installerar du en agent på båda servrarna. Agenter övervakar nätverksanslutning (länkar) mellan värdar, inte värdarna själva. 

* **Service Connectivity Monitor**: Installera en Log Analytics-agent på varje nod som du vill övervaka nätverksanslutningen till tjänstens slutpunkt. Ett exempel är om du vill övervaka nätverksanslutningen till Office 365 från kontorsplatserna O1, O2 och O3. Installera Log Analytics-agenten på minst en nod vardera i O1, O2 och O3. 

* **ExpressRoute Monitor**: Installera minst en Log Analytics-agent i ditt virtuella Azure-nätverk. Installera också minst en agent i ditt lokala undernätverk, som är anslutet via ExpressRoute privat peering.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurera Log Analytics-agenter för övervakning 

Network Performance Monitor använder syntetiska transaktioner för att övervaka nätverksprestanda mellan käll- och målagenter. Du kan välja mellan TCP och ICMP som protokoll för övervakning i prestandaövervakaren och tjänstanslutningsövervakaren. Endast TCP är tillgängligt som övervakningsprotokoll för ExpressRoute Monitor. Se till att brandväggen tillåter kommunikation mellan Log Analytics-agenter som används för övervakning på det protokoll du väljer. 

* **TCP-protokoll**: Om du väljer TCP som protokoll för övervakning öppnar du brandväggsporten på de agenter som används för Network Performance Monitor och ExpressRoute Monitor för att se till att agenterna kan ansluta till varandra. Om du vill öppna porten kör du [PowerShell-skriptet EnableRules.ps1](https://aka.ms/npmpowershellscript) utan några parametrar i ett PowerShell-fönster med administratörsbehörighet.

    Skriptet skapar registernycklar som krävs av lösningen. Det skapar också Windows-brandväggsregler så att agenter kan skapa TCP-anslutningar med varandra. Registernycklarna som skapas av skriptet anger om felsökningsloggarna och sökvägen för loggfilen ska loggas. Skriptet definierar också agenten TCP-port som används för kommunikation. Värdena för dessa nycklar ställs automatiskt in av skriptet. Ändra inte dessa tangenter manuellt. Porten som öppnas som standard är 8084. Du kan använda en anpassad port genom att ange parameterportNumber till skriptet. Använd samma port på alla datorer där skriptet körs. 

    >[!NOTE]
    > Skriptet konfigurerar endast Windows-brandväggen lokalt. Om du har en nätverksbrandvägg kontrollerar du att den tillåter trafik som är avsedd för TCP-porten som används av Network Performance Monitor.

    >[!NOTE]
    > Du behöver inte köra [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell-skriptet för serviceanslutningsövervakare.

    

* **ICMP-protokoll:** Om du väljer ICMP som protokoll för övervakning aktiverar du följande brandväggsregler för att på ett tillförlitligt sätt använda ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurera lösningen 

1. Lägg till lösningen för Nätverksprestandaövervakaren på arbetsytan från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Du kan också använda processen som beskrivs i [Lägg till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md). 
2. Öppna arbetsytan Log Analytics och välj panelen **Översikt.** 
3. Välj panelen **Nätverksprestandaövervakare** med meddelandet *Lösning kräver ytterligare konfiguration*.

   ![Panel för övervakare av nätverksprestanda](media/network-performance-monitor/npm-config.png)

4. På **inställningssidan** visas alternativet att installera Log Analytics-agenter och konfigurera agenter för övervakning i vyn **Gemensamma inställningar.** Som tidigare förklarats, om du har installerat och konfigurerat Log Analytics-agenter, väljer du **installationsvyn** för att konfigurera den kapacitet du vill använda. 

   **Prestandaövervakare:** Välj det protokoll **Default** som ska användas för syntetiska transaktioner i standardprestandaövervakaren och välj **Spara & Fortsätt**. Det här protokollvalet innehåller endast för den systemgenererade standardregeln. Du måste välja protokollet varje gång du skapar en prestandaövervakareregel uttryckligen. Du kan alltid **Default** gå över till standardregelinställningarna på fliken **Prestandaövervakare** (den visas när du har slutfört konfigurationen dag-0) och ändra protokollet senare. Om du inte vill ha prestandaövervakaren kan du **Default** inaktivera standardregeln från standardregelinställningarna på fliken **Prestandaövervakare.**

   ![Vyn Prestandaövervakare](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Service Connectivity Monitor**: Funktionen ger inbyggda förkonfigurerade tester för att övervaka nätverksanslutningen till Office 365 och Dynamics 365 från dina agenter. Välj de Office 365- och Dynamics 365-tjänster som du vill övervaka genom att markera kryssrutorna bredvid dem. Om du vill välja de agenter som du vill övervaka väljer du **Lägg till agenter**. Om du inte vill använda den här funktionen eller vill konfigurera den senare väljer du ingenting och väljer **Spara & Fortsätt**.

   ![Vyn Övervakare för tjänstanslutning](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute Monitor:** Välj **Upptäck nu** för att identifiera alla ExpressRoute-privata peerings som är anslutna till de virtuella nätverken i Azure-prenumerationen som är länkad till den här Log Analytics-arbetsytan. 

   ![Vy över ExpressRoute-övervakare](media/network-performance-monitor/npm-express-route.png)

   När identifieringen är klar visas de identifierade kretsarna och peeringsna i en tabell. 

   ![Konfigurationssida för nätverksprestandaövervakare](media/network-performance-monitor/npm-private-peerings.png)
    
Övervakningen för dessa kretsar och peerings är ursprungligen i ett inaktiverat tillstånd. Välj varje resurs som du vill övervaka och konfigurera övervakning för dem från informationsvyn till höger. Välj **Spara** om du vill spara konfigurationen. Mer information finns i artikeln "Konfigurera ExpressRoute monitoring". 

När installationen är klar tar det 30 minuter till en timme innan data fylls i. Medan lösningen sammanställer data från nätverket visas *meddelandelösningen kräver ytterligare konfiguration* på panelen Översikt **över** nätverksprestanda. När data har samlats in och **Overview** indexerats ändras översiktspanelen och informerar dig om nätverkets hälsa i en sammanfattning. Du kan sedan redigera övervakningen av de noder som Log Analytics-agenter är installerade på, samt de undernät som har identifierats från din miljö.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Redigera övervakningsinställningar för undernät och noder 

Alla undernät med minst en agent installerad visas på fliken **Undernät** på konfigurationssidan. 


Så här aktiverar eller inaktiverar du övervakning av vissa undernät:

1. Markera eller avmarkera kryssrutan bredvid **undernätverks-ID.** Kontrollera sedan att **Användning för övervakning** är markerat eller avmarkerat, beroende på vad som är lämpligt. Du kan markera eller rensa flera undernät. När det inaktiveras övervakas inte undernätverk och agenterna uppdateras för att sluta pinga andra agenter. 
2. Välj de noder som du vill övervaka i ett visst undernätverk. Markera undernätet i listan och flytta de nödvändiga noderna mellan listorna som innehåller oövervakade och övervakade noder. Du kan lägga till en anpassad beskrivning i undernätet.
3. Välj **Spara** om du vill spara konfigurationen. 

#### <a name="choose-nodes-to-monitor"></a>Välj noder att övervaka

Alla noder som har en agent installerad på dem visas på fliken **Noder.** 

1. Markera eller avmarkera de noder som du vill övervaka eller stoppa övervakningen. 
2. Välj **Använd för övervakning**eller avmarkera det, beroende på vad som är lämpligt. 
3. Välj **Spara**. 


Konfigurera de funktioner du vill ha:

- [Prestandaövervakaren](network-performance-monitor-performance-monitor.md#configuration)
- [Övervakare av tjänstanslutning](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute-övervakare](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Information om datainsamling
För att samla in information om förlust och svarstid använder Network Performance Monitor TCP SYN-SYNACK-ACK-handskakningspaket när du väljer TCP som protokoll. Network Performance Monitor använder ICMP ECHO ICMP ECHO REPLY när du väljer ICMP som protokoll. Spårningsväg används också för att få topologiinformation.

I följande tabell visas metoder för datainsamling och annan information om hur data samlas in för Network Performance Monitor.

| Plattform | Direkt agent | Agent för System Center Operations Manager | Azure Storage | Krävs Operations Manager? | Operations Manager-agentdata som skickas via hanteringsgrupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP-handslag/ICMP ECHO-meddelanden var 5:e sekund, data som skickas var tredje minut |
 

 
Lösningen använder syntetiska transaktioner för att bedöma nätverkets hälsa. Log Analytics-agenter installerade vid olika ställen i TCP-paket för nätverksutbyte eller ICMP Echo med varandra. Om agenterna använder TCP-paket eller ICMP Echo beror på vilket protokoll du har valt för övervakning. I processen lär agenter sig om tur-och-retur-tiden och paketförlust, om någon. Med jämna mellanrum utför varje agent också en spårningsväg till andra agenter för att hitta alla olika vägar i nätverket som måste testas. Med hjälp av dessa data kan agenterna härleda nätverksfördröjning och paketförlustsiffror. Testerna upprepas var femte sekund. Data sammanställs i ungefär tre minuter av agenterna innan de överförs till Log Analytics-arbetsytan i Azure Monitor.



>[!NOTE]
> Även om agenter kommunicerar med varandra ofta, genererar de inte betydande nätverkstrafik när de utför testerna. Agenter förlitar sig endast på TCP SYN-SYNACK-ACK-handskakningspaket för att fastställa förlust och svarstid. Inga datapaket utbyts. Under den här processen kommunicerar agenter med varandra endast när det behövs. Agentkommunikationstopologin är optimerad för att minska nätverkstrafiken.

## <a name="use-the-solution"></a>Använd lösningen 

### <a name="network-performance-monitor-overview-tile"></a>Översiktspanel för nätverksprestandaövervakare 

När du har aktiverat lösningen För nätverksprestandaövervakare ger lösningspanelen på sidan **Översikt** en snabb översikt över nätverkets hälsa. 

 ![Översiktspanel för nätverksprestandaövervakare](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Instrumentpanelen Nätverksprestandaövervakare 

* **Top Network Health Events**: Den här sidan innehåller en lista över de senaste hälsohändelserna och aviseringarna i systemet och tiden sedan händelserna har varit aktiva. En hälsohändelse eller avisering genereras när värdet för det valda måttet (förlust, svarstid, svarstid eller bandbreddsanvändning) för övervakningsregeln överskrider tröskelvärdet. 

* **ExpressRoute Monitor**: Den här sidan innehåller hälsosammanfattningar för de olika ExpressRoute-peering-anslutningarna som lösningen övervakar. Panelen **Topologi** visar antalet nätverkssökvägar via ExpressRoute-kretsarna som övervakas i nätverket. Välj den här panelen om du vill gå till **topologivyn.**

* **Service Connectivity Monitor**: Den här sidan innehåller hälsosammanfattningar för de olika tester som du har skapat. Panelen **Topologi** visar antalet slutpunkter som övervakas. Välj den här panelen om du vill gå till **topologivyn.**

* **Prestandaövervakare:** Den här **Network** sidan innehåller hälsosammanfattningar för nätverkslänkar och **undernätslänkar** som lösningen övervakar. Panelen **Topologi** visar antalet nätverkssökvägar som övervakas i nätverket. Välj den här panelen om du vill gå till **topologivyn.** 

* **Vanliga frågor:** Den här sidan innehåller en uppsättning sökfrågor som hämtar data för övervakning av rådsnätverk direkt. Du kan använda dessa frågor som utgångspunkt för att skapa egna frågor för anpassad rapportering. 

   ![Instrumentpanelen Nätverksprestandaövervakare](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Öka detaljnivån för djup 

Du kan välja olika länkar på lösningsinstrumentpanelen för att öka detaljnivån i alla intresseområden. När du till exempel ser en avisering eller en felaktig nätverkslänk visas på instrumentpanelen väljer du den för att undersöka vidare. På en sida visas alla undernätverkslänkar för den aktuella nätverkslänken. Du kan se förlust-, svarstids- och hälsostatus för varje undernätverkslänk. Du kan snabbt ta reda på vilken nätlänk som orsakar problem. Välj **Visa nodlänkar** om du vill visa alla nodlänkar för den felaktiga undernätslänken. Sedan kan du se enskilda nod-till-nod länkar och hitta den felaktiga nod länkar. 

Välj **Visa topologi** om du vill visa hopp-för-hopp-topologin för rutterna mellan käll- och målnoderna. De ohälsosamma rutterna visas i rött. Du kan visa svarstiden som varje hopp har bidragit med så att du snabbt kan identifiera problemet för en viss del av nätverket.

 

### <a name="network-state-recorder-control"></a>Kontroll av nättillståndsinspelare

Varje vy visar en ögonblicksbild av nätverkets hälsa vid en viss tidpunkt. Som standard visas det senaste tillståndet. Fältet högst upp på sidan visar den tidpunkt som tillståndet visas för. Om du vill visa en ögonblicksbild av nätverkets hälsa vid ett tidigare tillfälle väljer du **Åtgärder**. Du kan också aktivera eller inaktivera automatisk uppdatering för alla sidor medan du visar det senaste tillståndet. 

 ![Registrering av nätverkstillstånd](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trenddiagram 

På varje nivå som du detaljgransar nedåt kan du se trenden för det aktuella måttet. Det kan vara förlust, svarstid, svarstid eller bandbreddsanvändning. Om du vill ändra tidsintervallet för trenden använder du tidskontrollen högst upp i diagrammet. 

Trenddiagram visar ett historiskt perspektiv på resultatet för ett prestandamått. Vissa nätverksproblem är tillfälliga till sin natur och är svåra att fånga genom att bara titta på nätverkets aktuella tillstånd. Problem kan dyka upp snabbt och försvinna innan någon märker det, bara för att dyka upp igen vid en senare tidpunkt. Sådana tillfälliga problem kan också vara svåra för programadministratörer. Problemen visas ofta som oförklarliga ökningar i programmets svarstid, även när alla programkomponenter verkar fungera smidigt. 

Du kan enkelt upptäcka den här typen av problem genom att titta på ett trenddiagram. Problemet visas som en plötslig ökning av nätverksfördröjning eller paketförlust. Om du vill undersöka problemet använder du kontrollen Registrerare för nätverkstillstånd för att visa ögonblicksbilden av nätverket och topologin för den tidpunkten då problemet uppstod.

 
![Trenddiagram](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topologi karta 

Network Performance Monitor visar hopp-för-hopp-topologin för rutter mellan källan och målslutpunkten på en interaktiv topologikarta. Om du vill visa topologikartan väljer du panelen **Topologi** på lösningsinstrumentpanelen. Du kan också välja länken **Visa topologi** på de nedrullningsbara sidorna. 

Topologikartan visar hur många vägar som finns mellan källan och målet och vilka sökvägar datapaketen tar. Svarstiden som varje nätverkshopp bidrar med är också synlig. Alla sökvägar för vilka den totala sökvägen ligger över tröskelvärdet (som anges i motsvarande övervakningsregel) visas i rött. 

När du väljer en nod eller hovrar över den på topologikartan visas nodegenskaperna, till exempel FQDN och IP-adress. Välj ett hopp för att se dess IP-adress. Du kan identifiera det besvärliga nätverkshoppet genom att märka den svarstid som den bidrar med. Om du vill filtrera vissa vägar använder du filtren i det hopfällbara åtgärdsfönstret. Om du vill förenkla nätverkstopologierna döljer du mellanliggande hopp med hjälp av skjutreglaget i åtgärdsfönstret. Du kan zooma in eller zooma ut från topologikartan med hjälp av mushjulet. 

Topologin som visas på kartan är toologin för lager 3 och innehåller inte enheter och anslutningar i lager 2. 

 
![Topologi karta](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Logga frågor i Azure Monitor

Alla data som visas grafiskt via instrumentpanelen För nätverksprestandaövervakare och detaljgranskningssidor är också tillgängliga internt i [loggfrågor](../log-query/log-query-overview.md). Du kan utföra interaktiv analys av data i databasen och korrelera data från olika källor. Du kan också skapa anpassade aviseringar och vyer och exportera data till Excel, Power BI eller en delningsbar länk. Området **Vanliga frågor** på instrumentpanelen har några användbara frågor som du kan använda som utgångspunkt för att skapa egna frågor och rapporter. 

## <a name="alerts"></a>Aviseringar

Network Performance Monitor använder aviseringsfunktionerna i [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Det innebär att alla meddelanden hanteras med hjälp av [åtgärdsgrupper](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).  

Om du är en NPM-användare som skapar en avisering via Log Analytics: 
1. Du kommer att se en länk som omdirigerar dig till Azure-portalen. Klicka på den för att komma åt portalen.
2. Klicka på lösningspanelen För nätverksprestandaövervakaren. 
3. Navigera till Konfigurera.  
4. Välj det test som du vill skapa en avisering på och följ nedanstående steg.

Om du är en NPM-användare som skapar en avisering via Azure Portal:  
1. Du kan välja att ange din e-post direkt eller så kan du välja att skapa aviseringar via åtgärdsgrupper.
2. Om du väljer att ange din e-post direkt skapas en åtgärdsgrupp med namnet **NPM Email ActionGroup** och e-post-ID:n läggs till i den åtgärdsgruppen.
3. Om du väljer att använda åtgärdsgrupper måste du välja en tidigare skapad åtgärdsgrupp. Du kan lära dig hur du skapar en åtgärdsgrupp [här.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. När aviseringen har skapats kan du använda länken Hantera aviseringar för att hantera dina aviseringar. 

Varje gång du skapar en avisering skapar NPM en frågebaserad loggaviseringsregel i Azure Monitor. Den här frågan utlöses var 5:e minut som standard. Azure-övervakaren debiteras inte för de första 250 loggvarningsreglerna som skapats, och alla varningsregler ovanför gränsen för 250 loggvarningsregler debiteras enligt [priser för aviseringar i Azure Monitor-pristabellen](https://azure.microsoft.com/pricing/details/monitor/).
Meddelanden debiteras separat enligt [priser för meddelanden på azure monitor-prissidan](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Prissättning

Information om prissättning finns [tillgänglig online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Ge feedback 

* **UserVoice:** Du kan publicera dina idéer för nätverksprestandaövervakarfunktioner som du vill att vi ska arbeta med. Besök [sidan UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Gå med i vår kohort:** Vi är alltid intresserade av att få nya kunder att gå med i vår kohort. Som en del av det får du tidig tillgång till nya funktioner och en möjlighet att hjälpa oss att förbättra Network Performance Monitor. Om du är intresserad av att gå med, fyll i denna [snabba undersökning](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Nästa steg 
Läs mer om [Prestandaövervakare,](network-performance-monitor-performance-monitor.md) [Service Connectivity Monitor](network-performance-monitor-performance-monitor.md)och [ExpressRoute Monitor](network-performance-monitor-expressroute.md). 
