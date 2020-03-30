---
title: Utvärdera modellens prestanda
titleSuffix: ML Studio (classic) - Azure
description: Lär dig hur du utvärderar modellprestanda i Azure Machine Learning Studio (klassiskt) och om de mått som är tillgängliga för den här uppgiften.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 3c041834b9ad191817cdf1380b0a75efc7639bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218144"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Så här utvärderar du modellprestanda i Azure Machine Learning Studio (klassiskt)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Den här artikeln visar hur du utvärderar prestanda för en modell i Azure Machine Learning Studio (klassisk) och ger en kort förklaring av de mått som är tillgängliga för den här uppgiften. Tre vanliga övervakade inlärningsscenarier presenteras: 

* Regression
* binär klassificering 
* klassificering av fleraklasser



Att utvärdera en modells prestanda är ett av kärnstegen i datavetenskapsprocessen. Det visar hur framgångsrik bedömning (förutsägelser) av en datauppsättning har av en tränad modell. 

Azure Machine Learning Studio (klassisk) stöder modellutvärdering genom två av dess viktigaste maskininlärningsmoduler: [Utvärdera modell][evaluate-model] och [korsval validera modell][cross-validate-model]. Med de här modulerna kan du se hur din modell fungerar i termer av ett antal mått som ofta används i maskininlärning och statistik.

## <a name="evaluation-vs-cross-validation"></a>Utvärdering kontra korsvalidering
Utvärdering och korsvalidering är standardsätt för att mäta modellens prestanda. De genererar båda utvärderingsmått som du kan granska eller jämföra med andra modeller.

[Utvärdera modell][evaluate-model] förväntar sig en poängsatt datauppsättning som indata (eller två om du vill jämföra prestanda för två olika modeller). Därför måste du träna din modell med hjälp av modulen [Tågmodell][train-model] och göra förutsägelser på vissa datauppsättningar med modulen [Poängmodell][score-model] innan du kan utvärdera resultaten. Utvärderingen baseras på poängmärkena/sannolikheterna tillsammans med de sanna etiketterna, som alla matas ut av modulen [Poängmodell.][score-model]

Alternativt kan du använda korsvalidering för att utföra ett antal tåg-score-utvärdera operationer (10 gånger) automatiskt på olika delmängder av indata. Indata är uppdelade i 10 delar, där en är reserverad för testning, och den andra 9 för utbildning. Den här processen upprepas 10 gånger och utvärderingsmåtten är i genomsnitt. Detta hjälper till att avgöra hur väl en modell skulle generalisera till nya datauppsättningar. [Modulen Cross-Validate Model][cross-validate-model] tar in en otränad modell och vissa märkta datauppsättning och matar ut utvärderingsresultaten för var och en av de 10 vecken, utöver de genomsnittliga resultaten.

I följande avsnitt kommer vi att bygga enkla regressions- och klassificeringsmodeller och utvärdera deras prestanda, med hjälp av både [utvärdera modell][evaluate-model] och [cross-validate modell][cross-validate-model] moduler.

## <a name="evaluating-a-regression-model"></a>Utvärdera en regressionsmodell
Anta att vi vill förutsäga en bils pris med hjälp av funktioner som dimensioner, hästkrafter, motorspecifikationer och så vidare. Detta är ett typiskt regressionsproblem, där målvariabeln (*priset*) är ett kontinuerligt numeriskt värde. Vi kan passa en linjär regressionsmodell som, med tanke på funktionen värden av en viss bil, kan förutsäga priset på den bilen. Den här regressionsmodellen kan användas för att få samma datauppsättning som vi tränade på. När vi har de förväntade bilpriserna kan vi utvärdera modellens prestanda genom att titta på hur mycket prognoserna avviker från de faktiska priserna i genomsnitt. För att illustrera detta använder vi *datauppsättningen Automobile price dataset (Raw)* som är tillgänglig i avsnittet **Sparade datauppsättningar** i Machine Learning Studio (klassisk).

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler på arbetsytan i Azure Machine Learning Studio (klassisk):

* Bil prisdata (Raw)
* [Linjär regression][linear-regression]
* [Träningsmodell][train-model]
* [Poängmodell][score-model]
* [Utvärdera modell][evaluate-model]

Anslut portarna enligt bild 1 och ställ in etikettkolumnen för [tågmodellmodulen][train-model] till *pris*.

![Utvärdera en regressionsmodell](./media/evaluate-model-performance/1.png)

Bild 1. Utvärdera en regressionsmodell.

### <a name="inspecting-the-evaluation-results"></a>Granska utvärderingsresultaten
När du har kört experimentet kan du klicka på utdataporten för modulen [Utvärdera modell][evaluate-model] och välja *Visualisera* för att se utvärderingsresultaten. De utvärderingsmått som är tillgängliga för regressionsmodeller är: *Genomsnittligt absolut fel*, *Rotmedel absolut fel,* *relativt absolut fel,* *relativt fyrkantigt fel*och *bestämningskoefficienten*.

