---
title: Skapa automatiska ML-experiment
titleSuffix: Azure Machine Learning
description: Automatiserad maskininlärning väljer en algoritm åt dig och genererar en modell som är klar för distribution. Lär dig vilka alternativ du kan använda för att konfigurera automatiserade maskininlärningsexperiment.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 11f52df683b7a85a8ad5163bbede33d786897452
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257274"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurera automatiserade ML-experiment i Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här guiden kan du lära dig hur du definierar olika konfigurationsinställningar för dina automatiserade maskininlärningsexperiment med [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatiserad maskininlärning väljer en algoritm och hyperparametrar åt dig och genererar en modell som är klar för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment.

Information om hur du visar exempel på ett automatiserat maskininlärningsexperiment finns i [Självstudiekurs: Träna en klassificeringsmodell med automatiserad maskininlärning](tutorial-auto-train-models.md) eller [Träna modeller med automatiserad maskininlärning i molnet](how-to-auto-train-remote.md).

Konfigurationsalternativ som är tillgängliga i automatiserad maskininlärning:

* Välj experimenttyp: Klassificering, regression eller tidsserieprognoser
* Datakälla, format och hämta data
* Välj beräkningsmål: lokal eller fjärransluten
* Automatiska inställningar för maskininlärningsexperiment
* Kör ett automatiserat maskininlärningsexperiment
* Utforska modellmått
* Registrera och distribuera modell

