---
title: Modelltolkning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du förklarar varför din modell gör förutsägelser med hjälp av Azure Machine Learning SDK. Den kan användas under utbildning och härledning för att förstå hur din modell gör förutsägelser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 1e742c278b9356c7501964541802e0c96dc74b09
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358658"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Modell tolkning med Azure Machine Learning tjänst

I den här artikeln får du lära dig hur du förklarar varför din modell gjorde förutsägelserna i de olika tolknings paketen för Azure Machine Learning python SDK.

Med hjälp av klasserna och metoderna i SDK kan du hämta:
+ Funktions prioritets värden för både rå och uttillverkade funktioner
+ Tolkning av verkliga data uppsättningar i stor skala, under utbildning och härledning.
+ Interaktiva visualiseringar som hjälper dig att upptäcka mönster i data och förklaringar i utbildnings tid

Under övnings fasen av utvecklings cykeln kan modell designers och utvärderare använda tolknings resultat för en modell för att verifiera Hypotheses och bygga förtroende med intressenter.  De kan också använda insikter i modellen för fel sökning, att validera modell beteendet matchar deras mål och för att kontrol lera om det finns någon kompensation.

I Machine Learning är **funktioner** de data fält som används för att förutsäga en mål data punkt. För att förutsäga kredit risken kan exempelvis data fält för ålder, konto storlek och konto ålder användas. I det här fallet är ålder, konto storlek och konto ålder **funktioner**. Funktions prioriteten visar hur varje data fält påverkar modellens förutsägelser. Till exempel kan ålder användas kraftigt i förutsägelsen när kontots storlek och ålder inte påverkar förutsägelse noggrannheten avsevärt. Den här processen gör det möjligt för data experter att förklara de resulterande förutsägelserna, så att intressenterna får insyn i de data punkter som är viktigast i modellen.

Med hjälp av dessa verktyg kan du förklara maskin inlärnings modeller **globalt på alla data**eller **lokalt på en viss data punkt** med hjälp av de avancerade teknikerna i en lättanvänd och skalbar miljö.

Klasser för tolkning kan göras tillgängliga via flera SDK-paket. Lär dig hur du [installerar SDK-paket för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), huvud paketet, som innehåller funktioner som stöds av Microsoft.

* `azureml.contrib.explain.model`, för hands versioner och experiment funktioner som du kan prova.

* `azureml.train.automl.automlexplainer`paket för att tolka automatiserade maskin inlärnings modeller.

> [!IMPORTANT]
> Innehållet i `contrib` namn området stöds inte fullt ut. När experiment funktionerna blir mogna, kommer de gradvis att flyttas till huvud namn rummet.

## <a name="how-to-interpret-your-model"></a>Så här tolkar du din modell

Du kan använda klasser och metoder för tolkning för att förstå modellens globala beteende eller vissa förutsägelser. Den tidigare kallas global förklaring och den senare kallas lokal förklaring.

Metoderna kan också kategoriseras baserat på om metoden är modell oberoende eller modell Specific. Vissa metoder riktar sig till viss typ av modeller. Till exempel gäller SHAP träd förklaringar endast för trädbaserade modeller. Vissa metoder behandlar modellen som en svart ruta, t. ex. imiterare eller SHAPs kernel-förklaring. `explain` Paketet utnyttjar dessa olika metoder baserat på data uppsättningar, modell typer och användnings fall.

Utdata är en uppsättning information om hur en specifik modell gör dess förutsägelse, till exempel:
* Global/lokal relativ funktions prioritet

* Global/lokal funktion och förutsägelse relation

### <a name="explainers"></a>Förklaringar

Det finns två uppsättningar med förklaringar: Direkta förklaringar och meta-förklaringar i SDK.

__Direkta förklaringar__ kommer från integrerade bibliotek. SDK: n omsluter alla förklaringar så att de visar ett gemensamt API och utdataformat. Om du är mer bekväm med dessa förklaringar kan du direkt anropa dem i stället för att använda det vanliga API-och utdataformat. Nedan visas en lista över de direkta förklaringar som är tillgängliga i SDK:

