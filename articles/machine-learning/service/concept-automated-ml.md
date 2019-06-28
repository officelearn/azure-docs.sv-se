---
title: Vad är automatisk ML / automl
titleSuffix: Azure Machine Learning service
description: Lär dig hur Azure Machine Learning-tjänsten kan automatiskt välja en algoritm för dig och skapa en modell från den för att spara tid genom att använda parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: b9fe8ff710cbfe7fbb4a4d8bd351028bb50efcb0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331742"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatisk maskininlärning?

Automatiserad maskininlärning, är kallas även autoML, processen för automatisering av tidskrävande, iterativ uppgifter av machine learning-modeller. Det gör att dataforskare, analytiker och utvecklare att skapa ML-modeller med hög skala, effektivitet och produktivitet samtidigt tjänstemål Modellkvalitet.

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

Du kan också granska den loggade kör informationen som [innehåller mått](how-to-understand-accuracy-metrics.md) samlas in under körningen. Utbildning kör producerar ett Python-serialiserat objekt (`.pkl` fil) som innehåller modellen och dataförbearbetning.

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


## <a name="time-series-forecasting"></a>Tidsserieprognoser
Att skapa prognoser är en del av alla företag, oavsett om det gäller intäkter, inventering, försäljning eller kund begäran. Du kan använda automatiska ML för att kombinera tekniker och metoder och få en rekommenderas, hög kvalitet tidsserie prognos. 

Ett automatiserade time series-experiment behandlas som ett multivarierad regressionsproblem. Senaste time series-värden är ”pivoteras” om du vill bli ytterligare dimensioner för regressor tillsammans med andra förutsägelser. Den här metoden, till skillnad från klassisk time series-metoder, har en fördel med naturligt med flera kontextuella variabler och deras relation till varandra under utbildningen. Automatiserad ML lär sig en enda, men ofta internt förgrenad modell för alla objekt i datauppsättningen och förutsägelse vyer. Mer data kan därför beräkna Modellparametrar och generalisering till överblivna serien blir möjligt. 

Läs mer och se ett exempel på [automatiserad machine learning för tidsserier](how-to-auto-train-forecast.md).

## <a name="ensemble-models"></a>Ensemble modeller

Du kan träna ensemble modeller med automatiserade machine learning med den [Caruana ensemble val av algoritmen med sorterade Ensemble initieringen](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Ensemble learning förbättrar machine learning resultat och förutsägbar prestanda genom att nyhetsnotiser genomsöks många modeller till skillnad från med enkel modeller. Ensemble iteration visas som den senaste iterationen av din körning.

## <a name="use-with-onnx-in-c-apps"></a>Använd med ONNX i C# appar

Du kan använda automatiserad ML med Azure Machine Learning för att skapa en Python-modell och har den konverteras till ONNX-formatet. ONNX-körningen stöder C#, så du kan använda den modellen som skapats automatiskt i din C# appar utan behovet av dokumentera eller någon av nätverksfördröjningar där lägger till REST-slutpunkter. Testa ett exempel på det här flödet [i den här Jupyter-anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatiserad ML på Microsoft

Automatiserad ML är också tillgängligt i andra Microsoft-lösningar som:

|Integreringar|Beskrivning|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Val av automatisk modell och utbildning i .NET-appar med Visual Studio och Visual Studio Code med ML.NET automatiserad ML (förhandsversion).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Skala ut dina automatiserade ML utbildning jobb på Spark i HDInsight-kluster parallellt.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Anropa maskininlärningsmodeller direkt i Power BI (förhandsversion).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Skapa nya machine learning-modeller över dina data i SQL Server 2019 big data-kluster.|

## <a name="next-steps"></a>Nästa steg

Visa exempel och lär dig att skapa modeller som använder automatisk machine learning:

+ Följ den [självstudien: Automatiskt träna en modell för klassificering med Azure automatiserad Machine Learning](tutorial-auto-train-models.md)

+ Konfigurera inställningar för automatisk träningsexperiment:
  + I Azure portal-gränssnittet, [Följ dessa steg](how-to-create-portal-experiments.md).
  + Med SDK för Python [Följ dessa steg](how-to-configure-auto-train.md).

+ Lär dig hur du automatiskt med hjälp av time series-data, träna [Följ dessa steg](how-to-auto-train-forecast.md).

+ Prova att använda [Jupyter Notebook-exempel för automatiserade machine learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
