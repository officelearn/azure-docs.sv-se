---
title: Analysera gränsnodsprestanda i Azure CDN | Microsoft Docs
description: Analysera gränsnodsprestanda i Microsoft Azure CDN. Edge-prestandaanalys ger detaljerad information trafik och bandbreddsanvändning användning för CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7dfa252c29121adca2ecc77c08b2fca81d56e575
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61070107"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analysera gränsnodsprestanda i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Edge-prestandaanalys ger detaljerad information trafik och bandbreddsanvändning användning för CDN. Den här informationen kan sedan användas för att skapa statistik om trender, där du kan få information om hur dina resurser som ska cachelagras och levereras till kunderna. I sin tur på så sätt kan du att bilda en strategi för hur du optimera leveransen av ditt innehåll och för att avgöra vilka problem ska åtgärdas att bättre utnyttja CDN. Därför kan inte bara kommer du att kunna förbättra prestanda för leverans av data, men du kan också minska dina kostnader för CDN.

> [!NOTE]
> Alla rapporter använder UTC/GMT-notering när du anger ett datum/tid.
> 
> 

## <a name="reports-and-log-collection"></a>Rapporter och Logginsamling
CDN aktivitetsdata måste samlas in av modulen Edge-prestandaanalys innan det kan generera rapporter på den. Den här samlingen processen inträffar när en dag och det täcker den aktivitet som ägde rum under föregående dag. Det innebär att en rapport statistik representerar ett exempel på den dagen statistik när den har bearbetats och inte nödvändigtvis innehåller en fullständig uppsättning med data för den aktuella dagen. De här rapporterna huvudsakliga syfte är att utvärdera prestanda. De bör inte användas för fakturering eller exakta numeriska statistik.

> [!NOTE]
> Rådata som Edge prestanda analytiska rapporter genereras finns i minst 90 dagar.
> 
> 

## <a name="dashboard"></a>Instrumentpanel
Edge-prestandaanalys instrumentpanelen spårar aktuell och historisk CDN-trafik via ett diagram och statistik. Använd den här instrumentpanelen för att identifiera de senaste och långsiktiga trender på prestanda för CDN-trafik för ditt konto.

Den här instrumentpanelen består av:

* Ett interaktivt diagram som gör att visualisering av viktiga mått och trender.
* En tidslinje med en uppfattning om långsiktiga mönster för statistik och trender.
* Viktiga mätvärden och statistisk information om hur du förbättrar vår CDN-nätverket webbplatstrafiken enligt övergripande prestanda, användning och effektivitet.

### <a name="accessing-the-edge-performance-dashboard"></a>Åtkomst till instrumentpanelen för edge-prestanda
1. Från bladet för CDN-profil klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och sedan hovra över den **Edge-prestandaanalys** utfällbar meny.  Klicka på **instrumentpanelen**.
   
    Edge node analytics-instrumentpanelen visas.

### <a name="chart"></a>Diagram
Instrumentpanelen innehåller ett diagram som spårar ett mått över valda på tidslinjen som visas direkt under den tidsperioden.  En tidslinje att diagram till den senaste upp två års CDN aktivitet visas direkt under diagrammet.

#### <a name="using-the-chart"></a>Med hjälp av diagrammet
* Cache effektivitet avgiften för de senaste 30 dagarna kommer att läggas till som standard.
* Det här diagrammet är genereras sorterad per dag.
* Hovra över en dag på linjediagrammet visar ett datum och värdet för måttet på samma datum.
* Klicka på Markera helger för att växla mellan ett överlägg av ljust grå lodräta staplar som representerar helger till diagrammet. Den här typen av överlägget är användbart för att identifiera trafikmönster över helger.
* Klicka på Visa ett år sedan för att växla en övertäckning av det föregående årets aktivitet under samma tidsperiod till diagrammet. Den här typen av jämförelse ger inblick i användningsmönster för långsiktig CDN. Det övre högra hörnet av diagrammet innehåller en förklaring som anger färgkod för varje linjediagram.

