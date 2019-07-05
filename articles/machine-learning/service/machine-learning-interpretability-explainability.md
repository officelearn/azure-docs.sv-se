---
title: Modelltolkning
titleSuffix: Azure Machine Learning service
description: Lär dig hur du förklara varför din modell förutsägelser med hjälp av Azure Machine Learning-SDK. Det kan användas vid utbildning och inferens för att förstå hur din modell förutsägelser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: cba46a277dfce93d0080d8f04a26fd135407de15
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536745"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Modellen interpretability med Azure Machine Learning-tjänsten

I den här artikeln får du lära dig hur att förklara varför förutsägelserna gjorts i din modell med de olika interpretability paket av Azure Machine Learning Python SDK.

Använda klasser och metoder i SDK kan få du:
+ Funktionen vikten värden för både rådata och bakåtkompilerade funktioner
+ Interpretability verkliga datauppsättningar i stor skala, under utbildning och inferens.
+ Interaktiva visualiseringar som hjälper dig i identifiering av mönster i data och förklaringar på utbildning

Under fasen utbildning av utvecklingscykeln använder modellen designers och bedömare interpretability resultatet för en modell för att verifiera hypoteser och skapa förtroende med intressenter.  De även använda insikter om modellen för felsökning, verifierar beteendet för enhetsmodellen matchar deras mål, och att söka efter bias.

I machine learning **funktioner** är datafält som används för att förutsäga en datapunkt för målet. Till exempel för att förutsäga kreditrisker kanske datafält för ålder, kontostorlek och konto ålder används. I det här fallet ålder, kontostorlek och konto ålder är **funktioner**. Funktionen vikten anger hur varje fält påverkas modellens förutsägelser. Till exempel kan ålder kraftigt användas i förutsägelsen medan kontostorlek och ålder inte påverkar prognosens noggrannhet kan förbättras avsevärt. Den här processen kan dataforskare att förklara resulterande förutsägelser så att intressenter har insyn i vilka datapunkter som är viktigast i modellen.

Med dessa verktyg kan du förklara maskininlärningsmodeller **globalt på alla data**, eller **lokalt på en viss datapunkt** med för avancerade tekniker i ett enkelt att använda och skalbart sätt.

Klasserna interpretability görs tillgängliga via flera SDK-paket. Lär dig hur du [installera SDK-paket för Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), det största paket, som innehåller funktioner som stöds av Microsoft.

* `azureml.contrib.explain.model`, preview och experimentella funktioner som du kan prova.

* `azureml.train.automl.automlexplainer` paketet för att tolka automatiserade machine learning-modeller.

> [!IMPORTANT]
> Innehåll i den `contrib` namnområde stöds inte fullt ut. När de experimentella funktionerna blir mogen, flyttas de gradvis till det huvudsakliga namnområdet.

## <a name="how-to-interpret-your-model"></a>Så här tolkar du din modell

Du kan använda interpretability klasser och metoder för att förstå modellens globala beteende eller specifika förutsägelser. Den tidigare versionen är globala förklaring och det senare kallas lokala förklaring.

Metoderna som kan kategoriseras även baserat på om metoden är oberoende av modellen eller specifika modellen. Vissa metoder rikta in vissa typer av modeller. Till exempel gäller Formdatas trädet förklaring endast för trädet-baserade modellen. Vissa metoder behandlar modellen som en svart ruta, till exempel mimic förklaring eller Formdatas kernel förklaring. Den `explain` paketet utnyttjar dessa olika sätt utifrån datauppsättningar, modelltyper och användningsfall.

Utdata är en uppsättning med information om hur en viss modell gör dess prognoser, till exempel:
* Global/lokal relativa funktionen prioritet

* Global/lokal funktion och förutsägelser relation

### <a name="explainers"></a>Explainers

Det finns två uppsättningar explainers: Dirigera Explainers och Meta Explainers i SDK.

__Dirigera explainers__ kommer från integrerade bibliotek. SDK: N omsluter alla explainers så att de exponera ett gemensamt API och utdataformat. Om du är bekvämare direkt med hjälp av dessa explainers kan anropa du dem direkt i stället för med vanliga API och utdataformat. Här följer en lista över de direkta explainers som är tillgängliga i SDK:

