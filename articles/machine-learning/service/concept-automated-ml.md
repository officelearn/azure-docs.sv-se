---
title: Automatiserad ML-algoritmen och val av justering
titleSuffix: Azure Machine Learning service
description: Lär dig hur Azure Machine Learning-tjänsten kan automatiskt välja en algoritm för dig och skapa en modell från den för att spara tid genom att använda parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 16d100256f9252b478500488c2dc5a01c7e6a0b5
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418060"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatisk maskininlärning?

Automatiserad maskininlärning, är kallas även automatiserade ML processen för automatisering av tidskrävande, iterativ uppgifter av machine learning-modeller. Det gör att dataforskare, analytiker och utvecklare att skapa ML-modeller med hög skala, effektivitet och produktivitet samtidigt tjänstemål Modellkvalitet.

Traditionella machine learning-modeller är resurskrävande, som kräver betydande domänkunskap och tid för att producera och jämföra flera modeller. Tillämpa automatiska ML när du vill att Azure Machine Learning för att träna och finjustera en modell för dig med mål-mått som du anger. Tjänsten går igenom ML-algoritmer tillsammans med val av funktioner, där varje iteration producerar en modell med en poäng för utbildning. Ju högre poäng, desto bättre anses modellen ”efter” dina data.

Med automatiserade machine learning, kan du påskynda den tid det tar för att hämta produktionsklara ML-modeller med bra enkelt och effektivt.

## <a name="when-to-use-automated-ml"></a>När du ska använda automatisk ML

Automatiserad ML demokratiserar sakernas, de machine learning-modell utvecklingsprocessen och ger dess användare, oavsett deras data science-expertis, att identifiera en slutpunkt till slutpunkt-maskininlärningspipeline för alla problem.

Datatekniker, analytiker och utvecklare branscher kan använda automatiska ML som:

+ Implementera maskininlärningslösningar utan omfattande programming kunskap
+ Spara tid och resurser
+ Utnyttja bästa praxis för data science
+ Tillhandahålla flexibel problemlösning

## <a name="how-automated-ml-works"></a>Hur automatiserade ML fungerar

Med hjälp av **Azure Machine Learning-tjänsten**, du kan utforma och köra dina automatiserade experiment i ML utbildning med de här stegen:

1. **Identifiera problemet ML** som måste lösas: klassificering, prognoser eller regression

1. **Ange källa och formatet för den märkta träningsdata**: Numpy matriser eller Pandas-dataframe

1. **Konfigurera beräkningsmål för modellträning**, till exempel din [lokala datorn, Azure Machine Learning beräknar, remote virtuella datorer eller Azure Databricks](how-to-set-up-training-targets.md).  Lär dig mer om automatisk utbildning [på en fjärransluten resurs](how-to-auto-train-remote.md).

1. **Konfigurera parametrar för automatisk maskininlärning** som bestämmer hur många iterationer över olika modeller, inställningar för finjustering, avancerade Förbearbeta/funktionalisering och vilka mått som ska titta på när du ska bestämma den bästa modellen.  Du kan konfigurera inställningarna för automatisk träningsexperiment [i Azure-portalen](how-to-create-portal-experiments.md) eller [med SDK](how-to-configure-auto-train.md).

