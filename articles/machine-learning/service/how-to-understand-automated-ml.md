---
title: Förstå automatiserade ML-resultat
titleSuffix: Azure Machine Learning service
description: Lär dig att visa och förstå diagram och mått för var och en av dina automatiserade maskin inlärnings körningar.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: a5632767ef8230d16b47f350436fda53794efdf5
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872585"
---
# <a name="understand-automated-machine-learning-results"></a>Förstå automatiserade maskin inlärnings resultat

I den här artikeln får du lära dig att visa och förstå diagram och mått för var och en av dina automatiserade maskin inlärnings körningar. 

Läs mer om:
+ [Mått, diagram och kurvor för klassificerings modeller](#classification)
+ [Mått, diagram och diagram för Regressions modeller](#regression)
+ [Modellens tolknings-och funktions prioritet](#explain-model)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.

* Skapa ett automatiskt maskin inlärnings experiment, antingen med SDK eller i Azure Portal.

    * Använd SDK: n för att bygga en [klassificerings modell](how-to-auto-train-remote.md) eller [Regressions modell](tutorial-auto-train-models.md)
    * Använd [Azure Portal](how-to-create-portal-experiments.md) för att skapa en klassificerings-eller Regressions modell genom att överföra relevanta data.

## <a name="view-the-run"></a>Visa körningen

När du har kört ett automatiserat experiment med Machine Learning finns en historik över körningarna i arbets ytan Machine Learning-tjänst. 

1. Gå till din arbetsyta.

1. Välj **experiment**i den vänstra panelen i arbets ytan.

   ![Skärmbild av menyn för experiment](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. I listan över experiment väljer du det som du vill utforska.

   [![Experiment lista](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Välj **körnings numret**i den nedre tabellen.

   [ Experimentkörning![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. I upprepnings tabellen väljer du upprepnings **numret** för den modell som du vill utforska vidare.

   [![Experiment modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Du ser även samma resultat under en körning när du använder `RunDetails` [Jupyter-widgeten](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

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

|Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--|
AUC_Macro| AUC är området under mottagare fungerar egenskap kurvan. Makrot är det aritmetiska medelvärdet av AUC för varje klass.  | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Genomsnittlig = ”makrot”|
AUC_Micro| AUC är området under mottagare fungerar egenskap kurvan. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringar från varje klass| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Genomsnittlig = ”micro”|
AUC_Weighted  | AUC är området under mottagare fungerar egenskap kurvan. Viktat är det aritmetiska medelvärdet av poängen för varje klass, viktat av antalet instanser som true i varje klass| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Genomsnittlig = ”viktat”
accuracy|Procent av förväntade etiketter som exakt matchar SANT etiketter är. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Ingen|
average_precision_score_macro|Genomsnittlig precision sammanfattas en precisionsåterkallningsdiagram kurva som viktad medelvärdet av Precision-datorerna som kan uppnås på varje tröskelvärde ökade återkallande från föregående tröskelvärdet används som vikten. Makrot är det aritmetiska medelvärdet av den genomsnittliga precision poängen för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Genomsnittlig = ”makrot”|
average_precision_score_micro|Genomsnittlig precision sammanfattas en precisionsåterkallningsdiagram kurva som viktad medelvärdet av Precision-datorerna som kan uppnås på varje tröskelvärde ökade återkallande från föregående tröskelvärdet används som vikten. Micro beräknas globalt av nyhetsnotiser genomsöks positiva och falska positiva identifieringar vid varje brytfrekvens|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Genomsnittlig = ”micro”|
average_precision_score_weighted|Genomsnittlig precision sammanfattas en precisionsåterkallningsdiagram kurva som viktad medelvärdet av Precision-datorerna som kan uppnås på varje tröskelvärde ökade återkallande från föregående tröskelvärdet används som vikten. Viktat är det aritmetiska medelvärdet av den genomsnittliga precision poängen för varje klass, viktat av antalet instanser som true i varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Genomsnittlig = ”viktat”|
balanced_accuracy|Belastningsutjämnade är det aritmetiska medelvärdet om återkallande för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”makrot”|
f1_score_macro|F1 poängen är det harmoniska medelvärdet av precision och återkallande. Makrot är det aritmetiska medelvärdet av F1 poängen för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Genomsnittlig = ”makrot”|
f1_score_micro|F1 poängen är det harmoniska medelvärdet av precision och återkallande. Micro beräknas globalt genom att räkna den totala positiva och falska negativ falska positiva identifieringar|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Genomsnittlig = ”micro”|
f1_score_weighted|F1 poängen är det harmoniska medelvärdet av precision och återkallande. Viktat medelvärde av klassen frekvensen av F1 poängen för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Genomsnittlig = ”viktat”|
log_loss|Detta är funktionen för förlust som används i (multinomialen) logistic regression och tillägg av den som neurala nätverk, definierad som negativt log-sannolikheten för true etiketterna får en avsnittet om sannolikhetsbunden klassificerare förutsägelser. Etikett för en enda exemplet med true yt i {0,1} och uppskattade sannolikheten yp den yt = 1, log förlusten är – logga P (yt&#124;yp) =-(yt log(yp) + (1 - yt) log (1 - yp))|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Ingen|
norm_macro_recall|Normaliserade makrot återkalla är makrot återkalla normalized så att slumpmässiga prestanda har ett värde på 0 och perfekt prestanda har ett värde på 1. Detta uppnås med norm_macro_recall: = (recall_score_macro - R) /(1-R), där R är det förväntade värdet recall_score_macro för slumpmässiga förutsägelser (d.v.s. R = 0,5 för binär klassificering) och R=(1/C) för problem med C-class klassificering|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”makrot” och sedan (recall_score_macro - R) /(1-R), där R är det förväntade värdet recall_score_macro för slumpmässiga förutsägelser (d.v.s. R = 0,5 för binär klassificering) och R=(1/C) för problem med C-class klassificering|
precision_score_macro|Precisionen är procent av element som är märkt som en viss klass som faktiskt finns i den klassen. Makrot är det aritmetiska medelvärdet av precision för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”makrot”|
precision_score_micro|Precisionen är procent av element som är märkt som en viss klass som faktiskt finns i den klassen. Micro beräknas globalt genom att räkna totala positiva och falska positiva identifieringar|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”micro”|
precision_score_weighted|Precisionen är procent av element som är märkt som en viss klass som faktiskt finns i den klassen. Viktat är det aritmetiska medelvärdet av precision för varje klass, viktat av antalet SANT instanser i varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”viktat”|
recall_score_macro|Återkallande är procent av element faktiskt i en viss klass som är märkta korrekt. Makrot är det aritmetiska medelvärdet om återkallande för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”makrot”|
recall_score_micro|Återkallande är procent av element faktiskt i en viss klass som är märkta korrekt. Micro beräknas globalt genom att räkna totala positiva, false negativ|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”micro”|
recall_score_weighted|Återkallande är procent av element faktiskt i en viss klass som är märkta korrekt. Viktat är det aritmetiska medelvärdet om återkallande för varje klass, viktat av antalet SANT instanser i varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”viktat”|
weighted_accuracy|Viktad Precision är Precision där vikten för varje exempel är lika med andelen SANT instanser i det exemplet true-klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight är en vektor som är lika med andelen av den klassen för varje element i målet|

### <a name="confusion-matrix"></a>Felmatris

En felmatris används för att beskriva resultatet av en modell för klassificering. Varje rad visar instanserna av klassen SANT och varje kolumn representerar instanser av den förväntade klassen. Felmatrisen visar korrekt klassificerade etiketter och felaktigt klassificerad etiketter för en viss modell.

För klassificering, problem ger Azure Machine Learning automatiskt en felmatris för varje modell som har skapats. För varje felmatris visas automatiserade ML korrekt klassificerade etiketterna som gröna och felaktigt klassificerad etiketter rött. Storleken på cirkeln representerar antalet prov i lagerplatsen. Dessutom finns frekvens antal förväntade etiketterna och varje SANT etikett i intilliggande stapeldiagram. 

Exempel 1: En klassificerings modell med dålig ![precision för en klassificerings modell med dålig precision](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Exempel 2: En klassificerings modell med hög noggrannhet (idealisk ![) en klassificerings modell med hög exakthet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Precisionsåterkallningsdiagram diagram

Du kan jämföra precisionsåterkallningsdiagram kurvor för varje modell att avgöra vilken modell som har en godtagbar relation mellan precision och återkallande för din specifika affärsproblem med det här diagrammet. Det här diagrammet visar makrot genomsnittlig Precisionsåterkallningsdiagram, Micro genomsnittliga Precisionsåterkallningsdiagram och precisionsåterkallningsdiagram som är associerade med alla klasser för en modell.

Termen Precision representerar den möjligheten för en klassificerare att märka alla instanser korrekt. Återkallande representerar möjligheten för en klassificerare att hitta alla instanser av en viss etikett. Precisionsåterkallningsdiagram kurvan visar relationen mellan dessa två begrepp. Vi rekommenderar skulle modellen ha 100% noggrannhet och 100% noggrannhet.

Exempel 1: En klassificerings modell med låg precision och låg ![återkallning av en klassificerings modell med låg precision och låg återkallning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Exempel 2: En klassificerings modell med ~ 100% precision och ~ 100% återkalla (idealisk ![) en klassificerings modell med hög precision och återkallande](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Mottagare som fungerar egenskap (eller ROC) är en rityta för korrekt klassificerade etiketterna jämfört med felaktigt klassificerad etiketter för en viss modell. ROC-kurvan kan vara mindre informativa när modeller för utbildning på datauppsättningar med hög partiskt, som det inte visas falskt positivt etiketter.

Exempel 1: En klassificerings modell med låg sant etikett och etikett ![modell med hög falsk etikett med låg sant etiketter och höga Felaktiga etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Exempel 2: En klassificerings modell med High True Labels och low ![false etiketter en klassificerings modell med höga sanna etiketter och låg falskt etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Flytta kurva

Du kan jämföra hissen av modellen som skapats automatiskt med Azure Machine Learning till i baslinjen för att kunna visa vinsten värdet för den specifika modellen.

Lift scheman används för att utvärdera prestanda för en modell för klassificering. Den visar hur mycket bättre du kan förvänta dig att göra med en modell som jämfört med utan en modell. 

Exempel 1: Modellen presterar sämre än en slumpmässig markerings modell ![som är en klassificerings modell som är sämre än en slumpmässig markerings modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Exempel 2: Modellen fungerar bättre än en slumpmässig markerings ![modell som en klassificerings modell som fungerar bättre](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Vinster kurva

Ett diagram får utvärderar prestanda för en modell för klassificering av varje del av data. Den visar för vardera percentil för datamängd, hur mycket bättre du kan förvänta dig att utföra jämfört med mot ett slumpmässigt urval-modellen.

Använda ackumulerade vinster diagrammet för att hjälpa dig att välja den klassificering brytfrekvens med hjälp av en procentsats som motsvarar en önskad vinst från modellen. Den här informationen innehåller ett annat sätt att titta på resultaten i den medföljande ökningsdiagrammet.

Exempel 1: En klassificerings modell med minimal ![förstärkning av en klassificerings modell med minimal vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Exempel 2: En klassificerings modell med betydande ![förstärkning av en klassificerings modell med betydande vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Kalibreringskurva

Alla klassificering problem, kan du läsa kalibrering raden för micro medelvärde, makrot medelvärde och varje klass i en viss förutsägelsemodell. 

En kalibreringskurva används för att visa förtroendet hos en förutsägelsemodell. Det gör du genom att som visar relationen mellan sannolikheten förväntade och faktiska sannolikheten, där ”sannolikhet” representerar sannolikheten att en viss instans hör under vissa etiketten. En bra justerat modell överensstämmer med y = x rad, där det är ganska säker på i dess förutsägelser. En modell för över confident överensstämmer med y = 0 rad, där den förväntade sannolikheten finns men det finns inga faktiska sannolikheten.

Exempel 1: En mer välkalibrerad modell ![ som är mer bra kalibrerad modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Exempel 2: En över-trygg modell ![för en över-säker modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressions resultat

Thee följande mått och diagram är tillgängliga för varje Regressions modell som du skapar med hjälp av de automatiserade maskin inlärnings funktionerna i Azure Machine Learning

+ [Mått](#reg-metrics)
+ [Förväntade vs. SANT](#pvt)
+ [Histogram för restbelopp](#histo)


### <a name="reg-metrics"></a>Regressions mått

Följande mått sparas i varje körnings upprepning för en Regressions-eller prognos uppgift.

|Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--|
explained_variance|Beskrivs varians är den som en matematisk modell-konton för variant av en viss uppsättning data. Det är i procent minskning i variansen för den ursprungliga informationen med variansen av fel. När medelvärdet av felen är 0, är det lika beskrivs avvikelse.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Ingen|
r2_score|R2 är bestämning dvs procent minskningen jämfört med en baslinje-modell som visar medelvärdet av kvadratfel. När medelvärdet av felen är 0, är det lika beskrivs avvikelse.|[Beräkning](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Ingen|
spearman_correlation|Spearman korrelationen är ett nonparametric mått på monotonicity för relationen mellan två datauppsättningar. Till skillnad från Pearson-korrelationen Spearman korrelationen utgår inte från att distribueras normalt båda datauppsättningar. Som andra Korrelations-koefficienter varierar den här mellan-1 och + 1 med 0 innebär ingen korrelation. Sambandet mellan -1 eller + 1 innebär en exakt Monoton relation. Positivt korrelationer innebär att det som x ökar, ökar y. Negativt korrelationer innebär att som x ökar, y minskar.|[Beräkning](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Ingen|
mean_absolute_error|Innebära absoluta fel är det förväntade värdet i absoluta värdet för skillnaden mellan mål och förutsägelser|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Ingen|
normalized_mean_absolute_error|Normaliserade absoluta fel är absoluta fel dividerat med en uppsättning data|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dela med mängd data|
median_absolute_error|Median absoluta fel är Medianen för alla absolut skillnader mellan mål och förutsägelser. Den här förlusten är robust till extremvärden.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Ingen|
normalized_median_absolute_error|Normaliserade median absoluta fel är median absoluta fel dividerat med en uppsättning data|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dela med mängd data|
root_mean_squared_error|Rot innebära kvadratfel är kvadratroten ur den förväntade kvadratskillnaden mellan mål och förutsägelser|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Ingen|
normalized_root_mean_squared_error|Normaliserade rot innebära kvadratfel är roten mean kvadratfel dividerat med en uppsättning data|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dela med mängd data|
root_mean_squared_log_error|Rot innebära kvadraten logga fel är kvadratroten ur den förväntade logaritmisk kvadratfel|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Ingen|
normalized_root_mean_squared_log_error|Normaliserat rot genomsnitts fel i kvadratroten är ett rot genomsnitts logg fel som delas av data intervallet|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dela med mängd data|

### <a name="pvt"></a>Förutsägas kontra Sant

Förväntade vs. SANT visar förhållandet mellan ett prognostiserat värde och dess correlating SANT värde för ett regressionsproblem. Det här diagrammet kan användas för att mäta prestanda för en modell som närmare y = x rad de förväntade värdena är, desto bättre noggrannhet för en förutsägelsemodell.

Efter varje körning visas en förväntad kontra SANT graph för varje regressionsmodell. För att skydda integriteten för värden är binned tillsammans och storleken på varje lagerplats visas som ett stapeldiagram på den nedre delen av diagramområdet. Du kan jämföra förutsägande modell med området ljusare nyans som visar fel marginaler mot perfekt värdet för där modellen ska vara.

Exempel 1: En Regressions modell med låg noggrannhet i ![förutsägelser en Regressions modell med låg precision i förutsägelserna](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Exempel 2: En Regressions modell med hög noggrannhet i dess förutsägelser [ ![en Regressions modell med hög noggrannhet i dess förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram över rester

En återstående representerar en observerade y – förväntade y. Om du vill visa en felmarginalen med låg bias bör histogram för restbelopp skapas som en klockformad kurva inriktade på 0. 

Exempel 1: En Regressions modell med bias i sina ![fel sa Regressions modell med en förskjutning i sina fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Exempel 2: En Regressions modell med mer jämn fördelning av ![fel en Regressions modell med mer jämna distributioner av fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Modellens tolknings-och funktions prioritet

Med funktions prioritet kan du se hur värdefull varje funktion var i konstruktionen av en modell. Den här beräkningen är inaktive rad som standard, eftersom den kan öka körnings tiden betydligt.   Du kan aktivera modell förklaring för alla modeller eller förklara bara den bästa anpassnings modellen.

Du kan granska funktionen vikten poäng för modellen övergripande samt per klass på en förutsägelsemodell. Per funktion kan du se hur vikten jämförs mot varje klass och övergripande.

![Funktionen förklara möjlighet](./media/how-to-understand-automated-ml/feature-importance.gif)

Mer information om hur du aktiverar tolknings funktioner finns i [Konfigurera automatiserade ml-experiment i python](how-to-configure-auto-train.md#explain-the-model-interpretability).  Ett exempel som förklarar den bästa modellen finns i [förklaring till bästa modell](how-to-auto-train-remote.md#explain).

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.
+ Testa antecknings exemplet för [automatisk Machine Learning modell förklaring](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
