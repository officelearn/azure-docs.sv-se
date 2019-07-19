---
title: Utbildnings precisions mått i automatiserad ML
titleSuffix: Azure Machine Learning service
description: Lär dig mer om automatiserade precisions mått för maskin inlärning för var och en av dina körningar.
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: dc147fd0252b2b5ec4ce334d6c1c464d9cde8ef5
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297902"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>Utvärdera inlärnings precisionen i automatiserad ML med mått

I den här artikeln får du lära dig mer om de olika mått som är tillgängliga för automatiserade ml-modeller i Azure Machine Learning. 

Det finns flera sätt att Visa inlärnings precisions mått för varje körnings iteration.
* Använda [en Jupyter-widget](how-to-track-experiments.md#view-run-details)
* Använda funktionen på valfritt `Run` objekt [ `get_metrics()` ](how-to-track-experiments.md#query-run-metrics)
* Visa [experiment måtten i Azure Portal](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="prerequisites"></a>Förutsättningar
 
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria versionen eller betalversionen av Azure Machine Learning-tjänsten](https://aka.ms/AMLFree) i dag.
 
* Skapa ett automatiskt maskin inlärnings experiment, antingen med SDK eller i Azure Portal.
 
    * Använd SDK: n för att bygga en [klassificerings modell](how-to-auto-train-remote.md) eller [Regressions modell](tutorial-auto-train-models.md)
    * Använd [Azure Portal](how-to-create-portal-experiments.md) för att skapa en klassificerings-eller Regressions modell genom att överföra relevanta data.

## <a name="classification-metrics"></a>Klassificering mått

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

## <a name="regression-and-forecasting-metrics"></a>Regression och prognostisering mått

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

## <a name="next-steps"></a>Nästa steg

Läs mer om [automatiserad ml](concept-automated-ml.md) i Azure Machine Learning.