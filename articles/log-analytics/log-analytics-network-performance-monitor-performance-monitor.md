---
title: Övervaka prestandafunktion i Network Performance Monitor-lösningen i Azure Log Analytics | Microsoft Docs
description: Övervakare av nätverksprestanda-funktionen i Network Performance Monitor kan du övervaka nätverksanslutningar i olika punkter i nätverket. Du kan övervaka molndistributioner och lokala platser, flera datacenter och olika avdelningskontor och verksamhetskritiska program i flera skikt eller mikrotjänster.
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
ms.component: ''
ms.openlocfilehash: 1254afc1ad1c513c18d565be8a6543a6ee0ae94b
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040599"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Network Performance Monitor-lösningen: prestandaövervakning

Övervakare av nätverksprestanda-funktionen i [Övervakare av nätverksprestanda](log-analytics-network-performance-monitor.md) hjälper dig att övervaka Nätverksanslutningar mellan olika punkter i nätverket. Du kan övervaka molndistributioner och lokala platser, flera datacenter och olika avdelningskontor och verksamhetskritiska program i flera skikt eller mikrotjänster. Med Prestandaövervakaren kan du identifiera nätverksproblem innan dina användare börjar klaga. Viktiga fördelar är att du kan: 

- Övervaka dataförluster och svarstider över olika undernät och Ställ in aviseringar.
- Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket.
- Felsök tillfälliga och point-in-time nätverksproblem som är svåra att replikera.
- Fastställ det specifika segmentet på nätverket, vilket är ansvarig för försämrade prestanda.
- Övervaka hälsotillståndet för nätverket, utan att behöva SNMP.


