---
title: Modell tolkning i automatiserad ML
titleSuffix: Azure Machine Learning
description: Lär dig hur du kan förklara varför din automatiserade ML-modell gör förutsägelser med hjälp av Azure Machine Learning SDK. Den kan användas under utbildning och härledning för att förstå hur din modell fastställer funktions prioritet och gör förutsägelser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515335"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Modell tolkning för automatiserade ML-modeller

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här instruktionen får du lära dig hur du aktiverar tolknings funktioner i automatiserad maskin inlärning med Azure Machine Learning-tjänsten. Med automatisk ML kan du förstå både rå och bevarad funktions prioritet. För att kunna använda modell tolkning kan du ange `model_explainability=True` i `AutoMLConfig`-objektet.  

I den här artikeln får du lära dig följande uppgifter:

* Tolkning under utbildning för bästa modell eller vilken modell som helst
* Aktivera visualiseringar för att hjälpa dig att identifiera mönster i data och förklaringar
* Tolkning under härledning eller Poängsättning

## <a name="prerequisites"></a>Förutsättningar

* Kör `pip install azureml-interpret azureml-contrib-interpret` för att få de nödvändiga paketen för tolknings funktioner.
* Den här artikeln förutsätter kunskaper om att skapa automatiserade ML-experiment. Se [självstudien](tutorial-auto-train-models.md) eller [anvisningar](how-to-configure-auto-train.md) för att lära dig hur du använder automatiserad ml i SDK.
 
## <a name="interpretability-during-training-for-the-best-model"></a>Tolkning under utbildning för bästa modell 

Hämta förklaringen från `best_run`, som innehåller förklaringar för funktioner som har funktioner och RAW-funktioner. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Hämtnings bar funktions prioritet från artefakt arkivet

Du kan använda `ExplanationClient` för att ladda ned de utformade funktions förklaringarna från artefakt arkivet i best_run. För att få en förklaring till de råa funktioner som anges `raw=True`. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Tolkning under utbildning för vilken modell som helst 

I det här avsnittet får du lära dig hur du beräknar modell förklaringar och visualiserar förklaringarna. Förutom att hämta en befintlig modell förklaring för en automatiserad ML-modell kan du också förklara din modell med olika test data. Med hjälp av följande steg kan du beräkna och visualisera funktions vikten och den råa funktions betydelsen utifrån dina test data.

### <a name="retrieve-any-other-automl-model-from-training"></a>Hämta alla andra AutoML-modeller från utbildning

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>Konfigurera modell förklaringarna

Fitted_model kan generera följande objekt, som kommer att användas för att hämta de funktioner som är utformade för utvecklare och rå data med automl_setup_model_explanations:

* Bearbetas data från träna prover/test-exempel
* Samla in de utformade och råa funktions namn listorna
* Hitta klasserna i kolumnen med etiketter i klassificerings scenarier

Automl_explainer_setup_obj innehåller alla strukturer från listan ovan.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initiera utredaren för härma för funktions prioritet

För att förklara AutoML-modellerna använder du klassen `MimicWrapper`. MimicWrapper kan initieras med parametrar för välklarande setup-objektet, din arbets yta och en LightGBM-modell som fungerar som en surrogat modell för att förklara den automatiserade ML-modellen (fitted_model här). MimicWrapper tar också automl_run-objektet där de råa och de utformade förklaringarna överförs.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Använda MimicExplainer för beräkning och visualisering av utformad funktions prioritet

Metoden förklara () i MimicWrapper kan anropas med de transformerade test exemplen för att få funktions vikten för de genererade funktionerna. Du kan också använda ExplanationDashboard för att Visa streck korts visualiseringen för funktions prioritets värden för de genererade funktionerna genom automatisk ML featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Använd efterlikna förklaringar för beräkning och visualisering av rå funktions prioritet

Metoden förklara () i MimicWrapper kan anropas igen med de transformerade test exemplen och ange `get_raw` till sant för att få funktions vikten för RAW-funktionerna. Du kan också använda ExplanationDashboard för att visa instrument panelens visualisering av funktions prioritets värden för RAW-funktionerna.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Tolkning under härledning

I det här avsnittet får du lära dig hur du operationalisera en automatiserad ML-modell med förklaring, som användes för att beräkna förklaringarna i föregående avsnitt.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrera modellen och bedömnings förklaringen

Använd `TreeScoringExplainer` för att skapa bedömnings förklaringen, som kommer att användas för att beräkna de råa och skapade prioritets värdena för funktioner vid fördröjning. Observera att du initierar bedömnings förklaringen med den feature_map som har beräknats tidigare. Feature_map kommer att användas av bedömnings förklaringen för att returnera den råa funktions betydelsen.

I koden nedan sparar du bedömnings förklaringen och registrerar modellen och bedömnings förklaringen med Modellhantering tjänsten.

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Skapa Conda-beroenden för att konfigurera tjänsten

Därefter skapar du de miljö beroenden som behövs i behållaren för den distribuerade modellen.

```python
from azureml.core.conda_dependencies import CondaDependencies 

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Distribuera tjänsten

Distribuera tjänsten med hjälp av Conda-filen och bedömnings filen från föregående steg.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>Härledning med hjälp av test data

Använd vissa test data för att se det förväntade värdet från den automatiserade ML-modellen och visa den funktioner som är utformad för det förväntade värdet och den råa funktions betydelsen för det förväntade värdet.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Visualiseringar som hjälper dig att upptäcka mönster i data och förklaringar i utbildnings tid

Du kan också visualisera funktions prioritets diagrammet i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com). När den automatiserade ML-körningen är klar måste du klicka på Visa modell information, vilket leder till en speciell körning. Härifrån kan du klicka på fliken förklaringar för att se instrument panelen för förklarings visualisering. 

[Arkitektur för ![Machine Learning tolken](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Mer information om hur modell förklaringar och funktions prioritet kan aktive ras på andra områden i SDK utanför Automatisk maskin inlärning finns i avsnittet [begrepp](how-to-machine-learning-interpretability.md) om tolkning.