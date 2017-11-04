---
title: "Network Performance Monitor-lösning i Azure Log Analytics | Microsoft Docs"
description: "Nätverk i Azure Log Analytics identifiera hjälper dig att övervaka prestanda för ditt nätverk – i nära real-gång till och hitta flaskhalsar i nätverket."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: banders
ms.openlocfilehash: 10e8eeaade5d51b1a15c30802b28600bcf6c72d9
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
---
# <a name="network-performance-monitor-solution-in-log-analytics"></a>Network Performance Monitor-lösning i logganalys

![Nätverket Prestandaövervakaren symbol](./media/log-analytics-network-performance-monitor/npm-symbol.png)

Det här dokumentet beskriver hur installation och användning nätverk Network Performance Monitor-lösning i logganalys som hjälper dig att övervaka prestanda för ditt nätverk – i nära real-gång-om du vill identifiera och leta upp flaskhalsar. Du kan övervaka förluster eller fördröjningar mellan två nätverk, undernät eller servrar med Network Performance Monitor-lösning. Network Performance Monitor identifierar nätverksproblem som trafik blackholing routning fel och problem som vanliga nätverk övervakning metoder inte går att identifiera. Network Performance Monitor genererar aviseringar och meddelar och när ett tröskelvärde komprometteras för en nätverkslänk. Dessa tröskelvärden kan hämtas automatiskt av systemet eller du kan konfigurera dem för att använda anpassade Varningsregler. Network Performance Monitor garanterar snabbt ska kunna identifiera problem med prestanda och localizes källan till problemet till en viss nätverkssegment eller enhet.

Du kan identifiera problem med lösningen instrumentpanelen som visar sammanfattningsinformation om nätverket inklusive senaste hälsa händelser på nätverket, nätverkslänkar och undernätverkslänkar som ställs inför hög paketförlust och latens. Du kan gå nedåt i en nätverkslänk att visa aktuella hälsotillstånd undernätverkslänkar samt nod till nod länkar. Du kan också visa historisk trend förluster eller fördröjningar på nätverket, undernätverk och nod-till-nod-nivå. Du kan identifiera tillfälliga nätverksproblem genom att visa historiska trender diagram för paketförlust och fördröjning och hitta flaskhalsar i nätverk på en topologisk karta. Interaktiva topologi diagrammet kan du visualisera hopp som nexthop nätverksvägar och ta reda på orsaken till problemet. Du kan använda loggen Sök efter olika analytics krav för att skapa anpassade rapporter baserat på de data som samlas in av Network Performance Monitor som andra lösningar.

Lösningen använder syntetiska transaktioner som primär metod för att identifiera nätverksfel. Därför kan du använda det utan hänsyn till en viss nätverksenhet tillverkare eller modell. Den fungerar även över lokala molnet (IaaS) och hybridmiljöer. Lösningen upptäcker automatiskt nätverkets topologi och olika vägar i nätverket.

Vanliga nätverk övervakning produkter fokusera på att övervaka hälsotillståndet för nätverk-enhet (routrar, växlar osv), men ger inte insikter om den faktiska kvaliteten på nätverksanslutning mellan två platser, vilket gör Network Performance Monitor.

### <a name="using-the-solution-standalone"></a>Med fristående lösning
Om du vill övervaka kvaliteten på nätverksanslutning mellan sina kritiska arbetsbelastningar, kan nätverk, Datacenter eller office platser och du använda Network Performance Monitor-lösningen ensamt för att övervaka hälsotillståndet för anslutningen mellan:

* flera Datacenter eller office platser som är anslutna via ett offentligt eller privat nätverk
* viktiga arbetsbelastningar som körs affärsprogram
* offentliga molntjänster som Microsoft Azure eller Amazon Web Services (AWS) och lokala nätverk, om du har IaaS (VM) tillgänglig och du har gateways som konfigurerats för att tillåta kommunikation mellan lokala nätverk och moln-nätverk
* Azure och lokala nätverk när du använder Express Route

### <a name="using-the-solution-with-other-networking-tools"></a>Med lösningen med andra verktyg för nätverk
Om du vill övervaka en driftsapplikationer kan du använda Network Performance Monitor-lösning som en tillhörande lösning till andra verktyg. Ett långsamt nätverk kan leda till långsam program och Network Performance Monitor kan hjälpa dig att undersöka problem med prestanda som orsakas av underliggande nätverksproblem. Lösningen inte kräver någon åtkomst till nätverksenheter, behöver programadministratören inte eftersom förlitar sig på ett nätverk team att ge information om hur nätverket påverkar program.

