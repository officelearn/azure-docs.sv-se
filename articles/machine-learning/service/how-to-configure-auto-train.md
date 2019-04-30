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
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 33d8e18dcec98710443623c03651aa568aa37009
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819929"
---
# <a name="configure-automated-machine-learning-experiments"></a>Konfigurera automatisk machine learning-experiment

Automatiserad machine learning hämtar en algoritm och hyperparametrar för dig och genererar en modell som är klar för distribution. Det finns flera alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment. I den här guiden lär du dig hur du definierar olika konfigurationsinställningar.

Exempel på en automatiserad maskininlärningsexperiment finns [självstudien: Träna en modell för klassificering med automatiserade machine learning](tutorial-auto-train-models.md) eller [träna modeller med automatiserade maskininlärning i molnet](how-to-auto-train-remote.md).

Konfigurationsalternativ är tillgängliga i automatiserade machine learning:

* Välj din typ av experiment: Klassificering, Regression eller tidsserier prognoser
* Datakällan, format och hämta data
* Välj compute-mål: lokal eller fjärransluten
* Automatiserad machine learning-experiment-inställningar
* Kör en automatiserad machine learning-experiment
* Utforska mått i modellen
* Registrera och distribuera modellen

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
[DNN Classifer](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN linjär klassificerare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linjär Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Linjär Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[Stokastisk brantaste Lutningsmetoden (Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|


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
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
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
X_valid | Pandas-Dataframe eller Numpy matris   | data_train, etikett | _Valfritt_ alla funktioner ska verifiera med. Om inte anges X delas mellan träna och validera
y_valid |   Pandas-Dataframe eller Numpy matris | data_train, etikett | _Valfritt_ etikett data ska verifiera med. Om inte anges y delas mellan träna och validera
sample_weight | Pandas-Dataframe eller Numpy matris |   data_train, etikett, kolumner| _Valfritt_ ett viktningsvärde för varje exempel. Använd när du vill tilldela olika vikter för din datapunkter
sample_weight_valid | Pandas-Dataframe eller Numpy matris | data_train, etikett, kolumner |    _Valfritt_ ett viktningsvärde för varje Verifieringsexempel. Om inte anges sample_weight delas mellan träna och validera
data_train |    Pandas-Dataframe |  X, y, X_valid, y_valid |    Alla data (funktioner + etiketten) för att träna med
etikett | sträng  | X, y, X_valid, y_valid |  Vilken kolumn i data_train representerar etiketten
Kolumner | matris med strängar  ||  _Valfritt_ godkänd lista över kolumner som ska användas för funktioner
cv_splits_indices   | Heltalsmatris ||  _Valfritt_ lista över index att dela data för mellan verifiering

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>Läsa in och förbereda data med hjälp av DataPrep SDK
Automatiserad maskininlärningsexperiment stöder inläsning av data och transformerar med hjälp av den förberedelse av data SDK. Med hjälp av SDK gör möjligheten att

>* Läsa in från många filtyper med parsning parametern inferens (kodning, avgränsare, rubriker)
>* Typ konverze med inferens under inläsningen av filen
>* Stödet för MS SQL Server och Azure Data Lake Storage
>* Lägg till kolumnen med ett uttryck
>* Sedan imputera värden som saknas
>* Härled kolumner med exempel
>* Filtrering
>* Anpassad Python-transformeringar

Lär dig mer om data prep sdk finns den [hur du förbereder data för modellering av artikeln](how-to-load-data.md).
Nedan visas ett exempel som läser in data med hjälp av data prep SDK: n.
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>Träna och verifiering

Du kan ange separata träna upp och verifiering ange antingen via get_data() eller direkt i den `AutoMLConfig` metoden.

## <a name="cross-validation-split-options"></a>Delningsalternativ mellan verifiering

### <a name="k-folds-cross-validation"></a>K Vikningar Korsvalidering

Använd `n_cross_validations` inställningen för att ange hur många olika plattformar verifieringar. Utbildning datauppsättningen slumpmässigt delas upp i `n_cross_validations` vikningar av samma storlek. Vid varje mellan verifiering av runda används en av vikningar som för verifiering av modellen som har tränats på återstående vikningar. Den här processen upprepas för `n_cross_validations` Avrundar tills varje vikning används en gång som angetts för verifiering. Genomsnittlig poängen för alla `n_cross_validations` Avrundar rapporteras och motsvarande modellen kommer modellkomponenten utbildning på hela datauppsättningen. 

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo Korsvalidering (alias) Upprepas underordnade stickprov)

Använd `validation_size` att ange procentandelen av datauppsättning för träning som ska användas för verifiering och Använd `n_cross_validations` kan du ange antalet mellan verifieringar. Under varje korsvalidering avrunda en delmängd av storleken `validation_size` väljs slumpmässigt för verifiering av modellen som har tränats på kvarvarande data. Slutligen medelvärdet poängsätter för alla `n_cross_validations` Avrundar rapporteras och motsvarande modellen kommer modellkomponenten utbildning på hela datauppsättningen. Monte Carlo stöds inte för tidsserier.

### <a name="custom-validation-dataset"></a>Anpassad validering datauppsättning

Använd anpassad validering datauppsättning om slumpmässiga delning inte kan godkännas, vanligtvis time series-data eller imbalanced data. Du kan ange verifiering datauppsättningen. Modellen kommer att utvärderas mot verifiering datauppsättningen som anges i stället för slumpmässiga datauppsättning.

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Därefter fastställer där modellen ska tränas. En automatiserad machine learning-träningsexperiment kan köras på följande beräkningsalternativ:
*   Den lokala datorn, till exempel en lokal stationär eller bärbar dator – Allmänt när du har liten datamängd och du fortfarande är i fasen utforskning.
*   En fjärrdator i molnet – [Azure Machine Learning-hanterade Compute](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets) är en hanterad tjänst som gör möjligheten att träna maskininlärningsmodeller på kluster av virtuella Azure-datorer.

Se den [GitHub-webbplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) till exempel bärbara datorer med lokal och fjärransluten beräkningsmål.

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

## <a name="primary-metric"></a>Primär mått
Det primära metriskt; som du ser i exemplen ovan anger mått som ska användas när modellen för optimering. Den primära mått som du kan välja bestäms av Uppgiftstyp som du väljer. Nedan visas en lista över tillgängliga mått.

|Klassificering | Regression | Time Series prognoser
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

## <a name="data-pre-processing-and-featurization"></a>Förbearbetning av data och funktionalisering

Om du använder `preprocess=True`, följande data Förbearbeta stegen utförs automatiskt åt dig:
1.  Ta bort hög kardinalitet- eller inga varians-funktioner
    * Ta bort funktioner utan viktig information från uppsättningar för träning och verifiering. Dessa inkluderar funktioner med alla värden som saknas, samma värde på alla rader eller med mycket hög kardinalitet (t.ex. hashvärden, ID: N eller GUID).
1.  Saknas värde-uppräkning
    *   Sedan imputera saknade värden med medelvärdet av värdena i kolumnen för numeriska funktioner.
    *   Sedan imputera saknade värden med vanligast förekommande värdet för kategoriska funktioner.
1.  Generera ytterligare funktioner
    * För DateTime-funktioner: År, månad, dag, dag i vecka, dag på år, kvartal, vecka på året, timme, minut, sekund.
    * För Text-funktioner: Termen frekvensen baserat på ordet unigram, bi-g och tre-gram, antal vectorizer.
1.  Transformeringar och kodningar
    * Numeriska funktioner med mycket få unika värden som omvandlas till kategoriska funktioner.
    * Beroende på Kardinaliteten för kategoriska funktioner, utföra etikett kodning eller (hash) en frekvent kodning.

## <a name="ensemble-models"></a>Ensemble modeller
Ensemble learning förbättrar machine learning resultat och förutsägbar prestanda genom att nyhetsnotiser genomsöks många modeller till skillnad från med enkel modeller. När du använder automatisk maskininlärning, kan du träna ensemble modeller med hjälp av den [Caruana ensemble val av algoritmen med sorterade Ensemble initieringen](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Ensemble iteration visas som den senaste iterationen av din körning.

## <a name="run-experiment"></a>Kör experimentet

Skicka experiment för att köra och generera en modell. Skicka den `AutoMLConfig` till den `submit` metod för att generera modellen.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Beroenden installeras först på en ny dator.  Det kan ta upp till 10 minuter innan utdata visas.
>Ange `show_output` till `True` i utdata som visas på konsolen.

## <a name="exit-criteria"></a>Avsluta-villkor 
Det ett par alternativ du kan definiera för att slutföra experimentet.
1. Inga kriterier - om du inte definierar någon avsluta parametrar experimentet fortsätter tills inga ytterligare framsteg görs på din primära mått. 
1. Antalet iterationer - definierar du antalet upprepningar som krävs att köra experimentet. Du kan valfritt lägga till iteration_timeout_minutes för att definiera en tidsgräns i minuter per varje iteration.
1. Avsluta efter en lång tid – med hjälp av experiment_timeout_minutes i dina inställningar som du kan definiera hur länge i minuter bör fortsätta ett experiment i körning.
1. Avsluta när en poäng nåtts - med experiment_exit_score som du kan välja att utföra experiement när en poäng baserat på din primära mått har uppnåtts.

## <a name="explore-model-metrics"></a>Utforska mått i modellen
Du kan visa dina resultat i en widget eller en infogad om du är på en bärbar dator. Se [spåra och utvärdera modeller](how-to-track-experiments.md#view-run-details) för mer information.


### <a name="classification-metrics"></a>Klassificering mått
Följande mått har sparats i varje iteration för en klassificering.

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
norm_macro_recall|Normaliserade makrot återkalla är makrot återkalla normalized så att slumpmässiga prestanda har ett värde på 0 och perfekt prestanda har ett värde på 1. Detta uppnås med norm_macro_recall: = (recall_score_macro - R) /(1-R), där R är det förväntade värdet recall_score_macro för slumpmässiga förutsägelser (d.v.s. R = 0,5 för binär klassificering) och R=(1/C) för problem med C-class klassificering|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”makrot” och sedan (recall_score_macro - R) /(1-R), där R är det förväntade värdet recall_score_macro för slumpmässiga förutsägelser (d.v.s. R = 0,5 för binär klassificering) och R=(1/C) för problem med C-class klassificering|
precision_score_macro|Precisionen är procent av element som är märkt som en viss klass som faktiskt finns i den klassen. Makrot är det aritmetiska medelvärdet av precision för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”makrot”|
precision_score_micro|Precisionen är procent av element som är märkt som en viss klass som faktiskt finns i den klassen. Micro beräknas globalt genom att räkna totala positiva och falska positiva identifieringar|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”micro”|
precision_score_weighted|Precisionen är procent av element som är märkt som en viss klass som faktiskt finns i den klassen. Viktat är det aritmetiska medelvärdet av precision för varje klass, viktat av antalet SANT instanser i varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Genomsnittlig = ”viktat”|
recall_score_macro|Återkallande är procent av element faktiskt i en viss klass som är märkta korrekt. Makrot är det aritmetiska medelvärdet om återkallande för varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”makrot”|
recall_score_micro|Återkallande är procent av element faktiskt i en viss klass som är märkta korrekt. Micro beräknas globalt genom att räkna totala positiva, false negativ|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”micro”|
recall_score_weighted|Återkallande är procent av element faktiskt i en viss klass som är märkta korrekt. Viktat är det aritmetiska medelvärdet om återkallande för varje klass, viktat av antalet SANT instanser i varje klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Genomsnittlig = ”viktat”|
weighted_accuracy|Viktad Precision är Precision där vikten för varje exempel är lika med andelen SANT instanser i det exemplet true-klass|[Beräkning](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight är en vektor som är lika med andelen av den klassen för varje element i målet|

### <a name="regression-and-time-series-forecasting-metrics"></a>Regression och time series prognoser mått
Följande mått har sparats i varje iteration för en regression eller prognosmodellen uppgift.

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

## <a name="explain-the-model"></a>Förklara modellen

När automatisk maskininlärningsförmågor är allmänt tillgängliga **modellen explainability funktionen är fortfarande i förhandsversion.**

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

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![funktionen vikten graph](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du vill distribuera en modell](how-to-deploy-and-where.md).

Läs mer om [hur du tränar en regressionsmodell med automatisk machine learning](tutorial-auto-train-models.md) eller [hur du tränar med hjälp av automatisk machine learning i en fjärransluten resurs](how-to-auto-train-remote.md).
