---
title: Ställ en fråga data kan svara
titleSuffix: ML Studio (classic) - Azure
description: Lär dig att formulera en skarp datavetenskap fråga i Data Science för nybörjare video 3. Inkluderar en jämförelse av klassificerings- och regressionsfrågor.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 26837337b49d79a26404fd6709b036f6907720f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838843"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Ställ en fråga som du kan svara på med data
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Data Science för nybörjare serien
Lär dig hur du formulerar ett datavetenskapsproblem i en fråga i datavetenskap för nybörjare video 3. Den här videon innehåller en jämförelse av frågor för klassificerings- och regressionsalgoritmer.

För att få ut det mesta av serien, titta på dem alla. [Gå till listan med videoklipp](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i denna serie
*Data Science for Beginners* är en snabb introduktion till datavetenskap i fem korta videor.

* Video 1: [De 5 frågorna datavetenskap svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek)*
* Video 2: [Är dina data redo för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: Ställ en fråga som du kan besvara med data
* Video 4: [Förutsäg ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [Kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Avskrift: Ställ en fråga som du kan besvara med data
Välkommen till den tredje videon i serien "Data Science for Beginners".  

I den här får du några tips om hur du formulerar en fråga som du kan besvara med data.

Du kan få ut mer av den här videon, om du först tittar på de två tidigare videorna i den här serien: "De 5 frågorna som datavetenskap kan svara på" och "Är dina data redo för datavetenskap?"

## <a name="ask-a-sharp-question"></a>Ställ en skarp fråga
Vi har talat om hur datavetenskap är processen att använda namn (även kallade kategorier eller etiketter) och siffror för att förutsäga ett svar på en fråga. Men det kan inte vara vilken fråga som helst; Det måste vara en *skarp fråga.*

En fråga behöver inte besvaras med ett namn eller ett nummer. En skarp fråga måste.

Föreställ dig att du hittat en magisk lampa med en ande som sanningsenligt kommer att svara på alla frågor du ställer. Men det är en busig ande, som kommer att försöka göra sitt svar så vagt och förvirrande som de kan komma undan med. Du vill sätta dit dem med en fråga så lufttät att de inte kan låta bli att berätta vad du vill veta.

Om du skulle ställa en fråga, som Vad kommer att hända med mitt lager?, kan anden svara, Priset kommer att förändras. Det är ett sanningsenligt svar, men det är inte till stor hjälp.

Men om du skulle ställa en skarp fråga, som "Vad kommer mitt lager försäljningspris vara nästa vecka?", anden kan inte låta bli att ge dig ett specifikt svar och förutsäga ett försäljningspris.

## <a name="examples-of-your-answer-target-data"></a>Exempel på ditt svar: Måldata
När du har formulerat din fråga kontrollerar du om du har exempel på svaret i dina data.

Om vår fråga är "Vad kommer mina aktier försäljningspris vara nästa vecka?" då måste vi se till att våra data innehåller aktiekurshistoriken.

Om vår fråga är "Vilken bil i min flotta kommer att misslyckas först?" då måste vi se till att våra data innehåller information om tidigare fel.

![Måldata - exempel på ditt svar. Formulera en datavetenskaplig fråga.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Dessa exempel på svar kallas ett mål. Ett mål är vad vi försöker förutsäga om framtida datapunkter, oavsett om det är en kategori eller ett tal.

Om du inte har några måldata måste du skaffa några. Du kommer inte att kunna svara på din fråga utan den.

## <a name="reformulate-your-question"></a>Omformulera din fråga
Ibland kan du omformulera din fråga för att få ett mer användbart svar.

Frågan "Är detta datapunkt A eller B?" förutsäger kategori (eller namn eller etikett) för något. För att besvara det använder vi en *klassificeringsalgoritm*.

Frågan "Hur mycket?" eller "Hur många?" förutspår ett belopp. För att besvara det använder vi en *regressionsalgoritm*.

För att se hur vi kan förändra dessa, låt oss titta på frågan, "Vilken nyhet är den mest intressanta för denna läsare?" Det ber om en förutsägelse av ett enda val från många möjligheter - med andra ord "Är detta A eller B eller C eller D?" - och skulle använda en klassificeringsalgoritm.

Men kan denna fråga vara lättare att svara på om du omformulera det som "Hur intressant är varje berättelse på denna lista till denna läsare?" Nu kan du ge varje artikel en numerisk poäng, och sedan är det lätt att identifiera den högst poäng artikeln. Detta är en omformning av klassificeringen frågan i en regression fråga eller Hur mycket?

![Omformulera din fråga. Klassificeringsfråga kontra regressionsfråga.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hur du ställer en fråga är en ledtråd till vilken algoritm som kan ge dig ett svar.

Du kommer att upptäcka att vissa familjer av algoritmer - som de i vår nyhet exempel - är nära besläktade. Du kan omformulera din fråga för att använda den algoritm som ger dig det mest användbara svaret.

Men, viktigast av allt, ställa den skarpa frågan - den fråga som du kan svara med data. Och se till att du har rätt data för att svara på den.

Vi har talat om några grundläggande principer för att ställa en fråga som du kan svara på med data.

Var noga med att kolla in de andra videorna i "Data Science for Beginners" från Microsoft Azure Machine Learning Studio (klassisk).

## <a name="next-steps"></a>Nästa steg
* [Prova ett första datavetenskapsexperiment med Machine Learning Studio (klassiskt)](create-experiment.md)
* [Få en introduktion till Maskininlärning på Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
