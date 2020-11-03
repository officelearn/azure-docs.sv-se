---
title: Utvärdera AutoML experiment resultat
titleSuffix: Azure Machine Learning
description: Lär dig hur du visar och utvärderar diagram och mått för var och en av dina automatiserade experiment körningar i Machine Learning.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2
ms.openlocfilehash: d66c5001d94d0c2d28ae3c55b468fbaf45871c98
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280360"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Utvärdera resultat från automatiska maskin inlärnings experiment

I den här artikeln får du lära dig hur du visar och utvärderar resultaten från din automatiserade maskin inlärning, AutoML, experiment. Experimenten består av flera körningar, där varje körning skapar en modell. För att hjälpa dig att utvärdera varje modell genererar AutoML automatiskt prestanda mått och diagram som är speciella för din experiment typ. 

AutoML tillhandahåller till exempel olika diagram för klassificerings-och Regressions modeller. 

|Klassificering|Regression
|---|---|
|<li> [Förvirringsmatris](#confusion-matrix) <li>[Diagram över precisions återställning](#precision-recall-chart) <li> [Egenskaper för mottagar drift (eller ROC)](#roc) <li> [Lyft kurva](#lift-curve)<li> [Vinster-kurva](#gains-curve)<li> [Kalibrerings område](#calibration-plot) | <li> [Förutsägs jämfört med sant](#pvt) <li> [Histogram över rester](#histo)|

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Skapa ett experiment för din automatiserade Machine Learning-körning, antingen med SDK eller i Azure Machine Learning Studio.

    * Använd SDK: n för att bygga en [klassificerings modell](how-to-auto-train-remote.md) eller [Regressions modell](tutorial-auto-train-models.md)
    * Använd [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) för att skapa en klassificerings-eller Regressions modell genom att överföra relevanta data.

## <a name="view-run-results"></a>Visa körnings resultat

När din automatiserade maskin inlärnings experiment har slutförts kan du hitta en historik över körningarna i din Machine Learning-arbetsyta via [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 

För SDK-experiment kan du se samma resultat under en körning när du använder Jupyter- `RunDetails` [widgeten](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true).

Följande steg och animering visar hur du visar körnings historik och prestanda mått och diagram för en speciell modell i Studio.

![Steg för att Visa körnings historik och modell prestanda mått och diagram](./media/how-to-understand-automated-ml/view-run-metrics-ui.gif)

Visa körnings historik och modell prestanda mått och diagram i Studio: 

1. [Logga in på Studio](https://ml.azure.com/) och navigera till din arbets yta.
1. I den vänstra panelen i arbets ytan väljer du **Kör**.
1. I listan över experiment väljer du det som du vill utforska.
1. Välj kommandot **Kör** i den nedre tabellen.
1. På fliken **modeller** väljer du **algoritmens namn** för den modell som du vill utforska.
1. På fliken **mått** väljer du vilka mått och diagram som du vill utvärdera för den modellen. 


<a name="classification"></a> 

## <a name="classification-performance-metrics"></a>Klassificering av prestanda mått

I följande tabell sammanfattas de modell prestanda mått som AutoML beräknar för varje klassificerings modell som genereras för experimentet. 

Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--
AUC_macro| AUC är den del av den egenskaps kurva som används för mottagaren. Makro är det aritmetiska medelvärdet av AUC för varje klass.  | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | genomsnitt = "makro"|
AUC_micro| AUC är den del av den egenskaps kurva som används för mottagaren. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringar från varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | genomsnitt = "Micro"|
AUC_weighted  | AUC är den del av den egenskaps kurva som används för mottagaren. Viktat är det aritmetiska medelvärdet av poängen för varje klass, viktat med antalet sanna instanser i varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Average = "viktad"
accuracy|Noggrannhet är procent andelen förväntade etiketter som exakt matchar de sanna etiketterna. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Inget|
average_precision_score_macro|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Makro är det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|genomsnitt = "makro"|
average_precision_score_micro|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringarna vid varje avgränsare.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|genomsnitt = "Micro"|
average_precision_score_weighted|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Viktat är det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "viktad"|
balanced_accuracy|Balanserade noggrannhet är det aritmetiska medelvärdet för återkallning för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro"|
f1_score_macro|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Makro är det aritmetiska medelvärdet av F1-poängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|genomsnitt = "makro"|
f1_score_micro|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Micro beräknas globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|genomsnitt = "Micro"|
f1_score_weighted|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Viktat medelvärde efter klass frekvens för F1-Poäng för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "viktad"|
log_loss|Detta är den förlust funktion som används i (MULTINOMIAL) Logistisk regression och tillägg, till exempel neurala Networks, definierad som den negativa logg sannolikheten för de faktiska etiketterna med en Probabilistic klassificerares förutsägelser. För ett enda exempel med True Label yt i {0,1} och uppskattad sannolikhet YP som yt = 1, är logg förlusten-log P (yt&#124;YP) =-(yt log (YP) + (1-yt) log (1-YP)).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Inget|
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

Till exempel, i stället för att beräkna återkalla som, Genomsnittligt `tp / (tp + fn)` återställnings genomsnitt för flera `micro` `macro` `weighted` klasser över båda klasserna av en binär klassificerings data uppsättning. Detta motsvarar att beräkna återkallningen för `true` klassen och `false` klassen separat, och sedan ta medelvärdet av de två.

## <a name="confusion-matrix"></a>Förvirringsmatris

En Förväxlings mat ris beskriver prestanda för en klassificerings modell. Varje rad visar instanserna av klassen True, eller den faktiska klassen i din data uppsättning, och varje kolumn representerar instanser av klassen som förutsägs av modellen. 

För varje förvirrings mat ris visar automatiserad ML frekvensen för varje förväntad etikett (kolumn) jämfört med den sanna etiketten (rad). Ju mörkare färgen, desto högre är antalet i den specifika delen av matrisen. 

### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?

En förvirring mat ris jämför det faktiska värdet för data uppsättningen mot de förväntade värden som modellen gav. Därför har Machine Learning-modeller högre precision om modellen har de flesta värden längs diagonalen, vilket innebär att modellen förutsäger det korrekta värdet. Om en modell har klass obalans, hjälper Förväxlings mat ris att identifiera en prioriterad modell.

#### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exempel 1: en klassificerings modell med dålig precision
![En klassificerings modell med dålig precision](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

#### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exempel 2: en klassificerings modell med hög exakthet 
![En klassificerings modell med hög noggrannhet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exempel 3: en klassificerings modell med hög exakthet och hög bias i modell förutsägelser
![En klassificerings modell med hög exakthet och hög bias i modell förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>

## <a name="precision-recall-chart"></a>Diagram över precisions återställning

Kurvan om precisions åter kallelse visar förhållandet mellan precisionen och återställningen från en modell. Termen precision representerar möjligheten för en modell att märka alla instanser på rätt sätt. Återkallande representerar möjligheten för en klassificerare att hitta alla instanser av en viss etikett.

Med det här diagrammet kan du jämföra precisions återställnings kurvorna för varje modell för att avgöra vilken modell som har en acceptabel relation mellan precisionen och återställningen av ditt specifika affärs problem. Det här diagrammet visar Genomsnittligt antal återkallning i makron, mikrogenomsnittlig precisions återställning och precisions åter kallelse som är associerad med alla klasser för en modell. 

**Makro-genomsnitt** beräknar måttet oberoende av varje klass och tar sedan medelvärdet och behandlar alla klasser lika. Mikrogenomsnitt **micro-average** aggregerar emellertid bidragen från alla klasser för att beräkna medelvärdet. Micro-Average är att föredra om det finns klass obalans i data uppsättningen.

### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
Den perfekta precisions återställnings kurvan kan variera beroende på målet i affärs problemet. 

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exempel 1: en klassificerings modell med låg precision och låg återkallning
![En klassificerings modell med låg precision och låg återkallning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exempel 2: en klassificerings modell med ~ 100% precision och ~ 100% återkalla 
![En klassificerings modell med hög precision och återkallande](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

<a name="roc"></a>

## <a name="roc-chart"></a>ROC-diagram

Mottagarens operativa egenskaper (eller ROC) är ett diagram över de korrekt klassificerade etiketterna jämfört med de felaktigt klassificerade etiketterna för en viss modell. ROC-kurvan kan vara mindre informativ vid inlärnings modeller på data uppsättningar med hög obalans, eftersom majoriteten klass kan Drown ut från minoritets klasser.

Du kan visualisera ytan under ROC-diagrammet som andel av korrekt klassificerade exempel. En avancerad användare av ROC-diagrammet kan se bortom det område som finns under kurvan och få en intuition för de sanna positiva och falska positiva positiva priserna som en funktion i klassificerings tröskeln eller besluts gränsen.

### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
En ROC kurva som närmar sig det övre vänstra hörnet med 100% True positiv hastighet och 0% falskt positiv hastighet blir den bästa modellen. En slumpmässig modell visas som en platt linje från det nedre vänstra hörnet till det övre högra hörnet. Sämre än slumpmässigt DIP under raden y = x.

#### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exempel 1: en klassificerings modell med låg sant etiketter och höga Felaktiga etiketter
![Klassificerings modell med låg sann etiketter och hög falskt etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

#### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exempel 2: en klassificerings modell med höga sanna etiketter och låg falskt etiketter

![en klassificerings modell med höga sanna etiketter och låg falskt etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)


<a name="lift-curve"></a>

## <a name="lift-chart"></a>Lyft diagram

Lyft diagram utvärderar prestanda för klassificerings modeller. Ett lyft diagram visar hur många gånger bättre en modell fungerar jämfört med en slumpmässig modell. Detta ger dig en relativ prestanda som tar hänsyn till det faktum att klassificeringen blir svårare när du ökar antalet klasser. En slumpmässig modell förutsäger felaktigt en större del av exempel från en data uppsättning med tio klasser jämfört med en data uppsättning med två klasser.

Du kan jämföra hissen med modellen som skapats automatiskt med Azure Machine Learning till bas linjen (slumpmässig modell) för att visa den aktuella modellens värde vinst.

### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?

En bättre presterande modell har en lyft kurva som är högre i grafen och ytterligare från bas linjen. 

#### <a name="example-1-a-classification-model-that-performs-poorly-compared-to-a-random-selection-model"></a>Exempel 1: en klassificerings modell som fungerar dåligt jämfört med en slumpmässig markerings modell
![En klassificerings modell som är sämre än en slumpmässig markerings modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

#### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exempel 2: en klassificerings modell som fungerar bättre än en slumpmässig markerings modell
![En klassificerings modell som fungerar bättre](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

<a name="gains-curve"></a>

## <a name="cumulative-gains-chart"></a>Diagram över ackumulerade vinster

I ett diagram över ackumulerade vinster utvärderas prestandan för en klassificerings modell av varje del av data. För varje percentil av data uppsättningen visar diagrammet hur många fler exempel som har klassificerats korrekt jämfört med en modell som alltid är felaktig. Den här informationen ger ett annat sätt att titta på resultaten i det medföljande lyft diagrammet.

I diagrammet över ackumulerade vinster kan du välja klassificerings gräns genom att använda ett procenttal som motsvarar den önskade vinsten från modellen. Du kan jämföra det ackumulerade vinst diagrammet med bas linjen (felaktig modell) för att se hur många procent av exemplen som klassificerades korrekt vid varje konfidens percentil.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?

På samma sätt som i ett lyft diagram är ju högre den ackumulerade vinster-kurvan över bas linjen, desto bättre modell. Dessutom är den närmare kurvan för ackumulerade vinster i diagrammets övre vänstra hörn, desto bättre blir din modell jämfört med bas linjen. 

##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exempel 1: en klassificerings modell med minimal vinst
![en klassificerings modell med minimal vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exempel 2: en klassificerings modell med betydande vinst
![En klassificerings modell med betydande vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

<a name="calibration-plot"></a>

## <a name="calibration-chart"></a>Kalibrerings diagram

I en kalibrerings ritning visas förtroendet för en förutsägelse modell. Detta görs genom att visa relationen mellan den förväntade sannolikheten och den faktiska sannolikheten, där sannolikheten representerar sannolikheten att en viss instans tillhör en viss etikett.

För alla klassificerings problem kan du granska kalibrerings linjen för Micro-Average, makro medelvärde och varje klass i en viss förutsägelse modell.

**Makro – Genomsnittligt** beräknar måttet oberoende av varje klass och sedan tar genomsnittet och behandlar alla klasser lika. Mikrogenomsnitt **micro-average** aggregerar emellertid bidragen från alla klasser för att beräkna medelvärdet. 

### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
En väl kalibrerad modell justeras mot y = x-raden, där den beräknar sannolikheten att exempel tillhör varje klass. En över-säker modell är över-predict sannolikheten nära noll och en, vilket sällan är osäker om klassen för varje exempel.

#### <a name="example-1-a-well-calibrated-model"></a>Exempel 1: en väl kalibrerad modell
![ mer bra kalibrerad modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

#### <a name="example-2-an-over-confident-model"></a>Exempel 2: en över-säker modell
![En över-säker modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)


<a name="regression"></a> 

## <a name="regression-performance-metrics"></a>Regressions prestanda mått

I följande tabell sammanfattas de modell prestanda mått som AutoML beräknar för varje Regressions-eller prognos modell som genereras för experimentet. 

|Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--|
explained_variance|Förklarad varians är den proportion som en matematisk modell utgörs av för variationen av en specifik data uppsättning. Det är den procentuella minskningen av var Ian sen för de ursprungliga data som var fel. När medelvärdet för felen är 0 är det lika med koefficienten för bestämning (se r2_score nedan).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Inget|
r2_score|R ^ 2 är koefficienten för bestämningen eller procent minskningen i kvadrat-fel jämfört med en bas linje modell som matar ut medelvärdet. |[Beräkning](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Inget|
spearman_correlation|Spearman-korrelationen är en parameter som inte är ett mått på monotonicity för relationen mellan två data uppsättningar. Till skillnad från Pearson-korrelationen förutsätter Spearman-korrelationen inte att båda data uppsättningarna vanligt vis distribueras. Precis som andra korrelations koefficienter varierar den här typen mellan-1 och + 1 med 0, vilket innebär att ingen korrelation. Korrelationer på-1 eller + 1 innebär en exakt enkel färgs relation. Positiva korrelationer innebär att x ökar, så y. Negativa korrelationer innebär att vartefter x ökar, y minskar.|[Beräkning](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Inget|
mean_absolute_error|Medelvärde för absolut fel är det förväntade värdet av absolut värde för skillnaden mellan målet och förutsägelsen|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Inget|
normalized_mean_absolute_error|Normaliserat medelvärde är ett absolut fel dividerat med data området|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dela efter data intervall|
median_absolute_error|Median det absoluta felet är median värdet för alla absoluta skillnader mellan målet och förutsägelsen. Den här förlusten är robust för att kunna avvika.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Inget|
normalized_median_absolute_error|Det normaliserade median värdet är median som är ett absolut fel dividerat med data området|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dela efter data intervall|
root_mean_squared_error|Det genomsnittliga kvadratvärdet i roten är kvadratroten ur den förväntade skillnaden i kvadraten mellan målet och förutsägelsen|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Inget|
normalized_root_mean_squared_error|Normaliserat rot genomsnitts fel i roten är ett kvadratrots fel dividerat med data intervallet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dela efter data intervall|
root_mean_squared_log_error|Rot genomsnitts logg fel i kvadrat är kvadratroten ur det förväntade logaritmiska felet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Inget|
normalized_root_mean_squared_log_error|Normaliserat rot genomsnitts fel i kvadratroten är ett rot genomsnitts logg fel som delas av data intervallet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dela efter data intervall|

<a name="pvt"></a>

## <a name="predicted-vs-true-chart"></a>Förutsägande vs. True-diagram

Vid förutsägelse jämfört med sant visas relationen mellan ett förutsägande värde och dess korrelerade sanna värde för ett Regressions problem. 

Efter varje körning kan du se ett förutsägande vs. True-diagram för varje Regressions modell. För att skydda data integriteten är värden diskretiseras och storleken på varje lager plats visas som ett stapeldiagram i den nedre delen av diagram området. Du kan jämföra förutsägelse modellen med det ljusare skuggade avsnittet som visar fel marginaler mot det idealiska värdet för var modellen ska vara.

### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
Det här diagrammet kan användas för att mäta prestanda för en modell som närmare y = x-linjen, de förväntade värdena är, desto bättre prestanda för en förutsägelse modell.

#### <a name="example-1-a-regression-model-with-low-performance"></a>Exempel 1: en Regressions modell med låg prestanda
![En Regressions modell med låg precision i förutsägelserna](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

#### <a name="example-2-a-regression-model-with-high-performance"></a>Exempel 2: en Regressions modell med hög prestanda
![En Regressions modell med hög noggrannhet i dess förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a> 

## <a name="histogram-of-residuals-chart"></a>Histogram över resten av diagrammet

Med automatisk ML får du automatiskt ett rest diagram som visar distributionen av fel i förutsägelserna för en Regressions modell. En rest är skillnaden mellan förutsägelsen och det faktiska värdet ( `y_pred - y_true` ). 

### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
Om du vill visa en marginal på fel med låg förskjutning måste histogrammets histogram vara formad som en klock kurva och centreras runt noll.

#### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exempel 1: en Regressions modell med en förskjutning i dess fel
![SA Regressions modell med förskjutning i sina fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

#### <a name="example-2-a-regression-model-with-a-more-even-distribution-of-errors"></a>Exempel 2: en Regressions modell med en mer jämn fördelning av fel
![En Regressions modell med mer jämn fördelning av fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

<a name="explain-model"></a>

## <a name="model-interpretability-and-feature-importance"></a>Modellens tolknings-och funktions prioritet
Med automatisk ML får du en instrument panel för maskin inlärnings tolkning för dina körningar.

Mer information om hur du aktiverar tolknings funktioner finns i [tolkning: modell förklaringar i automatiserad maskin inlärning](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> ForecastTCN-modellen stöds för närvarande inte av förklarings klienten. Den här modellen returnerar inte en förklarings instrument panel om den returneras som den bästa modellen och inte stöder förklarings körningar på begäran.

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.
+ Prova de automatiska antecknings exemplen för [Machine Learning-modeller](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
