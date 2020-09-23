---
title: Förstå automatiserade ML-resultat
titleSuffix: Azure Machine Learning
description: Lär dig att visa och förstå diagram och mått för var och en av dina automatiserade maskin inlärnings körningar.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/05/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 42b2ff0b7c2d43beb1d5f0b2136a97e67c0143a5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905063"
---
# <a name="understand-automated-machine-learning-results"></a>Förstå resultat från automatiserad maskininlärning


I den här artikeln får du lära dig att visa och förstå diagram och mått för var och en av dina automatiserade maskin inlärnings körningar. 

Läs mer om:
+ [Mått och diagram för klassificerings modeller](#classification)
+ [Mått och diagram för Regressions modeller](#regression)
+ [Modellens tolknings-och funktions prioritet](#explain-model)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Skapa ett experiment för din automatiserade Machine Learning-körning, antingen med SDK eller i Azure Machine Learning Studio.

    * Använd SDK: n för att bygga en [klassificerings modell](how-to-auto-train-remote.md) eller [Regressions modell](tutorial-auto-train-models.md)
    * Använd [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) för att skapa en klassificerings-eller Regressions modell genom att överföra relevanta data.

## <a name="view-the-run"></a>Visa körningen

När du har kört ett automatiserat experiment med Machine Learning finns en historik över körningarna i din Machine Learning-arbetsyta. 

1. Gå till din arbetsyta.

1. Välj **experiment**i den vänstra panelen i arbets ytan.

   ![Skärm bild av menyn experimentera](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. I listan över experiment väljer du det som du vill utforska.

   [![Experiment lista](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Välj kommandot **Kör**i den nedre tabellen.

   [ ![ Experiment körning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. I modellerna väljer du **algoritmens namn** för den modell som du vill utforska ytterligare.

   [![Experiment modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Du ser även samma resultat under en körning när du använder Jupyter- `RunDetails` [widgeten](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true).

## <a name="classification-results"></a><a name="classification"></a> Klassificerings resultat

Thee följande mått och diagram är tillgängliga för alla klassificerings modeller som du skapar med hjälp av de automatiserade maskin inlärnings funktionerna i Azure Machine Learning

+ [Mått](#classification-metrics)
+ [Förvirringsmatris](#confusion-matrix)
+ [Diagram över precisions återställning](#precision-recall-chart)
+ [Egenskaper för mottagar drift (eller ROC)](#roc)
+ [Lyft kurva](#lift-curve)
+ [Vinster-kurva](#gains-curve)
+ [Kalibrerings område](#calibration-plot)

### <a name="classification-metrics"></a>Klassificerings mått

Följande mått sparas i varje körnings upprepning för en klassificerings uppgift.

Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--
AUC_macro| AUC är den del av den egenskaps kurva som används för mottagaren. Makro är det aritmetiska medelvärdet av AUC för varje klass.  | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | genomsnitt = "makro"|
AUC_micro| AUC är den del av den egenskaps kurva som används för mottagaren. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringar från varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | genomsnitt = "Micro"|
AUC_weighted  | AUC är den del av den egenskaps kurva som används för mottagaren. Viktat är det aritmetiska medelvärdet av poängen för varje klass, viktat med antalet sanna instanser i varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Average = "viktad"
accuracy|Noggrannhet är procent andelen förväntade etiketter som exakt matchar de sanna etiketterna. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Ingen|
average_precision_score_macro|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Makro är det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|genomsnitt = "makro"|
average_precision_score_micro|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringarna vid varje avgränsare.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|genomsnitt = "Micro"|
average_precision_score_weighted|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Viktat är det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "viktad"|
balanced_accuracy|Balanserade noggrannhet är det aritmetiska medelvärdet för återkallning för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro"|
f1_score_macro|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Makro är det aritmetiska medelvärdet av F1-poängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|genomsnitt = "makro"|
f1_score_micro|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Micro beräknas globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|genomsnitt = "Micro"|
f1_score_weighted|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Viktat medelvärde efter klass frekvens för F1-Poäng för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "viktad"|
log_loss|Detta är den förlust funktion som används i (MULTINOMIAL) Logistisk regression och tillägg, till exempel neurala Networks, definierad som den negativa logg sannolikheten för de faktiska etiketterna med en Probabilistic klassificerares förutsägelser. För ett enda exempel med True Label yt i {0,1} och uppskattad sannolikhet YP som yt = 1, är logg förlusten-log P (yt&#124;YP) =-(yt log (YP) + (1-yt) log (1-YP)).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Ingen|
norm_macro_recall|Normaliserat makro återkallande är ett makro som är normaliserat så att slumpmässiga prestanda har en poäng på 0 och perfekt prestanda har en poäng på 1. Detta uppnås genom norm_macro_recall: = (recall_score_macro-R)/(1-R), där R är det förväntade värdet för recall_score_macro för slumpmässiga förutsägelser (t. ex. R = 0,5 för binär klassificering och R = (1/C) för klassificerings problem i C-klass).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro" |
precision_score_macro|Precision är procent andelen positiva element som är korrekt märkta. Makro är det aritmetiska medelvärdet för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|genomsnitt = "makro"|
precision_score_micro|Precision är procent andelen positiva element som är korrekt märkta. Micro beräknas globalt genom att räkna antalet sanna positiva positiva och falska positiva identifieringar.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|genomsnitt = "Micro"|
precision_score_weighted|Precision är procent andelen positiva element som är korrekt märkta. Viktat är det aritmetiska medelvärdet för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "viktad"|
recall_score_macro|Återkallande är den procent andel av korrekt märkta element i en viss klass. Makro är det aritmetiska medelvärdet för återkallning för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro"|
recall_score_micro|Återkallande är den procent andel av korrekt märkta element i en viss klass. Micro beräknas globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "Micro"|
recall_score_weighted|Återkallande är den procent andel av korrekt märkta element i en viss klass. Viktat är det aritmetiska medelvärdet för återkallning för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "viktad"|
weighted_accuracy|Viktad noggrannhet är exakthet där vikten för varje exempel motsvarar proportionerna för de sanna instanserna i det här exemplets sanna klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight är en Vector som motsvarar den andel av klassen för varje element i målet|

### <a name="binary-vs-multiclass-metrics"></a>Egenskaper för binär vs. multiklass

AutoML skiljer sig inte mellan binära och multiklass-mått. Samma verifierings mått rapporteras om en data uppsättning har två klasser eller fler än två klasser. Vissa mått är dock avsedda för klassificering av flera klasser. När dessa mått används i en binär data uppsättning, kommer dessa mått inte att behandla någon klass som `true` klass, vilket kan förväntas. Mått som är tydligt avsedda för multiklassen är suffix till `micro` , `macro` eller `weighted` . Exempel:,,, `average_precision_score` `f1_score` `precision_score` `recall_score` och `AUC` .

Ett konkret exempel gör den här åtskillnaden tydligare: i stället för att beräkna återkallning i genomsnitts `tp / (tp + fn)` medelvärdet för återkallade multiklasser ( `micro` , `macro` eller `weighted` ) över båda klasserna av en binär klassificerings data uppsättning. Detta motsvarar att beräkna återkallningen för `true` klassen och `false` klassen separat och sedan ta medelvärdet av de två.

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Förvirringsmatris

#### <a name="what-is-a-confusion-matrix"></a>Vad är en förvirring mat ris?
En förvirring mat ris används för att beskriva prestanda för en klassificerings modell. Varje rad visar instanserna av klassen True, eller den faktiska klassen i din data uppsättning, och varje kolumn representerar instanser av klassen som förutsägs av modellen. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Vad sker i automatiserad ML med den Förväxlings bara matrisen?
För klassificerings problem tillhandahåller Azure Machine Learning automatiskt en Förväxlings mat ris för varje modell som har skapats. För varje förvirrings mat ris visar automatiserad ML frekvensen för varje förväntad etikett (kolumn) jämfört med den sanna etiketten (rad). Ju mörkare färgen, desto högre är antalet i den specifika delen av matrisen. 

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
Vi jämför det faktiska värdet för data uppsättningen mot de förväntade värdena som modellen gav. Därför har Machine Learning-modeller högre precision om modellen har de flesta värden längs diagonalen, vilket innebär att modellen förutsäger det korrekta värdet. Om en modell har klass obalans, kommer förvirrings mat ris att hjälpa till att identifiera en prioriterad modell.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exempel 1: en klassificerings modell med dålig precision
![En klassificerings modell med dålig precision](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exempel 2: en klassificerings modell med hög exakthet 
![En klassificerings modell med hög noggrannhet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exempel 3: en klassificerings modell med hög exakthet och hög bias i modell förutsägelser
![En klassificerings modell med hög exakthet och hög bias i modell förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Diagram över precisions återställning
#### <a name="what-is-a-precision-recall-chart"></a>Vad är ett precisions återställnings diagram?
Kurvan om precisions åter kallelse visar förhållandet mellan precisionen och återställningen från en modell. Termen precision representerar möjligheten för en modell att märka alla instanser på rätt sätt. Återkallande representerar möjligheten för en klassificerare att hitta alla instanser av en viss etikett.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Vad sker i automatiserad ML med precisions återställnings diagrammet?

Med det här diagrammet kan du jämföra precisions återställnings kurvorna för varje modell för att avgöra vilken modell som har en acceptabel relation mellan precisionen och återställningen av ditt specifika affärs problem. Det här diagrammet visar Genomsnittligt antal återkallning i makron, mikrogenomsnittlig precisions återställning och precisions åter kallelse som är associerad med alla klasser för en modell. 

Makrot beräknar medelvärdet för att beräkna måttet oberoende av varje klass och sedan ta genomsnittet och behandla alla klasser lika. Micro-Average sammanställer dock bidrags bidragen för alla klasser för att beräkna medelvärdet. Micro-Average är att föredra om det finns klass obalans i data uppsättningen.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
Den perfekta precisions återställnings kurvan kan variera beroende på målet i affärs problemet. Några exempel anges nedan

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exempel 1: en klassificerings modell med låg precision och låg återkallning
![En klassificerings modell med låg precision och låg återkallning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exempel 2: en klassificerings modell med ~ 100% precision och ~ 100% återkalla 
![En klassificerings modell med hög precision och återkallande](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC-diagram

#### <a name="what-is-a-roc-chart"></a>Vad är ett ROC-diagram?
Mottagarens operativa egenskaper (eller ROC) är ett diagram över de korrekt klassificerade etiketterna jämfört med de felaktigt klassificerade etiketterna för en viss modell. ROC-kurvan kan vara mindre informativ vid inlärnings modeller på data uppsättningar med hög obalans, eftersom majoriteten klass kan Drown ut från minoritets klasser.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Vad sker med automatisk ML i ROC-diagrammet?
Du kan visualisera ytan under ROC-diagrammet som andel av korrekt klassificerade exempel. En avancerad användare av ROC-diagrammet kan se bortom det område som finns under kurvan och få en intuition för de sanna positiva och falska positiva positiva priserna som en funktion i klassificerings tröskeln eller besluts gränsen.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
En ROC kurva som närmar sig det övre vänstra hörnet med 100% True positiv hastighet och 0% falskt positiv hastighet blir den bästa modellen. En slumpmässig modell visas som en platt linje från det nedre vänstra hörnet till det övre högra hörnet. Sämre än slumpmässigt DIP under raden y = x.

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exempel 1: en klassificerings modell med låg sant etiketter och höga Felaktiga etiketter
![Klassificerings modell med låg sann etiketter och hög falskt etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exempel 2: en klassificerings modell med höga sanna etiketter och låg falskt etiketter
![en klassificerings modell med höga sanna etiketter och låg falskt etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Lyft diagram
#### <a name="what-is-a-lift-chart"></a>Vad är ett lyft diagram?
Lyft diagram används för att utvärdera prestanda för klassificerings modeller. Ett lyft diagram visar hur många gånger bättre en modell fungerar jämfört med en slumpmässig modell. Detta ger dig en relativ prestanda som tar hänsyn till det faktum att klassificeringen blir svårare när du ökar antalet klasser. En slumpmässig modell förutsäger felaktigt en större del av exemplen från en data uppsättning med tio klasser jämfört med en data uppsättning med två klasser.

#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Vad sker i automatiserad ML med lyft diagrammet?
Du kan jämföra hissen med modellen som skapats automatiskt med Azure Machine Learning till bas linjen för att se den aktuella modellens värde vinst.
#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Exempel 1: en klassificerings modell som är sämre än en slumpmässig markerings modell
![En klassificerings modell som är sämre än en slumpmässig markerings modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exempel 2: en klassificerings modell som fungerar bättre än en slumpmässig markerings modell
![En klassificerings modell som fungerar bättre](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="cumulative-gains-chart"></a>Diagram över ackumulerade vinster
#### <a name="what-is-a-cumulative-gains-chart"></a>Vad är ett ackumulerat vinst diagram?

I ett diagram över ackumulerade vinster utvärderas prestandan för en klassificerings modell av varje del av data. För varje percentil av data uppsättningen visar diagrammet hur många fler exempel som har klassificerats korrekt.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Vad sker i automatiserad ML med diagrammet vinst?
Använd diagrammet för ackumulerade vinster för att hjälpa dig att välja klassificeringen för att ta del av en procent andel som motsvarar den önskade vinsten från modellen. Den här informationen ger ett annat sätt att titta på resultaten i det medföljande lyft diagrammet.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exempel 1: en klassificerings modell med minimal vinst
![en klassificerings modell med minimal vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exempel 2: en klassificerings modell med betydande vinst
![En klassificerings modell med betydande vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibrerings diagram

#### <a name="what-is-a-calibration-chart"></a>Vad är ett kalibrerings diagram?
En kalibrerings ritning används för att Visa förtroendet för en förutsägelse modell. Detta görs genom att visa relationen mellan den förväntade sannolikheten och den faktiska sannolikheten, där sannolikheten representerar sannolikheten att en viss instans tillhör en viss etikett.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Vad sker i automatiserad ML med kalibrerings diagrammet?
För alla klassificerings problem kan du granska kalibrerings linjen för Micro-Average, makro medelvärde och varje klass i en viss förutsägelse modell.

Makrot beräknar medelvärdet för att beräkna måttet oberoende av varje klass och sedan ta genomsnittet och behandla alla klasser lika. Micro-Average sammanställer dock bidrags bidragen för alla klasser för att beräkna medelvärdet. 
#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
En väl kalibrerad modell justeras mot y = x-raden, där den beräknar sannolikheten att exempel tillhör varje klass. En över-säker modell är över-predict sannolikheten nära noll och en, vilket sällan är osäker om klassen för varje exempel.


##### <a name="example-1-a-well-calibrated-model"></a>Exempel 1: en väl kalibrerad modell
![ mer bra kalibrerad modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Exempel 2: en över-säker modell
![En över-säker modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a> Regressions resultat

Thee följande mått och diagram är tillgängliga för varje Regressions modell som du skapar med hjälp av de automatiserade maskin inlärnings funktionerna i Azure Machine Learning

+ [Mått](#reg-metrics)
+ [Förutsägs jämfört med sant](#pvt)
+ [Histogram över rester](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a> Regressions mått

Följande mått sparas i varje körnings upprepning för en Regressions-eller prognos uppgift.

|Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--|
explained_variance|Förklarad varians är den proportion som en matematisk modell utgörs av för variationen av en specifik data uppsättning. Det är den procentuella minskningen av var Ian sen för de ursprungliga data som var fel. När medelvärdet för felen är 0, är det lika med förklarad varians.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Ingen|
r2_score|R2 är koefficienten för bestämningen eller procent minskningen i kvadrat-fel jämfört med en bas linje modell som matar ut medelvärdet. |[Beräkning](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Ingen|
spearman_correlation|Spearman-korrelationen är en parameter som inte är ett mått på monotonicity för relationen mellan två data uppsättningar. Till skillnad från Pearson-korrelationen förutsätter Spearman-korrelationen inte att båda data uppsättningarna vanligt vis distribueras. Precis som andra korrelations koefficienter varierar den här typen mellan-1 och + 1 med 0, vilket innebär att ingen korrelation. Korrelationer på-1 eller + 1 innebär en exakt enkel färgs relation. Positiva korrelationer innebär att x ökar, så y. Negativa korrelationer innebär att vartefter x ökar, y minskar.|[Beräkning](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Ingen|
mean_absolute_error|Medelvärde för absolut fel är det förväntade värdet av absolut värde för skillnaden mellan målet och förutsägelsen|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Ingen|
normalized_mean_absolute_error|Normaliserat medelvärde är ett absolut fel dividerat med data området|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dela efter data intervall|
median_absolute_error|Median det absoluta felet är median värdet för alla absoluta skillnader mellan målet och förutsägelsen. Den här förlusten är robust för att kunna avvika.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Ingen|
normalized_median_absolute_error|Det normaliserade median värdet är median som är ett absolut fel dividerat med data området|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dela efter data intervall|
root_mean_squared_error|Det genomsnittliga kvadratvärdet i roten är kvadratroten ur den förväntade skillnaden i kvadraten mellan målet och förutsägelsen|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Ingen|
normalized_root_mean_squared_error|Normaliserat rot genomsnitts fel i roten är ett kvadratrots fel dividerat med data intervallet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dela efter data intervall|
root_mean_squared_log_error|Rot genomsnitts logg fel i kvadrat är kvadratroten ur det förväntade logaritmiska felet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Ingen|
normalized_root_mean_squared_log_error|Normaliserat rot genomsnitts fel i kvadratroten är ett rot genomsnitts logg fel som delas av data intervallet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dela efter data intervall|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a> Förutsägande vs. True-diagram
#### <a name="what-is-a-predicted-vs-true-chart"></a>Vad är ett förutsägande vs. True-diagram?
Vid förutsägelse jämfört med sant visas relationen mellan ett förutsägande värde och dess korrelerade sanna värde för ett Regressions problem. Det här diagrammet kan användas för att mäta prestanda för en modell som närmare y = x-linjen, de förväntade värdena är, desto bättre precision för en förutsägelse modell.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Vad sker i automatiserad ML med det förväntade vs. True-diagrammet?
Efter varje körning kan du se ett förutsägande vs. True-diagram för varje Regressions modell. För att skydda data integriteten är värden diskretiseras och storleken på varje lager plats visas som ett stapeldiagram i den nedre delen av diagram området. Du kan jämföra förutsägelse modellen med det ljusare skuggade avsnittet som visar fel marginaler mot det idealiska värdet för var modellen ska vara.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Exempel 1: en klassificerings modell med låg exakthet
![En Regressions modell med låg precision i förutsägelserna](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Exempel 2: en Regressions modell med hög noggrannhet 
[![En Regressions modell med hög noggrannhet i dess förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a> Histogram över resten av diagrammet
#### <a name="what-is-a-residuals-chart"></a>Vad är ett resten av diagrammet?
En rest är skillnaden mellan förutsägelsen och det faktiska värdet ( `y_pred - y_true` ). Om du vill visa en marginal på fel med låg förskjutning måste histogrammets histogram vara formad som en klock kurva och centreras runt 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Vad sker i automatiserad ML med resten av diagrammet?
Med automatisk ML får du automatiskt ett rest diagram som visar distributionen av fel i förutsägelserna.
#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
En bra modell har vanligt vis rester nära noll.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exempel 1: en Regressions modell med en förskjutning i dess fel
![SA Regressions modell med förskjutning i sina fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Exempel 2: en Regressions modell med mer jämn distribution av fel
![En Regressions modell med mer jämn fördelning av fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a> Modellens tolknings-och funktions prioritet
Med automatisk ML får du en instrument panel för maskin inlärnings tolkning för dina körningar.
Mer information om hur du aktiverar tolknings funktioner finns i [så här aktiverar du](how-to-machine-learning-interpretability-automl.md) tolkning i automatiserade ml experiment.

> [!NOTE]
> ForecastTCN-modellen stöds för närvarande inte av förklarings klienten. Den här modellen returnerar ingen förklarings instrument panel om den returneras som den bästa modellen och inte stöder förklarings körningar på begäran.

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.
+ Testa antecknings exemplen för [automatisk Machine Learning modell förklaring](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
