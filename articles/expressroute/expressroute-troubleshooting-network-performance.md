---
title: "Felsökning av prestanda för virtuella Azure-nätverket | Microsoft Docs"
description: "Den här sidan innehåller en standardiserad metod för att testa Azure nätverksprestanda för länken."
services: expressroute
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2017
ms.author: jonor
ms.openlocfilehash: 56f011632a2aa3ef0632efd5ace472c0fc79a329
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="troubleshooting-network-performance"></a>Felsökning av nätverkets prestanda
## <a name="overview"></a>Översikt
Azure tillhandahåller stabil och snabbt sätt att ansluta från ditt lokala nätverk till Azure. Metoder som plats-till-plats VPN och ExpressRoute används har av kunder stora och små för att köra sina företag i Azure. Men vad händer när prestanda inte uppfyller dina förväntningar eller tidigare erfarenheter? Det här dokumentet hjälper dig att standardisera hur du testar och baslinjen din specifika miljö.

Det här dokumentet beskrivs hur du kan enkelt och konsekvent testa Nätverksfördröjningen och bandbredden mellan två värdar. Det här dokumentet innehåller också råd om sätt att titta på Azure-nätverk och hjälper dig för att isolera problemet punkter. PowerShell-skript och verktyg som beskrivs kräver två värdar i nätverket (i endera änden av länken testas). En värd måste vara en Windows Server eller skrivbordet, den andra kan vara antingen Windows- eller Linux. 

>[!NOTE]
>Metod för att felsöka, verktyg och metoder som används är personliga inställningar. Det här dokumentet beskriver metod och verktyg I ofta ta. Din metod varierar förmodligen, det inte finns något fel med olika metoder för att lösa problemet. Men om du inte har en upprättad metod kan i detta dokument komma igång i sökvägen till skapa egna metoder, verktyg och inställningar för felsökning av nätverksproblem.
>
>

## <a name="network-components"></a>Nätverkskomponenter
Innan du digging till felsökning diskuterar vi några vanliga termer och komponenter. Den här diskussionen ser vi funderar på att varje komponent i kedjan för slutpunkt-till-slutpunkt som gör anslutningen i Azure.
[![1]][1]

På den högsta nivån beskrivs I tre större nätverk routning domäner.

- Azure-nätverk (blå moln till höger)
- Internet- eller WAN (grön moln i mitten)
- Företagsnätverk (persikofärgad moln till vänster)

Titta på diagrammet från höger till vänster, vi att diskutera kort för varje komponent:
 - **Virtuella** - servern kan ha flera nätverkskort, se till att alla statiska vägar, standardvägar och operativsysteminställningar skickar och tar emot trafik på sätt som du tror att det är. Varje VM SKU har också en begränsning av bandbredd. Om du använder en mindre VM-SKU begränsas trafiken av bandbredden som är tillgängliga för nätverkskortet. Jag använder vanligtvis en DS5v2 för testning (och sedan ta bort göras en gång med tester för att spara pengar) att säkerställa tillräcklig bandbredd på den virtuella datorn.
 - **NIC** – se till att du vet att den privata IP-adress som har tilldelats till nätverkskortet i fråga.
 - **NIC NSG** - det får specifika NSG: er som tillämpas på NIC-nivå, kontrollera NSG-regelsamlingen är lämpligt för trafik som du försöker skicka. Kontrollera portar 5201 för iPerf, exempelvis 3389 för RDP eller 22 för SSH är öppna för att testa trafik passerar.
 - **VNet Subnet** -det nätverkskort som har tilldelats ett specifikt undernät, se till att du vet vilken en och regler som är kopplade till det undernätet.
 - **Undernät NSG** – precis NIC, NSG: er kan tillämpas på undernät samt. Kontrollera NSG-regelsamlingen är lämpligt för trafik som du försöker skicka. (för trafik inkommande till NIC undernätet NSG gäller först och sedan NIC NSG, omvänt trafik utgående från den virtuella datorn NIC NSG gäller först sedan NSG för undernätet kommer in i play).
 - **Undernät UDR** -användardefinierade vägar kan dirigera trafik till ett mellanliggande hopp (till exempel en brandvägg eller belastningsutjämnare). Se till att du vet om det finns en UDR på plats för trafiken och om så var den går och vad som nexthop gör att trafiken. (till exempel en brandvägg kan skicka viss trafik och neka andra trafik mellan samma två värdar).
 - **Gateway-undernät / NSG / UDR** -precis som det Virtuella datorundernätet gateway-undernätet kan ha NSG: er och udr: er. Kontrollera att du vet om de är det och vilka effekter som de har på trafiken.
 - **VNet-Gateway (ExpressRoute)** -när peering (ExpressRoute) eller VPN är aktiverat kan det inte finns många inställningar som kan påverka hur eller om trafik vägar. Om du har flera ExpressRoute-kretsar eller VPN-tunnlar som är anslutna till samma virtuella nätverk Gateway bör vara medveten om vikt anslutningsinställningar som den här inställningen påverkar anslutningsinställningar och påverkar trafiken tar sökvägen.
 - **Vidarebefordra Filter** (visas inte -) ett flöde filter endast gäller för Microsoft-Peering i ExpressRoute, men är viktigt att kontrollera om det inte uppstår vägar som du förväntar dig på Microsoft-Peering. 

