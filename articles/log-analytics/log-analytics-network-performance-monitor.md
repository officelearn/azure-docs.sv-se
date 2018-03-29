---
title: Network Performance Monitor-lösning i Azure | Microsoft Docs
description: Network Performance Monitor i Azure hjälper dig att övervaka prestanda för ditt nätverk i nära realtid för att identifiera och hitta flaskhalsar i nätverket.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 847213a5b8aab908d412ecc9aecec984aec1a9ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Network Performance Monitor-lösning i Azure

![Nätverket Prestandaövervakaren symbol](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Network Performance Monitor är en molnbaserad hybrid nätverk övervakningslösning som hjälper dig att övervaka nätverkets prestanda mellan olika platser i din nätverksinfrastruktur. Du kan också övervaka nätverksanslutning till tjänsten och programslutpunkter och övervaka prestanda för Azure ExpressRoute. 

Network Performance Monitor identifierar nätverksproblem som trafik blackholing routning fel och problem som övervakning metoder för vanliga nätverk inte kan identifiera. Lösningen genererar aviseringar och meddelar dig när ett tröskelvärde komprometteras för en nätverkslänk. Det garanterar snabbt ska kunna identifiera problem med prestanda och localizes källan till problemet till en viss nätverkssegment eller enhet. 

Network Performance Monitor finns tre allmänna funktioner: 

* [Prestandaövervakaren](log-analytics-network-performance-monitor-performance-monitor.md): du kan övervaka nätverksanslutningen mellan molndistributioner och lokala platser, flera datacenter, och filialkontor och verksamhetskritiska program i flera skikt eller mikrotjänster. Du kan identifiera nätverksproblem innan användare klagar med Prestandaövervakaren.

* [Tjänsten Endpoint övervakning](log-analytics-network-performance-monitor-service-endpoint.md): du kan övervaka anslutningen från användarna till de tjänster som du är intresserad av, avgör vilken infrastruktur som finns i sökvägen och identifiera där nätverksflaskhalsar i ska ske. Du kan känna avbrott innan användarna och se den exakta platsen problem längs nätverkssökvägen. 

    Den här funktionen hjälper dig att utföra test baserat på HTTP, HTTPS, TCP och ICMP nära realtid eller tidigare övervaka tillgänglighet och svarstid för din tjänst. Du kan också övervaka bidrag i nätverket i paketförlust och latens. Med en topologisk Nätverkskarta kan du isolera fördröjningar i nätverket. Du kan identifiera problem platser som sker längs sökvägen från noden till tjänsten med latens data på varje hopp. Du kan övervaka nätverksanslutning till Office 365 och Dynamics CRM utan någon förkonfiguration med inbyggda tester. Med den här funktionen kan du övervaka nätverksanslutning till valfri TCP-kapacitet slutpunkt, till exempel webbplatser, SaaS-program, PaaS-program och SQL-databaser.

* [Övervakare för ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): övervaka slutpunkt till slutpunkt-anslutning och prestanda mellan dina lokalkontor och Azure, över Azure ExpressRoute.  

Mer information om de olika funktioner som stöds av [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) är ansluten till.
 
## <a name="supported-regions"></a>Regioner som stöds
NPM Kontrollera anslutningen mellan nätverk och program i alla delar av världen, från en arbetsyta som finns i något av följande områden:
* Västra Europa
* Västra centrala USA
* Östra USA
* Sydostasien
* Syd Östra Australien
* Amerikanska myndigheter Virginia

Listan över stöds regioner för ExpressRoute Övervakare är tillgängliga i den [dokumentationen](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117#regions).


## <a name="set-up-and-configure"></a>Installera och konfigurera

### <a name="install-and-configure-agents"></a>Installera och konfigurera agenter 

Använd basic-processer för att installera agenter på [ansluta Windows-datorer till Azure logganalys](log-analytics-windows-agents.md) och [ansluta Operations Manager till logganalys](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Var du vill installera agenter 

* **Prestandaövervakaren**: Installera Operations Management Suite-agenterna på minst en nod som är ansluten till varje undernätverk som du vill övervaka nätverksanslutning till andra undernät.

    Installera agenter på båda slutpunkterna på länken om du vill övervaka en nätverkslänk. Om du är osäker på om nätverkets topologi, installera agenter på servrar med viktiga arbetsbelastningar som du vill övervaka nätverkets prestanda. Till exempel om du vill övervaka nätverksanslutningen mellan en webbserver och en server som kör SQL, installera en agent på båda servrarna. Agenter övervakar nätverksanslutning (länkar) mellan värdar, inte själva värdarna. 

* **Tjänsten Endpoint övervakning**: Installera en Operations Management Suite-agenten på varje nod som du vill övervaka nätverksanslutningen till tjänstslutpunkten. Ett exempel är om du vill övervaka nätverksanslutning till Office 365 från dina office-platser O1, O2 och O3. Installera Operations Management Suite-agenten på minst en nod varje i O1, O2 och O3. 

* **Övervakare för ExpressRoute**: installera minst en Operations Management Suite-agenten på dina virtuella Azure-nätverket. Även installera minst en agent i din lokala undernätverk som är anslutna via privata ExpressRoute-peering.  

### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Konfigurera Operations Management Suite-agenter för övervakning 

Network Performance Monitor använder syntetiska transaktioner för att övervaka nätverksprestanda mellan käll- och agenter. Du kan välja mellan TCP och ICMP som protokoll för övervakning i Prestandaövervakaren och tjänsten Endpoint övervakning funktioner. TCP används för ExpressRoute-övervakaren. Kontrollera att brandväggen tillåter kommunikation mellan Operations Management Suite-agenter som används för att övervaka på det protokoll som du väljer. 

* **TCP-protokollet**: Om du väljer TCP som protokoll för övervakning, öppna brandväggsport för agenterna som används för Prestandaövervakaren för nätverket och ExpressRoute övervakning för att kontrollera att agenterna kan ansluta till varandra. Om du vill öppna porten, kör du skriptet EnableRules.ps1 PowerShell utan några parametrar i ett PowerShell-fönster med administratörsbehörighet.

    Skriptet skapar registernycklar som krävs av lösningen. Det skapar också Windows-brandväggsreglerna för att tillåta agenter för att skapa TCP-anslutningar med varandra. Registernycklar som skapats av skriptet ange om du vill logga debug-loggar och sökvägen för filen loggar. Skriptet definierar också agent TCP-port som används för kommunikation. Skriptet anges automatiskt värden för nycklarna. Manuellt ändras inte de här nycklarna. Porten öppnas som standard är 8084. Du kan använda en anpassad port genom att ange parametern-portnummer i skriptet. Använd samma port på alla datorer där skriptet körs. 

    >[!NOTE]
    > Skriptet konfigurerar bara Windows-brandväggen lokalt. Om du har en brandvägg för nätverk, kontrollerar du att den tillåter trafik för TCP-porten som används av Network Performance Monitor.

    >[!NOTE]
    > Du behöver inte köra EnableRules.ps1 PowerShell-skript för tjänsten Endpoint för övervakning.

    

* **ICMP-protokollet**: Om du väljer ICMP som protokoll för övervakning, kan du aktivera följande brandväggsregler tillförlitligt kan utnyttja ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurera lösningen 

1. Lägga till Network Performance Monitor-lösningen till arbetsytan från den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Du kan också använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). 
2. Öppna logganalys-arbetsytan och välj den **översikt** panelen. 
3. Välj den **Network Performance Monitor** panelen med meddelandet *lösningen kräver ytterligare konfiguration*.

   ![Prestandaövervakaren nätverksikon](media/log-analytics-network-performance-monitor/npm-config.png)

4. På den **installationsprogrammet** kan du se alternativet att installera Operations Management Suite-agenter och konfigurera agenter för övervakning i den **gemensamma inställningar för** vyn. Som tidigare förklarats om du har installerat och konfigurerat Operations Management Suite agenter, väljer du den **installationsprogrammet** vyn för att konfigurera kapacitet du vill använda. 

   **Prestandaövervakaren**: Välj protokollet som ska användas för syntetiska transaktioner i den **standard** Prestandaövervakaren regeln och välj **Spara & fortsätta**. Det här protokollet valet innehåller endast för systemgenererade Standardregeln. Du måste välja protokollen varje gång du skapar en regel för Prestandaövervakaren explicit. Du kan alltid flytta till den **standard** regel inställningar för den **Prestandaövervakaren** fliken (det visas när du har slutfört konfigurationen dag 0) och senare ändrar protokollet. Om du inte vill rPerfomance bildskärmar, kan du inaktivera Standardregeln från den **standard** regel inställningar för den **Prestandaövervakaren** fliken.

   ![Visning av Prestandaövervakaren](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
   **Tjänsten Endpoint övervakning**: kapacitet innehåller inbyggda förkonfigurerade tester för att övervaka nätverksanslutningen till Office 365 och Dynamics 365 från dina agenter. Välj de Office 365 och Dynamics 365-tjänster som du vill övervaka genom att markera kryssrutorna bredvid de. Om du vill välja agenterna som du vill övervaka, Välj **lägga till agenter**. Om du inte vill använda den här funktionen eller vill ställa in den senare inte väljer något och väljer **Spara & fortsätta**.

   ![Tjänsten Endpoint övervakning vy](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

   **Övervakare för ExpressRoute**: Välj **identifiera nu** att identifiera alla ExpressRoute privata peerkopplingar som är anslutna till de virtuella nätverken i Azure-prenumeration som är kopplad till den här logganalys-arbetsytan. 

   >[!NOTE] 
   > Lösningen identifierar för närvarande endast ExpressRoute privata peerkopplingar. 

   >[!NOTE] 
   > Endast identifieras privata peerkopplingar som är anslutna till de virtuella nätverk som är associerade med prenumerationen som är kopplad till den här logganalys-arbetsytan. Om ExpressRoute är ansluten till virtuella nätverk utanför den prenumeration som är kopplad till den här arbetsytan kan du skapa logganalys-arbetsytan i dessa prenumerationer. Använda Prestandaövervakaren för nätverket för att övervaka de peerkopplingar.

   ![Övervakare för ExpressRoute-vyn](media/log-analytics-network-performance-monitor/npm-express-route.png)

   När identifieringen har slutförts visas identifierade privata peerkopplingar i en tabell. 

   ![Sidan för Performance Monitor-konfiguration av nätverk](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
Övervakning av dessa peerkopplingar är först i ett inaktiverat tillstånd. Markera varje peering som du vill övervaka och konfigurera övervakning av dem från Informationsvyn till höger. Välj **spara** att spara konfigurationen. Mer information finns i ”Konfigurera ExpressRoute övervakning” artikeln. 

När installationen är klar, tar 30 minuter till en timme att fylla i data. När lösningen samlar in data från nätverket, meddelandet *lösningen kräver ytterligare konfiguration* på Network Performance Monitor **översikt** panelen. När data samlas in och indexerad, den **översikt** panelen ändringar och informerar dig om dina nätverkets tillstånd i en sammanfattning. Du kan sedan redigera övervakningen av noderna på vilka Operations Management Suite agenter är installerade och undernät som identifieras från din miljö.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Redigera övervakningsinställningarna för undernät och noder 

Alla undernät med minst en agent installerad visas på den **undernätverk** fliken på konfigurationssidan. 


Aktivera eller inaktivera övervakning av specifika undernät:

1. Markera eller avmarkera kryssrutan bredvid den **undernätverk ID**. Kontrollera att **för övervakning** är markerad eller avmarkerad, vid behov. Du kan markera eller avmarkera flera undernät. När den är inaktiverad, undernät övervakas inte och agenterna har uppdaterats för att stoppa pinga andra agenter. 
2. Välj de noder som du vill övervaka i en viss undernätverk. Välj undernätverk i listan och flytta krävs noderna mellan listor som innehåller oövervakade och övervakade noder. Du kan lägga till en egen beskrivning av undernätverk.
3. Välj **spara** att spara konfigurationen. 

#### <a name="choose-nodes-to-monitor"></a>Välj noder för att övervaka

Alla noder som har en agent som installerats på de visas på den **noder** fliken. 

1. Markera eller avmarkera de noder som du vill övervaka eller stoppa övervakningen. 
2. Välj **för övervakning**, eller avmarkera den efter behov. 
3. Välj **Spara**. 


Konfigurera de funktioner du vill:

- [Prestandaövervakaren](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Tjänsten Endpoint för övervakning](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Övervakare för ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Information om samlingen
Om du vill samla in information för förluster eller fördröjningar, använder Network Performance Monitor TCP SYN-SYNACK-ACK handskakning paket när du väljer TCP som protokoll. Network Performance Monitor använder ICMP ECHO ICMP ECHO SVARSMEDDELANDEN när du väljer ICMP som protokoll. Spåra väg används också för att hämta information om nätverkstopologin.

I följande tabell visar metoder för insamling av data och annan information om hur data samlas in för Network Performance Monitor.

| Plattform | Styr agent | System Center Operations Manager-agenten | Azure Storage | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP-handskakningar på/ICMP ECHO-meddelanden var femte sekund data skickas var 3: e minut |
 

 
Lösningen använder syntetiska transaktioner för att utvärdera hälsan för nätverket. Operations Management Suite-agenter installerade på olika ställen i nätverkspaket exchange TCP eller ICMP Echo med varandra. Om agenterna använder TCP-paket eller ICMP Echo beror på det protokoll som du har valt för övervakning. I processen dig agenter fram och åter tid och paket förlust, eventuella. Varje agent med jämna mellanrum, utför även en spårningsrutt för att andra agenter för att hitta alla olika vägar i nätverket som ska testas. Med dessa data kan härleda agenterna Nätverksfördröjningen och paket förlustsiffror. Testerna upprepas var femte sekund. Data sammanställs av agenterna för cirka tre minuter innan det överförs till Log Analytics-tjänsten.



>[!NOTE]
> Även om agenter är ofta kommunicerar med varandra, kan de inte genererar mycket nätverkstrafik när du utför testerna. Agenter använda enbart TCP SYN-SYNACK-ACK handskakning paket för att fastställa förluster eller fördröjningar. Inga datapaket utbyts. Under den här processen kommunicerar agenter med varandra endast vid behov. Kommunikationstopologin agent är optimerad för att minska nätverkstrafiken.

## <a name="use-the-solution"></a>Använda lösningen 

### <a name="network-performance-monitor-overview-tile"></a>Översikt över övervaka prestanda för nätverksikon 

När du aktiverar Network Performance Monitor-lösning, lösningen panelen på den **översikt** sidan ger en snabb överblick över nätverkets tillstånd. 

 ![Översikt över övervaka prestanda för nätverksikon](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Nätverket Prestandaövervakaren instrumentpanelen 

* **De främsta händelser på nätverket hälsa**: den här sidan innehåller en lista över de senaste hälsa händelser och aviseringar i systemet och hur lång tid eftersom händelserna som har varit aktivt. En hälsohändelse eller en varning genereras när värdet för övervakning regeln valda måttet (förlust, svarstid, svarstid eller bandbreddsanvändningen) överstiger tröskelvärdet. 

* **Övervakare för ExpressRoute**: den här sidan innehåller hälsa sammanfattningar för olika anslutningar för ExpressRoute-peering lösningen bildskärmar. Den **topologi** panelen visar antalet nätverkssökvägar via ExpressRoute-kretsar som övervakas i nätverket. Välj den här panelen att gå till den **topologi** vyn.

* **Tjänsten Endpoint övervakning**: den här sidan innehåller hälsa sammanfattningar för de olika testerna som du skapade. Den **topologi** panelen visar antalet slutpunkter som ska övervakas. Välj den här panelen att gå till den **topologi** vyn.

* **Prestandaövervakaren**: den här sidan innehåller hälsa sammanfattningar för den **nätverk** länkar och **undernätverk** länkar som övervakar lösningen. Den **topologi** panelen visar antalet nätverkssökvägar som övervakas i nätverket. Välj den här panelen att gå till den **topologi** vyn. 

* **Vanliga frågor**: den här sidan innehåller en uppsättning sökfrågor som hämta rådata för nätverksövervakning data direkt. Du kan använda de här frågorna som utgångspunkt för att skapa egna frågor för anpassad rapportering. 

   ![Nätverket Prestandaövervakaren instrumentpanelen](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>detaljnivån för djup 

Du kan välja olika länkar på instrumentpanelen lösningen att gå ner djupare i alla intresseområde. När du ser en avisering eller ett feltillstånd nätverkslänken som visas på instrumentpanelen, väljer du exempelvis att undersöka vidare. En sida visar alla undernätverkslänkar för nätverks-länk. Du kan se förlust, svarstid och hälsostatus för varje undernätverk länk. Du kan snabbt ta reda vilka undernätverk länken orsakar problem. Välj **visa nodlänkar** att se alla länkar i noden för länken ohälsosamt undernät. Du kan sedan se enskilda nod till nod länkar och hitta nodlänkar. 

Välj **visa topologi** att visa hopp av hopp topologin vägar mellan käll- och noderna. Feltillstånd vägar visas i rött. Du kan visa den svarstid som har bidragit med varje hopp så att du snabbt kan identifiera problemet till en viss del av nätverket.

 

### <a name="network-state-recorder-control"></a>Nätverket lådan kontroll

Varje visas en ögonblicksbild av nätverkets tillstånd vid en viss tidpunkt. Senaste status visas som standard. Längst upp på sidan visar punkten i tiden som status visas för. Om du vill visa en ögonblicksbild av nätverkets tillstånd vid ett tidigare tillfälle, Välj **åtgärder**. Du kan också aktivera eller inaktivera automatisk uppdatering för en sida när du visar det aktuella tillståndet. 

 ![Network State Recorder](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trend diagram 

Du kan se trend tillämpliga mått på varje nivå som du detaljnivån. Det kan vara förlust, svarstid, svarstid eller bandbreddsanvändningen. Om du vill ändra tidsintervallet för trenden, använder du kontrollen tid överst i diagrammet. 

Trend diagram visar en historisk perspektiv prestanda för ett mått för prestanda. Vissa nätverksproblem är tillfälligt till sin natur och är svåra att fånga genom att titta på det aktuella tillståndet för nätverket. Problem kan ansluta snabbt och försvinner innan alla meddelanden enbart för att visas igen vid en senare tidpunkt. Sådana tillfälliga problem kan också vara svårt för administratörer. Problemen ofta visas som oförklarade ökningen av svarstiden för programmet, även när alla programkomponenter ut att fungera smidigt. 

Du kan enkelt identifiera dessa typer av problem genom att titta på ett trenddiagram. Problemet visas som en plötslig topp Nätverksfördröjningen eller paketförlust. Om du vill undersöka problemet använder du nätverket tillstånd lådan kontrollen för att visa nätverk ögonblicksbild och topologin för den tidpunkten och tidpunkt när problemet uppstod.

 
![Trend diagram](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topologisk karta 

Network Performance Monitor visas topologin som hopp av hopp av routinginformation mellan käll- och slutpunkt på en interaktiv topologisk karta. Om du vill visa topologisk karta, Välj den **topologi** panelen på instrumentpanelen för lösningen. Du kan också välja den **visa topologi** länk på sidorna nedåt. 

Topologisk karta visar hur många flöden är mellan källa och mål och vad sökvägar datapaketen ta. Fördröjningen av varje nätverk hopp visas också. Alla sökvägar som är den totala sökväg svarstiden tröskelvärdet (anges i motsvarande övervakning regel) visas i rött. 

När du väljer en nod eller hovra över den på topologisk karta finns nodegenskaper, till exempel FQDN och IP-adressen. Välj ett hopp till finns i dess IP-adress. Du kan identifiera program som krånglar nätverket hop genom meddela svarstiden bidrar. Använda filter för att filtrera särskilt vägar i åtgärdsrutan döljas. Dölj mellanliggande hopp med hjälp av skjutreglaget i åtgärdsfönstret för att förenkla nätverkstopologier. Du kan zooma in eller ut ur topologisk karta med mushjulet. 

Topologin visas på kartan är nivå 3-topologi och innehåller inte nivå 2-enheter och anslutningar. 

 
![Topologisk karta](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Logganalys Sök 

Alla data som är exponerade grafiskt via Network Performance Monitor-instrumentpanelen och nedåt sidor finns också internt i [logganalys söka](log-analytics-log-search-new.md). Du kan utföra interaktiv analys av data i databasen och korrelera data från olika källor. Du kan också skapa anpassade aviseringar och vyer och exportera data till Excel, Power BI eller delbart länk. Den **vanliga frågor** område i instrumentpanelen har vissa användbara frågor som du kan använda som utgångspunkt för att skapa egna frågor och rapporter. 

 

## <a name="provide-feedback"></a>Ge feedback 

* **UserVoice:** du kan publicera dina idéer för Network Performance Monitor-funktioner som du vill att vi ska arbeta med. Besök den [UserVoice sidan](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Ansluta till vår kommittén:** vi alltid är intresserad av att nya kunder ansluta till vår kommittén. Som en del av det får du snabb åtkomst till nya funktioner och möjlighet att hjälpa oss att förbättra Network Performance Monitor. Om du är intresserad av att ansluta till, fyller du i detta [snabb undersökning](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Nästa steg 
Lär dig mer om [Prestandaövervakaren](log-analytics-network-performance-monitor-performance-monitor.md), [Endpoint Tjänstövervakare](log-analytics-network-performance-monitor-performance-monitor.md), och [ExpressRoute övervakaren](log-analytics-network-performance-monitor-expressroute.md). 
