---
title: Konfigurera dina automatiserade machine learning-experiment - Azure Machine Learning
description: Automatiserad machine learning hämtar en algoritm för dig och genererar en modell som är klar för distribution. Läs om de alternativ som du kan använda för att konfigurera automatiserade maskininlärningsexperiment.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1aeb1315cfafbcdf3507a6e49d71e1f1e69b537c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430195"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>Konfigurera dina automatiserade machine learning-experiment

Automatiserad machine learning (automatiserade ML) hämtar en algoritm och hyperparametrar för dig och genererar en modell som är klar för distribution. Modellen kan laddas ned för att anpassas ytterligare samt. Det finns flera alternativ som du kan använda för att konfigurera automatisk ML-experiment. I den här guiden kommer du lära dig hur du definierar olika konfigurationsinställningar.

Exempel på en automatiserad ML finns [självstudie: tränar en modell för klassificering med automatiserade machine learning](tutorial-auto-train-models.md) eller [träna modeller med automatiserade maskininlärning i molnet](how-to-auto-train-remote.md).

Konfigurationsalternativ är tillgängliga i automatiserade machine learning:

* Väljer du typen experiment, t.ex. klassificering, Regression 
* Datakällan, format och hämta data
* Välj din beräkningsmål (lokal eller fjärransluten)
* Automatiska inställningar för ML-experiment
* Kör en automatiserad ML-experiment
* Utforska mått i modellen
* Registrera och distribuera modellen

## <a name="select-your-experiment-type"></a>Väljer du typen av experimentet
Innan du börjar experimentet måste bestämma du vilken typ av machine learning-problem som du vill lösa. Automatiserad ML stöder två typer av övervakad inlärning: klassificerings- och Regressionsmodeller. Automatiserad ML stöder följande algoritmer under automation och justering processen. Som en användare finns det inget behov av att ange algoritmen.
Klassificering | Regression
--|--
sklearn.linear_model. LogisticRegression | sklearn.linear_model. ElasticNet
sklearn.linear_model. SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes. BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes. MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model. LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model. SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm. LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm. LGBMClassifier |


## <a name="data-source-and-format"></a>Datakällan och format
Automatiserad ML har stöd för data som finns på din lokala dator eller i molnet i Azure Blob Storage. Data kan läsas in i scikit-Läs stöds dataformat. Du kan läsa data till 1) Numpy matriser X (funktioner) och y (målvariabel eller så kallade etikett) eller 2) Pandas-dataframe. 

Exempel:

1.  Numpy matriser

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Pandas-dataframe

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>Hämta data för att köra experiment på fjärranslutna beräkning

Om du använder en fjärransluten beräkning för att köra experimentet Datahämtningen måste inneslutas i en separat python-skriptet `get_data()`. Det här skriptet körs i den fjärranslutna beräkningen där automatiserade ML experimentet har körts. `get_data` eliminerar behovet av att hämta data i rörelse för varje iteration. Utan `get_data`, experimentet misslyckas när du kör på fjärranslutna beräkning.

Här är ett exempel på `get_data`:

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
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

`get_data` skriptet kan returnera följande:
Nyckel | Typ |    Ömsesidigt uteslutande med | Beskrivning
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

## <a name="train-and-validation-data"></a>Träna och verifiering

Du kan ange separata träna upp och verifiering ange antingen via get_data() eller direkt i den `AutoMLConfig` metoden.

## <a name="cross-validation-split-options"></a>Delningsalternativ mellan verifiering

### <a name="k-folds-cross-validation"></a>K Vikningar Korsvalidering

Använd `n_cross_validations` inställningen för att ange hur många olika plattformar verifieringar. Utbildning datauppsättningen slumpmässigt delas upp i `n_cross_validations` vikningar av samma storlek. Vid varje mellan verifiering av runda används en av vikningar som för verifiering av modellen som har tränats på återstående vikningar. Den här processen upprepas för `n_cross_validations` Avrundar tills varje vikning används en gång som angetts för verifiering. Slutligen medelvärdet poängsätter för alla `n_cross_validations` Avrundar rapporteras och motsvarande modellen kommer modellkomponenten utbildning på hela datauppsättningen.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>Monte Carlo Korsvalidering (alias) Upprepas underordnade stickprov)

