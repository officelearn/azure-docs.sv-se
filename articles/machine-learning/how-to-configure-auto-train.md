---
title: Skapa automatiska ML-experiment
titleSuffix: Azure Machine Learning
description: Automatisk maskin inlärning väljer en algoritm åt dig och genererar en modell som är klar för distribution. Läs om de alternativ som du kan använda för att konfigurera automatiserade maskin inlärnings experiment.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/20/2020
ms.custom: seodec18
ms.openlocfilehash: 5751a7c5eac9386ecd52e172b40aacbef5247dd1
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434622"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurera automatiserade ML-experiment i Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här guiden får du lära dig hur du definierar olika konfigurations inställningar för dina automatiserade maskin inlärnings experiment med [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatisk maskin inlärning väljer en algoritm och sina egenskaper för dig och genererar en modell som är klar för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskin inlärnings experiment.

Om du vill visa exempel på ett automatiserat maskin inlärnings experiment, se [Självstudier: träna en klassificerings modell med automatiserad maskin inlärning](tutorial-auto-train-models.md) eller [träna modeller med automatiserad maskin inlärning i molnet](how-to-auto-train-remote.md).

Konfigurations alternativ som är tillgängliga i Automatisk maskin inlärning:

* Välj experiment typ: klassificering, regression eller tids serie prognoser
* Data källa, format och hämta data
* Välj ditt beräknings mål: lokal eller fjärran sluten
* Automatiserade experiment inställningar för Machine Learning
* Köra ett automatiserat maskin inlärnings experiment
* Utforska modell mått
* Registrera och distribuera modell

