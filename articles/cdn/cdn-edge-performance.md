---
title: "Analysera gränsnodprestanda i Azure CDN | Microsoft Docs"
description: "Analysera gränsnodprestanda i Microsoft Azure CDN. Edge prestanda Analytics ger detaljerad information trafik och bandbredd användning för CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: ad285b4e2226c85859acb22ba214cc44c77c08e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analysera gränsnodsprestanda i Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Edge prestanda analytics ger detaljerad information trafik och bandbredd användning för CDN. Den här informationen kan sedan användas för att generera trender statistik, som gör det möjligt att få information om hur dina tillgångar som ska cachelagras och levereras till klienterna. I sin tur detta kan du att bilda en strategi för hur du optimerar leveransen av ditt innehåll och för att avgöra vilka problem ska behandlas för bättre utnyttjar CDN. Därför kan inte bara kommer du att kunna förbättra prestanda för leverans av data, men du kan också minska kostnaderna CDN.

> [!NOTE]
> Alla rapporter använder UTC/GMT-notering när du anger ett datum/tid.
> 
> 

## <a name="reports-and-log-collection"></a>Rapporter och Logginsamling
CDN-aktivitet måste samlas in av modulen Edge prestanda Analytics innan den kan även generera rapporter på den. Samlingen processen när en dag och den täcker den aktivitet som utfördes under föregående dag. Detta innebär att en rapport statistiken representerar ett urval av statistik för den dag då den bearbetades och inte nödvändigtvis innehåller en fullständig uppsättning data för den aktuella dagen. De här rapporterna huvudfunktion är att utvärdera prestanda. De bör inte användas för fakturering eller exakta numeriska statistik.

> [!NOTE]
> Rådata som Edge prestanda analysrapporter genereras är tillgänglig för minst 90 dagar.
> 
> 

## <a name="dashboard"></a>Instrumentpanel
Infopanelen Edge prestanda Analytics spårar aktuell och historisk CDN-trafik via ett diagram och statistik. Använd den här instrumentpanelen för att identifiera trender för senaste och lång sikt på prestandan hos CDN-trafik för ditt konto.

Den här instrumentpanelen består av:

* Ett interaktivt diagram som tillåter visualisering av nyckelvärden och trender.
* En tidslinje som ger en uppfattning om långsiktiga mönster för nyckelvärden och trender.
* Nyckelvärden och statistisk information om hur våra CDN-nätverkets förbättrar trafiken mätt av övergripande prestanda, användning och effektivitet.

### <a name="accessing-the-edge-performance-dashboard"></a>Åtkomst till instrumentpanelen edge prestanda
1. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och klicka sedan hovra över den **kant prestanda långsammare Analytics** utfällbar.  Klicka på **instrumentpanelen**.
   
    Edge nod analytics instrumentpanelen visas.

### <a name="chart"></a>Diagrammet
Instrumentpanelen innehåller ett diagram som visar ett mått över valda i tidslinjen visas direkt under den tidsperioden.  En tidslinje att diagram upp till den sista två års CDN aktivitet visas direkt under diagrammet.

#### <a name="using-the-chart"></a>Med hjälp av diagrammet
* Som standard diagrammet effektivitet cachelagrade under de senaste 30 dagarna.
* Det här diagrammet genereras från data som sammanställs dagligen.
* Hovra över en dag i linjediagrammet kommer att ange ett datum och värdet för måttet på det datumet.
* Klicka på Markera helger om du vill växla övertäckning lätta grå lodräta staplar som representerar söndag till diagrammet. Den här typen av överlägget är användbart för att identifiera trafikmönster över helger.
* Klicka på Visa ett år sedan för att växla en övertäckning av föregående år aktiviteten under samma tid till diagrammet. Den här typen av jämförelse ger inblick i långsiktiga CDN användningsmönster. Det övre högra hörnet i diagrammet innehåller en förklaring som anger färgkoden för varje linjediagram.

