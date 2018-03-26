---
title: Network Performance Monitor-lösning i Azure | Microsoft Docs
description: Prestandaövervakaren i Azure Network hjälper dig att övervaka prestanda för ditt nätverk – i nära verkliga tid-om du vill identifiera och hitta flaskhalsar i nätverket.
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
ms.openlocfilehash: 90fd9b09f90d5b209e0eb8cc6996ef7fb1e7e0b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Network Performance Monitor-lösning i Azure

![Nätverket Prestandaövervakaren symbol](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Network Performance Monitor (NPM) är en lösning som hjälper dig att övervaka nätverkets prestanda mellan olika platser i din nätverksinfrastruktur, bildskärm är ansluten till nätverket tjänstprogrammet/slutpunkter för molnbaserade hybrid av nätverksövervakning och övervaka prestanda för Azure ExpressRoute.  

NPM identifierar nätverksproblem som trafik blackholing routning fel och problem som vanliga nätverk övervakning metoder inte går att identifiera. Lösningen genererar varningar, meddelar när ett tröskelvärde komprometteras för en nätverkslänk garanterar snabbt ska kunna identifiera problem med prestanda och localizes källan till problemet till en viss nätverkssegment eller enhet. 

NPM erbjuder tre allmänna funktioner: 

[Prestandaövervakaren](log-analytics-network-performance-monitor-performance-monitor.md): Övervakare för nätverksanslutningen mellan moln distributioner och lokala platser, flera datacenter, och filialkontor, uppdragskritiska kritiska flera nivåer program/micro-tjänster. Du kan identifiera nätverksproblem innan dina användare klagar med Prestandaövervakaren.  

[Tjänsten Endpoint övervakning](log-analytics-network-performance-monitor-service-endpoint.md): du kan övervaka anslutningen från användarna till de tjänster du bryr dig om att avgöra vilken infrastruktur som finns i sökvägen och nätverk där flaskhalsar uppstår. Känna avbrott innan användarna och se den exakta platsen problem längs nätverkssökvägen. 

Den här funktionen hjälper dig att utföra http, HTTPS, TCP och ICMP-baserade tester för att övervaka i nära realtid eller tidigare tillgänglighet och svarstid för din tjänst och nätverket i paketförlust och fördröjning bidrag. Du kan använda nätverket topologisk karta för att isolera fördröjningar i nätverket genom att identifiera problem platser som sker längs sökvägen från noden till tjänsten med latens data på varje hopp. Övervaka nätverksanslutning till Office 365 och Dynamics CRM utan någon före konfiguration med inbyggda tester. Med den här funktionen kan du övervaka nätverksanslutning till alla kompatibla TCP-slutpunkt som webbplatser, SaaS, PaaS program, SQL-databaser osv.  

[Övervakare för ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): övervaka slutpunkt till slutpunkt-anslutning och prestanda mellan dina lokalkontor och Azure, över Azure ExpressRoute.  

Mer information om de olika funktioner som stöds av [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) är ansluten till.
 

## <a name="set-up-and-configure"></a>Installera och konfigurera

### <a name="install-and-configure-agents"></a>Installera och konfigurera agenter 

Använd basic-processer för att installera agenter på [ansluta Windows-datorer till logganalys](log-analytics-windows-agents.md) och [ansluta Operations Manager till logganalys](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Var du vill installera agenter 

**Prestandaövervakaren:** installera OMS-agenterna på minst en nod som är ansluten till varje undernätverk som du vill övervaka nätverksanslutning till andra undernät.  

För att övervaka en nätverkslänk, måste du installera agenter på båda slutpunkter för länken.  Om du är osäker på om nätverkets topologi, installera agenter på servrar med viktiga arbetsbelastningar som du vill övervaka nätverkets prestanda. Till exempel om du vill övervaka nätverksanslutningen mellan en webbserver och en server som kör SQL, installera en agent på båda servrarna. Agenter övervakar nätverksanslutning (länkar) mellan värdar inte själva värdarna. 

**Tjänsten Endpoint övervakning:** installera OMS-agenten på varje nod som du vill övervaka nätverksanslutningen till tjänstslutpunkten. Till exempel om du vill övervaka nätverksanslutning till Office365 från office-webbplatsen O1 installera O2 och O3, sedan OMS-agent på minst en nod varje i O1, O2 och O3. 

**Övervaka ExpressRoute:** installera minst en OMS-agent i ditt Azure VNET och minst en agent i din lokala undernätverk som är anslutna via privata ExpressRoute-Peering.  

### <a name="configure-oms-agents-for-monitoring"></a>Konfigurera OMS-agenter för övervakning  

NPM använder syntetiska transaktioner för att övervaka nätverksprestanda mellan käll- och agenter. Lösningen kan välja mellan TCP och ICMP som protokoll för övervakning i Prestandaövervakaren och tjänsten Endpoint övervakning funktioner, medan TCP används för ExpressRoute-övervakaren. Se till att brandväggen tillåter kommunikation mellan OMS-agenter som används för övervakning på det protokoll som du har valt för övervakning.  

**TCP-protokollet:** om du har valt TCP som protokoll för övervakning, öppna brandväggsport för agenterna för Prestandaövervakaren och övervaka ExpressRoute-funktioner, som används för att kontrollera att agenterna kan ansluta till varandra. Gör du genom att köra EnableRules.ps1 PowerShell-skriptet utan några parametrar i ett power shell-fönster med administratörsbehörighet.  

Skriptet skapar registernycklar som krävs av lösningen och skapar Windows brandväggsregler som tillåter agenter för att skapa TCP-anslutningar med varandra. Registernycklar som skapats av skriptet ange om du vill logga debug-loggar och sökvägen för filen loggar. Den definierar även agent TCP-port som används för kommunikation. Värden för nycklarna anges automatiskt av skript, så du inte bör ändra dessa nycklar manuellt. Porten öppnas som standard är 8084. Du kan använda en anpassad port genom att ange parametern-portnummer i skriptet. Men ska samma port användas på alla datorer där skriptet körs. 

>[!NOTE]
> Skriptet konfigurerar bara windows-brandväggen lokalt. Om du har en brandvägg för nätverk, bör du kontrollera att den tillåter trafik på TCP-port som används av NPM 

>[!NOTE]
> Du behöver inte köra EnableRules.ps1 PowerShell-skript för tjänsten Endpoint för övervakning 

 

**ICMP-protokollet** - om du har valt ICMP som protokoll för övervakning, aktivera följande brandväggsregler för att på ett tillförlitligt sätt använda ICMP: 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>Konfigurera lösningen 

1. Lägga till Network Performance Monitor-lösningen till arbetsytan från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md). 
2. Öppna logganalys-arbetsytan och klicka på den **översikt** panelen.  
3. Klicka på panelen med titeln **Network Performance Monitor** med meddelandet *lösningen kräver ytterligare konfiguration*.

    ![NPM sida vid sida](media/log-analytics-network-performance-monitor/npm-config.png)

3. På den **installationsprogrammet** sidan, finns alternativet att installera agenter på OMS och konfigurera agenter för övervakning i den **gemensamma inställningar för** vyn. Som beskrivs ovan, om du redan har installerat och konfigurerat OMS agenter, klicka på den **installationsprogrammet** vyn för att konfigurera funktionen som du vill använda.  

    **Visning av Prestandaövervakaren** -välja vilka protokoll som ska användas för syntetiska transaktioner i standard performance monitor regeln och klicka på Spara och fortsätta. Protokollet markeringen innehåller endast för systemgenererade Standardregeln och du måste välja protokollen varje gång du skapar en regel för Prestandaövervakaren explicit. Du kan alltid gå till regeln standardinställningarna på fliken Prestandaövervakaren (visas när du har slutfört konfigurationen dag 0) och ändra protokollet senare. Om du inte vill rPerfomance bildskärmar, kan du inaktivera Standardregeln från regeln standardinställningarna på fliken Prestandaövervakaren. 

    ![NPM-konfiguration](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **Tjänsten Endpoint övervakaren visa** -kapacitet innehåller inbyggda förkonfigurerade tester för att övervaka nätverksanslutning till Office 365 och Dynamcis365 från dina agenter. Välj de Office365 och Dynamcis365 tjänster som du är intresserad av övervakning genom att markera kryssrutan bredvid de. Välj de agenter där du vill övervaka genom att klicka på knappen Lägg till agenter. Om du inte vill använda den här funktionen eller vill konfigurera det senare, kan du hoppa över detta och klicka direkt på **spara** och **Fortsätt** utan att välja något.  

    ![NPM-konfiguration](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **ExpressRoute övervakaren visa** -Klicka på den **identifiera nu** för att identifiera alla ExpressRoute privata peerkopplingar som är anslutna till Vnet i Azure-prenumeration som är kopplad till den här logganalys-arbetsytan.  


    >[!NOTE] 
    > Lösningen identifierar för närvarande endast ExpressRoute privata peerkopplingar. 

    >[!NOTE] 
    > Endast de privata peerkopplingar identifieras som är anslutna till Vnet som är associerade med prenumerationen som är kopplad till den här logganalys-arbetsytan. Om din ExpressRoute är ansluten till Vnet utanför den prenumeration som är kopplad till den här arbetsytan, måste du skapa en logganalys-arbetsytan i dessa prenumerationer och använda NPM för att övervaka de peerkopplingar. 

    ![NPM-konfiguration](media/log-analytics-network-performance-monitor/npm-express-route.png)

    När identifieringen har slutförts visas identifierade privata peerkopplingar i en tabell.  

    ![NPM-konfiguration](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    Övervakning av dessa peerkopplingar är först i inaktiverat tillstånd. Klicka på varje peering att du är intresserad av övervakning och konfigurera övervakning av dem från höger sida (till höger) i Detaljvyn.  Klicka på Spara för att spara konfigurationen. Se [konfigurera ExpressRoute övervakning]() vill veta mer.  

    När installationen är klar tar 30 minuter till en timme att fylla i data. När lösningen har datainsamling från nätverket, se *lösningen kräver ytterligare konfiguration* på NPM översiktsikon. När data samlas in och indexerade översikten panelen ändringar och informerar dig om sammanfattningen av hälsotillståndet för nätverket. Du kan sedan välja att redigera övervakning av noder som OMS agenter är installerade, samt de undernät som identifieras från din miljö 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Redigera övervakningsinställningarna för undernät och noder 

Alla undernät med minst en agent installerad visas på fliken undernät i konfigurationssidan. 


Aktivera eller inaktivera övervakning av specifika undernät 

1. Markera eller avmarkera kryssrutan bredvid den **ID för undernätverk** och se till att **för övervakning** är markerad eller avmarkerad, vid behov. Du kan markera eller avmarkera flera undernät. När den är inaktiverad, övervakas inte undernätverk som agenterna har uppdaterats för att stoppa pinga andra agenter. 
2. Välj de noder som du vill övervaka i en viss undernätverk genom att välja undernätverk i listan och flytta krävs noderna mellan listor som innehåller oövervakade och övervakade noder. Du kan lägga till en **anpassade beskrivning av** undernätverk. 
3. Klicka på **spara** att spara konfigurationen. 

#### <a name="choose-nodes-to-monitor"></a>Välj noder för att övervaka

Alla noder som har en agent som installerats på de visas i den **noder** fliken. 

1. Markera eller avmarkera de noder som du vill övervaka eller stoppa övervakningen. 
2. Klicka på **för övervakning**, eller avmarkera den efter behov. 
3. Klicka på **Spara**. 


Konfigurera capability(s) som du är intresserad av: 
- Konfigurera [Prestandaövervakaren](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Konfigurera [tjänsten Endpoint Övervakare](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Konfigurera [ExpressRoute-Övervakaren](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Information om samlingen
Network Performance Monitor använder TCP SYN-SYNACK-ACK handskakning paket när du har valt TCP och ICMP ECHO ICMP ECHO SVARSMEDDELANDEN när ICMP är valt som protokoll för att samla in information om förluster eller fördröjningar. Traceroute används också för att hämta information om nätverkstopologin.

I följande tabell visar metoder för insamling av data och annan information om hur data samlas in för Network Performance Monitor.

| Plattform | Styr Agent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agent data som skickas via management-grupp | Frekvens för samlingen |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP-handskakningar på/ICMP ECHO-meddelanden var femte sekund data skickas var 3: e minut |
 

 
Lösningen använder syntetiska transaktioner för att utvärdera hälsan för nätverket. OMS-agenter som installerats på olika ställen i nätverkspaket exchange TCP eller ICMP Echo (beroende på det protokoll som valts för övervakning) med varandra. I processen dig agenter fram och åter tid och paket förlust, eventuella. Varje agent med jämna mellanrum, utför även en spårningsrutt för att andra agenter för att hitta alla olika vägar i nätverket som ska testas. Med dessa data kan härleda agenterna Nätverksfördröjningen och paket förlustsiffror. Testerna upprepas var femte sekund och data har aggregerats under en period på tre minuter med agenterna innan den skickas till logganalys-tjänsten. 



>[!NOTE]
> Även om agenter är ofta kommunicerar med varandra, genererar de inte betydande nätverkstrafik när du utför testerna. Agenter använda enbart TCP SYN-SYNACK-ACK handskakning paket att fastställa förluster eller fördröjningar – inga data som utbyts paket. Under den här processen agenter kommunicerar med varandra endast vid behov och kommunikationstopologin agent är optimerad för att minska nätverkstrafiken.

## <a name="using-the-solution"></a>Använda lösningen 

### <a name="npm-overview-tile"></a>NPM översiktsikon 

När du har aktiverat Network Performance Monitor-lösningen ger en snabb överblick över nätverkets tillstånd panelen lösning på översiktssidan. 

 ![NPM Översiktsikon](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Nätverket Prestandaövervakaren instrumentpanelen 

Den **upp händelser på nätverket hälsa** innehåller en lista över senaste hälsa händelser och aviseringar i systemet och tid eftersom händelsen har varit aktiv. En hälsohändelse eller en varning genereras när värdet för övervakning regeln valda måttet (förlust, svarstid, svarstid eller bandbreddsanvändningen) överstiger tröskelvärdet. 

Den **Prestandaövervakaren** sidan innehåller en översikt över hälsotillståndet för nätverkslänkar och undernätverk länkar som övervakas av lösningen. Panelen topologi informerar antalet nätverkssökvägar som övervakas i nätverket. Klicka på den här panelen direkt navigerar du till vyn topologi. 

Den **Endpoint Tjänstövervakare** sidan innehåller en översikt över hälsotillståndet för de olika tester som du har skapat. Panelen topologi informerar antalet slutpunkter som övervakas. Klicka på den här panelen direkt navigerar du till vyn topologi.

Den **ExpressRoute övervakaren** sidan innehåller en översikt över hälsotillståndet för olika ExpressRoute peering anslutningar som övervakas av lösningen. Panelen topologi informerar antalet nätverkssökvägar via ExpressRoute-circuit(s) som övervakas i nätverket. Klicka på den här panelen direkt navigerar du till vyn topologi.

Den **vanliga frågor** sidan innehåller en uppsättning sökfrågor som hämta rådata för nätverksövervakning data direkt. Du kan använda de här frågorna som utgångspunkt för att skapa egna frågor för anpassad rapportering. 

![NPM-instrumentpanelen](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>detaljnivån för djup 

Du kan klicka på olika länkar på instrumentpanelen lösningen att gå ner djupare i alla intresseområde. När du ser en avisering eller ett feltillstånd nätverkslänken som visas på instrumentpanelen kan du till exempel klicka att undersöka vidare. Du kommer till en sida som visar en lista över alla undernätverkslänkar för nätverks-länk. Du kan se status för dataförlust, svarstid och hälsotillståndet för varje undernätverk länk och snabbt ta reda på vilka undernätverkslänkar som orsakar problemet. Du kan klicka **visa nodlänkar** att se alla länkar i noden för länken ohälsosamt undernät. Du kan sedan se enskilda nod till nod länkar och hitta nodlänkar. 

Du kan klicka på **visa topologi** att visa hopp av hopp topologin vägar mellan käll- och noderna. Feltillstånd vägar som visas i rött och du kan visa den svarstid som har bidragit med varje hopp så att du snabbt kan identifiera problemet till en viss del av nätverket. 

 

### <a name="network-state-recorder"></a>Network State Recorder 

Varje visas en ögonblicksbild av nätverkets tillstånd vid en viss tidpunkt. Senaste status visas som standard. Längst upp på sidan visar punkten i tiden som status visas. Du kan välja att gå tillbaka i tiden och visa ögonblicksbilden av nätverkets tillstånd genom att klicka på listen på åtgärder. Du kan också välja att aktivera eller inaktivera automatisk uppdatering för en sida när du visar det aktuella tillståndet. 

 ![Network State Recorder](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trend diagram 

Du kan se trend tillämpliga mått – förlust, svarstid, svarstid, bandbreddsanvändningen på varje nivå som du detaljnivån. Du kan ändra tidsintervallet för trend genom att använda kontrollen tid överst i diagrammet. 

Trend diagram visar en historisk perspektiv prestanda för ett mått för prestanda. Vissa nätverksproblem är tillfälligt till sin natur och är svåra att fånga endast genom att titta på det aktuella tillståndet för nätverket. Detta beror på att problem kan ansluta snabbt och försvinner innan alla meddelanden enbart för att visas igen vid en senare tidpunkt. Sådana tillfälliga problem kan också vara svårt för administratörer eftersom de utfärdar ofta yta som oförklarade ökningen av svarstiden programmet, även när alla programkomponenter ut att fungera smidigt. 

Du kan enkelt identifiera dessa typer av problem genom att titta på trenddiagram där problemet visas som en plötslig topp i Nätverksfördröjningen eller paketförlust. Sedan kan du undersöka problemet genom för att visa nätverk ögonblicksbild och topologin för den tidpunkten och tidpunkt när problemet har uppstått med tillstånd inspelning av nätverket. 

 
![Trend diagram](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topologisk karta 

NPM visar hopp av hopp topologin av routinginformation mellan käll- och slutpunkten på en interaktiv topologisk karta. Du kan visa topologisk karta genom att klicka på den **topologi** panelen på instrumentpanelen för lösning eller genom att klicka på **visa topologi** länk på sidorna nedåt.  

Topologisk karta visar hur många flöden är mellan källa och mål och vad sökvägar datapaketen ta. Fördröjningen av varje nätverk hopp visas också. Alla sökvägar som är den totala sökväg svarstiden tröskelvärdet (anges i motsvarande övervakning regel) visas i rött.  

När du klickar på en nod eller hovra över den på kartan topologi, kan du se egenskaperna för noden som FQDN och IP-adressen. Klicka på ett hopp om du vill visa dess IP-adress. Du kan identifiera program som krånglar nätverket hopp genom att meddela den svarstid som har bidragit med den. Du kan välja att filtrera särskilt vägar genom att använda filter i åtgärdsrutan döljas. Du kan också förenkla nätverkstopologier genom att dölja mellanliggande hopp med skjutreglaget i åtgärdsfönstret. Du kan zooma in eller out-of-topologisk karta med mushjulet. 

Observera att topologin visas på kartan är nivå 3-topologi och innehåller inte nivå 2-enheter och anslutningar. 

 
![Topologisk karta](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Log Analytics Search 

Alla data som är exponerade grafiskt via NPM-instrumentpanelen och nedåt sidor finns också internt i [logganalys söka](log-analytics-log-search-new.md). Du kan utföra interaktiv analys av data i databasen, korrelera data från olika källor, skapa anpassade aviseringar, skapa anpassade vyer och exportera data till Excel, PowerBI eller delbart länk. Vanliga frågor område i instrumentpanelen har vissa användbara frågor som du kan använda som utgångspunkt för att skapa egna frågor och rapporter. 

 

## <a name="provide-feedback"></a>Ge feedback 

**UserVoice** -du kan publicera dina idéer för Network Performance Monitor-funktioner som du vill att vi ska arbeta med. Besök vår [UserVoice sidan](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

**Ansluta till vår kommittén** -vi alltid är intresserad av att nya kunder ansluta till vår kommittén. Som en del av det, du får snabb åtkomst till nya funktioner och hjälp oss att förbättra Network Performance Monitor. Om du är intresserad av att koppla kan fylla i det här [snabb undersökning](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Nästa steg 
- Lär dig mer om [Prestandaövervakaren](log-analytics-network-performance-monitor-performance-monitor.md), [Endpoint Tjänstövervakare](log-analytics-network-performance-monitor-performance-monitor.md), och [ExpressRoute övervakaren](log-analytics-network-performance-monitor-expressroute.md). 