* **SHAP Tree-förklaring**: SHAP: s träd förklaring, som fokuserar på polynomed Time fast SHAP för värde uppskattning som är speciell för träd och ensembler för träd.
* **SHAP djup förklaring**: Baserat på förklaringen från SHAP är djupgående förklaring en algoritm för hög hastighet för SHAP värden i djup inlärnings modeller som bygger på en anslutning med DeepLIFT som beskrivs i SHAP NIPS-papper. TensorFlow-modeller och keras-modeller med TensorFlow-backend stöds (det finns även stöd för PyTorch) ".
* **SHAP kernel-förklaring**: SHAPs kernel-förklaring använder en särskilt viktad lokal linjär regression för att beräkna SHAP-värden för alla modeller.
* **Imitera förklaring**: Härma förklarar vad som är baserat på idén med globala surrogat modeller. En global surrogat modell är en modell med en inbyggd tolkning som är tränad att approximera förutsägelserna av en svart Box-modell så fort som möjligt. Data expert kan tolka surrogat modellen för att rita slut satser om den svarta Box-modellen. Du kan använda någon av följande tolknings bara modeller som surrogat modell: LightGBM (LinearExplainableModel), linjär regression (LinearExplainableModel), Stochastic gradient brantaste-förklarande modell (SGDExplainableModel) och besluts träd (DecisionTreeExplainableModel).


