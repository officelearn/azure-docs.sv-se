---
title: Analysera användningsstatistik med Azure CDN avancerade HTTP-rapporter | Microsoft Docs
description: Lär dig mer om att skapa avancerade HTTP-rapporter i Microsoft Azure CDN. De här rapporterna ger detaljerad information om CDN-aktivitet.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c8cb4713e38ca0da610c687325f3810f57da2b26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216159"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analysera användningsstatistik med Azure CDN avancerade HTTP-rapporter
## <a name="overview"></a>Översikt
Det här dokumentet beskriver avancerade http-rapportering i Microsoft Azure CDN. De här rapporterna ger detaljerad information om CDN-aktivitet.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Åtkomst till avancerade HTTP-rapporter
1. Från bladet för CDN-profil klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och sedan hovra över den **avancerade HTTP-rapporter** utfällbar meny.  Klicka på **HTTP stora plattform**.
   
    ![CDN-hanteringsportalen - menyn för avancerade rapporter](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Rapportalternativ visas.

## <a name="geography-reports-map-based"></a>Geografi rapporter (Kartbaserade)
Det finns fem rapporter som utnyttjar en karta som visar de regioner där ditt innehåll har begärts. Dessa rapporter är världskarta, kartan i USA, Kanada kartan, Europa kartan och Asien/Stillahavsområdet kartan.

Varje kartbaserade rapport rangordnar geografiska enheter (d.v.s. länder/regioner, tillstånd och provinser) enligt procentandelen träffar som har sitt ursprung från den regionen. Dessutom ges en karta för att visualisera de platser där ditt innehåll har begärts. Det kan göra det genom att färgkodning varje region enligt mängden begäran erfarna i den regionen. Lättare skuggade regioner anger lägre efterfrågan på ditt innehåll, medan mörkare regioner indikerar högre nivåer av efterfrågan på ditt innehåll.

Detaljerad information om trafik och bandbreddsanvändning för varje region tillhandahålls direkt nedanför kartan. Detta kan du visa det totala antalet träffar, procent av träffar, den totala mängden data överförs (i GB) och procent data överförs för varje region. Visa en beskrivning för var och en av de här måtten. Slutligen, när du hovrar över en region (t.ex, land, delstat eller provins) namnet och procentandelen träffar som inträffat i regionen kommer att visas som en knappbeskrivning.

En kort beskrivning finns nedan för varje typ av kartbaserade geografi rapporten.

| Rapportnamn | Beskrivning |
| --- | --- |
| Världskarta |Den här rapporten kan du visa världen över behovet av dina CDN-innehåll. Varje land/region är färgkodade på världskartan för att ange hur stor procentandel av träffar som kommer från den regionen. |
| Förenta staternas karta |Den här rapporten kan du visa behovet av dina CDN-innehåll i USA. Varje tillstånd är färgkodade på den här kartan för att visa procentandelen träffar som kommer från den regionen. |
| Kanada karta |Den här rapporten kan du visa behovet av dina CDN-innehåll i Kanada. Varje region har en annan färg på den här kartan för att visa procentandelen träffar som kommer från den regionen. |
| Europa karta |Den här rapporten kan du visa behovet av dina CDN-innehåll i Europa. Varje land/region har en annan färg på den här kartan för att visa procentandelen träffar som kommer från den regionen. |
| Sydöstra Stillahavsområdet karta |Den här rapporten kan du visa behovet av dina CDN-innehåll i Asien. Varje land/region har en annan färg på den här kartan för att visa procentandelen träffar som kommer från den regionen. |

## <a name="geography-reports-bar-charts"></a>Geografi rapporter (cirkeldiagram)
Det finns två ytterligare rapporter som ger statistisk information enligt geografisk plats, som är upp städer och Top länder. De här rapporterna rangordnas städer och länder/regioner, beroende på antal träffar som kommer från dessa regioner. Vid generera den här typen av rapporten, visar ett liggande diagram de översta 10 populäraste städerna sett eller länder som har begärt innehåll över en viss plattform. Den här stapeldiagrammet kan du snabbt utvärdera de regioner som genererar det högsta antalet begäranden för ditt innehåll.

Till vänster i diagrammet (y-axeln) anger hur många träffar inträffade för den angivna regionen. Direkt under diagrammet (x-axeln) hittar du en etikett för var och en av de översta 10 regionerna.

### <a name="using-the-bar-charts"></a>Med hjälp av liggande diagram
* Om du hovrar över en stapel visas namnet och det totala antalet träffar som inträffat i regionen som en knappbeskrivning.
* Knappbeskrivning för Top städer rapporten identifierar en stad efter dess namn, region och landsförkortning.
* Om den stad eller region (t.ex, stat/provins) som en begäran kommer från inte kunde fastställas, att det indikera att de är okänd. Om landet är okänd och två frågetecken (d.v.s.??), visas.
* En rapport kan innehålla mått för ”Europa” eller ”Asien/Stillahavsområdet Region”. Dessa objekt är inte avsedda att ge statistisk information om alla IP-adresser i dessa regioner. I stället avser de endast begäranden som kommer från IP-adresser som är spridda över Europa eller Asien/Stillahavsområdet i stället för till en viss ort eller land.

De data som användes för att generera stapeldiagrammet kan visas under den. Där finns det totala antalet träffar, procent av träffar, mängden data som överförs (i GB) och procent data överförs för top 250-regioner. Visa en beskrivning för var och en av de här måtten.

En kort beskrivning tillhandahålls för båda typer av rapporter som nedan.

| Rapportnamn | Beskrivning |
| --- | --- |
| Översta städer |Den här rapporten rangordnar städer beroende på antal träffar som kommer från den regionen. |
| Främsta länder/regioner |Den här rapporten rangordnar länder beroende på antal träffar som kommer från den regionen. |

## <a name="daily-summary"></a>Daglig sammanfattning
Daglig sammanfattning i rapporten kan du visa det totala antalet träffar och data som överförs via en viss plattform per dag. Den här informationen kan användas för att snabbt avläsa CDN aktivitet mönster. Exempelvis kan den här rapporten kan hjälpa dig att identifiera vilka dagar erfarna högre eller lägre än förväntad trafik.

Vid generera den här typen av rapporten, ger ett liggande diagram en visuell indikering om mängden plattformsspecifika begäran erfarna per dag under den tidsperioden som omfattas av rapporten. Det gör det genom att visa en stapel för varje dag i rapporten. Till exempel att välja hur lång tid med namnet ”förra veckan” skapar ett stapeldiagram med sju staplar. Varje meddelandefältet visar det totala antalet träffar erfarna den dagen.

Till vänster i diagrammet (y-axeln) anger hur många träffar uppstod på det angivna datumet. Direkt under diagrammet (x-axeln), hittar du en etikett som anger det datum då (Format: YYYY-MM-DD) för varje dag med i rapporten.

> [!TIP]
> Om du hovrar över en stapel visas det totala antalet träffar som inträffat på samma datum som en knappbeskrivning.
> 
> 

De data som användes för att generera stapeldiagrammet kan visas under den. Där finns det totala antalet träffar och mängden data som överförs (i GB) för varje dag som omfattas av rapporten.

## <a name="by-hour"></a>Per timme
Rapporten för timme kan du visa det totala antalet träffar och data som överförs via en viss plattform per timme. Den här informationen kan användas för att snabbt avläsa CDN aktivitet mönster. Exempelvis kan den här rapporten kan hjälpa dig att identifiera tidsperioder under dagen som högre eller lägre än förväntad trafik.

Vid generera den här typen av rapporten, ger ett liggande diagram en visuell indikering om mängden plattformsspecifika begäran som uppstår vid timme under den tidsperioden som omfattas av rapporten. Det gör det genom att visa en stapel för varje timme som omfattas av rapporten. Till exempel genererar att välja en 24-timmars tidsperiod ett stapeldiagram med 24 staplar. Varje meddelandefältet visar det totala antalet träffar erfarna under den timmen.

Till vänster i diagrammet (y-axeln) anger hur många träffar uppstod på den angivna timmen. Direkt under diagrammet (x-axeln), hittar du en etikett som anger datum och tid (Format: YYYY-MM-DD hh: mm) för varje timme som ingår i rapporten. Tid rapporteras med 24-timmarsformat och det har angetts med hjälp av UTC/GMT-tidszonen.

> [!TIP]
> Om du hovrar över en stapel visas det totala antalet träffar som uppstod under den timmen som en knappbeskrivning.
> 
> 

De data som användes för att generera stapeldiagrammet kan visas under den. Där finns det totala antalet träffar och mängden data som överförs (i GB) för varje timme som omfattas av rapporten.

## <a name="by-file"></a>Efter fil
Rapporten genom att filen kan du visa mängden begäran och trafik som tillkommer under en viss plattform för de mest efterfrågade tillgångarna. Ett stapeldiagram kommer att skapas på de översta 10 mest efterfrågade tillgångarna på generera den här typen av rapporten, under den angivna tidsperioden.

> [!NOTE]
> För den här rapporten konverteras edge CNAME webbadresser till deras motsvarande CDN-URL: er. På så sätt kan en korrekt tally för det totala antalet träffar som är associerade med en tillgång, oberoende CDN eller edge CNAME-URL som används för att begära den.
> 
> 

Till vänster i diagrammet (y-axeln) anger hur många begäranden för varje plats under den angivna tidsperioden. Direkt under diagrammet (x-axeln) hittar du en etikett som anger filnamnet för var och en av de översta 10 begärda tillgångarna.

De data som användes för att generera stapeldiagrammet kan visas under den. Där finns följande information för var och en av de främsta 250 begärda tillgångarna: relativ sökväg, det totala antalet träffar, procent av träffar, mängden data som överförs (i GB) och procent data överförs.

## <a name="by-file-detail"></a>Av information i loggfilen
Information i loggfilen av rapporten kan du visa mängden begäran och trafik som tillkommer under en viss plattform för en specifik tillgång. Högst upp i den här rapporten är alternativet filen information för. Det här alternativet innehåller en lista över dina mest efterfrågade tillgångar på den valda plattformen. För att generera en rapport med information i loggfilen måste du välja önskad tillgången från alternativet filen information för. Då och ett stapeldiagram visar mängden daglig efterfrågan som skapades under den angivna tidsperioden.

Till vänster i diagrammet (y-axeln) anger det totala antalet begäranden om att en tillgång som uppstår vid en viss dag. Direkt under diagrammet (x-axeln), hittar du en etikett som anger det datum då (Format: YYYY-MM-DD) för vilka CDN behovet av tillgången har rapporterats.

De data som användes för att generera stapeldiagrammet kan visas under den. Där finns det totala antalet träffar och mängden data som överförs (i GB) för varje dag som omfattas av rapporten.

## <a name="by-file-type"></a>Efter filtyp
Filtyp rapporten kan du visa mängden begäran och trafiken åsamkar filtyp. Vid generera den här typen av rapporten, visar ett ringdiagram procentandelen av träffar som genereras av de översta 10 filtyperna.

> [!TIP]
> Om du hovrar över en sektor i ringdiagrammet skriver Internet-media för att filtypen kommer att visas som en knappbeskrivning.
> 
> 

De data som användes för att generera ringdiagrammet kan visas under den. Där finns medietyp för filen namnet tillägget/Internet, totalt antal träffar, procent av träffar, mängden data som överförs (i GB) och procent data överförs för var och en av de översta 250 filtyperna.

## <a name="by-directory"></a>Av Directory
Med Directory rapporten kan du visa mängden begäran och trafik som tillkommer under en viss plattform för innehåll från en viss katalog. Vid generera den här typen av rapporten, visar ett liggande diagram det totala antalet träffar som genererats av innehållet i de översta 10 katalogerna.

### <a name="using-the-bar-chart"></a>Med hjälp av stapeldiagrammet
* Hovra över ett fält att visa den relativa sökvägen till motsvarande katalog.
* Innehåll som lagras i en undermapp till en katalog räknas inte vid beräkning av begäran av directory. Den här beräkningen endast förlitar sig på antalet begäranden som genererats för innehåll som lagras i katalogen faktiska.
* För den här rapporten konverteras edge CNAME webbadresser till deras motsvarande CDN-URL: er. På så sätt kan en korrekt tally för all statistik som är associerade med en tillgång, oberoende CDN eller edge CNAME-URL som används för att begära den.

Till vänster i diagrammet (y-axeln) anger det totala antalet begäranden om innehåll som lagras i översta 10-kataloger. Varje stapel i diagrammet representerar en katalog. Använd color-coding schemat för att matcha ett fält i en katalog som anges i avsnittet upp 250 fullständig kataloger.

De data som användes för att generera stapeldiagrammet kan visas under den. Där finns följande information för var och en av de översta 250 katalogerna: relativ sökväg, det totala antalet träffar, procent av träffar, mängden data som överförs (i GB) och procent data överförs.

## <a name="by-browser"></a>I webbläsare
Rapporten genom att webbläsaren kan du visa vilka webbläsare som användes för att begära innehåll. Vid generera den här typen av rapporten, visar ett cirkeldiagram procentandelen förfrågningar som hanteras av de översta 10 webbläsarna.

### <a name="using-the-pie-chart"></a>Med hjälp av cirkeldiagrammet
* Hovra över en sektor i cirkeldiagrammet för att visa namn och version för en webbläsare.
* För den här rapporten betraktas varje unik webbläsarversion kombination som en annan webbläsare.
* Den sektor som kallas ”andra” anger procentandelen förfrågningar som hanteras av alla andra webbläsare och versioner.

De data som användes för att generera cirkeldiagrammet kan visas under den. Där finns webbläsaren/versionsnummer, totalt antal träffar och procentandelen träffar för var och en av de översta 250 webbläsarna.

## <a name="by-referrer"></a>Genom att referent
Av referent-rapporten kan du visa referenter till innehåll på den valda plattformen. En referent anger värdnamnet som en begäran skapades. Vid generera den här typen av rapporten, visar ett liggande diagram mängden begäran (d.v.s. träffar) som genereras av de översta 10 referenter.

Till vänster i diagrammet (y-axeln) anger det totala antalet begäranden om att en tillgång inträffade för varje referent. Varje stapel i diagrammet representerar en referent. Använd color-coding schemat för att matcha ett fält till en referent som anges i avsnittet upp 250 referent.

De data som användes för att generera stapeldiagrammet kan visas under den. Där finns URL: en och det totala antalet träffar procentandelen träffar som genereras från var och en av de främsta 250 referenter.

## <a name="by-download"></a>Genom att ladda ner
Genom att ladda ned rapporten kan du analysera download mönster för dina mest efterfrågade innehåll. Överst i rapporten innehåller ett stapeldiagram som jämför försökte nedladdningar med slutförda hämtningar för de översta 10 begärda tillgångarna. Varje fält är färgkodade enligt om det är ett försök har gjorts download (blå) eller en slutförd nedladdning (grön).

> [!NOTE]
> För den här rapporten konverteras edge CNAME webbadresser till deras motsvarande CDN-URL: er. På så sätt kan en korrekt tally för all statistik som är associerade med en tillgång, oberoende CDN eller edge CNAME-URL som används för att begära den.
> 
> 

Till vänster i diagrammet (y-axeln) anger namnet på filen för var och en av de översta 10 begärda resurser. Direkt under diagrammet (x-axeln) hittar du etiketter som anger det totala antalet försök/fyllas nedladdningar.

Direkt under stapeldiagram, följande information visas för de övre 250 begärda tillgångarna: relativa sökvägen (inklusive filnamnet), hur många gånger som det har laddats ned till slutförande, hur många gånger som den begärdes och procentandelen begäranden som resulterade i en fullständig nedladdning.

> [!TIP]
> Vår CDN inte informeras av en HTTP-klient (d.v.s. webbläsare) när en tillgång helt har hämtats. Därför kan vi behöva beräkna om en tillgång har laddats ned helt enligt statuskoder och byte-intervall begäranden. Det första vi letar när du gör den här beräkningen är om begäran resulterar i en 200 OK statuskod. I så, fall sedan titta vi på byte-intervall begäranden så att de täcker hela tillgången. Slutligen kan jämför vi mängden data som överförs till storleken på den begärda tillgången. Om överförda data är lika med eller större än filstorleken och begäranden byte-intervall som är lämpliga för tillgången, räknas höjdpunkt som en fullständig nedladdning.
> 
> På grund av interpretive typen av den här rapporten bör du tänka på följande punkter som får ändra konsekvens och korrektheten i den här rapporten.
> 
> * Trafikmönster kan inte förutsägas när användaragenter beter sig annorlunda. Detta kan ge hämtning slutförd resultat som är större än 100%.
> * Tillgångar som drar nytta av HTTP progressiv nedladdning är kanske inte korrekt representeras av den här rapporten. Detta beror på användare som vill ha till andra platser på en video.
> 
> 

## <a name="by-404-errors"></a>Genom att 404-fel
Rapporten genom att 404-fel kan du identifiera typ av innehåll som genererar flest antal statuskoder för 404 hittades inte. Överst i rapporten innehåller ett stapeldiagram för de översta 10 tillgångar som returnerades statuskod 404 hittades inte. Den här stapeldiagrammet jämförs det totala antalet begäranden med begäranden som resulterade i en 404 det gick inte att hitta statuskod för dessa resurser efterlevs. Varje fält är färgkodade. Ett gult fält används för att indikera att förfrågan resulterade i statuskod 404 hittades inte. En röd stapel används för att ange det totala antalet begäranden för tillgången.

> [!NOTE]
> För den här rapporten, Tänk på följande:
> 
> * En träff representerar begäranden för en tillgång, oberoende statuskod.
> * URL: er för Edge CNAME konverteras till deras motsvarande CDN-URL: er. På så sätt kan en korrekt tally för all statistik som är associerade med en tillgång, oberoende CDN eller edge CNAME-URL som används för att begära den.
> 
> 

Till vänster i diagrammet (y-axeln) anger filnamnet för var och en av de översta 10 begärda tillgångar som resulterade i statuskod 404 hittades inte. Du kommer märka etiketter som anger det totala antalet begäranden och antalet begäranden som resulterade i statuskod 404 hittades inte direkt under graph (x-axeln).

Direkt under stapeldiagram, följande information visas för de övre 250 begärda tillgångarna: relativa sökvägen (inklusive filnamnet), antalet begäranden som resulterade i en 404 det gick inte att hitta statuskod, det totala antalet gånger som tillgången begärdes och procentandelen av förfrågningarna som resulterade i statuskod 404 hittades inte.

## <a name="see-also"></a>Se också
* [Översikt över Azure CDN](cdn-overview.md)
* [Realtidsstatistik i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Åsidosätta standardbeteendet för HTTP med regelmotorn](cdn-rules-engine.md)
* [Analysera Gränsprestanda](cdn-edge-performance.md)