Även om du redan investerar i andra nätverksövervakningsverktyg kan sedan lösningen kompletterar dessa verktyg eftersom de flesta traditionella övervakning nätverkslösningar inte tillhandahåller insikter om slutpunkt till slutpunkt prestandamått som förluster eller fördröjningar.  Network Performance Monitor-lösningen hjälper dig att fylla den lucka.

## <a name="installing-and-configuring-agents-for-the-solution"></a>Installera och konfigurera agenter för lösningen
Använd basic-processer för att installera agenter på [ansluta Windows-datorer till logganalys](log-analytics-windows-agents.md) och [ansluta Operations Manager till logganalys](log-analytics-om-agents.md).

> [!NOTE]
> Du måste installera minst 2 agenter för att få tillräckligt med data för att identifiera och övervaka dina nätverksresurser. Annars förblir lösningen konfigurera statusen tills du installerar och konfigurerar ytterligare agenter.
>
>

### <a name="where-to-install-the-agents"></a>Var du vill installera agenter
Innan du installerar agenter, Överväg topologin hos ditt nätverk och vilka delar av nätverket som du vill övervaka. Vi rekommenderar att du installerar fler än en agent för varje undernät som du vill övervaka. Med andra ord för varje undernät som du vill övervaka, välja två eller flera servrar eller virtuella datorer och installera agenten på dem.

Om du är osäker på om nätverkets topologi, installera agenter på servrar med viktiga arbetsbelastningar där du vill övervaka nätverkets prestanda. Du kanske vill hålla reda på en nätverksanslutning mellan en webbserver och en server som kör SQL Server. I det här exemplet skulle du installerar en agent på båda servrarna.

Agenter övervakar nätverksanslutning (länkar) mellan värdar--inte själva värdarna. Så om du vill övervaka en nätverkslänk, måste du installera agenter på båda slutpunkter för länken.

### <a name="configure-agents"></a>Konfigurera agenter