#### <a name="updating-the-chart"></a>Uppdatera diagrammet
* Tidsintervall: Gör något av följande:
  * Välj önskad region i tidslinjen. Diagrammet kommer att uppdateras med data som motsvarar den valda tidsperioden.
  * Dubbelklicka på diagrammet om du vill visa alla tillgängliga historiska data upp till två år.
* Mått: Klicka på diagramikonen som visas bredvid önskat mått. Diagrammet och tidslinjen uppdateras med data för motsvarande mått.

### <a name="key-metrics-and-statistics"></a>Viktiga mätvärden och statistik
#### <a name="efficiency-metrics"></a>Effektivitet mått
Syftet med de här måtten är att se om cachelagringseffektivitet kan förbättras. De främsta fördelarna som härletts från cachelagringseffektivitet är:

* Minskade belastningen på den ursprungliga servern, vilket kan leda till:
  * Bättre webbserverprestanda.
  * Minskade driftskostnader.
* Förbättrat data leverans acceleration eftersom fler begäranden hanteras direkt från CDN.

| Fält | Beskrivning |
| --- | --- |
| Cachelagringseffektivitet |Anger procentandelen av data som överförs och som har behandlats från cachen. Det här måttet åtgärder när en cachelagrad version av det begärda innehållet behandlades direkt från CDN (edge-servrar) till beställare (t.ex. webbläsare) |
| Antal träffar i skriptmotorcache |Anger procentandelen förfrågningar som har hämtats från cachen. Det här måttet åtgärder när en cachelagrad version av det begärda innehållet behandlades direkt från CDN (edge-servrar) till beställare (t.ex. webbläsare). |
| % av Remote byte - inga Cache-Config |Anger procentandelen av trafiken som har hämtats från ursprungsservrar till CDN (edge-servrar) som inte cachelagras på grund av funktionen kringgå Cache (HTTP-regelmotor). |
| % av Remote byte - Cache som har upphört att gälla |Anger procentandelen av trafiken som har hämtats från ursprungsservrar till CDN (edge-servrar) på grund av inaktuella innehåll omverifieringen. |

#### <a name="usage-metrics"></a>Användningsstatistik
Syftet med de här måtten är att ge insikter i följande övergripande kostnader mått:

* Minimera driftskostnaderna via CDN.
* Minska CDN utgifter via cachelagringseffektivitet och komprimering.

> [!NOTE]
> Trafik volym siffror representerar trafik som användes i beräkningar av förhållanden och procentsatser, och endast visa en del av den totala trafiken för kunder med stora volymer.
> 
> 

| Fält | Beskrivning |
| --- | --- |
| Para byte ut |Anger det genomsnittliga antalet byte som överfördes för varje begäran som hämtats från CDN (edge-servrar) till beställare (t.ex. webbläsare). |
| Inga Cache Config Byte-hastighet |Anger procentandelen av trafiken som hämtats från CDN (edge-servrar) till beställare (t.ex. webbläsare) som inte cachelagras på grund av funktionen kringgå Cache. |
| Komprimerade Byte-hastighet |Anger procentandelen av trafik som skickas från CDN (edge-servrar) till beställare (t.ex. webbläsare) i ett komprimerat format. |
| Byte ut |Anger mängden data i byte som har levererats från CDN (edge-servrar) till beställare (t.ex. webbläsare). |
| Byte |Anger mängden data i byte som skickats från beställare (t.ex. webbläsare) till CDN (edge-servrar). |
| Byte fjärr |Anger mängden data i byte som skickats från CDN och kunden ursprungsservrar till CDN (edge-servrar). |

#### <a name="performance-metrics"></a>Prestandamått
Syftet med de här måtten är att spåra övergripande CDN-prestanda för trafiken.

