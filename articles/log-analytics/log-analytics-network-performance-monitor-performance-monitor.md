---
title: Performance Monitor funktion i Network Performance Monitor i Azure Log Analytics | Microsoft Docs
description: Performance Monitor-funktionen i Network Performance Monitor hjälper dig att övervaka nätverksanslutningen mellan olika punkter i nätverket. Du kan övervaka molndistributioner och lokala platser, flera datacenter och olika avdelningskontor och verksamhetskritiska program i flera skikt eller mikrotjänster.
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
ms.openlocfilehash: 65497548d0b8066627be25520c28d39491918d09
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30241453"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Network Performance Monitor-lösning: prestandaövervakning

Performance Monitor-funktionen i [Network Performance Monitor](log-analytics-network-performance-monitor.md) hjälper dig att övervaka nätverksanslutning mellan olika punkter i nätverket. Du kan övervaka molndistributioner och lokala platser, flera datacenter och olika avdelningskontor och verksamhetskritiska program i flera skikt eller mikrotjänster. Du kan identifiera nätverksproblem innan dina användare klagar med Prestandaövervakaren. Viktiga fördelar är att du kan: 

- Övervaka förluster eller fördröjningar över olika undernät och Ställ in aviseringar.
- Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket.
- Felsökning av nätverksproblem med tillfälliga och tidpunkt i, som är svåra att replikera.
- Kontrollera specifika segmentet på nätverket, vilket är ansvarig för försämrade prestanda.
- Övervakning av hälsan på nätverket, utan att behöva SNMP.


