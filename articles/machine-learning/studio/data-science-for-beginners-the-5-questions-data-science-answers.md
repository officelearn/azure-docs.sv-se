---
title: Datavetenskap för nybörjare
titleSuffix: ML Studio (classic) - Azure
description: Data Science for Beginners lär grundläggande begrepp i 5 korta videor, som börjar med De 5 frågor Data Science Answers. Från Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: af0611e121228a587e159bd4e6529c807132d16d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204451"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Datavetenskap för nybörjare, video 1: Fem frågor och svar om datavetenskap
Få en snabb introduktion till datavetenskap från *Data Science for Beginners* i fem korta videor från en toppdatavetare. Dessa videor är grundläggande men användbara, oavsett om du är intresserad av att göra datavetenskap eller om du arbetar med datavetar.

Denna första video handlar om vilka typer av frågor som datavetenskap kan besvara. För att få ut det mesta av serien, titta på dem alla. [Gå till listan med videoklipp](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i denna serie
*Data Science för nybörjare* är en snabb introduktion till datavetenskap tar ca 25 minuter totalt. Kolla in alla fem videor:

* Video 1: De 5 frågorna datavetenskap svar
* Video 2: [Är dina data redo för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: [Ställ en fråga som du kan svara på med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: [Förutsäg ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [Kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Avskrift: De 5 frågor datavetenskap svar
Hej! Välkommen till videoserien *Data Science for Beginners.*

Data Science kan vara skrämmande, så jag ska presentera grunderna här utan ekvationer eller datorprogrammering jargong.

I den här första videon kommer vi att prata om "De 5 frågorna datavetenskap svar."

Data science använder siffror och namn (kallas även kategorier eller etiketter) för att förutsäga svar på frågor.

Det kan överraska dig, men *det finns bara fem frågor som datavetenskap svar:*

* Är det A eller B?
* Är det här konstigt?
* Hur mycket – eller – Hur många?
* Hur är det här organiserat?
* Vad bör jag göra då?

Var och en av dessa frågor besvaras av en separat familj av maskininlärningsmetoder, så kallade algoritmer.

Det är bra att tänka på en algoritm som ett recept och dina data som ingredienser. En algoritm berättar hur man kombinerar och blandar data för att få ett svar. Datorer är som en mixer. De gör det mesta av det hårda arbetet i algoritmen för dig och de gör det ganska snabbt.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Fråga 1: Är detta A eller B? använder klassificeringsalgoritmer
Låt oss börja med frågan: Är detta A eller B?

![Klassificeringsalgoritmer: Är detta A eller B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Denna familj av algoritmer kallas tvåklass klassificering.

Det är användbart för alla frågor som bara har två möjliga svar.

Ett exempel:

* Kommer detta däck misslyckas i nästa 1000 miles: Ja eller nej?
* Vilket ger fler kunder: en $ 5 kupong eller en 25% rabatt?

Denna fråga kan också omformuleras för att inkludera mer än två alternativ: Är detta A eller B eller C eller D, etc.?  Detta kallas multiclass klassificering och det är användbart när du har flera - eller flera tusen - möjliga svar. Multiclass-klassificering väljer den mest sannolika.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Fråga 2: Är detta konstigt? använder avvikelseidentifieringsalgoritmer
Nästa fråga datavetenskap kan svara är: Är detta konstigt? Den här frågan besvaras av en familj av algoritmer som kallas avvikelseidentifiering.

![Anomali upptäckt algoritmer: Är detta konstigt?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Om du har ett kreditkort har du redan dragit nytta av avvikelseidentifiering. Ditt kreditkortsföretag analyserar dina köpmönster, så att de kan varna dig för eventuella bedrägerier. Avgifter som är "konstiga" kan vara ett köp i en butik där du normalt inte handlar eller köper en ovanligt dyr objekt.

Denna fråga kan vara användbar på många sätt. Till exempel:

* Om du har en bil med tryckmätare, kanske du vill veta: Är denna tryckmätare läsning normal?
* Om du övervakar Internet, skulle du vilja veta: Är detta meddelande från Internet typiskt?

Avvikelseidentifiering flaggar oväntade eller ovanliga händelser eller beteenden. Det ger ledtrådar var man ska leta efter problem.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Fråga 3: Hur mycket? eller Hur många? använder regressionsalgoritmer
Maskininlärning kan också förutsäga svaret på Hur mycket? eller Hur många? Algoritmfamiljen som besvarar den här frågan kallas regression.

![Regressionsalgoritmer: Hur mycket? eller Hur många?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regressionsalgoritmer gör numeriska förutsägelser, till exempel:

* Hur blir temperaturen nästa tisdag?  
* Vad blir min försäljning för fjärde kvartalet?

De hjälper till att svara på alla frågor som ber om ett nummer.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Fråga 4: Hur är detta organiserat? använder klusteralgoritmer
Nu de två sista frågorna är lite mer avancerade.

Ibland vill du förstå strukturen för en datauppsättning - Hur är detta organiserat? För den här frågan har du inte exempel som du redan vet resultat för.

Det finns många sätt att reta ut strukturen på data. En metod är klustring. Det separerar data till naturliga "klumpar", för enklare tolkning. Med klustring finns det inget rätt svar.

![Klusteralgoritmer: Hur är detta organiserat?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Vanliga exempel på klusterfrågor är:

* Vilka tittare gillar samma typer av filmer?
* Vilka skrivarmodeller misslyckas på samma sätt?

Genom att förstå hur data är organiserat kan du bättre förstå - och förutsäga - beteenden och händelser.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Fråga 5: Vad ska jag göra nu? använder förstärkningsinlärningsalgoritmer
Den sista frågan - Vad ska jag göra nu? – använder en familj av algoritmer som kallas förstärkningsinlärning.

Förstärkning lärande var inspirerad av hur hjärnan hos råttor och människor reagera på straff och belöningar. Dessa algoritmer lär sig av resultat och bestämmer dig för nästa åtgärd.

Vanligtvis är förstärkning lärande en bra passform för automatiserade system som måste göra massor av små beslut utan mänsklig vägledning.

![Reinforcement Learning algoritmer: Vad ska jag göra härnäst?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Frågor den besvarar handlar alltid om vilka åtgärder som ska vidtas - vanligtvis av en maskin eller en robot. Några exempel:

* Om jag är ett temperaturkontrollsystem för ett hus: Justera temperaturen eller lämna den där den är?  
* Om jag är en självkörande bil: Vid ett gult ljus, bromsa eller accelerera?  
* För en robot vakuum: Fortsätt dammsuga, eller gå tillbaka till laddningsstationen?

Förstärkningsalgoritmer samlar in data när de går, lära av trial and error.

Så det är det - De 5 frågor datavetenskap kan svara på.

## <a name="next-steps"></a>Nästa steg
* [Prova ett första datavetenskapsexperiment med Machine Learning Studio (klassiskt)](create-experiment.md)
* [Få en introduktion till Maskininlärning på Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
