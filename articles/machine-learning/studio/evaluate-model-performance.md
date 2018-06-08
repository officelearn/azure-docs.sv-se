---
title: Utvärdera modellen prestanda i Machine Learning | Microsoft Docs
description: Beskriver hur du utvärdera modellen prestanda i Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: bb49fd2fe7f72e211fbbda7cffdd2308c2c36fba
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834241"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>Så här utvärderar du modellens prestanda i Azure Machine Learning
Den här artikeln visar hur du utvärderar prestanda för en modell i Azure Machine Learning Studio och ger en kort förklaring av de tillgängliga måtten för den här uppgiften. Tre vanliga scenarier för övervakad inlärning visas: 

* Regression
* binär klassificering 
* multiklass-baserad klassificering

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Utvärderar prestanda för en modell är en grundläggande steg i vetenskap av data. Anger hur lyckade resultat (förutsägelser) i en dataset har av en tränad modell. 

Azure Machine Learning stöder modellen utvärdering via två av dess huvudsakliga moduler för maskininlärning: [utvärdera modell] [ evaluate-model] och [kontrolleras mot modellen] [ cross-validate-model]. Dessa moduler kan du se hur modellen presterar i ett antal mätvärden som är vanliga i machine learning och statistik.

## <a name="evaluation-vs-cross-validation"></a>Utvärderingen vs. Mellan verifiering
Utvärdering och mellan validering är standard sätt att mäta prestanda i din modell. De båda generera utvärdering mått som du kan kontrollera eller jämföra med de andra modeller.

[Utvärdera modellen] [ evaluate-model] förväntar sig en poängsatta dataset som indata (eller 2 om du vill jämföra resultat med 2 olika modeller). Det innebär att du behöver träna modellen med hjälp av den [träna modell] [ train-model] modulen och göra förutsägelser på vissa datauppsättningen med den [Poängmodell] [ score-model]modulen innan du kan utvärdera resultaten. Utvärderingen baseras på poängsatta etiketter/troliga tillsammans med true etiketter som matats ut av den [Poängmodell] [ score-model] modul.

Du kan också kan du använda mellan verifieringen för att utföra ett antal train poäng utvärdera åtgärder (10 gånger) automatiskt på olika delar av indata. Indata är uppdelad i 10 delar, där är ett reserverat för testning, och andra 9 för utbildning. Den här processen upprepas 10 gånger och utvärdering mått är ett genomsnitt. På så sätt kan bestämma hur väl en modell skulle generalisera till nya datamängder. Den [kontrolleras mot modellen] [ cross-validate-model] modulen hämtar i ett omdöme modellen och vissa märkt datamängd och matar ut utvärderingsresultaten av 10 gånger, förutom genomsnittlig resultaten.

I följande avsnitt kommer skapa enkla regression och klassificering modeller och utvärdera deras prestanda med hjälp av både den [utvärdera modell] [ evaluate-model] och [kontrolleras mot modellen ] [ cross-validate-model] moduler.

## <a name="evaluating-a-regression-model"></a>Utvärdera en regressionsmodell
Förutsätter vi vill förutsäga priset för en bil användningen av vissa funktioner, till exempel dimensioner, hästkrafter, motorn specifikationer och så vidare. Det här är en typisk regression problem där en målvariabel (*pris*) är en kontinuerlig numeriskt värde. Vi kan passa in en enkel linjär regressionsmodell som, med funktionen värdena för en viss bil kan förutsäga priset på den bilen. Den här regressionsmodell kan användas för att samla in samma datamängd vi tränats på. När vi har beräknade priser för alla bilarna kan utvärderar vi prestanda för modellen genom att titta på hur mycket förutsägelser avvika från de faktiska priserna i genomsnitt. För att illustrera detta vi använder den *Automobile price data (Raw) dataset* tillgängliga i den **sparade datauppsättningar** avsnitt i Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler i din arbetsyta i Azure Machine Learning Studio:

* Bil price data (Raw)
* [Linjär Regression][linear-regression]
* [Träningsmodell][train-model]
* [Poängsätta modell][score-model]
* [Utvärdera modellen][evaluate-model]

Ansluta portarna som visas nedan i figur 1 och ange kolumnen etiketten för den [Träningsmodell] [ train-model] modulen *pris*.

![Utvärdera en regressionsmodell](./media/evaluate-model-performance/1.png)

Bild 1. Utvärderar en regressionsmodell.

### <a name="inspecting-the-evaluation-results"></a>Kontrollera utvärderingsresultaten
När du har kört experimentet som du kan klicka på utdataporten för den [utvärdera modell] [ evaluate-model] modulen och välj *visualisera* att se utvärderingsresultaten. De utvärdering mätvärdena som är tillgänglig för regression modeller är: *medelvärdet av absoluta fel*, *rot innebär absoluta fel*, *relativa absoluta fel*,  *Relativ kvadrat fel*, och *Bestämningskoefficienten*.

