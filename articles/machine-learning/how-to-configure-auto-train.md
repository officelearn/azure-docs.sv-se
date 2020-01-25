---
title: Skapa automatiserade ML-experiment
titleSuffix: Azure Machine Learning
description: Automatiserad machine learning hämtar en algoritm för dig och genererar en modell som är klar för distribution. Läs om de alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: c1ebedcf93d66c01c80f7f40171a7aa27441488d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722160"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurera automatiserade ML-experiment i python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här guiden får du lära dig hur du definierar olika konfigurations inställningar för dina automatiserade maskin inlärnings experiment med [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatiserad machine learning hämtar en algoritm och hyperparametrar för dig och genererar en modell som är klar för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment.

Om du vill visa exempel på ett automatiserat maskin inlärnings experiment, se [Självstudier: träna en klassificerings modell med automatiserad maskin inlärning](tutorial-auto-train-models.md) eller [träna modeller med automatiserad maskin inlärning i molnet](how-to-auto-train-remote.md).

Konfigurationsalternativ är tillgängliga i automatiserade machine learning:

* Välj experiment typ: klassificering, regression eller tids serie prognoser
* Datakällan, format och hämta data
* Välj compute-mål: lokal eller fjärransluten
* Automatiserad machine learning-experiment-inställningar
* Kör en automatiserad machine learning-experiment
* Utforska mått i modellen
* Registrera och distribuera modellen

