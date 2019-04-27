---
title: 'Felsök virtuella nätverkets prestanda: Azure | Microsoft Docs'
description: Den här sidan ger en standardiserad metod för att testa Azure nätverksprestanda för länken.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: 9ec310ffaa9d2bb297abde9341bf7b6c2dc763b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883341"
---
# <a name="troubleshooting-network-performance"></a>Felsökning av nätverksprestanda
## <a name="overview"></a>Översikt
Azure erbjuder stabil och snabba sätt att ansluta från ditt lokala nätverk till Azure. Metoder som plats-till-plats-VPN och ExpressRoute har som används av kunder stora och små för att driva sina företag i Azure. Men vad händer när prestanda inte uppfyller dina förväntningar eller tidigare upplevelse? Det här dokumentet hjälper dig att standardisera utvärdering av säkerhetsbaslinjen och hur du testar din miljö.

Det här dokumentet visar hur du kan enkelt och konsekvent testa nätverkssvarstiden och bandbredden mellan två värdar. Det här dokumentet innehåller också råd om sätt att titta på Azure-nätverket och hjälpa dig för att isolera problemet punkter. PowerShell-skript och verktyg som beskrivs kräver två värdar i nätverket (början eller slutet av länken testas). En värd måste vara en Windows Server eller skrivbord, den andra kan vara antingen Windows eller Linux. 

>[!NOTE]
>Metod för att felsöka, verktyg och metoder som används är personliga inställningar. Det här dokumentet beskriver tillvägagångssätt och verktyg som jag ofta ta. Din skiljer sig förmodligen, det inte finns något fel med olika sätt att lösa problemet. Men om du inte har en etablerad metod, får det här dokumentet du bra bit på väg att skapa dina egna metoder, verktyg och inställningar för felsökning av nätverksproblem.
>
>

## <a name="network-components"></a>Nätverkskomponenter
Innan du itu med felsökning, vi ska diskutera några vanliga termer och komponenter. Den här diskussionen garanterar vi funderar på att varje komponent i kedjan för slutpunkt till slutpunkt som möjliggör anslutningar i Azure.
[![1]][1]

På den högsta nivån beskrivs jag tre större nätverk routningsdomäner;

- Azure-nätverk (blå moln till höger)
- Internet- eller WAN (grön moln i mitten)
- Företagets nätverk (persikofärgad moln till vänster)

Vi ska titta på diagrammet från höger till vänster, diskutera kort varje komponent:
 - **Virtuell dator** - servern kan ha flera nätverkskort, se till att alla statiska vägar, standardvägar, och operativsysteminställningar skickar och tar emot trafik på sätt som du tror att det är. Varje VM-SKU har dessutom en begränsning av bandbredd. Om du använder en mindre VM-SKU begränsas trafiken av bandbredden som är tillgängliga för nätverkskortet. Jag använder vanligtvis en DS5v2 för testning (och sedan ta bort när du har testning för att spara pengar) att säkerställa tillräcklig bandbredd på den virtuella datorn.
 - **NIC** – se till att du vet att den privata IP-adress som har tilldelats till nätverkskortet i fråga.
 - **NIC NSG** – det kan vara specifika NSG: er som tillämpas på nätverkskortnivån, se till att NSG-regeluppsättningen är lämpligt för trafik som du försöker skicka. Kontrollera portar 5201 för iPerf, exempelvis 3389 för RDP eller 22 för SSH är öppna för att tillåta testtrafik.
 - **VNet Subnet** – The nätverkskort tilldelas till ett specifikt undernät, se till att du vet vilka en och reglerna som är associerade med det undernätet.
 - **En Nätverkssäkerhetsgrupp för undernät** – precis som NIC, NSG: er kan tillämpas på undernät samt. Kontrollera att NSG-regeluppsättningen är lämpligt för trafik som du försöker skicka. (för trafik inkommande till NIC undernätet NSG: N gäller först och sedan NIC NSG, omvänt för trafik som utgår från den virtuella datorn NIC NSG gäller först och sedan NSG för undernätet kommer in play).
 - **Undernät UDR** -användardefinierade vägar kan dirigera trafik till en mellanliggande hopp (till exempel en brandvägg eller belastningsutjämnare). Se till att du vet om det finns en UDR på plats för trafiken och om så var den går och vad som nästa hopp gör att trafiken. (till exempel en brandvägg kan skicka en del trafik och neka annan trafik mellan samma två värdar).
 - **Gateway-undernät / NSG / UDR** -precis som det Virtuella datorundernätet gateway-undernätet ha NSG: er och udr: er. Kontrollera att du vet om de är det och vilken inverkan de har på trafiken.
 - **VNet-Gateway (ExpressRoute)** -när peering (ExpressRoute) eller VPN är aktiverat kan det inte finns många inställningar som kan påverka hur eller om trafik vägar. Om du har flera ExpressRoute-kretsar eller VPN-tunnlar som är anslutna till samma VNet-Gateway, du bör känna till vikt anslutningsinställningar som den här inställningen påverkar-inställningar för anslutning och påverkar trafiken tar sökvägen.
 - **Dirigera Filter** (visas inte -) ett flödesfilter endast gäller för Microsoft-Peering i ExpressRoute, men det är viktigt att kontrollera om du inte ser de vägar som du förväntar dig på Microsoft-Peering. 

