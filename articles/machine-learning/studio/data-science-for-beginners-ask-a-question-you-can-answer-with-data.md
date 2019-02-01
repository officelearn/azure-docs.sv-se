---
title: Ställ en fråga som kan svara på data
titleSuffix: Azure Machine Learning Studio
description: Lär dig att formulera en sharp data science-fråga i datavetenskap för nybörjare video 3. Innehåller en jämförelse av klassificerings- och regressionsmodeller frågor.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: garyericson
ms.author: garye
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/03/2018
ms.openlocfilehash: bddb97083bd1d1538b9f60ec3c0798cf1e964b1e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498156"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Ställ en fråga som du kan svara på med data
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Datavetenskap för nybörjare-serien
Lär dig att formulera data science problem i en fråga i datavetenskap för nybörjare video 3. Denna video innehåller en jämförelse av frågor för klassificering och regression algoritmer.

Titta på alla för att få ut det mesta av serien. [Gå till listan över videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Datavetenskap för nybörjare* är en snabbintroduktion i datakunskap med fem korta filmer.

* Video 1: [5 frågor och svar om datavetenskap](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Video 2: [Är dina data klara för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: Ställ en fråga som du kan svara på med data
* Video 4: [Förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [Kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Avskriften: Ställ en fråga som du kan svara på med data
Välkommen till tredje videon i serien ”datavetenskap för nybörjare”.  

I det här objektet får du några tips för utformningen av en fråga som du kan svara på med data.

Du kan få ut mer av den här videon om du först titta på två tidigare videor i den här serien: ”5 frågor data science kan besvara” och ”är dina data är klara för datavetenskap”?

## <a name="ask-a-sharp-question"></a>Ställ en sharp fråga
Vi har pratat om hur data science är processen för att förutsäga ett svar på en fråga med hjälp av namn (kallas även för kategorier eller etiketter) och siffror. Men det kan vara vilken fråga. Det måste vara en *sharp fråga.*

Vagt frågor behöver besvaras med ett namn eller en siffra. Måste vara en sharp fråga.

Anta att du hittat ett magic ljus med en genie som sanningsenligt sätt svarar på alla frågor som du be. Men det är en mischievous genie och han kommer försök att göra sitt svar så vaga och förvirrande eftersom han kan få direkt med. Du vill fästa honom med en fråga som lufttätt så att han kan hjälpa men Berätta vad du vill veta.

Om du vill ställa en vagt fråga som ”vad som händer med Mina lager”?, kan svara på genie, ”priset ändras”. Det är ett sanningsenliga svar, men är inte det mycket bra.

Men om du skulle ställa en sharp fråga som ”vad mitt lager försäljningspris blir nästa vecka”?, kan inte genie bidra men ger dig en specifik besvara och förutsäga ett försäljningspris.

## <a name="examples-of-your-answer-target-data"></a>Exempel på svaret: Måldata
Kontrollera om du har ett exempel på svaret i dina data när du formulera din fråga.

Om våra fråga är ”vad mitt lager försäljningspris blir nästa vecka”? sedan måste vi Kontrollera våra data ingår historiken för aktiekursen.

Om våra fråga är ”vilka bil i min flotta kommer att misslyckas först”? sedan måste vi Kontrollera att våra data innehåller information om föregående fel.

![Målprogram - exempel på ditt svar. Formulera en data science-fråga.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

De här exemplen för svar kallas för ett mål. Ett mål är vad vi vill förutsäga om framtida datapunkter, oavsett om det är en kategori eller en siffra.

Om du inte har några måldata, måste du gör. Du kan inte svara på din fråga utan den.

## <a name="reformulate-your-question"></a>Reformulate din fråga
Ibland kan du formulera om frågan för att få en mer användbar svar.

Frågan ”är den här data punkt A eller B”? förutsäger kategori (eller namnet eller etiketten) av något. För att besvara det vi använder en *klassificeringsalgoritm*.

Frågan ”hur mycket”? eller ”hur många”? beräknar ett belopp. Svara på det vi använder en *regressionsalgoritm*.

Om du vill se hur vi kan förvandla dem, ska vi titta på frågan ”vilka nyheter berättelse är den mest intressanta denna läsare”? Uppmanas du att ange en förutsägelse av ett val bland många möjligheter – med andra ord ”är den här A eller B eller C eller D”? - och använder en klassificeringsalgoritm för.

Men den här frågan kan vara lättare att svara på om du formulera om den som ”hur intressanta är varje artikel i listan till denna läsare”? Nu kan du ge varje artikel ett numeriskt värde och sedan är det enkelt att identifiera den högsta bedömning artikeln. Det här är att formulera om av klassificering-fråga till en regression fråga eller hur mycket?

![Reformulate din fråga. Klassificering fråga kontra regression fråga.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hur du ställa en fråga är en ledtråd till vilken algoritmen kan ge dig ett svar.

Du hittar att vissa familjer av algoritmer - som de i vårt exempel på berättelse nyheter – nära relaterade. Du kan reformulate din fråga om du vill använda den algoritm som ger dig mest användbara svaret.

Men viktigast av allt, och Ställ sharp frågan - fråga som du kan svara på med data. Och kontrollera att du har rätt data att besvara den.

Vi har pratat om några grundläggande principer för att ställa en fråga som du kan svara på med data.

Glöm inte att checka ut andra videor i ”Data datavetenskap för nybörjare” från Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Nästa steg
* [Prova en första dataexperiment med Machine Learning Studio](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](what-is-machine-learning.md)