1. **Skicka utbildningen kör.**

  ![Automatiserad Machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Vid träning skapar Azure Machine Learning-tjänsten ett antal i parallella pipelines som prova olika algoritmer och parametrar. Det slutar när den når avsluta-villkor som definierats i experimentet.

Du kan också granska den loggade kör informationen som innehåller mätvärden som samlats in under körningen. Utbildning kör producerar ett Python-serialiserat objekt (`.pkl` fil) som innehåller modellen och dataförbearbetning.

Medan modellskapandet är automatiserad, du kan också [Lär dig hur viktiga eller relevanta funktioner är](how-to-configure-auto-train.md#explain) för de genererade modellerna.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Förbearbeta

I varje automatiserade machine learning-experiment, är dina data förbearbetats med hjälp av standard-metoder och du kan också via Avancerad Förbearbeta.

### <a name="automatic-preprocessing-standard"></a>Automatisk Förbearbeta (standard)

I varje automatiserade machine learning-experiment, är dina data automatiskt skalas eller normalized för att hjälpa algoritmer som gör.  Under modellträning tillämpas en av följande metoder för skalning eller normalisering på varje modell.

|Skala&nbsp;&&nbsp;normalisering| Beskrivning |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisera funktioner genom att ta bort medelvärdet och skala till enhet varians  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Omvandlar funktioner genom att skala varje funktion av den kolumnen minimum och maximum  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skala varje funktion genom att dess högsta absolutvärde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Den här Scaler-funktioner genom att deras quantile intervall |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Linjär dimensionalitet minskning med rapportanvändare värdet uppdelning av data till projektet till en lägre dimensionell utrymme |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Den här omvandlare utför linjär dimensionalitet minskning med hjälp av trunkerat rapportanvändare värde uppdelning (SVD). Sätt som strider mot PCA Datacenter inte den här kostnadsuppskattning data innan databehandling rapportanvändare värdet uppdelning. Det innebär att fungera med scipy.sparse matriser effektivt |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Varje exempel (dvs, varje rad i matrisen data) med minst ett noll-komponenten är nytt skalade oberoende av andra exempel så att dess normen (l1 eller l2) är lika med en |

### <a name="advanced-preprocessing-optional-featurization"></a>Avancerade Förbearbeta: valfria funktionalisering

Ytterligare avancerade Förbearbeta och funktionalisering är också tillgängliga, till exempel värden uppräkning, kodning och transformeringar som saknas. [Mer information om vilka funktionalisering ingår](how-to-create-portal-experiments.md#preprocess). Aktivera den här inställningen med:

+ Azure-portalen: Att välja den **Preprocess** kryssrutan i den **avancerade inställningar** [med de här stegen](how-to-create-portal-experiments.md).

+ Python SDK: Ange `"preprocess": True` för den [ `AutoMLConfig` klass](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Ensemble modeller

Du kan träna ensemble modeller med automatiserade machine learning med den [Caruana ensemble val av algoritmen med sorterade Ensemble initieringen](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Ensemble learning förbättrar machine learning resultat och förutsägbar prestanda genom att nyhetsnotiser genomsöks många modeller till skillnad från med enkel modeller. Ensemble iteration visas som den senaste iterationen av din körning.

## <a name="training-metric-output"></a>Utbildning mått utdata

Det finns flera sätt att visa utbildning Precision mått för varje körning iteration.

* Använd Jupyter-widgeten.
* Använd den `get_metrics()` funktion på någon `Run` objekt.
* Visa värdena i Azure-portalen i experimentet.

### <a name="classification-metrics"></a>Klassificering mått

Följande mått har sparats i varje körning iteration för en klassificering.

|Mått|Beskrivning|Beräkning|Extra parametrar
--|--|--|--|
AUC_Macro| AUC är området under mottagare fungerar egenskap kurvan. Makrot är det aritmetiska medelvärdet av AUC för varje klass.  | [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Genomsnittlig = ”makrot”|
AUC_Micro| AUC är området under mottagare fungerar egenskap kurvan. Micro beräknas globalt genom att kombinera positiva och falska positiva identifieringar från varje klass| [Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Genomsnittlig = ”micro”|
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

### <a name="regression-and-forecasting-metrics"></a>Regression och prognostisering mått

Följande mått har sparats i varje körning iteration för en regression eller prognosmodellen uppgift.

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
normalized_root_mean_squared_log_error|Normaliserade rot mean kvadraten log fel är roten mean kvadraten logga fel dividerat med en uppsättning data|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dela med mängd data|


## <a name="use-with-onnx-in-c-apps"></a>Använd med ONNX i C# appar

Du kan använda automatiserad ML med Azure Machine Learning för att skapa en Python-modell och har den konverteras till ONNX-formatet. ONNX-körningen stöder C#, så du kan använda den modellen som skapats automatiskt i din C# appar utan behovet av dokumentera eller någon av nätverksfördröjningar där lägger till REST-slutpunkter. Testa ett exempel på det här flödet [i den här Jupyter-anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatiserad ML på Microsoft

Automatiserad ML är också tillgängligt i andra Microsoft-lösningar som:

|Integreringar|Beskrivning
|------------|-----------
|ML.NET|Val av automatisk modell och utbildning i .NET-appar med Visual Studio och Visual Studio Code med ML.NET automatiserad ML (förhandsversion). [Läs mer](https://docs.microsoft.com/dotnet/machine-learning/automl-overview).
|HDIsnight|Skala ut dina automatiserade ML utbildning jobb på Spark i HDInsight-kluster parallellt. [Läs mer](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md).
|PowerBI|Anropa maskininlärningsmodeller direkt i Power BI (förhandsversion). [Läs mer](https://docs.microsoft.com/power-bi/service-machine-learning-automated).

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du skapar modeller med hjälp av automatisk Machine Learning:

+ Följ den [självstudien: Automatiskt träna en modell för klassificering med Azure automatiserad Machine Learning](tutorial-auto-train-models.md)

+ Konfigurera inställningar för automatisk träningsexperiment:
  + I Azure portal-gränssnittet, [Följ dessa steg](how-to-create-portal-experiments.md).
  + Med SDK för Python [Följ dessa steg](how-to-configure-auto-train.md).

+ Lär dig hur du automatiskt med hjälp av time series-data, träna [Följ dessa steg](how-to-auto-train-forecast.md).

+ Prova att använda [Jupyter Notebook-exempel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