* **Förklaring till Viktighets funktion**för permutationer: Permutations funktionens betydelse är en teknik som används för att förklara klassificerings-och Regressions modeller som inspireras av Breiman-bladet för [slumpmässiga skogar](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (se avsnitt 10). På en hög nivå är det sättet som det fungerar genom att slumpmässigt blandning data en funktion i taget för hela data uppsättningen och att beräkna hur mycket prestanda måttet för räntan minskar. Ju större ändringen är, desto viktigare är funktionen.

* **Lime-förklaring** (`contrib`): Baserat på LIME används den avancerade oberoende förklaringar (kalk) för att skapa lokala surrogat modeller med hjälp av lime-algoritmen. Till skillnad från globala surrogat modeller fokuserar kalk på att träna lokala surrogat modeller för att förklara enskilda förutsägelser.
* **Text förklaring för han** (`contrib`): Text förklaring för HAN använder ett hierarkiskt Attention-nätverk för att få modell förklaringar från text data för en specifik text modell i svart ruta. Vi tränar den HAN surrogat-modellen på en viss lärares modells förväntade utdata. Efter att ha tränat över texten sökkorpus har vi lagt till ett fin justerings steg för ett särskilt dokument för att förbättra noggrannheten i förklaringarna. HAN använder en dubbelriktad RNN med två åtgärds lager, för mening och ord uppmärksamhet. När DNN har tränats i lärarens modell och finjusteras för ett specifikt dokument kan vi extrahera ordets betydelse från Attention-lagren. Vi har funnit att HAN är mer exakt än kalk eller SHAP för text data, men mer kostsamma vad gäller inlärnings tiden. Vi har dock gjort förbättringar i utbildnings tiden genom att ge användaren möjlighet att initiera nätverket med assisterad Word-inbäddningar, även om det fortfarande är långsamt. Inlärnings tiden kan förbättras avsevärt genom att köra HAN på en virtuell dator med Azure GPU. Implementeringen av HAN beskrivs i "hierarkiska Attention-nätverk för dokument klassificering (Yang et al., 2016)"[https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)().


__Meta-förklaringar__ väljer automatiskt en lämplig direkt förklaring och genererar den bästa förklarings informationen baserat på den aktuella modellen och data uppsättningar. Meta-förklaringarna utnyttjar alla bibliotek (SHAP, kalk, härma osv.) som vi har integrerat eller utvecklat. Följande är de meta-förklaringar som är tillgängliga i SDK:

* **Tabell förklaring**: Används med tabell data uppsättningar.
* **Text förklaring**: Används med text data uppsättningar.
* **Bild förklaring**: Används med bild data uppsättningar.

Förutom meta-markering av direkta förklaringar kan meta-förklaringar utveckla ytterligare funktioner ovanpå de underliggande biblioteken och förbättra hastigheten och skalbarheten i de direkta förklaringarna.

Använder `TabularExplainer` för närvarande följande logik för att anropa de direkta SHAP-förklaringarna:

1. Om det är en träd-baserad modell använder du SHAP `TreeExplainer`, annars
2. Om det är en DNN modell använder du SHAP `DeepExplainer`, annars
3. Behandla den som en svart Box-modell och tillämpa SHAP`KernelExplainer`

Den inbyggda information som `TabularExplainer` är inbyggd i blir mer avancerad eftersom fler bibliotek integreras i SDK och vi lär dig om-och nack delar med varje förklaring.

`TabularExplainer`har också gjort betydande förbättringar av funktioner och prestanda i de direkta förklaringarna:

* **Sammanfattning av initierings data uppsättningen**. I de fall då förklaringen är viktigast sammanfattar vi initierings data uppsättningen och genererar en liten uppsättning representativa exempel, vilket påskyndar både global och lokal förklaring.
* **Sampling av utvärderings data uppsättningen**. Om användaren går igenom en stor uppsättning utvärderings exempel, men inte behöver alla dem för att utvärderas, kan exempel parametern anges till sant för att påskynda den globala förklaringen.

Följande diagram visar den aktuella strukturen för direkta och meta-förklaringar.

[![Machine Learning tolknings arkitektur](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modeller som stöds

Alla modeller som har tränats på data uppsättningar i `numpy.array`python `pandas.DataFrame`, `iml.datatypes.DenseData`, `explain` eller `scipy.sparse.csr_matrix` format stöds av tolknings paketet för SDK: n.

Förklarings funktionerna accepterar både modeller och pipeliner som inmatade. Om en modell anges måste modellen implementera förutsägelse funktionen `predict` eller `predict_proba` som följer Scikit-konventionen. Om en pipeline (namnet på pipelinen) anges förutsätter förklarings funktionen att skriptet för pipeline-körningen returnerar en förutsägelse. Vi har stöd för modeller som har tränats via PyTorch, TensorFlow och keras djup inlärnings ramverk.

### <a name="local-and-remote-compute-target"></a>Lokalt och fjärrstyrt beräknings mål

`explain` Paketet är utformat för att fungera med både lokala och fjärranslutna beräknings mål. Om kör lokalt kontaktar inte SDK-funktionerna några Azure-tjänster. Du kan köra förklaringen via fjärr anslutning på Azure Machine Learning beräkna och logga förklarings informationen i Azure Machine Learning köra historik tjänster. När den här informationen har loggats finns rapporter och visualiseringar från förklaringen enkelt på Azure Machine Learning-arbetsyta Portal för användar analys.

## <a name="interpretability-in-training"></a>Tolkning i utbildning

### <a name="train-and-explain-locally"></a>Utbilda och förklara lokalt

1. Träna din modell i en lokal Jupyter-anteckningsbok.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Anropa förklaringen: Om du vill initiera ett förklarings objekt måste du skicka din modell och vissa utbildnings data till förklaringens konstruktor. Du kan också ange funktions namn och utdataports namn (om du gör klassificering) som ska användas för att göra dina förklaringar och visualiseringar mer informativa. Så här skapar du en instans av ett förklarings objekt med [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)och [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) lokalt. `TabularExplainer`anropar en av de tre SHAP-förklaringarna under`TreeExplainer`( `DeepExplainer`, eller `KernelExplainer`) och väljer automatiskt den lämpligaste för ditt användnings fall. Du kan dock anropa var och en av de tre underliggande förklaringarna direkt.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    eller

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   eller

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. Hämta de globala funktionernas prioritets värden.

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. Hämta prioritets värden för den lokala funktionen: Använd följande funktions anrop för att förklara en enskild instans eller en grupp av instanser. Observera att PFIExplainer inte stöder lokala förklaringar.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    eller

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Utbilda och förklara fjärran slutet

Även om du kan träna på de olika beräknings målen som stöds av Azure Machine Learning-tjänsten, visar exemplet i det här avsnittet hur du gör detta med ett Azure Machine Learning Compute-mål.

1. Skapa ett utbildnings skript i en lokal Jupyter Notebook (till exempel run_explainer. py).

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Följ instruktionerna för att konfigurera [beräknings mål för modell utbildning](how-to-set-up-training-targets.md#amlcompute) för att lära dig mer om hur du konfigurerar en Azure Machine Learning Compute som beräknings mål och hur du skickar in din utbildning.

3. Hämta förklaringen i den lokala Jupyter-anteckningsboken.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Visualiseringar

Använd instrument panelen för visualiseringar för att förstå och tolka din modell:

### <a name="global-visualizations"></a>Globala visualiseringar

I följande områden får du en global vy över den tränade modellen tillsammans med dess förutsägelser och förklaringar.

|Rita|Beskrivning|
|----|-----------|
|Data utforskning| En översikt över data uppsättningen tillsammans med förutsägelse värden.|
|Global prioritet|Visar de viktigaste K-funktionerna (konfigurerbart K) globalt. Det här diagrammet är användbart för att förstå den underliggande modellens globala beteende.|
|Förklarings utforskning|Visar hur en funktion ansvarar för att göra en ändring i modellens förutsägelse värden (eller sannolikheten för förutsägelse värden). |
|Sammanfattning| Använder ett inloggat värde för lokala funktioner i alla data punkter för att Visa fördelningen av effekten som varje funktion har på förutsägelse värdet.|

[![Global instrument panel för visualisering](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Lokala visualiseringar

Du kan klicka på en enskild data punkt när som helst på föregående områden för att läsa in prioritets ritningen för den lokala funktionen för den aktuella data punkten.

|Rita|Beskrivning|
|----|-----------|
|Lokal prioritet|Visar de viktigaste K-funktionerna (konfigurerbart K) globalt. Det här diagrammet är användbart för att förstå den underliggande modellens lokala funktion på en viss data punkt.|
|Perturbation-utforskning|Gör att du kan ändra funktions värden för den valda data punkten och se hur ändringarna påverkar förutsägelse värdet.|
|Individuell villkorlig förväntad (ICE)| Gör att du kan ändra ett funktions värde från ett minimivärde till ett högsta värde för att se hur data punktens förutsägelse ändras när en funktion ändras.|

[![Lokal funktions prioritet för visualiserings instrument panel](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Instrument panel funktionen perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![ICE-observationer på visualiserings instrument panelen](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Observera att du måste ha widgets tillägg för visualiserings instrument panelen som har Aktiver ATS innan Jupyter-kernel startar.

* Jupyter Notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
Använd följande kod för att läsa in visualiserings instrument panelen:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Transformeringar av RAW-funktioner

Om du vill kan du skicka en funktions omvandlings pipeline till förklaringen för att få förklaringar i termer av RAW-funktioner före transformeringen (i stället för de funktioner som har utvecklats). Om du hoppar över detta innehåller förklaringen förklaringar vad gäller de funktioner som har utformats.

Formatet på omvandlingar som stöds är samma som det som beskrivs i [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). I allmänhet stöds alla omvandlingar så länge de arbetar på en enda kolumn och är därför tydligt en till många. 

Vi kan förklara RAW-funktioner genom att antingen `sklearn.compose.ColumnTransformer` använda en eller en lista med monterade transformatorer. Cellen nedan används `sklearn.compose.ColumnTransformer`. 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Om du vill köra exemplet med listan över monterade transformatorer, använder du följande kod: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>Tolkning på inferencing tid

Förklaringen kan distribueras tillsammans med den ursprungliga modellen och kan användas vid beräknings tid för att tillhandahålla den lokala förklarings informationen. Vi erbjuder även undersökare med lättare vikter som gör det lättare att tolka på inferencing tid. Processen för att distribuera en undervisad resultat förklaring liknar att distribuera en modell och innehåller följande steg:




1. Skapa ett förklarings objekt (t. ex. med TabularExplainer):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Skapa en bedömnings förklaring med hjälp av förklarings objekt:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurera och registrera en avbildning som använder bedömnings modellen bedömning.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Valfritt Hämta förklaringen från molnet och testa förklaringarna

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Distribuera avbildningen till ett beräknings mål:

   1. Skapa en bedömnings fil (innan det här steget följer du stegen i [Distribuera modeller med Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) för att registrera din ursprungliga förutsägelse modell)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Definiera distributions konfigurationen (den här konfigurationen beror på kraven i din modell. I följande exempel definieras en konfiguration som använder en processor kärna och 1 GB minne.

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Skapa en fil med miljö beroenden

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Skapa en anpassad Dockerfile med g + + installerat

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Distribuera den skapade avbildningen (tids uppskattning: 5 minuter)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. Testa distributionen

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Rensa: Ta bort en distribuerad webbtjänst genom att använda `service.delete()`.




## <a name="next-steps"></a>Nästa steg

Om du vill se en samling av Jupyter-anteckningsböcker som visar anvisningarna ovan går du till [exempel antecknings böckerna för Azure Machine Learning tolkning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
