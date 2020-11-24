---
title: Så här kör och distribuerar du lokalt
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör utbildade modeller på den lokala datorn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: fcea7c162e5c978a7c8ff1b42e09ffe0afb98f3c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550159"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>Distribuera på dina lokala datorer modeller utbildade med Azure Machine Learning

Den här artikeln lär dig hur du använder den lokala datorn som mål för utbildning eller distribution av modeller som skapats i Azure Machine Learning. Azure Machine Learningens flexibilitet gör det möjligt att arbeta med de flesta python-ramverk för maskin inlärning. Maskin inlärnings lösningar har i allmänhet komplexa beroenden som kan vara svåra att duplicera. I den här artikeln får du lära dig hur du förbrukar total kontroll jämfört med enkel användning.

Vissa scenarier för lokal distribution är:

* Snabbt iterera data, skript och modeller tidigt i ett projekt
* Fel sökning och fel sökning i senare steg
* Slutlig distribution på användarens hanterade maskin vara

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning arbets yta](how-to-manage-workspace.md)
- En modell och miljö. Om du inte har en tränad modell kan du använda modell-och beroende filerna som anges i [den här självstudien](tutorial-train-models-with-aml.md)
- [Azure Machine Learning Software Development Kit (SDK) för python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
- En Conda-hanterare, till exempel Anaconda eller miniconda, om du vill spegla Azure Machine Learning paket beroenden
- Docker, om du vill använda en behållar version av Azure Machine Learnings miljön

## <a name="prepare-your-local-machine"></a>Förbered din lokala dator

Det mest pålitliga sättet att köra en Azure Machine Learning-modell är med en Docker-avbildning. En Docker-avbildning ger en isolerad, behållar upplevelse som är dubletter, med undantag för maskin varu problem, Azures körnings miljö. Mer information om hur du installerar och konfigurerar Docker för utvecklings scenarier finns i [Översikt över Docker-fjärrutveckling i Windows](/windows/dev-environment/docker/overview).

Även om det är möjligt att koppla en fel sökare till en process som körs i Docker (se [bifoga till en behållare som körs](https://code.visualstudio.com/docs/remote/attach-container)), kanske du vill felsöka och iterera din python-kod utan Docker. I det här scenariot är det viktigt att den lokala datorn använder samma bibliotek som används när du kör experimentet i Azure Machine Learning. Azure använder [Conda](https://docs.conda.io/)för att hantera python-beroenden. Även om du kan skapa om miljön med andra paket hanterare, är det enklaste sättet att synkronisera genom att installera och konfigurera Conda på din lokala dator. 

## <a name="prepare-your-entry-script"></a>Förbereda ditt inläggs skript

Även om du använder Docker för att hantera modellen och beroenden måste python-bedömnings skriptet vara lokalt. Skriptet måste ha två metoder:

- En `init()` metod som inte tar några argument och returnerar ingenting 
- En `run()` metod som tar en JSON-formaterad sträng och returnerar ett JSON-serialiserbar objekt.

Argumentet till- `run()` metoden kommer att ha formatet: 

```json
{
    "data": <model-specific-data-structure>
}
```

Objektet du returnerar från- `run()` metoden måste implementera `toJSON() -> string` .

I följande exempel visas hur du läser in en registrerad scikit-läre modell och visar den med numpy-data. Det här exemplet baseras på modellen och beroenden i [den här självstudien](tutorial-train-models-with-aml.md):

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

Mer avancerade exempel, inklusive automatisk Swagger av scheman och hur du visar binära data (t. ex. bilder) data, finns i [Avancerad post skript redigering](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-using-docker"></a>Distribuera som en lokal webb tjänst med Docker

Det enklaste sättet att replikera miljön som används av Azure Machine Learning är att distribuera en webb tjänst med hjälp av Docker. När Docker körs på den lokala datorn kommer du att:

1. Anslut till Azure Machine Learning arbets ytan där din modell är registrerad
1. Skapa ett `Model` objekt som representerar modellen
1. Skapa ett `Environment` objekt som innehåller beroenden och definierar i vilken program miljö koden ska köras
1. Skapa ett `InferenceConfig` objekt som associerar start skriptet och `Environment`
1. Skapa ett `DeploymentConfiguration` objekt av underklassen `LocalWebserviceDeploymentConfiguration`
1. Används `Model.deploy()` för att skapa ett `Webservice` objekt. Den här metoden hämtar Docker-avbildningen och associerar den med `Model` , `InferenceConfig` , och `DeploymentConfiguration`
1. Aktivera `Webservice` with `Webservice.wait_for_deployment()`

Följande kod visar de här stegen:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

Anropet till `Model.deploy()` kan ta några minuter. När du har distribuerat, är det mer effektivt att använda `update()` metoden i stället för att börja från början. Se [Uppdatera en distribuerad webb tjänst](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Testa din lokala distribution

När du kör det tidigare distributions skriptet, kommer det att mata ut den URI som du kan använda för att skicka data till poäng (till exempel `http://localhost:6789/score` ). I följande exempel visas ett skript som visar exempel data med den `"sklearn-mnist-local"` lokalt distribuerade modellen. Modellen, om den är korrekt utbildad, härleds som `normalized_pixel_values` ska tolkas som en "2". 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Hämta och kör din modell direkt

När du använder Docker för att distribuera din modell som en webb tjänst är det vanligaste alternativet, kanske du också vill köra koden direkt med lokala Python-skript. Det finns två viktiga element som du behöver: 

- Själva modellen
- Beroenden som modellen använder på 

Nedladdning av modellen kan göras:  

- Från portalen, genom att välja fliken **modeller** , välja önskad modell och på **informations** sidan, väljer du **Hämta**
- Från kommando raden, genom att använda `az ml model download` (se [referens för modell hämtning](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false))
- Med python SDK, genom att använda `Model.download()` -metoden (se [modell-API-referensen](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false))

En Azure-modell är ett eller flera serialiserade python-objekt, paketerade som en python Pickle-fil (**. PKL** -tillägg). Innehållet i Pickle-filen beror på det ML-bibliotek eller den teknik som används för att träna modellen. Med modellen från självstudien kan du till exempel läsa in modellen med:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Beroenden är alltid knepigt att komma åt precis, särskilt med Machine Learning, där det ofta kan finnas en Dizzying-webbplats med särskilda versions krav. Du kan återskapa en Azure Machine Learning miljö på den lokala datorn antingen som en fullständig Conda-miljö eller som en Docker-avbildning med hjälp av `Environment` klassens `build_local()` metod. 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda env
```

Om du anger `build_local()` argumentet `useDocker` till `True` , skapar funktionen en Docker-avbildning i stället för en Conda-miljö. Om du vill ha mer kontroll kan du använda `Environment` `save_to_directory()` metoden, som skriver **conda_dependencies. yml** och **azureml_environment.jspå** definitionsfiler som du kan finjustera och använda som grund för tillägg. 

`Environment`Klassen har ett antal andra metoder för att synkronisera miljöer över din beräknings maskin vara, din Azure-arbetsyta och Docker-avbildningar. Mer information finns i API- [ `Environment` referensen](/python/api/azureml-core/azureml.core.environment(class)).

När du har laddat ned modellen och löst dess beroenden finns det inga Azure-definierade begränsningar för hur du utför en bedömning, finjusterar modellen, använder överförings inlärning och så vidare. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Överför en omtränad modell till Azure Machine Learning

Om du har en lokalt utbildad eller retränad modell kan du registrera den med Azure. När du har registrerat dig kan du fortsätta att justera det med Azure Compute eller distribuera det med hjälp av Azure-tjänster som [Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md) eller [Triton-(för hands version)](how-to-deploy-with-triton.md).

För att kunna användas med Azure Machine Learnings python SDK måste en modell lagras som ett serialiserat python-objekt i Pickle-format (en **PKL** -fil) och måste implementera en `predict(data)` metod som returnerar ett JSON-serialiserbar objekt. Du kan till exempel lagra en lokalt utbildad scikit-läre diabetes-modell med: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Om du vill göra modellen tillgänglig i Azure kan du använda- `register()` metoden i- `Model` klassen:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Sedan kan du hitta din nyligen registrerade modell i Azure Machine Learning **modell** -fliken:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Skärm bild av fliken Azure Machine Learning modell som visar den uppladdade modellen.":::

Mer information om hur du laddar upp och uppdaterar modeller och miljöer finns i [Registrera modell och distribuera lokalt med avancerade användningar](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du hanterar miljöer finns [i skapa & använda program varu miljöer i Azure Machine Learning](how-to-use-environments.md)
- Information om hur du kommer åt data från data lagret finns i [ansluta till lagrings tjänster på Azure](how-to-access-data.md)