Termen "fel" här representerar skillnaden mellan det förväntade värdet och det verkliga värdet. Det absoluta värdet eller kvadraten på den här skillnaden beräknas vanligtvis för att fånga den totala storleken på felet i alla instanser, eftersom skillnaden mellan det förväntade och sanna värdet kan vara negativ i vissa fall. Felmåtten mäter prediktiva prestanda för en regressionsmodell när det gäller den genomsnittliga avvikelsen för dess förutsägelser från de sanna värdena. Lägre felvärden innebär att modellen är mer exakt när det gäller att göra förutsägelser. Ett övergripande felmått på noll innebär att modellen passar data perfekt.

Bestämningskoefficienten, som också kallas R i kvadrat, är också ett standardsätt att mäta hur väl modellen passar data. Det kan tolkas som den andel av variationen som förklaras av modellen. En högre andel är bättre i detta fall, där 1 indikerar en perfekt passform.

![Linjär regressionsutvärderingsmått](./media/evaluate-model-performance/2.png)

Figur 2. Linjär regressionsutvärderingsmått.

### <a name="using-cross-validation"></a>Använda korsvalidering
Som tidigare nämnts kan du utföra upprepad utbildning, poängsättning och utvärderingar automatiskt med modulen [Korsvalningsmodell.][cross-validate-model] Allt du behöver i det här fallet är en datauppsättning, en otränad modell och en [cross-validate modell][cross-validate-model] modul (se bilden nedan). Du måste ange att etikettkolumnen ska *prissättas* i modulen [Korsvalida modell.][cross-validate-model]

![Korskore validera en regressionsmodell](./media/evaluate-model-performance/3.png)

Bild 3. Kors validera en regressionsmodell.

När du har kört experimentet kan du granska utvärderingsresultaten genom att klicka på den högra utdataporten för modulen [Korsval validera modell.][cross-validate-model] Detta ger en detaljerad vy över måtten för varje iteration (vikning) och de genomsnittliga resultaten för vart och ett av måtten (figur 4).

![Korsvalideringsresultat för en regressionsmodell](./media/evaluate-model-performance/4.png)

Bild 4. Korsvalideringsresultat av en regressionsmodell.

