---
title: "5 datavetenskap frågor - datavetenskap för nybörjare - Azure Machine Learning | Microsoft Docs"
description: "Datavetenskap Lär för är nybörjare grundläggande koncept i 5 kort video, från och med 5 frågor datavetenskap svar. Från Azure Machine Learning."
keywords: "Gör datavetenskap, datavetenskap nybörjare, datavetenskap för nybörjare, datavetenskap grunderna, datavetenskap frågor, datavetenskap video, datavetenskap introduktion"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: a01f93ee-01eb-4afe-abbd-cfa035c119b0
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: 0482a680999e58b8be45334c9ae620b6b37ac273
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Datavetenskap för nybörjare, video 1: Fem frågor och svar om datavetenskap
Få en snabb introduktion till datavetenskap från *datavetenskap för nybörjare* i fem kort videor från en övre data forskare. Dessa videorna finns grundläggande men användbart om du är intresserad av att göra datavetenskap eller du arbetar med data forskare.

Den här första videon är om vilka typer av frågor som kan svara på datavetenskap. Titta på alla för att få ut mesta möjliga av serien. [Gå till listan över videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Datavetenskap för nybörjare* är en snabb introduktion till datavetenskap tar cirka 25 minuter totalt. Checka ut alla fem videor:

* Video 1: 5 frågor datavetenskap svar
* Video 2: [är data som är redo för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min. 56 sek)*
* Video 3: [Ställ en fråga som du kan svara med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: [förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min. 42 sek)*
* Video 5: [kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Betyg: 5 frågor datavetenskap svar
Hej! Välkommen till videoserie *datavetenskap för nybörjare*.

Datavetenskap kan det kännas avskräckande, så jag Lär grunderna här utan formler eller datorn programming språket.

I den här första videon kommer vi ”5 frågor datavetenskap svar”.

Datavetenskap använder och -namn (även kallat kategorier eller etiketter) för att förutsäga svar på frågor.

Det kan Överraska dig, men *finns bara fem frågor som datavetenskap svar*:

* Är den här A eller B?
* Är detta underligt?
* Hur mycket – eller – hur många?
* Hur detta organiseras?
* Vad ska jag göra sedan?

Var och en av de här frågorna har besvarats av en separat familj av machine learning-metoder, kallas algoritmer.

Det är bra att tänka på att en algoritm som ett recept och dina data efter beståndsdelarna. En algoritm som beskriver hur du kombinerar och blanda data för att få ett svar. Datorer som fungerar som en mixer. De kan utföra de flesta av det tunga arbetet med algoritmen för dig och de gör det ganska snabbt.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Fråga 1: Är den här A eller B? använder klassificering algoritmer
Vi börjar med frågan: är den här A eller B?

![Algoritmer för klassificering: är den här A eller B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Den här algoritmfamiljen kallas tvåklass klassificering.

Det är användbart för alla frågor som har två möjliga svar.

Exempel:

* Den här däck misslyckas i nästa 1 000 mil: Ja eller Nej?
* Detta leder i flera kunder: en $5 kuponger eller en 25% rabatt?

Den här frågan kan också omformulerad med fler än två alternativ: är den här A eller B- eller C- eller D, etc.?  Detta kallas för multiklass-baserad klassificering och det är användbart när du har flera – eller flera tusen – möjliga svar. Multiklass-baserad klassificering väljer det mest sannolika.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Fråga 2: Är detta underligt? använder algoritmer för identifiering av avvikelseidentifiering
Nästa fråga datavetenskap kan besvara är: är den här konstigt ut? Den här frågan besvaras av en familj av algoritmer som kallas avvikelseidentifiering.

![Algoritmer för identifiering av avvikelseidentifiering: är den här konstigt ut?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Om du har ett kreditkort, har du redan utnyttjat avvikelseidentifiering. Företaget kreditkort analyserar dina köp-mönster, så att de kan meddela dig att eventuellt bedrägeriförsök. Avgifter som är ”underligt” kan vara ett inköp på ett arkiv där du normalt inte handla eller köpa ett ovanligt dyra objekt.

Den här frågan kan vara användbart i många olika sätt. Exempel:

* Om du har en bil med trycket mätare kanske du vill veta: är den här trycket mätaren läsning normal?
* Om du övervaka internet, skulle du vill veta: är det här meddelandet från internet typisk?

Avvikelseidentifiering flaggor oväntat eller ovanliga händelser eller beteenden. Den ger ledtrådar var du ska leta efter problem.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Fråga 3: Hur mycket? eller hur många? använder regression algoritmer
Maskininlärning kan också förutsäga svaret på hur mycket? eller hur många? Algoritmen familj som svar på den här frågan kallas regression.

![Regression algoritmer: hur mycket? eller hur många?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regression algoritmer göra numeriska förutsägelser som:

* Vad temperaturen blir nästa tisdag?  
* Vad är min fjärde kvartal försäljning?

De hjälper besvara varje fråga som ber om ett tal.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Fråga 4: Hur detta organiseras? använder kluster algoritmer
De två sista frågorna är nu lite mer avancerade.

Ibland vill du att förstå strukturen för en datauppsättning - hur organiseras detta? Du har inte exempel som du redan känner till resultat för den här frågan.

Det finns många olika sätt att tease ut strukturen för data. En metod som kluster. Den separerar data till fysiska ”samlar ihop”, för enklare tolkning. Med klustring finns det inget en rätt svar.

![Klustring algoritmer: hur organiseras detta?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Vanliga exempel klustring frågor är:

* Vilka visningsprogram att samma typ av filmer?
* Vilka skrivarmodeller inte på samma sätt?

Förstå hur data ska ordnas du bättre förstå - och förutsäga - beteenden och händelser.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Fråga 5: Vad ska jag göra nu? använder förstärkning learning algoritmer
Senaste fråga – vad ska jag göra nu? – använder en familj av algoritmer som kallas förstärkning learning.

Förstärkning learning har inspirerat av hur hjärna för mus och människor reagera på straff och fördelar. Dessa algoritmer dig från resultaten och avgöra nästa åtgärd.

Normalt passar förstärkning learning bra för automatiserade system som har att göra många små beslut utan mänsklig vägledning.

![Förstärkning Learning algoritmer: Vad ska jag göra sedan?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Svarar på frågor är alltid om vilka åtgärder som bör vidtas - vanligtvis av en dator eller en robot. Exempel är:

* Om jag har ett system för ett hus temperatur: Justera temperaturen eller lämna den där det är?  
* Om jag en egen intresseväckande bil: vid ett gult ljus bromsa eller påskynda?  
* För en robot vakuum: Behåll vacuuming eller gå tillbaka till stationen som laddas?

Förstärkning learning algoritmer samla in data som de go learning från pröva.

Så att den it - kan besvara den 5 datavetenskap för frågor.

## <a name="next-steps"></a>Nästa steg
* [Försök med ett första datavetenskap experiment i Machine Learning Studio](create-experiment.md)
* [Få en introduktion till Maskininlärning på Microsoft Azure](what-is-machine-learning.md)
