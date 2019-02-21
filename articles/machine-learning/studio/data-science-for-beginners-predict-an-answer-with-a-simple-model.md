---
title: Förutsäga svar med regressionsmodeller
titleSuffix: Azure Machine Learning Studio
description: Så här skapar du en enkel regressionsmodell för att förutsäga ett pris i datavetenskap för nybörjare video 4. Innehåller en linjär regression med måldata.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: garyericson
ms.author: garye
ms.custom: seodec18
ms.date: 01/03/2018
ms.openlocfilehash: 6adcf59053186c961a115164baac1f0af631ff4a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456854"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Förutsäga ett svar med en enkel modell
## <a name="video-4-data-science-for-beginners-series"></a>Video 4: Datavetenskap för nybörjare-serien
Lär dig hur du skapar en enkel regressionsmodell för att förutsäga priset på en romb i datavetenskap för nybörjare video 4. Vi kommer att rita en regressionsmodell med måldata.

Titta på alla för att få ut det mesta av serien. [Gå till listan över videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Datavetenskap för nybörjare* är en snabbintroduktion i datakunskap med fem korta filmer.

* Video 1: [5 frågor och svar om datavetenskap](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Video 2: [Är dina data klara för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: [Ställ en fråga som du kan svara på med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: Förutsäga ett svar med en enkel modell
* Video 5: [Kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Avskriften: Förutsäga ett svar med en enkel modell
Välkommen till den fjärde videon i den ”Data datavetenskap för nybörjare” serien. I det här objektet ska vi skapa en enkel modell och göra en förutsägelse.

En *modellen* är en förenklad historia om våra data. Du lär dig vad jag betyder.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Collect relevanta, korrekt, ansluten, tillräckligt med data
Anta att jag vill leta efter en romb. Jag har en testgrupp som tillhör min mor med en inställning för en 1.35 cirkumflex romb och jag vill få en uppfattning av hur mycket det kostar. Jag blir anteckningar, penna till arkivet smycken och jag anteckna priset för alla romber i fallet och hur mycket de väg i carats. Från och med den första romben - 1.01 carats och $7,366.

Nu jag gå igenom och göra detta för alla andra romber i arkivet.

![Romb datastaplar](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Observera att listan har två kolumner. Varje kolumn har ett annat attribut – vikt i carats och pris- och varje rad är en enskild datapunkt som representerar en enskild romb.

Faktiskt har vi skapat en liten datauppsättning här – en tabell. Lägg märke till att de uppfyller våra villkor under kvalitetsuppdateringar:

* Data är **relevanta** -vikt definitivt är relaterad till pris
* Den har **korrekt** -vi dubbelkollat de priser som vi anteckna
* Den har **anslutna** -det finns inga blanksteg i någon av dessa kolumner
* Och eftersom vi ser den har **tillräckligt med** data som svar på våra fråga

## <a name="ask-a-sharp-question"></a>Ställ en sharp fråga
Nu ska vi utgöra vår fråga på en sharp sätt: ”Hur mycket det kostar för att köpa en 1.35 cirkumflex romb”?

Vår lista har en 1.35 cirkumflex romb, så vi måste du använda resten av våra data för att få ett svar på frågan.

## <a name="plot-the-existing-data"></a>Rita befintliga data
Det första vi ska göra är att rita en vågrät linje för tal, kallas en axel för att skapa diagram över vikterna. Intervallet av vikterna är 0 till 2, så vi kommer att rita en linje som täcker som intervall och placera ticken för varje halva cirkumflex.

Vi kommer sedan rita en lodrät axel för att registrera priset och ansluter den till vikt för vågrät axel. Det här är i enheter om dollar. Nu har vi en uppsättning koordinaten axlar.

![Vikt- och axlar](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vi ska nu ta dessa data och konverterar den till en *punktdiagram*. Det här är ett bra sätt att visualisera numeriska datauppsättningar.

Den första datapunkten eyeball vi en lodrät linje vid 1.01 carats. Sedan kan eyeball vi en vågrät linje vid $7,366. Om de uppfyller Rita vi en punkt. Detta representerar vår första romb.

Nu vi gå igenom varje romb i listan och göra samma sak. När vi är klar med det här är vad vi får: en massa punkter, en för varje romb.

![Punktdiagram](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Rita modellen via datapunkter
Nu om du tittar på de punkter och squint samlingen ser ut som en fat, fuzzy-rad. Vi kan ta våra markör och rita en rak linje genom den.

Genom att rita en rad som vi skapade en *modellen*. Tänk på det som tar den verkliga världen och göra en förenklad tecknad version av den. Nu tecknat är fel - raden Gå inte igenom alla datapunkter. Men det är en användbar förenkling av distribution.

![Linjär regression rad](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Det faktum att alla punkter inte exakt genomgå raden är OK. Dataexperter förklarar du detta genom att säga att det är den modell - som är rad - och sedan varje punkt har några *bruset* eller *varians* kopplade till den. Det finns underliggande perfekt relationen och det finns mer, verkliga världen som lägger till brus och osäkerheten.

Eftersom vi försöker besvara frågan *hur mycket?* detta kallas en *regression*. Och eftersom vi använder en rak linje, det är en *linjär regression*.

## <a name="use-the-model-to-find-the-answer"></a>Använd modellen svaret
Nu har vi en modell och vi har Ställ vår fråga: Hur mycket kostar en 1.35 cirkumflex romb?

För att besvara vår fråga vi ögonikonen 1.35 carats och rita en lodrät linje. Där den korsar raden modellen eyeball vi en vågrät linje dollar axel. Det kommer till höger på 10 000. Japp! Svaret är: En 1.35 cirkumflex romb kostnaderna om 10 000 USD.

![Hitta svaret på modellen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Skapa en konfidensintervallet
Det är naturlig fundera över hur exakt den här förutsägelse är. Det är bra att veta om 1.35 cirkumflex romb ska vara mycket nära 10 000 USD, eller mycket högre eller lägre. För att fastställa detta vi rita ett kuvert runt regressionslinjen som innehåller de flesta av punkterna. Den här kuvert kallas vår *konfidensintervall*: Vi är ganska säker på att priserna ligger inom det här kuvertet eftersom i den senaste de flesta av dem har. Vi kan hämta två mer vågräta linjer från där 1.35 cirkumflex linje korsar upp och längst ned på kuvertets.

![Konfidensintervall](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nu kan vi säga något om vårt konfidensintervall:  Vi kan säga tryggt att priset för en 1.35 cirkumflex romb är ungefär $ 10 000 – men det kan vara så lågt som 8 000 och det kan vara så mycket som 12 000 $.

## <a name="were-done-with-no-math-or-computers"></a>Vi är klara, utan matematiska eller datorer
Vi gjorde vad dataforskare få betalt för att göra och vi gjorde det genom att rita:

* Vi ställt frågan att vi kan svara på med data
* Vi har byggt en *modellen* med *linjär regression*
* Vi har gjort en *förutsägelse*, med en *konfidensintervall*

Och vi använder inte matematiska eller datorer till den.

Om vi hade haft mer information, som nu...

* Klipp ut av romben
* färg varianter (hur nära romben är att vara vit)
* antalet inkluderingar i romben

...så vi fick fler kolumner. I så fall blir matematiska användbart. Om du har fler än två kolumner, är det svårt att rita punkter på dokumentet. Resultatet kan du anpassa den raden eller detta plan till dina data mycket ett snyggt sätt.

Även om de i stället för att bara ett fåtal romber, vi hade två tusen eller två miljoner och arbetet kan göra mycket snabbare med en dator.

Idag vi har pratat om hur du gör linjär regression och vi har gjort en förutsägelse med hjälp av data.

Glöm inte att checka ut andra videor i ”Data datavetenskap för nybörjare” från Microsoft Azure Machine Learning Studio.

## <a name="next-steps"></a>Nästa steg
* [Prova en första dataexperiment med Machine Learning Studio](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](what-is-machine-learning.md)
