---
title: Analysera användningsstatistik med Azure CDN avancerade HTTP-rapporter | Microsoft-dokument
description: Lär dig hur du skapar avancerade HTTP-rapporter i Microsoft Azure CDN. Dessa rapporter innehåller detaljerad information om CDN-aktivitet.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 0b0eec2425f8a1663eb7a09c83a6bad037d1d79c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594099"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Analysera användningsstatistik med Avancerade HTTP-rapporter i Azure CDN
## <a name="overview"></a>Översikt
Det här dokumentet förklarar avancerad HTTP-rapportering i Microsoft Azure CDN. Dessa rapporter innehåller detaljerad information om CDN-aktivitet.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Komma åt avancerade HTTP-rapporter
1. Klicka på knappen **Hantera** i CDN-profilbladet.
   
    ![Knappen Hantera CDN-profilblad](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Hovra över fliken **Analytics** och hovra sedan över det avancerade **http-rapporternas** utfällbara rapporter.  Klicka på **HTTP Large Platform**.
   
    ![CdN-hanteringsportal - menyn Avancerade rapporter](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Rapportalternativ visas.

## <a name="geography-reports-map-based"></a>Geografi Rapporter (karta-baserade)
Det finns fem rapporter som utnyttjar en karta för att ange de regioner som ditt innehåll begärs från. Dessa rapporter är World Map, United States Map, Canada Map, Europe Map och Asia Pacific Map.

Varje kartbaserad rapport rangordnar geografiska entiteter (dvs. länder/regioner, en karta tillhandahålls som hjälper dig att visualisera de platser från vilka ditt innehåll begärs. Det kan göra det genom färgkodning varje region beroende på hur mycket efterfrågan upplevs i den regionen. Ljusare skuggade områden indikerar lägre efterfrågan på ditt innehåll, medan mörkare områden indikerar högre nivåer av efterfrågan på ditt innehåll.

Detaljerad trafik- och bandbreddsinformation för varje region finns direkt under kartan. På så sätt kan du visa det totala antalet träffar, procentandelen träffar, den totala mängden data som överförs (i gigabyte) och procentandelen data som överförs för varje region. Visa en beskrivning för vart och ett av dessa mått. När du håller muspekaren över en region (t.ex. land/region, delstat eller provins) visas namnet och procentandelen träffar som inträffade i regionen som ett verktygstips.

En kort beskrivning ges nedan för varje typ av kartbaserad geografirapport.

| Rapportnamn | Beskrivning |
| --- | --- |
| Världskarta |Med den här rapporten kan du visa den globala efterfrågan på cdn-innehåll. Varje land/region är färgkodad på världskartan för att ange procentandelen träffar som kommer från den regionen. |
| Karta över USA |I den här rapporten kan du visa efterfrågan på CDN-innehåll i USA. Varje tillstånd är färgkodat på den här kartan för att ange procentandelen träffar som kommer från den regionen. |
| Kanada Karta |I den här rapporten kan du visa efterfrågan på CDN-innehåll i Kanada. Varje provins är färgkodad på den här kartan för att ange procentandelen träffar som kommer från den regionen. |
| Europa Karta |I den här rapporten kan du visa efterfrågan på cdn-innehåll i Europa. Varje land/region är färgkodad på den här kartan för att ange procentandelen träffar som kommer från den regionen. |
| Asien och Stillahavsområdet Karta |I den här rapporten kan du visa efterfrågan på CDN-innehåll i Asien. Varje land/region är färgkodad på den här kartan för att ange procentandelen träffar som kommer från den regionen. |

## <a name="geography-reports-bar-charts"></a>Geografi rapporter (stapeldiagram)
Det finns ytterligare två rapporter som ger statistisk information enligt geografi, som är topstäder och toppländer. Dessa rapporter rangordnar städer och länder/regioner, respektive, enligt numrera av hits som påbörjade från de länder/regioner. När du skapar den här typen av rapport anger ett stapeldiagram de tio bästa städerna eller länderna/regionerna som har begärt innehåll via en viss plattform. Med det här stapeldiagrammet kan du snabbt bedöma de regioner som genererar det högsta antalet begäranden om ditt innehåll.

Diagrammets vänstra sida (y-axeln) anger hur många träffar som inträffade i det angivna området. Direkt under diagrammet (x-axeln) hittar du en etikett för var och en av de 10 bästa regionerna.

### <a name="using-the-bar-charts"></a>Använda stapeldiagrammen
* Om du hovrar över en stapel visas namnet och det totala antalet träffar som har inträffat i regionen som ett verktygstips.
* Verktygstipset för rapporten Toppstäder identifierar en stad efter namn, stat/provins och lands-/regionförkortning.
* Om den stad eller region (dvs. stat/provins) från vilken en begäran inte kunde fastställas, kommer det att indikera att de är okända. Om landet/regionen är okänd visas två frågetecken (d.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.v.s.
* En rapport kan innehålla mått för "Europa" eller "Asien/Stillahavsområdet". Dessa poster är inte avsedda att ge statistisk information om alla IP-adresser i dessa regioner. Snarare gäller de bara för förfrågningar som kommer från IP-adresser som är utspridda över Europa eller Asien/Stillahavsområdet i stället för till en viss stad eller ett visst land/en viss region.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du det totala antalet träffar, procentandelen träffar, mängden data som överförs (i gigabyte) och procentandelen data som överförs för de 250 regionerna. Visa en beskrivning för vart och ett av dessa mått.

En kort beskrivning ges för båda typerna av rapporter nedan.

| Rapportnamn | Beskrivning |
| --- | --- |
| Toppstäder |Denna rapport rangordnar städer efter antalet träffar som härstammar från den regionen. |
| Toppländer |Denna rapport rangordnar länder/regioner efter antalet träffar som härstammar från det landet/regionen. |

## <a name="daily-summary"></a>Daglig sammanfattning
Med rapporten Daglig sammanfattning kan du visa det totala antalet träffar och data som överförs via en viss plattform dagligen. Denna information kan användas för att snabbt urskilja CDN-aktivitetsmönster. Den här rapporten kan till exempel hjälpa dig att identifiera vilka dagar som upplevdes som var högre eller lägre än förväntat trafik.

När den här typen av rapport tas fram ger ett stapeldiagram en visuell indikation på hur stor plattformsspecifik efterfrågan som upplevs dagligen under den tidsperiod som rapporten omfattar. Det kommer att göra det genom att visa en stapel för varje dag i rapporten. Om du till exempel väljer den tidsperiod som kallas "Förra veckan" skapas ett stapeldiagram med sju staplar. Varje stapel anger det totala antalet träffar som upplevs den dagen.

Diagrammets vänstra sida (y-axeln) anger hur många träffar som inträffade på det angivna datumet. Direkt under diagrammet (x-axeln) hittar du en etikett som anger datumet (Format: YYY-MM-DD) för varje dag som ingår i rapporten.

> [!TIP]
> Om du hovrar över en stapel visas det totala antalet träffar som inträffade på det datumet som ett verktygstips.
> 
> 

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du det totala antalet träffar och mängden data som överförs (i gigabyte) för varje dag som omfattas av rapporten.

## <a name="by-hour"></a>Per timme
Med rapporten Per timme kan du visa det totala antalet träffar och data som överförs via en viss plattform varje timme. Denna information kan användas för att snabbt urskilja CDN-aktivitetsmönster. Den här rapporten kan till exempel hjälpa dig att identifiera tidsperioder under dagen som får högre eller lägre trafik än förväntat.

När du skapar den här typen av rapport ger ett stapeldiagram en visuell indikation på hur stor plattformsspecifik efterfrågan som upplevs per timme under den tidsperiod som rapporten omfattar. Det kommer att göra det genom att visa en stapel för varje timme som omfattas av rapporten. Om du till exempel väljer en tidsperiod på 24 timmar skapas ett stapeldiagram med 24 staplar. Varje stapel anger det totala antalet träffar som upplevs under den timmen.

Diagrammets vänstra sida (y-axeln) anger hur många träffar som inträffade under den angivna timmen. Direkt under diagrammet (x-axeln) hittar du en etikett som anger datum/tid (Format: YYYY-MM-DD hh:mm) för varje timme som ingår i rapporten. Tiden rapporteras med 24-timmarsformat och det anges med hjälp av UTC/GMT tidszon.

> [!TIP]
> Om du hovrar över ett fält visas det totala antalet träffar som inträffade under den timmen som ett verktygstips.
> 
> 

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du det totala antalet träffar och mängden data som överförs (i gigabyte) för varje timme som omfattas av rapporten.

## <a name="by-file"></a>Efter fil
Med rapporten Efter fil kan du visa mängden efterfrågan och den trafik som uppstått över en viss plattform för de mest efterfrågade tillgångarna. När du skapar den här typen av rapport genereras ett stapeldiagram på de 10 mest efterfrågade tillgångarna under den angivna tidsperioden.

> [!NOTE]
> I den här rapporten konverteras edge CNAME-url:er till motsvarande CDN-webbadresser. Detta möjliggör en korrekt sammanräkning för det totala antalet träffar som är associerade med en tillgång oavsett CDN eller kant CNAME URL används för att begära det.
> 
> 

Diagrammets vänstra sida (y-axeln) anger antalet begäranden för varje tillgång under den angivna tidsperioden. Direkt under diagrammet (x-axeln) hittar du en etikett som anger filnamnet för var och en av de 10 begärda tillgångarna.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du följande information för var och en av de 250 begärda tillgångarna: relativ sökväg, det totala antalet träffar, procentandelen träffar, mängden data som överförs (i gigabyte) och procentandelen data som överförs.

## <a name="by-file-detail"></a>Information om fil
Med rapporten Efter filinformation kan du visa mängden efterfrågan och den trafik som uppstått över en viss plattform för en viss tillgång. Högst upp i den här rapporten finns alternativet Filinformation för. Det här alternativet innehåller en lista över dina mest efterfrågade tillgångar på den valda plattformen. För att kunna generera en rapport om efter filinformation måste du välja önskad tillgång i alternativet Filinformation för. Därefter anger ett stapeldiagram hur mycket daglig efterfrågan som genereras under den angivna tidsperioden.

Diagrammets vänstra sida (y-axeln) anger det totala antalet begäranden som en tillgång har upplevt en viss dag. Direkt under diagrammet (x-axeln) hittar du en etikett som anger det datum (Format: YYYY-MM-DD) för vilket CDN-efterfrågan på tillgången rapporterades.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du det totala antalet träffar och mängden data som överförs (i gigabyte) för varje dag som omfattas av rapporten.

## <a name="by-file-type"></a>Efter filtyp
Med rapporten Efter filtyp kan du visa mängden efterfrågan och den trafik som uppstår efter filtyp. När du skapar den här typen av rapport visar ett donutdiagram hur många träffar som genereras av de 10 bästa filtyperna.

> [!TIP]
> Om du hovrar över ett segment i donutdiagrammet visas internetmedietypen för den filtypen som ett verktygstips.
> 
> 

De data som användes för att generera donutdiagrammet kan ses under det. Där hittar du filnamnstillägget/Internet-medietypen, det totala antalet träffar, procentandelen träffar, mängden data som överförs (i gigabyte) och procentandelen data som överförs för var och en av de 250 bästa filtyperna.

## <a name="by-directory"></a>Efter katalog
Med rapporten By Directory kan du visa mängden efterfrågan och den trafik som uppstått över en viss plattform för innehåll från en viss katalog. När du skapar den här typen av rapport visar ett stapeldiagram det totala antalet träffar som genereras av innehåll i de 10 främsta katalogerna.

### <a name="using-the-bar-chart"></a>Använda stapeldiagrammet
* Hovra över ett fält om du vill visa den relativa sökvägen till motsvarande katalog.
* Innehåll som lagras i en undermapp i en katalog räknas inte vid beräkning av efterfrågan per katalog. Den här beräkningen är enbart beroende av antalet begäranden som genereras för innehåll som lagras i den faktiska katalogen.
* I den här rapporten konverteras edge CNAME-url:er till motsvarande CDN-webbadresser. Detta möjliggör en korrekt sammanräkning för all statistik som är associerad med en tillgång oavsett CDN eller edge CNAME URL används för att begära det.

Den vänstra sidan av diagrammet (y-axeln) anger det totala antalet begäranden för innehållet som lagras i dina 10 toppkataloger. Varje stapel i diagrammet representerar en katalog. Använd färgkodningsschemat för att matcha ett fält med en katalog som visas i avsnittet Topp 250 fullständiga kataloger.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du följande information för var och en av de 250 katalogerna: relativ sökväg, det totala antalet träffar, procentandelen träffar, mängden data som överförs (i gigabyte) och procentandelen data som överförs.

## <a name="by-browser"></a>Efter webbläsare
Med rapporten Efter webbläsare kan du visa vilka webbläsare som användes för att begära innehåll. När du skapar den här typen av rapport anger ett cirkeldiagram procentandelen begäranden som hanteras av de tio bästa webbläsarna.

### <a name="using-the-pie-chart"></a>Använda cirkeldiagrammet
* Hovra över ett segment i cirkeldiagrammet om du vill visa en webbläsares namn och version.
* I den här rapporten betraktas varje unik kombination av webbläsare/version som en annan webbläsare.
* Segmentet som kallas "Övrigt" anger procentandelen begäranden som hanteras av alla andra webbläsare och versioner.

De data som användes för att generera cirkeldiagrammet kan visas under det. Där hittar du webbläsaren typ / versionsnummer, det totala antalet träffar och andelen träffar för var och en av de 250 webbläsare.

## <a name="by-referrer"></a>Av Referrer
Med rapporten Efter hänvisning kan du visa de översta referenterna till innehåll på den valda plattformen. En referent anger det värdnamn från vilket en begäran genererades. När du skapar den här typen av rapport anger ett stapeldiagram hur mycket efterfrågan (dvs. träffar) som genereras av de 10 främsta referenerna.

Diagrammets vänstra sida (y-axeln) anger det totala antalet begäranden som en tillgång har upplevt för varje referent. Varje stapel i diagrammet representerar en referrer. Använd färgkodningsschemat för att matcha ett fält med en referrer som visas i avsnittet Översta 250-hänvisningen.

De data som användes för att generera stapeldiagrammet kan visas under det. Där hittar du webbadressen, det totala antalet träffar och andelen träffar som genereras från var och en av de 250 som refererar.

## <a name="by-download"></a>Genom att ladda ner
Med rapporten Genom att hämta kan du analysera nedladdningsmönster för ditt mest efterfrågade innehåll. Överst i rapporten finns ett stapeldiagram som jämför försök till nedladdningar med slutförda nedladdningar för de 10 begärda tillgångarna. Varje stapel är färgkodad beroende på om det är ett försök att ladda ner (blå) eller en slutförd nedladdning (grön).

> [!NOTE]
> I den här rapporten konverteras edge CNAME-url:er till motsvarande CDN-webbadresser. Detta möjliggör en korrekt sammanräkning för all statistik som är associerad med en tillgång oavsett CDN eller edge CNAME URL används för att begära det.
> 
> 

Den vänstra sidan av diagrammet (y-axeln) anger filnamnet för var och en av de 10 begärda tillgångarna. Direkt under diagrammet (x-axeln) hittar du etiketter som anger det totala antalet försök/slutförda nedladdningar.

Direkt under stapeldiagrammet visas följande information för de 250 begärda tillgångarna: relativ sökväg (inklusive filnamn), antalet gånger som den har hämtats till slutförande, antalet gånger som begärdes och procentandelen av begäranden som resulterade i en fullständig nedladdning.

> [!TIP]
> Vår CDN informeras inte av en HTTP-klient (dvs. webbläsare) när en tillgång har laddats ner helt. Som ett resultat måste vi beräkna om en tillgång har laddats ner helt enligt statuskoder och byte-range-begäranden. Det första vi letar efter när du gör denna beräkning är om begäran resulterar i en 200 OK statuskod. Om så är fallet, då vi tittar på byte-range förfrågningar för att säkerställa att de täcker hela tillgången. Slutligen jämför vi mängden data som överförs till storleken på den begärda tillgången. Om de överförda data är lika med eller större än filstorleken och byte-intervallbegäranden är lämpliga för den tillgången, räknas träffen som en fullständig hämtning.
> 
> På grund av den tolkningskaraktär som finns i rapporten bör du tänka på följande punkter som kan ändra rapportens enhetlighet och riktighet.
> 
> * Trafikmönster kan inte förutsägas korrekt när användaragenter beter sig annorlunda. Detta kan ge slutförda nedladdningsresultat som är större än 100 %.
> * Tillgångar som utnyttjar HTTP Progressive Download kanske inte representeras korrekt av den här rapporten. Detta beror på användare som söker till olika positioner i en video.
> 
> 

## <a name="by-404-errors"></a>Med 404 fel
Med rapporten Genom 404 fel kan du identifiera vilken typ av innehåll som genererar flest antal statuskoder för 404 hittades inte. Överst i rapporten finns ett stapeldiagram för de tio viktigaste tillgångarna för vilka en statuskod på 404 hittades. I det här stapeldiagrammet jämförs det totala antalet begäranden med begäranden som resulterade i en statuskod på 404 hittades inte för dessa tillgångar. Varje stapel är färgkodad. En gul stapel används för att ange att begäran resulterade i en statuskod på 404 hittades inte. Ett rött fält används för att ange det totala antalet begäranden för tillgången.

> [!NOTE]
> I denna rapport bör du tänka på följande:
> 
> * En träff representerar alla begäranden om en tillgång oavsett statuskod.
> * Edge CNAME-url:er konverteras till motsvarande CDN-url:er. Detta möjliggör en korrekt sammanräkning för all statistik som är associerad med en tillgång oavsett CDN eller edge CNAME URL används för att begära det.
> 
> 

Den vänstra sidan av diagrammet (y-axeln) anger filnamnet för var och en av de 10 begärda tillgångarna som resulterade i en statuskod på 404 hittades inte. Direkt under diagrammet (x-axeln) hittar du etiketter som anger det totala antalet begäranden och antalet begäranden som resulterade i en statuskod på 404 hittades inte.

Direkt under stapeldiagrammet visas följande information för de 250 begärda tillgångarna: relativ sökväg (inklusive filnamn), antalet begäranden som resulterade i en statuskod på 404 hittades inte, det totala antalet gånger som tillgången begärdes och procentandelen begäranden som resulterade i statuskoden 404 hittades inte.

## <a name="see-also"></a>Se även
* [Översikt över Azure CDN](cdn-overview.md)
* [Statistik i realtid i Microsoft Azure CDN](cdn-real-time-stats.md)
* [Åsidosätta standard-HTTP-beteende med hjälp av regelmotorn](cdn-rules-engine.md)
* [Analysera edge-prestanda](cdn-edge-performance.md)