| Fält | Beskrivning |
| --- | --- |
| Överföringshastighet |Anger Genomsnittshastigheten innehåll har överförts från CDN till en beställare. |
| Varaktighet |Anger den genomsnittliga tiden, i millisekunder som det tog för att leverera en tillgång till en beställare (t.ex. webbläsare). |
| Komprimerade begärandehastighet |Anger procentandelen träffar som levererades från CDN (edge-servrar) till den som begär (t.ex. webbläsare) i ett komprimerat format. |
| Frekvens för 4xx-fel |Anger procentandelen träffar som genererat en 4xx-statuskod. |
| Frekvens för 5xx-fel |Anger procentandelen träffar som genererat en 5xx-statuskod. |
| Träffar |Anger antalet begäranden för CDN-innehåll. |

#### <a name="secure-traffic-metrics"></a>Säker trafik mått
Syftet med de här måtten är att spåra CDN-prestanda för HTTPS-trafik.

| Fält | Beskrivning |
| --- | --- |
| Säker Cachelagringseffektivitet |Anger procentandelen av data som överförs för HTTPS-begäranden som har hämtats från cachen. Det här måttet åtgärder när en cachelagrad version av det begärda innehållet behandlades direkt från CDN (edge-servrar) till beställare (t.ex. webbläsare) via HTTPS. |
| Säker överföringshastighet |Anger Genomsnittshastigheten innehåll har överförts från CDN (edge-servrar) till beställare (t.ex. webbservrar) via HTTPS. |
| Genomsnittlig varaktighet för säker |Anger den genomsnittliga tiden, i millisekunder som det tog för att leverera en tillgång till en beställare (t.ex. webbläsare) via HTTPS. |
| Säker träffar |Anger hur många HTTPS-begäranden för CDN-innehåll. |
| Säker byte ut |Anger mängden HTTPS-trafik, i byte som har levererats från CDN (edge-servrar) till beställare (t.ex. webbläsare). |

## <a name="reports"></a>Rapporter
Varje rapport i den här modulen innehåller ett diagram och statistik om användning för bandbredd och trafik för olika typer av mått (t.ex. HTTP-statuskoder, cache-statuskoder, begäran-URL, osv.). Den här informationen kan användas till gräver djupare hur innehållet visas för klienter och för att finjustera CDN beteende för att förbättra prestanda för leverans av data.

### <a name="accessing-the-edge-performance-reports"></a>Åtkomst till edge sestavy výkonu
1. Från bladet för CDN-profil klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och sedan hovra över den **Edge-prestandaanalys** utfällbar meny.  Klicka på **LOB-HTTP-**.
   
    Edge node analytics rapporter skärm som visas.

