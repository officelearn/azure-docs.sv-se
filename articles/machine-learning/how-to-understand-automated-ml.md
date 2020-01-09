---
title: Förstå automatiserade ML-resultat
titleSuffix: Azure Machine Learning
description: Lär dig att visa och förstå diagram och mått för var och en av dina automatiserade maskin inlärnings körningar.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 69cf79f8258f85f2fb5e787f91aa843837d0a3a1
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75538493"
---
# <a name="understand-automated-machine-learning-results"></a>Förstå automatiserade maskin inlärnings resultat
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att visa och förstå diagram och mått för var och en av dina automatiserade maskin inlärnings körningar. 

Läs mer om:
+ [Mått, diagram och kurvor för klassificerings modeller](#classification)
+ [Mått, diagram och diagram för Regressions modeller](#regression)
+ [Modellens tolknings-och funktions prioritet](#explain-model)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Skapa ett experiment för din automatiserade Machine Learning-körning, antingen med SDK eller i Azure Machine Learning Studio.

    * Använd SDK: n för att bygga en [klassificerings modell](how-to-auto-train-remote.md) eller [Regressions modell](tutorial-auto-train-models.md)
    * Använd [Azure Machine Learning Studio](how-to-create-portal-experiments.md) för att skapa en klassificerings-eller Regressions modell genom att överföra relevanta data.

## <a name="view-the-run"></a>Visa körningen

När du har kört ett automatiserat experiment med Machine Learning finns en historik över körningarna i din Machine Learning-arbetsyta. 

1. Gå till din arbetsyta.

1. Välj **experiment**i den vänstra panelen i arbets ytan.

   ![Skärmbild av menyn för experiment](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. I listan över experiment väljer du det som du vill utforska.

   [![experiment lista](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Välj kommandot **Kör**i den nedre tabellen.

   [![experiment körning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. I modellerna väljer du **algoritmens namn** för den modell som du vill utforska ytterligare.

   [![experiment modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Du ser även samma resultat under en körning när du använder [widgeten `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Klassificerings resultat

Thee följande mått och diagram är tillgängliga för alla klassificerings modeller som du skapar med hjälp av de automatiserade maskin inlärnings funktionerna i Azure Machine Learning

+ [Mått](#classification-metrics)
+ [Felmatris](#confusion-matrix)
+ [Precisionsåterkallningsdiagram diagram](#precision-recall-chart)
+ [Mottagare operativa egenskaper (eller ROC)](#roc)
+ [Flytta kurva](#lift-curve)
+ [Vinster kurva](#gains-curve)
+ [Kalibreringskurva](#calibration-plot)

### <a name="classification-metrics"></a>Klassificering mått

Följande mått sparas i varje körnings upprepning för en klassificerings uppgift.

Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--
AUC_Macro| AUC är området under mottagare fungerar egenskap kurvan. Makrot är det aritmetiska medelvärdet av AUC för varje klass.  | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Genomsnittlig = ”makrot”|
AUC_Micro| AUC är området under mottagare fungerar egenskap kurvan. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringar från varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Genomsnittlig = ”micro”|
AUC_Weighted  | AUC är området under mottagare fungerar egenskap kurvan. Viktat är det aritmetiska medelvärdet av poängen för varje klass, viktat med antalet sanna instanser i varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Genomsnittlig = ”viktat”
accuracy|Procent av förväntade etiketter som exakt matchar SANT etiketter är. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Inget|
average_precision_score_macro|Genomsnittlig precision sammanfattas en precisionsåterkallningsdiagram kurva som viktad medelvärdet av Precision-datorerna som kan uppnås på varje tröskelvärde ökade återkallande från föregående tröskelvärdet används som vikten. Makro är det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Genomsnittlig = ”makrot”|
average_precision_score_micro|Genomsnittlig precision sammanfattas en precisionsåterkallningsdiagram kurva som viktad medelvärdet av Precision-datorerna som kan uppnås på varje tröskelvärde ökade återkallande från föregående tröskelvärdet används som vikten. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringarna vid varje avgränsare.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Genomsnittlig = ”micro”|
average_precision_score_weighted|Genomsnittlig precision sammanfattas en precisionsåterkallningsdiagram kurva som viktad medelvärdet av Precision-datorerna som kan uppnås på varje tröskelvärde ökade återkallande från föregående tröskelvärdet används som vikten. Viktat är det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Genomsnittlig = ”viktat”|
balanced_accuracy|Belastningsutjämnade är det aritmetiska medelvärdet om återkallande för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”makrot”|
f1_score_macro|F1 poängen är det harmoniska medelvärdet av precision och återkallande. Makro är det aritmetiska medelvärdet av F1-poängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Genomsnittlig = ”makrot”|
f1_score_micro|F1 poängen är det harmoniska medelvärdet av precision och återkallande. Micro beräknas globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Genomsnittlig = ”micro”|
f1_score_weighted|F1 poängen är det harmoniska medelvärdet av precision och återkallande. Viktat medelvärde av klassen frekvensen av F1 poängen för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Genomsnittlig = ”viktat”|
log_loss|Detta är funktionen för förlust som används i (multinomialen) logistic regression och tillägg av den som neurala nätverk, definierad som negativt log-sannolikheten för true etiketterna får en avsnittet om sannolikhetsbunden klassificerare förutsägelser. För ett enda exempel med True Label yt i {0,1} och uppskattad sannolikhet YP som yt = 1, är logg förlusten-log P (&#124;yt YP) =-(yt logg (YP) + (1-yt) log (1-YP)).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Inget|
norm_macro_recall|Normaliserade makrot återkalla är makrot återkalla normalized så att slumpmässiga prestanda har ett värde på 0 och perfekt prestanda har ett värde på 1. Detta uppnås genom norm_macro_recall: = (recall_score_macro-R)/(1-R), där R är det förväntade värdet för recall_score_macro för slumpmässiga förutsägelser (t. ex. R = 0,5 för binär klassificering och R = (1/C) för klassificerings problem i C-klass).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro" |
precision_score_macro|Precision är procent andelen positiva element som är korrekt märkta. Makro är det aritmetiska medelvärdet för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”makrot”|
precision_score_micro|Precision är procent andelen positiva element som är korrekt märkta. Micro beräknas globalt genom att räkna antalet sanna positiva positiva och falska positiva identifieringar.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”micro”|
precision_score_weighted|Precision är procent andelen positiva element som är korrekt märkta. Viktat är det aritmetiska medelvärdet för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”viktat”|
recall_score_macro|Återkallande är den procent andel av korrekt märkta element i en viss klass. Makro är det aritmetiska medelvärdet för återkallning för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”makrot”|
recall_score_micro|Återkallande är den procent andel av korrekt märkta element i en viss klass. Micro beräknas globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”micro”|
recall_score_weighted|Återkallande är den procent andel av korrekt märkta element i en viss klass. Viktat är det aritmetiska medelvärdet för återkallning för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”viktat”|
weighted_accuracy|Viktad noggrannhet är exakthet där vikten för varje exempel motsvarar proportionerna för de sanna instanserna i det här exemplets sanna klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight är en vektor som är lika med andelen av den klassen för varje element i målet|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Felmatris

#### <a name="what-is-a-confusion-matrix"></a>Vad är en förvirring mat ris?
En felmatris används för att beskriva resultatet av en modell för klassificering. Varje rad visar instanserna av klassen True, eller den faktiska klassen i din data uppsättning, och varje kolumn representerar instanser av klassen som förutsägs av modellen. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Vad sker i automatiserad ML med den Förväxlings bara matrisen?
För klassificering, problem ger Azure Machine Learning automatiskt en felmatris för varje modell som har skapats. För varje förvirrings mat ris visar automatiserad ML frekvensen för varje förväntad etikett (kolumn) jämfört med den sanna etiketten (rad). Ju mörkare färgen, desto högre är antalet i den specifika delen av matrisen. 

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
Vi jämför det faktiska värdet för data uppsättningen mot de förväntade värdena som modellen gav. Därför har Machine Learning-modeller högre precision om modellen har de flesta värden längs diagonalen, vilket innebär att modellen förutsäger det korrekta värdet. Om en modell har klass obalans, kommer förvirrings mat ris att hjälpa till att identifiera en prioriterad modell.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exempel 1: en klassificerings modell med dålig precision
![En klassificerings modell med dålig precision](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exempel 2: en klassificerings modell med hög exakthet 
![En klassificerings modell med hög noggrannhet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exempel 3: en klassificerings modell med hög exakthet och hög bias i modell förutsägelser
![En klassificerings modell med hög exakthet och hög bias i modell förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Precisionsåterkallningsdiagram diagram
#### <a name="what-is-a-precision-recall-chart"></a>Vad är ett precisions återställnings diagram?
Kurvan om precisions åter kallelse visar förhållandet mellan precisionen och återställningen från en modell. Termen precision representerar möjligheten för en modell att märka alla instanser på rätt sätt. Återkallande representerar möjligheten för en klassificerare att hitta alla instanser av en viss etikett.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Vad sker i automatiserad ML med precisions återställnings diagrammet?

Du kan jämföra precisionsåterkallningsdiagram kurvor för varje modell att avgöra vilken modell som har en godtagbar relation mellan precision och återkallande för din specifika affärsproblem med det här diagrammet. Det här diagrammet visar makrot genomsnittlig Precisionsåterkallningsdiagram, Micro genomsnittliga Precisionsåterkallningsdiagram och precisionsåterkallningsdiagram som är associerade med alla klasser för en modell. 

Makrot beräknar medelvärdet för att beräkna måttet oberoende av varje klass och sedan ta genomsnittet och behandla alla klasser lika. Micro-Average sammanställer dock bidrags bidragen för alla klasser för att beräkna medelvärdet. Micro-Average är att föredra om det finns klass obalans i data uppsättningen.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
Den perfekta precisions återställnings kurvan kan variera beroende på målet i affärs problemet. Några exempel anges nedan

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exempel 1: en klassificerings modell med låg precision och låg återkallning
![En klassificerings modell med låg precision och låg återkallning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exempel 2: en klassificerings modell med ~ 100% precision och ~ 100% återkalla 
![en klassificerings modell med hög precision och återkallande](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC-diagram

#### <a name="what-is-a-roc-chart"></a>Vad är ett ROC-diagram?
Mottagare som fungerar egenskap (eller ROC) är en rityta för korrekt klassificerade etiketterna jämfört med felaktigt klassificerad etiketter för en viss modell. ROC-kurvan kan vara mindre informativa när modeller för utbildning på datauppsättningar med hög partiskt, som det inte visas falskt positivt etiketter.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Vad sker med automatisk ML i ROC-diagrammet?
Automatiserad ML genererar genomsnittlig precisions återställning, mikrogenomsnittlig precisions återställning och precisions åter kallelse som är associerad med alla klasser för en modell. 

Makrot beräknar medelvärdet för att beräkna måttet oberoende av varje klass och sedan ta genomsnittet och behandla alla klasser lika. Micro-Average sammanställer dock bidrags bidragen för alla klasser för att beräkna medelvärdet. Micro-Average är att föredra om det finns klass obalans i data uppsättningen.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
Vi rekommenderar att modellen är närmare 100% True positiv hastighet och närmare 0% falskt positiv hastighet. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exempel 1: en klassificerings modell med låg sant etiketter och höga Felaktiga etiketter
![Klassificerings modell med låg sann etiketter och hög falskt etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exempel 2: en klassificerings modell med höga sanna etiketter och låg falskt etiketter
![en klassificerings modell med High True Labels och low false-etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Lyft diagram
#### <a name="what-is-a-lift-chart"></a>Vad är ett lyft diagram?
Lift scheman används för att utvärdera prestanda för en modell för klassificering. Det visar hur mycket som är bättre att göra med den genererade modellen jämfört med utan en modell med avseende på noggrannhet.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Vad sker i automatiserad ML med lyft diagrammet?
Du kan jämföra hissen av modellen som skapats automatiskt med Azure Machine Learning till i baslinjen för att kunna visa vinsten värdet för den specifika modellen.
#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Exempel 1: en klassificerings modell som är sämre än en slumpmässig markerings modell
![En klassificerings modell som är sämre än en slumpmässig markerings modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exempel 2: en klassificerings modell som fungerar bättre än en slumpmässig markerings modell
![en klassificerings modell som fungerar bättre](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Vinst diagram
#### <a name="what-is-a-gains-chart"></a>Vad är ett vinst diagram?

Ett diagram får utvärderar prestanda för en modell för klassificering av varje del av data. Den visar för vardera percentil för datamängd, hur mycket bättre du kan förvänta dig att utföra jämfört med mot ett slumpmässigt urval-modellen.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Vad sker i automatiserad ML med diagrammet vinst?
Använda ackumulerade vinster diagrammet för att hjälpa dig att välja den klassificering brytfrekvens med hjälp av en procentsats som motsvarar en önskad vinst från modellen. Den här informationen innehåller ett annat sätt att titta på resultaten i den medföljande ökningsdiagrammet.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exempel 1: en klassificerings modell med minimal vinst
![en klassificerings modell med minimal vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exempel 2: en klassificerings modell med betydande vinst
![en klassificerings modell med betydande vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibrerings diagram

#### <a name="what-is-a-calibration-chart"></a>Vad är ett kalibrerings diagram?
En kalibreringskurva används för att visa förtroendet hos en förutsägelsemodell. Det gör du genom att som visar relationen mellan sannolikheten förväntade och faktiska sannolikheten, där ”sannolikhet” representerar sannolikheten att en viss instans hör under vissa etiketten.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Vad sker i automatiserad ML med kalibrerings diagrammet?
Alla klassificering problem, kan du läsa kalibrering raden för micro medelvärde, makrot medelvärde och varje klass i en viss förutsägelsemodell.

Makrot beräknar medelvärdet för att beräkna måttet oberoende av varje klass och sedan ta genomsnittet och behandla alla klasser lika. Micro-Average sammanställer dock bidrags bidragen för alla klasser för att beräkna medelvärdet. 
#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
 En väl kalibrerad modell justeras mot y = x-linjen, där det är rimligt att vara trygg i dess förutsägelser. En modell för över confident överensstämmer med y = 0 rad, där den förväntade sannolikheten finns men det finns inga faktiska sannolikheten. 


##### <a name="example-1-a-well-calibrated-model"></a>Exempel 1: en väl kalibrerad modell
![ mer bra kalibrerad modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Exempel 2: en över-säker modell
![En över-säker modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressions resultat

Thee följande mått och diagram är tillgängliga för varje Regressions modell som du skapar med hjälp av de automatiserade maskin inlärnings funktionerna i Azure Machine Learning

+ [Mått](#reg-metrics)
+ [Förutsägs jämfört med sant](#pvt)
+ [Histogram för restbelopp](#histo)


### <a name="reg-metrics"></a>Regressions mått

Följande mått sparas i varje körnings upprepning för en Regressions-eller prognos uppgift.

|Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--|
explained_variance|Beskrivs varians är den som en matematisk modell-konton för variant av en viss uppsättning data. Det är i procent minskning i variansen för den ursprungliga informationen med variansen av fel. När medelvärdet av felen är 0, är det lika beskrivs avvikelse.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Inget|
r2_score|R2 är bestämning dvs procent minskningen jämfört med en baslinje-modell som visar medelvärdet av kvadratfel. |[Beräkning](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Inget|
spearman_correlation|Spearman korrelationen är ett nonparametric mått på monotonicity för relationen mellan två datauppsättningar. Till skillnad från Pearson-korrelationen Spearman korrelationen utgår inte från att distribueras normalt båda datauppsättningar. Som andra Korrelations-koefficienter varierar den här mellan-1 och + 1 med 0 innebär ingen korrelation. Sambandet mellan -1 eller + 1 innebär en exakt Monoton relation. Positivt korrelationer innebär att det som x ökar, ökar y. Negativt korrelationer innebär att som x ökar, y minskar.|[Beräkning](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Inget|
mean_absolute_error|Innebära absoluta fel är det förväntade värdet i absoluta värdet för skillnaden mellan mål och förutsägelser|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Inget|
normalized_mean_absolute_error|Normaliserade absoluta fel är absoluta fel dividerat med en uppsättning data|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dela med mängd data|
median_absolute_error|Median absoluta fel är Medianen för alla absolut skillnader mellan mål och förutsägelser. Den här förlusten är robust till extremvärden.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Inget|
normalized_median_absolute_error|Normaliserade median absoluta fel är median absoluta fel dividerat med en uppsättning data|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dela med mängd data|
root_mean_squared_error|Rot innebära kvadratfel är kvadratroten ur den förväntade kvadratskillnaden mellan mål och förutsägelser|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Inget|
normalized_root_mean_squared_error|Normaliserade rot innebära kvadratfel är roten mean kvadratfel dividerat med en uppsättning data|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dela med mängd data|
root_mean_squared_log_error|Rot innebära kvadraten logga fel är kvadratroten ur den förväntade logaritmisk kvadratfel|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Inget|
normalized_root_mean_squared_log_error|Normaliserat rot genomsnitts fel i kvadratroten är ett rot genomsnitts logg fel som delas av data intervallet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dela med mängd data|

### <a name="pvt"></a>Förutsägande vs. True-diagram
#### <a name="what-is-a-predicted-vs-true-chart"></a>Vad är ett förutsägande vs. True-diagram?
Vid förutsägelse jämfört med sant visas relationen mellan ett förutsägande värde och dess korrelerade sanna värde för ett Regressions problem. Det här diagrammet kan användas för att mäta prestanda för en modell som närmare y = x rad de förväntade värdena är, desto bättre noggrannhet för en förutsägelsemodell.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Vad sker i automatiserad ML med det förväntade vs. True-diagrammet?
Efter varje körning visas en förväntad kontra SANT graph för varje regressionsmodell. För att skydda integriteten för värden är binned tillsammans och storleken på varje lagerplats visas som ett stapeldiagram på den nedre delen av diagramområdet. Du kan jämföra förutsägande modell med området ljusare nyans som visar fel marginaler mot perfekt värdet för där modellen ska vara.

#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Exempel 1: en klassificerings modell med låg exakthet
![En Regressions modell med låg precision i förutsägelserna](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Exempel 2: en Regressions modell med hög noggrannhet 
[![en Regressions modell med hög noggrannhet i dess förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram över resten av diagrammet
#### <a name="what-is-a-residuals-chart"></a>Vad är ett resten av diagrammet?
En återstående representerar en observerade y – förväntade y. Om du vill visa en felmarginalen med låg bias bör histogram för restbelopp skapas som en klockformad kurva inriktade på 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Vad sker i automatiserad ML med resten av diagrammet?
Med automatisk ML får du automatiskt ett rest diagram som visar distributionen av fel i förutsägelserna.
#### <a name="what-does-a-good-model-look-like"></a>Vad ser en bra modell ut?
En lämplig modell har vanligt vis en klock kurva eller fel runt noll.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exempel 1: en Regressions modell med en förskjutning i dess fel
![SA Regressions modell med förskjutning i sina fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Exempel 2: en Regressions modell med mer jämn distribution av fel
![En Regressions modell med mer jämn fördelning av fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Modellens tolknings-och funktions prioritet
Med automatisk ML får du en instrument panel för maskin inlärnings tolkning för dina körningar.
Mer information om hur du aktiverar tolknings funktioner finns i [så här aktiverar du](how-to-machine-learning-interpretability-automl.md) tolkning i automatiserade ml experiment.

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.
+ Testa antecknings exemplen för [automatisk Machine Learning modell förklaring](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