* **FORMDATA trädet förklaring**: Formdatas trädet förklaring, som fokuserar på polynom snabb FORMDATA värdet uppskattning av algoritmen time specifika för träd och -ensembler av träd.
* **FORMDATA djup förklaring**: Baserat på förklaring från FORMDATA djup förklaring ”är en snabb approximativ algoritm för FORMDATA värdena i modeller för djup maskininlärning som bygger på en anslutning med DeepLIFT som beskrivs i dokumentet FORMDATA NIPS. TensorFlow-modeller och Keras med TensorFlow-serverdelen som stöds (det finns också preliminär stöd för PyTorch) ”.
* **FORMDATA Kernel förklaring**: Formdatas Kernel förklaring använder ett särskilt viktad lokala linjär regression för att beräkna FORMDATA värden för alla modeller.
* **Efterlikna förklaring**: Mimic förklaring baseras på idén om global surrogate modeller. En global surrogate modell är en tack interpretable modell som har tränats för att beräkna ungefär förutsägelser för en svart ruta modell så noggrant som möjligt. Dataexpert kan tolka surrogate-modellen för att dra slutsatser om modellen svart ruta. Du kan använda någon av följande interpretable modeller som surrogate-modell: LightGBM (LinearExplainableModel), linjär Regression (LinearExplainableModel), Stokastisk brantaste Lutningsmetoden explainable modell (SGDExplainableModel) och beslutsträd (DecisionTreeExplainableModel).


* **Permutation funktionen vikten förklaring**: Permutation funktionen vikten är en teknik som används för att förklara klassificerings- och regressionsmodeller modeller som är inspirerat [Breimans slumpmässiga skogar papper](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (se avsnitt 10). På en hög nivå är hur det fungerar med blandning data en funktion i taget för hela datauppsättningen slumpmässigt och beräkna hur mycket prestandamått intressanta minskar. Större ändringar, de viktigaste är den funktionen.

* **GRÖN förklaring** (`contrib`): Baserat på grön, används grön förklaring enligt modellen-oberoende förklaringar i den senaste lokala interpretable (grön) algoritmen för att skapa lokala surrogate modeller. Till skillnad från de globala surrogate modellerna fokuserar grön på utbildning modeller för lokala surrogate att förklara enskilda förutsägelser.
* **HAN Text förklaring** (`contrib`): HAN Text förklaring använder ett hierarkisk uppmärksamhet nätverk för att hämta modellen förklaringar från textdata för en viss svart ruta text-modell. Vi tränar HAN surrogate på en viss lärare modell förväntade utdata. Vi har lagt till ett fine-tune steg för ett visst dokument för att kunna förbättra förklaringarna efter utbildning globalt över texten Kristi. HAN använder en dubbelriktad RNN med två uppmärksamhet lager för mening och word uppmärksamhet. När DNN har tränats på modellen för lärare och finjusterat på ett visst dokument, kan vi extrahera word importances från uppmärksamhet-lager. Vi har hittat HAN ska exaktare än grön eller FORMDATA för textdata men dyrare i villkoren i utbildning samt tid. Men har vi gjort förbättringar till dess att utbildning genom att ge användaren kan initiera nätverk med GloVe ordinbäddningar, även om det är fortfarande långsamt. Utbildningstid kan förbättras avsevärt genom att köra HAN på en fjärransluten Azure GPU VM. Implementeringen av HAN beskrivs i ”hierarkisk uppmärksamhet nätverk för Dokumentklassificering (Yang et al., 2016)” ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).


__Meta explainers__ automatiskt välja en lämplig direkt förklaring och generera den bästa förklaring information baserat på den angivna modellen och datauppsättningar. Meta-explainers utnyttja alla bibliotek (FORMDATA, grön, avbildningen osv.) som vi har integrerat eller utvecklas. Följande är de meta explainers som är tillgängliga i SDK:

* **Tabular förklaring**: Använda med tabelldatauppsättningar.
* **Text förklaring**: Används med text datauppsättningar.
* **Bild förklaring**: Används med bild datauppsättningar.

Dessutom till meta välja av direkt explainers, meta explainers utveckla ytterligare funktioner ovanpå de underliggande bibliotek och förbättras direkt explainers hastighet och skalbarhet.

För närvarande `TabularExplainer` använder följande logik för att anropa direkt FORMDATA Explainers:

1. Om det är ett träd-baserade modellen gäller FORMDATA `TreeExplainer`, annat
2. Om det är en DNN-modell, tillämpa FORMDATA `DeepExplainer`, annat
3. Behandlar det som en svart ruta modell och tillämpa FORMDATA `KernelExplainer`

Intelligens är inbyggt i `TabularExplainer` kommer allt mer sofistikerade som ytterligare bibliotek är integrerade i SDK: N och vi gå igenom- och nackdelar med varje förklaring.

`TabularExplainer` har även gjort betydande förbättringar för funktionen och prestanda över direkt Explainers:

