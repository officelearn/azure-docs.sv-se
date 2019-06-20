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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 9c003ebaed645fcdefb379eb100220ccc2207d82
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202980"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Konfigurera automatisk ML-experiment i Python

I den här guiden lär du dig hur du definierar olika konfigurationsinställningar för dina automatiserade machine learning-experiment med den [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Automatiserad machine learning hämtar en algoritm och hyperparametrar för dig och genererar en modell som är klar för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment.

Exempel på en automatiserad maskininlärningsexperiment finns [självstudien: Träna en modell för klassificering med automatiserade machine learning](tutorial-auto-train-models.md) eller [träna modeller med automatiserade maskininlärning i molnet](how-to-auto-train-remote.md).

Konfigurationsalternativ är tillgängliga i automatiserade machine learning:

* Välj din typ av experiment: Klassificering, Regression eller tidsserier prognoser
* Datakällan, format och hämta data
* Välj compute-mål: lokal eller fjärransluten
* Automatiserad machine learning-experiment-inställningar
* Kör en automatiserad machine learning-experiment
* Utforska mått i modellen
* Registrera och distribuera modellen

Om du föredrar en ingen kod-upplevelsen kan du också [skapa dina automatiserade machine learning-experiment i Azure-portalen](how-to-create-portal-experiments.md).

## <a name="select-your-experiment-type"></a>Väljer du typen av experimentet

Innan du börjar experimentet måste bestämma du vilken typ av machine learning-problem som du vill lösa. Automatiserad maskininlärning stöder uppgift typer av klassificering, regression och prognostisering.

Automatiserad maskininlärning stöder följande algoritmer under automation och justera processen. Som en användare finns det inget behov av att ange algoritmen. DNN-algoritmer är tillgängliga vid träning, automatiserade ML inte att skapa DNN-modeller.

Klassificering | Regression | Time Series prognoser
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
[DNN klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN linjär klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linjär Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Linjär Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

Använd den `task` parametern i den `AutoMLConfig` att ange din typ av experiment.

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

Om du använder en fjärransluten beräkning för att köra experimentet Datahämtningen måste inneslutas i en separat python-skriptet `get_data()`. Det här skriptet körs i den fjärranslutna beräkningen där automatiserade machine learning-experiment körs. `get_data` eliminerar behovet av att hämta data i rörelse för varje iteration. Utan `get_data`, experimentet misslyckas när du kör på fjärranslutna beräkning.

Här är ett exempel på `get_data`:

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

I din `AutoMLConfig` objekt kan du ange den `data_script` parametern och ange sökvägen till den `get_data` skriptfilen ungefär som nedan:

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` skriptet kan returnera:

Nyckel | Typ | Ömsesidigt uteslutande med    | Beskrivning
---|---|---|---
X | Pandas-Dataframe eller Numpy matris | data_train, etikett, kolumner |  Alla funktioner för att träna med
Y | Pandas-Dataframe eller Numpy matris |   etikett   | Märk data att träna med. Klassificering, bör vara en matris av heltal.
X_valid | Pandas-Dataframe eller Numpy matris   | data_train, etikett | _Valfritt_ funktionen data som utgör uppsättningen verifiering. Om inte anges X delas mellan träna och validera
y_valid |   Pandas-Dataframe eller Numpy matris | data_train, etikett | _Valfritt_ etikett data ska verifiera med. Om inte anges y delas mellan träna och validera
sample_weight | Pandas-Dataframe eller Numpy matris |   data_train, etikett, kolumner| _Valfritt_ ett viktningsvärde för varje exempel. Använd när du vill tilldela olika vikter för din datapunkter
sample_weight_valid | Pandas-Dataframe eller Numpy matris | data_train, etikett, kolumner |    _Valfritt_ ett viktningsvärde för varje Verifieringsexempel. Om inte anges sample_weight delas mellan träna och validera
data_train |    Pandas-Dataframe |  X, y, X_valid, y_valid |    Alla data (funktioner + etiketten) för att träna med
etikett | sträng  | X, y, X_valid, y_valid |  Vilken kolumn i data_train representerar etiketten
Kolumner | matris med strängar  ||  _Valfritt_ godkänd lista över kolumner som ska användas för funktioner
cv_splits_indices   | Heltalsmatris ||  _Valfritt_ lista över index att dela data för mellan verifiering

## <a name="train-and-validation-data"></a>Träna och verifiering

Du kan ange separata träna upp och verifiering ange antingen via get_data() eller direkt i den `AutoMLConfig` metoden.

### <a name="k-folds-cross-validation"></a>K Vikningar Korsvalidering

Använd `n_cross_validations` inställningen för att ange hur många olika plattformar verifieringar. Utbildning datauppsättningen slumpmässigt delas upp i `n_cross_validations` vikningar av samma storlek. Vid varje mellan verifiering av runda används en av vikningar som för verifiering av modellen som har tränats på återstående vikningar. Den här processen upprepas för `n_cross_validations` Avrundar tills varje vikning används en gång som angetts för verifiering. Genomsnittlig poängen för alla `n_cross_validations` Avrundar rapporteras och motsvarande modellen kommer modellkomponenten utbildning på hela datauppsättningen.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo mellan verifiering (upprepade underordnade stickprov)

Använd `validation_size` att ange procentandelen av datauppsättning för träning som ska användas för verifiering och Använd `n_cross_validations` kan du ange antalet mellan verifieringar. Under varje korsvalidering avrunda en delmängd av storleken `validation_size` väljs slumpmässigt för verifiering av modellen som har tränats på kvarvarande data. Slutligen medelvärdet poängsätter för alla `n_cross_validations` Avrundar rapporteras och motsvarande modellen kommer modellkomponenten utbildning på hela datauppsättningen. Monte Carlo stöds inte för tidsserier.

### <a name="custom-validation-dataset"></a>Anpassad validering datauppsättning

Använd anpassad validering datauppsättning om slumpmässiga delning inte kan godkännas, vanligtvis time series-data eller imbalanced data. Du kan ange verifiering datauppsättningen. Modellen kommer att utvärderas mot verifiering datauppsättningen som anges i stället för slumpmässiga datauppsättning.

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Därefter fastställer där modellen ska tränas. En automatiserad machine learning-träningsexperiment kan köras på följande beräkningsalternativ:
*   Den lokala datorn, till exempel en lokal stationär eller bärbar dator – Allmänt när du har liten datamängd och du fortfarande är i fasen utforskning.
*   En fjärrdator i molnet – [Azure Machine Learning-hanterade Compute](concept-compute-target.md#amlcompute) är en hanterad tjänst som gör möjligheten att träna maskininlärningsmodeller på kluster av virtuella Azure-datorer.

Se den [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) till exempel bärbara datorer med lokal och fjärransluten beräkningsmål.

*   Ett Azure Databricks-kluster i Azure-prenumerationen. Du hittar mer information här – [installationsprogrammet Azure Databricks-klustret för automatiserad ML](how-to-configure-environment.md#azure-databricks)

Se den [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) till exempel bärbara datorer med Azure Databricks.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>Konfigurera inställningarna för experiment

Det finns flera alternativ som du kan använda för att konfigurera dina automatiserade machine learning-experiment. Dessa parametrar anges av instansiera en `AutoMLConfig` objekt. Se den [AutoMLConfig klass](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) för en fullständig lista över parametrar.

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

Tre olika `task` parametervärden avgörs av algoritmer för att tillämpa.  Använd den `whitelist` eller `blacklist` parametrar för att ändra ytterligare iterationer med de tillgängliga algoritmerna för att inkludera eller exkludera. Listan över modeller som stöds finns på [SupportedAlgorithms klass](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py).

### <a name="primary-metric"></a>Primär mått
Det primära metriskt; som du ser i exemplen ovan anger mått som ska användas när modellen för optimering. Den primära mått som du kan välja bestäms av Uppgiftstyp som du väljer. Nedan visas en lista över tillgängliga mått.

|Klassificering | Regression | Time Series prognoser
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>Förbearbeta data & funktionalisering

I varje automatiserade machine learning-experiment, dina data är [automatiskt skalas och normalized](concept-automated-ml.md#preprocess) att algoritmer som gör.  Du kan också aktivera ytterligare Förbearbeta/funktionalisering, till exempel värden uppräkning, kodning och transformeringar som saknas. [Mer information om vilka funktionalisering ingår](how-to-create-portal-experiments.md#preprocess).

Om du vill aktivera den här funktionalisering ange `"preprocess": True` för den [ `AutoMLConfig` klass](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

### <a name="time-series-forecasting"></a>Time Series prognoser
Time series prognosmodellen Uppgiftstyp har ytterligare parametrar för att definiera.
1. time_column_name – det här är en obligatorisk parameter som definierar namnet på kolumnen i din utbildning som innehåller datum/tid dataserien.
1. max_horizon - detta definierar hur lång tid som du vill förutsäga ut baserat på periodiciteten för träningsdata. Till exempel om du har träningsdata till dagliga tid kärnor kan du definiera hur långt ut i dagar som du vill att modellen för att träna för.
1. grain_column_names - detta definierar namnet på kolumner som innehåller enskilda time series-data i dina utbildningsdata. Om du försäljningsprognoser för ett visst varumärke per butik, skulle du till exempel definiera store och varumärke kolumner som grain-kolumner.

Se exempel på dessa inställningar som används nedan, notebook-exempel finns [här](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb).

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

## <a name="run-experiment"></a>Kör experimentet

För automatiserade ML behöver du skapa en `Experiment` -objektet, vilket är namngivna objekt i en `Workspace` används för att köra experiment.

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

### <a name="exit-criteria"></a>Avsluta-villkor
Det ett par alternativ du kan definiera för att slutföra experimentet.
1. Inga kriterier - om du inte definierar någon avsluta parametrar experimentet fortsätter tills inga ytterligare framsteg görs på din primära mått.
1. Antalet iterationer - definierar du antalet upprepningar som krävs att köra experimentet. Du kan valfritt lägga till iteration_timeout_minutes för att definiera en tidsgräns i minuter per varje iteration.
1. Avsluta efter en lång tid – med hjälp av experiment_timeout_minutes i dina inställningar som du kan definiera hur länge i minuter bör fortsätta ett experiment i körning.
1. Avsluta när en poäng nåtts - med experiment_exit_score som du kan välja att utföra experimentet när en poäng baserat på din primära mått har uppnåtts.

### <a name="explore-model-metrics"></a>Utforska mått i modellen

Du kan visa dina utbildning resultat i en widget eller en infogad om du är på en bärbar dator. Se [spåra och utvärdera modeller](how-to-track-experiments.md#view-run-details) för mer information.

## <a name="understand-automated-ml-models"></a>Förstå automatiserade ML-modeller

Alla modeller som skapas med hjälp av automatisk ML omfattar följande steg:
+ Automatiserad funktionsframställning (om Förbearbeta = True)
+ Skala/normalisering och algoritm med hypermeter värden

Vi gör det transparent för att hämta informationen från fitted_model utdata från automatisk ML.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>Automatiska funktioner

Se en lista över Förbearbeta och [automatiserad funktionsframställning](concept-automated-ml.md#preprocess) som händer när Förbearbeta = True.

Överväg det här exemplet:
+ Det finns 4 indatafunktionerna: A (numeriska), B (numeriska), C (numeriska), D (DateTime)
+ Numeriska funktionen C har släppts eftersom det är en ID-kolumn med enbart unika värden
+ Numeriska funktioner A och B har värden som saknas och därför är tillräknade med
+ DateTime-funktionen D är trädmodell till 11 olika bakåtkompilerade funktioner

Använd dessa 2 API: er på det första steget i anpassade modell som vill veta mer.  Se [exempel anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

+ API 1: `get_engineered_feature_names()` returnerar en lista med bakåtkompilerade funktionsnamn.

  Användning:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  Den här listan innehåller alla bakåtkompilerade funktionsnamn.

  >[!Note]
  >Använda 'timeseriestransformer' för uppgiften = 'prognoser ”annan användning 'datatransformer' för 'regression' eller 'klassificering” uppgiften.

+ API 2: `get_featurization_summary()` returnerar funktionalisering sammanfattning för alla funktioner som indata.

  Användning:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >Använda 'timeseriestransformer' för uppgiften = 'prognoser ”annan användning 'datatransformer' för 'regression' eller 'klassificering” uppgiften.

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

   |Resultat|Definition|
   |----|--------|
   |RawFeatureName|Funktionen/indatakolumnen namn från den datauppsättning som angetts.|
   |TypeDetected|Identifierade datatype av indata-funktionen.|
   |Ta bort|Anger om funktionen indata har släppts eller används.|
   |EngineeringFeatureCount|Antal funktioner som genereras genom automatisk funktion engineering transformeringar.|
   |Transformationer|Lista över transformationer som används för att ange funktioner för att generera bakåtkompilerade funktioner.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Skala/normalisering och algoritmen med hypermeter värden:

Använd fitted_model.steps för att förstå de skalning/normalisering och algoritmen/finjustering värdena för en pipeline. [Mer information om skalning/normalisering](concept-automated-ml.md#preprocess). Här är exempel på utdata:

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

Om du vill ha mer information om att använda den här hjälpfunktionen visas i [exempel anteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb).

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

Följande är exempel på utdata för en pipeline med en specifik algoritm (LogisticRegression med RobustScalar i det här fallet).

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

## <a name="explain-the-model-interpretability"></a>Förklara modellen (interpretability)

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

Du kan visualisera funktionen vikten diagrammet på din arbetsyta i Azure-portalen. Diagrammet visas också när du använder en Jupyter-widget på en bärbar dator. Lär dig mer om diagrammen finns på den [exemplet Azure Machine Learning-tjänsten anteckningsböcker artikeln.](samples-notebooks.md)

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![funktionen vikten graph](./media/how-to-configure-auto-train/feature-importance.png)

Mer information om hur modellen förklaringar och funktionen prioritet kan aktiveras i andra delar av SDK: N utanför automatiserade maskininlärning finns det [konceptet](machine-learning-interpretability-explainability.md) artikeln på interpretability.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du vill distribuera en modell](how-to-deploy-and-where.md).

Läs mer om [hur du tränar en regressionsmodell med automatisk machine learning](tutorial-auto-train-models.md) eller [hur du tränar med hjälp av automatisk machine learning i en fjärransluten resurs](how-to-auto-train-remote.md).
