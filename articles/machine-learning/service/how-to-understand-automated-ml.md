---
title: Förstå automatiserade ML-resultat
titleSuffix: Azure Machine Learning
description: Lär dig att visa och förstå diagram och mått för var och en av dina automatiserade maskin inlärnings körningar.
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 93695e0bbcb81a570519a6f74cfdeab4ef85f076
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489391"
---
# <a name="understand-automated-machine-learning-results"></a>Förstå automatiserade maskin inlärnings resultat
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig att visa och förstå diagram och mått för var och en av dina automatiserade maskin inlärnings körningar. 

Läs mer om:
+ [Mått, diagram och kurvor för klassificerings modeller](#classification)
+ [Mått, diagram och diagram för Regressions modeller](#regression)
+ [Modellens tolknings-och funktions prioritet](#explain-model)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* Skapa ett experiment för din automatiserade Machine Learning-körning, antingen med SDK eller i Azure Machine Learning Studio.

    * Använd SDK: n för att bygga en [klassificerings modell](how-to-auto-train-remote.md) eller [Regressions modell](tutorial-auto-train-models.md)
    * Använd [Azure Machine Learning Studio](how-to-create-portal-experiments.md) för att skapa en klassificerings-eller Regressions modell genom att överföra relevanta data.

## <a name="view-the-run"></a>Visa körningen

När du har kört ett automatiserat experiment med Machine Learning finns en historik över körningarna i din Machine Learning-arbetsyta. 

1. Gå till din arbetsyta.

1. Välj **experiment**i den vänstra panelen i arbets ytan.

   ![Skärm bild av menyn experimentera](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. I listan över experiment väljer du det som du vill utforska.

   [![experiment lista](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Välj **körnings numret**i den nedre tabellen.

   [![experiment körning](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. I upprepnings tabellen väljer du **upprepnings numret** för den modell som du vill utforska vidare.

   [![experiment modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Du ser även samma resultat under en körning när du använder [widgeten `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Klassificerings resultat

Thee följande mått och diagram är tillgängliga för alla klassificerings modeller som du skapar med hjälp av de automatiserade maskin inlärnings funktionerna i Azure Machine Learning

+ [Mått](#classification-metrics)
+ [Förvirring mat ris](#confusion-matrix)
+ [Diagram över precisions återställning](#precision-recall-chart)
+ [Egenskaper för mottagar drift (eller ROC)](#roc)
+ [Lyft kurva](#lift-curve)
+ [Vinster-kurva](#gains-curve)
+ [Kalibrerings område](#calibration-plot)

### <a name="classification-metrics"></a>Klassificerings mått

Följande mått sparas i varje körnings upprepning för en klassificerings uppgift.

|Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--|
AUC_Macro| AUC är den del av den egenskaps kurva som används för mottagaren. Makro är det aritmetiska medelvärdet av AUC för varje klass.  | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | genomsnitt = "makro"|
AUC_Micro| AUC är den del av den egenskaps kurva som används för mottagaren. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringar från varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | genomsnitt = "Micro"|
AUC_Weighted  | AUC är den del av den egenskaps kurva som används för mottagaren. Viktat är det aritmetiska medelvärdet av poängen för varje klass, viktat med antalet sanna instanser i varje klass.| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Average = "viktad"
accuracy|Noggrannhet är procent andelen förväntade etiketter som exakt matchar de sanna etiketterna. |[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Ingen|
average_precision_score_macro|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Makro är det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|genomsnitt = "makro"|
average_precision_score_micro|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Micro beräknas globalt genom att kombinera de sanna positiva positiva och falska positiva identifieringarna vid varje avgränsare.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|genomsnitt = "Micro"|
average_precision_score_weighted|Genomsnittlig precision sammanfattar en precisions återställnings kurva som viktat medelvärde för de precisioner som uppnås vid varje tröskelvärde, med en ökning av återställningen från föregående tröskel som används som vikt. Viktat är det aritmetiska medelvärdet av den genomsnittliga precisions poängen för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "viktad"|
balanced_accuracy|Balanserade noggrannhet är det aritmetiska medelvärdet för återkallning för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro"|
f1_score_macro|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Makro är det aritmetiska medelvärdet av F1-poängen för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|genomsnitt = "makro"|
f1_score_micro|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Micro beräknas globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|genomsnitt = "Micro"|
f1_score_weighted|F1-Poäng är det harmoniska medelvärdet av precision och återkallande. Viktat medelvärde efter klass frekvens för F1-Poäng för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "viktad"|
log_loss|Detta är den förlust funktion som används i (MULTINOMIAL) Logistisk regression och tillägg, till exempel neurala Networks, definierad som den negativa logg sannolikheten för de faktiska etiketterna med en Probabilistic klassificerares förutsägelser. För ett enda exempel med True Label yt i {0,1} och uppskattad sannolikhet YP som yt = 1, är logg förlusten-log P (&#124;yt YP) =-(yt logg (YP) + (1-yt) log (1-YP)).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Ingen|
norm_macro_recall|Normaliserat makro återkallande är ett makro som är normaliserat så att slumpmässiga prestanda har en poäng på 0 och perfekt prestanda har en poäng på 1. Detta uppnås av norm_macro_recall: = (recall_score_macro-R)/(1-R), där R är det förväntade värdet för recall_score_macro för slumpmässiga förutsägelser (t. ex. R = 0,5 för binär klassificering och R = (1/C) för klassificerings problem i C-klass).|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro" |
precision_score_macro|Precision är procent andelen element som är märkta som en viss klass som faktiskt finns i klassen. Makro är det aritmetiska medelvärdet för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|genomsnitt = "makro"|
precision_score_micro|Precision är procent andelen element som är märkta som en viss klass som faktiskt finns i klassen. Micro beräknas globalt genom att räkna antalet sanna positiva positiva och falska positiva identifieringar.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|genomsnitt = "Micro"|
precision_score_weighted|Precision är procent andelen element som är märkta som en viss klass som faktiskt finns i klassen. Viktat är det aritmetiska medelvärdet för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "viktad"|
recall_score_macro|Återkallande är procent andelen element som faktiskt är i en viss klass som är korrekt märkt. Makro är det aritmetiska medelvärdet för återkallning för varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "makro"|
recall_score_micro|Återkallande är procent andelen element som faktiskt är i en viss klass som är korrekt märkt. Micro beräknas globalt genom att räkna antalet sanna positiva identifieringar, falska negativa negativa och falska positiva identifieringar|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|genomsnitt = "Micro"|
recall_score_weighted|Återkallande är procent andelen element som faktiskt är i en viss klass som är korrekt märkt. Viktat är det aritmetiska medelvärdet för återkallning för varje klass, viktat med antalet sanna instanser i varje klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "viktad"|
weighted_accuracy|Viktad noggrannhet är exakthet där vikten för varje exempel motsvarar proportionerna för de sanna instanserna i det här exemplets sanna klass.|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight är en Vector som motsvarar den andel av klassen för varje element i målet|

### <a name="confusion-matrix"></a>Förvirring mat ris

En förvirring mat ris används för att beskriva prestanda för en klassificerings modell. Varje rad visar instanser av klassen True och varje kolumn representerar instanserna av den förväntade klassen. Förväxlings mat ris visar de korrekt klassificerade etiketterna och de felaktigt klassificerade etiketterna för en specifik modell.

För klassificerings problem tillhandahåller Azure Machine Learning automatiskt en Förväxlings mat ris för varje modell som har skapats. För varje förvirrings mat ris visar automatiserad ML frekvensen för varje förväntad etikett och varje sann etikett skärnings punkt. Ju mörkare färgen, desto högre är antalet i den specifika delen av matrisen. Vi rekommenderar att de mörkaste färgerna är längs diagonalen av matrisen. 

Exempel 1: en klassificerings modell med låg precision ![en klassificerings modell med dålig precision](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Exempel 2: en klassificerings modell med hög noggrannhet (idealisk) ![en klassificerings modell med hög exakthet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Diagram över precisions återställning

Med det här diagrammet kan du jämföra precisions återställnings kurvorna för varje modell för att avgöra vilken modell som har en acceptabel relation mellan precisionen och återställningen av ditt specifika affärs problem. Det här diagrammet visar Genomsnittligt antal återkallning i makron, mikrogenomsnittlig precisions återställning och precisions åter kallelse som är associerad med alla klasser för en modell.

Termen precision representerar möjligheten för en klassificerare att märka alla instanser korrekt. Återkallande representerar möjligheten för en klassificerare att hitta alla instanser av en viss etikett. Kurvan om precisions åter kallelse visar relationen mellan dessa två begrepp. Helst skulle modellen ha 100% precision och 100% noggrannhet.

Exempel 1: en klassificerings modell med låg precision och låg återkallning ![en klassificerings modell med låg precision och låg återställnings](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Exempel 2: en klassificerings modell med ~ 100% precision och ~ 100% återkalla (idealisk) ![en klassificerings modell med hög precision och återkallande](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

Mottagarens operativa egenskaper (eller ROC) är ett diagram över de korrekt klassificerade etiketterna eller de felaktigt klassificerade etiketterna för en viss modell. ROC-kurvan kan vara mindre informativ vid inlärnings modeller på data uppsättningar med hög förskjutning, eftersom det inte visar de falskt positiva etiketter.

Exempel 1: en klassificerings modell med låg sant etikett och höga falskt-etiketter ![klassificerings modell med låg sant etikett och hög falskt etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Exempel 2: en klassificerings modell med High True Labels och low false-etiketter ![en klassificerings modell med höga True-etiketter och låg falskt etiketter](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Lyft kurva

Du kan jämföra hissen med modellen som skapats automatiskt med Azure Machine Learning till bas linjen för att se den aktuella modellens värde vinst.

Lyft diagram används för att utvärdera prestanda för en klassificerings modell. Det visar hur mycket som är bättre att göra med en modell som jämförs med utan en modell. 

Exempel 1: modellen presterar sämre än en slumpmässig markerings modell ![en klassificerings modell som är sämre än en slumpmässig markerings modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Exempel 2: modellen fungerar bättre än en slumpmässig markerings modell ![en klassificerings modell som fungerar bättre](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Vinster-kurva

Ett vinst diagram utvärderar prestanda för en klassificerings modell av varje del av data. Den visas för varje percentil av data uppsättningen, hur mycket bättre du kan vänta på att utföra jämförelsen med en slumpmässig markerings modell.

Använd diagrammet för ackumulerade vinster för att hjälpa dig att välja klassificeringen för att ta del av en procent andel som motsvarar den önskade vinsten från modellen. Den här informationen ger ett annat sätt att titta på resultaten i det medföljande lyft diagrammet.

Exempel 1: en klassificerings modell med minimal vinst ![en klassificerings modell med minimal vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Exempel 2: en klassificerings modell med betydande vinst ![en klassificerings modell med betydande vinst](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Kalibrerings område

För alla klassificerings problem kan du granska kalibrerings linjen för Micro-Average, makro medelvärde och varje klass i en viss förutsägelse modell. 

En kalibrerings ritning används för att Visa förtroendet för en förutsägelse modell. Detta görs genom att visa relationen mellan den förväntade sannolikheten och den faktiska sannolikheten, där sannolikheten representerar sannolikheten att en viss instans tillhör en viss etikett. En väl kalibrerad modell justeras med rad y = x, där det är rimligt att vara säkert i dess förutsägelser. En över-säker modell anpassas efter raden y = 0, där den förväntade sannolikheten finns, men det inte finns någon faktisk sannolikhet.

Exempel 1: en mer välkalibrerad modell ![ mer bra kalibrerad modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Exempel 2: en över-trygg modell ![en över-säker modell](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Regressions resultat

Thee följande mått och diagram är tillgängliga för varje Regressions modell som du skapar med hjälp av de automatiserade maskin inlärnings funktionerna i Azure Machine Learning

+ [Mått](#reg-metrics)
+ [Förutsägs jämfört med sant](#pvt)
+ [Histogram över rester](#histo)


### <a name="reg-metrics"></a>Regressions mått

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

### <a name="pvt"></a>Förutsägs jämfört med sant

Vid förutsägelse jämfört med sant visas relationen mellan ett förutsägande värde och dess korrelerade sanna värde för ett Regressions problem. Det här diagrammet kan användas för att mäta prestanda för en modell som närmare y = x-linjen, de förväntade värdena är, desto bättre precision för en förutsägelse modell.

Efter varje körning kan du se ett förutsägande vs. True-diagram för varje Regressions modell. För att skydda data integriteten är värden diskretiseras och storleken på varje lager plats visas som ett stapeldiagram i den nedre delen av diagram området. Du kan jämföra förutsägelse modellen med det ljusare skuggade avsnittet som visar fel marginaler mot det idealiska värdet för var modellen ska vara.

Exempel 1: en Regressions modell med låg precision i förutsägelser ![en Regressions modell med låg precision i förutsägelserna](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Exempel 2: en Regressions modell med hög noggrannhet i dess förutsägelser [![en Regressions modell med hög noggrannhet i dess förutsägelser](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histogram över rester

En rest representerar en observerad y – det förväntade y. Om du vill visa en marginal på fel med låg förskjutning måste histogrammets histogram vara formad som en klock kurva och centreras runt 0. 

Exempel 1: en Regressions modell med en bias-fel ![SA regression-modell med en förskjutning i sina fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Exempel 2: en Regressions modell med mer jämn fördelning av fel ![en Regressions modell med mer jämna distributioner av fel](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Modellens tolknings-och funktions prioritet

Med funktions prioritet kan du se hur värdefull varje funktion var i konstruktionen av en modell. Den här beräkningen är inaktive rad som standard, eftersom den kan öka körnings tiden betydligt.   Du kan aktivera modell förklaring för alla modeller eller förklara bara den bästa anpassnings modellen.

Du kan granska funktions prioritets poängen för modellen övergripande och per klass i en förutsägelse modell. Du kan se varje funktion hur prioriteten jämförs med varje klass och övergripande.

![Funktions förklarande funktion](./media/how-to-understand-automated-ml/feature-importance.gif)

Mer information om hur du aktiverar tolknings funktioner finns i [så här aktiverar du](how-to-machine-learning-interpretability-automl.md) tolkning i automatiserade ml experiment.

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.
+ Testa antecknings exemplet för [automatisk Machine Learning modell förklaring](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