#### <a name="updating-the-chart"></a>Uppdatera diagrammet
* Tidsintervall: Utföra något av följande:
  * Välj önskad region i tidslinjen. Diagrammet kommer att uppdateras med data som motsvarar den valda tidsperioden.
  * Dubbelklicka på diagrammet om du vill visa alla tillgängliga historiska data upp till två år.
* Mått: Klicka på ikonen diagram som visas bredvid önskade mått. Diagrammet och tidslinjen uppdateras med data för motsvarande mått.

### <a name="key-metrics-and-statistics"></a>Viktiga mått och statistik
#### <a name="efficiency-metrics"></a>Effektivitet mått
Syftet med de här måtten är att se om cachens effektivitet kan förbättras. De främsta fördelarna som härletts från cachens effektivitet är:

* Minskad belastningen på den ursprungliga servern, vilket kan leda till:
  * Bättre prestanda för web-server.
  * Minskade driftskostnader.
* Bättre data leverans acceleration eftersom fler begäranden hanteras direkt från CDN.

| Fält | Beskrivning |
| --- | --- |
| Cachens effektivitet |Anger procentandelen av data som överförs som skickades från cachen. Detta mått åtgärder när en cachelagrad version av det begärda innehållet behandlades direkt från CDN (kant servrar) till beställare (t.ex. webbläsare) |
| Antal träffa i skriptmotorcache |Anger procentandelen förfrågningar som har sett från cachen. Detta mått åtgärder när en cachelagrad version av det begärda innehållet behandlades direkt från CDN (kant servrar) till beställare (t.ex. webbläsare). |
| % av Remote byte - ingen Cache-konfiguration |Anger procentandelen av trafik som skickades från ursprung servrar till CDN (kant-servrar) som inte cachelagras på grund av funktionen kringgå Cache (HTTP-regelmotor). |
| % av Remote byte - Cache har upphört att gälla |Anger procentandelen av trafik som skickades från ursprung servrar till CDN (kant servrar) på grund av inaktuella Omverifiering av innehåll. |

#### <a name="usage-metrics"></a>Användningsstatistik
Syftet med de här måtten är att ge insikt om kostnaden skärande följande åtgärder:

* Minimera driftskostnaderna via CDN.
* Minska omkostnader CDN genom cachens effektivitet och komprimering.

> [!NOTE]
> Trafik volymen siffrorna representerar trafik som användes vid beräkning av förhållandet och procenttal och kan bara visa en del av den totala trafiken för högvolyms-kunder.
> 
> 

| Fält | Beskrivning |
| --- | --- |
| Ave byte ut |Anger det genomsnittliga antalet byte som överförs för varje begäran som hanteras från CDN (kant servrar) till beställare (t.ex. webbläsare). |
| Inga cachelagrade Config-Byte |Anger procentandelen av trafiken hanteras från CDN (kant servrar) till beställaren (t.ex. webbläsare) som inte cachelagras på grund av funktionen kringgå Cache. |
| Komprimerade Byte hastighet |Anger procentandelen av trafik som skickas från CDN (kant servrar) till beställare (t.ex. webbläsare) i komprimerat format. |
| Byte ut |Anger mängden data i byte som har levererats från CDN (kant servrar) till beställare (t.ex. webbläsare). |
| Byte i |Anger mängden data i byte som skickats från beställare (t.ex. webbläsare) till CDN (kant-servrar). |
| Byte fjärr |Anger mängden data i byte som skickats från CDN och kunden ursprung servrar till CDN (kant-servrar). |

#### <a name="performance-metrics"></a>Prestandamått
Syftet med de här måtten är att spåra CDN prestandan för trafiken.

| Fält | Beskrivning |
| --- | --- |
| Överföringshastighet |Visar genomsnittligt innehåll har överförts från Innehållsleverantörsnätverket till en beställare. |
| Varaktighet |Visar den genomsnittliga tiden, i millisekunder som det tog för att leverera en tillgång till en beställare (t.ex. webbläsare). |
| Komprimerade begärandehastighet |Anger procentandelen träffar som har levererats från CDN (kant servrar) till beställare (t.ex. webbläsare) i komprimerat format. |
| 4xx Felfrekvens |Anger procentandelen träffar som genererade en 4xx statuskod. |
| Frekvens för 5xx-fel |Anger procentandelen träffar som genererade en 5xx-statuskod. |
| Träffar |Anger antalet begäranden för CDN-innehåll. |

