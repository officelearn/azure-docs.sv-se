---
title: Analysera kantnodprestanda i Azure CDN | Microsoft-dokument
description: Analysera edge-nodprestanda i Microsoft Azure CDN. Edge Performance Analytics tillhandahåller detaljerad informationstrafik och bandbreddsanvändning för CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b8a65d4ae6aaac78e642c851a66b745a940fa0ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593902"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analysera gränsnodsprestanda i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Edge prestandaanalys ger detaljerad informationstrafik och bandbreddsanvändning för CDN. Den här informationen kan sedan användas för att generera trendstatistik, vilket gör att du kan få insikt om hur dina tillgångar cachelagras och levereras till dina kunder. Detta gör att du kan bilda en strategi för hur du optimerar leveransen av ditt innehåll och för att avgöra vilka frågor som bör åtgärdas för att bättre utnyttja CDN. Som ett resultat kommer du inte bara att kunna förbättra dataleveransprestanda, men du kommer också att kunna minska dina CDN-kostnader.

> [!NOTE]
> Alla rapporter använder UTC/GMT-notation när du anger ett datum/en tid.
> 
> 

## <a name="reports-and-log-collection"></a>Rapporter och loggsamling
CDN-aktivitetsdata måste samlas in av Edge Performance Analytics-modulen innan den kan generera rapporter om den. Denna insamlingsprocess sker en gång om dagen och täcker den aktivitet som ägde rum under föregående dag. Detta innebär att en rapports statistik representerar ett urval av dagens statistik vid den tidpunkt då den bearbetades och inte nödvändigtvis innehåller den fullständiga uppsättningen data för den aktuella dagen. Den primära funktionen i dessa rapporter är att bedöma prestanda. De bör inte användas för faktureringsändamål eller exakt numerisk statistik.

> [!NOTE]
> Rådata som Edge Performance-analytiska rapporter genereras från är tillgängliga i minst 90 dagar.
> 
> 

## <a name="dashboard"></a>Instrumentpanel
Instrumentpanelen Edge Performance Analytics spårar aktuell och historisk CDN-trafik genom ett diagram och statistik. Använd den här instrumentpanelen för att identifiera senaste och långsiktiga trender för prestanda för CDN-trafik för ditt konto.

Den här instrumentpanelen består av:

* Ett interaktivt diagram som gör det möjligt att visualisera viktiga mått och trender.
* En tidslinje som ger en känsla av långsiktiga mönster för viktiga mått och trender.
* Viktiga mått och statistisk information om hur vårt CDN-nätverk förbättrar webbplatstrafiken mätt med övergripande prestanda, användning och effektivitet.