![Övervakning av nätverksprestanda](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguration
För att öppna konfigurationen för Network Performance Monitor, öppna den [Network Performance Monitor lösning](log-analytics-network-performance-monitor.md), och välj **konfigurera**.

![Konfigurera nätverket Prestandaövervakaren](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Skapa nya nätverk

Ett nätverk i Prestandaövervakaren för nätverk är en logisk behållare för undernät. Det kan du ordna övervakning av nätverksinfrastrukturen efter dina behov. Du kan skapa ett nätverk med ett eget namn och Lägg till undernät i den enligt affärslogik. Du kan till exempel skapa ett nätverk med namnet London och lägga till alla undernät i ditt datacenter i London. Du kan också skapa ett nätverk med namnet *ContosoFrontEnd* och lägga till i det här nätverket alla de undernät med namnet Contoso som betjänar klientdelen för din app. Lösningen skapas automatiskt ett standardnätverk, som innehåller alla undernät som identifieras i din miljö. 

När du skapar ett nätverk kan du lägga till ett undernät till den. Undernätet tas sedan bort från standardnätverk. Om du tar bort ett nätverk, returneras alla undernät automatiskt till standardnätverk. Standardnätverk fungerar som en behållare för alla undernät som inte finns i ett nätverk med användardefinierad. Du kan inte redigera eller ta bort standardnätverk. Det finns alltid i systemet. Du kan skapa så många anpassade nätverk som du behöver. I de flesta fall är undernät i din organisation ordnade i mer än ett nätverk. Skapa en eller flera nätverk för att gruppera dina undernät för affärslogik.

Skapa ett nytt nätverk:


1. Välj den **nätverk** fliken.
1. Välj **Lägg till nätverket**, och sedan ange nätverkets namn och beskrivning. 
2. Välj ett eller flera undernät och välj sedan **Lägg till**. 
3. Välj **spara** att spara konfigurationen. 


### <a name="create-monitoring-rules"></a>Skapa regler för övervakning 

Prestandaövervakaren genererar hälsa händelser när tröskelvärdet för prestandan hos Nätverksanslutningar mellan två undernät eller mellan två nätverk överskrids. Systemet kan lära dig tröskelvärdena automatiskt. Du kan även ange anpassade tröskelvärden. Systemet skapar automatiskt en standardregel som genererar en hälsohändelse när förlust eller fördröjning mellan ett par nätverk eller undernätverk länkar överträdelser system lärt dig tröskelvärdet. Den här processen kan lösningen övervaka nätverkets infrastruktur tills du uttryckligen inte har skapat några regler för övervakning. Om Standardregeln är aktiverad kan skicka alla noder syntetiska transaktioner till alla andra noder som du har aktiverat för övervakning. Standardregeln är användbart med små nätverk. Ett exempel är ett scenario där du har ett litet antal servrar som kör en mikrotjänster och du vill kontrollera att alla servrar som är ansluten till varandra.

>[!NOTE]
> Vi rekommenderar att du inaktiverar Standardregeln och skapa anpassade regler för övervakning, särskilt med stora nätverk där du använder ett stort antal noder för övervakning. Anpassad övervakning regler kan minska den trafik som genereras av lösningen och hjälper dig att ordna övervakning av nätverket.

Skapa regler för övervakning enligt affärslogik. Ett exempel är om du vill övervaka prestanda för nätverksanslutningen på två platser för office till huvudkontor. Gruppera alla undernät i office site1 i nätverket O1. Gruppera alla undernät i office webbplats2 i nätverket O2. Slutligen gruppen alla undernät i huvudkontor i nätverket H. Skapa två övervakning regler--mellan O1 och H och andra mellan O2 och H. 

Skapa anpassade regler för övervakning:

1. Välj **Lägg till regel** på den **övervakaren** fliken och ange namn och beskrivning.
2. Välj paret med nätverks- eller undernätverk länkar för att övervaka i listan. 
3. Markera det nätverk som innehåller undernätverk som du vill använda från listrutan nätverk. Välj sedan undernätverk från listrutan motsvarande undernätverk. Om du vill övervaka alla undernät i en nätverkslänk väljer **alla undernät**. På liknande sätt, välj andra undernät som du vill. Om du vill exkludera övervakning för specifika undernätverkslänkar från de val du gjort, Välj **Lägg till undantag**. 
4. Välj mellan ICMP- och TCP protokoll som ska köra syntetiska transaktioner. 
5. Om du inte vill skapa hälsa händelser för objekt du har valt, rensa **aktivera övervakning av hälsotillstånd på länkarna som omfattas av den här regeln**. 
6. Välj övervakning villkor. Ange anpassade tröskelvärden för health-händelse genereras, ange tröskelvärden. När värdet för villkoret överskrider det valda tröskelvärdet för det valda nätverket eller undernätverk par genereras en hälsohändelse. 
7. Välj **spara** att spara konfigurationen. 

När du har sparat en regel för övervakning, du kan integrera regeln med Alert Management genom att välja **skapa avisering**. En aviseringsregel skapas automatiskt i sökningen. Andra nödvändiga parametrar fylls i automatiskt. Med en varningsregel kan du ta emot e-post meddelanden, förutom befintliga meddelanden inom nätverket Prestandaövervakaren. Aviseringar kan också utlösa vidtar åtgärder med runbooks eller de kan integrera med befintliga lösningar tjänsten med hjälp av webhooks. Välj **hantera avisering** redigera inställningar för avisering. 

Du kan nu skapa flera regler som Prestandaövervakaren eller flytta till instrumentpanelen lösning för att använda funktionen.

### <a name="choose-the-protocol"></a>Välj protokollen

Network Performance Monitor använder syntetiska transaktioner för att beräkna prestandamått för nätverket som paketet dataförlust och länka svarstid. För att bättre förstå detta begrepp att överväga en Network Performance Monitor-agent som är ansluten till ett end för en nätverkslänk. Den här Network Performance Monitor-agenten skickar avsökningen paket till en andra Network Performance Monitor-agent som är ansluten till en annan slutet av nätverket. Andra agenten svarar med svarspaket. Den här processen upprepas några gånger. Den första Network Performance Monitor-agenten utvärderar länkfördröjningen genom att mäta antalet svar och den tid det tar att ta emot varje svar och tappade paket. 

Format, storlek och ordning i paketen bestäms av det protokoll som du väljer när du skapar regler för övervakning. Baserat på protokollet för paket, kan de mellanliggande nätverksenheter, t.ex routrar och växlar, bearbeta paketen på olika sätt. Följaktligen påverkar önskat protokoll precisionen i resultaten. Önskat protokoll avgör också om du måste vidta någon manuella steg när du distribuerar Network Performance Monitor-lösning. 

Network Performance Monitor kan du välja mellan att ICMP- och TCP-protokoll för att köra syntetiska transaktioner. Om du väljer ICMP när du skapar en regel för syntetisk transaktion, använda Network Performance Monitor-agenter ICMP ECHO-meddelanden för att beräkna Nätverksfördröjningen och paketförlust. ICMP ECHO använder samma meddelande som skickas av konventionella ping-verktyget. När du använder TCP som protokoll, skicka Network Performance Monitor agenter TCP SYN paket via nätverket. Det här steget följs av en TCP-handskakningen slutförande och anslutningen tas bort med hjälp av RST paket. 

Tänk på följande innan du väljer ett protokoll: 

* **Identifiering av flera nätverksvägar.** TCP är mer exakt när identifiering av flera vägar och den måste färre agenter i varje undernät. En eller två agenter som använder TCP kan exempelvis identifiera alla redundanta sökvägar mellan undernät. Du behöver flera agenter som använder ICMP för att uppnå samma resultat. Med ICMP, om du har ett antal vägar mellan två undernät, behöver du mer än 5N agenter i källan eller målet undernät.

* **Riktighet resultat.** Routrar och växlar tenderar att tilldela ICMP ECHO-paket jämfört med TCP-paket med lägre prioritet. I vissa situationer när nätverksenheter är högt belastad, avspeglar data hämtas av TCP närmare den förluster eller fördröjningar orsakade av program. Detta inträffar eftersom de flesta programtrafiken flödar över TCP. I sådana fall ger ICMP mindre-korrekta resultat jämfört med TCP. 

* **Brandväggskonfigurationen.** TCP-protokollet kräver att TCP-paket skickas till en målport. Standardporten som används av Network Performance Monitor-agenter är 8084. Du kan ändra porten när du konfigurerar agenter. Kontrollera att din nätverkets brandväggar eller regler för nätverkssäkerhetsgrupper (NSG) (i Azure) tillåter trafik på port. Du måste också se till att den lokala brandväggen på datorer där agenter är installerade är konfigurerad för att tillåta trafik på den här porten. Du kan använda PowerShell-skript för att konfigurera brandväggsregler för din Windows-datorer, men du måste konfigurera nätverkets brandvägg manuellt. Däremot fungerar ICMP inte med hjälp av en port. I de flesta företagsscenarier tillåts ICMP-trafik via brandvägg så att du kan använda verktyg för Nätverksdiagnostik som ping-verktyget. Om du kan pinga en dator från en annan, kan du använda ICMP-protokollet utan att behöva konfigurera brandväggar manuellt.

>[!NOTE] 
> Vissa brandväggar kan blockera ICMP, vilket kan leda till återöverföring som resulterar i ett stort antal händelser i din information och händelsen hanteringssystem för informationssäkerhet. Kontrollera att det protokoll som du väljer inte blockeras av en brandvägg eller NSG. Annars kan Network Performance Monitor övervaka nätverkssegmentet. Vi rekommenderar att du använder TCP för övervakning. Använda ICMP i situationer där du kan inte använda TCP, t.ex när: 
>
> - Du kan använda Windows klientbaserade noder eftersom TCP raw sockets inte tillåts i Windows-klienter.
> - Din brandvägg eller NSG blockerar TCP.
> - Du vet inte hur man byter protokollet.

Om du väljer att använda ICMP under distributionen måste växla du till TCP när som helst genom att redigera standardinställningar för övervakning av regeln.

1. Gå till **nätverksprestanda** > **övervakaren** > **konfigurera**   >  **Övervakaren**. Välj sedan **standardregel**. 
2. Bläddra till den **protokollet** och markerar det protokoll som du vill använda. 
3. Välj **spara** inställningen ska användas. 

Även om Standardregeln använder ett visst protokoll, kan du skapa nya regler med ett annat protokoll. Du kan även skapa en blandning av regler där vissa regler använder ICMP och andra använda TCP. 

## <a name="walkthrough"></a>Genomgång 

Nu titta på en enkel undersökning grundorsaken till en hälsohändelse.

På instrumentpanelen i lösningen visar en hälsohändelse som att en nätverkslänk är felfri. Om du vill undersöka problemet och välj den **nätverk länkar övervakas** panelen.

Gå nedåt sidan visar att den **DMZ2 DMZ1** nätverkslänk är felfri. Välj **visa undernät länkar** för den här nätverkslänken. 


Sidan nedåt visar alla undernätverkslänkar i den **DMZ2 DMZ1** nätverkslänken. För båda undernätverkslänkar överskred svarstiden tröskelvärdet, vilket gör nätverkslänken feltillstånd. Du kan också se trenderna svarstid för både undernätverkslänkar. Använd valet av tid styra i diagrammet att fokusera på tidsintervallet som krävs. Du kan se vilken tid på dagen när svarstid nåtts dess belastning. Söka i loggar för den här tidsperioden att undersöka problemet. Välj **visa nodlänkar** och öka detaljnivån ytterligare. 
 
 ![Sidan för undernätverk länkar](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Liknar föregående sida, nedåt sidan för viss undernätverk länken visar dess ingående nodlänkar. Du kan utföra samma åtgärder som du gjorde i föregående steg. Välj **visa topologi** att visa topologin mellan två noder. 
 
 ![Sidan för noden länkar](media/log-analytics-network-performance-monitor/node-links.png) 

Alla sökvägar mellan de två valda noderna visas i topologisk karta. Du kan visualisera hopp av hopp topologin av routinginformation mellan två noder i topologisk karta. Den ger dig en tydlig bild av hur många vägar finns mellan två noder och vad sökvägar datapaketen ta. Flaskhalsar i nätverket visas i rött. För att hitta en felaktig nätverksanslutning eller en felaktig nätverksenhet kan du titta på red elementen på topologisk karta. 

 ![Topologi instrumentpanel med topologisk karta](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Du kan granska förlust, svarstid och antal hopp i varje sökväg i den **åtgärd** fönstret. Använd rullningslisten för att visa information om feltillstånd sökvägar. Använd filter för att välja sökvägar med feltillstånd hopp så att topologin för bara de valda sökvägarna ritas. Använda mushjulet för att zooma in eller ut topologisk karta. 

I följande bild visas orsaken till problemområden till specifika avsnitt i nätverket i rött sökvägar och hopp. Välj en nod i topologin kartan för att visa egenskaperna för noden som innehåller det fullständigt bestämda domännamn och IP-adressen. Om du väljer ett hopp visas IP-adress för hopp. 
 
![Topologisk karta med nodegenskaper markerat](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Nästa steg
[Söka i loggar](log-analytics-log-searches.md) att visa detaljerad nätverket prestanda dataposter.