| Rapport | Beskrivning |
| --- | --- |
| Daglig sammanfattning |Kan du visa trender för daglig trafik över en angiven tidsperiod. Varje stapel i diagrammet motsvarar ett visst datum. Storleken på fältet anger det totala antalet träffar som inträffat på det datumet. |
| Per timme sammanfattning |Kan du visa per timme trafik trender under en angiven tidsperiod. Varje stapel i diagrammet representerar en enskild timme på ett visst datum. Storleken på fältet anger det totala antalet träffar som uppstod under den timmen. |
| Protokoll |Visar fördelningen av trafiken mellan HTTP och HTTPS-protokoll. Ett ringdiagram indikerar procenttalet träffar som inträffade för varje typ av protokoll. |
| HTTP-metoder |Kan du få en snabb översikt över för HTTP-metoder som används för att begära dina data. Vanligtvis är de vanligaste HTTP-begäran metoderna GET, HEAD och POST. Ett ringdiagram indikerar träffar som inträffat för varje typ av HTTP-frågemetoden. |
| URL: er |Innehåller ett diagram som visar de översta 10 begärda URL: er. Ett fält visas för varje URL. Höjden på fältet anger hur många träffar som specifika URL: en har genererat över tidsintervallet som omfattas av rapporten. Statistik för de 100 främsta begärda URL: er visas direkt under det här diagrammet. |
| CNAMEs |Innehåller ett diagram som visar upp 10 CNAME-poster för att begära tillgångar över tiden sträcker sig över av en rapport. Statistik för de 100 främsta begärda CNAME-poster visas direkt under det här diagrammet. |
| Ursprung |Innehåller ett diagram som visar de översta 10 CDN eller kund ursprungsservrar som begärdes tillgångar över en angiven tidsperiod. Statistik för de 100 främsta begärda CDN eller kund ursprungsservrar visas direkt under det här diagrammet. Kunden ursprungsservrar identifieras med det namn som definierats i alternativet katalognamn. |
| GEO-POP |Visar hur mycket av din trafik som dirigeras via en viss point-of-presence (POP). Ländernas trebokstavsförkortningar representerar en POP i vår CDN-nätverket. |
| Klienter |Innehåller ett diagram som visar de översta 10 klienter som har begärt tillgångar över en angiven tidsperiod. För den här rapporten anses alla begäranden som kommer från samma IP-adress vara från samma klient. Statistik för de översta 100 klienterna visas direkt under det här diagrammet. Den här rapporten är användbart för att fastställa download aktivitet mönster för övre klienter. |
| Status för cachelagring |Ger detaljer för beteendet för cachelagring, vilket kan avslöja metoder för att förbättra den övergripande slutanvändarens upplevelsen. Eftersom bästa prestanda kommer från cacheträffar, kan du optimera hastighet för leverans av data genom att minimera cachemissar och har upphört att gälla cacheträffar. |
| Ingen information |Innehåller ett diagram som visar översta 10 URL: er för tillgångar som cacheminnet content färskhet inte kontrollerades över en angiven tidsperiod. Statistik för de översta 100 URL-adresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| CONFIG_NOCACHE Details |Innehåller ett diagram som visar de översta 10 URL: er för tillgångar som inte har cachelagrats på grund av kundens CDN konfiguration. Dessa typer av tillgångar har hanteras direkt från den ursprungliga servern. Statistik för de översta 100 URL-adresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| UNCACHEABLE information |Innehåller ett diagram som visar översta 10 URL: er för tillgångar inte kan cachelagras på grund av begärandedata för rubriken. Statistik för de översta 100 URL-adresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_HIT information |Innehåller ett diagram som visar översta 10 URL: er för tillgångar som hanteras direkt från cachen. Statistik för de översta 100 URL-adresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_MISS Details |Innehåller ett diagram som visar översta 10 webbadresserna för tillgångar som har statusen cache TCP_MISS. Statistik för de översta 100 URL-adresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_EXPIRED_HIT information |Innehåller ett diagram som visar översta 10 webbadresserna för inaktuella tillgångar som har hämtats direkt från den POP-plats. Statistik för de översta 100 URL-adresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_EXPIRED_MISS information |Innehåller ett diagram som visar översta 10 URL: er för inaktuella tillgångar som hade en ny version som ska hämtas från den ursprungliga servern. Statistik för de översta 100 URL-adresserna för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_CLIENT_REFRESH_MISS Details |Innehåller ett stapeldiagram som visar de översta 10 URL: er för tillgångar har hämtats från en ursprungsserver på grund av en no-cache-begäran från klienten. Statistik för de översta 100 URL-adresserna för dessa typer av begäranden visas direkt under det här diagrammet. |
| Klienttyper för begäran |Anger vilken typ av förfrågningar som gjorts av HTTP-klienter (till exempel webbläsare). Den här rapporten innehåller ett ringdiagram som ger en uppfattning om hur begäranden hanteras. Information om bandbredd och trafik för varje typ av begäran visas under diagrammet. |
| Användaragent |Innehåller ett stapeldiagram som visar de översta 10 användaragenter för att begära innehållet genom vår CDN. En användaragent är vanligtvis en webbläsare, mediaspelare eller en mobiltelefon webbläsare. Statistik för de översta 100 användaragenter visas direkt under det här diagrammet. |
| Referenter |Innehåller ett stapeldiagram som visar de översta 10 hänvisningar till innehåll som nås via vår CDN. En referent är vanligtvis URL: en för webbsidan eller resurs som länkar till ditt innehåll. Detaljerad information finns nedan i diagrammet för de 100 främsta hänvisningar. |
| Komprimeringstyper |Innehåller ett ringdiagram som delar upp begärda tillgångar som om de komprimerats med vår edge-servrar. Procentandelen av komprimerade tillgångar är uppdelade efter typ av komprimering som används. Detaljerad information finns nedan i diagrammet för varje typ av komprimering och status. |
| Filtyper |Innehåller ett stapeldiagram som visar de översta 10 filtyper som har begärts via vår CDN för ditt konto. För den här rapporten, en filtyp definieras av tillgångens filnamnstillägg och Internet medietyp (t.ex. .html \[text/html\], .htm \[text/html\], .aspx \[text/html\]osv.). Detaljerad information finns nedan i diagrammet för översta 100 filtyper. |
| Unika filer |Innehåller ett diagram som visar det totala antalet unika tillgångar som begärdes vid en viss dag under en angiven tidsperiod. |
| Token-autentisering-sammanfattning |Innehåller ett cirkeldiagram som ger en snabb översikt på om begärda tillgångar har skyddats av tokenbaserad autentisering. Skyddade tillgångar visas i diagrammet enligt resultatet av sin försök autentisering. |
| Token Auth neka information |Innehåller ett stapeldiagram där du kan visa de översta 10 förfrågningar som nekades på grund av tokenbaserad autentisering. |
| HTTP-svarskoder |Visar en uppdelning av HTTP-statuskoder (t.ex. 200 OK, 403 tillåts inte, det gick inte att hitta 404, etc.) som har levererats till din HTTP-klienter genom våra edgeservrar. Ett cirkeldiagram kan du snabbt utvärdera hur dina resurser har hämtats. Detaljerad statistiska data tillhandahålls för varje svarskod nedan i diagrammet. |
| 404-fel |Innehåller ett stapeldiagram där du kan visa de översta 10 förfrågningar som resulterade i en 404 det gick inte att hitta svarskod. |
| 403-fel |Innehåller ett stapeldiagram där du kan visa de översta 10 förfrågningar som resulterade i en 403 Forbidden svarskod. Ett 403 Forbidden svarskod inträffar när en begäran nekas av en kund ursprungsservern eller en edge-server på vår POP. |
| 4xx-fel |Innehåller ett stapeldiagram där du kan visa de översta 10 förfrågningar som resulterade i en svarskod i 400-intervallet. Den här rapporten gäller 403 gick inte att hitta och 404 förbjuden svarskoder. En svarskod 4xx inträffar vanligtvis när en begäran nekas på grund av ett klientfel. |
| 504 fel |Innehåller ett stapeldiagram där du kan visa de översta 10 förfrågningar som resulterade i en 504 Gateway-Timeout-svarskod. En 504 Gateway-Timeout-svarskod inträffar när en tidsgräns som inträffar när en HTTP-proxy försöker kommunicera med en annan server. När det gäller våra CDN förekommer en 504 Gateway-Timeout-svarskod normalt när en edge-server kan inte upprätta kommunikation med en kund ursprungsservern. |
| 502-fel |Innehåller ett stapeldiagram där du kan visa de översta 10 förfrågningar som resulterade i en 502 felaktig Gateway-svarskod. En 502 felaktig Gateway-svarskod inträffar när en HTTP-protokollfel uppstår mellan en server och en HTTP-proxy. När det gäller våra CDN förekommer en 502 felaktig Gateway-svarskod normalt när en kund ursprungsservern returnerar ett ogiltigt svar till en edge-server. Svaret är ogiltig om det inte går att parsa eller om det är ofullständig. |
| 5xx-fel |Innehåller ett stapeldiagram där du kan visa de översta 10 förfrågningar som resulterade i en svarskod i intervallet 500.  Den här rapporten gäller 502 felaktig Gateway och 504 Gateway-Timeout-svarskoder. |

## <a name="see-also"></a>Se också
* [Översikt över Azure CDN](cdn-overview.md)
* [Realtidsstatistik i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Åsidosätta standardbeteendet för HTTP med regelmotorn](cdn-rules-engine.md)
* [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)