Om du föredrar en ingen kodupplevelse kan du också [skapa dina automatiserade maskininlärningsexperiment i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="select-your-experiment-type"></a>Välj experimenttyp

Innan du påbörjar experimentet bör du bestämma vilken typ av maskininlärningsproblem du löser. Automatiserad maskininlärning stöder uppgiftstyper av klassificering, regression och prognostisering. Läs mer om [aktivitetstyper](how-to-define-task-type.md).

Automatiserad maskininlärning stöder följande algoritmer under automatiserings- och justeringsprocessen. Som användare behöver du inte ange algoritmen.

> [!NOTE]
> Om du planerar att exportera dina automatiska ML-skapade modeller till en [ONNX-modell](concept-onnx.md)kan endast de algoritmer som anges med ett * konverteras till ONNX-formatet. Läs mer om [hur du konverterar modeller till ONNX](concept-automated-ml.md#use-with-onnx). <br> <br> Observera också att ONNX endast stöder klassificerings- och regressionsaktiviteter just nu. 

Klassificering | Regression | Prognostisering för tidsserier
|-- |-- |--
[Logistisk regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastiskt nät](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Uppbackning av övertoning](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[Uppbackning av övertoning](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[Uppbackning av övertoning](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslut Träd](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[Beslut Träd](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Beslut Träd](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linjär SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Stöd vektorklassificering (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[Stokastisk lutningsnedstigning (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[Stokastisk lutningsnedstigning (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremt randomiserade träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremt randomiserade träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremt randomiserade träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost (mycket)](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost (mycket)](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN-klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[KLASSIFICERARE FÖR DNN-linjära](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linjär regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[Linjär regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naiva Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[Snabb linjär regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Stokastisk lutningsnedstigning (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[Online Gradient Descent Regressor](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Profeten](https://facebook.github.io/prophet/docs/quick_start.html)
|[Genomsnittlig Perceptron klassificerare](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||PrognosTCN
|[Linjär SVM-klassificerare](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

Använd `task` parametern `AutoMLConfig` i konstruktorn för att ange experimenttypen.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>Datakälla och format

Automatiserad maskininlärning stöder data som finns på ditt lokala skrivbord eller i molnet, till exempel Azure Blob Storage. Data kan läsas in i en **Pandas DataFrame** eller en **Azure Machine Learning TabularDataset**.  [Läs mer om datauppsättningar](how-to-create-register-datasets.md).

Krav för utbildningsdata:
- Data måste vara i tabellform.
- Värdet för att förutsäga, målkolumn, måste finnas i data.

Följande kodexempel visar hur du lagrar data i dessa format.

* Tabelldatauppsättning

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandas dataram

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Hämta data för att köra experiment på fjärrberäkning

För fjärrkörningar måste utbildningsdata vara tillgängliga från fjärrberäkningen. Klassen [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) i SDK exponerar funktioner för:

* enkelt överföra data från statiska filer eller URL-källor till din arbetsyta
* göra dina data tillgängliga för utbildningsskript när du kör på molnberäkningsresurser

Se [hur-till](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) för ett exempel `Dataset` på att använda klassen för att montera data till ditt beräkningsmål.

## <a name="train-and-validation-data"></a>Tåg- och valideringsdata

Du kan ange separata tåg- `AutoMLConfig` och valideringsuppsättningar direkt i konstruktorn.

### <a name="k-folds-cross-validation"></a>K-Folds Kors Validering

Använd `n_cross_validations` inställningen för att ange antalet korsvalideringar. Träningsdatauppsättningen delas upp slumpmässigt i `n_cross_validations` veck av samma storlek. Under varje korsvalideringsrunda kommer en av vecken att användas för validering av den modell som tränas på de återstående vecken. Den här processen `n_cross_validations` upprepas för avrundor tills varje veck används en gång som valideringsuppsättning. De genomsnittliga poängen `n_cross_validations` för alla rundor rapporteras och motsvarande modell kommer att omskolas på hela träningsdatauppsättningen.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo Cross Validering (upprepad slumpmässig sub-sampling)

Används `validation_size` för att ange procentandelen av utbildningsdatauppsättningen som `n_cross_validations` ska användas för validering och använd för att ange antalet korsvalideringar. Under varje korsvalideringsrunda `validation_size` väljs en delmängd av storleken slumpmässigt för validering av modellen som tränas på de återstående data. Slutligen kommer de genomsnittliga `n_cross_validations` poängen för alla rundor att rapporteras, och motsvarande modell kommer att omskolas på hela träningsdatauppsättningen. Monte Carlo stöds inte för tidsserieprognoser.

### <a name="custom-validation-dataset"></a>Anpassad valideringsdatauppsättning

Använd anpassad valideringsdatauppsättning om slumpmässig delning inte är acceptabel, vanligtvis tidsseriedata eller obalanserade data. Du kan ange din egen valideringsdatauppsättning. Modellen utvärderas mot den valideringsdatauppsättning som anges i stället för slumpmässig datauppsättning.

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Nästa bestämma var modellen kommer att utbildas. Ett automatiserat maskininlärningsexperiment kan köras på följande beräkningsalternativ:
* Din lokala dator som en lokal stationär eller bärbar dator - I allmänhet när du har liten datauppsättning och du är fortfarande i prospekteringsstadiet.
* En fjärrdator i molnet – [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute) är en hanterad tjänst som gör det möjligt att träna maskininlärningsmodeller i kluster av virtuella Azure-datorer.

  Se den här [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för exempel på anteckningsböcker med lokala och fjärranslutna beräkningsmål.

* Ett Azure Databricks-kluster i din Azure-prenumeration. Du hittar mer information här - [Setup Azure Databricks kluster för Automated ML](how-to-configure-environment.md#azure-databricks)

  Se den här [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) för exempel på anteckningsböcker med Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurera experimentinställningarna

Det finns flera alternativ som du kan använda för att konfigurera ditt automatiserade maskininlärningsexperiment. Dessa parametrar ställs in genom `AutoMLConfig` att instansiera ett objekt. Se [klassen AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) för en fullständig lista över parametrar.

Några exempel är:

1. Klassificeringsexperiment med AUC viktat som primärt mått med experimenttidsminuter inställda på 30 minuter och 2 korsvalideringsveckor.

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
2. Nedan är ett exempel på en regressions experiment som ska avslutas efter 60 minuter med fem validering korsveck.

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

De tre `task` olika parametervärdena (den `forecasting`tredje aktivitetstypen `regression` är och använder en liknande algoritmpool som uppgifter) bestämmer listan över modeller som ska tillämpas. Använd `whitelist` parametrarna eller för `blacklist` att ytterligare ändra iterationer med tillgängliga modeller för att inkludera eller utesluta. Listan över modeller som stöds finns i [klassen SupportedModels](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) för ([Klassificering,](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification) [Prognostering](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)och [Regression](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)).

För att undvika tidsgränsen för experiment kräver Automated `experiment_timeout_minutes` ML:s valideringstjänst att den ställs in på minst 15 minuter, eller 60 minuter om rad för kolumnstorlek överstiger 10 miljoner.

### <a name="primary-metric"></a>Primärt mått
Det primära måttet bestämmer det mått som ska användas under modellutbildning för optimering. Vilka mått som är tillgängliga som du kan välja bestäms av den aktivitetstyp du väljer, och i följande tabell visas giltiga primära mått för varje aktivitetstyp.

|Klassificering | Regression | Prognostisering för tidsserier
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

Lär dig mer om de specifika definitionerna av dessa mått i [Förstå automatiserade maskininlärningsresultat](how-to-understand-automated-ml.md).

### <a name="data-featurization"></a>Data med

I varje automatiserat maskininlärningsexperiment [skalas och normaliseras](concept-automated-ml.md#preprocess) dina data automatiskt för att hjälpa *vissa* algoritmer som är känsliga för funktioner som finns i olika skalor.  Du kan dock också aktivera ytterligare medsättning, till exempel saknade värden imputering, kodning och transformeringar. [Läs mer om vad medsättning ingår](how-to-use-automated-ml-for-ml-models.md#featurization).

När du konfigurerar experimenten kan du `featurization`aktivera den avancerade inställningen . I följande tabell visas godkända inställningar för featurization i [klassen AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

|Konfiguration för featurization | Beskrivning |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Anger att anpassat mediseringssteg ska användas. [Läs om hur du anpassar featurization](how-to-configure-auto-train.md#customize-feature-engineering).|
|`"featurization": 'off'`| Anger att mediseringssteget inte ska göras automatiskt.|
|`"featurization": 'auto'`| Anger att data [skyddsräcken och mediseringssteg](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) utförs automatiskt som en del av förbearbetningen.|

> [!NOTE]
> Automatiserad maskininlärning med steg (funktionen normalisering, hantering av saknade data, konvertera text till numeriska, etc.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma medsättningssteg som tillämpas under träningen automatiskt på indata.

### <a name="time-series-forecasting"></a>Prognostisering för tidsserier
Tidsserieuppgiften `forecasting` kräver ytterligare parametrar i konfigurationsobjektet:

1. `time_column_name`: Parameter som krävs som definierar namnet på kolumnen i träningsdata som innehåller en giltig tidsserie.
1. `max_horizon`: Definierar hur lång tid du vill förutsäga ut baserat på träningsdatas periodicitet. Om du till exempel har träningsdata med dagliga tidskorn definierar du hur långt ut i dagar du vill att modellen ska träna för.
1. `grain_column_names`: Definierar namnet på kolumner som innehåller enskilda tidsseriedata i dina träningsdata. Om du till exempel räknar försäljningen av ett visst varumärke efter butik definierar du butiks- och märkeskolumner som kornkolumner. Separata tidsserier och prognoser skapas för varje korn/gruppering. 

Exempel på de inställningar som används nedan finns i [exempelanteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

### <a name="ensemble-configuration"></a><a name="ensemble"></a>Ensemble konfiguration

Ensemblemodeller är aktiverade som standard och visas som de slutliga körningsiterationerna i en automatiserad maskininlärningskörning. För närvarande stöds ensemble metoder är röstning och stapling. Röstning genomförs som mjuk-röstning med viktade medelvärden, och stapling genomförandet använder en två lager genomförande, där det första lagret har samma modeller som röstning ensemble, och det andra lagret modellen används för att hitta den optimala kombinationen av modellerna från det första lagret. Om du använder ONNX-modeller, **eller** har modellförklarbarhet aktiverat, inaktiveras stapling och endast röstning kommer att användas.

Det finns flera standardargument `kwargs` som `AutoMLConfig` kan anges som i ett objekt för att ändra standardstackenensemblens beteende.

* `stack_meta_learner_type`: meta-eleven är en modell som tränas på utgången av de enskilda heterogena modellerna. Standardmeta-elever `LogisticRegression` är för klassificeringsuppgifter (eller `LogisticRegressionCV` om korsvalidering är aktiverat) och `ElasticNet` `ElasticNetCV` för regressions-/prognosaktiviteter (eller om korsvalidering är aktiverat). Den här parametern kan vara `LogisticRegression`en `LogisticRegressionCV` `LightGBMClassifier`av `ElasticNet` `ElasticNetCV`följande `LightGBMRegressor`strängar: , , , , , eller `LinearRegression`.
* `stack_meta_learner_train_percentage`: anger den andel av utbildningen (vid val av utbildning och valideringstyp av utbildning) som ska reserveras för utbildning av metastuderande. Standardvärdet `0.2`är .
* `stack_meta_learner_kwargs`: Valfria parametrar för att gå vidare till meta elevens initializer. Dessa parametrar och parametertyper speglar parametrarna och parametertyperna från motsvarande modellkonstruktor och vidarebefordras till modellkonstruktorn.

Följande kod visar ett exempel på hur `AutoMLConfig` anpassad ensemble ska vara i ett objekt.

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

Ensemble utbildning är aktiverad som standard, men `enable_voting_ensemble` det `enable_stack_ensemble` kan inaktiveras med hjälp av och boolean parametrar.

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

För automatiserad ML `Experiment` skapar du ett objekt som `Workspace` är ett namngivet objekt i ett som används för att köra experiment.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

Skicka experimentet för att köra och generera en modell. Skicka `AutoMLConfig` till `submit` metoden för att generera modellen.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Beroenden installeras först på en ny dator.  Det kan ta upp till 10 minuter innan utdata visas.
>Inställningen `show_output` `True` till resultat i produktionen visas på konsolen.

### <a name="exit-criteria"></a>Avsluta villkor
Det finns några alternativ som du kan definiera för att avsluta experimentet.
1. Inga villkor: Om du inte definierar några avslutningsparametrar fortsätter experimentet tills inga ytterligare framsteg görs på ditt primära mått.
1. Avsluta efter en längre `experiment_timeout_minutes` tid: Med hjälp av inställningarna kan du definiera hur länge i minuter som ett experiment ska fortsätta att köras.
1. Avsluta efter att en poäng `experiment_exit_score` har uppnåtts: När du använder slutförs experimentet efter att en primär måttpoäng har uppnåtts.

### <a name="explore-model-metrics"></a>Utforska modellmått

Du kan visa dina träningsresultat i en widget eller infogad om du är i en anteckningsbok. Se [Spåra och utvärdera modeller](how-to-track-experiments.md#view-run-details) för mer information.

## <a name="understand-automated-ml-models"></a>Förstå automatiserade ML-modeller

Alla modeller som produceras med hjälp av automatiserade ML innehåller följande steg:
+ Automatiserad funktionsteknik `"featurization": 'auto'`(om)
+ Skalning/normalisering och algoritm med hyperparametervärden

Vi gör det öppet för att få denna information från fitted_model produktionen från automatiserade ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatiserad funktionsteknik

Se listan över förbearbetning och [automatiserad funktionsteknik](concept-automated-ml.md#preprocess) som händer när `"featurization": 'auto'`.

Tänk på det här exemplet:
+ Det finns fyra inmatningsfunktioner: A (numerisk), B (numerisk), C (numerisk), D (DateTime)
+ Numerisk funktion C tas bort eftersom det är en ID-kolumn med alla unika värden
+ Numeriska funktioner A och B har saknade värden och därför tillskrivs med medelvärdet
+ DateTime-funktionen D är presenterad i 11 olika konstruerade funktioner

Använd dessa 2 API:er på det första steget i den monterade modellen för att förstå mer.  Se [den här exempelanteckningsboken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` Returnerar en lista över konstruerade funktionsnamn.

  Användning:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Den här listan innehåller alla konstruerade funktionsnamn.

  >[!Note]
  >Använd "timeseriestransformer" för task='forecasting', annars använd "datatransformer" för "regression" eller "klassificering" uppgift.

+ API 2: `get_featurization_summary()` returnerar sammanfattning av medfunktioner för alla indatafunktioner.

  Användning:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Använd "timeseriestransformer" för task='forecasting', annars använd "datatransformer" för "regression" eller "klassificering" uppgift.

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

   |Resultat|Definition|
   |----|--------|
   |RawFeatureName|Indatafunktions-/kolumnnamn från den angivna datauppsättningen.|
   |TypeDetected|Identifierade datatyp för indatafunktionen.|
   |Tappade|Anger om indatafunktionen har tappats eller använts.|
   |EngineeringFeatureCount|Antal funktioner som genereras genom automatiserade funktionstekniktransformeringar.|
   |Transformationer|Lista över omvandlingar som tillämpas på indatafunktioner för att generera konstruerade funktioner.|
   
### <a name="customize-feature-engineering"></a>Anpassa funktionsteknik
Om du vill `"featurization": FeaturizationConfig`anpassa funktionsteknik anger du .

Anpassning som stöds omfattar:

|Anpassning|Definition|
|--|--|
|Uppdatering av kolumnsyfte|Åsidosättningsfunktionstyp för den angivna kolumnen.|
|Uppdatering av transformatorparameter |Uppdatera parametrar för den angivna transformatorn. Stöder för närvarande Imputer (medelvärde, vanligaste & median) och HashOneHotEncoder.|
|Släpp kolumner |Kolumner att släppa från att vara featurized.|
|Blockera transformatorer| Blockera transformatorer som ska användas på featurization-processen.|

Skapa Objektet FeaturizationConfig med API-anrop:
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>Skalning/normalisering och algoritm med hyperparametervärden:

Om du vill förstå skalnings-/normaliserings- och algoritm-/hyperparametervärdena för en pipeline använder du fitted_model.steps. [Läs mer om skalning/normalisering](concept-automated-ml.md#preprocess). Här är exempel på utdata:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Om du vill ha mer information använder du den här hjälpfunktionen som visas i [den här exempelanteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

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

Följande exempelutdata är för en pipeline med hjälp av en specifik algoritm (LogisticRegression med RobustScalar, i det här fallet).

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

### <a name="predict-class-probability"></a>Förutsäga klasssannolikhet

Modeller som produceras med hjälp av automatiserade ML har alla omslagsobjekt som speglar funktionalitet från deras ursprungsklass med öppen källkod. De flesta klassificeringsmodellomslagsobjekt som `predict_proba()` returneras av automatiserade ML implementerar funktionen, som accepterar ett matrisliknande eller glest matrisdataprov av dina funktioner (X-värden) och returnerar en n-dimensionell matris för varje prov och dess respektive klasssannolikhet.

Förutsatt att du har hämtat den bästa körningen och monterad `predict_proba()` modell med samma samtal ovanifrån, kan du ringa direkt från den monterade modellen, leverera ett `X_test` prov i lämpligt format beroende på modelltyp.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Om den underliggande modellen `predict_proba()` inte stöder funktionen eller om formatet är felaktigt genereras ett modellklassspecifikt undantag. Se [Referensdokumenten RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) och [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) för exempel på hur den här funktionen implementeras för olika modelltyper.

<a name="explain"></a>

## <a name="model-interpretability"></a>Modelltolkning

Med modelltolkbarhet kan du förstå varför dina modeller gjorde förutsägelser och de underliggande funktionsviktvärdena. SDK innehåller olika paket för att aktivera funktioner för modelltolkbarhet, både vid utbildning och inferenstid, för lokala och distribuerade modeller.

Se [kodexemplen](how-to-machine-learning-interpretability-automl.md) för hur du aktiverar tolkningsfunktioner specifikt i automatiserade maskininlärningsexperiment.

Allmän information om hur modellförklaringar och funktionsvikt kan aktiveras i andra delar av SDK utanför automatiserad maskininlärning finns i [konceptartikeln](how-to-machine-learning-interpretability.md) om tolkning.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du kan distribuera en modell](how-to-deploy-and-where.md).

Läs mer om [hur du tränar en regressionsmodell med automatiserad maskininlärning](tutorial-auto-train-models.md) eller hur du tränar med [automatiserad maskininlärning på en fjärrresurs](how-to-auto-train-remote.md).
