---
title: Modell tolkning i automatiserad maskin inlärning
titleSuffix: Azure Machine Learning
description: Lär dig hur du får förklaringar för hur din automatiserade ML-modell fastställer funktions prioritet och gör förutsägelser när du använder Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a0d805d6ae9b129443a2850e0741d5da87feac84
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75539611"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>Modell tolkning i automatiserad maskin inlärning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du aktiverar tolknings funktionerna för automatisk Machine Learning (ML) i Azure Machine Learning. Med automatisk ML får du hjälp att förstå både rå och utformad funktions prioritet. För att kunna använda modell tolkning kan du ange `model_explainability=True` i `AutoMLConfig`-objektet.  

I den här artikeln kan du se hur du:

- Gör tolkningen under utbildningen för bästa modell eller modell.
- Aktivera visualiseringar så att du kan se mönster i data och förklaringar.
- Implementera tolkning under härledning eller poängsättning.

## <a name="prerequisites"></a>Krav

- Tolknings funktioner. Kör `pip install azureml-interpret azureml-contrib-interpret` för att hämta de nödvändiga paketen.
- Kunskap om att skapa automatiserade ML-experiment. Mer information om hur du använder Azure Machine Learning SDK finns i [själv studie kursen om regressions modell](tutorial-auto-train-models.md) eller hur du [konfigurerar automatiserade ml-experiment](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Tolkning under utbildning för bästa modell

Hämta förklaringen från `best_run`, som innehåller förklaringar för funktioner som har funktioner och RAW-funktioner.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Hämtnings bar funktions prioritet från artefakt arkivet

Du kan använda `ExplanationClient` för att hämta de förklarade funktions förklaringarna från artefakt lagret för `best_run`. För att få en förklaring till de råa funktioner som anges `raw=True`.

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Tolkning under utbildning för vilken modell som helst 

När du beräknar modell förklaringar och visualiserar dem är du inte begränsad till en befintlig modell förklaring för en automatiserad ML-modell. Du kan också få en förklaring till din modell med olika test data. Stegen i det här avsnittet visar hur du beräknar och visualiserar den kapacitet som är utformad och den råa funktions betydelsen baseras på dina test data.

### <a name="retrieve-any-other-automl-model-from-training"></a>Hämta alla andra AutoML-modeller från utbildning

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="set-up-the-model-explanations"></a>Konfigurera modell förklaringarna

Använd `automl_setup_model_explanations` för att få en förklaring av de funktioner som medföljde och RAW. `fitted_model` kan generera följande objekt:

- Aktuella data från utbildade eller test exempel
- Namn listor för tekniker och RAW-funktioner
- Klasser som går att hitta i kolumnen med etiketter i klassificerings scenarier

`automl_explainer_setup_obj` innehåller alla strukturer från listan ovan.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initiera utredaren för härma för funktions prioritet

Om du vill generera en förklaring för AutoML-modeller använder du klassen `MimicWrapper`. Du kan initiera MimicWrapper med följande parametrar:

- Installations objekt för förklaring
- Din arbets yta
- En LightGBM-modell som fungerar som ett surrogat till den `fitted_model` automatiserade ML-modellen

MimicWrapper tar också `automl_run`-objektet där de råa och de utformade förklaringarna kommer att överföras.

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

Du kan anropa metoden `explain()` i MimicWrapper med de transformerade test exemplen för att få funktions vikten för de genererade funktionerna. Du kan också använda `ExplanationDashboard` för att visa instrument panelens visualisering av funktions prioritets värden för de genererade funktionerna genom automatisk ML-featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Använd efterlikna förklaringar för beräkning och visualisering av rå funktions prioritet

Du kan anropa metoden `explain()` i MimicWrapper igen med de transformerade test exemplen och ange `get_raw=True` för att få funktions vikten för RAW-funktionerna. Du kan också använda `ExplanationDashboard` för att visa instrument panelens visualisering av funktions prioritets värden för RAW-funktionerna.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Tolkning under härledning

I det här avsnittet får du lära dig hur du operationalisera en automatiserad ML-modell med den förklaring som användes för att beräkna förklaringarna i föregående avsnitt.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrera modellen och bedömnings förklaringen

Använd `TreeScoringExplainer` för att skapa bedömnings förklaringen som beräknar den råa och skapade funktions prioritets värden vid fördröjning. Du initierar bedömnings förklaringen med `feature_map` som har beräknats tidigare. Bedömnings förklararen använder `feature_map` för att returnera den råa funktions prioriteten.

Spara bedömnings förklararen och registrera sedan modellen och bedömnings förklaringen med Modellhantering tjänsten. Kör följande kod:

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

Skapa sedan de nödvändiga miljö beroendena i behållaren för den distribuerade modellen. Observera att azureml-defaults med version > = 1.0.45 måste anges som ett pip-beroende, eftersom det innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst.

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
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Härledning med test data

En härledning med vissa test data för att se det förväntade värdet från den automatiserade ML-modellen. Visa den funktioner som är utformad för det förväntade värdet och den råa funktions betydelsen för det förväntade värdet.

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

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualisera för att identifiera mönster i data och förklaringar i utbildnings tid

Du kan visualisera funktions prioritets diagrammet i din arbets yta i [Azure Machine Learning Studio](https://ml.azure.com). När den automatiserade ML-körningen är klar väljer du **Visa modell information** för att visa en speciell körning. Välj fliken **förklaringar** om du vill visa instrument panelen förklarings visualisering.

[Arkitektur för ![Machine Learning tolken](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan aktivera modell förklaringar och funktions prioritet i områden i Azure Machine Learning SDK förutom Automatisk maskin inlärning finns i avsnittet [begrepp om tolkning](how-to-machine-learning-interpretability.md).
