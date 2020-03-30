---
title: 'Felsöka prestanda för nätverkslänkar: Azure'
description: Den här sidan innehåller en standardiserad metod för att testa Azure-nätverkslänkprestanda.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: bb68919fba731caa32dcca3f4c991b8881afc6f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869654"
---
# <a name="troubleshooting-network-performance"></a>Felsöka nätverksprestanda
## <a name="overview"></a>Översikt
Azure erbjuder ett stabilt och snabbt sätt att ansluta från ditt lokala nätverk till Azure. Små och stora kunder använder metoder som plats-till-plats-VPN och ExpressRoute för att driva sin verksamhet i Azure. Men vad händer när prestanda inte uppfyller dina förväntningar eller tidigare erfarenheter? Det här dokumentet kan hjälpa till att standardisera hur du testar och baslinje din specifika miljö.

Det här dokumentet visar hur du enkelt och konsekvent kan testa nätverksfördröjning och bandbredd mellan två värdar. Det här dokumentet innehåller också några råd om hur du kan titta på Azure-nätverket och hjälpa till att isolera problempunkter. PowerShell-skriptet och de verktyg som diskuteras kräver två värdar i nätverket (i vardera änden av länken som testas). En värd måste vara en Windows Server eller Desktop, den andra kan vara antingen Windows eller Linux. 

>[!NOTE]
>Metoden för felsökning, verktyg och metoder som används är personliga preferenser. Detta dokument beskriver den metod och de verktyg jag ofta tar. Din strategi kommer förmodligen att skilja sig, det är inget fel med olika metoder för problemlösning. Men om du inte har en etablerad metod kan det här dokumentet komma igång på vägen till att skapa egna metoder, verktyg och inställningar för felsökning av nätverksproblem.
>
>

## <a name="network-components"></a>Nätverkskomponenter
Innan du gräver i felsökning, låt oss diskutera några vanliga termer och komponenter. Den här diskussionen säkerställer att vi tänker på varje komponent i slutkedjan som möjliggör anslutning i Azure.
![1][1]

På högsta nivå beskriver jag tre stora nätverksroutningsdomäner;

- Azure-nätverket (blått moln till höger)
- Internet eller WAN (grönt moln i mitten)
- Corporate Network (persika moln till vänster)

Om du tittar på diagrammet från höger till vänster, låt oss diskutera kortfattat varje komponent:
 - **Virtuell dator** - Servern kan ha flera nätverkskort, se till att alla statiska vägar, standardvägar och inställningar för operativsystem skickar och tar emot trafik som du tror att den är. Dessutom har varje VM SKU en bandbreddsbegränsning. Om du använder en mindre VM SKU begränsas trafiken av den bandbredd som är tillgänglig för nätverkskortet. Jag brukar använda en DS5v2 för att testa (och sedan ta bort en gång gjort med testning för att spara pengar) för att säkerställa tillräcklig bandbredd på den virtuella datorn.
 - **NIC** - Se till att du känner till den privata IP som har tilldelats nätverkskortet i fråga.
 - **NIC NSG** - Det kan finnas specifika NSGs tillämpas på NIC-nivå, se till att NSG regeluppsättningen är lämplig för den trafik du försöker passera. Kontrollera till exempel att portarna 5201 för iPerf, 3389 för RDP eller 22 för SSH är öppna så att testtrafiken kan passera.
 - **VNet-undernät** - Nätverkskortet tilldelas ett visst undernät, se till att du vet vilken och vilka regler som är associerade med det undernätet.
 - **Subnet NSG** - Precis som nätverkskortet kan NSG:er också användas i undernätet. Kontrollera att NSG-regeluppsättningen är lämplig för den trafik du försöker passera. (för trafik som kommer in till nätverkskortet applicerar subnet NSG först, därefter NICEN NSG, omvänt för trafikerar utgående från DEN VM NIC NSGEN applicerar först därefter subnet NSG kommer in i lek).
 - **Udr** för undernät – Användardefinierade rutter kan dirigera trafik till ett mellanliggande hopp (som en brandvägg eller belastningsutjämnare). Se till att du vet om det finns en UDR på plats för din trafik och i så fall var det går och vad det nästa hopp kommer att göra med din trafik. (till exempel kan en brandvägg passera en del trafik och neka annan trafik mellan samma två värdar).
 - **Gateway undernät / NSG / UDR** - Precis som vm undernätet, gateway undernätet kan ha NSGs och UDRs. Se till att du vet om de är där och vilka effekter de har på din trafik.
 - **VNet Gateway (ExpressRoute)** - När peering (ExpressRoute) eller VPN är aktiverat finns det inte många inställningar som kan påverka hur eller om trafikvägar. Om du har flera ExpressRoute-kretsar eller VPN-tunnlar anslutna till samma VNet Gateway bör du vara medveten om anslutningsviktinställningarna eftersom den här inställningen påverkar anslutningsinställningarna och påverkar den väg som trafiken tar.
 - **Flödesfilter** (visas inte) – Ett flödesfilter gäller endast För Microsoft Peering på ExpressRoute, men det är viktigt att kontrollera om du inte ser de vägar du förväntar dig på Microsoft Peering. 