#### <a name="secure-traffic-metrics"></a>Skydda trafiken mått
Syftet med de här måtten är att spåra CDN prestanda för HTTPS-trafik.

| Fält | Beskrivning |
| --- | --- |
| Säker cachens effektivitet |Anger procentandelen av data som överförs för HTTPS-begäranden som har hanteras från cachen. Detta mått åtgärder när en cachelagrad version av det begärda innehållet behandlades direkt från CDN (kant servrar) till beställare (t.ex. webbläsare) via HTTPS. |
| Säker överföringshastighet |Visar genomsnittligt som överfördes innehåll från CDN (kant servrar) till beställare (till exempel webbservrar) via HTTPS. |
| Genomsnittlig varaktighet för säker |Visar den genomsnittliga tiden, i millisekunder som det tog för att leverera en tillgång till en beställare (t.ex. webbläsare) via HTTPS. |
| Säker träffar |Anger antalet HTTPS-begäranden för CDN-innehåll. |
| Säker byte ut |Anger mängden HTTPS-trafik, i byte som har levererats från CDN (kant servrar) till beställare (t.ex. webbläsare). |

## <a name="reports"></a>Rapporter
Varje rapport i den här modulen innehåller ett diagram och statistik över användning av nätverksbandbredd och trafik för olika typer av mått (t.ex. HTTP-statuskoder cache statuskoder begärande-URL, osv.). Den här informationen kan användas för att ger dig djupare in hur innehållet levereras till dina klienter och för att finjustera CDN beteende för att förbättra prestanda för leverans av data.

### <a name="accessing-the-edge-performance-reports"></a>Åtkomst till edge prestandarapporter
1. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och klicka sedan hovra över den **kant prestanda långsammare Analytics** utfällbar.  Klicka på **HTTP stort objekt**.
   
    Edge nod analytics rapporter skärmen visas.