Om du föredrar en ingen kod upplevelse kan du också [skapa dina automatiserade maskin inlärnings experiment i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Välj experimenttyp

Innan du påbörjar experimentet bör du bestämma vilken typ av Machine Learning-problem som du löser. Automatisk maskin inlärning stöder aktivitets typer för klassificering, regression och Prognosticering. Läs mer om [aktivitets typer](how-to-define-task-type.md).

Automatisk Machine Learning stöder följande algoritmer under Automation-och fin justerings processen. Som användare behöver du inte ange algoritmen.

> [!NOTE]
> Om du planerar att exportera dina Auto ML-skapade modeller till en [ONNX-modell](concept-onnx.md), kan bara de algoritmer som anges med en * konverteras till ONNX-formatet. Lär dig mer om [att konvertera modeller till ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Observera också att ONNX endast stöder klassificerings-och Regressions uppgifter för tillfället. 

Klassificering | Regression | Prognostisering för tidsserier
|-- |-- |--
[Logistisk regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Ljust GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Ljust GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Ljust GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Tonings förstärkning](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Tonings förstärkning](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Tonings förstärkning](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Besluts träd](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Besluts träd](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Besluts träd](https://scikit-learn.org/stable/modules/tree.html#regression)
[K närmsta grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K närmsta grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K närmsta grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linjär SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Stöd vektor klassificering (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stochastic gradient brantaste (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stochastic gradient brantaste (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremt slumpmässiga träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN-klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN linjär klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linjär modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Linjär modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Fast linjär modellerings regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[ARIMA automatiskt](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stochastic gradient brantaste (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Brantaste modellerings regressor för online-gradient](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[Genomsnittlig Perceptron-klassificerare](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[Linjär SVM-klassificerare](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Använd `task` parametern i `AutoMLConfig` konstruktorn för att ange din experiment typ.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Data källa och format

Automatisk Machine Learning stöder data som finns på ditt lokala skriv bord eller i molnet, till exempel Azure Blob Storage. Data kan läsas i en Pandas- **DataFrame** eller en **Azure Machine Learning TabularDataset**.  [Läs mer om data uppsättningar](how-to-create-register-datasets.md).

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

* Pandas dataframe

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Hämta data för att köra experiment på fjärrberäkning

För fjärrkörningar måste inlärnings data vara tillgängliga från fjärrdatorn. -Klassen [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) i SDK visar funktioner för att:

* överför enkelt data från statiska filer eller URL-källor till din arbets yta
* gör dina data tillgängliga för att träna skript när de körs på moln beräknings resurser

Se [instruktionen How-to](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) för ett exempel på hur du använder `Dataset` klassen för att montera data till beräknings målet.

## <a name="train-and-validation-data"></a>Träna och verifiera data

Du kan ange separata tåg-och validerings uppsättningar direkt i `AutoMLConfig` konstruktorn.

### <a name="k-folds-cross-validation"></a>Kors validering med K-vikning

Använd `n_cross_validations` inställningen för att ange antalet kors valideringar. Träning-datauppsättningen är slumpmässigt uppdelad i `n_cross_validations` vikning av samma storlek. Under varje kors validerings avrundning används en av vik stegen för att verifiera modellen som tränas på återstående vikning. Den här processen upprepas för `n_cross_validations` avrundade tills varje vikning används en gång som validerings uppsättningen. Genomsnitts poängen för alla `n_cross_validations` avrundning rapporteras och motsvarande modell kommer att omtränas på hela inlärnings data uppsättningen.

Lär dig mer om hur autoML använder kors validering för att [förhindra överanpassning av modeller](concept-manage-ml-pitfalls.md#prevent-over-fitting).
### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo kors validering (upprepad slumpmässig under sampling)

Används för `validation_size` att ange procent andelen av den tränings data uppsättning som ska användas för verifiering och används för `n_cross_validations` att ange antalet kors valideringar. Under varje kors validerings avrundning väljs en delmängd av storleken `validation_size` slumpmässigt för att verifiera den modell som har tränats på återstående data. Slutligen rapporteras genomsnitts poängen för alla `n_cross_validations` avrundning, och motsvarande modell kommer att omtränas på hela inlärnings data uppsättningen. Monte Carlo stöds inte för tids serie prognoser.

### <a name="custom-validation-dataset"></a>Anpassad verifierings data uppsättning

Använd anpassad verifierings data uppsättning om slumpmässig delning inte är acceptabelt, vanligt vis Time Series-data eller obalanserade data. Du kan ange din egen validerings data uppsättning. Modellen utvärderas mot den angivna validerings data uppsättningen i stället för slumpmässig data uppsättning.

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Härnäst bestämmer var modellen ska tränas. Ett automatiskt experiment med Machine Learning-utbildning kan köras med följande beräknings alternativ:
* Din lokala dator, till exempel ett lokalt skriv bord eller en bärbar dator – vanligt vis när du har en liten data uppsättning och du fortfarande befinner dig i utforsknings fasen.
* En fjärrdator i molnet – [Azure Machine Learning hanterad beräkning](concept-compute-target.md#amlcompute) är en hanterad tjänst som gör det möjligt att träna maskin inlärnings modeller på kluster av virtuella Azure-datorer. 

  Se den här [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för exempel på antecknings böcker med lokala och fjärranslutna beräknings mål.

* Ett Azure Databricks kluster i din Azure-prenumeration. Du kan hitta mer information här – [konfigurera Azure Databricks kluster för automatisk ml](how-to-configure-environment.md#azure-databricks)

  Se den här [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) för exempel på antecknings böcker med Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurera dina experiment inställningar

Det finns flera alternativ som du kan använda för att konfigurera ditt automatiserade experiment för maskin inlärning. Dessa parametrar anges genom att instansiera ett `AutoMLConfig` objekt. Se [AutoMLConfig-klassen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) för en fullständig lista över parametrar.

Några exempel är:

1. Klassificerings experiment med AUC viktat som primärt mått med experiment tids gräns minuter angivet till 30 minuter och 2 kors validerings vikning.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. Nedan visas ett exempel på ett Regressions experiment som slutar efter 60 minuter med fem validerings kors vikning.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

De tre olika `task` parameter värdena (den tredje aktivitets typen är `forecasting` och använder en liknande algoritm som `regression` aktiviteter) för att fastställa listan över modeller som ska användas. Använd `whitelist` parametrarna eller `blacklist` för att ytterligare ändra iterationer med tillgängliga modeller som ska tas med eller undantas. Listan över modeller som stöds finns i SupportedModels- [klassen](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) för ([klassificering](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [Prognosticering](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)och [regression](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

För att undvika experiment tids gränser kräver automatisk ML-validerings tjänst att den `experiment_timeout_minutes` är minst 15 minuter eller 60 minuter om raden efter kolumn storlek överskrider 10 000 000.

### <a name="primary-metric"></a>Primärt mått
Det primära måttet avgör vilket mått som ska användas vid modell träning för optimering. Tillgängliga mått som du kan välja bestäms av den aktivitets typ som du väljer och följande tabell visar giltiga primära mått för varje aktivitets typ.

|Klassificering | Regression | Prognostisering för tidsserier
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Lär dig mer om de olika definitionerna av dessa mått i [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Data funktionalisering

I varje automatiserad maskin inlärnings experiment [skalas dina data automatiskt och normaliseras](how-to-configure-auto-features.md#) för att hjälpa *vissa* algoritmer som är känsliga för funktioner som är i olika skalor.  Du kan dock också aktivera ytterligare funktionalisering, t. ex. saknade värden Imputation, encoding och transformationer.

När du konfigurerar experiment i `AutoMLConfig` objektet kan du aktivera/inaktivera inställningen `featurization` . I följande tabell visas de accepterade inställningarna för funktionalisering i [klassen AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Funktionalisering-konfiguration | Beskrivning |
| ------------- | ------------- |
|`"featurization": 'auto'`| Anger att [data guardrails och funktionalisering-steg](how-to-configure-auto-features.md#featurization) utförs automatiskt när en del av förbearbetningen. **Standardinställning**|
|`"featurization": 'off'`| Anger att funktionalisering-steget inte ska göras automatiskt.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Anger att det anpassade funktionalisering-steget ska användas. [Lär dig hur du anpassar funktionalisering](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> Automatiserade funktionalisering-steg för Machine Learning (funktions normalisering, hantering av data som saknas, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma funktionalisering-steg som tillämpades under träningen på dina indata automatiskt.

### <a name="time-series-forecasting"></a>Prognostisering för tidsserier
Tids serie `forecasting` aktiviteten kräver ytterligare parametrar i konfigurationsobjektet:

1. `time_column_name`: Obligatorisk parameter som definierar namnet på kolumnen i dina utbildnings data som innehåller en giltig tids serie.
1. `max_horizon`: Anger hur lång tid som ska förutsägas baserat på tränings datans periodicitet. Om du till exempel har utbildnings information med dagliga tids kärnor definierar du hur långt ut i dagar du vill att modellen ska tränas.
1. `grain_column_names`: Definierar namnet på kolumner som innehåller individuella tids serie data i dina utbildnings data. Om du till exempel ska prognostisera försäljning av ett visst varumärke med Store definierar du butiks-och märkes kolumner som dina korn kolumner. Separata tids serier och prognoser skapas för varje kornig het/gruppering. 

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Konfiguration av Ensemble

Ensemble-modeller är aktiverade som standard och visas som de slutliga körnings iterationerna i en automatiserad Machine Learning-körning. Aktuella Ensemble-metoder som stöds är röstning och stackning. Röstning implementeras som mjuk röstning med viktade medelvärden och stack implementeringen använder en två lager implementering, där det första lagret har samma modeller som röstnings ensemblen och den andra skikt modellen används för att hitta den optimala kombinationen av modeller från det första lagret. Om du använder ONNX-modeller **eller** om du har aktiverat modell förklaringen inaktive ras stackning och endast röstning används.

Det finns flera standard argument som kan anges som `kwargs` i ett `AutoMLConfig` objekt för att ändra standard-Ensemble-beteendet.

* `ensemble_download_models_timeout_sec`: Vid genereringen av **VotingEnsemble** och **StackEnsemble** -modellen laddas flera monterade modeller från de tidigare underordnade körningarna ned. Om du stöter på det här felet `AutoMLEnsembleException: Could not find any models for running ensembling` kan du behöva ange mer tid för att modeller ska laddas ned. Standardvärdet är 300 sekunder för nedladdning av dessa modeller parallellt och det finns ingen maximal tids gräns. Konfigurera den här parametern med ett högre värde än 300 sekunder, om mer tid behövs. 

  > [!NOTE]
  >  Om tids gränsen har nåtts och det finns hämtade modeller, fortsätter ensembling med så många modeller som den har laddat ned. Det krävs inte att alla modeller måste laddas ned för att slutföras inom tids gränsen.

Följande parametrar gäller endast för **StackEnsemble** -modeller: 

* `stack_meta_learner_type`: meta-eleven är en modell som är utbildad i resultatet av de enskilda heterogena modellerna. Standard-metadata är `LogisticRegression` för klassificerings aktiviteter (eller `LogisticRegressionCV` om kors validering är aktiverat) och `ElasticNet` för Regressions-/prognos aktiviteter (eller `ElasticNetCV` om kors validering är aktive rad). Den här parametern kan vara en av följande strängar:,,,,, `LogisticRegression` `LogisticRegressionCV` `LightGBMClassifier` `ElasticNet` `ElasticNetCV` `LightGBMRegressor` eller `LinearRegression` .

* `stack_meta_learner_train_percentage`: anger den del av inlärnings uppsättningen (när du väljer tåg-och validerings typ för utbildning) som ska reserveras för att träna meta-eleven. Standardvärdet är `0.2` . 

* `stack_meta_learner_kwargs`: valfria parametrar som ska skickas till initieraren för meta-eleven. Dessa parametrar och parameter typer speglar parametrar och parameter typer från motsvarande modell-konstruktor och vidarebefordras till modell-konstruktorn.

Följande kod visar ett exempel på hur du anger anpassade Ensemble-beteenden i ett `AutoMLConfig` objekt.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
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

Ensemble-utbildning är aktiverat som standard, men det kan inaktive ras med hjälp av `enable_voting_ensemble` `enable_stack_ensemble` parametrarna och.

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

## <a name="run-experiment"></a>Kör experiment

För automatisk ML skapar du ett `Experiment` -objekt, som är ett namngivet objekt i en som `Workspace` används för att köra experiment.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Skicka experimentet för att köra och generera en modell. Skicka `AutoMLConfig` till- `submit` metoden för att generera modellen.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Beroenden installeras först på en ny dator.  Det kan ta upp till 10 minuter innan utdata visas.
>Inställning `show_output` till `True` resultat i utdata som visas i-konsolen.

### <a name="exit-criteria"></a><a name="exit"></a>Avslutnings villkor

Det finns några alternativ som du kan definiera för att avsluta experimentet.
1. Inga kriterier: om du inte definierar några avslutnings parametrar fortsätter experimentet tills inga ytterligare framsteg görs på ditt primära mått.
1. Avsluta efter en viss tid: om `experiment_timeout_minutes` du använder i dina inställningar kan du definiera hur länge i minuter som ett experiment ska fortsätta att köras.
1. Avsluta efter att poängen har nåtts: med hjälp av `experiment_exit_score` slutförs experimentet när en primär mått har nåtts.

### <a name="explore-model-metrics"></a>Utforska modell mått

Du kan visa dina utbildnings resultat i en widget eller infogad om du befinner dig i en bärbar dator. Se [spåra och utvärdera modeller](how-to-track-experiments.md#view-run-details) för mer information.

## <a name="understand-automated-ml-models"></a>Förstå automatiserade ML-modeller

Alla modeller som skapas med automatiserad ML innehåller följande steg:
+ Automatiserad funktions teknik (om `"featurization": 'auto'` )
+ Skalning/normalisering och algoritm med värden för en parameter

Vi gör det transparent för att hämta den här informationen från fitted_model utdata från automatisk ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatiserad funktions teknik

Se listan över förbehandling och [automatiserad funktions teknik]() som inträffar när `"featurization": 'auto'` .

Tänk på det här exemplet:
+ Det finns fyra ingångs funktioner: A (numeriskt), B (numeriskt), C (numeriskt), D (DateTime)
+ Numerisk funktion C ignoreras eftersom det är en ID-kolumn med alla unika värden
+ Numeriska funktioner A och B innehåller värden som saknas och anges därför av medelvärdet
+ DateTime-funktionen D är bearbetas till 11 olika funktioner som har utformats

Använd de här två API: erna i det första steget i den monterade modellen för att förstå mer.  Se [den här exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` returnerar en lista med namn på de funktioner som har utvecklats.

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

   Där:

   |Utdata|Definition|
   |----|--------|
   |RawFeatureName|Inmatad funktion/kolumn namn från den angivna data uppsättningen.|
   |TypeDetected|Identifierad datatyp för indata-funktionen.|
   |Släpper|Anger om inmatad funktion har släppts eller använts.|
   |EngineeringFeatureCount|Antal funktioner som genererats via automatiserad funktion teknik Transforms.|
   |Transformationer|Lista över omvandlingar som används för inmatade funktioner för att generera funktioner som har utvecklats.|
### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Skalning/normalisering och algoritm med värden för en valfri parameter:

Om du vill förstå värdena för skalning/normalisering och algoritm/för en pipeline använder du fitted_model. steg. [Läs mer om skalning/normalisering](). Här är exempel på utdata:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Använd den här hjälp funktionen om du vill ha mer information: 

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


print_model(model)
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

Modeller som skapas med hjälp av automatiserade ML alla har wrapper-objekt som speglar funktioner från deras ursprungs klass med öppen källkod. De flesta omslutna objekt för klassificerings modell som returnerades av automatisk ML implementera `predict_proba()` funktionen, som accepterar ett mat ris data exempel för matriser eller sparse-data (X-värden) och returnerar en n-dimensionell matris av varje sampel och dess respektive klass sannolikhet.

Förutsatt att du har hämtat den bästa körningen och den monterade modellen med samma anrop från ovan kan du anropa `predict_proba()` direkt från den monterade modellen och tillhandahålla ett `X_test` exempel i lämpligt format beroende på modell typen.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Om den underliggande modellen inte stöder `predict_proba()` funktionen eller om formatet är felaktigt, kommer ett modell Klasss-särskilt undantag att genereras. I referens dokumenten [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) och [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) finns exempel på hur funktionen implementeras för olika modell typer.

<a name="explain"></a>

## <a name="model-interpretability"></a>Modelltolkning

Med modell tolkning kan du förstå varför dina modeller har gjort förutsägelser och de underliggande funktions prioritets värdena. SDK: n innehåller olika paket för att aktivera modell tolknings funktioner, både vid utbildning och drift störningar, för lokala och distribuerade modeller.

Se anvisningar [för kod exempel för hur](how-to-machine-learning-interpretability-automl.md) du aktiverar tolknings funktioner specifikt inom automatiserade maskin inlärnings experiment.

Allmän information om hur modell förklaringar och funktions prioritet kan aktive ras på andra områden i SDK utanför Automatisk maskin inlärning finns i avsnittet [begrepp](how-to-machine-learning-interpretability.md) om tolkning.

## <a name="next-steps"></a>Nästa steg

+ Läs mer om [hur och var du distribuerar en modell](how-to-deploy-and-where.md).

+ Lär dig mer om [hur du tränar en Regressions modell med automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar användning av automatiserad maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).
+ Lär dig hur du tränar flera modeller med autoML i [många modeller lösnings acceleratorer](https://aka.ms/many-models).
