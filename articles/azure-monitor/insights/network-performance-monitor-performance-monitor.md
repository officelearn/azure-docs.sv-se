---
title: Prestandaövervakaren i övervakare av nätverksprestanda
description: Prestandaövervakaren i Network Performance Monitor hjälper dig att övervaka nätverksanslutningen över olika punkter i nätverket. Du kan övervaka molndistributioner och lokala platser, flera datacenter och filialkontor och verksamhetskritiska multitierprogram eller mikrotjänster.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 126cca9d3606b378e59e4f4e1c5b52d985d19d94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055696"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Lösning för nätverksprestandaövervakning: Prestandaövervakning

Prestandaövervakaren i [Network Performance Monitor](network-performance-monitor.md) hjälper dig att övervaka nätverksanslutningen över olika punkter i nätverket. Du kan övervaka molndistributioner och lokala platser, flera datacenter och filialkontor och verksamhetskritiska multitierprogram eller mikrotjänster. Med Performance Monitor kan du identifiera nätverksproblem innan användarna klagar. De viktigaste fördelarna är att du kan: 

- Övervaka förlust och svarstid i olika undernät och ställ in aviseringar.
- Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket.
- Felsöka tillfälliga och punkt-i-tid nätverksproblem, som är svåra att replikera.
- Bestäm det specifika segmentet i nätverket, som ansvarar för försämrad prestanda.
- Övervaka nätverkets hälsa, utan att behöva SNMP.