| Rapport | Beskrivning |
| --- | --- |
| Daglig sammanfattning |Kan du visa trender för dagliga trafik över en angiven tidsperiod. Varje stapel i det här diagrammet motsvarar ett visst datum. Storleken på fältet anger det totala antalet träffar som inträffat på det datumet. |
| Sammanfattning av varje timme |Kan du visa varje timme trafik trender under en angiven tidsperiod. Varje stapel i det här diagrammet representerar en enskild timme på ett visst datum. Storleken på fältet anger det totala antalet träffar som uppstod under den timmen. |
| Protokoll |Visar fördelningen av trafiken mellan protokollen HTTP och HTTPS. Ett ringdiagram indikerar procentandelen träffar som inträffade för varje typ av protokoll. |
| HTTP-metoder |Kan du få en snabb översikt av för HTTP-metoder används för att begära dina data. Vanligtvis är de vanligaste HTTP-begäran metoderna GET, HEAD och POST. Ett ringdiagram indikerar procenttalet träffar som inträffat för varje typ av HTTP-begäran-metod. |
| URL: er |Innehåller ett diagram som visar de översta 10 begärda URL: er. Ett fält visas för varje URL. Höjden på fältet som anger hur många träffar som viss URL har genererat över tidsintervallet som omfattas av rapporten. Statistik för de 100 främsta begärda URL: er visas direkt under det här diagrammet. |
| Skapa CNAME-poster |Innehåller ett diagram som visar upp 10 CNAME-resursposter för att begära tillgångar över tiden span för en rapport. Statistik för de 100 främsta begärt skapa CNAME-poster visas direkt under det här diagrammet. |
| Ursprung |Innehåller ett diagram som visar de översta 10 CDN eller kund ursprung servrar som begärdes tillgångar under en angiven tidsperiod. Statistik för de 100 främsta begärt CDN eller kund ursprung servrar visas direkt under det här diagrammet. Kunden ursprung servrar identifieras med det namn som definierats i alternativet katalognamnet. |
| GEO-POP |Visar hur mycket av din trafik dirigeras via en viss punkt-för-förekomst (POP). Tre bokstäver representerar en POP i vårt CDN-nätverk. |
| Klienter |Innehåller ett diagram som visar de översta 10 klienter som begärt tillgångar under en angiven tidsperiod. Vid tillämpningen av den här rapporten anses alla begäranden som kommer från samma IP-adress vara från samma klient. Statistik för de översta 100 klienterna visas direkt under det här diagrammet. Den här rapporten är användbar för att fastställa download aktivitet mönster för top-klienter. |
| Cache-status |Ger en detaljerad analys av cachebeteende, kan du få metoder för att förbättra den övergripande slutanvändarens upplevelsen. Eftersom bästa prestanda kommer från cacheträffar, kan du optimera data leverans hastigheter genom att minimera cachemissar och cacheträffar på har upphört att gälla. |
| Ingen information |Innehåller ett diagram som visar översta 10 URL: er för tillgångar som cache innehåll dokumentens inte kontrollerades under en angiven tidsperiod. Statistik för övre 100 URL: er för dessa typer av tillgångar visas direkt under det här diagrammet. |
| CONFIG_NOCACHE information |Innehåller ett diagram som visar upp 10-URL: er för tillgångar som inte har cachelagrats på grund av kundens CDN konfiguration. Dessa typer av tillgångar har sett direkt från den ursprungliga servern. Statistik för övre 100 URL: er för dessa typer av tillgångar visas direkt under det här diagrammet. |
| UNCACHEABLE information |Innehåller ett diagram som visar översta 10 webbadresserna för tillgångar som inte kunde cachelagras på grund av data för begäran-huvud. Statistik för övre 100 URL: er för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_HIT information |Innehåller ett diagram som visar översta 10 URL: er för tillgångar som hanteras direkt från cachen. Statistik för övre 100 URL: er för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_MISS information |Innehåller ett diagram som visar översta 10 webbadresserna för tillgångar som har statusen TCP_MISS cache. Statistik för övre 100 URL: er för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_EXPIRED_HIT information |Innehåller ett diagram som visar översta 10 webbadresserna för inaktuella tillgångar som har sett direkt från POP. Statistik för övre 100 URL: er för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_EXPIRED_MISS information |Innehåller ett diagram som visar översta 10 URL: er för inaktuella tillgångar som hade en ny version som ska hämtas från den ursprungliga servern. Statistik för övre 100 URL: er för dessa typer av tillgångar visas direkt under det här diagrammet. |
| TCP_CLIENT_REFRESH_MISS information |Innehåller ett diagram som visar de översta 10 URL: er för tillgångar har hämtats från ett ursprungsservern på grund av ett no-cache-begäran från klienten. Statistik för övre 100 URL: er för dessa typer av begäranden visas direkt under det här diagrammet. |
| Klienttyper av begäranden |Anger vilken typ av förfrågningar som gjorts av HTTP-klienter (till exempel webbläsare). Den här rapporten innehåller en ringdiagram som ger en uppfattning om hur begäranden hanteras. Information om bandbredd och trafik för varje typ av begäran visas under diagrammet. |
| Användaragent |Innehåller ett stapeldiagram som visar de översta 10 användaragenter om du vill begära ditt innehåll genom vår CDN. En användaragent är vanligtvis en webbläsare, media player eller en mobiltelefon webbläsare. Statistik för övre 100 användaragenter visas direkt under det här diagrammet. |
| Referenter |Innehåller ett stapeldiagram som visar de 10 referenter till innehåll som nås via vårt CDN. Vanligtvis är en referent URL till webbsidan eller resurs som länkar till ditt innehåll. Detaljerad information finns nedan i diagrammet för de 100 främsta hänvisare. |
| Komprimeringstyper |Innehåller ett ringdiagram uppdelad begärda tillgångar av om de komprimerats med våra edge-servrar. Procentandelen komprimerade tillgångar är fördelade på vilken typ av komprimering som används. Detaljerad information finns nedan i diagrammet för varje typ av komprimering och status. |
| Filtyper |Innehåller ett stapeldiagram som visar de översta 10 filtyper som har begärts via vårt CDN för ditt konto. Vid tillämpningen av den här rapporten en filtyp definieras av tillgångens filnamnstillägg och medietyp för Internet (exempelvis .html \[text/html\], .htm \[text/html\], .aspx \[text/html\]osv.). Detaljerad information finns nedan i diagrammet för övre 100 filtyper. |
| Unika filer |Innehåller ett diagram som visar det totala antalet unika tillgångar som begärdes under en viss dag under en angiven tidsperiod. |
| Token autentisering-sammanfattning |Innehåller ett cirkeldiagram som ger en snabb överblick på om begärda tillgångar skyddades av Token-baserad autentisering. Skyddade tillgångar visas i diagrammet enligt resultatet av sin försök autentisering. |
| Token Auth neka information |Innehåller ett stapeldiagram som gör att du kan visa översta 10 förfrågningar som har nekats på grund av Token-baserad autentisering. |
| HTTP-svarskoder |Visar en uppdelning av HTTP-statuskoder (t.ex. 200 OK 403 tillåts inte, det gick inte att hitta 404, etc.) som har levererats till HTTP-klienter med våra edge-servrar. Ett cirkeldiagram kan du snabbt bedöma hur dina tillgångar har sett. Detaljerad statistiska data anges för varje svarskoden under diagrammet. |
| 404-fel |Innehåller ett stapeldiagram som gör att du kan visa de översta 10 begäranden som resulterade i en 404 gick inte att hitta svarskod. |
| 403-fel |Innehåller ett stapeldiagram som gör att du kan visa de översta 10 begäranden som resulterade i en 403 förbjuden svarskod. En 403 förbjuden svarskod inträffar när en begäran nekades av en kund ursprungsserver eller en gränsserver på vår POP. |
| 4xx fel |Innehåller ett stapeldiagram som gör att du kan visa de översta 10 begäranden som resulterade i en svarskod i 400 intervallet. Undantas från den här rapporten är 403 inte hittas och svarskoder 404 förbjuden. En svarskod 4xx inträffar vanligtvis när en begäran nekas på grund av ett klientfel. |
| 504 fel |Innehåller ett stapeldiagram som gör att du kan visa de översta 10 begäranden som resulterade i en 504 Gateway-Timeout-svarskod. En 504 Gateway-Timeout-svarskod inträffar när en timeout inträffar när en HTTP-proxy försöker kommunicera med en annan server. När det gäller våra CDN normalt en 504 Gateway-Timeout-svarskod när en gränsserver inte går att upprätta kommunikation med en kund ursprungsservern. |
| 502 fel |Innehåller ett stapeldiagram som gör att du kan visa de översta 10 begäranden som resulterade i en 502 felaktig Gateway-svarskod. En 502 felaktig Gateway-svarskod inträffar när ett HTTP-protokollfel inträffar mellan en server och en HTTP-proxy. När det gäller våra CDN normalt en 502 felaktig Gateway-svarskod när en kund ursprungsservern returnerar ett ogiltigt svar på en gränsserver. Ett svar är ogiltigt om det inte går att parsa eller om den är ofullständig. |
| 5xx-fel |Innehåller ett stapeldiagram som gör att du kan visa de översta 10 begäranden som resulterade i en svarskod inom intervallet 500.  Undantas från den här rapporten är 502 felaktig Gateway och svarskoder 504 Gateway-Timeout. |

## <a name="see-also"></a>Se även
* [Azure CDN-översikt](cdn-overview.md)
* [Realtid statistik i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Åsidosätta standardbeteendet i HTTP-motorn regler](cdn-rules-engine.md)
* [Avancerade http-rapporter](cdn-advanced-http-reports.md)

