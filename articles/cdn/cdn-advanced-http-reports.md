---
title: "Analysera användningsstatistik med Azure CDN avancerade HTTP rapporter | Microsoft Docs"
description: "Lär dig mer om att skapa avancerade HTTP-rapporter i Microsoft Azure CDN. De här rapporterna ger detaljerad information om CDN-aktivitet."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 2dfbc046674b2da692f30c945aee3ea25ae524eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analysera användningsstatistik med Azure CDN avancerade http-rapporter
## <a name="overview"></a>Översikt
Det här dokumentet beskrivs avancerade http-rapportering i Microsoft Azure CDN. De här rapporterna ger detaljerad information om CDN-aktivitet.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Åtkomst till avancerade HTTP-rapporter
1. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över den **Analytics** och klicka sedan hovra över den **avancerade HTTP rapporter** utfällbar.  Klicka på **HTTP stora plattform**.
   
    ![CDN-hanteringsportalen - avancerade rapporter-menyn](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Rapportalternativ visas.

## <a name="geography-reports-map-based"></a>Geografi rapporter (karta-baserat)
Det finns fem rapporter som utnyttjar en kartan för att visa de regioner där ditt innehåll som begärs. Dessa rapporter är World karta, USA karta, Kanada karta, Europa kartan och karta för Asien/Stillahavsområdet.

Varje karta-baserad rapport rangordningar geografiska enheter (d.v.s. länder, tillstånd och regioner) enligt procentandelen träffar som kommer från den regionen. Dessutom tillhandahålls en karta som hjälper dig att visualisera de platser där ditt innehåll som begärs. Det kan göra det med färgkodade varje region enligt mängden begäran erfarna i den regionen. Lättare skuggad regioner ange lägre behovet av innehåll, medan mörka områdena anger högre nivåer av behovet av ditt innehåll.

Detaljerad information om trafik och bandbredden för varje region tillhandahålls direkt under kartan. Detta kan du visa det totala antalet träffar, procentandelen träffar, den totala mängden data överförs (i GB) och procentandelen data överförs för varje region. Visa en beskrivning för var och en av de här måtten. Slutligen, när du hovrar över en region (d.v.s. land, delstat eller provins) namnet och procentandelen träffar som uppstått i regionen visas som en knappbeskrivning.

En kort beskrivning finns nedan för varje typ av geografisk karta-baserade rapporten.

| Rapportnamn | Beskrivning |
| --- | --- |
| World karta |Den här rapporten kan du visa hela världen behovet av CDN-innehåll. Varje land är färgkodade på world kartan för att ange procentandelen träffar som kommer från den regionen. |
| USA: S karta |Den här rapporten kan du visa begäran för CDN-innehåll i USA. Varje tillstånd är färgkodade på den här kartan för att visa procentandelen träffar som kommer från den regionen. |
| Kanada karta |Den här rapporten kan du visa begäran för CDN-innehåll i Kanada. Varje region är färgkodade på den här kartan för att visa procentandelen träffar som kommer från den regionen. |
| Europa karta |Den här rapporten kan du visa begäran för CDN-innehåll i Europa. Varje land färgkodade på den här kartan för att visa procentandelen träffar som kommer från den regionen. |
| Asien Pacific karta |Den här rapporten kan du visa begäran för CDN-innehåll i Asien. Varje land färgkodade på den här kartan för att visa procentandelen träffar som kommer från den regionen. |

## <a name="geography-reports-bar-charts"></a>Geografi rapporter (cirkeldiagram)
Det finns två ytterligare rapporter som ger statistisk information enligt geografisk plats, som är upp orter och upp länder. De här rapporterna rangordnas städer och länder, respektive enligt antalet träffar som kommer från dessa regioner. Vid genererar den här typen av rapporten, visar ett stapeldiagram om de översta 10 städer eller länder som begärt innehåll över en viss plattform. Den här stapeldiagram kan du snabbt bedöma regioner som genererar det högsta antalet begäranden om ditt innehåll.

Den vänstra sidan av diagrammet (y-axeln) anger hur många träffar som uppstått i den angivna regionen. Direkt under diagrammet (x-axeln) hittar du en etikett för var och en av de översta 10 regionerna.

### <a name="using-the-bar-charts"></a>Med hjälp av liggande diagram
* Om du hovrar över ett fält visas namnet och det totala antalet träffar som uppstått i regionen som en knappbeskrivning.
* Knappbeskrivning för rapporten upp orter identifierar en stad med dess namn, region och landsförkortning.
* Om den stad eller region (d.v.s. region) som en begäran kommer från inte kunde fastställas att det indikera att de är okänd. Om landet är okänd, två frågetecken (d.v.s.?), visas.
* En rapport kan innehålla mått för ”Europa” eller ”Asien/Stillahavsområdet Region”. Dessa objekt är inte avsedda att ge statistisk information om alla IP-adresser i dessa regioner. I stället gäller de endast begäranden som kommer från IP-adresser som är utspridda över Europa eller Asien i stället för till en viss ort eller land.

De data som användes för att generera stapeldiagrammet kan visas under den. Det finns det totala antalet träffar, procentandelen träffar, mängden data som överförs (i GB) och procentandelen data överförs för top 250 regioner. Visa en beskrivning för var och en av de här måtten.

En kort beskrivning anges för båda typerna av rapporter nedan.

| Rapportnamn | Beskrivning |
| --- | --- |
| TOP orter |Den här rapporten rangordnar orter enligt antalet träffar som kommer från den regionen. |
| Övre länder |Den här rapporten rangordnar länder enligt antalet träffar som kommer från den regionen. |

## <a name="daily-summary"></a>Daglig sammanfattning
Dagliga sammanfattningsrapporten kan du visa det totala antalet träffar och data som överförs via en viss plattform dagligen. Den här informationen kan användas för att snabbt fram CDN aktivitet mönster. Till exempel hjälper den här rapporten dig att identifiera vilka dagar erfarna högre eller lägre än förväntad trafik.

Vid genererar den här typen av rapporten, ger ett liggande diagram en indikering om mängden plattformsspecifika begäran erfarna dagligen under den period som omfattas av rapporten. Det gör det genom att visa en stapel för varje dag i rapporten. Till exempel välja tidsperioden kallas ”förra veckan” genererar ett stapeldiagram med sju staplar. Varje visar det totala antalet träffar erfarna den dagen.

Den vänstra sidan av diagrammet (y-axeln) anger hur många träffar inträffade på det angivna datumet. Direkt under diagrammet (x-axeln) hittar du en etikett som anger datumet (Format: ÅÅÅÅ-MM-DD) för varje dag med i rapporten.

> [!TIP]
> Om du hovrar över ett fält visas det totala antalet träffar som inträffade vid denna tidpunkt som en knappbeskrivning.
> 
> 

De data som användes för att generera stapeldiagrammet kan visas under den. Finns det totala antalet träffar och mängden data som överförs (i GB) för varje dag som omfattas av rapporten.

## <a name="by-hour"></a>Per timme
För timme rapporten kan du visa det totala antalet träffar och data som överförs via en viss plattform timme. Den här informationen kan användas för att snabbt fram CDN aktivitet mönster. Till exempel hjälper den här rapporten dig identifiera tidsperioder under dagen som uppstår högre eller lägre än förväntad trafik.

Vid genererar den här typen av rapporten, ger ett liggande diagram en indikering om mängden plattformsspecifika begäran orsakade timme under den period som omfattas av rapporten. Det gör det genom att visa en stapel för varje timme som omfattas av rapporten. Till exempel genererar att välja en 24-timmarsformat tidsperiod ett stapeldiagram med 24 staplar. Varje visar det totala antalet träffar erfarna under den timmen.

Den vänstra sidan av diagrammet (y-axeln) anger hur många träffar inträffade på angiven timme. Direkt under diagrammet (x-axeln) hittar du en etikett som anger tidsvärdet (Format: ÅÅÅÅ-MM-DD hh: mm) för varje timme med i rapporten. Tid rapporteras med 24-timmarsformat och anges med hjälp av UTC/GMT-tidszonen.

> [!TIP]
> Om du hovrar över ett fält visas det totala antalet träffar som uppstod under den timmen som en knappbeskrivning.
> 
> 

De data som användes för att generera stapeldiagrammet kan visas under den. Finns det totala antalet träffar och mängden data som överförs (i GB) för varje timme som omfattas av rapporten.

## <a name="by-file"></a>Fil
Rapporten av filen kan du visa mängden begäran och trafik som uppstår under en viss plattform för de mest efterfrågade tillgångarna. Ett stapeldiagram kommer att skapas på de översta 10 mest efterfrågade tillgångarna vid genererar den här typen av rapporten för den angivna tidsperioden.

> [!NOTE]
> Vid tillämpningen av den här rapporten konverteras edge CNAME URL: er till sina motsvarande CDN-URL: er. På så sätt kan en korrekt tally för det totala antalet träffar som är associerade med en tillgång oavsett CDN eller edge CNAME-URL som används för att begära den.
> 
> 

Den vänstra sidan av diagrammet (y-axeln) anger antalet begäranden för varje resurs för den angivna tidsperioden. Direkt under diagrammet (x-axeln) hittar du en etikett som anger filnamnet för var och en av de översta 10 begärda tillgångarna.

De data som användes för att generera stapeldiagrammet kan visas under den. Innehåller följande information för var och en av de översta 250 begärda tillgångarna: relativ sökväg, det totala antalet träffar, procentandelen träffar, mängden data som överförs (i GB) och procentandelen data överförs.

## <a name="by-file-detail"></a>Efter fil-information
Av filen detaljerad rapport kan du visa mängden begäran och trafik som uppstår under en viss plattform för en viss resurs. Högst upp i den här rapporten är alternativet filen information för. Det här alternativet innehåller en lista över dina mest efterfrågade tillgångar på den valda plattformen. För att generera en rapport med information behöver du välja önskad tillgången från filen information för alternativet. När du har som ett stapeldiagram visar mängden daglig efterfrågan som skapades under den angivna tidsperioden.

Den vänstra sidan av diagrammet (y-axeln) anger det totala antalet begäranden om att det uppstått en tillgång under en viss dag. Direkt under diagrammet (x-axeln) hittar du en etikett som anger datumet (Format: ÅÅÅÅ-MM-DD) för vilka CDN rapporterades begäran för tillgången.

De data som användes för att generera stapeldiagrammet kan visas under den. Finns det totala antalet träffar och mängden data som överförs (i GB) för varje dag som omfattas av rapporten.

## <a name="by-file-type"></a>Efter filtyp
Filtyp rapporten kan du visa mängden begäran och trafiken för filtypen. Vid genererar den här typen av rapporten, visar ett ringdiagram procentandelen träffar som genererats av de översta 10 filtyperna.

> [!TIP]
> Om du hovrar över en sektor i ringdiagram typen Internet-media för att filtypen ska visas som en knappbeskrivning.
> 
> 

De data som användes för att generera ringdiagram kan visas under den. Innehåller namnet tillägg/Internet media filtypen, det totala antalet träffar, procentandelen träffar, mängden data som överförs (i GB) och procentandelen data överförs för var och en av de översta 250 filtyperna.

## <a name="by-directory"></a>Med Directory
Med Directory rapporten kan du visa mängden begäran och trafik som uppstår under en viss plattform för innehåll från en specifik katalog. Vid genererar den här typen av rapporten, visar ett stapeldiagram det totala antalet träffar som genererats av innehållet i de översta 10 katalogerna.

### <a name="using-the-bar-chart"></a>Med hjälp av liggande stapeldiagram
* Hovra över ett fält att visa den relativa sökvägen till motsvarande katalog.
* Innehåll som lagras i en undermapp i en katalog räknas inte vid beräkning av begäran av katalogen. Den här beräkningen endast förlitar sig på antalet begäranden som har genererats för innehåll som lagras i katalogen faktiska.
* Vid tillämpningen av den här rapporten konverteras edge CNAME URL: er till sina motsvarande CDN-URL: er. På så sätt kan en korrekt tally för all statistik som är associerade med en tillgång oavsett CDN eller edge CNAME-URL som används för att begära den.

Den vänstra sidan av diagrammet (y-axeln) anger det totala antalet förfrågningar om innehåll som lagras i topp 10-kataloger. Varje stapel på diagrammet representerar en katalog. Använda color-coding schemat måste matcha ett fält till en katalog som anges i avsnittet upp 250 fullständig kataloger.

De data som användes för att generera stapeldiagrammet kan visas under den. Innehåller följande information för var och en av de översta 250 katalogerna: relativ sökväg, det totala antalet träffar, procentandelen träffar, mängden data som överförs (i GB) och procentandelen data överförs.

## <a name="by-browser"></a>Webbläsare
Rapporten av webbläsare kan du visa vilka webbläsare som användes för att begära innehåll. Vid genererar den här typen av rapporten, visar ett cirkeldiagram procentandelen förfrågningar som hanteras av de översta 10 webbläsarna.

### <a name="using-the-pie-chart"></a>Med hjälp av cirkeldiagrammet
* Hovra över en sektor i cirkeldiagrammet för att visa en webbläsare namn och version.
* Vid tillämpningen av den här rapporten anses varje unik webbläsarversion kombination vara en annan webbläsare.
* Sektorn kallas ”andra” anger procentandelen förfrågningar som hanteras av andra webbläsare och versioner.

De data som användes för att generera cirkeldiagrammet kan visas under den. Finns det webbläsare/versionsnummer, det totala antalet träffar och procentandelen träffar för var och en av de översta 250 webbläsarna.

## <a name="by-referrer"></a>Av referent
Referent av rapporten kan du visa referenter till innehåll på den valda plattformen. En referent anger värdnamnet som en begäran har genererats. Vid genererar den här typen av rapporten, visar ett stapeldiagram mängden begäran (d.v.s. träffar) som genereras av referenter för 10.

Den vänstra sidan av diagrammet (y-axeln) anger det totala antalet begäranden om att en tillgång inträffade för varje referent. Varje stapel på diagrammet representerar en referent. Använda color-coding schemat måste matcha ett fält till en referent i avsnittet upp 250 referent.

De data som användes för att generera stapeldiagrammet kan visas under den. Du hittar det URL-Adressen, det totala antalet träffar och procentandelen träffar som genereras från var och en av de främsta 250 hänvisare.

## <a name="by-download"></a>Hämtning
Genom att hämta rapporten kan du analysera download mönster för dina mest efterfrågade innehåll. Överst i rapporten innehåller ett stapeldiagram som jämför försökte hämtningar med slutförda hämtningar för de översta 10 begärda tillgångarna. Varje fält är färgkodade enligt om det är en försök nedladdning (blå) eller en slutförd hämtning (grön).

> [!NOTE]
> Vid tillämpningen av den här rapporten konverteras edge CNAME URL: er till sina motsvarande CDN-URL: er. På så sätt kan en korrekt tally för all statistik som är associerade med en tillgång oavsett CDN eller edge CNAME-URL som används för att begära den.
> 
> 

Den vänstra sidan av diagrammet (y-axeln) anger filnamnet för var och en av de översta 10 begärda tillgångarna. Direkt under diagrammet (x-axeln) hittar du etiketter som anger det totala antalet försök/avslutade hämtningar.

Direkt under stapeldiagram följande information visas för de övre 250 begärda tillgångarna: relativa sökvägen (inklusive filnamnet), hur många gånger som det har laddats ner till slutförande, antalet gånger som den begärdes och procentandelen förfrågningar som resulterade i en fullständig hämtning.

> [!TIP]
> Vår CDN inte informeras av HTTP-klienten (d.v.s. webbläsare) när en tillgång helt har hämtats. Därför har vi att beräkna om en tillgång helt har laddats ned enligt statuskoder och byte-intervall begäranden. Det första vi letar när du gör den här beräkningen är om begäran resulterar i en 200 OK statuskod. I så fall, sedan titta vi på begäranden om byte-intervall så att de täcker hela tillgången. Slutligen jämföra vi mängden data som överförs till storleken på den begärda tillgången. Om de data som överförs är lika med eller större än filstorleken och byte-intervall-begäranden är lämpliga för tillgången, kommer träffar räknas som en fullständig hämtning.
> 
> På grund av den här rapporten interpretive hur bör du tänka på följande punkter som får ändra konsekvens och korrektheten i den här rapporten.
> 
> * Trafikmönster förutsägas inte när användaren agenter fungerar annorlunda. Detta kan ge slutförda hämta resultat som är större än 100%.
> * Tillgångar som utnyttjar HTTP progressiv nedladdning representeras inte korrekt av den här rapporten. Detta beror på användare som vill ha till andra platser på en video.
> 
> 

## <a name="by-404-errors"></a>Av 404-fel
Rapporten av 404-fel kan du identifiera vilken sorts innehåll som genererar flest 404 gick inte att hitta statuskoder. Överst i rapporten innehåller ett stapeldiagram för topp 10 tillgångarna som en 404 gick inte att hitta statuskod returnerades. Den här stapeldiagram jämför det totala antalet begäranden med begäranden som resulterade i en 404 gick inte att hitta statuskod för dessa tillgångar. Varje fält är färgkodade. Ett gult fält används för att indikera att begäran resulterade i en 404 gick inte att hitta statuskod. En röd stapel används för att ange det totala antalet begäranden för tillgången.

> [!NOTE]
> Vid tillämpningen av den här rapporten, Tänk på följande:
> 
> * En träff representerar en begäran för en tillgång oavsett statuskod.
> * Edge CNAME webbadresser konverteras till sina motsvarande CDN-URL: er. På så sätt kan en korrekt tally för all statistik som är associerade med en tillgång oavsett CDN eller edge CNAME-URL som används för att begära den.
> 
> 

Den vänstra sidan av diagrammet (y-axeln) anger filnamnet för var och en av de översta 10 begärda tillgångar som resulterade i en 404 gick inte att hitta statuskod. Direkt under diagrammet (x-axeln) hittar du etiketter som anger det totala antalet begäranden och antalet begäranden som resulterade i en 404 gick inte att hitta statuskod.

Direkt under stapeldiagram följande information visas för de övre 250 begärda tillgångarna: relativa sökvägen (inklusive filnamnet), antal begäranden som resulterade i en 404 gick inte att hitta statuskod, det totala antalet gånger som begärdes tillgången och procentandelen förfrågningar som resulterade i en 404 gick inte att hitta statuskod.

## <a name="see-also"></a>Se även
* [Azure CDN-översikt](cdn-overview.md)
* [Realtid statistik i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Åsidosätta standardbeteendet i HTTP-motorn regler](cdn-rules-engine.md)
* [Analysera Edge prestanda](cdn-edge-performance.md)