Nu är du på WAN-delen av länken. Den här routningsdomän kan vara din tjänstleverantör, ditt företags WAN-nätverk eller Internet. Många hopp, teknik och företag som ingår i dessa länkar kan göra det lite svårt att felsöka. Ofta kan arbeta du kunna utesluta både Azure och ditt företagsnätverk först innan vi går vidare till den här samlingen av företag och hopp.

I det föregående diagrammet är längst till vänster företagets nätverk. Beroende på storleken på ditt företag kan kan den här routningsdomän vara några nätverksenheter mellan dig och WAN eller flera lager av enheter i ett campus/enterprise-nätverk.

Komplexiteten i de här tre olika övergripande nätverksmiljöer får den är ofta optimala för att starta vid kanterna och försök att visa där prestanda är bra och där det försämrar. Den här metoden kan identifiera problem routningsdomän för tre och därefter visa felsökningen på den specifika miljön.

## <a name="tools"></a>Verktyg
De flesta nätverksproblem kan analyseras och isoleras med grundläggande verktyg som ping och traceroute. Det är ovanligt att du behöver gå på djupet en analys av gränssnittspaket som Wireshark. För att underlätta felsökningen utvecklades Azure Connectivity Toolkit (AzureCT) om du vill placera vissa av verktygen i ett enkelt paket. Jag vilja använda iPerf och PSPing för prestandatestning. iPerf är ett verktyg som används ofta och körs på de flesta operativsystem. iPerf är bra för grundläggande prestanda tester och är ganska enkelt att använda. PSPing är ett ping-verktyg som har utvecklats av SysInternals. PSPing är ett enkelt sätt att utföra ICMP- och TCP-ping i en också lätt att använda kommandot. De här verktygen är lätta och ”installeras” genom att kopiera filer till en katalog på värden.

Jag har omslutits alla dessa verktyg och metoder i en PowerShell-modul (AzureCT) som du kan installera och använda.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – Azure Connectivity Toolkit
AzureCT PowerShell-modulen har två komponenter [tillgänglighet testning] [ Availability Doc] och [prestandatestning][Performance Doc]. Det här dokumentet är endast berörda med prestandatestning, så Låt oss att fokusera på de två länk prestanda-kommandona i det här PowerShell-modulen.

Det finns tre grundläggande steg för att använda dessa verktyg för prestandatestning. (1) installera PowerShell-modulen, 2) installera stödjande program iPerf och PSPing 3) kör prestandatest.

1. Installera PowerShell-modulen

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Det här kommandot laddar ned PowerShell-modulen och installerar den lokalt.

2. Installera stödjande program
    ```powershell
    Install-LinkPerformance
    ```
    Det här kommandot AzureCT installerar iPerf och PSPing i en ny katalog ”C:\ACTTools”, öppnas också Windows brandväggsportar för att tillåta ICMP och port 5201 (iPerf)-trafik.

3. Kör prestandatest

    På fjärrdatorn måste du först installera och köra iPerf i serverläge. Se också till fjärrvärden lyssnar på antingen 3389 (RDP för Windows) eller 22 (SSH för Linux) och att tillåta trafik på port 5201 för iPerf. Om fjärrvärden är windows kan installera AzureCT och kör kommandot Install-LinkPerformance du ställer in iPerf och brandväggsregler som behövs för att starta iPerf i serverläge har. 
    
    När fjärrdatorn är klar, öppna PowerShell på den lokala datorn och starta testet:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Det här kommandot Kör en serie med samtidiga belastning och svarstid tester för att beräkna bandbreddskapacitet och svarstid för nätverkslänk.