* **Sammanfattning av datauppsättningen som initieringen**. I fall där hastigheten på förklaring är viktigast vi sammanfatta initieringen datauppsättningen och skapa en liten uppsättning representativa mängder som påskyndar både globala och lokala förklaring.
* **Sampling datauppsättningen utvärdering**. Om användaren skickar i en stor uppsättning utvärdering exempel men inte verkligen behöver dem som ska utvärderas alla, kan parametern sampling anges som SANT för att snabba upp globala förklaring.

Följande diagram visar de aktuella strukturen för direct och meta explainers.

[![Machine Learning-Interpretability arkitektur](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modeller som stöds

Alla modeller som är tränade på datauppsättningar i Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, eller `scipy.sparse.csr_matrix` format som stöds av interpretability `explain` paket med SDK: N.

Förklaring-funktioner innehålla både modeller och pipelines som indata. Om en modell har angetts och modellen måste implementera förutsägelsefunktionen `predict` eller `predict_proba` som överensstämmer med Scikit-konventionen. Om en pipeline (namnet på skriptet pipeline) tillhandahålls förutsätter funktionen förklaring att köra pipeline-skriptet returnerar en förutsägelse. Vi stöder modeller tränas via PyTorch, TensorFlow och Keras deep learning-ramverk.

### <a name="local-and-remote-compute-target"></a>Lokal och fjärransluten beräkningsmål

Den `explain` paketet är utformat för att arbeta med både lokala och fjärranslutna beräkningsmål. Om du kör lokalt SDK-funktioner inte att kontakta Azure-tjänster. Du kan köra förklaring via fjärranslutning på beräkning av Azure Machine Learning och logga in informationen som förklaring på Azure Machine Learning kör historik Services. Rapporter och visualiseringar från en förklaring finns tillgängliga i portalen Azure Machine Learning-arbetsyta för Användaranalys när den här informationen loggas.

## <a name="interpretability-in-training"></a>Interpretability i utbildning

### <a name="train-and-explain-locally"></a>Träna och förklarar lokalt

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

2. Anropa förklaring: Om du vill initiera en förklaring-objekt, måste du skicka din modell och vissa träningsdata till den förklaring konstruktorn. Du kan också välja att skicka i funktionsnamn och utdata klassnamn (om göra klassificering) som ska användas för att göra dina förklaringar och visualiseringar mer informativ. Här är att skapa en instans av en förklaring objekt med hjälp av [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), och [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) lokalt. `TabularExplainer` anropar någon av de tre FORMDATA explainers under (`TreeExplainer`, `DeepExplainer`, eller `KernelExplainer`), och automatiskt välja det passar bäst för ditt användningsområde. Men du kan anropa var och en av dess tre underliggande explainers direkt.

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

3. Hämta funktionen globala vikten värden.

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

4. Hämta lokala funktionen vikten värden: Använd följande funktionsanrop för att förklara en enskild instans eller en grupp av instanser. Observera att PFIExplainer inte har stöd för lokala förklaringar.

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

### <a name="train-and-explain-remotely"></a>Träna och förklarar via en fjärranslutning

Medan du kan träna på olika beräkningsmål som stöds av Azure Machine Learning-tjänsten visas i exempel i det här avsnittet hur du gör detta med hjälp av ett mål för beräkning av Azure Machine Learning.

1. Skapa ett inlärningsskript i en lokal Jupyter-anteckningsbok (till exempel run_explainer.py).

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

2. Följ anvisningarna på [konfigurera beräkningsmål för modellträning](how-to-set-up-training-targets.md#amlcompute) mer information om hur du konfigurerar en Azure Machine Learning Compute som din beräkningsmål och skicka in din utbildning-körning.

3. Hämta förklaring i din lokal Jupyter-anteckningsbok.

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

Använd instrumentpanelen för visualisering för att förstå och tolka din modell:

### <a name="global-visualizations"></a>Global visualiseringar

Följande områden tillhandahåller en global vy av den tränade modellen tillsammans med dess förutsägelser och förklaringar.

|Rita|Beskrivning|
|----|-----------|
|Datautforskning| En översikt över datauppsättningen tillsammans med förutsägelse värden.|
|Global prioritet|Visar de översta K (konfigurerbara K) viktigaste funktionerna globalt. Det här diagrammet är användbart för att förstå global beteendet för den underliggande modellen.|
|Förklaring utforskning|Visar hur en funktion är ansvarig för att göra en ändring i modellens förutsägelser värden (eller sannolikhet för förutsägelse värden). |
|Sammanfattning| Använder en signerad lokala funktion vikten värden över alla datapunkter för att visa fördelningen av varje funktion har på värdet för förutsägelse effekten.|

[![Instrumentpanelen för visualisering globala](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Lokala visualiseringar

Du kan klicka på en enskild datapunkt när som helst av de föregående diagrammen att läsa in lokala funktion vikten området för den angivna datapunkten.

|Rita|Beskrivning|
|----|-----------|
|Lokala prioritet|Visar de översta K (konfigurerbara K) viktigaste funktionerna globalt. Det här diagrammet är användbart för att förstå den underliggande modellen på en viss datapunkt lokala beteende.|
|Perturbation utforskning|Kan du ändra funktionen värden av valda data peka och se hur ändringarna påverkar förutsägelse värde.|
|Enskilda villkorlig förväntan (ICE)| Kan du ändra värdet för en funktion från ett lägsta värde till ett högsta värde vill se hur den datapunkt förutsägelse ändras när en funktion ändras.|

[![Visualisering instrumentpanelen lokala funktion prioritet](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Visualisering instrumentpanelen funktionen Perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Instrumentpanelen för visualisering ICE ritar](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Observera att du behöver du ha widget tillägg på instrumentpanelen för visualisering aktiverat innan du startar om Jupyter kernel.

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
Använd följande kod för att läsa in instrumentpanelen för visualisering:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Rå funktionen omvandlingar

Du kan också skicka din funktion på transfomeringspipeline till förklaring att ta emot förklaringar när det gäller de råa funktionerna innan den transformering (snarare än bakåtkompilerade funktioner). Om du hoppar över det här ger förklaring förklaringar när det gäller bakåtkompilerade funktioner.

Formatet för stöds transformationer är samma som det beskrivs i [sklearn pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). I allmänhet stöds alla transformeringar så länge som de fungerar på en enda kolumn och därför är tydligt en till många. 

Vi förklara raw funktioner genom att antingen använda en `sklearn.compose.ColumnTransformer` eller en lista över anpassade omvandlare tupplar. Cellen nedan används `sklearn.compose.ColumnTransformer`. 

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

Om du vill köra exemplet med listan över monterade omvandlare tupplar, Använd följande kod: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

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

## <a name="interpretability-at-inferencing-time"></a>Interpretability inferensjobb tidpunkt

Förklaring kan distribueras tillsammans med den ursprungliga modellen och kan användas vid bedömning tid för att ange information om lokala förklaring. Vi erbjuder också slimmad bedömnings explainers för att göra interpretability på inferensjobb tid bättre. Processen för att distribuera en slimmad bedömnings förklaring påminner om att distribuera en modell och omfattar följande steg:




1. Skapa ett objekt för förklaring (t.ex. med TabularExplainer):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Skapa en arbetsflödesbaserad förklaring med hjälp av objektet förklaring:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurera och registrera en avbildning som använder bedömningsmodell för förklaring.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. [Valfritt] Hämta bedömnings förklaring från molnet och testa förklaringarna

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

1. Distribuera avbildningen till ett beräkningsmål:

   1. Skapa en bedömningsfilen (innan det här steget, följer du stegen i [distribuera modeller med Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) att registrera din ursprungliga prognosmodell med tidsserie)

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

   1. Definiera distributionskonfigurationen (den här konfigurationen beror på kraven i din modell. Följande exempel definierar en konfiguration som använder en processorkärna och 1 GB minne)

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

   1. Skapa en anpassad docker-fil med g ++ installerat

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Distribuera den skapa avbildningen (Uppskattad tidsåtgång: 5 minuter)

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

## <a name="interpretability-in-automated-ml"></a>Interpretability i automatiserade ML

Automatiserad maskininlärning innehåller paket för att tolka funktionen betydelse i auto-utbildade modeller. Dessutom kan klassificering scenarier du hämta klassnivå funktionen prioritet. Det finns två metoder för att aktivera det här beteendet i automatiserade maskininlärning:

* Om du vill aktivera funktionen betydelse för en tränad ensemble modell, använda den [ `explain_model()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) funktion.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* Om du vill aktivera funktionen vikten för varje enskild innan utbildning, ange den `model_explainability` parameter `True` i den `AutoMLConfig` objekt, tillsammans med tillhandahåller verifieringsdata för. Använd sedan den [ `retrieve_model_explanation()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) funktion.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

Mer information finns i den [how-to](how-to-configure-auto-train.md#explain-the-model-interpretability) om hur du aktiverar interpretability funktioner i automatiserade machine learning.

## <a name="next-steps"></a>Nästa steg

En samling med Jupyter-anteckningsböcker som visar anvisningarna ovan finns i den [exempelanteckningsböcker som Azure Machine Learning Interpretability](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
