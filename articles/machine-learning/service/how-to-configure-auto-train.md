---
title: Skapa automatiserade ML-experiment
titleSuffix: Azure Machine Learning service
description: Automatiserad machine learning hämtar en algoritm för dig och genererar en modell som är klar för distribution. Läs om de alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 5dee966f8664bc14d81004e625ad9632066ffcb2
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742310"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurera automatiserade ML-experiment i python

I den här guiden får du lära dig hur du definierar olika konfigurations inställningar för dina automatiserade maskin inlärnings experiment med [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatiserad machine learning hämtar en algoritm och hyperparametrar för dig och genererar en modell som är klar för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment.

Om du vill visa exempel på en automatiserad maskin inlärnings experiment, se [Självstudier: Träna en klassificerings modell med automatiserad](tutorial-auto-train-models.md) maskin inlärning eller [träna modeller med automatiserad maskin inlärning i molnet](how-to-auto-train-remote.md).

Konfigurationsalternativ är tillgängliga i automatiserade machine learning:

* Välj experiment typ: Prognoser för klassificering, regression eller tids serie
* Datakällan, format och hämta data
* Välj compute-mål: lokal eller fjärransluten
* Automatiserad machine learning-experiment-inställningar
* Kör en automatiserad machine learning-experiment
* Utforska mått i modellen
* Registrera och distribuera modellen

Om du föredrar en ingen kod upplevelse kan du också [skapa dina automatiserade maskin inlärnings experiment i Azure Portal](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Väljer du typen av experimentet

Innan du börjar experimentet måste bestämma du vilken typ av machine learning-problem som du vill lösa. Automatiserad maskininlärning stöder uppgift typer av klassificering, regression och prognostisering.

Automatiserad maskininlärning stöder följande algoritmer under automation och justera processen. Som en användare finns det inget behov av att ange algoritmen.

Klassificering | Regression | Prognosticering för tids serier
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastisk Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastisk Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Ljus GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslutsträd](https://scikit-learn.org/stable/modules/tree.html#regression)
[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K närmaste grannar](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linjär SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[Dell Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Dell Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-Support Vector klassificering (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Slumpmässig skog](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremt slumpmässig träd](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN-klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN linjär klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linjär modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Linjär modellerings regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

`task` Använd parametern `AutoMLConfig` i konstruktorn för att ange din experiment typ.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>Datakällan och format
Automatiserad maskininlärning har stöd för data som finns på din lokala dator eller i molnet, till exempel Azure Blob Storage. Data kan läsas in i scikit-Läs stöds dataformat. Du kan läsa data till:
* Numpy matriser X (funktioner) och y (målvariabel eller så kallade etikett)
* Pandas-dataframe

Exempel:

*   Numpy matriser

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Pandas-dataframe

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Hämta data för att köra experiment på fjärranslutna beräkning

För fjärrkörningar måste du göra data tillgängliga från fjärrdatorn. Detta kan göras genom att ladda upp data till data lagret.

Här är ett exempel på hur `datastore`du använder:

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>Definiera dprep-referenser

Definiera X-och y som dprep-referens, som skickas till det automatiserade `AutoMLConfig` Machine Learning-objektet, ungefär så här:

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>Träna och verifiering

Du kan ange separat tåg-och validerings uppsättning direkt `AutoMLConfig` i-metoden.

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

Se [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) för till exempel antecknings böcker med lokala och fjärranslutna beräknings mål.

*   Ett Azure Databricks kluster i din Azure-prenumeration. Du kan hitta mer information här – [konfigurera Azure Databricks kluster för automatisk ml](how-to-configure-environment.md#azure-databricks)

Se [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) för till exempel antecknings böcker med Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurera inställningarna för experiment

Det finns flera alternativ som du kan använda för att konfigurera dina automatiserade machine learning-experiment. Dessa parametrar anges av instansiera en `AutoMLConfig` objekt. Se [AutoMLConfig-klassen](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) för en fullständig lista över parametrar.

Några exempel är:

1.  Klassificering experiment med AUC viktad som primär mått med en maximal tid på 12 000 sekunder per iteration med experimentet att avsluta när du har 50 iterationer och 2 mellan verifiering vikningar.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  Nedan visas ett exempel på en regression experiment uppsättning avslutas efter 100 iterationer, där varje iteration långvarigt upp till 600 sekunder med 5 verifiering mellan vikningar.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

De tre olika `task` parametervärdena bestämmer listan över algoritmer som ska användas.  Använd parametrarna `blacklist` eller för att ytterligare ändra iterationer med tillgängliga algoritmer som ska tas med eller undantas. `whitelist` Listan över modeller som stöds finns i SupportedAlgorithms- [klassen](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Primär mått
Primärt mått. som du ser i exemplen ovan bestäms måttet som ska användas vid modell träning för optimering. Det primära måttet du kan välja avgörs av den aktivitets typ som du väljer. Nedan visas en lista över tillgängliga mått.

|Klassificering | Regression | Prognosticering för tids serier
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Data förbehandling & funktionalisering

I varje automatiserad maskin inlärnings experiment skalas dina data [automatiskt och normaliseras](concept-automated-ml.md#preprocess) för att hjälpa algoritmerna att fungera bra.  Du kan dock också aktivera ytterligare för bearbetning/funktionalisering, till exempel saknade värden Imputation, encoding och transformationer. [Läs mer om vad funktionalisering ingår](how-to-create-portal-experiments.md#preprocess).

Om du vill aktivera den här `"preprocess": True` funktionalisering anger du [ `AutoMLConfig` för klassen](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Prognosticering för tids serier
För aktivitets typen tids serie prognos anger du ytterligare parametrar för att definiera.
1. time_column_name – det här är en obligatorisk parameter som definierar namnet på kolumnen i dina utbildnings data som innehåller datum/tid-serien.
1. max_horizon – detta definierar den tids längd som du vill förutsäga baserat på tränings informationens periodicitet. Om du till exempel har utbildnings information med dagliga tids kärnor definierar du hur långt ut i dagar du vill att modellen ska tränas.
1. grain_column_names – detta definierar namnet på kolumner som innehåller individuella tids serie data i dina utbildnings data. Om du till exempel ska prognostisera försäljning av ett visst varumärke med Store definierar du butiks-och märkes kolumner som dina korn kolumner.

Se exemplet på de här inställningarna som används nedan. exempel på bärbara datorer finns [här](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>Konfiguration av Ensemble

Ensemble-modeller är aktiverade som standard och visas som de slutliga körnings iterationerna i en automatiserad Machine Learning-körning. Aktuella Ensemble-metoder som stöds är röstning och stackning. Röstning implementeras som mjuk röstning med hjälp av viktade medelvärden och stack implementeringen använder en 2-nivå implementering, där det första lagret har samma modeller som röstnings ensemblen och den andra skikt modellen används för att hitta den optimala kombinationen av modeller från det första lagret. Om du använder ONNX-modeller **eller** om du har aktiverat modell förklaringen inaktive ras stackning och endast röstning används.

Det finns flera standard argument som kan anges som `kwargs` i ett `AutoMLConfig` objekt för att ändra standardformat för stack-egenskapen.

* `stack_meta_learner_type`: meta-eleven är en modell som är utbildad i resultatet av de enskilda heterogena-modellerna. Standard `LogisticRegression` -metadata är för klassificerings aktiviteter (eller `LogisticRegressionCV` om kors validering är aktiverat) och `ElasticNet` för Regressions-/prognos aktiviteter (eller `ElasticNetCV` om kors validering är aktive rad). Den här parametern kan vara en av följande strängar: `LogisticRegression` `LightGBMClassifier`, `LogisticRegressionCV` `LightGBMRegressor` `ElasticNet` `ElasticNetCV`,,,, eller `LinearRegression`.
* `stack_meta_learner_train_percentage`: anger den del av inlärnings uppsättningen (när du väljer tåg-och validerings typ för utbildning) som ska reserveras för att träna meta-eleven. Standardvärdet `0.2`är.
* `stack_meta_learner_kwargs`: valfria parametrar som ska skickas till initieraren för meta-eleven. Dessa parametrar och parameter typer speglar de från motsvarande modell-konstruktor och vidarebefordras till modell-konstruktorn.

Följande kod visar ett exempel på hur du anger anpassade Ensemble-beteenden i ett `AutoMLConfig` objekt.

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
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

Ensemble-utbildning är aktiverat som standard, men det kan inaktive ras `enable_voting_ensemble` med `enable_stack_ensemble` hjälp av parametrarna och.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>Kör experimentet

För automatisk ml skapar du ett `Experiment` -objekt, som är ett namngivet objekt `Workspace` som används för att köra experiment.

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
Det finns några alternativ som du kan definiera för att slutföra experimentet.
1. Inga villkor – om du inte definierar några avslutnings parametrar fortsätter experimentet tills inga ytterligare framsteg görs på ditt primära mått.
1. Antal iterationer – du definierar antalet iterationer för experimentet som ska köras. Du kan välja Lägg till iteration_timeout_minutes för att definiera en tids gräns i minuter per iteration.
1. Avsluta efter en tids period – med experiment_timeout_minutes i dina inställningar kan du ange hur lång tid i minuter som ett experiment ska fortsätta köras.
1. Avsluta efter att poängen har nåtts – när du använder experiment_exit_score kan du välja att slutföra experimentet när en poäng baserat på ditt primära mått har nåtts.

### <a name="explore-model-metrics"></a>Utforska mått i modellen

Du kan visa dina utbildnings resultat i en widget eller infogad om du befinner dig i en bärbar dator. Se [spåra och utvärdera modeller](how-to-track-experiments.md#view-run-details) för mer information.

## <a name="understand-automated-ml-models"></a>Förstå automatiserade ML-modeller

Alla modeller som skapas med automatiserad ML innehåller följande steg:
+ Automatiserad funktions teknik (om preprocess = sant)
+ Skalning/normalisering och algoritm med värden för ommätare

Vi gör det transparent för att hämta den här informationen från fitted_model-utdata från automatisk ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatiserad funktions teknik

Se listan över förbehandling och automatiserad [funktions teknik](concept-automated-ml.md#preprocess) som inträffar när preprocess = True.

Tänk på det här exemplet:
+ Det finns 4 ingångs funktioner: A (numeriskt), B (numeriskt), C (numeriskt), D (DateTime)
+ Numerisk funktion C ignoreras eftersom det är en ID-kolumn med alla unika värden
+ Numeriska funktioner A och B innehåller värden som saknas och tas därför med i genomsnitt
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

  Utdata:
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

   |Output|Definition|
   |----|--------|
   |RawFeatureName|Inmatad funktion/kolumn namn från den angivna data uppsättningen.|
   |TypeDetected|Identifierad datatyp för indata-funktionen.|
   |Släpper|Anger om inmatad funktion har släppts eller använts.|
   |EngineeringFeatureCount|Antal funktioner som genererats via automatiserad funktion teknik Transforms.|
   |Transformationer|Lista över omvandlingar som används för inmatade funktioner för att generera funktioner som har utvecklats.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Skalning/normalisering och algoritm med värden för ommätare:

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

Följande är exempel på utdata för en pipeline med en speciell algoritm (LogisticRegression med RobustScalar, i det här fallet).

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

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>Förklara modellen (tolkning)

Automatiserad maskininlärning kan du förstå vikten av funktionen.  Under processen utbildning får du global funktionen betydelse för modellen.  Du kan också få klassnivå funktionen vikten för scenarier med klassificering.  Du måste ange en datauppsättning (X_valid) för att få funktionen vikten för verifiering.

Det finns två sätt att skapa funktionen prioritet.

*   När ett experiment är klar kan du använda `explain_model` metoden på eventuella iterationer.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   Om du vill visa funktionen betydelse för alla iterationer, ange `model_explainability` flaggan till `True` i AutoMLConfig.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    När du har gjort, kan du använda retrieve_model_explanation metoden för att hämta funktionen betydelse för en viss iteration.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

Du kan visualisera funktionen vikten diagrammet på din arbetsyta i Azure-portalen. Visa URL: en med hjälp av objektet kör:

```
automl_run.get_portal_url()
```

Du kan visualisera funktionen vikten diagrammet på din arbetsyta i Azure-portalen. Diagrammet visas också när du `RunDetails` använder Jupyter- [widgeten](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) i en bärbar dator. Om du vill veta mer om diagrammen kan du läsa mer om att [förstå automatiserade maskin inlärnings resultat](how-to-understand-automated-ml.md).

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![funktionen vikten graph](./media/how-to-configure-auto-train/feature-importance.png)

Mer information om hur modell förklaringar och funktions prioritet kan aktive ras på andra områden i SDK utanför Automatisk maskin inlärning finns i avsnittet [begrepp](machine-learning-interpretability-explainability.md) om tolkning.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du vill distribuera en modell](how-to-deploy-and-where.md).

Lär dig mer om [hur du tränar en Regressions modell med automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar användning av automatiserad maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).
