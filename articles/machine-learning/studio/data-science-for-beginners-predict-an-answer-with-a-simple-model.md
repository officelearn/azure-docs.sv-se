---
title: Förutsäga svar med regressionsmodeller
titleSuffix: ML Studio (classic) - Azure
description: Hur man skapar en enkel regressionsmodell för att förutsäga ett pris i Data Science for Beginners video 4. Inkluderar en linjär regression med måldata.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 6ad9c7912eee6c3f5ec55b9cd7ab340bc79c9db7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837779"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Förutsäga ett svar med en enkel modell
## <a name="video-4-data-science-for-beginners-series"></a>Video 4: Data Science för nybörjare serien
Lär dig hur du skapar en enkel regressionsmodell för att förutsäga priset på en diamant i Data Science for Beginners video 4. Vi ritar en regressionsmodell med måldata.

För att få ut det mesta av serien, titta på dem alla. [Gå till listan med videoklipp](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i denna serie
*Data Science for Beginners* är en snabb introduktion till datavetenskap i fem korta videor.

* Video 1: [De 5 frågorna datavetenskap svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek)*
* Video 2: [Är dina data redo för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: [Ställ en fråga som du kan svara på med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: Förutsäga ett svar med en enkel modell
* Video 5: [Kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Avskrift: Förutsäga ett svar med en enkel modell
Välkommen till den fjärde videon i "Data Science for Beginners"-serien. I den här bygger vi en enkel modell och gör en förutsägelse.

En *modell* är en förenklad berättelse om våra data. Jag ska visa dig vad jag menar.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Samla in relevanta, korrekta, anslutna, tillräckligt med data
Säg att jag vill handla en diamant. Jag har en ring som tillhörde min mormor med en inställning för en 1,35 karat diamant, och jag vill få en uppfattning om hur mycket det kommer att kosta. Jag tar ett anteckningsblock och penna i smycken butiken, och jag skriver ner priset på alla diamanter i fallet och hur mycket de väger i karat. Från och med den första diamanten - det är 1,01 karat och $ 7.366.

Nu går jag igenom och gör detta för alla andra diamanter i butiken.

![Kolumner med diamantdata](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Observera att vår lista har två kolumner. Varje kolumn har ett annat attribut - vikt i karat och pris - och varje rad är en enda datapunkt som representerar en enda diamant.

Vi har faktiskt skapat en liten datauppsättning här - en tabell. Observera att det uppfyller våra kriterier för kvalitet:

* Uppgifterna är **relevanta** - vikten är definitivt relaterad till pris
* Det är **korrekt** - vi dubbelkollade de priser som vi skriver ner
* Den är **ansluten** - det finns inga tomma utrymmen i någon av dessa kolumner
* Och som vi får se, det är **tillräckligt med** data för att svara på vår fråga

## <a name="ask-a-sharp-question"></a>Ställ en skarp fråga
Nu ska vi ställa vår fråga på ett skarpt sätt: "Hur mycket kommer det att kosta att köpa en 1,35 karat diamant?"

Vår lista har inte en 1,35 karat diamant i den, så vi måste använda resten av våra data för att få ett svar på frågan.

## <a name="plot-the-existing-data"></a>Rita befintliga data
Det första vi ska göra är att rita en horisontell tallinje, som kallas en axel, för att kartlägga vikterna. Intervallet för vikterna är 0 till 2, så vi drar en linje som täcker det intervallet och sätter fästingar för varje halv karat.

Därefter ritar vi en vertikal axel för att registrera priset och ansluta den till den horisontella viktaxeln. Detta kommer att vara i enheter av dollar. Nu har vi en uppsättning koordinatyxor.

![Vikt- och prisaxlar](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vi ska ta dessa data nu och göra den till en *scatter plot.* Detta är ett bra sätt att visualisera numeriska datauppsättningar.

För den första datapunkten, vi ögongloben en vertikal linje på 1,01 karat. Sedan ögongloben en horisontell linje på $ 7.366. Där de möts ritar vi en prick. Det här är vår första diamant.

Nu går vi igenom varje diamant på denna lista och göra samma sak. När vi är klara, är detta vad vi får: en massa prickar, en för varje diamant.

![Punkt tomt](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Rita modellen genom datapunkterna
Nu om man tittar på prickar och kisa, ser samlingen ut som en fet, suddig linje. Vi kan ta vår markör och dra en rak linje genom den.

Genom att rita en linje skapade vi en *modell.* Tänk på detta som att ta den verkliga världen och göra en förenklad tecknad version av den. Nu den tecknade är fel - linjen går inte igenom alla datapunkter. Men det är en nyttig förenkling.

![Linjär regressionslinje](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Det faktum att alla prickar inte går exakt igenom linjen är OK. Dataforskare förklarar detta genom att säga att det finns modellen - det är linjen - och sedan varje punkt har några *buller* eller *varians* i samband med det. Det finns den underliggande perfekta relationen, och sedan finns det skitiga, verkliga världen som tillför buller och osäkerhet.

För att vi försöker svara på frågan Hur *regression* *mycket?* Och eftersom vi använder en rak linje, det är en *linjär regression*.

## <a name="use-the-model-to-find-the-answer"></a>Använd modellen för att hitta svaret
Nu har vi en modell och vi ställer den vår fråga: Hur mycket kommer en 1,35 karat diamant kostnad?

För att besvara vår fråga, vi ögongloben 1,35 karat och dra en vertikal linje. Där den korsar modelllinjen, vi ögongloben en horisontell linje till dollarn axeln. Den träffar på 10.000. Boom! Det är svaret: En 1,35 karat diamant kostar ca $ 10.000.

![Hitta svaret på modellen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Skapa ett konfidensintervall
Det är naturligt att undra hur exakt denna förutsägelse är. Det är bra att veta om 1,35 karat diamant kommer att vara mycket nära $ 10.000, eller mycket högre eller lägre. För att räkna ut detta, låt oss dra ett kuvert runt regressionslinjen som innehåller de flesta punkter. Detta kuvert kallas vårt *konfidensintervall:* Vi är ganska säkra på att priserna faller inom detta kuvert, eftersom tidigare de flesta av dem har. Vi kan dra ytterligare två horisontella linjer från där 1,35 karatlinjen korsar toppen och botten av kuvertet.

![Konfidensintervall](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nu kan vi säga något om vårt konfidensintervall: Vi kan med tillförsikt säga att priset på en diamant på 1,35 karat är ungefär $ 10.000 - men det kan vara så lågt som $ 8.000 och det kan vara så högt som $ 12.000.

## <a name="were-done-with-no-math-or-computers"></a>Vi är klara, utan matte eller datorer
Vi gjorde vad dataforskare får betalt för att göra, och vi gjorde det bara genom att rita:

* Vi ställde en fråga som vi kunde besvara med
* Vi byggde en *modell* med linjär *regression*
* Vi gjorde en *förutsägelse,* komplett med ett *konfidensintervall*

Och vi använde inte matte eller datorer för att göra det.

Om vi hade fått mer information, som...

* snittet av diamanten
* färgvariationer (hur nära diamanten är att vara vit)
* antalet inneslutningar i diamanten

... då skulle vi ha haft fler kolumner. I så fall blir matte till hjälp. Om du har fler än två kolumner är det svårt att rita punkter på papper. Matten kan du passa den linjen eller det planet till dina data mycket fint.

Dessutom, om istället för bara en handfull diamanter, vi hade två tusen eller två miljoner, då kan du göra det arbetet mycket snabbare med en dator.

Idag har vi talat om hur man gör linjär regression, och vi gjorde en förutsägelse med hjälp av data.

Var noga med att kolla in de andra videorna i "Data Science for Beginners" från Microsoft Azure Machine Learning Studio (klassisk).

## <a name="next-steps"></a>Nästa steg
* [Prova ett första datavetenskapsexperiment med Machine Learning Studio (klassiskt)](create-experiment.md)
* [Få en introduktion till Maskininlärning på Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
