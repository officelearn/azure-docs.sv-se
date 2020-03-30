---
title: Förstå automatiserade ML-resultat
titleSuffix: Azure Machine Learning
description: Lär dig hur du visar och förstår diagram och mått för var och en av dina automatiska maskininlärningskörningar.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283464"
---
# <a name="understand-automated-machine-learning-results"></a>Förstå resultat från automatiserad maskininlärning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du visar och förstår diagrammen och måtten för var och en av dina automatiska maskininlärningskörningar. 

Läs mer om:
+ [Mått, diagram och kurvor för klassificeringsmodeller](#classification)
+ [Mått, diagram och diagram för regressionsmodeller](#regression)
+ [Modelltolkbarhet och funktionsvikt](#explain-model)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Skapa ett experiment för din automatiska maskininlärningskörning, antingen med SDK eller i Azure Machine Learning studio.

    * Använd SDK för att skapa en [klassificeringsmodell](how-to-auto-train-remote.md) eller [regressionsmodell](tutorial-auto-train-models.md)
    * Använd [Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) för att skapa en klassificerings- eller regressionsmodell genom att ladda upp lämpliga data.

## <a name="view-the-run"></a>Visa körningen

När du har kört ett automatiserat maskininlärningsexperiment finns en historik över körningarna på arbetsytan för maskininlärning. 

1. Gå till din arbetsyta.

1. Välj **Experiment**på arbetsytans vänstra panel .

   ![Skärmbild av experimentmenyn](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Välj den du vill utforska i listan över experiment.

   [![Experimentlista](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Välj **kör**i den nedre tabellen .

   [ ![Experimentkörning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. I modellerna väljer du **algoritmnamnet** för den modell som du vill utforska ytterligare.

   [![Experimentmodell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Du ser också samma resultat under en `RunDetails`körning när du använder [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification-results"></a><a name="classification"></a>Klassificeringsresultat

Följande mått och diagram är tillgängliga för alla klassificeringsmodeller som du skapar med hjälp av de automatiserade machine learning-funktionerna i Azure Machine Learning

+ [Statistik](#classification-metrics)
+ [Matris för förvirring](#confusion-matrix)
+ [Precisions-recall-diagram](#precision-recall-chart)
+ [Mottagarens driftsegenskaper (eller ROC)](#roc)
+ [Lyftkurva](#lift-curve)
+ [Vinster kurva](#gains-curve)
+ [Kalibreringstomt](#calibration-plot)

### <a name="classification-metrics"></a>Klassificeringsmått

Följande mått sparas i varje körning iteration för en klassificeringsuppgift.

Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--
AUC_Macro| AUC är området under mottagarens funktionsegenskaperkurva. Makro är aritmetiska medelvärdet av AUC för varje klass.  | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="makro"|
AUC_Micro| AUC är området under mottagarens funktionsegenskaperkurva. Micro beräknas globalt genom att kombinera de sanna positiva och falska positiva resultaten från varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC är området under mottagarens funktionsegenskaperkurva. Viktad är det aritmetiska medelvärdet av poängen för varje klass, viktat med antalet sanna förekomster i varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="viktad"
accuracy|Noggrannhet är procentandelen förutspådde etiketter som exakt matchar de sanna etiketterna. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Inget|
average_precision_score_macro|Genomsnittlig precision sammanfattar en precision-recall kurva som det viktade medelvärdet av precisioner uppnås vid varje tröskel, med ökningen av återkallande från den tidigare tröskeln som används som vikt. Makro är det aritmetiska medelvärdet för den genomsnittliga precisionspoängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="makro"|
average_precision_score_micro|Genomsnittlig precision sammanfattar en precision-recall kurva som det viktade medelvärdet av precisioner uppnås vid varje tröskel, med ökningen av återkallande från den tidigare tröskeln som används som vikt. Micro beräknas globalt genom att kombinera de sanna positiva och falska positiva vid varje cutoff.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Genomsnittlig precision sammanfattar en precision-recall kurva som det viktade medelvärdet av precisioner uppnås vid varje tröskel, med ökningen av återkallande från den tidigare tröskeln som används som vikt. Viktad är det aritmetiska medelvärdet av den genomsnittliga precisionspoängen för varje klass, viktat med antalet sanna förekomster i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="viktad"|
balanced_accuracy|Balanserad noggrannhet är det aritmetiska medelvärdet för återkallande för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="makro"|
f1_score_macro|F1 poäng är det harmoniska medelvärdet av precision och återkallande. Makro är det aritmetiska medelvärdet av F1-poäng för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="makro"|
f1_score_micro|F1 poäng är det harmoniska medelvärdet av precision och återkallande. Micro beräknas globalt genom att räkna den totala sanna positiva, falska negativ och falska positiva.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 poäng är det harmoniska medelvärdet av precision och återkallande. Viktad medelvärde med klassfrekvens för F1-poäng för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="viktad"|
log_loss|Detta är förlustfunktionen som används i (multinomial) logistisk regression och tillägg av det såsom neurala nätverk, definieras som den negativa log-sannolikheten för de sanna etiketterna ges en probabilistic klassificerare förutsägelser. För ett enda prov med {0,1} true label yt in och uppskattad sannolikhet yp som yt = 1, är loggförlusten -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Inget|
norm_macro_recall|Normaliserad makro recall är Macro Recall normaliserad så att slumpmässiga prestanda har poängen 0 och perfekt prestanda har poängen 1. Detta uppnås genom norm_macro_recall := (recall_score_macro - R)/(1 - R), där R är det förväntade värdet av recall_score_macro för slumpmässiga förutsägelser (dvs. R=0,5 för binär klassificering och R=(1/C) för C-klassklassificeringsproblem).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro" |
precision_score_macro|Precision är procentandelen positivt förutspådde element som är korrekt märkta. Makro är det aritmetiska precisionsvärdet för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="makro"|
precision_score_micro|Precision är procentandelen positivt förutspådde element som är korrekt märkta. Micro beräknas globalt genom att räkna den totala sanna positiva och falska positiva.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision är procentandelen positivt förutspådde element som är korrekt märkta. Viktad är det aritmetiska precisionsmedelvärdet för varje klass, viktat med antal sanna förekomster i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="viktad"|
recall_score_macro|Recall är procentandelen korrekt märkta element i en viss klass. Makro är det aritmetiska medelvärdet för återkallande för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="makro"|
recall_score_micro|Recall är procentandelen korrekt märkta element i en viss klass. Micro beräknas globalt genom att räkna den totala sanna positiva, falska negativ och falska positiva|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall är procentandelen korrekt märkta element i en viss klass. Viktad är det aritmetiska medelvärdet för återkallande för varje klass, viktat med antal sanna förekomster i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="viktad"|
weighted_accuracy|Viktad noggrannhet är noggrannhet där vikten som ges till varje exempel är lika med andelen sant förekomster i det exemplets sanna klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight är en vektor som är lika med andelen av den klassen för varje element i målet|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Matris för förvirring

#### <a name="what-is-a-confusion-matrix"></a>Vad är en förvirringsmatris?
En förvirringsmatris används för att beskriva prestanda för en klassificeringsmodell. Varje rad visar förekomsterna av den sanna eller faktiska klassen i datauppsättningen, och varje kolumn representerar förekomsterna av klassen som förutsades av modellen. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Vad gör automatiserade ML med förvirring matris?
För klassificeringsproblem tillhandahåller Azure Machine Learning automatiskt en förvirringsmatris för varje modell som är byggd. För varje förväxlingsmatris visar automatiserad ML frekvensen för varje förväntad etikett (kolumn) jämfört med den sanna etiketten (raden). Ju mörkare färg, desto högre antal i just den delen av matrisen. 

#### <a name="what-does-a-good-model-look-like"></a>Hur ser en bra modell ut?
Vi jämför det faktiska värdet av datauppsättningen mot de förväntade värden som modellen gav. På grund av detta har maskininlärningsmodeller högre noggrannhet om modellen har de flesta av sina värden längs diagonalen, vilket innebär att modellen förutsåg rätt värde. Om en modell har klass obalans, kommer förvirring matrisen bidra till att upptäcka en partisk modell.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exempel 1: En klassificeringsmodell med dålig noggrannhet
![En klassificeringsmodell med dålig noggrannhet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exempel 2: En klassificeringsmodell med hög noggrannhet 
![En klassificeringsmodell med hög noggrannhet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exempel 3: En klassificeringsmodell med hög noggrannhet och hög bias i modellförutsägelser
![En klassificeringsmodell med hög noggrannhet och hög bias i modellförutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Diagram över precisionsåterkallelse
#### <a name="what-is-a-precision-recall-chart"></a>Vad är ett precisions-recall-diagram?
Precisionsåterkallningskurvan visar förhållandet mellan precision och återkallande från en modell. Termen precision representerar den möjligheten för en modell att märka alla instanser korrekt. Recall representerar möjligheten för en klassificerare att hitta alla instanser av en viss etikett.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Vad gör automatiserade ML med precision-recall diagram?

Med det här diagrammet kan du jämföra precisionsåterkallelsekurvorna för varje modell för att avgöra vilken modell som har en acceptabel relation mellan precision och återkallande för just ditt affärsproblem. Det här diagrammet visar Makrogenomsnittsprecision-recall, Micro Average Precision-Recall och precision-recall som är associerade med alla klasser för en modell. 

Makrogenomsnittet beräknar måttet oberoende av varje klass och tar sedan medelvärdet och behandlar alla klasser lika. Mikrogenomsnittet sammanställer dock bidragen från alla klasser för att beräkna genomsnittet. Mikrogenomsnitt är att föredra om det finns klassobalans i datauppsättningen.

#### <a name="what-does-a-good-model-look-like"></a>Hur ser en bra modell ut?
Beroende på målet med affärsproblemet kan den idealiska precisionsåterkallelsekurvan skilja sig åt. Några exempel ges nedan

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exempel 1: En klassificeringsmodell med låg precision och låg återkallelse
![En klassificeringsmodell med låg precision och låg återkallelse](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exempel 2: En klassificeringsmodell med ~100% precision och ~100% återkallelse 
![En klassificeringsmodell med hög precision och återkallelse](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC-diagram

#### <a name="what-is-a-roc-chart"></a>Vad är ett ROC-diagram?
Mottagarens funktionsegenskaper (eller ROC) är ett område med korrekt klassificerade etiketter jämfört med de felaktigt klassificerade etiketterna för en viss modell. ROC-kurvan kan vara mindre informativ när du tränar modeller på datauppsättningar med hög bias, eftersom den inte kommer att visa de falska positiva etiketterna.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Vad gör automatiserade ML med ROC-diagrammet?
Automatiserad ML genererar Makro genomsnittlig Precision-Recall, Micro Average Precision-Recall, och precision-recall associeras med alla klasser för en modell. 

Makrogenomsnittet beräknar måttet oberoende av varje klass och tar sedan medelvärdet och behandlar alla klasser lika. Mikrogenomsnittet sammanställer dock bidragen från alla klasser för att beräkna genomsnittet. Mikrogenomsnitt är att föredra om det finns klassobalans i datauppsättningen.

#### <a name="what-does-a-good-model-look-like"></a>Hur ser en bra modell ut?
Helst kommer modellen att ha närmare 100% sann positiv ränta och närmare 0% falsk positiv ränta. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exempel 1: En klassificeringsmodell med låga sanna etiketter och höga falska etiketter
![Klassificeringsmodell med låga sanna etiketter och höga falska etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exempel 2: En klassificeringsmodell med höga sanna etiketter och låga falska etiketter
![en klassificeringsmodell med höga sanna etiketter och låga falska etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Lyftdiagram
#### <a name="what-is-a-lift-chart"></a>Vad är ett lyftdiagram?
Lyftdiagram används för att utvärdera prestanda för en klassificeringsmodell. Det visar hur mycket bättre du kan förvänta dig att göra med den genererade modellen jämfört med utan en modell när det gäller noggrannhet.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Vad gör automatiserade ML med hissdiagrammet?
Du kan jämföra lyftet för modellen som skapas automatiskt med Azure Machine Learning med baslinjen för att visa värdeökningen för just den modellen.
#### <a name="what-does-a-good-model-look-like"></a>Hur ser en bra modell ut?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Exempel 1: En klassificeringsmodell som gör sämre än en slumpmässig urvalsmodell
![En klassificeringsmodell som gör sämre än en slumpmässig urvalsmodell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exempel 2: En klassificeringsmodell som presterar bättre än en slumpmässig urvalsmodell
![En klassificeringsmodell som fungerar bättre](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Vinstdiagram
#### <a name="what-is-a-gains-chart"></a>Vad är ett vinstdiagram?

Ett vinstdiagram utvärderar prestanda för en klassificeringsmodell för varje del av data. Den visar för varje percentil i datauppsättningen, hur mycket bättre du kan förvänta dig att prestera jämfört med en slumpmässig urvalsmodell.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Vad gör automatiserade ML göra med vinster diagrammet?
Använd diagrammet ackumulerade vinster för att välja klassificeringsavskärning med en procentsats som motsvarar en önskad vinst från modellen. Denna information ger ett annat sätt att titta på resultaten i det medföljande hissdiagrammet.

#### <a name="what-does-a-good-model-look-like"></a>Hur ser en bra modell ut?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exempel 1: En klassificeringsmodell med minimal förstärkning
![en klassificeringsmodell med minimal förstärkning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exempel 2: En klassificeringsmodell med betydande vinst
![En klassificeringsmodell med betydande](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Kalibreringsdiagram

#### <a name="what-is-a-calibration-chart"></a>Vad är ett kalibreringsdiagram?
Ett kalibreringsdiagram används för att visa förtroendet hos en prediktiv modell. Detta gör detta genom att visa förhållandet mellan den förväntade sannolikheten och den faktiska sannolikheten, där "sannolikhet" representerar sannolikheten för att en viss instans hör under någon etikett.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Vad gör automatiserade ML med kalibreringsdiagrammet?
För alla klassificeringsproblem kan du granska kalibreringslinjen för mikrogenomsnitt, makrogenomsnitt och varje klass i en viss prediktiv modell.

Makrogenomsnittet beräknar måttet oberoende av varje klass och tar sedan medelvärdet och behandlar alla klasser lika. Mikrogenomsnittet sammanställer dock bidragen från alla klasser för att beräkna genomsnittet. 
#### <a name="what-does-a-good-model-look-like"></a>Hur ser en bra modell ut?
 En väl kalibrerad modell ligger i linje med y=x-linjen, där den är någorlunda säker på sina förutsägelser. En översäker modell ligger i linje med y=0-linjen, där den förväntade sannolikheten finns men det inte finns någon faktisk sannolikhet. 


##### <a name="example-1-a-well-calibrated-model"></a>Exempel 1: En väl kalibrerad modell
![ mer välkalibrerad modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Exempel 2: En översäker modell
![En översäker modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Regressionsresultat

Följande mått och diagram är tillgängliga för alla regressionsmodeller som du skapar med hjälp av de automatiserade machine learning-funktionerna i Azure Machine Learning

+ [Statistik](#reg-metrics)
+ [Förutsagt kontra sant](#pvt)
+ [Histogram av rester](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Regressionsmått

Följande mått sparas i varje körning iteration för en regression eller prognoser aktivitet.

|Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--|
explained_variance|Förklarad varians är den proportion som en matematisk modell står för variationen i en viss datauppsättning. Det är den procentuella minskningen av variansen för de ursprungliga data till variansen för felen. När felvärdet är 0 är det lika med förklarad varians.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Inget|
r2_score|R2 är bestämningskoefficienten eller procentuell minskning av kvadratfel jämfört med en baslinjemodell som matar ut medelvärdet. |[Beräkning](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Inget|
spearman_correlation|Spearman korrelation är ett ickeparametriskt mått på monotoniteten i förhållandet mellan två datamängder. Till skillnad från Pearson-korrelationen förutsätter spearmankorrelationen inte att båda datauppsättningarna normalt distribueras. Liksom andra korrelationskoefficienter varierar den här mellan -1 och +1 med 0 vilket innebär ingen korrelation. Korrelationer av -1 eller +1 innebär ett exakt monotoniskt förhållande. Positiva korrelationer innebär att som x ökar, så gör y. Negativa korrelationer innebär att när x ökar, minskar y.|[Beräkning](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Inget|
mean_absolute_error|Genomsnittligt absolut fel är det förväntade värdet av absoluta skillnaden mellan målet och förutsägelsen|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Inget|
normalized_mean_absolute_error|Normaliserat genomsnittligt absolut fel är genomsnittligt absolut fel dividerat med dataområdet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividera med dataintervall|
median_absolute_error|Median absolut fel är medianen för alla absoluta skillnader mellan målet och förutsägelsen. Denna förlust är robust för extremvärden.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Inget|
normalized_median_absolute_error|Normaliserat medianresolutfel är medianresolet fel dividerat med dataområdet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividera med dataintervall|
root_mean_squared_error|Rotmedelvärdet kvadratfel är kvadratroten för den förväntade kvadratskillnaden mellan målet och förutsägelsen|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Inget|
normalized_root_mean_squared_error|Normaliserat rotmedelvärdesrutat fel är rotmedelstvadrat fel dividerat med dataområdet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividera med dataintervall|
root_mean_squared_log_error|Rotmedelvärdet för kvadratisk logg fel är kvadratroten för det förväntade kvadratiska logaritmiska felet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Inget|
normalized_root_mean_squared_log_error|Normaliserat rotmedelvärdesrutad loggfel är rotmedelvadvadtloggfel dividerat med dataområdet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividera med dataintervall|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Förutsagt kontra sant diagram
#### <a name="what-is-a-predicted-vs-true-chart"></a>Vad är ett predicted vs. True-diagram?
Förutsagt kontra sant visar förhållandet mellan ett förväntat värde och dess korrelerande verkliga värde för ett regressionsproblem. Detta diagram kan användas för att mäta prestanda för en modell som närmare y = x linje de förväntade värdena är, desto bättre noggrannhet en prediktiv modell.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Vad gör automatiserade ML med predicted vs True-diagrammet?
Efter varje körning kan du se ett förutspått kontra sant diagram för varje regressionsmodell. För att skydda datasekretessen lagerförs värdena ihop och storleken på varje lagerplats visas som ett stapeldiagram i den nedre delen av diagramområdet. Du kan jämföra den prediktiva modellen med det ljusare skuggområdet som visar felmarginaler, mot det ideala värdet för var modellen ska vara.

#### <a name="what-does-a-good-model-look-like"></a>Hur ser en bra modell ut?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Exempel 1: En klassificeringsmodell med låg noggrannhet
![En regressionsmodell med låg noggrannhet i förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Exempel 2: En regressionsmodell med hög noggrannhet 
[![En regressionsmodell med hög noggrannhet i förutsägelserna](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Histogram av rester diagram
#### <a name="what-is-a-residuals-chart"></a>Vad är ett restdiagram?
En kvarvarande representerar en observerad y – den förväntade y. För att visa en felmarginal med låg bias, bör histogrammet av rester formas som en klockkurva, centrerad kring 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Vad gör automatiserade ML med rester diagrammet?
Automatiserad ML ger automatiskt ett restdiagram för att visa fördelningen av fel i förutsägelserna.
#### <a name="what-does-a-good-model-look-like"></a>Hur ser en bra modell ut?
En bra modell har vanligtvis en klockkurva eller fel runt noll.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exempel 1: En regressionsmodell med fördomar i sina fel
![SA regressionsmodell med fördomar i sina fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Exempel 2: En regressionsmodell med jämnare fördelning av fel
![En regressionsmodell med jämnare fördelning av fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Modelltolkbarhet och funktionsvikt
Automatiserad ML ger en instrumentpanel för maskininlärningstolkbarhet för dina körningar.
Mer information om hur du aktiverar tolkningsfunktioner finns i hur du aktiverar tolkningsbarhet i automatiserade [ML-experiment.](how-to-machine-learning-interpretability-automl.md)

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.
+ Prova exempelböcker [för automatisk maskininlärningsmodellförklaring.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