## <a name="evaluating-a-binary-classification-model"></a>Utvärdera en binär klassificeringsmodell
I ett binärt klassificeringsscenario har målvariabeln bara två möjliga utfall, till exempel :{0, 1} eller {false, true}, {negative, positive}. Anta att du får en datauppsättning av vuxna anställda med vissa demografiska variabler och sysselsättningsvariabler, och att du uppmanas att förutsäga inkomstnivån, en binär variabel med värdena {"<=50 K", ">50 K"}. Med andra ord representerar den negativa klassen de anställda som tjänar mindre än eller lika med 50 K per år, och den positiva klassen representerar alla andra medarbetare. Precis som i regressionsscenariot skulle vi träna en modell, få vissa data och utvärdera resultaten. Den största skillnaden här är valet av mått Azure Machine Learning Studio (klassiska) beräkningar och utdata. För att illustrera förutsägelsescenariot för inkomstnivå använder vi datauppsättningen [Vuxen](https://archive.ics.uci.edu/ml/datasets/Adult) för att skapa ett Studio-experiment (klassiskt) och utvärdera resultatet för en tvåklassig logistisk regressionsmodell, en vanlig binär klassificerare.

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler på arbetsytan i Azure Machine Learning Studio (klassisk):

* Vuxenräkning inkomst binär klassificering dataset
* [Logistic Regression med två klasser][two-class-logistic-regression]
* [Träningsmodell][train-model]
* [Poängmodell][score-model]
* [Utvärdera modell][evaluate-model]

Anslut portarna enligt bild 5 och ställ in etikettkolumnen för [tågmodellmodulen][train-model] till *inkomst*.

![Utvärdera en binär klassificeringsmodell](./media/evaluate-model-performance/5.png)

Bild 5. Utvärdera en binär klassificeringsmodell.

### <a name="inspecting-the-evaluation-results"></a>Granska utvärderingsresultaten
När du har kört experimentet kan du klicka på utdataporten i modulen [Utvärdera modell][evaluate-model] och välja *Visualisera* för att se utvärderingsresultaten (bild 7). Utvärderingsmåtten som är tillgängliga för binära klassificeringsmodeller är: *Noggrannhet*, *Precision*, *Återkallelse*, *F1-poäng*och *AUC*. Dessutom matar modulen ut en förvirringsmatris som visar antalet sanna positiva, falska negativ, falska positiva och sanna negativ, samt *ROC-,* *Precision/Recall-* och *Liftkurvor.*

Noggrannhet är helt enkelt andelen korrekt klassificerade instanser. Det är oftast det första måttet du tittar på när du utvärderar en klassificerare. Men när testdata är obalanserad (där de flesta instanser tillhör en av klasserna), eller om du är mer intresserad av prestanda på någon av klasserna, tar noggrannheten inte riktigt upp effektiviteten hos en klassificerare. I klassificeringsscenariot för inkomstnivå antar du att du testar på vissa data där 99 % av instanserna representerar personer som tjänar mindre än eller lika med 50 000 per år. Det är möjligt att uppnå en 0,99 noggrannhet genom att förutsäga klassen "<= 50K" för alla instanser. Klassificeraren i detta fall verkar göra ett bra jobb totalt sett, men i verkligheten misslyckas med att klassificera någon av de höginkomsttagare (1%) Korrekt.

Därför är det bra att beräkna ytterligare mått som samlar in mer specifika aspekter av utvärderingen. Innan du går in på detaljerna i sådana mått är det viktigt att förstå förvirringsmatrisen för en binär klassificeringsutvärdering. Klassetiketterna i träningsuppsättningen kan bara ta på sig två möjliga värden, som vi brukar kalla positiva eller negativa. De positiva och negativa förekomster som en klassificerare förutsäger korrekt kallas sanna positiva (TP) respektive sanna negativ (TN). På samma sätt kallas de felaktigt klassificerade instanserna falska positiva identifieringar (FP) och falska negativ (FN). Förvirringsmatrisen är helt enkelt en tabell som visar antalet instanser som faller under var och en av dessa fyra kategorier. Azure Machine Learning Studio (klassisk) bestämmer automatiskt vilken av de två klasserna i datauppsättningen som är den positiva klassen. Om klassetiketterna är booleska eller heltal tilldelas den "sanna" eller "1" märkta instanserna den positiva klassen. Om etiketterna är strängar, till exempel med inkomstdatauppsättningen, sorteras etiketterna alfabetiskt och den första nivån väljs till den negativa klassen medan den andra nivån är den positiva klassen.

![Matris för förväxling av binär klassificering](./media/evaluate-model-performance/6a.png)

Bild 6. Matris för förväxling av binär klassificering.

Att gå tillbaka till inkomstklassificeringsproblemet, skulle vi vilja ställa flera utvärderingsfrågor som hjälper oss att förstå resultatet av klassificeraren som används. En naturlig fråga är: "Av de individer som modellen förutspådde att tjäna >50 K (TP + FP), hur många klassificerades korrekt (TP)?" Denna fråga kan besvaras genom att titta på **precisionen** i modellen, vilket är andelen positiva som klassificeras korrekt: TP/(TP+FP). En annan vanlig fråga är "Av alla höginkomsttagare med inkomst >50k (TP + FN), hur många klassificerade klassificera korrekt (TP)". Detta är faktiskt **Recall**, eller den verkliga positiva hastigheten: TP/(TP+FN) för klassificeraren. Du kanske märker att det finns en uppenbar kompromiss mellan precision och återkallande. Med tanke på en relativt balanserad datauppsättning skulle en klassificerare som förutspår mestadels positiva instanser ha en hög återkallelse, men en ganska låg precision eftersom många av de negativa instanserna skulle felklassificeras vilket resulterar i ett stort antal falska positiva identifieringar. Om du vill se en ritplan över hur dessa två mätvärden varierar kan du klicka på **precisions-/återkallelsekurvan** på sidan för utvärderingsresultat (den övre vänstra delen av figur 7).

![Utvärderingsresultat för binär klassificering](./media/evaluate-model-performance/7.png)

Bild 7. Utvärderingsresultat för binär klassificering.

Ett annat relaterat mått som ofta används är **F1-poängen**, som tar hänsyn till både precision och återkallande. Det är det harmoniska medelvärdet av dessa två mått och beräknas som sådan: F1 = 2 (precision x recall) / (precision + återkallande). F1-poängen är ett bra sätt att sammanfatta utvärderingen i ett enda nummer, men det är alltid en bra idé att titta på både precision och minns tillsammans för att bättre förstå hur en klassificerare beter sig.

Dessutom kan man inspektera den verkliga positiva hastigheten jämfört med den falska positiva hastigheten i **mottagarens funktionsegenskaps-kurva (ROC)** och motsvarande **område under kurvan (AUC)** värde. Ju närmare denna kurva är till det övre vänstra hörnet, desto bättre är klassificerarens prestanda (det är att maximera den verkliga positiva hastigheten samtidigt som den falska positiva hastigheten minimeras). Kurvor som ligger nära diagrammets diagonala, resultat från klassificerare som tenderar att göra förutsägelser som är nära slumpmässiga gissningar.

### <a name="using-cross-validation"></a>Använda korsvalidering
Precis som i regressionsexemplet kan vi utföra korsvalidering för att upprepade gånger träna, poängsätta och utvärdera olika delmängder av data automatiskt. På samma sätt kan vi använda modulen [Cross-Validate Model,][cross-validate-model] en otränad logistisk regressionsmodell och en datauppsättning. Etikettkolumnen måste anges till *intäkter* i modulen [Korsvalida modell.][cross-validate-model] Efter att ha kört experimentet och klickat på den högra utdataporten för modulen [Korsvala modell][cross-validate-model] kan vi se de binära klassificeringsmåttvärdena för varje vik, utöver medel- och standardavvikelsen för varje. 

![Korskore validera en binär klassificeringsmodell](./media/evaluate-model-performance/8.png)

Figur 8. Korskore validera en binär klassificeringsmodell.

![Korsvalideringsresultat för en binär klassificerare](./media/evaluate-model-performance/9.png)

Figur 9. Korsvalideringsresultat för en binär klassificerare.

## <a name="evaluating-a-multiclass-classification-model"></a>Utvärdera en klassificeringsmodell för fleraklasser
I det här experimentet [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") kommer vi att använda den populära Iris-datauppsättningen, som innehåller instanser av tre olika typer (klasser) av irisanläggningen. Det finns fyra funktionsvärden (sepal längd / bredd och kronblad längd / bredd) för varje instans. I de tidigare experimenten tränade och testade vi modellerna med samma datauppsättningar. Här kommer vi att använda [modulen Dela data][split] för att skapa två delmängder av data, träna på den första och poängsätta och utvärdera den andra. Iris-datauppsättningen är allmänt tillgänglig i [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.html)och kan laddas ned med hjälp av en [importdatamodul.][import-data]

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler på arbetsytan i Azure Machine Learning Studio (klassisk):

* [Importera data][import-data]
* [Beslutsskog med två klasser][multiclass-decision-forest]
* [Dela data][split]
* [Träningsmodell][train-model]
* [Poängmodell][score-model]
* [Utvärdera modell][evaluate-model]

Anslut portarna enligt bild 10.

Ange kolumnindexet Etikett för [tågmodellmodulen][train-model] till 5. Datauppsättningen har ingen rubrikrad men vi vet att klassetiketterna finns i femte kolumnen.

Klicka på modulen [Importera data][import-data] och ange egenskapen *Data source* till *webb-URL via HTTP*och *URL:en* till http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Ange den del av instanser som ska användas för utbildning i modulen [Dela data][split] (0,7 till exempel).

![Utvärdera en klassificerare för flera klasser](./media/evaluate-model-performance/10.png)

Figur 10. Utvärdera en klassificerare för flera klasser

### <a name="inspecting-the-evaluation-results"></a>Granska utvärderingsresultaten
Kör experimentet och klicka på utdataporten [för Utvärdera modell][evaluate-model]. Utvärderingsresultaten presenteras i form av en förvirring matris, i detta fall. Matrisen visar de faktiska kontra förväntade instanserna för alla tre klasserna.

![Utvärderingsresultat för klassificering av flera klass](./media/evaluate-model-performance/11.png)

Figur 11. Utvärderingsresultat för klassificering av flera klass.

### <a name="using-cross-validation"></a>Använda korsvalidering
Som tidigare nämnts kan du utföra upprepad utbildning, poängsättning och utvärderingar automatiskt med modulen [Korsvalningsmodell.][cross-validate-model] Du skulle behöva en datauppsättning, en otränad modell och en [cross-validate modell][cross-validate-model] modul (se bilden nedan). Återigen måste du ställa in etikettkolumnen för modulen [Korsval validera modell][cross-validate-model] (kolumnindex 5 i det här fallet). När du har kört experimentet och klickat på den högra utdataporten för [cross-validate-modellen][cross-validate-model]kan du granska måttvärdena för varje vikning samt medel- och standardavvikelsen. Måtten som visas här är liknande de som beskrivs i binärklassificeringsfallet. I klassificeringen av flera klasser görs dock beräkning av de verkliga positiva/negativa och falska positiva/negativa genom att räkna per klass, eftersom det inte finns någon övergripande positiv eller negativ klass. Till exempel, när du beräknar precision eller återkallande av klassen "Iris-setosa" antas det att detta är den positiva klassen och alla andra som negativa.

![Korsvalificera en klassificeringsmodell för fleraklasser](./media/evaluate-model-performance/12.png)

Figur 12. Korsvalificera en klassificeringsmodell för fleraklasser.

![Korsvalideringsresultat för en klassificeringsmodell för fleraklasser](./media/evaluate-model-performance/13.png)

Figur 13. Korsvalideringsresultat för en klassificeringsmodell för fleraklasser.

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