Om du föredrar en ingen kod upplevelse kan du också [skapa dina automatiserade maskin inlärnings experiment i Azure Machine Learning Studio](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Väljer du typen av experimentet

Innan du börjar experimentet måste bestämma du vilken typ av machine learning-problem som du vill lösa. Automatisk maskin inlärning stöder aktivitets typer för klassificering, regression och Prognosticering. Läs mer om [aktivitets typer](how-to-define-task-type.md).

Automatiserad maskininlärning stöder följande algoritmer under automation och justera processen. Som en användare finns det inget behov av att ange algoritmen.

Klassificering | Regression | Prognosticering för tids serier
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastisk Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastisk Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#regression)
[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linjär SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[Dell Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Dell Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Stöd vektor klassificering (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN-klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN linjär klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linjär modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Linjär modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Fast linjär modellerings regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[ARIMA automatiskt](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Brantaste modellerings regressor för online-gradient](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Genomsnittlig Perceptron-klassificerare](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Linjär SVM-klassificerare](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

Använd `task`-parametern i konstruktorn `AutoMLConfig` för att ange din experiment typ.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Datakällan och format

Automatiserad maskininlärning har stöd för data som finns på din lokala dator eller i molnet, till exempel Azure Blob Storage. Data kan läsas i en Pandas- **DataFrame** eller en **Azure Machine Learning TabularDataset**.  [Läs mer om data uppsättningar](how-to-create-register-datasets.md).

Krav för tränings data:
- Data måste vara i tabell form.
- Värdet som ska förutsäga, mål kolumnen, måste finnas i datan.

Följande kod exempel visar hur du lagrar data i dessa format.

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandas-dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("your-local-file.csv")
    train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
    label = "label-col-name"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Hämta data för att köra experiment på fjärranslutna beräkning

För fjärrkörningar måste inlärnings data vara tillgängliga från fjärrdatorn. Klassen [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) i SDK visar funktioner för att:

* överför enkelt data från statiska filer eller URL-källor till din arbets yta
* gör dina data tillgängliga för att träna skript när de körs på moln beräknings resurser

Se [instruktionen How-to](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) för ett exempel på hur du använder `Dataset`-klassen för att montera data till beräknings målet.

## <a name="train-and-validation-data"></a>Träna och verifiering

Du kan ange separata tåg-och validerings uppsättningar direkt i `AutoMLConfig`-konstruktorn.

### <a name="k-folds-cross-validation"></a>K Vikningar Korsvalidering

Använd `n_cross_validations` inställningen för att ange hur många olika plattformar verifieringar. Utbildning datauppsättningen slumpmässigt delas upp i `n_cross_validations` vikningar av samma storlek. Vid varje mellan verifiering av runda används en av vikningar som för verifiering av modellen som har tränats på återstående vikningar. Den här processen upprepas för `n_cross_validations` Avrundar tills varje vikning används en gång som angetts för verifiering. Genomsnittlig poängen för alla `n_cross_validations` Avrundar rapporteras och motsvarande modellen kommer modellkomponenten utbildning på hela datauppsättningen.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo kors validering (upprepad slumpmässig under sampling)

Använd `validation_size` att ange procentandelen av datauppsättning för träning som ska användas för verifiering och Använd `n_cross_validations` kan du ange antalet mellan verifieringar. Under varje korsvalidering avrunda en delmängd av storleken `validation_size` väljs slumpmässigt för verifiering av modellen som har tränats på kvarvarande data. Slutligen medelvärdet poängsätter för alla `n_cross_validations` Avrundar rapporteras och motsvarande modellen kommer modellkomponenten utbildning på hela datauppsättningen. Monte Carlo stöds inte för tids serie prognoser.

### <a name="custom-validation-dataset"></a>Anpassad validering datauppsättning

Använd anpassad verifierings data uppsättning om slumpmässig delning inte är acceptabelt, vanligt vis Time Series-data eller obalanserade data. Du kan ange verifiering datauppsättningen. Modellen kommer att utvärderas mot verifiering datauppsättningen som anges i stället för slumpmässiga datauppsättning.

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Därefter fastställer där modellen ska tränas. En automatiserad machine learning-träningsexperiment kan köras på följande beräkningsalternativ:
*   Den lokala datorn, till exempel en lokal stationär eller bärbar dator – Allmänt när du har liten datamängd och du fortfarande är i fasen utforskning.
*   En fjärrdator i molnet – [Azure Machine Learning-hanterade Compute](concept-compute-target.md#amlcompute) är en hanterad tjänst som gör möjligheten att träna maskininlärningsmodeller på kluster av virtuella Azure-datorer.

    Se den här [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för exempel på antecknings böcker med lokala och fjärranslutna beräknings mål.

*   Ett Azure Databricks kluster i din Azure-prenumeration. Du kan hitta mer information här – [konfigurera Azure Databricks kluster för automatisk ml](how-to-configure-environment.md#azure-databricks)

    Se den här [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) för exempel på antecknings böcker med Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurera inställningarna för experiment

Det finns flera alternativ som du kan använda för att konfigurera dina automatiserade machine learning-experiment. Dessa parametrar anges av instansiera en `AutoMLConfig` objekt. Se [AutoMLConfig-klassen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) för en fullständig lista över parametrar.

Några exempel är:

1.  Klassificerings experiment med AUC viktat som primärt mått med experiment tids gräns minuter angivet till 30 minuter och 2 kors validerings vikning.

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  Nedan visas ett exempel på ett Regressions experiment som slutar efter 60 minuter med fem validerings kors vikning.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

De tre olika `task` parameter värden (den tredje aktivitets typen är `forecasting`och använder en liknande algoritm som `regression` aktiviteter) för att fastställa listan över modeller som ska användas. Använd parametrarna `whitelist` eller `blacklist` för att ytterligare ändra iterationer med tillgängliga modeller som ska tas med eller undantas. Listan över modeller som stöds finns i SupportedModels- [klassen](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels).

### <a name="primary-metric"></a>Primär mått
Det primära måttet avgör vilket mått som ska användas vid modell träning för optimering. Tillgängliga mått som du kan välja bestäms av den aktivitets typ som du väljer och följande tabell visar giltiga primära mått för varje aktivitets typ.

|Klassificering | Regression | Prognosticering för tids serier
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Lär dig mer om de olika definitionerna av dessa mått i [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Data funktionalisering

I varje automatiserad maskin inlärnings experiment [skalas dina data automatiskt och normaliseras](concept-automated-ml.md#preprocess) för att hjälpa *vissa* algoritmer som är känsliga för funktioner som är i olika skalor.  Du kan dock också aktivera ytterligare funktionalisering, t. ex. saknade värden Imputation, encoding och transformationer. [Läs mer om vad funktionalisering ingår](how-to-create-portal-experiments.md#preprocess).

Ange `"featurization": 'auto'` för [`AutoMLConfig`-klassen](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)om du vill aktivera den här funktionalisering.

> [!NOTE]
> Automatiserad bearbetning av Machine Learning för bearbetning (funktions normalisering, hantering av saknade data, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma för bearbetnings steg som tillämpas på dina indata-data automatiskt.

### <a name="time-series-forecasting"></a>Prognosticering för tids serier
Tids seriens `forecasting` aktivitet kräver ytterligare parametrar i konfigurationsobjektet:

1. `time_column_name`: obligatorisk parameter som definierar namnet på kolumnen i dina utbildnings data som innehåller en giltig tids serie.
1. `max_horizon`: definierar den tids längd som du vill förutsäga baserat på tränings datans periodicitet. Om du till exempel har utbildnings information med dagliga tids kärnor definierar du hur långt ut i dagar du vill att modellen ska tränas.
1. `grain_column_names`: definierar namnet på kolumner som innehåller individuella tids serie data i dina tränings data. Om du till exempel ska prognostisera försäljning av ett visst varumärke med Store definierar du butiks-och märkes kolumner som dina korn kolumner. Separata tids serier och prognoser skapas för varje kornig het/gruppering. 

Exempel på de inställningar som används nedan finns i [exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Konfiguration av Ensemble

Ensemble-modeller är aktiverade som standard och visas som de slutliga körnings iterationerna i en automatiserad Machine Learning-körning. Aktuella Ensemble-metoder som stöds är röstning och stackning. Röstning implementeras som mjuk röstning med hjälp av viktade medelvärden och stack implementeringen använder en två lager implementering, där det första lagret har samma modeller som röstetalet, och den andra skikt modellen används för att hitta den optimala kombinationen av modellerna från det första lagret. Om du använder ONNX-modeller **eller** om du har aktiverat modell förklaringen inaktive ras stackning och endast röstning används.

Det finns flera standard argument som kan anges som `kwargs` i ett `AutoMLConfig`-objekt för att ändra standardvärdet för egenskapen ensemble.

* `stack_meta_learner_type`: meta-eleven är en modell som är utbildad i resultatet av de enskilda heterogena modellerna. Standard-metadata är `LogisticRegression` för klassificerings aktiviteter (eller `LogisticRegressionCV` om kors validering är aktiverat) och `ElasticNet` för Regressions-/prognos uppgifter (eller `ElasticNetCV` om kors validering är aktive rad). Den här parametern kan vara en av följande strängar: `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`eller `LinearRegression`.
* `stack_meta_learner_train_percentage`: anger andelen av inlärnings uppsättningen (när du väljer träna och validerings typ av utbildning) som ska reserveras för att träna meta-eleven. Standardvärdet är `0.2`.
* `stack_meta_learner_kwargs`: valfria parametrar som ska skickas till initieraren för meta-eleven. Dessa parametrar och parameter typer speglar parametrar och parameter typer från motsvarande modell-konstruktor och vidarebefordras till modell-konstruktorn.

Följande kod visar ett exempel på hur du anger anpassade Ensemble-beteenden i ett `AutoMLConfig`-objekt.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Ensemble-utbildning är aktiverat som standard, men det kan inaktive ras med hjälp av `enable_voting_ensemble` och `enable_stack_ensemble` booleska parametrar.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Kör experimentet

För automatisk ML skapar du ett `Experiment`-objekt, som är ett namngivet objekt i en `Workspace` som används för att köra experiment.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Skicka experiment för att köra och generera en modell. Skicka den `AutoMLConfig` till den `submit` metod för att generera modellen.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Beroenden installeras först på en ny dator.  Det kan ta upp till 10 minuter innan utdata visas.
>Ange `show_output` till `True` i utdata som visas på konsolen.

### <a name="exit-criteria"></a>Avslutnings villkor
Det finns några alternativ som du kan definiera för att avsluta experimentet.
1. Inga kriterier: om du inte definierar några avslutnings parametrar fortsätter experimentet tills inga ytterligare framsteg görs på ditt primära mått.
1. Avsluta efter en viss tid: om du använder `experiment_timeout_minutes` i dina inställningar kan du definiera hur länge i minuter som ett experiment ska fortsätta att köras.
1. Avsluta efter att poängen har uppnåtts: om du använder `experiment_exit_score` slutförs experimentet efter att ett primärt mått har nåtts.

### <a name="explore-model-metrics"></a>Utforska mått i modellen

Du kan visa dina utbildnings resultat i en widget eller infogad om du befinner dig i en bärbar dator. Se [spåra och utvärdera modeller](how-to-track-experiments.md#view-run-details) för mer information.

## <a name="understand-automated-ml-models"></a>Förstå automatiserade ML-modeller

Alla modeller som skapas med automatiserad ML innehåller följande steg:
+ Automatiserad funktions teknik (om `"featurization": 'auto'`)
+ Skalning/normalisering och algoritm med värden för en parameter

Vi gör det transparent för att hämta den här informationen från fitted_model utdata från automatisk ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatiserad funktions teknik

Se listan över förbehandling och [automatiserad funktions teknik](concept-automated-ml.md#preprocess) som inträffar när `"featurization": 'auto'`.

Tänk på det här exemplet:
+ Det finns fyra ingångs funktioner: A (numeriskt), B (numeriskt), C (numeriskt), D (DateTime)
+ Numerisk funktion C ignoreras eftersom det är en ID-kolumn med alla unika värden
+ Numeriska funktioner A och B innehåller värden som saknas och anges därför av medelvärdet
+ DateTime-funktionen D är bearbetas till 11 olika funktioner som har utformats

Använd de här två API: erna i det första steget i den monterade modellen för att förstå mer.  Se [den här exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` returnerar en lista med namn på de bevisade funktionerna.

  Användning:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Den här listan innehåller alla bevarade funktions namn.

  >[!Note]
  >Använd ' timeseriestransformer ' för aktiviteten = ' Prognosticering ', Använd annars ' datatransformer ' för uppgiften ' regression ' eller ' klassificering '.

+ API 2: `get_featurization_summary()` returnerar funktionalisering-Sammanfattning för alla inmatade funktioner.

  Användning:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Använd ' timeseriestransformer ' för aktiviteten = ' Prognosticering ', Använd annars ' datatransformer ' för uppgiften ' regression ' eller ' klassificering '.

  Resultat:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   Var:

   |Resultat|Definition|
   |----|--------|
   |RawFeatureName|Inmatad funktion/kolumn namn från den angivna data uppsättningen.|
   |TypeDetected|Identifierad datatyp för indata-funktionen.|
   |Släpper|Anger om inmatad funktion har släppts eller använts.|
   |EngineeringFeatureCount|Antal funktioner som genererats via automatiserad funktion teknik Transforms.|
   |Transformationer|Lista över omvandlingar som används för inmatade funktioner för att generera funktioner som har utvecklats.|
   
### <a name="customize-feature-engineering"></a>Anpassa funktions teknik
Om du vill anpassa funktioner, ange `"feauturization":FeaturizationConfig`.

Anpassning som stöds omfattar:

|Anpassning|Definition|
|--|--|
|Uppdatering av kolumn syfte|Åsidosätt funktions typ för den angivna kolumnen.|
|Transformering av parameter uppdatering |Uppdatera parametrar för den angivna transformeraren. Stöder för närvarande imputerade (medel, de vanligaste & median) och HashOneHotEncoder.|
|Släpp kolumner |Kolumner att släppa från bearbetas.|
|Block transformatorer| Block transformatorer som ska användas för funktionalisering-processen.|

Skapa FeaturizationConfig-objektet med API-anrop:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Skalning/normalisering och algoritm med värden för en valfri parameter:

Om du vill förstå värdena för skalning/normalisering och algoritm/för en pipeline använder du fitted_model. steg. [Läs mer om skalning/normalisering](concept-automated-ml.md#preprocess). Här är exempel på utdata:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Om du vill ha mer information använder du den här hjälp funktionen som visas i [den här exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

Följande exempel på utdata är för en pipeline med en speciell algoritm (LogisticRegression med RobustScalar, i det här fallet).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Sannolikhet för förutsägelse av klass

Modeller som skapas med hjälp av automatiserade ML alla har wrapper-objekt som speglar funktioner från deras ursprungs klass med öppen källkod. De flesta omslutna objekt för klassificerings modell som returneras av automatisk ML implementerar funktionen `predict_proba()`, som accepterar ett mat ris data exempel för matriser eller sparse-data (X-värden) och returnerar en n-dimensionell matris av varje sampel och dess respektive klass sannolikhet.

Förutsatt att du har hämtat den bästa körningen och den monterade modellen med samma anrop från ovan kan du anropa `predict_proba()` direkt från den monterade modellen, ange ett `X_test`-exempel i lämpligt format beroende på modell typen.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Om den underliggande modellen inte stöder `predict_proba()`-funktionen eller om formatet är felaktigt kommer ett modell Klasss undantag att genereras. I referens dokumenten [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) och [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) finns exempel på hur funktionen implementeras för olika modell typer.

<a name="explain"></a>

## <a name="model-interpretability"></a>Modelltolkning

Med modell tolkning kan du förstå varför dina modeller har gjort förutsägelser och de underliggande funktions prioritets värdena. SDK: n innehåller olika paket för att aktivera modell tolknings funktioner, både vid utbildning och drift störningar, för lokala och distribuerade modeller.

Se anvisningar [för kod exempel för hur](how-to-machine-learning-interpretability-automl.md) du aktiverar tolknings funktioner specifikt inom automatiserade maskin inlärnings experiment.

Allmän information om hur modell förklaringar och funktions prioritet kan aktive ras på andra områden i SDK utanför Automatisk maskin inlärning finns i avsnittet [begrepp](how-to-machine-learning-interpretability.md) om tolkning.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du vill distribuera en modell](how-to-deploy-and-where.md).

Lär dig mer om [hur du tränar en Regressions modell med automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar användning av automatiserad maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).