Om du tänker använda ICMP-protokollet för syntetiska transaktioner måste du aktivera följande brandväggsregler för att på ett tillförlitligt sätt använda ICMP:

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow
```


Om du planerar att använda TCP-protokollet måste du öppna portar i brandväggen för dessa datorer för att kontrollera att agenterna kan kommunicera. Du måste hämta och kör den [EnableRules.ps1 PowerShell-skript](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) utan några parametrar i ett PowerShell-fönster med administratörsbehörighet.

Skriptet skapar registernycklar som krävs av Network Performance Monitor och skapar Windows brandväggsregler som tillåter agenter för att skapa TCP-anslutningar med varandra. Registernycklar som skapats av skriptet ange om du vill logga debug-loggar och sökvägen för filen loggar. Den definierar även agent TCP-port som används för kommunikation. Värden för nycklarna anges automatiskt av skript, så du inte bör ändra dessa nycklar manuellt.

Porten öppnas som standard är 8084. Du kan använda en anpassad port genom att ange parametern `portNumber` i skriptet. Men ska samma port användas på alla datorer där skriptet körs.

> [!NOTE]
> Skriptet EnableRules.ps1 konfigurerar regler för Windows-brandväggen endast på den dator där skriptet körs. Om du har en brandvägg för nätverk, bör du kontrollera att den tillåter trafik för TCP-porten som används av Network Performance Monitor.
>
>

## <a name="configuring-the-solution"></a>Konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

1. Network Performance Monitor-lösningen hämtar data från datorer som kör Windows Server 2008 SP 1 eller senare eller Windows 7 SP1 eller senare, vilket är samma krav som Microsoft Monitoring Agent (MMA). NPM agenter kan också köra på Windows desktop/client-operativsystem (Windows 10, Windows 8.1, Windows 8 och Windows 7).
    >[!NOTE]
    >Agenter för Windows server-operativsystem stöder både TCP och ICMP som protokoll för syntetisk transaktion. Agenter för Windows-klientoperativsystem stöder dock endast ICMP som protokoll för syntetisk transaktion.

2. Lägga till Network Performance Monitor-lösningen till arbetsytan från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).<br><br> ![Nätverket Prestandaövervakaren symbol](./media/log-analytics-network-performance-monitor/npm-symbol.png)  
3. I OMS-portalen visas en ny panel med titeln **Network Performance Monitor** med meddelandet *lösningen kräver ytterligare konfiguration*. Klicka på panelen för att navigera till den **distribution** fliken och markera protokollet som ska användas för att göra de syntetiska transaktionerna för övervakning av nätverket.  Granska [välja rätt protokoll-ICMP- eller TCP](#choose-the-right-protocol-icmp-or-tcp) för att välja rätt protokoll som är bäst för ditt nätverk.<br><br> ![lösningen kräver val](media/log-analytics-network-performance-monitor/log-analytics-netmon-perf-welcome.png)<br><br>

4. När du har valt protokollen du omdirigeras till den **OMS översikt** sidan. När lösningen samlar in data från ditt nätverk, nätverket Prestandaövervakaren översikt över ikonen att visa meddelande om *Dataaggregering pågår*.<br><br> ![lösning för datainsamling](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-01.png)<br><br>
5. När data samlas in och indexerade, översikt över panelen ändrar och ange måste du utföra ytterligare konfiguration.<br><br> ![lösning panelen kräver ytterligare konfiguration](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-02.png)<br><br>
6. Klicka på panelen och börja konfigurera lösningen följa stegen nedan.

### <a name="create-new-networks"></a>Skapa nya nätverk
Ett nätverk i Prestandaövervakaren för nätverk är en logisk behållare för undernät. Du kan skapa ett nätverk med ett eget namn och Lägg till undernät i den enligt affärslogik. Du kan till exempel skapa ett nätverk med namnet *London* och Lägg till alla undernät i datacentrets London eller ett nätverk med namnet *ContosoFrontEnd* och Lägg till alla undernät som betjänar klientdelen för din app med namnet Contoso i det här nätverket.
På konfigurationssidan ser du ett nätverk med namnet **standard** på fliken nätverk. Om du inte skapat några nätverk placeras alla automatiskt identifieras undernät i standardnätverk.
När du skapar ett nätverk kan du lägga till ett undernät till den och undernätet tas bort från standardnätverk. Om du tar bort ett nätverk, returneras alla undernät automatiskt till standard-nätverk.
Därför fungerar standardnätverk som en behållare för alla undernät som inte ingår i en användardefinierad nätverk. Du kan inte redigera eller ta bort standardnätverk. Det finns alltid i systemet. Du kan dock skapa så många anpassade nätverk som du behöver.
I de flesta fall undernät i din organisation ska ordnas i flera nätverk och du måste skapa ett eller flera nätverk för att gruppera dina undernät enligt affärslogik

#### <a name="to-create-a-new-network"></a>Skapa ett nytt nätverk
1. Klicka på **Lägg till nätverket** och Skriv nätverkets namn och beskrivning.
2. Välj ett eller flera undernät och klicka sedan på **Lägg till**.
3. Klicka på **spara** att spara konfigurationen.<br><br> ![lägga till nätverk](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>Vänta tills Datasammanställning
När du har sparat konfigurationen för första gången, startar lösningen samla in paket förluster eller fördröjningar nätverksinformation mellan noderna där agenter är installerade. Den här processen kan ta en stund ibland över 30 minuter. Under det här tillståndet Network Performance Monitor-panelen på översiktssidan visas ett meddelande om *Datasammanställning pågående*.

![Dataaggregering pågår](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

När data har överförts visas Network Performance Monitor panelen uppdateras visar data.

![Prestandaövervakaren nätverksikon](./media/log-analytics-network-performance-monitor/npm-tile.png)

Klicka på panelen om du vill visa infopanelen Network Performance Monitor.

![Nätverket Prestandaövervakaren instrumentpanelen](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Redigera inställningarna för övervakning för undernät
Visas alla undernät där minst en agent har installerats på den **undernätverk** fliken i konfigurationssidan.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Aktivera eller inaktivera övervakning för specifika undernät
1. Markera eller avmarkera kryssrutan bredvid den **ID för undernätverk** och se till att **för övervakning** är markerad eller avmarkerad, vid behov. Du kan markera eller avmarkera flera undernät. När den är inaktiverad, övervakas inte undernätverk som agenterna uppdateras för att stoppa pinga andra agenter.
2. Välj de noder som du vill övervaka för en viss undernätverk genom att välja undernätverk i listan och flytta krävs noderna mellan listor som innehåller oövervakade och övervakade noder.
   Du kan lägga till en anpassad **beskrivning** till undernätverk, om du vill.
3. Klicka på **spara** att spara konfigurationen.<br><br> ![Redigera undernätet](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Välj noder för att övervaka
Alla noder som har en agent som installerats på de visas i den **noder** fliken.

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Aktivera eller inaktivera övervakning för noder
1. Markera eller avmarkera de noder som du vill övervaka eller stoppa övervakningen.
2. Klicka på **för övervakning**, eller avmarkera den efter behov.
3. Klicka på **Spara**.<br><br> ![aktivera övervakning av nod](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>Ange regler för övervakning
Network Performance Monitor genererar hälsa händelser när tröskelvärdet för prestandan hos Nätverksanslutningar mellan 2 undernät eller mellan 2 nätverk överskrids. Dessa tröskelvärden kan hämtas automatiskt av systemet eller du kan ange anpassade tröskelvärden.
Systemet skapar automatiskt en standardregel som genererar en hälsohändelse när förlust eller fördröjning mellan ett par nätverk/undernätverk länkar överträdelser system lärt dig tröskelvärdet. Detta hjälper lösningen övervaka nätverkets infrastruktur tills du uttryckligen inte har skapat några regler för övervakning. Om Standardregeln är aktiverad kan skicka alla noder syntetiska transaktioner till alla andra noder som du har aktiverat för övervakning. Standardregeln är användbart vid små nätverk, till exempel i ett scenario där du har ett litet antal servrar som kör en mikrotjänster och du vill se till att alla o servrarna har anslutning till varandra.

>[!NOTE]
>Vi rekommenderar starkt att du inaktiverar Standardregeln och skapa anpassade regler för övervakning, särskilt vid stora nätverk där du använder ett stort antal noder för övervakning. Detta minskar den trafik som genereras av lösningen och hjälper dig att ordna övervakning av nätverket.

Skapa anpassade regler för övervakning enligt affärslogik. Till exempel om du vill övervaka prestanda för nätverksanslutningen 2 office platser headquarter sedan gruppera alla undernät i office site1 i nätverket O1, alla undernät i office webbplats2 i nätverket O2 och alla undernät i headquarter i nätverket H. Skapa 2 övervakning regler-ett mellan O1 och H och andra mellan O2 och H.


#### <a name="to-create-custom-monitoring-rules"></a>Att skapa anpassade regler för övervakning
1. Klicka på **Lägg till regel** i den **övervakaren** och ange namn och beskrivning.
2. Välj paret med nätverks- eller undernätverk länkar för att övervaka i listan.
3. Först välja nätverket första undernätverk/s intressanta finns i nätverket listrutan och välj sedan undernätverk/s i motsvarande undernätverk listrutan.
   Välj **alla undernät** om du vill övervaka alla undernät i en nätverkslänk. På liknande sätt väljer du den andra undernätverk/s av intresse. Och du kan klicka på **Lägg till undantag** för att utesluta övervakning för specifika undernätverkslänkar från den val.
4. [Välj mellan ICMP- och TCP-protokoll](#choose-the-right-protocol-icmp-or-tcp) för att köra syntetiska transaktioner.
5. Om du inte vill skapa hälsa händelser för de objekt som du har valt, sedan avmarkera **aktivera övervakning av hälsotillstånd på länkarna som omfattas av den här regeln**.
6. Välj övervakning villkor.
   Du kan ange anpassade tröskelvärden för hälsotillstånd händelse genereras genom att skriva tröskelvärden. När värdet för villkoret går över dess valda tröskelvärdet för det valda nätverk/undernätverk paret, skapas en hälsohändelse.
7. Klicka på **spara** att spara konfigurationen.<br><br> ![Skapa anpassad regel för övervakning](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

När du har sparat en regel för övervakning, du kan integrera regeln med Alert Management genom att klicka på **skapa avisering**. En aviseringsregel skapas automatiskt med sökvillkoret och andra nödvändiga parametrar automatiskt ifyllda. Med hjälp av en aviseringsregel, du kan ta emot e-postbaserad aviseringar, förutom befintliga aviseringar i NPM. Aviseringar kan också utlösa vidtar åtgärder med runbooks eller de kan integrera med befintliga lösningar tjänsten med hjälp av webhooks. Du kan klicka på **hantera avisering** redigera inställningar för avisering.

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>Välja rätt protokoll-ICMP- eller TCP

Network Performance Monitor (NPM) använder syntetiska transaktioner för att beräkna prestandamått för nätverket som paketet dataförlust och länka svarstid. För att förstå det bättre att överväga en NPM-agent som är ansluten till ett end för en nätverkslänk. Den här NPM-agenten skickar avsökningen paket till en andra NPM-agent som är ansluten till en annan slutet av nätverket. Andra agenten svarar med svarspaket. Den här processen upprepas några gånger. Första NPM agenten utvärderar länkfördröjningen genom att mäta antalet svar och tidsåtgång för att ta emot varje svar och tappade paket.

Format, storlek och ordning i paketen bestäms av det protokoll som du väljer när du skapar regler för övervakning. Baserat på protokollet för paket, mellanliggande nätverksenheter (routrar, växlar osv.) kan bearbeta paketen på olika sätt. Följaktligen påverkar önskat protokoll precisionen i resultaten. Och önskat protokoll avgör också om du måste vidta alla manuella steg när du distribuerar lösningen NPM.

NPM kan du välja mellan att ICMP- och TCP-protokoll för att köra syntetiska transaktioner.
Om du väljer ICMP när du skapar en regel för syntetisk transaktion, använda NPM-agenter ICMP ECHO-meddelanden för att beräkna Nätverksfördröjningen och paketförlust. ICMP ECHO använder samma meddelande som skickas av konventionella Ping-verktyget. När du använder TCP som protokoll, skickar NPM agenter TCP SYN paket via nätverket. Detta följs av en TCP-handskakning slutförande och sedan ta bort anslutningen via RST paket.

#### <a name="points-to-consider-before-choosing-the-protocol"></a>Saker att tänka på innan du väljer protokollet
Tänk på följande innan du väljer ett protokoll som ska användas:

##### <a name="discovering-multiple-network-routes"></a>Identifiering av flera nätverksvägar
TCP är mer exakt när identifiering av flera vägar och den måste färre agenter i varje undernät. En eller två agenter som använder TCP kan exempelvis identifiera alla redundanta sökvägar mellan undernät. Du måste dock flera agenter använder ICMP för att få samma resultat. Med ICMP, om du har *N* antal vägar mellan två undernät som du behöver mer än 5*N* agenter i källan eller målet undernät.

##### <a name="accuracy-of-results"></a>Riktighet resultat
Routrar och växlar tenderar att tilldela ICMP ECHO-paket jämfört med TCP-paket med lägre prioritet. I vissa situationer när nätverksenheter är högt belastad, avspeglar data hämtas av TCP närmare den förluster eller fördröjningar orsakade av program. Detta inträffar eftersom de flesta programtrafiken flödar över TCP. I sådana fall ger ICMP mindre korrekta resultat jämfört med TCP.

##### <a name="firewall-configuration"></a>Brandväggskonfiguration
TCP-protokollet kräver att TCP-paket skickas till en målport. Standardport som används av NPM agenter är 8084, men du kan ändra detta när du konfigurerar agenter. Därför måste du kontrollera att din nätverkets brandväggar eller NSG-regler (i Azure) tillåter trafik på port. Du måste också se till att den lokala brandväggen på datorer där agenter är installerade är konfigurerad för att tillåta trafik på den här porten.

Du kan använda PowerShell-skript för att konfigurera brandväggens regler på dina datorer som kör Windows, men du måste konfigurera nätverkets brandvägg manuellt.

Däremot fungerar inte ICMP använder port. I de flesta företagsscenarier tillåts ICMP-trafik via brandvägg så att du kan använda verktyg för Nätverksdiagnostik som Ping-verktyget. Så om du kan pinga en dator från en annan, kan du använda ICMP-protokollet utan att behöva konfigurera brandväggar manuellt.

> [!NOTE]
> Vissa brandväggar kan blockera ICMP, vilket kan leda till återöverföring ledde till ett stort antal händelser i din information och händelsen hanteringssystem för informationssäkerhet. Kontrollera att det protokoll som du väljer inte blockeras av en nätverkssäkerhetsgrupp brandvägg/NSG, annars NPM kommer inte att kunna övervaka nätverkssegmentet.  Därför rekommenderar vi att du använder TCP för övervakning. Du bör använda ICMP i scenarier där det går inte att använda TCP, t.ex när:
> * Du använder Windows klientbaserad noder eftersom TCP raw sockets inte tillåts i Windows-klient
> * Ditt nätverks brandvägg/NSG blockerar TCP


#### <a name="how-to-switch-the-protocol"></a>Så här byter du protokollet

Om du väljer att använda ICMP under distributionen måste växla du till TCP när som helst genom att redigera standardinställningar för övervakning av regeln.

##### <a name="to-edit-the-default-monitoring-rule"></a>Så här redigerar du standardinställningar för övervakning av regel
1.  Gå till **nätverksprestanda** > **övervakaren** > **konfigurera** > **övervakaren** och klicka sedan på **standardregel**.
2.  Bläddra till den **protokollet** och välj det protokoll som du vill använda.
3.  Klicka på **spara** inställningen ska användas.

Även om Standardregeln är med ett visst protokoll, kan du skapa nya regler med ett annat protokoll. Du kan även skapa en blandning av regler om några av reglerna som använder ICMP och en annan med TCP.


## <a name="data-collection-details"></a>Information om samlingen
Network Performance Monitor använder TCP SYN-SYNACK-ACK handskakning paket när du har valt TCP och ICMP ECHO ICMP ECHO SVARSMEDDELANDEN när ICMP är valt som protokoll för att samla in information om förluster eller fördröjningar. Traceroute används också för att hämta information om nätverkstopologin.

I följande tabell visar metoder för insamling av data och annan information om hur data samlas in för Network Performance Monitor.

| Plattform | Styr Agent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agent data som skickas via management-grupp | Frekvens för samlingen |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP-handskakningar på/ICMP ECHO-meddelanden var femte sekund data skickas var 3: e minut |

Lösningen använder syntetiska transaktioner för att utvärdera hälsan för nätverket. OMS-agenter som installerats på olika punkt i nätverkspaket exchange TCP eller ICMP Echo (beroende på det protokoll som valts för övervakning) med varandra. I processen dig agenter fram och åter tid och paket förlust, eventuella. Varje agent med jämna mellanrum, utför även en spårningsrutt för att andra agenter för att hitta alla olika vägar i nätverket som ska testas. Med dessa data kan härleda agenterna Nätverksfördröjningen och paket förlustsiffror. Testerna upprepas var femte sekund och data har aggregerats under en period på tre minuter med agenterna innan den skickas till logganalys-tjänsten.

> [!NOTE]
> Även om agenter är ofta kommunicerar med varandra, genererar de inte mycket nätverkstrafik när du utför testerna. Agenter använda enbart TCP SYN-SYNACK-ACK handskakning paket att fastställa förluster eller fördröjningar – inga data som utbyts paket. Under den här processen agenter kommunicerar med varandra endast vid behov och kommunikationstopologin agent är optimerad för att minska nätverkstrafiken.
>
>

## <a name="using-the-solution"></a>Använda lösningen
Det här avsnittet beskriver alla instrumentpanelen funktioner och hur de används.

### <a name="solution-overview-tile"></a>Lösning: översikt sida vid sida
När du har aktiverat Network Performance Monitor-lösningen ger en snabb överblick över nätverkets tillstånd panelen lösning på översiktssidan OMS. Ett ringdiagram visar antalet felfritt och feltillstånd undernätverkslänkar visas. När du klickar på ikonen öppnas lösning instrumentpanelen.

![Prestandaövervakaren nätverksikon](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>Nätverket Prestandaövervakaren lösning instrumentpanelen
Den **nätverk sammanfattning** bladet visar en översikt över nätverk tillsammans med relativ storlek. Detta följs av paneler visar totalt antal nätverkslänkar, undernät länkar och sökvägar i systemet (en sökväg som består av IP-adresserna för två värdar med agenter och alla hopp mellan dem).

Den **upp händelser på nätverket hälsa** bladet visar en lista över senaste hälsa händelser och aviseringar i systemet och hur lång tid eftersom händelsen har varit aktiv. En hälsohändelse eller en varning genereras när paketförlust eller latens för en länk för nätverk eller undernätverk överskrider ett tröskelvärde.

Den **upp ohälsosamt nätverkslänkar** bladet visar en lista över nätverkslänkar. Dessa är nätverkslänkar som har en eller flera negativa hälsohändelse för dem för tillfället.

Den **upp Undernätverkslänkar med mest förlust** och **Undernätverkslänkar med mest svarstid** blad Visa översta undernätverkslänkar genom paketförlust och främsta undernätverkslänkar efter svarstid respektive. Fördröjningar eller vissa mängden paketförlust kan förväntas på vissa nätverkslänkarna. Dessa länkar visas i den översta tio listan men är inte dåligt hälsotillstånd.

Den **vanliga frågor** bladet innehåller en uppsättning sökfrågor som hämta rådata för nätverksövervakning data direkt. Du kan använda de här frågorna som utgångspunkt för att skapa egna frågor för anpassad rapportering.

![Nätverket Prestandaövervakaren instrumentpanelen](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>Gå nedåt för djup
Du kan klicka på olika länkar på lösningen instrumentpanelen för att gå nedåt djupare i alla intresseområde. När du ser en avisering eller ett feltillstånd nätverkslänken som visas på instrumentpanelen kan du till exempel klicka att undersöka vidare. Vidarebefordras du till en sida som visar alla undernätverkslänkar för nätverks-länk. Du kommer att kunna se status för dataförlust, svarstid och hälsotillståndet för varje undernätverk länk och snabbt ta reda på vilka undernätverkslänkar som orsakar problemet. Du kan klicka **visa nodlänkar** att se alla länkar i noden för länken ohälsosamt undernät. Du kan sedan se enskilda nod till nod länkar och hitta nodlänkar.

Du kan klicka på **visa topologi** att visa hopp av hopp topologin vägar mellan käll- och noderna. Feltillstånd vägar eller hopp visas i rött så att du snabbt kan identifiera problemet till en viss del av nätverket.

![specificera-data](./media/log-analytics-network-performance-monitor/npm-drill.png)

### <a name="network-state-recorder"></a>Nätverket lådan

Varje visas en ögonblicksbild av nätverkets tillstånd vid en viss tidpunkt. Senaste status visas som standard. Längst upp på sidan visar punkten i tiden som status visas. Du kan välja att gå tillbaka i tiden och visa ögonblicksbilden av nätverkets tillstånd genom att klicka på listen på **åtgärder**. Du kan också välja att aktivera eller inaktivera automatisk uppdatering för en sida när du visar det aktuella tillståndet.

![nätverket](./media/log-analytics-network-performance-monitor/network-state.png)

#### <a name="trend-charts"></a>Trend diagram
På varje nivå som du nedåt, du kan se trend förluster eller fördröjningar för en nätverkslänk. Trend diagram är också tillgängliga för undernätverk och nod-länkar. Du kan ändra tidsintervallet för diagram ska ritas med hjälp av kontrollen tid överst i diagrammet.

Trend diagram visar en historisk perspektiv för prestandan hos en nätverkslänk. Vissa nätverksproblem är tillfälligt till sin natur och är svåra att fånga endast genom att titta på det aktuella tillståndet för nätverket. Detta beror på att problem kan ansluta snabbt och försvinner innan alla meddelanden enbart för att visas igen vid en senare tidpunkt. Sådana tillfälliga problem kan också vara svårt för administratörer eftersom de utfärdar ofta yta som oförklarade ökningen av svarstiden programmet, även när alla programkomponenter ut att fungera smidigt.

Du kan enkelt identifiera dessa typer av problem genom att titta på trenddiagram där problemet visas som en plötslig topp i Nätverksfördröjningen eller paketförlust.

![trenddiagram](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>hopp av hopp topologisk karta
Network Performance Monitor visas topologin som hopp av hopp av routinginformation mellan två noder i en interaktiv topologisk karta. Du kan visa topologisk karta genom att markera en nod länk och sedan klicka på **visa topologi**. Du kan också visa topologisk karta genom att klicka på **sökvägar** panelen på instrumentpanelen. När du klickar på **sökvägar** på instrumentpanelen, måste du markera käll- och noder från den vänstra panelen och klicka sedan på **ritytans** ska ritas vägar mellan två noder.

Topologisk karta visar hur många vägar mellan två noder och vad är sökvägar datapaketen ta. Flaskhalsar i nätverket är markerat i rött på topologisk karta. Du kan hitta en felaktig nätverksanslutning eller en felaktig nätverksenhet genom att titta på red färgade elementen på topologisk karta.

När du klickar på en nod eller hovra över den på topologisk karta, visas egenskaperna för noden som FQDN och IP-adressen. Klicka på ett hopp finns den IP-adress. Du kan välja att filtrera särskilt vägar genom att använda filter i åtgärdsrutan döljas. Och du kan också förenkla nätverkstopologier genom att dölja mellanliggande hopp med skjutreglaget i åtgärdsfönstret. Du kan zooma in eller out-of-topologisk karta med mushjulet.

Observera att topologin visas på kartan är nivå 3-topologi och innehåller inte nivå 2-enheter och anslutningar.

![hopp av hopp topologisk karta](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Lokalisering av fel
Network Performance Monitor kan hitta flaskhalsar i nätverk utan att ansluta till nätverksenheter. Network Performance Monitor gör baserat på de data som samlas in från nätverket och genom att använda avancerade algoritmer på diagrammet nätverk, en probabilistic uppskattning av de delar av nätverket som är mest sannolika orsaken till problemet.

Den här metoden är användbar för att avgöra flaskhalsarna i nätverket när åtkomst till hopp är inte tillgängligt eftersom det inte kräver några data samlas in från nätverksenheterna som routrar och växlar. Detta är också användbart när hopp mellan två noder som inte ingår i din administrativ kontroll. Hopp kanske till exempel Internet-routrar.

### <a name="log-analytics-search"></a>Logganalys Sök
Alla data som är exponerade grafiskt via Network Performance Monitor-instrumentpanelen och nedåt sidor finns inbyggt i logganalys-sökning. Du kan fråga efter data med hjälp av frågespråket Sök och skapa anpassade rapporter genom att exportera data till Excel eller PowerBI. Den **vanliga frågor** bladet på instrumentpanelen har vissa användbara frågor som du kan använda som utgångspunkt för att skapa egna frågor och rapporter.

![sökfrågor](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>Undersök orsaken till en avisering om hälsa
Nu när du har läst om Network Performance Monitor vi titta på en enkel undersökning grundorsaken till en hälsohändelse.

1. På sidan Översikt över får du en ögonblicksbild av hälsotillståndet för ditt nätverk genom att följa den **Network Performance Monitor** panelen. Observera att utanför 6 undernätverk länkar som övervakas, 2 är felfria. Detta garanterar undersökning. Klicka på panelen om du vill visa instrumentpanelen för lösningen.<br><br> ![Prestandaövervakaren nätverksikon](./media/log-analytics-network-performance-monitor/npm-investigation01.png)  
2. I exemplet bilden nedan märker du att det finns en hälsohändelse som en nätverkslänk som är i feltillstånd. Du vill undersöka problemet och klicka på den **DMZ2 DMZ1** nätverkslänken ta reda på roten till problemet.<br><br> ![exempel ohälsosamt nätverk](./media/log-analytics-network-performance-monitor/npm-investigation02.png)  
3. Sidan nedåt visar alla undernätverkslänkar i **DMZ2 DMZ1** nätverkslänken. Lägg märke till att svarstiden för både undernätverkslänkar har passerat tröskelvärdet för att göra nätverkslänken feltillstånd. Du kan också se trenderna svarstid för både undernätverkslänkar. Du kan använda valet av tid kontroll i diagrammet för att fokusera på tidsintervallet som krävs. Du kan se tid på dagen när latens har nått sin belastning. Senare kan du söka efter denna tidsperiod att undersöka problemet. Klicka på **visa nodlänkar** till nedåt ytterligare.<br><br> ![feltillstånd undernät länkar exempel](./media/log-analytics-network-performance-monitor/npm-investigation03.png) 
4. Liknar föregående sida, nedåt sidan för viss undernätverk länken visar ned dess ingående nodlänkar. Du kan utföra samma åtgärder som du gjorde i föregående steg. Klicka på **visa topologi** att visa topologi mellan 2 noderna.<br><br> ![felaktiga noden länkar exempel](./media/log-analytics-network-performance-monitor/npm-investigation04.png)  
5. Alla sökvägar mellan noderna 2 ritas i topologisk karta. Du kan visualisera hopp av hopp topologin av routinginformation mellan två noder i topologisk karta. Den ger dig en tydlig bild av hur många vägar finns mellan två noder och vad sökvägar datapaketen tar. Flaskhalsar i nätverket markeras med röd färg. Du kan hitta en felaktig nätverksanslutning eller en felaktig nätverksenhet genom att titta på red färgade elementen på topologisk karta.<br><br> ![exemplet ohälsosamt topologi](./media/log-analytics-network-performance-monitor/npm-investigation05.png)  
6. Förlust, svarstid och antal hopp i varje sökväg kan ses i den **åtgärd** fönstret. Använd rullningslisten för att visa information om dessa ohälsosamt sökvägar.  Använd filter för att välja sökvägar med feltillstånd hopp så att topologin för bara de valda sökvägarna ritas. Du kan använda mushjulet för att zooma in eller ut topologisk karta.

   I den under bilden visas tydligt orsaken till problemområden till specifika avsnitt i nätverket genom att titta på sökvägar och hopp med röd färg. Klicka på en nod i topologisk karta visar egenskaperna för den noden, inklusive det fullständiga Domännamnet och IP-adress. IP-adress för hopp visas när du klickar på ett hopp.<br><br> ![feltillstånd topologi - sökvägen information exempel](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="provide-feedback"></a>Ge feedback

- **UserVoice** -du kan publicera dina idéer för Network Performance Monitor-funktioner som du vill att vi ska arbeta med. Besök vår [UserVoice sidan](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring).
- **Ansluta till vår kommittén** -vi alltid är intresserad av att nya kunder ansluta till vår kommittén. Som en del av det kan du få en förhandsåtkomst till nya funktioner och hjälp oss att förbättra Network Performance Monitor. Om du är intresserad av att koppla kan fylla i det här [snabb undersökning](https://aka.ms/npmcohort).

## <a name="next-steps"></a>Nästa steg
* [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad nätverket prestanda dataposter.
