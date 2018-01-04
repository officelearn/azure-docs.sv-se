---
title: "Ställ en fråga data kan besvara - problem med vetenskap - Azure Machine Learning | Microsoft Docs"
description: "Lär dig hur du formulerar en skarpa datavetenskap fråga i datavetenskap för nybörjare video 3. Innehåller en jämförelse av klassificering och regression frågor."
keywords: "datavetenskap problem, datavetenskap frågor Formulera frågor, regression frågor, klassificering, frågor, skarpa fråga"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: 5b9501e3-9964-417a-8ffc-8913103da77b
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: 2f3d8d5c2e7cf1ebc88dc1ff1d7d03bf85383884
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Ställ en fråga som du kan svara på med data
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Datavetenskap för nybörjare serien
Lär dig att formulera datavetenskap problem i en fråga i datavetenskap för nybörjare video 3. Den här videon innehåller en jämförelse av frågor för klassificering och regression algoritmer.

Titta på alla för att få ut mesta möjliga av serien. [Gå till listan över videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Datavetenskap för nybörjare* är en snabb introduktion till datavetenskap i fem kort video.

* Video 1: [5 frågor datavetenskap svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min. 14 sek)*
* Video 2: [är data som är redo för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min. 56 sek)*
* Video 3: Ställ en fråga som du kan svara med data
* Video 4: [förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min. 42 sek)*
* Video 5: [kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Betyg: Ställ en fråga som du kan svara med data
Välkommen till tredje videon i serien ”datavetenskap för nybörjare”.  

I det här objektet får du några tips för att utforma en fråga som du kan svara med data.

Du kan få ut mer av den här videon om du tittar på de två tidigare videorna i den här serien: ”datavetenskap 5 frågor kan besvara” och ”är dina data är klar för datavetenskap”?

## <a name="ask-a-sharp-question"></a>Ställ en fråga som skarpa
Hittills har diskuterats hur datavetenskap är processen för att förutsäga ett svar på en fråga med hjälp av namn (kallas även för kategorier eller etiketter) och siffror. Men den kan inte vara valfri fråga. Det måste vara en *skarpa fråga.*

En vaga fråga behöver inte besvaras med ett namn eller en siffra. Måste vara en skarpa fråga.

Anta att du hittat ett magiskt ljus med en genie som sanningsenligt ska besvara varje fråga du har. Men det är en mischievous genie och han försöker göra sin svar så vag och förvirrande eftersom han kan få direkt med. Du vill fästa honom med en fråga lufttätt så att han kan hjälpa men Berätta vad du vill veta.

Om du vill ställa en vaga fråga som ”vad som ska hända med min börs”?, genie kan besvara, ”priset ändras”. Utgivarens svaret är, men det är inte mycket användbara.

Men om du vill ställa en fråga som skarpa, t.ex. ”vad min börs försäljningspriset nästa vecka”?, genie kan hjälpa men ger dig en specifik svara och förutsäga ett försäljningspriset.

## <a name="examples-of-your-answer-target-data"></a>Exempel på ditt svar: målprogram
Kontrollera om du har ett exempel på svaret i dina data när du formulerar din fråga.

Om vår frågan ”vad min börs försäljningspriset blir nästa vecka”? Vi har sedan kontrollerar du att våra data innehåller lager pris historiken.

Om vår frågan är ”vilka bil i min flottan kommer att misslyckas först”? Vi har sedan kontrollerar du att våra data innehåller information om föregående fel.

![Målprogram - exempel på ditt svar. Formulera en fråga om vetenskap.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

De här exemplen svar kallas för ett mål. Ett mål är vad vi vill förutsäga om framtida datapunkter, oavsett om det är en kategori eller en siffra.

Om du inte har några måldata behöver du få några. Du kan inte svara på din fråga utan den.

## <a name="reformulate-your-question"></a>Reformulate din fråga
Ibland kan du formulera om frågan för att få en mer användbar svar.

Frågan ”är den här data punkt A eller B”? beräknar kategorinamnet (eller eller etikett) av något. För att besvara det vi använder en *klassificeringsalgoritm*.

Frågan ”hur mycket”? eller ”hur många”? beräknar ett belopp. Besvara den vi använder en *regressionsalgoritm*.

Om du vill se hur vi kan omvandla dessa nu ska vi titta på frågan ”vilken nyheter artikeln är den mest intressanta denna läsare”? Uppmanas du att ange en förutsägelse av ett val från många möjligheter - med andra ord ”är den här A eller B eller C eller D”? - och använder en klassificeringsalgoritm för.

Men den här frågan kan vara enklare att besvara om formulera om den som ”hur intressanta är varje artikel på den här listan för att denna läsare”? Nu kan du ge varje artikel en numerisk poäng och är det enkelt att identifiera flest poäng artikeln. Detta är att skriva om av klassificering frågan till en regression fråga eller hur mycket?

![Reformulate din fråga. Klassificering fråga kontra regression fråga.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hur du ställa en fråga är en ledtråd till vilken algoritm kan du få ett svar.

Du hittar att vissa typer av algoritmer - de i vårt nyheter artikeln exempel - relaterade. Du kan reformulate din fråga om du vill använda den algoritm som ger dig mest användbara svaret.

Men de viktigaste fråga att skarpa - fråga som du kan svar med data. Och kontrollera att du har rätt data att besvara den.

Hittills har diskuterats vissa grundläggande principer för ställa en fråga som du kan svara med data.

Glöm inte att checka ut andra videor i ”datavetenskap för nybörjare” från Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Nästa steg
* [Försök med ett första datavetenskap experiment i Machine Learning Studio](create-experiment.md)
* [Få en introduktion till Maskininlärning på Microsoft Azure](what-is-machine-learning.md)
