---
title: Vad är automatiserad ML/automl
titleSuffix: Azure Machine Learning service
description: Lär dig hur Azure Machine Learning-tjänsten kan välja en algoritm automatiskt och generera en modell från den för att spara tid genom att använda de parametrar och kriterier som du anger för att välja den bästa algoritmen för din modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: 064fd0e2bf503d917c809aa576bbc332b5b18a77
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742364"
---
# <a name="what-is-automated-machine-learning"></a>Vad är automatisk maskininlärning?

Automatisk maskin inlärning, som även kallas autoML, är en process för att automatisera tids krävande, repetitiva uppgifter om utveckling av maskin inlärnings modeller. Det gör det möjligt för data experter, analytiker och utvecklare att bygga ML-modeller med hög skalbarhet, effektivitet och produktivitet samtidigt som modell kvaliteten försämras.

Traditionell miljö utveckling för maskin inlärning är resurs intensiv, vilket kräver betydande domän kunskap och tid för att producera och jämföra dussin tals modeller. Använd automatisk ML när du vill Azure Machine Learning för att träna och finjustera en modell för att använda det målmått du anger. Tjänsten itererar sedan igenom ML-algoritmer med funktions val där varje iteration genererar en modell med en utbildnings poäng. Ju högre poäng, desto bättre blir modellen att "passa" dina data.

Med automatisk maskin inlärning kan du påskynda den tid det tar att få produktions klara ML-modeller med mycket enkelt och effektivt.

## <a name="when-to-use-automated-ml"></a>När du ska använda automatisk ML

Automatiserad ML demokratiserar sakernas utvecklings processen för maskin inlärning och ger användarna möjlighet att, oavsett data vetenskaps expert, identifiera en pipeline för maskin inlärning från slut punkt till slut punkt för alla problem.

Data forskare, analytiker och utvecklare i olika branscher kan använda automatisk ML för att:

+ Implementera maskin inlärnings lösningar utan omfattande programmerings kunskap
+ Spara tid och resurser
+ Utnyttja metod tips för data vetenskap
+ Ge smidig problemlösning

## <a name="how-automated-ml-works"></a>Så här fungerar automatisk ML

Med hjälp av **Azure Machine Learning tjänsten**kan du utforma och köra dina automatiserade ml-experiment med följande steg:

1. **Identifiera det ml-problem** som ska lösas: klassificering, Prognosticering eller regression

1. **Ange källa och format för de märkta tränings data**: Numpy-matriser eller Pandas-dataframe

1. **Konfigurera beräknings målet för modell träning**, till exempel din [lokala dator, Azure Machine Learning beräkningar, fjärranslutna virtuella datorer eller Azure Databricks](how-to-set-up-training-targets.md).  Lär dig mer om automatisk utbildning [på en fjär resurs](how-to-auto-train-remote.md).

1. **Konfigurera de automatiserade Machine Learning-parametrarna** som avgör hur många iterationer över olika modeller, inställningar för funktionalisering, avancerade förbearbetnings-/och vilka mått som ska visas när du bestämmer den bästa modellen.  Du kan konfigurera inställningarna för automatisk inlärnings experiment [i Azure Portal](how-to-create-portal-experiments.md) eller [med SDK](how-to-configure-auto-train.md).

1. **Skicka in utbildnings körningen.**

  ![Automatisk maskin inlärning](./media/how-to-automated-ml/automl-concept-diagram2.png)

Under utbildningen skapar Azure Machine Learning tjänsten ett antal parallella pipelines som testar olika algoritmer och parametrar. Det stoppas när det träffar de slut kriterier som definierats i experimentet.

Du kan också kontrol lera den loggade körnings informationen, som [innehåller mått](how-to-understand-automated-ml.md) som samlats in under körningen. Inlärnings körningen genererar ett serialiserat objekt (`.pkl` fil) som innehåller modellen och data förbearbetningen.

