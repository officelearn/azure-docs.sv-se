---
title: Performance Monitor funktion i Network Performance Monitor i Azure Log Analytics | Microsoft Docs
description: "Performance Monitor-funktionen i Network Performance Monitor hjälper dig att övervaka nätverksanslutningen mellan olika punkter i nätverket, till exempel distribution av molntjänster och lokala platser, flera datacenter och avdelningskontor, verksamhetskritiska flera nivåer program/micro-tjänster."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: a90ab3bc857b704d9d94daf96d17611844abb1a6
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution---performance-monitoring"></a>Nätverk Prestandaövervakaren lösning - prestandaövervakning

Performance Monitor-funktionen i [Network Performance Monitor](log-analytics-network-performance-monitor.md) hjälper dig att övervaka nätverksanslutningen mellan olika pekar i nätverket, till exempel molndistributioner och lokala platser, flera datacenter och avdelningskontor, uppdrag kritiska flera nivåer program/micro-tjänster. Du kan identifiera nätverksproblem innan dina användare klagar med Prestandaövervakaren. Viktiga fördelar är: 

- Övervaka förluster eller fördröjningar över olika undernät och set-aviseringar 
- Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket 
- Felsökning av nätverksproblem med tillfälliga & tidpunkt i, som är svåra att replikera 
- Fastställa specifika segmentet på nätverket, vilket är ansvarig för försämrade prestanda 
- Övervakning av hälsan på nätverket, utan att behöva SNMP 


