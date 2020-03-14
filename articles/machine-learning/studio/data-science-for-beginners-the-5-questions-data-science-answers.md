---
title: Datavetenskap för nybörjare
titleSuffix: ML Studio (classic) - Azure
description: Datavetenskap Lär för nybörjare är grundläggande begrepp i 5 korta videor från och med den 5 frågor och svar om datavetenskap. Från Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: af0611e121228a587e159bd4e6529c807132d16d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204451"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Datavetenskap för nybörjare, video 1: Fem frågor och svar om datavetenskap
Få en snabb introduktion till data vetenskap från *data vetenskap för nybörjare* i fem korta videor från en topp data expert. Dessa videoklipp är basic men användbart, oavsett om du är intresserad av i datavetenskap eller om du arbetar med dataexperter.

Den här första videon handlar om typer av frågor som kan svara på datavetenskap. Titta på alla för att få ut det mesta av serien. [Gå till listan med videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Data vetenskap för nybörjare* är en snabb introduktion till data vetenskap med en total summa på 25 minuter. Kolla in alla fem videor:

* Video 1: 5 frågor och svar om datavetenskap
* Video 2: [är dina data klara för data vetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 SEK)*
* Video 3: [Ställ en fråga som du kan besvara med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 SEK)*
* Video 4: [förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 SEK)*
* Video 5: [Kopiera andras arbete för att göra data vetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 SEK)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Avskriften: 5 frågor och svar om datavetenskap
Hej! Välkommen till video seriens *data vetenskap för nybörjare*.

Datavetenskap kan det kännas avskräckande, så jag kommer att introducera grunderna här utan formler eller datorn programming jargong.

I den här första videon ska vi tala om ”5 frågor och svar om datavetenskap”.

Datavetenskap använder och -namn (även kallat kategorier eller etiketter) för att förutsäga svar på frågor.

Det kan bli överrasknings, men *det finns bara fem frågor som data vetenskaps svar*:

* Är den här A eller B?
* Är detta underligt?
* Hur mycket – eller – hur många?
* Hur organiseras detta?
* Vad ska jag göra härnäst?

Var och en av dessa frågor besvaras av en separat serie machine learning-metoder, kallade algoritmer.

Nu är det bra att tänka på en algoritm som ett recept och dina data som ingredienserna. En algoritm talar om hur du kombinerar och blanda data för att få ett svar. Datorer som fungerar som en blender. De kan utföra de flesta av arbetet med algoritmen för dig och de gör det ganska snabbt.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Fråga 1: Är den här A eller B? använder klassificering algoritmer
Låt oss börja med frågan: är den här A eller B?

![Algoritmer för klassificering: är den här A eller B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Den här serien av algoritmer kallas tvåklassförhöjt klassificering.

Det är användbart för alla frågor som har bara två möjliga svar.

Exempel:

* Misslyckas den här däck i nästa 1 000 mil: Ja eller Nej?
* Detta leder i fler kunder: en kupong för 5 USD eller en rabatt på 25%?

Den här frågan kan också omformulerad med fler än två alternativ: är den här A eller B eller C eller D, osv.?  Detta kallas multiklass-baserad klassificering och det är användbart när det finns flera – eller flera tusen – möjliga svar. Multiklass-baserad klassificering väljer det mest sannolika.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Fråga 2: Är detta underligt? använder algoritmer för identifiering av avvikelser
Nästa fråga kan svara på datavetenskap är: är den här konstigt? Den här frågan besvaras av en familj av algoritmer som kallas avvikelseidentifiering.

![Algoritmer för identifiering av avvikelser: är den här konstigt?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Om du har ett kredit kort har du redan fått till gång till avvikelse identifiering. Ditt kreditkortföretag analyserar dina köp mönster, så att de kan varna dig om möjligt bedrägeri. Som har ”underligt” kan vara ett inköp i en store där du inte handla normalt eller köpa ett ovanligt dyr objekt.

Den här frågan kan vara användbart i många olika sätt. Exempel:

* Om du har en bil med hög belastning mätare, kanske du vill veta: är den här mätaren för hög belastning som läser normala?
* Om du övervakar Internet vill du veta: är det här meddelandet från Internet som standard?

Identifiering av avvikelser flaggar oväntade eller ovanliga händelser eller beteenden. Det ger ledtrådar att söka efter problem.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Fråga 3: Hur mycket? eller hur många? använder regression algoritmer
Machine learning kan också förutse svaret på hur mycket? eller hur många? Algoritm-familjen som besvarar frågan kallas regression.

![Regression algoritmer: hur mycket? eller hur många?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regression algoritmer göra numeriska förutsägelser som:

* Vad temperaturen blir nästa tisdag?  
* Vad blir min fjärde kvartal försäljning?

De hjälper svar på alla frågor som frågar efter ett tal.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Fråga 4: Hur detta organiseras? använder klustring algoritmer
De två sista frågorna är nu lite mer avancerade.

Ibland vill du att förstå strukturen för en datauppsättning – hur organiseras detta? I den här frågan har du inte några exempel på att du redan känner till resultat för.

Det finns många olika sätt att locka fram strukturen för data. En metod för klustring. Delar den upp data till naturlig ”samlar ihop”, för enklare tolkning. Med klustring finns inga en rätt svar.

![Klustring algoritmer: hur organiseras detta?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Vanliga exempel på klustring frågor är:

* Vilka användare som samma typer av filmer?
* Vilka skrivarmodeller växlar på samma sätt?

Genom att förstå hur data sammanställs, kan du bättre förstå - och förutsäga - beteenden och händelser.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Fråga 5: Vad gör jag nu? använder förstärkt algoritmer
Senaste fråga – vad gör jag nu? – använder en familj av algoritmer som kallas förstärkande inlärning.

Förstärkande inlärning har INSPIRERAD av hur hjärnan för mus och människor svarar på straff och belöningar i gruppen. Dessa algoritmer Lär dig av resultat och bestämt dig för nästa åtgärd.

Förstärkande inlärning är vanligtvis ett bra alternativ för automatiserade system som har att göra många små beslut utan mänsklig vägledning.

![Förstärkt algoritmer: Vad ska jag göra härnäst?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Den svarar på frågor som är alltid om vilka åtgärder som vidtas - vanligtvis av en dator eller en robot. Några exempel:

* Om jag har ett system för temperatur för ett hus: Justera temperaturen eller lämna den där det är?  
* Om jag är en dataredaktör bil: vid en gul ljus bromsa eller snabbare?  
* För en robot vakuum: Behåll vacuuming eller gå tillbaka till stationen som laddas?

Förstärkt learning-algoritmer samla in data när de går, utbildning från pröva.

Så det var allt – kan The 5 frågor för datavetenskap svara på.

## <a name="next-steps"></a>Nästa steg
* [Testa ett första data vetenskaps experiment med Machine Learning Studio (klassisk)](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