När modell byggnaden automatiseras, kan du också [lära dig hur viktiga eller relevanta funktioner är](how-to-configure-auto-train.md#explain) i de genererade modellerna.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Förbearbeta

I varje automatiserad maskin inlärnings experiment förbehandlas dina data med hjälp av standard metoderna och kan du välja genom avancerad förbearbetning.

### <a name="automatic-preprocessing-standard"></a>Automatisk för bearbetning (standard)

I varje automatiserad maskin inlärnings experiment skalas dina data automatiskt eller normaliseras för att hjälpa algoritmerna att fungera bra.  I modell utbildningen används en av följande skalnings-eller normaliserings tekniker för varje modell.

|Skala&nbsp;normalisering&&nbsp;| Beskrivning |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisera funktioner genom att ta bort medelvärdet och skalan till enhets avvikelse  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformerar funktioner genom att skala varje funktion efter minsta och högsta värde för kolumnen  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skala varje funktion efter dess maximala absoluta värde |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |De här skalnings funktionerna efter deras quantile-intervall |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Linjär linjär reducering med enkel värdes nedbrytning av data för att projicera den till ett lägre dimensionellt utrymme |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Den här transformeringen utför linjär minskning genom trunkering av sammansatta värde diskompositioner (SVD). Till skillnad från PCA centrerar denna uppskattning inte data innan de beräknar ett sammanställnings värde. Det innebär att det fungerar med scipy. sparse-matriser effektivt |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Varje exempel (det vill säga varje rad i data matrisen) med minst en komponent som inte är noll skalas om oberoende av andra prover så att dess norm (L1 eller L2) är lika med ett |

### <a name="advanced-preprocessing-optional-featurization"></a>Avancerad för bearbetning: valfria funktionalisering

Ytterligare avancerade för bearbetnings-och funktionalisering är också tillgängliga, t. ex. saknade värden Imputation, encoding och transformationer. [Läs mer om vad funktionalisering ingår](how-to-create-portal-experiments.md#preprocess). Aktivera den här inställningen med:

+ Azure-portalen: Markera kryss rutan för **process** i **Avancerade inställningar** [med de här stegen](how-to-create-portal-experiments.md).

+ Python SDK: Anger `"preprocess": True` [för klassen`AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Tidsserieprognoser
Att skapa prognoser är en viktig del av alla verksamheter, vare sig det gäller intäkter, inventering, försäljning eller kund efter frågan. Du kan använda automatiserad ML för att kombinera teknik och metoder och få en rekommenderad prognos för tids serier med hög kvalitet.

Ett automatiserat experiment med tids serier behandlas som ett multivarierad Regressions problem. Tidigare tids serie värden är "pivoterade" för att bli ytterligare dimensioner för modellerings regressor tillsammans med andra förutsägelser. Den här metoden, till skillnad från klassiska Time Series-metoder, har en fördel med att använda flera sammanhangsbaserade variabler och deras relation till varandra under utbildningen. Med automatisk ML får du en enda, men ofta ingrenad modell för alla objekt i data uppsättningen och förutsägelserna. Mer data är därför tillgängliga för att uppskatta modell parametrar och generalisering till osett-serien blir möjlig.

Läs mer och se ett exempel på [automatiserad maskin inlärning för tids serie prognoser](how-to-auto-train-forecast.md).

## <a name="ensemble"></a>Ensemble-modeller

Automatisk Machine Learning stöder Ensemble-modeller som är aktiverade som standard. Ensemble-inlärningen förbättrar maskin inlärnings resultatet och förutsäger prestanda genom att kombinera flera modeller i stället för att använda enskilda modeller. Ensemble-iterationerna visas som de slutliga iterationerna av din körning. Automatiserad Machine Learning använder både röstnings-och stack-Ensemble-metoder för att kombinera modeller:

* **Röstning**: förutsäger baserat på viktat medelvärde för förutsebara klass sannolikheter (för klassificerings aktiviteter) eller förutsägande Regressions mål (för Regressions aktiviteter).
* **Stackning**: stackning kombinerar heterogena modeller och tågen en meta-modell som baseras på utdata från de enskilda modellerna. De aktuella standard-meta-modellerna är LogisticRegression för klassificerings uppgifter och ElasticNet för Regressions-/prognos uppgifter.

[Caruana Ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) -algoritmen med sorterad Ensemble-initiering används för att bestämma vilka modeller som ska användas i ensemblen. På hög nivå initierar den här algoritmen ensemblen med upp till fem modeller med de bästa enskilda poängen och kontrollerar att dessa modeller är inom tröskelvärdet på 5% av det bästa resultatet för att undvika en dåligt inledande ensemble. För varje Ensemble-iteration läggs en ny modell till i den befintliga ensemblen och den resulterande poängen beräknas. Om en ny modell förbättrar den befintliga Ensemble-poängen uppdateras ensemblen för att inkludera den nya modellen.

Se [hur du](how-to-configure-auto-train.md#ensemble) ändrar standardinställningar för ensemble i automatiserad maskin inlärning.

## <a name="use-with-onnx-in-c-apps"></a>Använda med ONNX i C# appar

Med Azure Machine Learning kan du använda automatisk ML för att bygga en python-modell och konvertera den till ONNX-format. ONNX runtime stöder C#, så du kan använda modellen som skapats automatiskt i dina C# appar utan att behöva koda om eller någon av nätverks fördröjningarna som rest-slutpunkter introducerar. Prova ett exempel på det här flödet [i den här Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)-anteckningsboken.

## <a name="automated-ml-across-microsoft"></a>Automatiserad ML över Microsoft

Automatisk ML är också tillgängligt i andra Microsoft-lösningar som:

|Integreringar|Beskrivning|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Automatisk modell val och träning i .NET-appar med Visual Studio och Visual Studio Code med ML.NET Automatic ML (för hands version).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Skala ut dina automatiserade ML-utbildnings jobb på spark i HDInsight-kluster parallellt.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Anropa maskin inlärnings modeller direkt i Power BI (för hands version).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Skapa nya maskin inlärnings modeller över dina data i SQL Server 2019 stora data kluster.|

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du skapar modeller med hjälp av automatisk maskin inlärning:

+ [Följ självstudien: Träna en Regressions modell automatiskt med Azures automatiserade Machine Learning](tutorial-auto-train-models.md)

+ Konfigurera inställningarna för automatiskt utbildnings experiment:
  + [Använd de här stegen](how-to-create-portal-experiments.md)i Azure Portal gränssnitt.
  + [Använd de här stegen](how-to-configure-auto-train.md)med python SDK.

+ Lär dig hur du automatiskt tränar använda Time Series-data med hjälp av [de här stegen](how-to-auto-train-forecast.md).

+ Prova [Jupyter Notebook exempel för automatisk maskin inlärning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