4. Granska resultatet av testerna

    PowerShell-utdataformat liknar:

    [![4]][4]

    Ett detaljerat resultat av alla iPerf och PSPing tester finns i enskilda textfiler i AzureCT-verktygsmappen på ”C:\ACTTools”.

## <a name="troubleshooting"></a>Felsökning
Om prestandatest inte ger vara du önskat resultat, räkna ut därför en progressiv stegvisa process. Angivet antal komponenter i sökvägen, har en systematisk metod vanligtvis en snabbare sökväg till lösning än hoppa över och potentiellt onödan gör samma testa flera gånger.

>[!NOTE]
>Det här scenariot är ett prestandaproblem, inte ett anslutningsproblem. Stegen är olika om trafiken inte skicka alls.
>
>

Först utmana dina antaganden. Är dina förväntningar rimligt? Till exempel om du har en ExpressRoute-krets med 1 Gbit/s och 100 ms fördröjning är orimligt du kan förvänta dig fullständig 1 Gbit/s trafiktyper beroende prestandaegenskaper för TCP över länkar med lång svarstid. Se den [refererar till avsnittet](#references) för mer på prestanda antaganden.

Nu ska jag rekommenderar att du börjar vid kanterna mellan routningsdomäner och försök att isolera problemet till en enda större routningsdomän; Företagets nätverk, WAN eller Azure-nätverket. Personer ofta skuld ”black box” i sökvägen, samtidigt som blaming black box är enkelt att göra den avsevärt fördröjer upplösning särskilt om problemet som faktiskt finns i ett område som du har möjlighet att göra ändringar. Kontrollera att du gör dina sistone innan du skickar till din tjänstleverantör eller Internetleverantör.

När du har identifierat den större routningsdomän som visas innehåller problemet, bör du skapa ett diagram över området i fråga. Antingen på en whiteboard eller anteckningar Visio som ett diagram ger en konkret ”beprövad kartan” så att en metodisk metod för att ytterligare isolera problemet. Du kan planera testning punkter och uppdatera kartan när du tar bort områden eller fördjupa dig under testning gång.

Nu när du har ett diagram kan börja dela upp nätverket i segment och begränsa problemet. Ta reda på var den fungerar och där det inte. Att flytta dina belastningstester poäng att isolera ned till den felaktiga komponenten.

Dessutom Glöm inte att titta på andra lager i OSI-modellen. Det är enkelt att fokusera på de nätverks- och lager 1 – 3 (fysisk, Data och nätverk lager) men problem kan också vara in Layer 7 i programlagret. Håll en öppen åtanke och verifiera antaganden.

## <a name="advanced-expressroute-troubleshooting"></a>Avancerad felsökning av ExpressRoute
Om du inte vet var i utkanten av molnet faktiskt är kan att isolera Azure-komponenterna vara en utmaning. När ExpressRoute används är edge en nätverkskomponent som heter Microsoft Enterprise Edge (MSEE). **När du använder ExpressRoute**, den msee: N är den första kontaktpunkten i Microsofts nätverk och det senaste hoppets lämnar Microsoft-nätverk. När du skapar ett anslutningsobjekt mellan din VNet-gateway och ExpressRoute-kretsen, vill du göra en anslutning till den msee: N. Eftersom den msee: N som det första eller sista hoppet (beroende på vilken riktning du ska) är avgörande för att isolera Azure nätverksproblem antingen bevisa problemet finns i Azure eller ytterligare nedströms i WAN eller företagets nätverk. 

[![2]][2]

>[!NOTE]
> Observera att den msee: N finns inte i Azure-molnet. ExpressRoute är faktiskt i utkanten av Microsoft network inte faktiskt i Azure. När du är ansluten med ExpressRoute för en MSEE, du är ansluten till Microsofts nätverk, där kan du sedan gå till någon av molntjänster som Office 365 (med Microsoft-Peering) eller Azure (med privata och/eller Microsoft-Peering).
>
>

Om två virtuella nätverk (VNets A och B i diagrammet) är anslutna till den **samma** ExpressRoute-krets som du kan utföra en serie tester för att isolera problemet i Azure (eller bevisa att den inte är i Azure)
 
### <a name="test-plan"></a>Testplan
1. Kör Get-LinkPerformance testet mellan VM1 och VM2. Det här testet ger information om problemet är lokala eller inte. Om det här testet producerar acceptabel svarstid och bandbredd resultat, kan du markera det lokala nätverket VNet som bra.
2. Om vi antar att det lokala virtuella nätverket är trafik bra, kör Get-LinkPerformance testet mellan VM1 och VM3. Det här testet utför anslutning via Microsoft-nätverket till den msee: N och tillbaka till Azure. Om det här testet producerar acceptabel svarstid och bandbredd resultat, kan du markera Azure-nätverket som bra.
3. Om Azure är uteslutas kan du utföra en liknande uppsättning tester i företagsnätverket. Om som testar också bra, är det dags att arbeta med din tjänstleverantör eller Internetleverantör att diagnostisera WAN-anslutningen. Exempel: Kör det här testet mellan två olika avdelningskontor, eller mellan ditt skrivbord och en data center-servern. Beroende på vad du testar, hitta slutpunkter (servrar, datorer, etc.) som kan arbeta med samma sökväg.

>[!IMPORTANT]
> Det är viktigt att för varje test du markera tid på dagen som du kör testet och registrera resultatet i en gemensam plats (jag gillar OneNote eller Excel). Kör varje test bör ha identiska utdata så att du kan jämföra de resulterande data över testkörningar och inte har ”hål” i data. Konsekvent på flera test är den främsta orsaken jag använda AzureCT för felsökning. Magi finns inte i exakta load-scenarier som jag kör istället den *magic* är det faktum att jag får ett *konsekvent test- och utdata* från varje test. Spela in tiden och gör att konsekventa data varje gång är särskilt användbart om du senare upptäcker att problemet är sporadiska. Vara flitig med din insamling av data direkt och du undviker timmar testning i samma scenarier (jag lärde mig vis hårda många år sedan).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problemet är isolerat, vad händer nu?
Ju mer kan du isolera problemet desto lättare är det att åtgärda, men ofta du når den punkt där du inte kan gå djupare eller ytterligare med felsökningen. Exempel: du ser länken mellan din tjänstleverantör för att utföra hopp via Europa, men den förväntade sökvägen är allt i Asien. Nu är när du bör kontakta för att få hjälp. Som du ber är beroende av den routningsdomän du isolerade problemet till eller ännu bättre om du kan begränsa den till en viss komponent.

För företagsnätverket problem med kanske din interna IT-avdelningen eller -leverantör som stöd för ditt nätverk (vilket kan vara maskinvarutillverkaren) kan hjälpa dig med enhetskonfiguration eller maskinvarureparation.

För WAN, dela din testning resultatet med din tjänstleverantör eller Internetleverantör kan hjälpa att få dem igång och undvika som täcker vissa samma grunden som du redan har testat. Dock inte vara offended om de vill kontrollera dina resultat själva. ”Lita men kontrollera” är en bra motto när felsökning baserat på andra personers rapporterade resultat.

Med Azure när du orsaken till felet i så mycket information som kan, är det dags att granska den [dokumentation om Azure Network] [ Network Docs] och sedan om det behövs fortfarande [öppna ett supportärende][Ticket Link].

## <a name="references"></a>Referenser
### <a name="latencybandwidth-expectations"></a>Förväntningar på svarstid/bandbredd
>[!TIP]
> Geografisk svarstiden (miles eller kilometer) mellan slutpunkter som du testar är allra största komponent i svarstiden. Det är utrustning svarstid (fysiska och virtuella komponenter, antalet hopp osv) som ingår, har geografi visat sig vara den största delen av Total svarstid när du hanterar WAN-anslutningar. Det är också viktigt att notera att avståndet är avståndet från fiber kör inte linjär eller översikten avstånd. Den här avståndet är mycket svårt att få med alla noggrannhet. Därför kan jag Allmänt använda en stad avståndet kalkylator på internet och vet att den här metoden är ett grovt felaktiga mått, men räcker för att ange en allmän förväntan.
>
>

Jag har en ExpressRoute-installation i Seattle, Washington i USA. I följande tabell visar svarstiden och bandbredden jag såg testning till olika Azure-platser. Jag har beräknat geografiska avståndet mellan varje slutet av testet.

Testa installationen:
 - En fysisk server som kör Windows Server 2016 med ett 10 Gbps NIC, ansluten till en ExpressRoute-krets.
 - En Premium ExpressRoute-krets för 10 Gbit/s på den plats som anges med privat Peering aktiverad.
 - Ett virtuellt Azure nätverk med en UltraPerformance-gateway för den angivna regionen.
 - En DS5v2 virtuell dator som kör Windows Server 2016 på det virtuella nätverket. Den virtuella datorn har inte tillhör en domän ansluten, bygger på standard Azure bild (inga optimering eller anpassning) med AzureCT installerat.
 - All testning har kommandot AzureCT Get-LinkPerformance med ett belastningstest för 5 minuter för var och en av de sex testkörningar. Exempel:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Dataflöde för varje test hade belastningen flödar från den lokala fysiska servern (iPerf klient i Seattle) upp till den virtuella Azure-datorer (iPerf server i listan Azure-region).
 - Kolumnen ”svarstidsdata” är från Nej belastningstest (en TCP svarstid test utan iPerf körs).
 - ”Maximalt Bandwidth” kolumndata är från belastningstest för 16 TCP flöde med en 1 Mb fönsterstorlek.

[![3]][3]

### <a name="latencybandwidth-results"></a>Svarstid/bandbredden resultat
>[!IMPORTANT]
> Dessa siffror är för allmänt bruk. Många faktorer påverkar svarstiden och dessa värden är vanligtvis konsekvent över tid, villkor i Azure eller tjänstleverantörer nätverket kan skicka trafik via olika sökvägar när som helst, därför svarstider och bandbredd kan påverkas. I allmänhet bör resultera effekterna av dessa ändringar inte i viktiga skillnader.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Location|Azure<br/>Region|Beräknad<br/>Avståndet (km)|Svarstid|1-session<br/>Bandbredd|Maximal<br/>Bandbredd|
| Seattle | Västra USA 2        |    191 km |   5 ms | 262.0 Mbit/s |  3.74 Gbit/s |
| Seattle | Västra USA          |  1,094 km |  18 ms |  82.3 Mbit/s |  3.70 Gbit/s |
| Seattle | Centrala USA       |  2,357 km |  40 ms |  38.8 Mbit/s |  2.55 Gbit/s |
| Seattle | Södra centrala USA |  2,877 km |  51 ms |  30.6 Mbit/s |  2.49 Gbit/s |
| Seattle | Norra centrala USA |  2,792 km |  55 ms |  27,7 Mbit/s |  2.19 Gbit/s |
| Seattle | USA, östra 2        |  3,769 km |  73 ms |  21.3 Mbit/s |  1.79 Gbit/s |
| Seattle | Östra USA          |  3,699 km |  74 ms |  21.1 Mbit/s |  1,78 Gbit/s |
| Seattle | Östra Japan       |  7,705 km | 106 ms |  14.6 Mbit/s |  1.22 enligt Gbit/s |
| Seattle | Storbritannien, södra         |  7,708 km | 146 ms |  10.6 Mbit/s |   896 Mbit/s |
| Seattle | Västra Europa      |  7,834 km | 153 ms |  10.2 Mbit/s |   761 Mbit/s |
| Seattle | Östra Australien   | 12,484 km | 165 ms |   9.4 Mbit/s |   794 Mbit/s |
| Seattle | Sydostasien   | 12,989 km | 170 ms |   9.2 Mbit/s |   756 Mbit/s |
| Seattle | Södra Brasilien *   | 10,930 km | 189 ms |   8.2 Mbit/s |   699 Mbit/s |
| Seattle | Södra Indien      | 12,918 km | 202 ms |   7,7 Mbit/s |   634 Mbit/s |

\* Svarstiden för Brasilien är ett bra exempel där linjär avståndet skiljer sig markant från fiber kör avstånd. Jag kan förvänta dig att svarstiden i nätverket på 160 ms, men är faktiskt 189 ms. Denna skillnad mot Mina förväntningar kan tyda på ett nätverksproblem någonstans, men troligen att fiber kör inte går att Brasilien rakt och har en extra 1 000 km eller det resor att komma till Brasilien från Seattle.

## <a name="next-steps"></a>Nästa steg
1. Ladda ned Azure Connectivity Toolkit från GitHub vid [https://aka.ms/AzCT][ACT]
2. Följ anvisningarna för [länka prestandatestning][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "azure nätverkskomponenter"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute felsökning"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Perf-testmiljö"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-utdata"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