Nu är du på WAN-delen av länken. Den här routningsdomänen kan vara din tjänsteleverantör, ditt FÖRETAGS WAN eller Internet. Många humle, tekniker och företag som arbetar med dessa länkar kan göra det något svårt att felsöka. Ofta arbetar du för att utesluta både Azure och dina företagsnätverk först innan du hoppar in i den här samlingen av företag och hopp.

Längst till vänster finns ditt företagsnätverk. Beroende på företagets storlek kan den här routningsdomänen vara några nätverksenheter mellan dig och WAN eller flera lager av enheter i ett campus/företagsnätverk.

Med tanke på komplexiteten i dessa tre olika nätverksmiljöer på hög nivå är det ofta optimalt att börja vid kanterna och försöka visa var prestanda är bra och var den försämras. Den här metoden kan hjälpa till att identifiera problemroutningsdomänen för de tre och sedan fokusera felsökningen på den specifika miljön.

## <a name="tools"></a>Verktyg
De flesta nätverksproblem kan analyseras och isoleras med hjälp av grundläggande verktyg som ping och traceroute. Det är ovanligt att du måste gå så djupt som ett paket analys som Wireshark. För att hjälpa till med felsökning har Azure Connectivity Toolkit (AzureCT) utvecklats för att placera några av dessa verktyg i ett enkelt paket. För prestandatestning, jag gillar att använda iPerf och PSPing. iPerf är ett vanligt verktyg och körs på de flesta operativsystem. iPerf är bra för grundläggande prestanda tester och är ganska lätt att använda. PSPing är ett pingverktyg utvecklat av SysInternals. PSPing är ett enkelt sätt att utföra ICMP och TCP ping i en också lätt att använda kommandot. Båda dessa verktyg är lätta och är "installerade" helt enkelt genom att hantera filerna till en katalog på värden.

Jag har slagit in alla dessa verktyg och metoder i en PowerShell-modul (AzureCT) som du kan installera och använda.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – verktygslådan för Azure Connectivity-anslutning
AzureCT PowerShell-modulen har två komponenter [Testning][Availability Doc] och [prestandatestning][Performance Doc]. Det här dokumentet handlar bara om prestandatestning, så kan fokusera på de två Link Performance-kommandona i den här PowerShell-modulen.

Det finns tre grundläggande steg för att använda den här verktygslådan för prestandatestning. 1) Installera PowerShell-modulen, 2) Installera stödprogrammen iPerf och PSPing 3) Kör prestandatestet.

1. Installera PowerShell-modulen

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Det här kommandot hämtar PowerShell-modulen och installerar den lokalt.

2. Installera stödprogrammen
    ```powershell
    Install-LinkPerformance
    ```
    Detta AzureCT-kommando installerar iPerf och PSPing i en ny katalog "C:\ACTTools", det öppnar också Windows-brandväggen portar så att ICMP och port 5201 (iPerf) trafik.

3. Kör prestandatestet

    Först på fjärrvärden måste du installera och köra iPerf i serverläge. Se också till att fjärrvärden lyssnar på antingen 3389 (RDP för Windows) eller 22 (SSH för Linux) och tillåter trafik på port 5201 för iPerf. Om fjärrvärden är windows installerar du AzureCT och kör kommandot Install-LinkPerformance för att konfigurera iPerf och brandväggsreglerna som krävs för att starta iPerf i serverläge. 
    
    När fjärrdatorn är klar öppnar du PowerShell på den lokala datorn och startar testet:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Det här kommandot kör en serie samtidiga belastnings- och svarstidstester för att uppskatta bandbreddskapaciteten och svarstiden för nätverkslänken.