Använd `validation_size` att ange procentandelen av datauppsättning för träning som ska användas för verifiering och Använd `n_cross_validations` kan du ange antalet mellan verifieringar. Under varje korsvalidering avrunda en delmängd av storleken `validation_size` väljs slumpmässigt för verifiering av modellen som har tränats på kvarvarande data. Slutligen medelvärdet poängsätter för alla `n_cross_validations` Avrundar rapporteras och motsvarande modellen kommer modellkomponenten utbildning på hela datauppsättningen.

### <a name="custom-validation-dataset"></a>Anpassad validering datauppsättning

Använd anpassad validering datauppsättning om slumpmässiga delning inte kan godkännas (vanligtvis time series-data eller imbalanced data). Med detta kan ange du verifiering datauppsättningen. Modellen kommer att utvärderas mot verifiering datauppsättningen som anges i stället för slumpmässiga datauppsättning.

## <a name="compute-to-run-experiment"></a>Beräkning för att köra experiment

Därefter fastställer där modellen ska tränas. En automatiserad ML-träningsexperiment körs på ett beräkningsmål som du äger och hanterar. 

Compute-alternativ som stöds är:
1.  Den lokala datorn, till exempel en lokal stationär eller bärbar dator – Allmänt när du har liten datamängd och du fortfarande är i fasen utforskning.
2.  En fjärrdator i molnet – [Azure Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) som kör Linux – du har en stor datauppsättning och vill skala upp till en stor virtuell dator som är tillgänglig i Azure-molnet. 
3.  Azure Batch AI-kluster – A hanterade kluster som du kan ställa in att skala ut och köra automatiserade ML iterationer parallellt. 

<a name='configure-experiment'/>
## <a name="configure-your-experiment-settings"></a>Konfigurera inställningarna för experiment

Det finns flera rattar som du kan använda för att konfigurera ditt automatiserade ML-experiment. Dessa parametrar anges av instansiera en `AutoMLConfig` objekt.

Några exempel är:

1.  Klassificering experiment med AUC viktad som primär mått med en maximal tid på 12 000 sekunder per iteration med experimentet att avsluta när du har 50 iterationer och 2 mellan verifiering vikningar.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  Nedan visas ett exempel på en regression experiment uppsättning avslutas efter 100 iterationer, där varje iteration långvarigt upp till 600 sekunder med 5 verifiering mellan vikningar.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

Den här tabellen anger parameterinställningar som är tillgängliga för experimentet och deras standardvärden.

