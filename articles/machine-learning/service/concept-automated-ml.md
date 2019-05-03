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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cba01b5ad5c2cfea32f1faa6cb67c5ce7ee98fcd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024905"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatisk maskininlärning?

Automatiserad maskininlärning, även kallat AutoML, kan dataforskare, analytiker och utvecklare att skapa ML-modeller med hög skala, effektivitet och produktivitet samtidigt tjänstemål Modellkvalitet. 

Automatiserad ML bygger en uppsättning ML-modeller som automatiskt, smart väljer modeller för utbildning och rekommenderar sedan som passar dig bäst. Traditionella machine learning-modeller är resurskrävande kräver betydande domänkunskap och tid för att producera och jämföra flera modeller. Med automatiserade ML dig kommer att öka den tid det tar för att hämta produktionsklara ML-modeller med bra enkelt och effektivt.

I bakgrunden är dina utbildningsdata tas med ett definierat mål-funktionen och Smart resultatuppsättningen kan upprepas vid via kombinationerna av ML-algoritmer och val av funktioner. Sedan, baserat på utbildning poäng, den bästa anpassade modellen identifieras och rekommenderas för dig. 

Du kan fortfarande har kontroll över dina experiment och insyn i vad som händer. Du kan definiera villkor och experimentera mål baserat på tid, korrekthet eller antalet iterationer, till exempel. Du kan se varje modell som har genererats för experimentet utbildning flödet för varje iteration och de mest inflytelserika funktionerna för en viss modell.

## <a name="how-automated-ml-works"></a>Hur automatiserade ML fungerar

Med hjälp av **Azure Machine Learning-tjänsten**, du kan utforma och köra dina automatiserade experiment i ML utbildning med de här stegen:

1. **Identifiera problemet ML** som måste lösas: klassificering, prognoser eller regression
   
1. **Ange källa och formatet för den märkta träningsdata**: Numpy matriser eller Pandas-dataframe

1. **Konfigurera beräkningsmål för modellträning**, till exempel din [lokala datorn, Azure Machine Learning beräknar, remote virtuella datorer eller Azure Databricks](how-to-set-up-training-targets.md).  Lär dig mer om automatisk utbildning [på en fjärransluten resurs](how-to-auto-train-remote.md).

1. **Konfigurera parametrar för automatisk maskininlärning** som bestämmer hur många iterationer över olika modeller, inställningar för finjustering, avancerade Förbearbeta/funktionalisering och vilka mått som ska titta på när du ska bestämma den bästa modellen.  Du kan konfigurera inställningarna för automatisk träningsexperiment [i Azure-portalen](how-to-create-portal-experiments.md) eller [med SDK](how-to-configure-auto-train.md).

1. **Skicka utbildningen kör.** 


[![Automatiserad Machine learning](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Vid träning skapar Azure Machine Learning-tjänsten ett antal pipelines som prova olika algoritmer och parametrar. Det slutar när den når avsluta-villkor som definierats i experimentet. 

Du kan också granska den loggade kör informationen som innehåller mätvärden som samlats in under körningen. Utbildning kör producerar ett Python-serialiserat objekt (`.pkl` fil) som innehåller modellen och dataförbearbetning.


Medan modellskapandet är automatiserad, du kan också [Lär dig hur viktiga eller relevanta funktioner är](how-to-configure-auto-train.md#explain) för de genererade modellerna. 

> [!VIDEO https://www.youtube.com/embed/l8c-4iDPE0M]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Förbearbeta

I varje automatiserade machine learning-experiment, är dina data förbearbetats med hjälp av standard-metoder och du kan också via Avancerad Förbearbeta.

### <a name="automatic-preprocessing-standard"></a>Automatisk Förbearbeta (standard)
I varje automatiserade machine learning-experiment, är dina data automatiskt skalas eller normalized för att hjälpa algoritmer som gör.  Under modellträning tillämpas en av följande metoder för skalning eller normalisering på varje modell.

|Skala&nbsp;&&nbsp;normalisering| Beskrivning |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisera funktioner genom att ta bort medelvärdet och skala till enhet varians  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Omvandlar funktioner genom att skala varje funktion av den kolumnen minimum och maximum  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |    Skala varje funktion genom att dess högsta absolutvärde |  
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |   Den här Scaler-funktioner genom att deras quantile intervall |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) | Linjär dimensionalitet minskning med rapportanvändare värdet uppdelning av data till projektet till en lägre dimensionell utrymme | 
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |    Den här omvandlare utför linjär dimensionalitet minskning med hjälp av trunkerat rapportanvändare värde uppdelning (SVD). Sätt som strider mot PCA Datacenter inte den här kostnadsuppskattning data innan databehandling rapportanvändare värdet uppdelning. Det innebär att fungera med scipy.sparse matriser effektivt | 
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Varje exempel (dvs, varje rad i matrisen data) med minst ett noll-komponenten skalas oberoende av andra exempel så att dess normen (l1 eller l2) är lika med en | 

### <a name="advanced-preprocessing-optional-featurization"></a>Avancerade Förbearbeta: valfria funktionalisering

Ytterligare avancerade Förbearbeta och funktionalisering är också tillgängliga, till exempel värden uppräkning, kodning och transformeringar som saknas. [Mer information om vilka funktionalisering ingår](how-to-create-portal-experiments.md#preprocess). Aktivera den här inställningen med:
+ Azure-portalen: Att välja den **Preprocess** kryssrutan i den **avancerade inställningar** [med de här stegen](how-to-create-portal-experiments.md). 
+ Python SDK: Ange `"preprocess": True` för den [ `AutoMLConfig` klass](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Ensemble modeller

Du kan träna ensemble modeller med automatiserade machine learning med den [Caruana ensemble val av algoritmen med sorterade Ensemble initieringen](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Ensemble learning förbättrar machine learning resultat och förutsägbar prestanda genom att nyhetsnotiser genomsöks många modeller till skillnad från med enkel modeller. Ensemble iteration visas som den senaste iterationen av din körning.

## <a name="use-with-onnx-in-c-apps"></a>Använd med ONNX i C# appar

Du kan använda automatiserad ML med Azure Machine Learning för att skapa en Python-modell och har den konverteras till ONNX-formatet. ONNX-körningen stöder C#, så du kan använda den modellen som skapats automatiskt i din C# appar utan behovet av dokumentera eller någon av nätverksfördröjningar där lägger till REST-slutpunkter. Testa ett exempel på det här flödet [i den här Jupyter-anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatiserad ML på Microsoft

Automatiserad ML är också tillgängligt i andra Microsoft-lösningar som:
+ I .NET-appar med Visual Studio och Visual Studio Code med [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/what-is-mldotnet)
+ [På HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md), där du skala ut dina automatiserade ML utbildning jobb på Spark i HDInsight-kluster parallellt. 
+ [I Powerbi](https://docs.microsoft.com/power-bi/service-machine-learning-automated)

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du skapar modeller med hjälp av automatisk Machine Learning:

+ Följ den [självstudien: Automatiskt träna en modell för klassificering med Azure automatiserad Machine Learning](tutorial-auto-train-models.md)

+ Konfigurera inställningar för automatisk träningsexperiment: 
   + I Azure portal-gränssnittet, [Följ dessa steg](how-to-create-portal-experiments.md).
   + Med SDK för Python [Följ dessa steg](how-to-configure-auto-train.md).
  
 + Lär dig hur du automatiskt med hjälp av time series-data, träna [Följ dessa steg](how-to-auto-train-forecast.md).

+ Prova att använda [Jupyter Notebook-exempel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