![Övervakning av nätverksprestanda](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguration
För att öppna konfigurationen för Network Performance Monitor, öppna den [Network Performance Monitor lösning](log-analytics-network-performance-monitor.md) och klicka på den **konfigurera** knappen.

![Konfigurera nätverket Prestandaövervakaren](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Skapa nya nätverk

Ett nätverk i NPM är en logisk behållare för undernät. Det kan du ordna övervakning av nätverksinfrastrukturen efter dina behov för övervakning. Du kan skapa ett nätverk med ett eget namn och Lägg till undernät i den enligt affärslogik. Du kan skapa ett nätverk med namnet London och lägga till alla undernät i datacentrets London, t.ex ett nätverk med namnet *ContosoFrontEnd* och Lägg till alla undernät som betjänar klientdelen för din app med namnet Contoso till det här nätverket. Lösningen skapas automatiskt ett standardnätverk som innehåller alla undernät som identifieras i din miljö. När du skapar ett nätverk kan du lägga till ett undernät till den och undernätet tas bort från standardnätverk. Om du tar bort ett nätverk, returneras alla undernät automatiskt till standard-nätverk. Därför fungerar standardnätverk som en behållare för alla undernät som inte ingår i en användardefinierad nätverk. Du kan inte redigera eller ta bort standardnätverk. Det finns alltid i systemet. Du kan dock skapa så många anpassade nätverk som du behöver. I de flesta fall undernät i din organisation är ordnade i mer än ett nätverk och du måste skapa ett eller flera nätverk för att gruppera dina undernät för affärslogik.

Skapa ett nytt nätverk:


1. Klicka på den **flik nätverk**.
1. Klicka på **Lägg till nätverket** och Skriv nätverkets namn och beskrivning. 
2. Välj ett eller flera undernät och klicka sedan på **Lägg till**. 
3. Klicka på **spara** att spara konfigurationen. 


### <a name="create-monitoring-rules"></a>Skapa regler för övervakning 

Prestandaövervakaren genererar hälsa händelser när tröskelvärdet för prestandan hos Nätverksanslutningar mellan två undernät eller mellan två nätverk överskrids. Dessa tröskelvärden kan hämtas automatiskt av systemet eller du kan ange anpassade tröskelvärden. Systemet skapar automatiskt en standardregel som genererar en hälsohändelse när förlust eller fördröjning mellan ett par nätverk/undernätverk länkar överträdelser system lärt dig tröskelvärdet. Detta hjälper lösningen övervaka nätverkets infrastruktur tills du uttryckligen inte har skapat några regler för övervakning. Om den **standardregel** är aktiverat kan alla noder skicka syntetiska transaktioner till alla andra noder som du har aktiverat för övervakning. Standardregeln är användbart med små nätverk, till exempel i ett scenario där du har ett litet antal servrar som kör en mikrotjänster och du vill kontrollera att alla servrar som är ansluten till varandra. 

>[!NOTE]
> Vi rekommenderar att du inaktiverar den **standardregel** och skapa anpassade regler för övervakning, särskilt med stora nätverk där du använder ett stort antal noder för övervakning. Detta minskar den trafik som genereras av lösningen och hjälper dig att ordna övervakning av nätverket. 

Skapa regler för övervakning enligt affärslogik. Till exempel om du vill övervaka prestanda för nätverksanslutningen på två platser för office till headquarter sedan gruppera alla undernät i office site1 i nätverket O1, alla undernät i office webbplats2 i nätverket O2 och alla undernät i headquarter i nätverket H. Skapa 2 övervakning regler-ett mellan O1 och H och andra mellan O2 och H. 

Skapa anpassade regler för övervakning:

1. Klicka på **Lägg till regel** i den **övervakaren** och ange namn och beskrivning. 
2. Välj paret med nätverks- eller undernätverk länkar för att övervaka i listan. 
3. Först välja nätverket första undernätverk/s intressanta finns i nätverket listrutan och välj sedan undernätverk/s i motsvarande undernätverk listrutan. Välj **alla undernät** om du vill övervaka alla undernät i en nätverkslänk. På liknande sätt väljer du den andra undernätverk/s av intresse. Och du kan klicka på **Lägg till undantag** för att utesluta övervakning för specifika undernätverkslänkar från den val. 
4. Välj mellan ICMP- och TCP protokoll för att köra syntetiska transaktioner. 
5. Om du inte vill skapa hälsa händelser för de objekt som du har valt, sedan avmarkera **aktivera övervakning av hälsotillstånd på länkarna som omfattas av den här regeln**. 
6. Välj övervakning villkor. Du kan ange anpassade tröskelvärden för hälsotillstånd händelse genereras genom att skriva tröskelvärden. När värdet för villkoret går över dess valda tröskelvärdet för det valda nätverk/undernätverk paret, skapas en hälsohändelse. 
7. Klicka på **spara** att spara konfigurationen. 

När du har sparat en regel för övervakning, du kan integrera regeln med Alert Management genom att klicka på **skapa avisering**. En aviseringsregel skapas automatiskt med sökvillkoret och andra nödvändiga parametrar automatiskt ifyllda. Med hjälp av en aviseringsregel, du kan ta emot e-postbaserad aviseringar, förutom befintliga aviseringar i NPM. Aviseringar kan också utlösa vidtar åtgärder med runbooks eller de kan integrera med befintliga lösningar tjänsten med hjälp av webhooks. Du kan klicka på **hantera avisering** redigera inställningar för avisering. 

Du kan nu skapa flera regler i Prestandaövervakaren eller flytta till instrumentpanelen lösningen att börja använda kapaciteten 

### <a name="choose-the-protocol"></a>Välj protokollen

Network Performance Monitor (NPM) använder syntetiska transaktioner för att beräkna prestandamått för nätverket som paketet dataförlust och länka svarstid. För att förstå det bättre att överväga en NPM-agent som är ansluten till ett end för en nätverkslänk. Den här NPM-agenten skickar avsökningen paket till en andra NPM-agent som är ansluten till en annan slutet av nätverket. Andra agenten svarar med svarspaket. Den här processen upprepas några gånger. Första NPM agenten utvärderar länkfördröjningen genom att mäta antalet svar och tidsåtgång för att ta emot varje svar och tappade paket. 

Format, storlek och ordning i paketen bestäms av det protokoll som du väljer när du skapar regler för övervakning. Baserat på protokollet för paket, mellanliggande nätverksenheter (routrar, växlar osv.) kan bearbeta paketen på olika sätt. Följaktligen påverkar önskat protokoll precisionen i resultaten. Och önskat protokoll avgör också om du måste vidta alla manuella steg när du distribuerar lösningen NPM. 

NPM kan du välja mellan att ICMP- och TCP-protokoll för att köra syntetiska transaktioner. Om du väljer ICMP när du skapar en regel för syntetisk transaktion, använda NPM-agenter ICMP ECHO-meddelanden för att beräkna Nätverksfördröjningen och paketförlust. ICMP ECHO använder samma meddelande som skickas av konventionella Ping-verktyget. När du använder TCP som protokoll, skickar NPM agenter TCP SYN paket via nätverket. Detta följs av en TCP-handskakning slutförande och sedan ta bort anslutningen via RST paket. 

Tänk på följande innan du väljer ett protokoll som ska användas: 

**Identifiering av flera nätverksvägar.**  TCP är mer exakt när identifiering av flera vägar och den måste färre agenter i varje undernät. En eller två agenter som använder TCP kan exempelvis identifiera alla redundanta sökvägar mellan undernät. Du måste dock flera agenter använder ICMP för att få samma resultat. Med ICMP, om du har ett antal vägar mellan två undernät behöver du fler 5N agenter i källan eller målet undernät. 

**Riktighet resultat.** Routrar och växlar tenderar att tilldela ICMP ECHO-paket jämfört med TCP-paket med lägre prioritet. I vissa situationer när nätverksenheter är högt belastad, avspeglar data hämtas av TCP närmare den förluster eller fördröjningar orsakade av program. Detta inträffar eftersom de flesta programtrafiken flödar över TCP. I sådana fall ger ICMP mindre korrekta resultat jämfört med TCP. 

**Brandväggskonfigurationen.** TCP-protokollet kräver att TCP-paket skickas till en målport. Standardport som används av NPM agenter är 8084, men du kan ändra detta när du konfigurerar agenter. Därför måste du kontrollera att din nätverkets brandväggar eller NSG-regler (i Azure) tillåter trafik på port. Du måste också se till att den lokala brandväggen på datorer där agenter är installerade är konfigurerad för att tillåta trafik på den här porten. Du kan använda PowerShell-skript för att konfigurera brandväggens regler på dina datorer som kör Windows, men du måste konfigurera nätverkets brandvägg manuellt. Däremot fungerar inte ICMP använder port. I de flesta företagsscenarier tillåts ICMP-trafik via brandvägg så att du kan använda verktyg för Nätverksdiagnostik som Ping-verktyget. Så om du kan pinga en dator från en annan, kan du använda ICMP-protokollet utan att behöva konfigurera brandväggar manuellt. 

>[!NOTE] 
> Vissa brandväggar kan blockera ICMP, vilket kan leda till återöverföring ledde till ett stort antal händelser i din information och händelsen hanteringssystem för informationssäkerhet. Kontrollera att det protokoll som du väljer inte blockeras av en nätverkssäkerhetsgrupp brandvägg/NSG, annars NPM inte kunna övervaka nätverkssegmentet. Därför rekommenderar vi att du använder TCP för övervakning. Du bör använda ICMP i scenarier där det går inte att använda TCP, t.ex när: 
>
> - Du använder Windows klientbaserade noder eftersom TCP raw sockets inte tillåts i Windows-klient
> - Ditt nätverks brandvägg/NSG blockerar TCP
> - Så här byter du protokollet 

Om du väljer att använda ICMP under distributionen måste växla du till TCP när som helst genom att redigera standardinställningar för övervakning av regeln:

1. Gå till **nätverksprestanda** > **övervakaren** > **konfigurera**   >  **Övervakaren** och klicka sedan på **standardregel**. 
2. Bläddra till den **protokollet** och välj det protokoll som du vill använda. 
3. Klicka på **spara** inställningen ska användas. 

Även om Standardregeln är med ett visst protokoll, kan du skapa nya regler med ett annat protokoll. Du kan även skapa en blandning av regler om några av reglerna som använder ICMP och en annan med TCP. 

## <a name="walkthrough"></a>Genomgång 

Nu när du har läst om Prestandaövervakaren vi titta på en enkel undersökning grundorsaken till en hälsohändelse.  

På instrumentpanelen i lösningen kan du se att det finns en hälsohändelse – en nätverkslänk är felfri. Du vill undersöka problemet och klicka på **nätverk länkar övervakas** panelen.

På sidan nedbrytning Observera att den **DMZ2 DMZ1** nätverkslänk är felfri. Du klickar på den **visa undernät länkar** länk för den här nätverkslänken. 


Sidan nedåt visar alla undernätverkslänkar i **DMZ2 DMZ1** nätverkslänken. Du märker att fördröjningen för både undernätverkslänkar har passerat tröskelvärdet för att göra nätverkslänken feltillstånd. Du kan också se trenderna svarstid för både undernätverkslänkar. Du kan använda valet av tid kontroll i diagrammet för att fokusera på tidsintervallet som krävs. Du kan se tid på dagen när latens har nått sin belastning. Senare kan du söka efter denna tidsperiod att undersöka problemet. Klicka på **visa nodlänkar** och öka detaljnivån ytterligare. 
 
 ![Undernätverkslänkar](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Liknar föregående sida, nedåt sidan för viss undernätverk länken visar ned dess ingående nodlänkar. Du kan utföra samma åtgärder som du gjorde i föregående steg. Klicka på **visa topologi** att visa topologin mellan två noder. 
 
 ![Nodlänkar](media/log-analytics-network-performance-monitor/node-links.png) 

Alla sökvägar mellan de två valda noderna visas i topologisk karta. Du kan visualisera hopp av hopp topologin av routinginformation mellan två noder i topologisk karta. Den ger dig en tydlig bild av hur många vägar finns mellan två noder och vad sökvägar datapaketen tar. Flaskhalsar i nätverket markeras med röd färg. Du kan hitta en felaktig nätverksanslutning eller en felaktig nätverksenhet genom att titta på red färgade elementen på topologisk karta. 

 ![Topologi instrumentpanelen](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Förlust, svarstid och antal hopp i varje sökväg kan ses i den **åtgärd** fönstret. Använd rullningslisten för att visa information om dessa ohälsosamt sökvägar. Använd filter för att välja sökvägar med feltillstånd hopp så att topologin för bara de valda sökvägarna ritas. Du kan använda mushjulet för att zooma in eller ut topologisk karta. 

I den under bild, kan du tydligt se orsaken till problemområden till specifika avsnitt i nätverket genom att titta på sökvägar och hopp med röd färg. Klicka på en nod i topologisk karta visar egenskaperna för den noden, inklusive det fullständiga Domännamnet och IP-adress. IP-adress för hopp visas när du klickar på ett hopp. 
 
![Topologi instrumentpanelen](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Nästa steg
* [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad nätverket prestanda dataposter.
