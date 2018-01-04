---
title: "Förutsäga ett svar med en enkel regressionsmodell - Azure Machine Learning | Microsoft Docs"
description: "Så här skapar du en enkel regressionsmodell för att förutsäga ett pris i datavetenskap för nybörjare video 4. Innehåller en linjär regression med måldata."
keywords: "Skapa en modell, enkla modellen, pris förutsägelse, enkla regressionsmodell"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: a28f1fab-e2d8-4663-aa7d-ca3530c8b525
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: 79c66fe29c8d51a8cd1db24a024974f943e89e74
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="predict-an-answer-with-a-simple-model"></a>Förutsäga ett svar med en enkel modell
## <a name="video-4-data-science-for-beginners-series"></a>Video 4: Datavetenskap för nybörjare serien
Lär dig hur du skapar en enkel regressionsmodell för att förutsäga priset på en rombformade i datavetenskap för nybörjare video 4. Vi ska rita en regressionsmodell med måldata.

Titta på alla för att få ut mesta möjliga av serien. [Gå till listan över videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Datavetenskap för nybörjare* är en snabb introduktion till datavetenskap i fem kort video.

* Video 1: [5 frågor datavetenskap svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min. 14 sek)*
* Video 2: [är data som är redo för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min. 56 sek)*
* Video 3: [Ställ en fråga som du kan svara med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: Förutsäga ett svar med en enkel modell
* Video 5: [kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Betyg: Förutsäga ett svar med en enkel modell
Välkommen till den fjärde videon i den ”datavetenskap för nybörjare” serien. I det här objektet ska vi skapa en enkel modell och göra en förutsägelse.

En *modellen* är en förenklad artikel om våra data. Jag lära dig vad det innebär.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Samla relevant, korrekt, ansluten, tillräckligt med data
Anta jag vill handla för en rombformade. Jag har en ring tillhörde min mor med en inställning för en 1.35 cirkumflex rombformade och jag vill få en uppfattning om hur mycket kostar. Jag blir anteckningar, penna till arkivet smycken och jag skriva ned priset på alla stjärnor i fallet och hur mycket de väg i carats. Från och med den första rombformade - 1.01 carats och $7,366.

Nu jag gå igenom och göra detta för alla andra stjärnor i arkivet.

![Datakolumner rombformade](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Observera att vår lista har två kolumner. Varje kolumn har ett annat attribut - vikt i carats och pris- och varje rad är en enskild datapunkt som representerar en enskild rombformade.

Vi har skapat en liten faktiskt data här - ange en tabell. Observera att det uppfyller våra kriterier för kvalitet:

* Data är **relevanta** -vikt definitivt är relaterad till pris
* Den har **korrekt** -vi dubbelkollat de priser som vi anteckna
* Den har **anslutna** -det finns inga blanksteg i något av dessa kolumner
* Och som vi ser den har **tillräckligt med** data som svar på vår fråga

## <a name="ask-a-sharp-question"></a>Ställ en fråga som skarpa
Nu ska vi utgöra våra fråga i skarpa sätt: ”hur mycket kostar det för att köpa en 1.35 cirkumflex rombformade”?

Vår lista har en 1.35 cirkumflex rombformade, så vi kommer att behöva använda resten av våra data för att få svar på frågan.

## <a name="plot-the-existing-data"></a>Rita befintliga data
Det första vi ska göra är att dra en linje för vågrät nummer, kallas en axel för att skapa diagram över vikterna. Vikterna intervallet är 0 till 2 så vi ska rita som täcker intervall och placera tick för varje halva cirkumflex.

Vi kommer nästa rita en lodrät axel för att registrera priset och ansluta till vikt vågrät axel. Detta kan vara i enheter om dollar. Nu har vi en uppsättning koordinaten axlar.

![Vikt- och axlar](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vi tar nu dessa data och konverterar den till en *punktdiagram ritytans*. Detta är ett bra sätt att visualisera numeriska datamängder.

För den första datapunkten eyeball vi en lodrät linje vid 1.01 carats. Vi eyeball sedan en vågrät linje vid $7,366. Om de uppfyller Rita vi en punkt. Detta representerar vår första rombformade.

Nu vi gå igenom varje rombformade i listan och gör samma sak. När vi via det här är vad vi får: flera punkter, en för varje rombformade.

![Punktdiagram ritytans](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Rita modellen genom datapunkter
Nu om du tittar på punkter och squint samlingen ser ut som en fat fuzzy linje. Vi kan ta våra markör och rita rakt igenom den.

Genom att dra en linje vi har skapat en *modellen*. Tänk på detta som tar verkligheten och göra en simplistic tecknad version av den. Nu tecknat är fel - raden Gå inte igenom alla datapunkter. Men det är en användbar förenkling av distribution.

![Regressionslinjen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Det faktum att alla punkter inte går exakt genom raden är OK. Datavetare förklara detta genom att säga att modellen - som är rad - och sedan varje punkt har vissa *brus* eller *varians* som är kopplade till den. Det finns underliggande perfekt relationen och det finns mer, verkliga världen som lägger till brus och osäkerhet.

Eftersom vi försöker besvara frågan *hur mycket?* detta kallas en *regression*. Och eftersom vi använder rakt, är det en *linjär regression*.

## <a name="use-the-model-to-find-the-answer"></a>Använd modellen för att hitta svaret
Nu när vi har en modell och vi fråga den våra: hur mycket kostar en 1.35 cirkumflex rombformade?

För att besvara våra fråga vi ögonglobens 1.35 carats och rita en lodrät linje. Där den korsar raden modellen eyeball vi vågrät linje dollar axel. Den träffar höger på 10 000. BOM! Det är svaret: en 1.35 cirkumflex rombformade kostnader cirka 10 000 $.

![Hitta svaret på modellen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Skapa en konfidensintervallet
Det är fysiska undrar hur exakt den här förutsägelse är. Det är bra att känna av om 1.35 cirkumflex rombformade blir mycket nära 10 000 kr eller mycket högre eller lägre. För att lösa detta ut vi Rita kuvertet runt regressionslinjen som innehåller de flesta av punkterna. Kuvertet kallas vår *konfidensintervallet*: vi ganska säker på att priserna ligger inom det här kuvertet eftersom de senaste av dem ha. Vi kan hämta två mer horisontella raderna från där 1.35 cirkumflex linjen korsar upp och längst ned på kuvertets.

![Konfidensintervallet](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nu kan vi säga något om våra konfidensintervallet: vi kan säga säkert sätt att priset på en 1.35 cirkumflex rombformade är ungefär $ 10 000 - men det kan vara så lågt som 8 000 och det kan vara så högt som $ 12 000.

## <a name="were-done-with-no-math-or-computers"></a>Vi är klara, utan matematiska eller datorer
Vi gjorde vad datavetare hämta betald att göra och vi gjorde det genom att rita:

* Vi frågade en fråga för att vi kan besvara med data
* Vi har skapat en *modellen* med *linjär regression*
* Vi har gjort en *prediction*, med en *konfidensintervallet*

Och vi använder inte matematiska eller datorer till den.

Nu om vi hade mer information som...

* Klipp ut av rombformade
* färgvariationer (hur nära symbol är blir vit)
* antalet inkluderingar i rombformade

...så vi fick fler kolumner. I så fall blir matematiska användbara. Om du har fler än två kolumner är det svårt att rita punkter på dokumentet. Matematiskt kan du anpassa den raden eller detta plan till dina data mycket snyggt.

Även om i stället för bara ett fåtal stjärnor, vi hade två tusen eller två miljoner och sedan göra mycket snabbare som fungerar med en dator.

Idag hittills har diskuterats hur du gör linjär regression och vi har gjort en förutsägelse med hjälp av data.

Glöm inte att checka ut andra videor i ”datavetenskap för nybörjare” från Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Nästa steg
* [Försök med ett första datavetenskap experiment i Machine Learning Studio](create-experiment.md)
* [Få en introduktion till Maskininlärning på Microsoft Azure](what-is-machine-learning.md)