4. Granska resultaten av testerna

    Utdataformatet PowerShell liknar:

    ![4][4]

    De detaljerade resultaten av alla iPerf- och PSPing-tester finns i enskilda textfiler i AzureCT-verktygskatalogen på "C:\ACTTools".

## <a name="troubleshooting"></a>Felsökning
Om prestandatestet inte ger dig förväntade resultat bör du räkna ut varför det ska vara en progressiv steg-för-steg-process. Med tanke på antalet komponenter i vägen, ett systematiskt tillvägagångssätt ger i allmänhet en snabbare väg till upplösning än att hoppa runt och potentiellt i onödan göra samma tester flera gånger.

>[!NOTE]
>Scenariot här är ett prestandaproblem, inte ett anslutningsproblem. Stegen skulle vara annorlunda om trafiken inte passerade alls.
>
>

Först utmana dina antaganden. Är dina förväntningar rimliga? Om du till exempel har en 1-Gbps ExpressRoute-krets och 100 ms latens är det orimligt att förvänta sig hela 1 Gbit/s trafik med tanke på prestandaegenskaperna hos TCP över länkar med hög latens. Mer information om prestandaantaganden finns i [avsnittet Referenser.](#references)

Därefter rekommenderar jag att du börjar vid kanterna mellan routningsdomäner och försöker isolera problemet till en enda större routningsdomän. företagsnätverket, WAN eller Azure-nätverket. Människor skyller ofta den "svarta lådan" i vägen, medan skylla den svarta lådan är lätt att göra, kan det avsevärt fördröja upplösningen, särskilt om problemet är faktiskt i ett område som du har förmågan att göra ändringar. Se till att du gör din due diligence innan du lämnar över till din tjänsteleverantör eller Internetleverantör.

När du har identifierat den större routningsdomänen som verkar innehålla problemet bör du skapa ett diagram över området i fråga. Antingen på en whiteboard, anteckningsblock eller Visio som ett diagram ger en konkret "stridskarta" för att möjliggöra en metodisk metod för att ytterligare isolera problemet. Du kan planera testpunkter och uppdatera kartan när du rensar områden eller gräver djupare under testningens gång.

Nu när du har ett diagram börjar du dela upp nätverket i segment och begränsa problemet. Ta reda på var det fungerar och var det inte gör det. Fortsätt att flytta dina testpunkter för att isolera ner till den felande komponenten.

Glöm inte heller att titta på andra lager av OSI-modellen. Det är lätt att fokusera på nätverket och lager 1 - 3 (fysiska, data och nätverkslager) men problemen kan också vara uppe på lager 7 i programlagret. Tänk på det och verifiera antaganden.

## <a name="advanced-expressroute-troubleshooting"></a>Avancerad Felsökning av ExpressRoute
Om du inte är säker på var kanten av molnet faktiskt är, kan det vara en utmaning att isolera Azure-komponenterna. När ExpressRoute används är kanten en nätverkskomponent som kallas Microsoft Enterprise Edge (MSEE). **När du använder ExpressRoute**är MSEE den första kontaktpunkten i Microsofts nätverk och det sista hoppet som lämnar Microsoft-nätverket. När du skapar ett anslutningsobjekt mellan VNet-gatewayen och ExpressRoute-kretsen gör du faktiskt en anslutning till MSEE. Att känna igen MSEE som det första eller sista hoppet (beroende på vilken riktning du ska) är avgörande för att isolera Azure Network-problem för att antingen bevisa att problemet finns i Azure eller längre nedströms i WAN eller Företagsnätverket. 

![2][2]

>[!NOTE]
> Observera att MSEE inte finns i Azure-molnet. ExpressRoute är faktiskt i utkanten av Microsoft-nätverket faktiskt inte i Azure. När du är ansluten till ExpressRoute till en MSEE är du ansluten till Microsofts nätverk, därifrån kan du sedan gå till någon av molntjänsterna, till exempel Office 365 (med Microsoft Peering) eller Azure (med Privat och/eller Microsoft Peering).
>
>

Om två virtuella nätverk (VNets A och B i diagrammet) är anslutna till **samma** ExpressRoute-krets kan du utföra en serie tester för att isolera problemet i Azure (eller bevisa att det inte finns i Azure)
 
### <a name="test-plan"></a>Testplan
1. Kör Get-LinkPerformance-testet mellan VM1 och VM2. Det här testet ger insikt om problemet är lokalt eller inte. Om det här testet ger godtagbara svarstid och bandbreddsresultat kan du markera det lokala virtuella nätverket som bra.
2. Om du antar att den lokala VNet-trafiken är bra kör du Get-LinkPerformance-testet mellan VM1 och VM3. Det här testet övningar anslutningen via Microsoft-nätverket ner till MSEE och tillbaka till Azure. Om det här testet ger godtagbara svarstid och bandbreddsresultat kan du markera Azure-nätverket som bra.
3. Om Azure är uteslutet kan du utföra en liknande sekvens av tester i ditt företagsnätverk. Om det också testar bra är det dags att arbeta med din tjänsteleverantör eller Internetleverantör för att diagnostisera DIN WAN-anslutning. Exempel: Kör det här testet mellan två filialkontor eller mellan skrivbordet och en datacenterserver. Beroende på vad du testar, hitta slutpunkter (servrar, datorer, etc.) som kan utöva den sökvägen.

>[!IMPORTANT]
> Det är viktigt att du för varje test markerar den tid på dagen du kör testet och registrerar resultaten på en gemensam plats (jag gillar OneNote eller Excel). Varje testkörning bör ha identiska utdata så att du kan jämföra de resulterande data över testkörningar och inte ha "hål" i data. Konsekvens i flera tester är den främsta anledningen till att jag använder AzureCT för felsökning. Magin är inte i den exakta belastningen scenarier jag kör, men istället *magin* är det faktum att jag får ett *konsekvent test och datautgång* från varje test. Att registrera tiden och ha konsekventa data varje gång är särskilt användbart om du senare upptäcker att problemet är sporadiskt. Var flitig med din datainsamling på framsidan och du kommer att undvika timmar av omtestning av samma scenarier (jag lärde mig denna hårda väg för många år sedan).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problemet är isolerat, vad händer nu?
Ju mer du kan isolera problemet desto lättare är det att åtgärda, men ofta når du den punkt där du inte kan gå djupare eller längre med felsökningen. Exempel: du ser länken mellan din tjänsteleverantör som tar hopp via Europa, men din förväntade väg är allt i Asien. Denna punkt är när du bör nå ut för hjälp. Vem du frågar är beroende av routningsdomänen som du har isolerat problemet till, eller ännu bättre om du kan begränsa det till en viss komponent.

För företagsnätverksproblem kan din interna IT-avdelning eller tjänsteleverantör som stöder nätverket (som kan vara maskinvarutillverkaren) hjälpa till med enhetskonfiguration eller maskinvarureparation.

För WAN kan det hjälpa dig att komma igång med att dela dina testresultat med din Tjänsteleverantör eller Internetleverantör och undvika att täcka en del av samma mark som du redan har testat. Men bli inte förolämpad om de vill verifiera dina resultat själva. "Lita på men verifiera" är ett bra motto vid felsökning baserat på andras rapporterade resultat.

Med Azure är det dags att granska [Azure Network-dokumentationen][Network Docs] när du har isolerat problemet så detaljerat som [möjligt.][Ticket Link]

## <a name="references"></a>Referenser
### <a name="latencybandwidth-expectations"></a>Förväntningar om latens/bandbredd
>[!TIP]
> Geografisk svarstid (miles eller kilometer) mellan slutpunkterna du testar är den överlägset största komponenten i svarstiden. Även om det finns utrustning svarstid (fysiska och virtuella komponenter, antal humle, etc.) inblandade, geografi har visat sig vara den största komponenten i övergripande latens när det handlar om WAN-anslutningar. Det är också viktigt att notera att avståndet är avståndet till fibern kör inte den raka linjen eller vägkarta avstånd. Detta avstånd är otroligt svårt att få med någon noggrannhet. Som ett resultat använder jag i allmänhet en stad avstånd kalkylator på Internet och vet att denna metod är en grovt felaktig åtgärd, men är tillräckligt för att ställa en allmän förväntan.
>
>

Jag har en ExpressRoute setup i Seattle, Washington i USA. Följande tabell visar svarstiden och bandbredden som jag såg testa till olika Azure-platser. Jag har uppskattat det geografiska avståndet mellan varje ände av testet.

Testinställning:
 - En fysisk server som kör Windows Server 2016 med ett 10 Gbit/s-nätverkskort som är anslutet till en ExpressRoute-krets.
 - En 10 Gbit/s Premium ExpressRoute-krets på den plats som identifieras med Privat peering aktiverat.
 - Ett Azure VNet med en UltraPerformance-gateway i den angivna regionen.
 - En virtuell DS5v2-dator som kör Windows Server 2016 på det virtuella nätverket. Den virtuella datorn var icke-domän ansluten, byggd från standard Azure-avbildningen (ingen optimering eller anpassning) med AzureCT installerat.
 - Alla tester använde azurect get-linkperformance kommandot med en 5-minuters belastningstest för var och en av de sex testkörningarna. Ett exempel:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Dataflödet för varje test hade belastningen flödade från den lokala fysiska servern (iPerf-klient i Seattle) upp till Azure VM (iPerf-servern i den listade Azure-regionen).
 - Kolumndata för "Latens" kommer från testet Ingen belastning (ett TCP-svarstidstest utan iPerf som körs).
 - Kolumndata för "Max bandbredd" kommer från 16 TCP-flödesbelastningstestet med en fönsterstorlek på 1 Mb.

![3][3]

### <a name="latencybandwidth-results"></a>Resultat av svarstid/bandbredd
>[!IMPORTANT]
> Dessa siffror är endast för allmän referens. Många faktorer påverkar svarstiden, och även om dessa värden i allmänhet är konsekventa över tiden kan villkoren i Azure eller Service Providers-nätverket skicka trafik via olika sökvägar när som helst, vilket innebär att svarstid och bandbredd kan påverkas. I allmänhet leder effekterna av dessa förändringar inte till betydande skillnader.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Location|Azure<br/>Region|Beräknade<br/>Avstånd (km)|Svarstid|1 Session<br/>Bandbredd|Maximal<br/>Bandbredd|
| Seattle | USA, västra 2        |    191 km |   5 ms | 262,0 Mbit/sek |  3,74 Gbits/sek |
| Seattle | USA, västra          |  1 094 km |  18 ms |  82,3 Mbits/sek |  3,70 Gbits/sek |
| Seattle | USA, centrala       |  2 357 km |  40 ms |  38,8 Mbits/sek |  2,55 Gbits/sek |
| Seattle | USA, södra centrala |  2 877 km |  51 ms |  30,6 Mbits/sek |  2,49 Gbits/sek |
| Seattle | USA, norra centrala |  2 792 km |  55 ms |  27,7 Mbits/sek |  2.19 Gbits/sek |
| Seattle | USA, östra 2        |  3 769 km |  73 ms |  21,3 Mbits/sek |  1,79 Gbits/sek |
| Seattle | USA, östra          |  3 699 km |  74 ms |  21,1 Mbits/sek |  1,78 Gbits/sek |
| Seattle | Japan, östra       |  7 705 km | 106 ms |  14,6 Mbits/sek |  1,22 Gbits/sek |
| Seattle | Storbritannien, södra         |  7 708 km | 146 ms |  10,6 Mbit/sek |   896 Mbits/sek |
| Seattle | Europa, västra      |  7 834 km | 153 ms |  10,2 Mbit/sek |   761 Mbits/sek |
| Seattle | Australien, östra   | 12 484 km | 165 ms |   9,4 Mbits/sek |   794 Mbits/sek |
| Seattle | Sydostasien   | 12 989 km | 170 ms |   9,2 Mbits/sek |   756 Mbits/sek |
| Seattle | Södra Brasilien *   | 10 930 km | 189 ms |   8,2 Mbits/sek |   699 Mbits/sek |
| Seattle | Indien, södra      | 12 918 km | 202 ms |   7,7 Mbits/sek |   634 Mbits/sek |

\*Latensen till Brasilien är ett bra exempel där den raka linjen avstånd skiljer sig avsevärt från fiber kör avstånd. Jag förväntar mig att latensen skulle vara i närheten av 160 ms, men är faktiskt 189 ms. Denna skillnad mot mina förväntningar kan tyda på ett nätverk fråga någonstans, men troligen att fibern köra inte går till Brasilien i en rak linje och har en extra 1.000 km eller så av resor för att komma till Brasilien från Seattle.

## <a name="next-steps"></a>Nästa steg
1. Ladda ned Azure Connectivity Toolkit från GitHub på[https://aka.ms/AzCT][ACT]
2. Följ instruktionerna för [länkprestandatestning][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure-nätverkskomponenter"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Felsökning av ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Perf testmiljö"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-utgång"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