Termen ”error” här representerar skillnaden mellan det förväntade värdet och det verkliga värdet. Det absoluta värdet eller kvadraten av denna skillnad beräknas vanligtvis att samla in den totala storleken på fel i alla instanser som skillnaden mellan värdena förutsagda och true får vara negativt i vissa fall. Fel mått mäta förutsägbar prestanda för en regressionsmodell vad gäller avvikelse medelvärdet för dess förutsägelser från värdet true. Nedre felvärdena betyder modellen är exaktare i att göra förutsägelser. Ett övergripande fel mått 0 betyder att modellen passar data perfekt.

Bestämningskoefficienten, som även kallas R kvadrat är också ett standardiserat sätt att mäta hur väl modellen passar data. Det kan tolkas som del av variationen beskrivs av modellen. En högre andel är bättre i detta fall där 1 anger en perfekt passning.

![Linjär Regression utvärdering mått](./media/evaluate-model-performance/2.png)

Figur 2. Linjär Regression utvärdering mått.

### <a name="using-cross-validation"></a>Med mellan verifiering
Som tidigare nämnts kan du utföra upprepade utbildning, poäng och utvärderingar automatiskt med hjälp av den [kontrolleras mot modellen] [ cross-validate-model] modul. Allt du behöver i det här fallet är en datamängd, ett omdöme modell och en [kontrolleras mot modellen] [ cross-validate-model] modul (se figuren nedan). Observera att du måste ange etikettkolumnen till *pris* i den [kontrolleras mot modellen] [ cross-validate-model] modulens egenskaper.

![Validera en regressionsmodell mellan](./media/evaluate-model-performance/3.png)

Bild 3. Cross-validerar en regressionsmodell.

När du har kört experimentet, du kan inspektera utvärderingsresultaten genom att klicka på den högra utdataporten för den [kontrolleras mot modellen] [ cross-validate-model] modul. Detta ger en detaljerad vy av mätvärden för varje iteration (vikt) och genomsnittlig resultatet av varje mått (bild 4).

![Korsvalidering resultatet av en regressionsmodell](./media/evaluate-model-performance/4.png)

Bild 4. Korsvalidering resultaten av en regressionsmodell.