![Övervakning av nätverksprestanda](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguration
Om du vill öppna konfigurationen för Network Performance Monitor öppnar du [lösningen För nätverksprestandaövervakare](network-performance-monitor.md)och väljer **Konfigurera**.

![Konfigurera Övervakare av nätverksprestanda](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Skapa nya nätverk

Ett nätverk i Network Performance Monitor är en logisk behållare för undernät. Det hjälper dig att organisera övervakningen av din nätverksinfrastruktur efter dina behov. Du kan skapa ett nätverk med ett eget namn och lägga till undernät i det enligt din affärslogik. Du kan till exempel skapa ett nätverk med namnet London och lägga till alla undernät i londondatacentret. Du kan också skapa ett nätverk med namnet *ContosoFrontEnd* och lägga till alla undernät som heter Contoso i nätverket i nätverket. Lösningen skapar automatiskt ett standardnätverk som innehåller alla undernät som har identifierats i din miljö. 

När du skapar ett nätverk lägger du till ett undernät i det. Sedan tas det undernätet bort från standardnätverket. Om du tar bort ett nätverk returneras alla dess undernät automatiskt till standardnätverket. Standardnätverket fungerar som en behållare för alla undernät som inte finns i något användardefinierat nätverk. Du kan inte redigera eller ta bort standardnätverket. Det finns alltid kvar i systemet. Du kan skapa så många anpassade nätverk som du behöver. I de flesta fall är undernäten i organisationen ordnade i mer än ett nätverk. Skapa ett eller flera nätverk för att gruppera undernäten efter din affärslogik.

Så här skapar du ett nytt nätverk:


1. Välj fliken **Nätverk.**
1. Välj **Lägg till nätverk**och ange sedan nätverksnamn och beskrivning. 
2. Markera ett eller flera undernät och välj sedan **Lägg till**. 
3. Välj **Spara** om du vill spara konfigurationen. 


### <a name="create-monitoring-rules"></a>Skapa övervakningsregler 

Prestandaövervakaren genererar hälsohändelser när tröskelvärdet för nätverksanslutningars prestanda mellan två undernätverk eller mellan två nätverk bryts. Systemet kan lära sig dessa tröskelvärden automatiskt. Du kan också ange anpassade tröskelvärden. Systemet skapar automatiskt en standardregel som genererar en hälsohändelse när förlust eller svarstid mellan ett par nätverks- eller undernätverkslänkar bryter mot tröskelvärdet för systeminlärd. Den här processen hjälper lösningen att övervaka nätverksinfrastrukturen tills du inte har skapat några övervakningsregler uttryckligen. Om standardregeln är aktiverad skickar alla noder syntetiska transaktioner till alla andra noder som du har aktiverat för övervakning. Standardregeln är användbar med små nätverk. Ett exempel är ett scenario där du har ett litet antal servrar som kör en mikrotjänst och du vill vara säker på att alla servrar har anslutning till varandra.

>[!NOTE]
> Vi rekommenderar att du inaktiverar standardregeln och skapar anpassade övervakningsregler, särskilt med stora nätverk där du använder ett stort antal noder för övervakning. Anpassade övervakningsregler kan minska den trafik som genereras av lösningen och hjälpa dig att organisera övervakningen av nätverket.

Skapa övervakningsregler enligt affärslogiken. Ett exempel är om du vill övervaka prestanda för nätverksanslutningen för två kontorsplatser till huvudkontoret. Gruppera alla undernät på kontorsplats1 i nätverk O1. Gruppera sedan alla undernät på kontorsplats2 i nätverk O2. Slutligen gruppera alla undernät i huvudkontoret i nätverk H. Skapa två övervakningsregler - ett mellan O1 och H och det andra mellan O2 och H. 

Så här skapar du anpassade övervakningsregler:

1. Välj **Lägg till regel** på fliken **Övervakar** och ange regelnamn och beskrivning.
2. Välj det par av nätverks- eller undernätverkslänkar som ska övervakas i listorna. 
3. Välj det nätverk som innehåller de undernätverk du vill använda i den nedrullningsbara nätverksrutan. Välj sedan undernätverket i motsvarande nedrullningsbara undernätslista. Om du vill övervaka alla undernätverk i en nätverkslänk väljer du **Alla undernätverk**. På samma sätt väljer du de andra undernätverken du vill använda. Om du vill utesluta övervakning för särskilda undernätslänkar från de val du har gjort väljer du **Lägg till undantag**. 
4. Välj mellan ICMP- och TCP-protokoll för att utföra syntetiska transaktioner. 
5. Om du inte vill skapa hälsohändelser för de objekt du har valt avmarkerar du **Aktivera hälsoövervakning på länkarna som omfattas av den här regeln**. 
6. Välj övervakningsvillkor. Om du vill ange anpassade tröskelvärden för generering av hälsohändelseer anger du tröskelvärden. När värdet för villkoret överskrider det valda tröskelvärdet för det valda nätverket eller nätparet genereras en hälsohändelse. 
7. Välj **Spara** om du vill spara konfigurationen. 

När du har sparat en övervakningsregel kan du integrera den regeln med aviseringshantering genom att välja **Skapa avisering**. En varningsregel skapas automatiskt med sökfrågan. Andra obligatoriska parametrar fylls i automatiskt. Med hjälp av en varningsregel kan du ta emot e-postbaserade aviseringar, utöver de befintliga aviseringarna i Network Performance Monitor. Aviseringar kan också utlösa avhjälpande åtgärder med runbooks, eller så kan de integreras med befintliga tjänsthanteringslösningar med hjälp av webhooks. Välj **Hantera avisering** om du vill redigera aviseringsinställningarna. 

Du kan nu skapa fler prestandaövervakaresregler eller gå till lösningsinstrumentpanelen för att använda funktionen.

### <a name="choose-the-protocol"></a>Välj protokollet

Network Performance Monitor använder syntetiska transaktioner för att beräkna mätvärden för nätverksprestanda som paketförlust och länkfördröjning. Om du vill förstå det här konceptet bättre bör du tänka på en Network Performance Monitor-agent som är ansluten till ena änden av en nätverkslänk. Den här Network Performance Monitor-agenten skickar avsökningspaket till en andra Network Performance Monitor-agent som är ansluten till en annan ände av nätverket. Den andra agenten svarar med svarspaket. Denna process upprepas några gånger. Genom att mäta antalet svar och den tid det tar att ta emot varje svar bedömer den första Network Performance Monitor-agenten länkfördröjning och paketdroppar. 

Formatet, storleken och sekvensen för dessa paket bestäms av det protokoll som du väljer när du skapar övervakningsregler. Baserat på protokollet för paketen kan mellanliggande nätverksenheter, till exempel routrar och växlar, bearbeta dessa paket på ett annat sätt. Därför påverkar ditt protokollval resultatens riktighet. Ditt protokollval avgör också om du måste vidta några manuella åtgärder när du har distribuerat lösningen för nätverksprestandaövervakaren. 

Network Performance Monitor ger dig möjlighet att välja mellan ICMP- och TCP-protokoll för att utföra syntetiska transaktioner. Om du väljer ICMP när du skapar en syntetisk transaktionsregel använder Network Performance Monitor-agenterna ICMP ECHO-meddelanden för att beräkna nätverksfördröjningen och paketförlusten. ICMP ECHO använder samma meddelande som skickas av det konventionella ping-verktyget. När du använder TCP som protokoll skickar Network Performance Monitor-agenter TCP SYN-paket över nätverket. Det här steget följs av en TCP-handskakning och anslutningen tas bort med hjälp av RST-paket. 

Tänk på följande information innan du väljer ett protokoll: 

* **Identifiering av flera nätverksvägar.** TCP är mer exakt när du upptäcker flera vägar, och det behöver färre agenter i varje undernät. Ett eller två agenter som använder TCP kan till exempel identifiera alla redundanta sökvägar mellan undernäten. Du behöver flera agenter som använder ICMP för att uppnå liknande resultat. Om du använder ICMP behöver du mer än 5N-agenter i antingen ett käll- eller målundernät om du har ett antal vägar mellan två undernät.

* **Resultatens noggrannhet.** Routrar och växlar tenderar att tilldela lägre prioritet till ICMP ECHO-paket jämfört med TCP-paket. I vissa situationer, när nätverksenheter är kraftigt inlästa, återspeglar de data som erhållits av TCP närmare den förlust och latens som upplevs av program. Detta beror på att de flesta programtrafiken flödar över TCP. I sådana fall ger ICMP mindre exakta resultat jämfört med TCP. 

* **Brandväggskonfiguration.** TCP-protokollet kräver att TCP-paket skickas till en målport. Standardporten som används av Network Performance Monitor-agenter är 8084. Du kan ändra porten när du konfigurerar agenter. Kontrollera att nätverksbrandväggerna eller NSG-reglerna (Network Security Group) (i Azure) tillåter trafik i porten. Du måste också se till att den lokala brandväggen på datorerna där agenter är installerade är konfigurerad för att tillåta trafik på den här porten. Du kan använda PowerShell-skript för att konfigurera brandväggsregler på dina datorer som kör Windows, men du måste konfigurera nätverksbrandväggen manuellt. ICMP fungerar däremot inte med hjälp av en port. I de flesta företagsscenarier tillåts ICMP-trafik via brandväggarna så att du kan använda verktyg för nätverksdiagnostik som ping-verktyget. Om du kan pinga en dator från en annan kan du använda ICMP-protokollet utan att behöva konfigurera brandväggar manuellt.

>[!NOTE] 
> Vissa brandväggar kan blockera ICMP, vilket kan leda till återsändning som resulterar i ett stort antal händelser i säkerhetsinformationen och händelsehanteringssystemet. Kontrollera att protokollet du väljer inte blockeras av en nätverksbrandvägg eller NSG. Annars kan Network Performance Monitor inte övervaka nätverkssegmentet. Vi rekommenderar att du använder TCP för övervakning. Använd ICMP i scenarier där du inte kan använda TCP, till exempel när: 
>
> - Du använder Windows klientbaserade noder eftersom TCP-råa socketar inte är tillåtna i Windows-klienter.
> - Nätverksbrandväggen eller NSG blockerar TCP.
> - Du vet inte hur man byter protokoll.

Om du väljer att använda ICMP under distributionen kan du växla till TCP när som helst genom att redigera standardövervakningsregeln.

1. Gå till Konfigurera **Monitor** > **övervakare för** > **Monitor** **nätverksprestanda** >. Välj sedan **Standardregel**. 
2. Bläddra till avsnittet **Protokoll** och välj det protokoll som du vill använda. 
3. Välj **Spara** om du vill använda inställningen. 

Även om standardregeln använder ett visst protokoll kan du skapa nya regler med ett annat protokoll. Du kan även skapa en blandning av regler där vissa regler använder ICMP och andra använder TCP. 

## <a name="walkthrough"></a>Genomgång 

Nu titta på en enkel undersökning av orsaken till en hälsohändelse.

På lösningsinstrumentpanelen visar en hälsohändelse att en nätverkslänk är felfritt. Om du vill undersöka problemet väljer du panelen **Nätverkslänkar som övervakas.**

Sidan för nedrullning visar att nätverkslänken **DMZ2-DMZ1** är felaktig. Välj **Visa undernätslänkar** för den här nätverkslänken. 


På sidan för detaljgranskning visas alla undernätslänkar i **dmz2-DMZ1-nätverkslänken.** För båda nätlänkarna korsade svarstiden tröskelvärdet, vilket gör nätverkslänken felaktig. Du kan också se svarstidstrenderna för båda undernätverkslänkarna. Använd tidsvalskontrollen i diagrammet för att fokusera på det tidsintervall som krävs. Du kan se den tid på dagen då svarstiden nådde sin topp. Sök i loggarna senare för den här tidsperioden för att undersöka problemet. Välj **Visa nodlänkar** om du vill öka detaljnivån ytterligare. 
 
 ![Sidan Länkar till undernät](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

I likhet med föregående sida visar den nedrullningsmaskinssidan för den aktuella undernätverkslänken sina ingående nodlänkar. Du kan utföra liknande åtgärder här som du gjorde i föregående steg. Välj **Visa topologi** om du vill visa topologin mellan de två noderna. 
 
 ![Sidan Länkar för nod](media/network-performance-monitor-performance-monitor/node-links.png) 

Alla sökvägar mellan de två markerade noderna ritas i topologikartan. Du kan visualisera hopp-för-hopp-topologin för rutter mellan två noder på topologikartan. Det ger dig en tydlig bild av hur många vägar som finns mellan de två noderna och vilka sökvägar datapaketen tar. Flaskhalsar i nätverkets prestanda visas i rött. Om du vill hitta en felaktig nätverksanslutning eller en felaktig nätverksenhet tittar du på de röda elementen på topologikartan. 

 ![Topologi instrumentpanel med topologi karta](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Du kan granska förlust, svarstid och antalet hopp i **Action** varje sökväg i åtgärdsfönstret. Använd rullningslisten för att visa information om de felaktiga sökvägarna. Använd filtren för att markera banorna med det felaktiga hoppet så att topologin för endast de valda banorna ritas. Om du vill zooma in eller ut från topologikartan använder du mushjulet. 

I följande bild visas orsaken till problemområdena till den specifika delen av nätverket i de röda banorna och hoppen. Välj en nod i topologikartan för att visa egenskaperna för noden, som innehåller FQDN och IP-adressen. Om du väljer ett hopp visas IP-adressen för hoppet. 
 
![Topologi karta med nod egenskaper markerade](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Nästa steg
[Sök loggar](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerade dataposter för nätverksprestanda.