![Övervakning av nätverksprestanda](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguration
För att öppna konfigurationen för Övervakare av nätverksprestanda, öppna den [Network Performance Monitor-lösningen](log-analytics-network-performance-monitor.md), och välj **konfigurera**.

![Konfigurera Övervakare av nätverksprestanda](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Skapa nya nätverk

Ett nätverk i Network Performance Monitor är en logisk behållare för undernät. Det hjälper dig att organisera för övervakning av nätverksinfrastrukturen efter dina behov. Du kan skapa ett nätverk med ett eget namn och Lägg till undernät i den enligt din affärslogik. Du kan till exempel ett nätverk med namnet London och lägga till alla undernät i ditt datacenter i London. Eller så kan du skapa ett nätverk med namnet *ContosoFrontEnd* och lägga till i det här nätverket de undernät med namnet Contoso som klientdelen för din app att fungera. Lösningen skapar automatiskt ett standardnätverk som innehåller alla undernät som har identifierats i din miljö. 

När du skapar ett nätverk kan du lägga till ett undernät till den. Undernätet tas sedan bort från standardnätverk. Om du tar bort ett nätverk, returneras alla undernät automatiskt till standardnätverk. Standardnätverket fungerar som en behållare för alla undernät som inte tillhör någon användardefinierad för nätverket. Du kan inte redigera eller ta bort standardnätverket. Det finns alltid kvar i systemet. Du kan skapa så många anpassade nätverk som du behöver. I de flesta fall är undernät i din organisation ordnade i mer än ett nätverk. Skapa en eller flera nätverk för att gruppera dina undernät för din affärslogik.

Skapa ett nytt nätverk:


1. Välj den **nätverk** fliken.
1. Välj **Lägg till nätverk**, och ange ett nätverksnamn och en beskrivning. 
2. Välj ett eller flera undernät och därefter **Lägg till**. 
3. Välj **spara** att spara konfigurationen. 


### <a name="create-monitoring-rules"></a>Skapa regler för övervakning 

Övervakare av nätverksprestanda genererar health-händelser när tröskelvärdet för prestandan hos Nätverksanslutningar mellan två undernätverk eller mellan två nätverk överskrids. Systemet kan lära dig de här tröskelvärdena automatiskt. Du kan även ange anpassade tröskelvärden. Systemet skapar automatiskt en standardregel som genererar en hälsohändelse när förlust eller svarstid mellan ett par nätverks- eller undernätverk länkar överträdelser tröskelvärdet system lärt dig. Den här processen kan lösningen övervaka din nätverksinfrastruktur tills du uttryckligen inte har skapat några regler för övervakning. Om Standardregeln är aktiverad, skickar alla noder syntetiska transaktioner för alla andra noder som du har aktiverat för övervakning. Standardregeln är användbart med små nätverk. Ett exempel är ett scenario där du har ett litet antal servrar som kör en mikrotjänst och du vill se till att alla servrar har anslutning till varandra.

>[!NOTE]
> Vi rekommenderar att du inaktivera Standardregeln och skapa anpassade övervakningsregler, särskilt i stora nätverk där du använder ett stort antal noder för övervakning. Anpassade övervakningsregler kan minska den trafik som genereras av lösningen och hjälper dig att ordna för övervakning av nätverket.

Skapa regler för övervakning enligt din affärslogik. Ett exempel är om du vill övervaka prestanda för nätverksanslutningen för två office-platser för huvudkontor. Gruppera alla undernät i office site1 i nätverket O1. Gruppera sedan alla undernät i office site2 i nätverket O2. Slutligen gruppen alla undernät i sitt huvudkontor i nätverket H. Skapa två regler för övervakning – en mellan O1 och H och den andra mellan O2 och H. 

Skapa anpassade övervakningsregler:

1. Välj **Lägg till regel** på den **övervakaren** och anger ett namn och en beskrivning.
2. Välj två nätverk eller undernätverk länkar för att övervaka i listorna. 
3. Välj det nätverk som innehåller de undernätverk som du vill från listrutan nätverk. Välj sedan undernätverk från listrutan motsvarande undernätverk. Om du vill övervaka alla undernät i en nätverkslänk väljer **alla undernätverk**. Välj på samma sätt kan de undernätverk som du vill. Om du vill exkludera övervakning för specifika undernätverkslänkar från de val du gjort, Välj **Lägg till undantag**. 
4. Välj mellan ICMP- och TCP protokoll för att köra syntetiska transaktioner. 
5. Om du inte vill skapa health-händelser för objekt du har valt, rensa **aktivera hälsoövervakning på länkarna som omfattas av den här regeln**. 
6. Välj övervakning villkor. Om du vill ange anpassade tröskelvärden för hälsohändelse generering, ange tröskelvärden. När värdet för villkoret överskrider det valda tröskelvärdet för den valda nätverk eller undernätverk par, genereras en hälsotillståndshändelse. 
7. Välj **spara** att spara konfigurationen. 

När du har sparat en övervakningsregel kan du integrera regeln med hantering av varningar genom att välja **skapa avisering**. En varningsregel skapas automatiskt med sökfrågan. Andra nödvändiga parametrar fylls i automatiskt. Med en varningsregel kan få du e-post aviseringar, utöver de befintliga aviseringarna i övervakaren av nätverksprestanda. Aviseringar kan också utlösa korrigerande åtgärder med runbooks eller de kan integrera med befintliga lösningar för hantering av tjänsten med hjälp av webhooks. Välj **hantera avisering** så här redigerar du aviseringsinställningarna. 

Du kan nu skapa fler Prestandaövervakaren regler eller flytta till instrumentpanelen för lösningen för att använda funktionen.

### <a name="choose-the-protocol"></a>Välj protokollet

Övervakare av nätverksprestanda använder syntetiska transaktioner för att beräkna prestandamått för nätverket som paketet förlust och länka svarstid. För att bättre förstå detta begrepp att överväga en Network Performance Monitor-agent som är anslutna till ena änden av en nätverkslänk. Den här övervakaren av nätverksprestanda-agenten skickar avsökningen paket till en agent på andra Övervakare av nätverksprestanda är ansluten till en annan slutet av nätverket. Andra agenten svarar den med svarspaket. Den här processen upprepas några gånger. Första Övervakare av nätverksprestanda agenten utvärderar länkfördröjningen genom att mäta antalet svar och den tid det tar att ta emot varje svar och tappade paket. 

Format, storlek och sekvens med dessa paket bestäms av det protokoll som du väljer när du skapar regler för övervakning. Baserat på protokollet för paket, kan mellanliggande nätverksenheter som routrar och växlar, bearbeta dessa paket på olika sätt. Därför påverkar valfritt protokoll precisionen i resultaten. Valfritt protokoll avgör också om du måste vidta alla manuella åtgärder när du distribuerar Network Performance Monitor-lösningen. 

Övervakare av nätverksprestanda erbjuder valet mellan ICMP- och TCP-protokoll för att köra syntetiska transaktioner. Om du väljer ICMP när du skapar en regel för syntetisk transaktion, använder Network Performance Monitor-agenter ICMP ECHO-meddelanden för att beräkna paketförlust och nätverksfördröjning. ICMP ECHO använder samma meddelande som skickas av konventionella ping-verktyget. När du använder TCP som protokoll, Network Performance Monitor agenter skickar TCP SYN paket via nätverket. Det här steget följs av en TCP-handskakningen slutförande och anslutningen tas bort med hjälp av RSTA paket. 

Tänk på följande innan du väljer ett protokoll: 

* **Identifiering av flera nätverksvägar.** TCP är mer exakta när identifiering av flera vägar och behöver färre agenter i varje undernät. En eller två agenter som använder TCP kan exempelvis identifiera alla redundanta sökvägar mellan undernät. Du behöver flera agenter som använder ICMP för att få liknande resultat. Använda ICMP, om du har ett antal vägar mellan två undernät, behöver du mer än 5N agenter i en käll- eller målservrar undernät.

* **Riktighet resultat.** Routrar och växlar tenderar att tilldela ICMP ECHO-paket som jämfört med TCP-paket med lägre prioritet. I vissa situationer när nätverksenheter är högt belastad, återspeglar de data som hämtas av TCP närmare förlust och svarstiden av program. Det beror på att de flesta av trafiken som flödar över TCP. I sådana fall kan ger ICMP mindre exakta resultat jämfört med TCP. 

* **Brandväggskonfigurationen.** TCP-protokollet kräver att TCP-paket skickas till en målport. Standardporten som används av Network Performance Monitor-agenter är 8084. Du kan ändra porten när du konfigurerar agenter. Se till att din nätverksbrandväggar eller regler för nätverkssäkerhetsgrupper (NSG) (i Azure) tillåter trafik på port. Du måste också se till att den lokala brandväggen på datorer där agenter är installerade är konfigurerad för att tillåta trafik på den här porten. Du kan använda PowerShell-skript för att konfigurera brandväggsregler för datorerna som kör Windows, men du måste manuellt konfigurera nätverkets brandvägg. ICMP fungerar däremot inte med hjälp av en port. I de flesta scenarier med enterprise tillåts ICMP-trafik via brandväggarna så att du kan använda verktyg för diagnostik som ping-verktyget. Om du kan pinga en dator från en annan, kan du använda ICMP-protokollet utan att behöva konfigurera brandväggar manuellt.

>[!NOTE] 
> Vissa brandväggar kan blockera ICMP, vilket kan leda till återöverföring som resulterar i ett stort antal händelser i dina säkerhets- och händelsehantering hanteringssystemet. Kontrollera att det protokoll som du väljer inte blockeras av en brandvägg eller en NSG. I annat fall kan inte Övervakare av nätverksprestanda övervaka nätverkssegmentet. Vi rekommenderar att du använder TCP för övervakning. Använd ICMP i scenarier där du kan inte använda TCP, t.ex när: 
>
> - Du kan använda Windows klientbaserade noder eftersom TCP raw sockets inte tillåts i Windows-klienter.
> - Din brandvägg eller Nätverkssäkerhetsgrupp blockerar TCP.
> - Du vet inte hur man byter protokollet.

Om du väljer att använda ICMP under distributionen kan växla du till TCP när som helst genom att redigera regeln för övervakning.

1. Gå till **nätverksprestanda** > **övervakaren** > **konfigurera**   >  **Övervakaren**. Välj sedan **Standardregeln**. 
2. Bläddra till den **protokollet** och välj det protokoll som du vill använda. 
3. Välj **spara** inställningen ska användas. 

Även om Standardregeln använder ett visst protokoll, kan du skapa nya regler med ett annat protokoll. Du kan även skapa en blandning av regler där vissa regler använder ICMP och andra använda TCP. 

## <a name="walkthrough"></a>Genomgång 

Titta nu på ett enkelt undersöka orsaken till en hälsohändelse.

På instrumentpanelen för lösningen visar en hälsohändelse att en nätverkslänk är skadad. Om du vill undersöka problemet, Välj den **nätverk länkar som övervakas** panelen.

Gå nedåt sidan visar att den **DMZ2 DMZ1** Nätverkslänken är skadad. Välj **visa länkar till undernätverk** för den här nätverkslänken. 


Gå nedåt sidan visar alla undernätverkslänkar i den **DMZ2 DMZ1** nätverkslänken. För båda undernätverkslänkar överskred svarstiden tröskelvärdet, vilket gör nätverkslänken feltillstånd. Du kan också se svarstider trenderna i båda undernätverkslänkar. Använd val av tid styra i diagrammet kan fokusera på tidsintervallet som krävs. Du kan se tid på dagen när svarstid nådde sin topp. Söka i loggarna senare för den här tidsperioden att undersöka problemet. Välj **visa nodlänkar** och öka detaljnivån ytterligare. 
 
 ![Sidan för undernätverk länkar](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Sidan detaljnivån för specifika undernätverkslänk liknar föregående sida, visas dess ingående nodlänkar. Du kan utföra liknande åtgärder här som du gjorde i föregående steg. Välj **visa topologi** att visa topologi mellan de två noderna. 
 
 ![Sidan för nod-länkar](media/log-analytics-network-performance-monitor/node-links.png) 

Alla sökvägar mellan de två valda noderna visas i topologisk karta. Du kan visualisera hop-by-hop-topologin i vägar mellan två noder i topologisk karta. Det ger dig en tydlig bild av hur många vägar finns mellan de två noderna och vilka sökvägar datapaketen ta. Flaskhalsar i prestanda visas i rött. Titta på de röda element på topologisk karta för att hitta en felaktig nätverksanslutning eller felaktiga nätverksenhet. 

 ![Instrumentpanel för topologi med topologisk karta](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Du kan granska förlust, svarstid och antalet hopp i varje sökväg i den **åtgärd** fönstret. Använd rullningslisten för att visa information om defekta sökvägar. Använd filtren för att markera sökvägar med defekta hopp så att topologin för endast de valda sökvägarna ritas. För att zooma in eller ut från topologisk karta genom att använda mushjulet. 

I följande bild visas de grundläggande orsakerna till problemområden till viss del av nätverket i rött sökvägar och hopp. Välj en nod i topologin kartan för att visa egenskaperna för den nod som innehåller FQDN och IP-adressen. Om du väljer ett hopp visas IP-adressen för hoppet. 
 
![Topologisk karta med nodegenskaper valt](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Nästa steg
[Söka loggarna](log-analytics-log-searches.md) att visa detaljerad nätverk prestanda dataposter.