Du är nu på WAN-delen av länken. Den här routningsdomän kan vara tjänstleverantören, din företagets WAN eller Internet. Många hopp, tekniker och företag som ingår i dessa länkar kan göra det något svårt att felsöka. Ofta kan arbeta du kan utesluta både Azure och ditt företagsnätverk först innan du hoppa till den här samlingen av företag och hopp.

I föregående diagram är längst till vänster ditt företagsnätverk. Beroende på storleken på ditt företag, kan denna routningsdomän vara några nätverksenheter mellan dig och WAN eller flera lager för enheter i en plats/företagsnätverk.

Svårigheter av dessa tre olika övergripande nätverksmiljöer får den är ofta optimala för att starta vid kanterna och försök att visa där prestanda är bra och där det försämrar. Den här metoden kan du identifiera problemet routningsdomän av tre och därefter visa felsökningen på den specifika miljön.

## <a name="tools"></a>Verktyg
De flesta nätverksproblem kan analyseras och isoleras med grundläggande verktyg som ping och traceroute. Det är ovanligt att du behöver gå djupt en paket-analys som Wireshark. För att underlätta felsökningen, utvecklades Azure anslutningen Toolkit (AzureCT) för att placera vissa av verktygen i ett enkelt paket. För att testa prestanda, som jag vilja använda iPerf och PSPing. iPerf är ett verktyg som används ofta och körs på de flesta operativsystem. iPerf är bra för grundläggande resultaten testerna och är ganska enkel att använda. PSPing är en ping-verktyget som har utvecklats av SysInternals. PSPing är ett enkelt sätt att utföra ICMP- och TCP-ping också lätt att använda ett enda kommando. De här verktygen är lätta och ”installeras” genom att kopiera filerna till en katalog på värden.

Jag har omslutas alla dessa verktyg och metoder i en PowerShell-modul (AzureCT) som du kan installera och använda.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - verktygspaketet Azure anslutning
AzureCT PowerShell-modulen har två komponenter [tillgänglighet testar] [ Availability Doc] och [prestandatester][Performance Doc]. Det här dokumentet är endast berörda med prestandatester, så kan fokusera på länken prestanda två kommandon i PowerShell-modulen.

Det finns tre grundläggande steg för att använda dessa verktyg för att testa prestanda. 1) installera PowerShell-modulen, 2) installera stödjande program iPerf och PSPing 3) kör testet prestanda.

1. Installera PowerShell-modul

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Detta kommando hämtar PowerShell-modulen och installeras lokalt.

2. Installera stödjande program
    ```powershell
    Install-LinkPerformance
    ```
    Det här kommandot AzureCT installerar iPerf och PSPing i en ny katalog ”C:\ACTTools”, öppnas också portar för Windows-brandväggen för att tillåta ICMP och port 5201 (iPerf)-trafiken.

3. Kör testet prestanda

    På fjärrdatorn måste du först installera och köra iPerf i serverläge. Kontrollera också att fjärrvärden lyssnar på antingen 3389 (RDP för Windows) eller 22 (SSH för Linux) och tillåter trafik på port 5201 för iPerf. Om värddatorn windows, installera AzureCT och kör kommandot installera LinkPerformance att ställa in iPerf och brandväggsregler som behövs för att starta iPerf i serverläge. 
    
    När fjärrdatorn är klar öppnar du PowerShell på den lokala datorn och starta testet:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Det här kommandot Kör en serie samtidiga belastning och svarstid tester för att beräkna bandbreddskapacitet och svarstid för nätverkslänk.