### <a name="accessing-the-edge-performance-dashboard"></a>Komma åt instrumentpanelen för kantprestanda
1. Klicka på knappen **Hantera** i CDN-profilbladet.
   
    ![Knappen Hantera CDN-profilblad](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Håll muspekaren över fliken **Analytics** och hovra sedan över det utfällbara **edge-prestandaanalysen.**  Klicka på **Instrumentpanelen**.
   
    Instrumentpanelen för edge-nodanalys visas.

### <a name="chart"></a>Diagram
Instrumentpanelen innehåller ett diagram som spårar ett mått under den tidsperiod som valts på tidslinjen som visas direkt under den.  En tidslinje som visar upp till de två senaste årens CDN-aktivitet visas direkt under diagrammet.

#### <a name="using-the-chart"></a>Använda diagrammet
* Som standard kommer cacheeffektivitetshastigheten för de senaste 30 dagarna att kartläggas.
* Det här diagrammet genereras från data som samlas in dagligen.
* Om du hovrar över en dag i linjediagrammet visas ett datum och värdet för måttet på det datumet.
* Klicka på Markera helger om du vill växla ett överlägg av ljusgråtta lodräta staplar som representerar helger i diagrammet. Den här typen av överlägg är användbar för att identifiera trafikmönster under helgerna.
* Klicka på Visa ett år sedan om du vill växla ett överlägg av föregående års aktivitet under samma tidsperiod till diagrammet. Den här typen av jämförelse ger insikt i långsiktiga CDN-användningsmönster. Diagrammets övre högra hörn innehåller en förklaring som anger färgkoden för varje linjediagram.

#### <a name="updating-the-chart"></a>Uppdatera diagrammet
* Tidsintervall: Utför något av följande:
  * Välj önskat område i tidslinjen. Diagrammet uppdateras med data som motsvarar den valda tidsperioden.
  * Dubbelklicka på diagrammet om du vill visa alla tillgängliga historiska data upp till högst två år.
* Mått: Klicka på diagramikonen som visas bredvid önskat mått. Diagrammet och tidslinjen uppdateras med data för motsvarande mått.

### <a name="key-metrics-and-statistics"></a>Viktiga mått och statistik
#### <a name="efficiency-metrics"></a>Effektivitetsmått
Syftet med dessa mått är att se om cacheeffektiviteten kan förbättras. De största fördelarna med cacheeffektivitet är:

* Minskad belastning på ursprungsservern som kan leda till:
  * Bättre webbserverprestanda.
  * Minskade driftskostnader.
* Förbättrad dataleveransacceleration eftersom fler begäranden kommer att betjänas direkt från CDN.

| Field | Beskrivning |
| --- | --- |
| Cache effektivitet |Anger den procentandel data som överfördes och som serverades från cacheminnet. Det här måttet mäter när en cachelagd version av det begärda innehållet serverades direkt från CDN (edge-servrar) till beställare (t.ex. webbläsare) |
| Träfffrekvens |Anger procentandelen begäranden som har serverats från cacheminnet. Det här måttet mäter när en cachelagd version av det begärda innehållet serverades direkt från CDN (edge-servrar) till beställare (t.ex. webbläsare). |
| % av fjärrbyten - ingen cachekonfiguration |Anger den procentandel av trafiken som har serverats från ursprungsservrar till CDN (kantservrar) som inte cachelagras som ett resultat av funktionen Kringgå cache (HTTP Rules Engine). |
| % av fjärrbyten – cache för utgångna |Anger den procentandel av trafiken som har serverats från ursprungsservrar till CDN (edge-servrar) som ett resultat av inaktuella innehållsåtervalidation. |

#### <a name="usage-metrics"></a>Användningsstatistik
Syftet med dessa mått är att ge insikt i följande besparingsåtgärder:

* Minimera driftskostnaderna via CDN.
* Minska CDN-utgifter genom cacheeffektivitet och komprimering.

> [!NOTE]
> Trafikvolymnummer representerar trafik som användes vid beräkningar av nyckeltal och procentsatser och får endast visa en del av den totala trafiken för kunder med stora volymer.
> 
> 

| Field | Beskrivning |
| --- | --- |
| Ave Bytes Ut |Anger det genomsnittliga antalet byte som överförs för varje begäran som visas från CDN (edge-servrar) till beställaren (t.ex. webbläsare). |
| Ingen cachekonfigurationsbyteshastighet |Anger den procentandel av trafiken som betjänas från CDN (edge-servrar) till beställaren (t.ex. webbläsare) som inte cachelagras på grund av funktionen Bypass-cache. |
| Komprimerad bytehastighet |Anger procentandelen trafik som skickas från CDN (kantservrar) till beställare (t.ex. webbläsare) i komprimerat format. |
| Byte ut |Anger mängden data, i byte, som levererades från CDN (edge-servrar) till beställaren (t.ex. webbläsare). |
| Byte i |Anger mängden data, i byte, som skickas från beställare (t.ex. webbläsare) till CDN (edge-servrar). |
| Fjärrkontroll för byte |Anger mängden data, i byte, som skickas från CDN och kundursprungsservrar till CDN (edge-servrar). |

#### <a name="performance-metrics"></a>Prestandamått
Syftet med dessa mått är att spåra övergripande CDN-prestanda för din trafik.

| Field | Beskrivning |
| --- | --- |
| Överföringshastighet |Anger den genomsnittliga hastighet med vilken innehållet överfördes från CDN till en beställare. |
| Varaktighet |Anger den genomsnittliga tiden, i millisekunder, det tog att leverera en tillgång till en beställare (t.ex. webbläsare). |
| Komprimerad begärandehastighet |Anger procentandelen träffar som levererades från CDN (edge-servrar) till beställaren (t.ex. webbläsare) i komprimerat format. |
| 4xx Felfrekvens |Anger procentandelen träffar som genererade en 4xx-statuskod. |
| 5xx Felfrekvens |Anger procentandelen träffar som genererade en 5xx-statuskod. |
| Träffar |Anger antalet begäranden om CDN-innehåll. |

#### <a name="secure-traffic-metrics"></a>Mått för säker trafik
Syftet med dessa mått är att spåra CDN-prestanda för HTTPS-trafik.

| Field | Beskrivning |
| --- | --- |
| Säker cacheeffektivitet |Anger procentandelen data som överförts för HTTPS-begäranden som serverades från cacheminnet. Det här måttet mäter när en cachelagd version av det begärda innehållet serverades direkt från CDN (edge-servrar) till beställare (t.ex. webbläsare) via HTTPS. |
| Säker överföringshastighet |Anger den genomsnittliga hastighet med vilken innehåll överfördes från CDN (edge-servrar) till beställare (t.ex. webbservrar) via HTTPS. |
| Genomsnittlig säker varaktighet |Anger den genomsnittliga tiden, i millisekunder, det tog att leverera en tillgång till en beställare (t.ex. webbläsare) via HTTPS. |
| Säkra träffar |Anger antalet HTTPS-begäranden för CDN-innehåll. |
| Säkra byte ut |Anger mängden HTTPS-trafik, i byte, som levererades från CDN (edge-servrar) till beställaren (t.ex. webbläsare). |

## <a name="reports"></a>Rapporter
Varje rapport i den här modulen innehåller ett diagram och statistik om bandbredd och trafikanvändning för olika typer av mått (t.ex. HTTP-statuskoder, cachestatuskoder, begärande-URL osv.). Den här informationen kan användas för att fördjupa sig i hur innehåll serveras till dina klienter och för att finjustera CDN-beteende för att förbättra dataleveransprestanda.

### <a name="accessing-the-edge-performance-reports"></a>Komma åt prestandarapporterna för kanten
1. Klicka på knappen **Hantera** i CDN-profilbladet.
   
    ![Knappen Hantera CDN-profilblad](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Håll muspekaren över fliken **Analytics** och hovra sedan över det utfällbara **edge-prestandaanalysen.**  Klicka på **HTTP Large Object**.
   
    Skärmen för kannodanalysrapporter visas.

| Rapport | Beskrivning |
| --- | --- |
| Daglig sammanfattning |Gör att du kan visa dagliga trafiktrender under en angiven tidsperiod. Varje stapel i det här diagrammet representerar ett visst datum. Storleken på stapeln anger det totala antalet träffar som inträffade det datumet. |
| Sammanfattning per timme |Gör att du kan visa trafiktrender per timme under en angiven tidsperiod. Varje stapel i det här diagrammet representerar en timme på ett visst datum. Storleken på stapeln anger det totala antalet träffar som inträffade under den timmen. |
| Protokoll |Visar fördelningen av trafiken mellan HTTP- och HTTPS-protokollen. Ett donutdiagram anger hur många procent träffar som inträffade för varje typ av protokoll. |
| HTTP-metoder |Gör att du kan få en snabb uppfattning om vilka HTTP-metoder som används för att begära dina data. Vanligtvis är de vanligaste HTTP-begärandemetoderna GET, HEAD och POST. Ett donutdiagram anger hur många procent träffar som inträffade för varje typ av HTTP-begärandemetod. |
|  URL:er |Innehåller ett diagram som visar de tio mest efterfrågade webbadresserna. En stapel visas för varje WEBBADRESS. Höjden på stapeln anger hur många träffar just webbadressen har genererat under den tidsperiod som rapporten omfattar. Statistik för de 100 mest efterfrågade webbadresserna visas direkt under det här diagrammet. |
| CNAMEs (CNAMEs) |Innehåller ett diagram som visar de 10 främsta CNAMEs som används för att begära tillgångar under en rapports tidsperiod. Statistik för de 100 mest efterfrågade CNAMEs visas direkt under detta diagram. |
| Ursprung |Innehåller ett diagram som visar de 10 bästa CDN- eller kundursprungsservrarna från vilka tillgångar begärdes under en angiven tidsperiod. Statistik för de 100 mest efterfrågade CDN- eller kundursprungsservrarna visas direkt under det här diagrammet. Kundernas ursprungsservrar identifieras med namnet som definieras i alternativet Katalognamn. |
| Geo POP |Visar hur mycket av din trafik som dirigeras genom en viss point-of-presence (POP). Förkortningen med tre bokstäver representerar en POP i vårt CDN-nätverk. |
| Klienter |Innehåller ett diagram som visar de 10 klienter som har begärt tillgångar under en angiven tidsperiod. I den här rapporten anses alla begäranden som kommer från samma IP-adress komma från samma klient. Statistik för de 100 bästa klienterna visas direkt under det här diagrammet. Den här rapporten är användbar för att bestämma hämtningsaktivitetsmönster för dina bästa klienter. |
| Cachestatus |Ger en detaljerad uppdelning av cachebeteende, som kan avslöja metoder för att förbättra den övergripande slutanvändarens upplevelse. Eftersom den snabbaste prestandan kommer från cacheträffar kan du optimera dataleveranshastigheter genom att minimera cachemissar och utgångna cacheträffar. |
| INGA Detaljer |Innehåller ett diagram som visar de 10 främsta webbadresserna för tillgångar för vilka cacheinnehållets färskhet inte har kontrollerats under en angiven tidsperiod. Statistik för de 100 bästa webbadresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| CONFIG_NOCACHE Detaljer |Innehåller ett diagram som visar de 10 främsta webbadresserna för tillgångar som inte cachelagrades på grund av kundens CDN-konfiguration. Dessa typer av tillgångar betjänades direkt från ursprungsservern. Statistik för de 100 bästa webbadresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| EJACHEABLE Detaljer |Innehåller ett diagram som visar de 10 främsta url:erna för tillgångar som inte kunde cachelagras på grund av begärandehuvuddata. Statistik för de 100 bästa webbadresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_HIT Detaljer |Innehåller ett diagram som visar de 10 främsta webbadresserna för tillgångar som visas direkt från cacheminnet. Statistik för de 100 bästa webbadresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_MISS Detaljer |Innehåller ett diagram som visar de 10 främsta webbadresserna för tillgångar som har en cachestatus på TCP_MISS. Statistik för de 100 bästa webbadresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_EXPIRED_HIT detaljer |Innehåller ett diagram som visar de 10 främsta webbadresserna för inaktuella tillgångar som har serverats direkt från POP.Contains a graph that displays the top 10 URLLs for stale assets that were served directly from the POP. Statistik för de 100 bästa webbadresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_EXPIRED_MISS detaljer |Innehåller ett diagram som visar de 10 främsta webbadresserna för inaktuella tillgångar för vilka en ny version måste hämtas från ursprungsservern. Statistik för de 100 bästa webbadresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_CLIENT_REFRESH_MISS Detaljer |Innehåller ett stapeldiagram som visar de 10 främsta url:erna för tillgångar hämtades från en ursprungsserver på grund av en begäran om att inte cachelagra från klienten. Statistik för de 100 bästa webbadresserna för dessa typer av begäranden visas direkt under det här diagrammet. |
| Typer av klientbegäranden |Anger vilken typ av begäranden som har gjorts av HTTP-klienter (t.ex. webbläsare). Den här rapporten innehåller ett donutdiagram som ger en känsla för hur begäranden hanteras. Bandbredds- och trafikinformation för varje begärandetyp visas under diagrammet. |
| Användaragent |Innehåller ett stapeldiagram som visar de 10 vanligaste användaragenterna för att begära ditt innehåll via vår CDN. Vanligtvis är en användaragent en webbläsare, mediaspelare eller en mobiltelefonwebbläsare. Statistik för de 100 bästa användaragenterna visas direkt under det här diagrammet. |
| Hänvisningsadresser |Innehåller ett stapeldiagram som visar de 10 som refererar till innehåll som nås via vår CDN. Vanligtvis är en referent webbadressen till webbsidan eller resursen som länkar till ditt innehåll. Detaljerad information finns under diagrammet för de 100 främsta referensarna. |
| Komprimeringstyper |Innehåller ett donutdiagram som delar upp begärda tillgångar efter om de komprimerades av våra kantservrar. Procentandelen komprimerade tillgångar delas upp efter den typ av komprimering som används. Detaljerad information finns under diagrammet för varje komprimeringstyp och status. |
| Filtyper |Innehåller ett stapeldiagram som visar de 10 vanligaste filtyperna som har begärts via vårt CDN för ditt konto. I den här rapporten definieras en filtyp av tillgångens filnamnstillägg och Internet-medietyp (t.ex. \[html-text/html\], .htm \[text/html\], .aspx \[text/html\], etc.). Detaljerad information finns under diagrammet för de 100 filtyperna. |
| Unika filer |Innehåller ett diagram som visar det totala antalet unika tillgångar som begärdes en viss dag under en viss tidsperiod. |
| Sammanfattning av tokenauth |Innehåller ett cirkeldiagram som ger en snabb översikt över om begärda tillgångar skyddades av tokenbaserad autentisering. Skyddade tillgångar visas i diagrammet enligt resultaten av deras försök till autentisering. |
| Information om tokenauth neka |Innehåller ett stapeldiagram som gör att du kan visa de 10 begäranden som nekades på grund av tokenbaserad autentisering. |
| HTTP-svarskoder |Ger en uppdelning av HTTP-statuskoder (t.ex. 200 OK, 403 Forbidden, 404 Hittades inte, etc.) som levererades till dina HTTP-klienter av våra kantservrar. Med ett cirkeldiagram kan du snabbt bedöma hur dina tillgångar har delgivits. Detaljerade statistiska uppgifter finns för varje svarskod under diagrammet. |
| 404 fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 vanligaste begärandena som resulterade i en svarskod på 404 hittades inte. |
| 403 fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 vanligaste begärandena som resulterade i en 403 Förbjuden svarskod. En 403 Förbjuden svarskod inträffar när en begäran nekas av en kundursprungsserver eller en kantserver på vår POP. |
| 4xx Fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 vanligaste begärandena som resulterade i en svarskod i intervallet 400. Undantagna från den här rapporten är 403 Hittades inte och 404 Förbjudna svarskoder. Vanligtvis uppstår en 4xx-svarskod när en begäran nekas till följd av ett klientfel. |
| 504 fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 vanligaste begärandena som resulterade i en 504 Gateway Timeout-svarskod. En 504 Gateway Timeout-svarskod inträffar när en timeout inträffar när en HTTP-proxy försöker kommunicera med en annan server. När det gäller vårt CDN uppstår vanligtvis en 504 Gateway Timeout-svarskod när en kantserver inte kan upprätta kommunikation med en kundursprungsserver. |
| 502 fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 vanligaste begärandena som resulterade i en 502-svarskod för felaktig gateway. En 502-fel gateway-svarskod inträffar när ett HTTP-protokollfel inträffar mellan en server och en HTTP-proxy. När det gäller vårt CDN uppstår vanligtvis en 502-svarskod för felaktig gateway när en kundursprungsserver returnerar ett ogiltigt svar till en kantserver. Ett svar är ogiltigt om det inte kan tolkas eller om det är ofullständigt. |
| 5xx Fel |Innehåller ett stapeldiagram som gör att du kan visa de 10 vanligaste begärandena som resulterade i en svarskod i intervallet 500.  Undantagna från den här rapporten är 502 Bad Gateway och 504 Gateway Timeout-svarskoder. |

## <a name="see-also"></a>Se även
* [Översikt över Azure CDN](cdn-overview.md)
* [Statistik i realtid i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Åsidosätta standard-HTTP-beteende med hjälp av regelmotorn](cdn-rules-engine.md)
* [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)