Egenskap  |  Beskrivning | Standardvärde
--|--|--
`task`  |Ange vilken typ av problem med machine learning. Tillåtna värden är <li>Klassificering</li><li>Regression</li>    | Ingen |
`primary_metric` |Mått som du vill optimera i att skapa din modell. Om du anger Precision som primary_metric, söker exempelvis automatisk ML för att hitta en modell med största noggrannhet. Du kan bara ange en primary_metric per experiment. Tillåtna värden är <br/>**Klassificering**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**Regression**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | För klassificering: Precision <br/>För Regression: spearman_correlation <br/> |
`exit_score` |  Du kan ange ett målvärde för din primary_metric. När en modell finns som uppfyller primary_metric mål, automatiserad ML stoppar iterera och försöket avslutas. Om det här värdet inte anges (standard), fortsätter automatiserad ML-experiment att köra antal upprepningar som anges i iterationer. Tar ett double-värde. Om målet når aldrig, fortsätter automatiserad ML tills den når antal upprepningar som anges i iterationer.|   Ingen
`iterations` |Maximalt antal upprepningar. Varje iteration är lika med ett utbildningsjobb som resulterar i en pipeline. Pipeline är Förbearbeta data och modell. Få en högkvalitativ modellen använder 250 eller fler | 100
`Concurrent_iterations`|    Maxantal upprepningar som ska köras parallellt. Den här inställningen fungerar endast för fjärranslutna beräkning.|    1
`max_cores_per_iteration`   | Anger hur många kärnor på beräkningsmål skulle användas för att träna en enkel rörledning. Om algoritmen kan utnyttja flera kärnor, ökar prestanda på en dator med flera kärnor. Du kan ange den till -1 för att använda alla tillgängliga kärnor på datorn.|  1
`max_time_sec` |    Begränsar mängden tid (sekunder) tar för en viss iteration. Om en iteration överskrider angiven mängd, den iterationen hämtar har avbrutits. Om den inte anges kommer iterationen fortsätter att köras tills den är klar. |   Ingen
`n_cross_validations`   |Antalet delningar i korsverifieringar| Ingen
`validation_size`   |Storleken på verifiering som procent av alla utbildning exemplet.|  Ingen
`preprocess` | SANT/FALSKT <br/>True aktiverar experimentera om du vill utföra Förbearbeta på indata. Följande är en delmängd av Förbearbeta<li>Data saknas: Imputes det saknade data numeriska med Average, Text med de flesta förekomst </li><li>Kategoriska värden: Om-datatypen är numeriska datatyper och antalet unika värden är mindre än 5 procent, konverterar till en hot-kodning </li><li>Etc. fullständig lista finns [GitHub-lagringsplatsen](https://aka.ms/aml-notebooks)</li><br/>Obs: om data som är gles du inte använda Förbearbeta = true |  False | 
`blacklist_algos`   | Automatiserad ML-experiment har många olika algoritmer som försök. Konfigurera automatisk ML för att undanta vissa algoritmer från experimentet. Användbart om du är medveten om att algoritm(er) inte fungerar bra för din datauppsättning. Exkludera algoritmer kan spara beräkningsresurser och utbildning.<br/>Tillåtna värden för klassificering<br/><li>Logistic regression</li><li>Descent klassificerare</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>extra träd</li><li>gradient boosting</li><li>lgbm_classifier</li><br/>Tillåtna värden för Regression<br/><li>Elastisk net</li><li>Gradient boosting regressor</li><li>DT regressor</li><li>kNN regressor</li><li>Lasso Dell</li><li>Descent regressor</li><li>RF regressor</li><li>extra träd regressor</li>|   Ingen
`verbosity` |Styr loggningsnivå med information som är den mest utförliga och kritiska som minsta möjliga.<br/>Tillåtna värden är:<br/><li>logging.INFO</li><li>loggning. VARNING</li><li>loggning. FEL</li><li>loggning. KRITISKA</li>  | logging.INFO</li> 
`X` | Alla funktioner för att träna med |  Ingen
`y` |   Märk data att träna med. Klassificering, bör vara en matris av heltal.|  Ingen
`X_valid`|_Valfritt_ alla funktioner ska verifiera med. Om inte anges X delas mellan träna och validera |   Ingen
`y_valid`   |_Valfritt_ etikett data ska verifiera med. Om inte anges y delas mellan träna och validera    | Ingen
`sample_weight` |   _Valfritt_ ett viktningsvärde för varje exempel. Använd när du vill tilldela olika vikter för din datapunkter |   Ingen
`sample_weight_valid`   |   _Valfritt_ ett viktningsvärde för varje Verifieringsexempel. Om inte anges sample_weight delas mellan träna och validera   | Ingen
`run_configuration` |   RunConfiguration-objekt.  Används för fjärråtkomst körs. |Ingen
`data_script`  |    Sökväg till en fil som innehåller get_data-metoden.  Krävs för remote körs.   |Ingen


## <a name="run-experiment"></a>Kör experimentet

Vi kan sedan starta experimentet för att köra och generera en modell för oss. Skicka den `AutoMLConfig` till den `submit` metod för att generera modellen.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>Beroenden installeras först på en ny DSVM.  Det kan ta upp till 10 minuter innan utdata visas.
>Ange `show_output` till SANT resulterar i utdata som visas på konsolen.


## <a name="explore-model-metrics"></a>Utforska mått i modellen
Du kan visa dina resultat i en widget eller en infogad om du är på en bärbar dator. Se information om att ”spåra och utvärdera modeller”. (kontrollera AML-innehållet innehåller relevant information till automatiserade ML)

Följande mått har sparats i varje iteration
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur och var du vill distribuera en modell](how-to-deploy-and-where.md).

Läs mer om [hur du tränar en modell för klassificering med automatiserade ML](tutorial-auto-train-models.md) eller [hur du tränar med automatiserade ML på en fjärransluten resurs](how-to-auto-train-remote.md). 