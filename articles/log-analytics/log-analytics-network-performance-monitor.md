---
title: Network Performance Monitor-lösning i Azure | Microsoft Docs
description: Övervakare av nätverksprestanda i Azure kan du övervaka prestanda för dina nätverk i nära realtid för att identifiera och hitta flaskhalsar i prestanda.
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
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: na
ms.openlocfilehash: 3640f5bb2c2e9457e269bd189cbec3b627ee7349
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626880"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Network Performance Monitor-lösning i Azure

![Network Performance Monitor symbol](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Network Performance Monitor är en molnbaserad hybridnätverk övervakningslösning som hjälper dig att övervaka nätverkets prestanda mellan olika platser i din nätverksinfrastruktur. Du kan också övervaka nätverksanslutningar till tjänsten och slutpunkterna och övervaka prestanda för Azure ExpressRoute. 

Övervakare av nätverksprestanda identifierar nätverksproblem som trafik blackholing, routning fel och problem som konventionella nätverk övervakning metoder som inte kan identifiera. Lösningen genererar aviseringar och meddelar dig när en tröskelvärdet överskrids för en nätverkslänk. Det säkerställer rätt tid identifiering av problem med nätverksprestanda och localizes orsaken till problemet till en viss nätverkssegment eller enhet. 

Övervakare av nätverksprestanda innehåller tre breda funktioner: 

* [Övervakare av nätverksprestanda](log-analytics-network-performance-monitor-performance-monitor.md): du kan övervaka nätverksanslutningar i molndistributioner och lokala platser, flera datacenter, och olika avdelningskontor och verksamhetskritiska program i flera skikt eller mikrotjänster. Med Prestandaövervakaren kan du identifiera nätverksproblem innan användare börjar klaga.

* [Tjänsten Anslutningsövervakare](log-analytics-network-performance-monitor-service-endpoint.md): du kan övervaka anslutningen från användarna till de tjänster du bryr dig om, fastställa vilken infrastruktur som finns i sökvägen och identifiera där network flaskhalsar inträffa. Du kan veta om avbrott innan dina användare och var exakt problemen finns längs med nätverkssökvägen. 

    Den här funktionen kan du utföra tester baserat på HTTP, HTTPS, TCP och ICMP för att övervaka i nära realtid eller historiskt tillgänglighet och svarstid för din tjänst. Du kan också övervaka bidraget för nätverket i paketförlust och fördröjning. Med en topologisk Nätverkskarta kan du isolera nätverk kapacitetssänkningar. Du kan identifiera problem vinklar som inträffar längs nätverkssökvägen från noden till tjänsten med svarstidsdata på varje hopp. Med inbyggda tester kan du övervaka nätverksanslutning till Office 365 och Dynamics CRM utan någon förkonfiguration. Med den här funktionen kan du övervaka nätverksanslutning till alla TCP-kompatibla slutpunkt, till exempel webbplatser, SaaS-program, PaaS-program och SQL-databaser.

* [ExpressRoute-övervakning](log-analytics-network-performance-monitor-expressroute.md): Övervaka anslutningar slutpunkt till slutpunkt och prestanda mellan dina avdelningskontor och Azure, via Azure ExpressRoute.  

Mer information om de olika funktionerna som stöds av [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview) finns tillgänglig online.
 
## <a name="supported-regions"></a>Regioner som stöds
NPM kan övervaka anslutningen mellan nätverk och program i andra delar av världen, från en arbetsyta som är värd för någon av följande regioner:
* Europa, västra
* Västra centrala USA
* USA, östra
* Östra Japan
* Asien, sydöstra
* Södra Australien, östra
* Södra Storbritannien
* Government Virginia (USA)

Regioner som listan över stöds för ExpressRoute-övervakning är tillgängligt i den [dokumentation](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117#regions).


## <a name="set-up-and-configure"></a>Installera och konfigurera

### <a name="install-and-configure-agents"></a>Installera och konfigurera agenter 

Använd basic-processer för att installera agenter på [ansluta Windows-datorer till Azure Log Analytics](log-analytics-windows-agents.md) och [ansluta Operations Manager till Log Analytics](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Var du vill installera agenter 

* **Övervakare av nätverksprestanda**: Installera Operations Management Suite-agenterna på minst en nod kopplad till varje undernätverk som du vill övervaka nätverksanslutningar till andra undernätverk.

    För att övervaka en nätverkslänk, installera agenter på båda slutpunkterna för länken. Om du är osäker på om topologin för ditt nätverk, kan du installera agenter på servrar med kritiska arbetsbelastningar som du vill övervaka nätverkets prestanda. Till exempel om du vill övervaka nätverksanslutningen mellan en webbserver och en server som kör SQL, installera en agent på båda servrarna. Agenter övervakar nätverksanslutningen (länkar) mellan värdar, inte själva värdarna. 

* **Tjänsten Anslutningsövervakare**: Installera en Operations Management Suite-agenten på varje nod som du vill övervaka nätverksanslutningar till tjänsteslutpunkt. Ett exempel är om du vill övervaka nätverksanslutningar till Office 365 från office platserna O1, O2 och O3. Installera Operations Management Suite-agenten på minst en nod i O1 och O2 O3. 

* **ExpressRoute-övervakning**: installera minst en Operations Management Suite-agenten i Azure-nätverk. Även installera minst en agent i din lokala undernätverket som är anslutna via ExpressRoute privat peering.  

### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Konfigurera Operations Management Suite-agenter för övervakning 

Övervakare av nätverksprestanda använder syntetiska transaktioner för att övervaka nätverksprestanda mellan käll- och agenter. Du kan välja mellan TCP och ICMP som protokoll för övervakning i Prestandaövervakaren och tjänsten Anslutningsövervakare. Endast TCP är tillgängligt som övervakning protokoll för ExpressRoute-övervakning. Kontrollera att brandväggen tillåter kommunikation mellan Operations Management Suite-agenter som används för övervakning på det protokoll som du väljer. 

* **TCP-protokollet**: Om du väljer TCP som protokoll för övervakning, öppna brandväggsporten för agenterna som används för övervakning av nätverksprestanda och ExpressRoute-övervakning för att kontrollera att agenterna kan ansluta till varandra. Öppna porten genom att köra den [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell-skript utan några parametrar i ett PowerShell-fönster med administratörsbehörighet.

    Skriptet skapar registernycklar som krävs av lösningen. Det skapar också Windows brandväggsregler för att tillåta agenter att skapa TCP-anslutningar med varandra. De registernycklar som skapats av skriptet anger du om du vill logga debug-loggarna och sökvägen för filen loggar. Skriptet definierar också agenten TCP-port som används för kommunikation. Värdena för dessa nycklar anges automatiskt av skriptet. Inte manuellt ändra de här nycklarna. Den port som öppnas som standard är 8084. Du kan använda en anpassad port genom att ange parametern-portnummer till skriptet. Använd samma port på alla datorer där skriptet körs. 

    >[!NOTE]
    > Skriptet konfigurerar bara Windows-brandväggen lokalt. Om du har en brandvägg kan du kontrollera att den tillåter trafik till TCP-port som används av Övervakare av nätverksprestanda.

    >[!NOTE]
    > Du behöver inte köra den [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell-skript för övervakning av tjänstens slutpunkt.

    

* **ICMP-protokollet**: Om du väljer ICMP som protokoll för övervakning, kan du aktivera följande brandväggsregler på ett tillförlitligt sätt använder ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurera lösningen 

1. Lägga till Network Performance Monitor-lösning för din arbetsyta från den [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Du kan också använda metoden som beskrivs i [lägga till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md). 
2. Öppna Log Analytics-arbetsytan och välj den **översikt** panelen. 
3. Välj den **Övervakare av nätverksprestanda** panelen med meddelandet *ytterligare konfiguration krävs för lösningen*.

   ![Övervakare av nätverksprestanda nätverksikon](media/log-analytics-network-performance-monitor/npm-config.png)

4. På den **installationsprogrammet** du se att alternativet att installera Operations Management Suite-agenter och konfigurera agenter för övervakning i den **gemensamma inställningar för** vy. Som tidigare förklarats om du har installerat och konfigurerat Operations Management Suite-agenter, väljer den **installationsprogrammet** vy för att konfigurera funktionen du vill använda. 

   **Övervakare av nätverksprestanda**: Välj protokollet som ska användas för syntetiska transaktioner i den **standard** Prestandaövervakaren regel och välj **spara och fortsätt**. Den här protokollval innehåller endast för systemgenererade Standardregeln. Du måste välja protokollet som varje gång du skapar en regel för Prestandaövervakaren uttryckligen. Du alltid gå till den **standard** regel inställningar för den **Prestandaövervakaren** fliken (den visas när du har slutfört konfigurationen dag 0) och senare ändrar protokollet. Om du inte vill rPerfomance bildskärmar, kan du inaktivera Standardregeln från den **standard** regel inställningar för den **Prestandaövervakaren** fliken.

   ![Visning av Prestandaövervakaren](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
   **Tjänsten Anslutningsövervakare**: funktionen ger inbyggd förkonfigurerade tester för att övervaka nätverksanslutningar till Office 365 och Dynamics 365 från dina agenter. Välj de Office 365 och Dynamics 365-tjänster som du vill övervaka genom att markera kryssrutorna bredvid dem. Om du vill välja de agenter som du vill övervaka väljer **Lägg till agenter**. Om du inte vill använda denna funktion eller vill du konfigurera den senare inte väljer något och väljer **spara och fortsätt**.

   ![Övervakning av tjänstens slutpunkt vy](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute-övervakning**: Välj **identifiera nu** att identifiera alla ExpressRoute privat peering-sessioner som är anslutna till de virtuella nätverken i Azure-prenumeration som är kopplad till den här Log Analytics-arbetsytan. 

   >[!NOTE] 
   > Lösningen identifierar för närvarande endast ExpressRoute privat peering-sessioner. 

   >[!NOTE] 
   > Endast identifieras privata peering-sessioner som är anslutna till virtuella nätverk som är associerade med prenumerationen är kopplad till den här Log Analytics-arbetsytan. Om ExpressRoute är ansluten till virtuella nätverk utanför den prenumeration som är länkad till den här arbetsytan kan du skapa en Log Analytics-arbetsyta i dessa prenumerationer. Du kan använda Övervakare av nätverksprestanda för att övervaka de peer-kopplingar.

   ![Visa ExpressRoute-övervakning](media/log-analytics-network-performance-monitor/npm-express-route.png)

   När identifieringen har slutförts visas identifierade privata peerkopplingar i en tabell. 

   ![Sidan konfiguration av övervakaren av nätverksprestanda](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
Övervakning för dessa peer-kopplingar är först i ett inaktiverat tillstånd. Välj varje peering som du vill övervaka och konfigurera övervakning för dem från Informationsvyn till höger. Välj **spara** att spara konfigurationen. Mer information finns i artikeln ”konfigurera ExpressRoute-övervakning”. 

När installationen är klar tar 30 minuter till en timme innan informationen som ska visas. Medan lösningen sammanställer data från ditt nätverk, visas meddelandet *ytterligare konfiguration krävs för lösningen* på Network Performance Monitor **översikt** panelen. När data samlas in och indexeras, den **översikt** panelen ändras och informerar dig om din nätverkets tillstånd i en sammanfattning. Du kan sedan redigera övervakning av noderna på vilka Operations Management Suite-agenter är installerade, samt de undernät som identifieras från din miljö.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Redigera övervakningsinställningar för undernät och noder 

Alla undernät med minst en installerad agent visas på den **undernätverk** fliken på konfigurationssidan. 


Aktivera eller inaktivera övervakning av specifika undernätverk:

1. Markera eller avmarkera kryssrutan bredvid den **undernätverks-ID**. Kontrollera att **för övervakning** är markerad eller avmarkerad, efter behov. Du kan markera eller avmarkera flera undernät. När inaktiverad, undernätverk övervakas inte och agenterna har uppdaterats för att stoppa pinga andra agenter. 
2. Välj de noder som du vill övervaka i en viss undernätverket. Välj undernätverket i listan och flytta noderna som krävs mellan de listor som innehåller oövervakade och övervakade noder. Du kan lägga till en beskrivning av anpassad undernätverket.
3. Välj **spara** att spara konfigurationen. 

#### <a name="choose-nodes-to-monitor"></a>Välj noder att övervaka

Alla noder som har en agent installerad visas på den **noder** fliken. 

1. Markera eller avmarkera de noder som du vill övervaka eller stoppa övervakningen. 
2. Välj **för övervakning**, eller avmarkera det, efter behov. 
3. Välj **Spara**. 


Konfigurera de funktioner du vill:

- [Övervakare av nätverksprestanda](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Övervakning av tjänstens slutpunkt](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute-övervakning](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Information om insamling av data
Om du vill samla in information för förlust och fördröjning, använder Övervakare av nätverksprestanda TCP SYN-SYNACK-ACK handskakning paket när du väljer TCP som protokoll. Övervakare av nätverksprestanda använder ICMP ECHO ICMP ECHO SVARSMEDDELANDEN när du väljer ICMP som protokoll. Vägspårning används också för att hämta topologiinformation om.

I följande tabell visas data samlingsmetoder och annan information om hur data samlas in för Övervakare av nätverksprestanda.

| Plattform | Direktagent | System Center Operations Manager-agenten | Azure-lagring | Operations Manager som krävs? | Operations Manager agent-data skickas via hanteringsgruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP handskakningar/ICMP ECHO meddelanden var femte sekund data skickas var 3: e minut |
 

 
Lösningen använder syntetiska transaktioner för att utvärdera hälsotillståndet i nätverket. Operations Management Suite-agenter installerade vid olika tidpunkter i nätverkspaket exchange TCP eller ICMP Echo med varandra. Om agenterna använder TCP-paket eller ICMP Echo beror på det protokoll som du har valt för övervakning. I processen Läs agenter fram och åter tid och paket förlust, om sådana. Varje agent utför regelbundet, en spårningsrutt för att andra agenter att hitta alla olika vägar i nätverket som ska testas. Med dessa data kan härleda agenterna nätverksfördröjning och paket förlustsiffror. Testerna upprepas var femte sekund. Data sammanställs av agenter för cirka tre minuter innan det överförs till Log Analytics-tjänsten.



>[!NOTE]
> Även om agenter är ofta kommunicerar med varandra, genererar de inte mycket nätverkstrafik när du utför testerna. Agenter använda enbart TCP SYN-SYNACK-ACK handskakning paket för att fastställa förlust och fördröjning. Inga datapaket har angetts. Under den här processen agenter som kommunicerar med varandra enbart när det behövs. Agenten kommunikationstopologin är optimerad för att minska nätverkstrafiken.

## <a name="use-the-solution"></a>Använd lösningen 

### <a name="network-performance-monitor-overview-tile"></a>Övervaka Prestandaöversikt nätverksikon 

När du aktiverar Network Performance Monitor-lösningen lösningen panelen på den **översikt** sidan ger en snabb överblick över nätverkets tillstånd. 

 ![Övervaka Prestandaöversikt nätverksikon](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Network Performance Monitor-instrumentpanel 

* **De främsta Hälsohändelser i nätverk**: den här sidan innehåller en lista över de senaste health-händelser och aviseringar i systemet och hur lång tid eftersom händelserna har varit aktiva. En hälsohändelse eller en varning genereras när värdet för övervakningsregel för den valda mått (förlust, svarstid, svarstiden eller nyttjandet av bandbredd) överstiger tröskelvärdet. 

* **ExpressRoute-övervakning**: den här sidan innehåller hälsotillstånd sammanfattningar för olika anslutningar för ExpressRoute-peering lösningen Övervakare. Den **topologi** panel visar antalet sökvägar på nätverket via ExpressRoute-kretsar som övervakas i nätverket. Välj den här panelen för att gå till den **topologi** vy.

* **Tjänsten Anslutningsövervakare**: den här sidan innehåller hälsotillstånd sammanfattningar för de olika testerna som du skapade. Den **topologi** panel visar antalet slutpunkter som övervakas. Välj den här panelen för att gå till den **topologi** vy.

* **Övervakare av nätverksprestanda**: den här sidan innehåller sammanfattningar av hälsotillståndet för den **nätverk** länkar och **undernätverk** länkar som övervakar lösningen. Den **topologi** panel visar antalet sökvägar på nätverket som övervakas i nätverket. Välj den här panelen för att gå till den **topologi** vy. 

* **Vanliga frågor om**: den här sidan innehåller en uppsättning sökfrågor som hämtar rådata för nätverksövervakning data direkt. Du kan använda de här frågorna som utgångspunkt för att skapa egna frågor för anpassad rapportering. 

   ![Network Performance Monitor-instrumentpanel](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Öka detaljnivån för djup 

Du kan välja olika länkar på instrumentpanelen för lösningen att öka detaljnivån ned djupare till alla delar av intresse. Till exempel när du ser en avisering eller en länk för nätverkslänkarna som visas på instrumentpanelen, Välj den för att undersöka vidare. En sida visar en lista över alla undernätslänkar för nätverks-länk. Du kan se den förlust, svarstid och hälsotillståndet för varje undernätverkslänk. Du kan snabbt ta reda vilka undernätverkslänk orsakar problem. Välj **visa nodlänkar** att se alla nodlänkar för länken feltillstånd undernät. Du kan sedan se enskilda nod till nod-länkar och hitta nodlänkar som innehåller fel. 

Välj **visa topologi** att visa hopp för hopp-topologi vägar mellan käll- och målnoder. Felaktiga vägar visas i rött. Du kan visa den svarstid som tillförts av varje hopp så att du snabbt kan identifiera problemet till en viss del av nätverket.

 

### <a name="network-state-recorder-control"></a>Nätverket Recorder kontroll

Varje vy visar en ögonblicksbild av nätverkets tillstånd vid en viss tidpunkt. Som standard visas det senaste tillståndet. I fältet högst upp på sidan visar punkten i tiden för vilken status visas. Om du vill visa en ögonblicksbild av nätverkets tillstånd vid ett tidigare tillfälle, Välj **åtgärder**. Du kan också aktivera eller inaktivera automatisk uppdatering för en sida medan du visar det senaste tillståndet. 

 ![Nätverket Recorder](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trenddiagram 

Du kan se trenden för den tillämpliga måtten på varje nivå som du öka detaljnivån. Det kan vara förlust, svarstid, svarstiden eller bandbreddsanvändningen. Du kan ändra tidsintervallet för trenden med kontrollen tid överst i diagrammet. 

Trenddiagram visar en historisk översikt över prestandan hos en prestandamått. Vissa nätverksproblem är tillfälliga sin natur och är svåra att fånga upp genom att titta på det aktuella tillståndet för nätverket. Problem kan lyfta fram snabbt och försvinner innan alla meddelanden, endast för att visas igen vid en senare tidpunkt. Sådana tillfälliga problem kan också vara svårt för administratörer. Problem som ofta visas som oförklarade ökningen av svarstiden för programmet, även när alla programkomponenter visas att köras. 

Du kan enkelt identifiera dessa typer av problem genom att titta på ett trenddiagram. Problemet visas som en plötslig insamling i nätverksfördröjning eller paketförlust. Använda nätverket tillstånd Recorder kontrollen för att visa nätverk ögonblicksbild och topologi för den tidpunkten och tidpunkt när problemet uppstod för att undersöka problemet.

 
![Trenddiagram](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topologisk karta 

Övervakare av nätverksprestanda visar hopp för hopp-topologin i vägar mellan käll- och slutpunkten på en interaktiv topologisk karta. Om du vill visa topologisk karta, Välj den **topologi** panelen på instrumentpanelen för lösningen. Du kan också välja den **visa topologi** länk på nedåt-sidor. 

Topologisk karta som visar hur många vägar är mellan källan och målet och vad sökvägar datapaketen ta. Svarstiden från varje hopp för nätverk visas också. Alla sökvägar som den totala sökväg svarstiden är över tröskelvärdet (som anges i övervakningsregel för motsvarande) visas i rött. 

När du väljer en nod eller hovrar över den på kartan topologi kan se du nodegenskaper kan till exempel FQDN och IP-adress. Välj ett hopp för att se dess IP-adress. Du kan identifiera problematiska nätverk-hop genom att upptäcka den svarstid som den bidrar. Använda filter för att filtrera specifika vägar i komprimerbart åtgärdsfönstret. Dölj mellanliggande hopp för att förenkla nätverkstopologier med hjälp av skjutreglaget i åtgärdsfönstret. Du kan zooma in eller Zooma ut topologisk karta med mushjulet. 

Topologin visas på kartan är layer 3-topologi och innehåller inte nivå 2-enheter och anslutningar. 

 
![Topologisk karta](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Log Analytics-sökningen 

Alla data som är exponerade grafiskt via instrumentpanelen för övervakning av nätverksprestanda och nedåt sidor är också tillgängligt internt i [Log Analytics Sök](log-analytics-log-search-new.md). Du kan utföra interaktiva analyser av data i databasen och korrelera data från olika källor. Du kan också skapa anpassade aviseringar och vyer och exportera data till Excel, Power BI eller en delbart länk. Den **vanliga frågor om** område i instrumentpanelen har vissa användbara frågor som du kan använda som startpunkt för att skapa egna frågor och rapporter. 

## <a name="alerts"></a>Varningar

Övervakare av nätverksprestanda använder aviseringar funktionerna i [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Det innebär att alla aviseringar hanteras med hjälp av [åtgärdsgrupper](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#overview).  

Om du är en NPM-användare som skapar en avisering via OMS: 
1. Du ser en länk som kommer att omdirigera dig till Azure-portalen. Klicka på den för att få åtkomst till portalen.
2. Klicka på panelen Övervakare av nätverksprestanda. 
3. Gå till Konfigurera.  
4. Välj test som du vill skapa en avisering på och följ de nämns stegen nedan.

Om du är en NPM-användare som skapar en avisering via Azure-portalen:  
1. Du kan välja att ange din e-post direkt eller du kan välja att skapa aviseringar via åtgärdsgrupper.
2. Om du vill ange din e-post direkt en åtgärd grupp med namnet **NPM e-post ActionGroup** skapas och e-post-ID: t har lagts till som åtgärdsgruppen.
3. Om du väljer att använda åtgärdsgrupper, måste du välja en tidigare skapad åtgärdsgrupp. Du kan lära dig hur du skapar en åtgärdsgrupp [här.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Du kan använda Hantera aviseringar länk för att hantera dina aviseringar när aviseringen har skapats. 

##<a name="pricing"></a>Prissättning

Information om priser finns [online](log-analytics-network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Lämna feedback 

* **UserVoice:** du kan publicera dina idéer för Network Performance Monitor-funktioner som du vill att vi ska arbeta med. Gå till den [UserVoice-sida](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Ansluta till vår kohort:** vi är alltid intresserade med nya kunder ansluta till vår kohort. Som en del av det kan få du snabb åtkomst till nya funktioner och en möjlighet att hjälpa oss att förbättra Övervakare av nätverksprestanda. Om du vill ansluta till, fylla i det här [snabbundersökning](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Nästa steg 
Läs mer om [Prestandaövervakaren](log-analytics-network-performance-monitor-performance-monitor.md), [Service Anslutningsövervakare](log-analytics-network-performance-monitor-performance-monitor.md), och [ExpressRoute-övervakning](log-analytics-network-performance-monitor-expressroute.md). 
