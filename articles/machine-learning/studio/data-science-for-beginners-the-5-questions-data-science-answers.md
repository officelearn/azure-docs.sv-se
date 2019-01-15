---
Rubrik: Datavetenskap för nybörjare titleSuffix: Beskrivning av Azure Machine Learning Studio: Datavetenskap Lär för nybörjare är grundläggande begrepp i 5 korta videor från och med den 5 frågor och svar om datavetenskap. Från Azure Machine Learning.
tjänster: machine learning ms.service: machine learning ms.component: studio ms.topic: artikel

author: garyericson ms.author: garye ms.custom: seodec18 ms.date: 01/03/2018
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Datavetenskap för nybörjare, video 1: 5 frågor och svar om datavetenskap
Få en snabbintroduktion i datakunskap från *datavetenskap för nybörjare* i fem korta filmer från övre dataexpert. Dessa videoklipp är basic men användbart, oavsett om du är intresserad av i datavetenskap eller om du arbetar med dataexperter.

Den här första videon handlar om typer av frågor som kan svara på datavetenskap. Titta på alla för att få ut det mesta av serien. [Gå till listan över videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Datavetenskap för nybörjare* är en snabb introduktion till data science tar cirka 25 minuter total. Kolla in alla fem videor:

* Video 1: 5 frågor och svar om datavetenskap
* Video 2: [Är dina data klara för datavetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: [Ställ en fråga som du kan svara på med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: [Förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [Kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Avskriften: 5 frågor och svar om datavetenskap
Hej! Välkommen till videoserien *datavetenskap för nybörjare*.

Datavetenskap kan det kännas avskräckande, så jag kommer att introducera grunderna här utan formler eller datorn programming jargong.

I den här första videon ska vi tala om ”5 frågor och svar om datavetenskap”.

Datavetenskap använder och -namn (även kallat kategorier eller etiketter) för att förutsäga svar på frågor.

Det kan Överraska dig, men *finns bara fem frågor och svar om de datavetenskap*:

* Är den här A eller B?
* Är detta underligt?
* Hur mycket – eller – hur många?
* Hur organiseras detta?
* Vad ska jag göra härnäst?

Var och en av dessa frågor besvaras av en separat serie machine learning-metoder, kallade algoritmer.

Nu är det bra att tänka på en algoritm som ett recept och dina data som ingredienserna. En algoritm talar om hur du kombinerar och blanda data för att få ett svar. Datorer som fungerar som en blender. De kan utföra de flesta av arbetet med algoritmen för dig och de gör det ganska snabbt.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Fråga 1: Är den här A eller B? använder klassificering algoritmer
Låt oss börja med frågan: Är den här A eller B?

![Algoritmer för klassificering: Är den här A eller B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Den här serien av algoritmer kallas tvåklassförhöjt klassificering.

Det är användbart för alla frågor som har bara två möjliga svar.

Exempel:

* Misslyckas den här däck i nästa 1 000 mil: Ja eller nej?
* Detta leder i fler kunder: en kupong för 5 USD eller en rabatt på 25%?

Den här frågan kan också omformulerad med fler än två alternativ: Är den här A eller B eller C eller D, osv.?  Detta kallas multiklass-baserad klassificering och det är användbart när det finns flera – eller flera tusen – möjliga svar. Multiklass-baserad klassificering väljer det mest sannolika.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Fråga 2: Är detta underligt? använder algoritmer för identifiering av avvikelser
Nästa fråga kan svara på datavetenskap är: Är detta underligt? Den här frågan besvaras av en familj av algoritmer som kallas avvikelseidentifiering.

![Algoritmer för identifiering av avvikelser: Är detta underligt?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Om du har ett kreditkort, har du redan utnyttjat avvikelseidentifiering. Ditt kreditkortföretag analyserar dina köp mönster, så att de kan varna dig om möjligt bedrägeri. Som har ”underligt” kan vara ett inköp i en store där du inte handla normalt eller köpa ett ovanligt dyr objekt.

Den här frågan kan vara användbart i många olika sätt. Exempel:

* Om du har en bil med hög belastning mätare, kanske du vill veta: Den här mätaren tryck läser normala?
* Om du övervakar internet, skulle du kunna: Är det här meddelandet från internet typiskt?

Identifiering av avvikelser flaggar oväntade eller ovanliga händelser eller beteenden. Det ger ledtrådar att söka efter problem.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Fråga 3: Hur mycket? eller hur många? använder regression algoritmer
Machine learning kan också förutse svaret på hur mycket? eller hur många? Algoritm-familjen som besvarar frågan kallas regression.

![Regression algoritmer: Hur mycket? eller hur många?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regression algoritmer göra numeriska förutsägelser som:

* Vad temperaturen blir nästa tisdag?  
* Vad blir min fjärde kvartal försäljning?

De hjälper svar på alla frågor som frågar efter ett tal.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Fråga 4: Hur organiseras detta? använder klustring algoritmer
De två sista frågorna är nu lite mer avancerade.

Ibland vill du att förstå strukturen för en datauppsättning – hur organiseras detta? Du har inte exempel som du redan vet resultat för den här frågan.

Det finns många olika sätt att locka fram strukturen för data. En metod för klustring. Delar den upp data till naturlig ”samlar ihop”, för enklare tolkning. Med klustring finns inga en rätt svar.

![Klustring algoritmer: Hur organiseras detta?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Vanliga exempel på klustring frågor är:

* Vilka användare som samma typer av filmer?
* Vilka skrivarmodeller växlar på samma sätt?

Genom att förstå hur data sammanställs, kan du bättre förstå - och förutsäga - beteenden och händelser.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Fråga 5: Vad gör jag nu? använder förstärkt algoritmer
Senaste fråga – vad gör jag nu? – använder en familj av algoritmer som kallas förstärkande inlärning.

Förstärkande inlärning har INSPIRERAD av hur hjärnan för mus och människor svarar på straff och belöningar i gruppen. Dessa algoritmer Lär dig av resultat och bestämt dig för nästa åtgärd.

Förstärkande inlärning är vanligtvis ett bra alternativ för automatiserade system som har att göra många små beslut utan mänsklig vägledning.

![Förstärkt Learning-algoritmer: Vad ska jag göra härnäst?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Den svarar på frågor som är alltid om vilka åtgärder som vidtas - vanligtvis av en dator eller en robot. Några exempel:

* Om jag är ett system för temperatur för ett hus: Justera temperaturen eller lämna den där det är?  
* Om jag är en dataredaktör bil: Vid en gul ljus bromsa eller snabbare?  
* För en robot vakuum: Behåll vacuuming eller gå tillbaka till stationen som laddas?

Förstärkt learning-algoritmer samla in data när de går, utbildning från pröva.

Så det var allt – kan The 5 frågor för datavetenskap svara på.

## <a name="next-steps"></a>Nästa steg
* [Prova en första dataexperiment med Machine Learning Studio](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](what-is-machine-learning.md)
