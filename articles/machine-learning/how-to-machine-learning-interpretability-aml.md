---
title: Förklara maskininlärningsmodeller och förutsägelser
titleSuffix: Azure Machine Learning
description: Lär dig hur du får förklaringar till hur din maskininlärningsmodell avgör funktionens betydelse och gör förutsägelser när du använder Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/12/2020
ms.openlocfilehash: c1282ed16c9e3b92e7d5ec3f9969bee6fc3d917f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257213"
---
# <a name="explain-machine-learning-models-and-predictions"></a>Förklara maskininlärningsmodeller och förutsägelser

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här programguiden lär du dig att använda tolkningspaketet i Azure Machine Learning Python SDK för att utföra följande uppgifter:


* Förklara hela modellen beteende eller enskilda förutsägelser på din personliga dator lokalt.

* Aktivera tolkningstekniker för konstruerade funktioner.

* Förklara beteendet för hela modellen och enskilda förutsägelser i Azure.

* Använd en instrumentpanel för visualisering för att interagera med dina modellförklaringar.

* Distribuera en bedömningsut explainer tillsammans med din modell för att observera förklaringar under inferencing.



Mer information om tolkningstekniker och maskininlärningsmodeller som stöds finns [i Standardtolkbarhet i Azure Machine Learning](how-to-machine-learning-interpretability.md) och exempel [anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Generera prioritetsvärde för funktioner på din personliga dator 
I följande exempel visas hur du använder tolkningspaketet på din personliga dator utan att kontakta Azure-tjänster.

1. Installera `azureml-interpret` `azureml-interpret-contrib` och paket.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```

2. Träna en provmodell i en lokal Jupyter-anteckningsbok.

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

3. Ring förklararen lokalt.
   * Om du vill initiera ett explainer-objekt skickar du modellen och vissa träningsdata till explainerns konstruktor.
   * Om du vill göra dina förklaringar och visualiseringar mer informativa kan du välja att skicka in funktionsnamn och utdataklassnamn om du gör klassificering.

   Följande kodblock visar hur du instansierar `TabularExplainer`ett `MimicExplainer`explainer-objekt med , och `PFIExplainer` lokalt.
   * `TabularExplainer`kallar en av de tre SHAP-explainers under (`TreeExplainer`, `DeepExplainer`eller `KernelExplainer`).
   * `TabularExplainer`väljer automatiskt den lämpligaste för ditt användningsfall, men du kan ringa var och en av dess tre underliggande explainers direkt.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    eller

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

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
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Förklara hela modellen beteende (global förklaring) 

Se följande exempel för att hjälpa dig att få de sammanlagda (globala) funktionsviktvärdena.

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Förklara en individuell förutsägelse (lokal förklaring)
Hämta de enskilda funktionsviktvärdena för olika datapunkter genom att anropa förklaringar för en enskild instans eller en grupp instanser.
> [!NOTE]
> `PFIExplainer`stöder inte lokala förklaringar.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Omvandlingar av funktioner för råa funktioner

Du kan välja att få förklaringar i form av råa, oöversatta funktioner snarare än konstruerade funktioner. För det här alternativet skickar du din pipeline `train_explain.py`för funktionsomvandling till explainern i . Annars ger explainer förklaringar när det gäller konstruerade funktioner.

Formatet på omvandlingar som stöds är detsamma som beskrivs i [sklearn-pandor](https://github.com/scikit-learn-contrib/sklearn-pandas). I allmänhet stöds alla omvandlingar så länge de fungerar på en enda kolumn så att det är tydligt att de är en-till-många.

Få en förklaring till råa funktioner med hjälp av en `sklearn.compose.ColumnTransformer` eller med en lista över monterade transformatortuppningar. I följande `sklearn.compose.ColumnTransformer`exempel används .

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


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Om du vill köra exemplet med listan över monterade transformatortupplar, använd följande kod:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generera värden för funktionsavstÃ¤nning via fjärrkörningar

I följande exempel visas hur `ExplanationClient` du kan använda klassen för att aktivera modelltolkbarhet för fjärrkörningar. Det är begreppsmässigt liknar den lokala processen, förutom du:

* Använd `ExplanationClient` i fjärrkörningen för att ladda upp tolkningskontexten.
* Hämta kontexten senare i en lokal miljö.

1. Installera `azureml-interpret` `azureml-interpret-contrib` och paket.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```
1. Skapa ett utbildningsskript i en lokal Jupyter-anteckningsbok. Till exempel `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

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

1. Konfigurera en Azure Machine Learning Compute som beräkningsmål och skicka in din träningskörning. Se [ställa in beräkningsmål för modellutbildning](how-to-set-up-training-targets.md#amlcompute) för instruktioner. Du kan också hitta [exempel anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) till hjälp.

1. Ladda ner förklaringen i din lokala Jupyter anteckningsbok.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
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

När du har hämtat förklaringarna i den lokala Jupyter-anteckningsboken kan du använda instrumentpanelen för visualisering för att förstå och tolka din modell.

### <a name="understand-entire-model-behavior-global-explanation"></a>Förstå hela modellens beteende (global förklaring) 

Följande tomter ger en helhetsbild av den utbildade modellen tillsammans med dess förutsägelser och förklaringar.

|Tomt|Beskrivning|
|----|-----------|
|Data Exploration| Visar en översikt över datauppsättningen tillsammans med förutsägelsevärden.|
|Global betydelse|Aggregat har viktiga värden för enskilda datapunkter för att visa modellens övergripande topp K (konfigurerbara K) viktiga funktioner. Hjälper till att förstå underliggande modellens övergripande beteende.|
|Förklaring Exploration|Visar hur en funktion påverkar en ändring av modellens förutsägelsevärden eller sannolikheten för förutsägelsevärden. Visar effekten av funktionsinteraktion.|
|Sammanfattning betydelse|Använder enskilda prioritetsvärden för funktioner över alla datapunkter för att visa fördelningen av varje funktions inverkan på förutsägelsevärdet. Med hjälp av det här diagrammet undersöker du i vilken riktning funktionsvärdena påverkar förutsägelsevärdena.
|

[![Instrumentpanel för visualisering globalt](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Förstå enskilda förutsägelser (lokal förklaring) 

Du kan läsa in det enskilda funktionsviktsdiagrammet för alla datapunkter genom att klicka på någon av de enskilda datapunkterna i något av de övergripande diagrammen.

|Tomt|Beskrivning|
|----|-----------|
|Lokal betydelse|Visar de viktigaste funktionerna i K (konfigurerbara K) för en individuell förutsägelse. Hjälper till att illustrera det lokala beteendet hos den underliggande modellen på en viss datapunkt.|
|Störning Exploration (tänk om analys)|Tillåter ändringar av funktionsvärden för den valda datapunkten och observera resulterande ändringar i förutsägelsevärdet.|
|Individuella villkorliga förväntningar (ICE)| Tillåter funktionsvärdeändringar från ett minimivärde till ett högsta värde. Hjälper till att illustrera hur datapunktens förutsägelse ändras när en funktion ändras.|

[![Lokal funktionsvikt för visualiseringsinstrumentpanel](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Funktionsövermring av visualiseringsinstrumentpanel](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![ICE-diagram för visualisering](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Innan Jupyter-kärnan startar kontrollerar du att du aktiverar widgettillägg för instrumentpanelen för visualisering.

* Jupyter Notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab (på sätt och vir)

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Om du vill läsa in instrumentpanelen för visualisering använder du följande kod.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisering i Azure Machine Learning studio

Om du slutför stegen [för fjärrtolkning](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (överför genererad förklaring till Azure Machine Learning Run History) kan du visa visualiseringsinstrumentpanelen i [Azure Machine Learning studio](https://ml.azure.com). Den här instrumentpanelen är en enklare version av instrumentpanelen för visualisering som förklaras ovan (förklaringsutforskning och ICE-diagram är inaktiverade eftersom det inte finns någon aktiv beräkning i studion som kan utföra sina beräkningar i realtid).

Om datauppsättningen, globala och lokala förklaringar är tillgängliga fylls alla flikar i data (förutom Perturbation Exploration och ICE). Om det bara finns en global förklaring inaktiveras fliken Sammanfattningsbetens och alla lokala förklaringsflikar.

Följ en av dessa sökvägar för att komma åt instrumentpanelen för visualisering i Azure Machine Learning studio:

* **Fönstret Experiment** (förhandsgranskning)
  1. Välj **Experiment** i den vänstra rutan om du vill visa en lista över experiment som du har kört på Azure Machine Learning.
  1. Välj ett visst experiment om du vill visa alla körningar i experimentet.
  1. Välj en körning och sedan fliken **Förklaringar** till instrumentpanelen för förklaringsvisualisering.

   [![Lokal funktionsvikt för visualiseringsinstrumentpanel](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Fönstret Modeller**
  1. Om du har registrerat den ursprungliga modellen genom att följa stegen i [Distribuera modeller med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)kan du välja **Modeller** i den vänstra rutan för att visa den.
  1. Välj en modell och sedan fliken **Förklaringar** för att visa instrumentpanelen för förklaringsvisualisering.

## <a name="interpretability-at-inference-time"></a>Tolkningsbarhet vid slutledningstid

Du kan distribuera explainern tillsammans med den ursprungliga modellen och använda den vid sluten tid för att ge de enskilda funktionsviktvärdena (lokal förklaring) för nya nya datapunkter. Vi erbjuder också lättare poängbeklarare för att förbättra tolkningsförmågan vid sluten tid. Processen att distribuera en lägre poängbeklarare liknar distribuera en modell och innehåller följande steg:

1. Skapa ett förklaringsobjekt. Du kan till `TabularExplainer`exempel använda:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Skapa en bedömningsförklaring med förklaringsobjektet.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurera och registrera en bild som använder bedömningsförklarningsmodellen.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Som ett valfritt steg kan du hämta bedömningsförklaringen från molnet och testa förklaringarna.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Distribuera avbildningen till ett beräkningsmål genom att följa följande steg:

   1. Om det behövs registrerar du den ursprungliga förutsägelsemodellen genom att följa stegen i [Distribuera modeller med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

   1. Skapa en bedömningsfil.

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
   1. Definiera distributionskonfigurationen.

         Den här konfigurationen beror på kraven för din modell. I följande exempel definieras en konfiguration som använder en CPU-kärna och en GB minne.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Skapa en fil med miljöberoenden.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Skapa en anpassad dockerfil med g++ installerat.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Distribuera den skapade avbildningen.
   
         Denna process tar cirka fem minuter.

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

1. Testa distributionen.

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

1. Städa upp.

   Om du vill ta `service.delete()`bort en distribuerad webbtjänst använder du .

## <a name="next-steps"></a>Nästa steg

[Läs mer om modelltolkbarhet](how-to-machine-learning-interpretability.md)

[Kolla in azure machine learning-tolkningsformulär anteckningsböcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