## <a name="evaluating-a-binary-classification-model"></a>Utvärdering av en modell för binär klassificering
I ett scenario med binär klassificering, en målvariabel har bara två möjliga resultat, till exempel: {0, 1} eller {FALSKT, SANT}, {negativt, positivt}. Anta att du får en datauppsättning för vuxna anställda med några demografisk och anställningen variabler och du uppmanas att förutsäga intäkter-nivå, en binär variabel med värdena {”< = 50K” ”, > 50K”}. Med andra ord negativt klassen representerar anställda som gör mindre än eller lika med 50K per år och positiva klassen representerar alla anställda. I scenariot regression vi tränar en modell, poängsätta vissa data och utvärdera resultaten. Den största skillnaden är valet av mått som beräknar Azure Machine Learning och utdata. För att illustrera intäkter nivån förutsägelse scenario, kommer vi att använda den [vuxna](http://archive.ics.uci.edu/ml/datasets/Adult) dataset för att skapa ett experiment i Azure Machine Learning och utvärderar prestanda för en två-klass logistic regressionsmodell, en binär som används ofta klassificerare.

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler i din arbetsyta i Azure Machine Learning Studio:

* Vuxna inventering intäkter binär klassificering dataset
* [Two-Class Logistic Regression][two-class-logistic-regression]
* [Träningsmodell][train-model]
* [Poängsätta modell][score-model]
* [Utvärdera modellen][evaluate-model]

Ansluta portarna som visas nedan i figur 5 och ange kolumnen etiketten för den [Träningsmodell] [ train-model] modulen *intäkter*.

![Utvärdering av en modell för binär klassificering](./media/evaluate-model-performance/5.png)

Bild 5. Utvärderar en binär klassificering modell.

### <a name="inspecting-the-evaluation-results"></a>Kontrollera utvärderingsresultaten
När du har kört experimentet som du kan klicka på utdataporten för den [utvärdera modell] [ evaluate-model] modulen och välj *visualisera* att se utvärderingsresultaten (bild 7). De utvärdering mätvärdena som är tillgänglig för binär klassificering modeller är: *noggrannhet*, *Precision*, *återkalla*, *F1 poäng*, och  *AUC*. Dessutom modulen matar ut en förvirring matris som visar antalet positiva identifieringar som SANT, FALSKT negativ, falska positiva identifieringar och SANT negativ samt *ROC*, *Precision/återkalla*, och  *Lyfter* kurvor.

Precisionen är helt enkelt andelen korrekt klassificerad instanser. Det är vanligtvis den första mått som du tittar på när du utvärderar en klassificerare. När testdata är dock obalanserade (där de flesta av instanserna tillhöra en av klasserna) och du är intresserad av mer prestanda på någon av klasserna inte noggrannhet verkligen avbilda en klassificerare effektivitet. I scenario intäkter nivån klassificering förutsätter att du testar på vissa data där 99% av instanserna representerar personer som får mindre än eller lika med 50K per år. Det är möjligt att uppnå en 0.99 noggrannhet genom att förutsäga klassen ”< = 50K” för alla instanser. I det här fallet visas klassificeraren att utföra en övergripande bra, men i själva verket det går inte att klassificera alla high-income enskilda användare (1%) korrekt.

Därför är det bra att beräkna ytterligare mått som samlar in mer specifika aspekter av utvärderingen. Innan du fortsätter till information om dessa mått är det viktigt att förstå matrisen förvirring för en binär klassificering utvärderingen. Klassen etiketter i träningsmängden kan ha endast 2 möjliga värden som vi vanligtvis refererar till som positivt eller negativt. Positiva och negativa instanserna som en klassificerare beräknar korrekt kallas true positiva identifieringar (TP) och true negativ (TN). På liknande sätt kallas felaktigt klassificerad instanser falska positiva identifieringar (RP) och FALSKT negativ (FN). Matrisen förvirring är helt enkelt en tabell som visar antalet instanser som faller under följande 4 kategorier. Azure Machine Learning beslutar automatiskt som två klasser i datauppsättningen är positivt klass. Om klassen etiketter är Boolean eller heltal, har namngivna instanser 'true' eller '1' tilldelats klassen positivt. Om etiketter är strängar, som i fallet med intäkter datauppsättningen etiketterna sorteras alfabetiskt och den första nivån väljs vara negativa klassen medan den andra nivån är positivt klass.

![Binär klassificering förvirring matris](./media/evaluate-model-performance/6a.png)

Bild 6. Binär klassificering förvirring matris.

Gå tillbaka till klassificeringsproblem intäkter, vill vi be flera utvärderingsfrågor som hjälper oss att förstå prestanda för klassificeraren används. En mycket naturliga frågan är: ' utanför till personer som modellen förutsade till att tjäna > 50 K (TP + RP), hur många har klassificerats på rätt sätt (TP) ”? Den här frågan kan lösas genom att titta på den **Precision** av modellen, vilket är andelen av positiva identifieringar som klassificerats korrekt: TP/(TP+FP). En annan vanlig fråga är ”utanför alla hög med anställda med intäkter > 50 k (TP + FN), hur många klassificeraren klassificera korrekt (TP)”. Detta är den **återkalla**, eller true positiva satsen: TP/(TP+FN) för klassificeraren. Du kan se att det finns en uppenbara kompromiss mellan precision och återkalla. Till exempel ges en relativt belastningsutjämnade dataset en klassificerare som beräknar främst positivt instanser skulle ha en hög återkallning, men en snarare Låg precision så många negativt instanser skulle klassificeras som resulterar i ett stort antal falska positiva identifieringar. Om du vill se en rityta av hur dessa två mått varierar, kan du klicka på ' PRECISION/ÅTERKALLA-kurvan i utdata för utvärdering resultatsidan (övre vänstra del av bild 7).

![Utvärderingsresultat av binär klassificering](./media/evaluate-model-performance/7.png)

Bild 7. Utvärderingsresultat av binär klassificering.

En annan relaterade mått som används ofta är den **F1 poäng**, som tar både precision och återkalla i beräkningen. Det är det harmoniska medelvärdet av de här 2 måtten och beräknas som sådana: F1 = 2 (precision x återkalla) / (precision + återkalla). F1-resultatet är ett bra sätt att sammanfatta utvärdering i ett tal, men det är alltid en bra idé att titta på både precision och återkalla tillsammans för att bättre förstå hur en klassificerare fungerar.

Dessutom kan en inspektera true positiva satsen kontra falska positiva hastigheten i den **mottagare operativsystem egenskap (ROC)** kurva och motsvarande **område Under i kurvan (AUC)** värde. Närmare kurvan till det övre vänstra hörnet är bättre prestanda för den klassificerare (som är att maximera true positivt hastighet och minimerar den falska positiva hastigheten). Kurvor som närmar sig diagonal ut resultatet från klassificerare tenderar att göra förutsägelser som närmar sig slumpmässiga att gissa.

### <a name="using-cross-validation"></a>Med mellan verifiering
Som i exemplet regression utföra vi mellan verifiering för att träna, poäng och utvärdera olika delmängder av data automatiskt upprepade gånger. På liknande sätt kan vi kan använda den [kontrolleras mot modellen] [ cross-validate-model] modul, ett omdöme logistic regressionsmodell och en datamängd. Etikettkolumnen måste anges till *intäkter* i den [kontrolleras mot modellen] [ cross-validate-model] modulens egenskaper. När du kör experimentet och klicka på höger utgående porten för den [kontrolleras mot modellen] [ cross-validate-model] modulen, vi kan se måttvärden för binär klassificering för varje vikning dessutom medelvärdet och standardavvikelsen för var och en. 

![Validera en binär klassificering modell mellan](./media/evaluate-model-performance/8.png)

Figur 8. Cross-validerar en binär klassificering modell.

![Korsvalideringsresultaten av en binär klassificerare](./media/evaluate-model-performance/9.png)

Bild 9. Korsvalideringsresultaten av en binär klassificerare.

## <a name="evaluating-a-multiclass-classification-model"></a>Utvärdering av en modell för multiklass-baserad klassificering
Vi använder den populära i experimentet [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") datauppsättning som innehåller instanser av 3 olika typer (klasser) av iris anläggningen. Det finns 4 funktionen värden (sepal längd och bredd och bladets längd och bredd) för varje instans. I de föregående försök vi tränas och testas modeller som använder samma datauppsättningar. Här kan vi använder den [dela Data] [ split] modulen skapa 2 delmängder av data, träna på först och poängsätta och utvärdera på andra. Iris dataset är allmänt tillgänglig på den [UCI Machine Learning databasen](http://archive.ics.uci.edu/ml/index.html), och kan hämtas med hjälp av en [importera Data] [ import-data] modul.

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler i din arbetsyta i Azure Machine Learning Studio:

* [Importera Data][import-data]
* [Multiclass beslut skog][multiclass-decision-forest]
* [Dela Data][split]
* [Träningsmodell][train-model]
* [Poängsätta modell][score-model]
* [Utvärdera modellen][evaluate-model]

Ansluta portarna som visas nedan i figur 10.

Ange etikett kolumnindex för de [Träningsmodell] [ train-model] modulen till 5. Datamängden har ingen rubrikrad men vi vet att klassen etiketter är i femte kolumnen.

Klicka på den [importera Data] [ import-data] modulen och ange den *datakällan* egenskapen *Webbadress via HTTP*, och *URL* till http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Ange del av instanser som ska användas för den [dela Data] [ split] modul (0,7 till exempel).

![Utvärdering av en multiklass-baserad klassificerare](./media/evaluate-model-performance/10.png)

Bild 10. Utvärdering av en multiklass-baserad klassificerare

### <a name="inspecting-the-evaluation-results"></a>Kontrollera utvärderingsresultaten
Kör experimentet och klicka på utdataporten för [utvärdera modell][evaluate-model]. Utvärderingsresultaten visas i form av en förvirring matris i det här fallet. Matrisen visar det faktiska kontra förväntade instanser för alla 3 klasser.

![Utvärderingsresultat av multiklass-baserad klassificering](./media/evaluate-model-performance/11.png)

Figur 11. Utvärderingsresultat av multiklass-baserad klassificering.

### <a name="using-cross-validation"></a>Med mellan verifiering
Som tidigare nämnts kan du utföra upprepade utbildning, poäng och utvärderingar automatiskt med hjälp av den [kontrolleras mot modellen] [ cross-validate-model] modul. Du behöver en datamängd, ett omdöme modell och en [kontrolleras mot modellen] [ cross-validate-model] modul (se figuren nedan). Igen måste du ange etikettkolumnen för den [kontrolleras mot modellen] [ cross-validate-model] modul (kolumnindex 5 i det här fallet). När du kör experimentet och klicka på höger utgående porten för den [kontrolleras mot modellen][cross-validate-model], du kan inspektera måttvärden för varje vikning samt avvikelse medelvärde och standard. Mått som visas här är samma som de som beskrivs i fallet binär klassificering. Observera dock att i multiklass-baserad klassificering true positiva identifieringar/negativ och falska positiva identifieringar/negativ görs genom att räkna på grundval av per klass, eftersom det finns ingen övergripande positivt eller negativt klass. Till exempel när datoranvändning precision eller återkallas av klassen 'Iris setosa', antas det att det här är klassen positiva och alla andra som negativt.

![Verifiera mellan en modell för multiklass-baserad klassificering](./media/evaluate-model-performance/12.png)

Figur 12. Cross-validering av en modell för multiklass-baserad klassificering.

![Korsvalideringsresultaten av en modell för multiklass-baserad klassificering](./media/evaluate-model-performance/13.png)

Figur 13. Korsvalideringsresultaten av en modell för multiklass-baserad klassificering.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/

