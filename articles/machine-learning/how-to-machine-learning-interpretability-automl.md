---
title: Förklaringsförmåga i automatiserad ML
titleSuffix: Azure Machine Learning
description: Lär dig hur du får förklaringar till hur din automatiserade ML-modell avgör funktionens betydelse och gör förutsägelser när du använder Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.date: 03/11/2020
ms.openlocfilehash: e9155104905ae3e686a01b90cbcad2610b6f4c91
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086427"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning"></a>Tolkningsbarhet: modellförklaringar inom automatiserad maskininlärning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du får förklaringar till automatiserad maskininlärning (ML) i Azure Machine Learning. Automatiserad ML hjälper dig att förstå konstruerad funktionsvikt. 

Alla SDK-versioner efter 1.0.85 som `model_explainability=True` standard. I SDK version 1.0.85 och tidigare `model_explainability=True` versioner `AutoMLConfig` måste användare ange objektet för att kunna använda modelltolkbarhet. 

I den här artikeln kan du se hur du:

- Utför tolkningsförmåga under träning för bästa modell eller någon modell.
- Aktivera visualiseringar som hjälper dig att se mönster i data och förklaringar.
- Implementera tolkningsbarhet under slutledning eller bedömning.

## <a name="prerequisites"></a>Krav

- Tolkningsfunktioner. Kör `pip install azureml-interpret azureml-contrib-interpret` för att få de nödvändiga paketen.
- Kunskap om att bygga automatiserade ML experiment. Mer information om hur du använder Azure Machine Learning SDK kan du slutföra den här [självstudiekursen för regressionsmodell](tutorial-auto-train-models.md) eller se hur du [konfigurerar automatiserade ML-experiment](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Tolkningsförmåga under träning för den bästa modellen

Hämta förklaringen från `best_run`, som innehåller förklaringar till konstruerade funktioner.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Hämta konstruerad funktionsbetebetebetydelse från artefaktarkivet

Du kan `ExplanationClient` använda för att hämta de konstruerade funktionsförklaringarna från artefaktarkivet för `best_run`. 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Tolkningsförmåga under utbildning för alla modeller 

När du beräknar modellförklaringar och visualiserar dem är du inte begränsad till en befintlig modellförklaring för en automatiserad ML-modell. Du kan också få en förklaring till din modell med olika testdata. Stegen i det här avsnittet visar hur du beräknar och visualiserar konstruerad funktionsbetens betydelse baserat på dina testdata.

### <a name="retrieve-any-other-automl-model-from-training"></a>Hämta någon annan AutoML-modell från utbildning

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Ställ in modellförklaringarna

Används `automl_setup_model_explanations` för att få konstruerade förklaringar. Kan `fitted_model` generera följande objekt:

- Utvalda data från tränade eller testprover
- Id-funktionsnamnlistor
- Sökbara klasser i den märkta kolumnen i klassificeringsscenarier

Den `automl_explainer_setup_obj` innehåller alla strukturer från ovan lista.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initiera Mimic Explainer för funktionen betydelse

Om du vill generera en förklaring `MimicWrapper` till AutoML-modeller använder du klassen. Du kan initiera MimicWrapper med dessa parametrar:

- Det explainer-inställningsobjektet
- Din arbetsyta
- En LightGBM-modell, som fungerar `fitted_model` som ett surrogat till den automatiserade ML-modellen

MimicWrapper tar också `automl_run` objektet där de konstruerade förklaringarna kommer att laddas upp.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Använd MimicExplainer för dator- och visualisering av konstruerad funktionsvikt

Du kan `explain()` anropa metoden i MimicWrapper med de transformerade testexemplen för att få funktionens betydelse för de genererade konstruerade funktionerna. Du kan `ExplanationDashboard` också använda för att visa visualisering av instrumentpanelen för funktionsavstångsvärdena för de genererade konstruerade funktionerna av automatiserade ML-featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Tolkningsförmåga vid slutledning

I det här avsnittet får du lära dig hur du kan operationalisera en automatiserad ML-modell med explainer som användes för att beräkna förklaringarna i föregående avsnitt.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrera modellen och poängutklararen

Använd `TreeScoringExplainer` för att skapa bedömningsut explainer som beräknar de konstruerade funktionsbetviktsvärdena vid sluten tid. Du initierar poäng explainer `feature_map` med som beräknades tidigare. 

Spara poängut explainern och registrera sedan modellen och poängut explainern med Model Management Service. Kör följande kod:

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Skapa conda-beroenden för att ställa in tjänsten

Skapa sedan nödvändiga miljöberoenden i behållaren för den distribuerade modellen. Observera att azureml-standardvärden med version >= 1.0.45 måste anges som ett pipberoende, eftersom det innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst.

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

Distribuera tjänsten med conda-filen och bedömningsfilen från föregående steg.

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

### <a name="inference-with-test-data"></a>Slutsats med testdata

Dra slutsatsen med vissa testdata för att se det förväntade värdet från automatiserad ML-modell. Visa den konstruerade funktionens betydelse för det förväntade värdet.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualisera för att upptäcka mönster i data och förklaringar vid träningstid

Du kan visualisera funktionsviktsdiagrammet på arbetsytan i [Azure Machine Learning studio](https://ml.azure.com). När den automatiska ML-körningen är klar väljer du **Visa modellinformation** för att visa en viss körning. Välj fliken **Förklaringar** om du vill visa instrumentpanelen för förklaringsvisualisering.

[![Maskininlärningsretolkbarhetsarkitektur](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan aktivera modellförklaringar och funktionsbetydelse inom områden i Azure Machine Learning SDK än automatiserad maskininlärning finns i [konceptartikeln om tolkning](how-to-machine-learning-interpretability.md).