4. Granska resultatet från testerna

    PowerShell-utdataformatet liknar:

    [![4]][4]

    Detaljerade resultat för alla iPerf och PSPing test finns i enskilda filer i katalogen AzureCT verktyg på ”C:\ACTTools”.

## <a name="troubleshooting"></a>Felsökning
Om testet prestanda inte ger bör du förväntat resultat, räkna ut varför progressiv steg för steg. Antalet komponenter i sökvägen får erbjuder systematiskt en snabbare sökväg till upplösning än hoppa runt och potentiellt onödan utföra samma tester flera gånger.

>[!NOTE]
>Det här scenariot är ett prestandaproblem, inte ett anslutningsproblem. Stegen är olika om trafik inte skickas alls.
>
>

Först utmaning din antaganden. Är förväntningar rimligt? Till exempel om du har en ExpressRoute-kretsen med 1 Gbit/s och 100 ms av svarstid är orimligt förväntar dig fullständig 1 Gbit/s trafiktyper angivna prestandaegenskaper för TCP via fördröjningar som länkar. Finns det [refererar till avsnittet](#references) för mer på prestanda antaganden.

Sedan jag rekommenderar att du börjar vid kanterna mellan routningsdomäner och försöka isolera problemet till en enda större routningsdomän; Företagets nätverk, WAN eller Azure-nätverk. Personer ofta skuld ”svart ruta” i sökvägen när blaming svart ruta är lätt att göra den avsevärt fördröjer upplösning särskilt om problemet som faktiskt finns i ett område för att du har möjlighet att göra ändringar. Kontrollera att du gör dina vederbörlig möda åt innan du skickar till din serviceleverantör eller Internet.

När du har hittat större routningsdomän som innehåller problemet bör du skapa ett diagram över området i fråga. Antingen på en whiteboard, anteckningar eller Visio som ett diagram ger en konkret ”slaget karta” så att en metodisk metod för att ytterligare isolera problemet. Du kan planera testning punkter och uppdatera kartan som du avmarkerar områden eller gräva djupare när testet fortlöper.

Nu när du har ett diagram börja delar av nätverket i segment och begränsa problemet. Ta reda på var den fungerar och där det inte. Behåll flytta dina tester pekar på isolera nedåt felaktiga komponenten.

Dessutom Glöm inte att titta på andra lager i OSI-modellen. Det är enkelt att fokusera på de nätverks- och lager 1 – 3 (fysisk, Data och nätverket lager) men problem kan också vara in på Layer 7 i Applikationsnivån. Behåll en öppen ihåg och verifiera antaganden.

## <a name="advanced-expressroute-troubleshooting"></a>Avancerad felsökning av ExpressRoute
Om du inte vet där gränsen på molnet är faktiskt kan isolera Azure-komponenter vara en utmaning. När ExpressRoute används är gränsen en nätverkskomponent kallas Microsoft Enterprise kant (MSEE). **När du använder ExpressRoute**, MSEE är den första kontakten till Microsofts nätverk och den senaste hopp lämnar Microsoft network. När du skapar ett anslutningsobjekt mellan din VNet-gateway och ExpressRoute-krets gör du faktiskt en anslutning till MSEE. Identifiera MSEE som den första eller sista hopp (beroende på vilken riktning ska) är det avgörande att isolera Azure nätverksproblem antingen styrka problemet finns i Azure eller ytterligare nedströms i WAN eller företagsnätverket. 

[![2]][2]

>[!NOTE]
> Observera att MSEE finns inte i Azure-molnet. ExpressRoute är faktiskt i kanten av Microsoft network inte faktiskt i Azure. När du är ansluten med ExpressRoute till en MSEE du är ansluten till Microsofts nätverk, där du sedan gå till någon av molntjänster som Office 365 (med Microsoft-Peering) eller Azure (med privata och/eller Microsoft-Peering).
>
>

Om två Vnet (VNets A och B i diagrammet) som är anslutna till den **samma** ExpressRoute-kretsen kan du utföra en serie tester för att isolera problemet i Azure (eller verifiera att den inte är i Azure)
 
### <a name="test-plan"></a>Testplan
1. Kör testet Get-LinkPerformance mellan VM1 och VM2. Det här testet ger information om om problemet är lokala eller inte. Om det här testet producerar acceptabel svarstid och bandbredd resultat, kan du markera det lokala nätverket för virtuella nätverk som bra.
2. Under förutsättning att den lokala VNet är trafik bra, kör testet Get-LinkPerformance mellan VM1 och VM3. Det här testet utför anslutning via Microsoft network nedåt i MSEE och tillbaka i Azure. Om det här testet producerar acceptabel svarstid och bandbredd resultat, kan du markera Azure-nätverk som bra.
3. Om Azure är uteslutas kan du utföra en liknande uppsättning tester på företagets nätverk. Om som testar också bra, är det dags att arbeta med-leverantör eller Internet-leverantör för att diagnosticera WAN-anslutningen. Exempel: Kör det här testet mellan två avdelningskontor eller ditt skrivbord och en datacenters-server. Beroende på vad du testar, hitta slutpunkter (servrar, datorer, etc.) som kan använda denna sökväg.

>[!IMPORTANT]
> Det är viktigt att för varje test du markera tid på dagen som du kör testet och registrera resultatet i en gemensam plats (jag som OneNote eller Excel). Kör varje test bör ha identiska utdata så att du kan jämföra resulterande data över testkörningar och inte har ”hål” i data. Konsekvent på flera test är den främsta orsaken jag använda AzureCT för felsökning. Magiskt är inte i exakt belastningen scenarier som jag kör utan den *magic* är det faktum att jag får ett *konsekvent test- och utdata* från varje test. Spela in tiden och som har konsekventa data varje gång är särskilt användbart om du senare upptäcker att problemet är sporadiska. Att et med datainsamling direkt så undviker du timmars testning samma scenarier (jag lärt dig hårda sätt många år sedan).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problemet är isolerad, vad?
Ju mer isolera du problemet desto lättare är det att åtgärda, men ofta du når den punkt där du inte kan gå djupare eller ytterligare med felsökningen. Exempel: du se länken över tjänstleverantören tar hopp via Europa, men den förväntade sökvägen är i Asien. Den här platsen är när du ska nå om du behöver hjälp. Som du be är beroende av routningsdomän isolerade av du eller ännu bättre om du ska kunna begränsa den till en viss komponent.

Mer information om företagets nätverk kanske dina interna IT-avdelningen eller tjänstprovider som stöder nätverket (som kan vara maskinvarutillverkaren) hjälpa till med enhetskonfigurationen eller maskinvarureparation.

För WAN, kan dela din testresultaten med din serviceleverantör eller till Internet-hjälpa dem igång och undvika som täcker vissa samma grunden som du redan har testat. Dock inte vara offended om de vill kontrollera resultatet sig själva. ”Litar på men kontrollera” är en bra motto när felsökning baserat på andra personer rapporterade resultat.

Med Azure, när du orsaken till felet i detaljerad kan, är det dags att granska den [dokumentationen för Azure-nätverket] [ Network Docs] och sedan om fortfarande behövs [skapar ett supportärende][Ticket Link].

## <a name="references"></a>Referenser
### <a name="latencybandwidth-expectations"></a>Latens/bandbredd förväntningar
>[!TIP]
> Geografisk svarstid (miles eller kilometer) mellan slutpunkter som du testar är allra den största delen av svarstid. Utrustning svarstid (fysiska och virtuella komponenter, antalet hopp osv.) är inblandad, har geography visat sig vara den största delen av övergripande svarstiden när du hanterar WAN-anslutningar. Det är också viktigt att notera att avståndet avståndet fiber kör inte linjär eller översikt över avstånd. Detta avstånd är otroligt svårt att få med en noggrannhet. Därför jag normalt använda en stad avståndet kalkylator på internet och vet att den här metoden är ett grovt stämmer mått, men är tillräckligt för att ange en allmän förväntan.
>
>

Jag har fått en ExpressRoute-installation i Seattle, Washington i USA. I följande tabell visas svarstiden och bandbredd som jag sett testningen till olika platser i Azure. Jag har uppskattade geografiska avståndet mellan varje slutet av testet.

Testa installationen:
 - En fysisk server som kör Windows Server 2016 med ett 10 Gbps NIC, ansluten till en ExpressRoute-krets.
 - En Premium ExpressRoute-krets för 10 Gbit/s på plats som identifieras med privat Peering aktiverat.
 - Ett virtuellt Azure-nätverk med en UltraPerformance gateway för den angivna regionen.
 - En DS5v2 virtuell dator som kör Windows Server 2016 på VNet. Den virtuella datorn kunde inte tillhör en domän ansluten, skapas från standardvärdet Azure-avbildning (utan optimering eller anpassning) med AzureCT installerat.
 - All testning har kommandot AzureCT Get-LinkPerformance med ett belastningstest med 5 minuter för var och en av sex testkörningar. Exempel:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Dataflöde för varje test hade belastningen flödar från den lokala fysisk servern (iPerf klient i Seattle) upp till den virtuella Azure-datorn (iPerf server i listan Azure-region).
 - Kolumnen ”svarstidsdata” är från Nej belastningstest (ett TCP latens test utan iPerf körs).
 - ”Max Bandwidth” kolumndata är från 16 TCP flödet belastningstest med en 1 Mb fönsterstorlek.

[![3]][3]

### <a name="latencybandwidth-results"></a>Latens/bandbredd resultat
>[!IMPORTANT]
> Dessa siffror är för allmänt bruk. Många faktorer som påverkar svarstiden, och dessa värden är vanligtvis konsekvent över tid, villkor i Azure eller tjänsteleverantörer nätverket kan skicka trafik via olika sökvägar när som helst, vilket svarstid och bandbredd kan påverkas. I allmänhet leder effekterna av att ändringarna inte till betydande skillnader.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Plats|Azure<br/>Region|Beräknad<br/>Avståndet (km)|Svarstid|1-session<br/>Bandbredd|Maximal<br/>Bandbredd|
| Seattle | Västra USA 2        |    191 km |   5 ms | 262.0 Mbit/s |  3.74 Gbit/s | 21
| Seattle | Västra USA          |  1,094 km |  18 ms |  82.3 Mbit/s |  3.70 Gbit/s | 20
| Seattle | Centrala USA       |  2,357 km |  40 ms |  38.8 Mbit/s |  2.55 Gbit/s | 17
| Seattle | Södra centrala USA |  2,877 km |  51 ms |  30.6 Mbit/s |  2.49 Gbit/s | 19
| Seattle | Norra centrala USA |  2,792 km |  55 ms |  27,7 Mbit/s |  2.19 Gbit/s | 18
| Seattle | Östra USA 2        |  3,769 km |  73 ms |  21.3 Mbit/s |  1.79 Gbit/s | 16
| Seattle | Östra USA          |  3,699 km |  74 ms |  21,1 Mbit/s |  1,78 Gbit/s | 15
| Seattle | Östra Japan       |  7,705 km | 106 ms |  14.6 Mbit/s |  1.22 enligt Gbit/s | 28
| Seattle | Storbritannien, södra         |  7,708 km | 146 ms |  10.6 Mbit/s |   896 Mbit/s | 24
| Seattle | Västra Europa      |  7,834 km | 153 ms |  10.2 Mbit/s |   761 Mbit/s | 23
| Seattle | Östra Australien   | 12,484 km | 165 ms |   9.4 Mbit/s |   794 Mbit/s | 26
| Seattle | Sydostasien   | 12,989 km | 170 ms |   9.2 Mbit/s |   756 Mbit/s | 25
| Seattle | Södra *   | 10,930 km | 189 ms |   8.2 Mbit/s |   699 Mbit/s | 22
| Seattle | Södra Indien      | 12,918 km | 202 ms |   7,7 Mbit/s |   634 Mbit/s | 27

\*Fördröjning till Brasilien är ett bra exempel där linjär avståndet skiljer sig markant från fiber kör avstånd. Jag förväntar att svarstiden är i nätverket för 160 ms, men är faktiskt 189 ms. Denna skillnad mot min förväntan kan tyda på ett nätverksfel någonstans, men troligen att fiber kör inte går att Brasilien rakt och har en extra 1 000 km eller så att resa till hämta Brasilien från Seattle.

## <a name="next-steps"></a>Nästa steg
1. Ladda ned verktyget Azure anslutningen från GitHub på [http://aka.ms/AzCT][ACT]
2. Följ instruktionerna för [länka prestandatester][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "azure nätverkskomponenter"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute felsökning"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Perf-testmiljö"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-utdata"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index#pivot=services&panel=network
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: http://aka.ms/AzCT











