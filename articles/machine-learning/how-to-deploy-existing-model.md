---
title: Använda och distribuera befintliga modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kan använda Azure Machine Learning med modeller som har tränats utanför tjänsten. Du kan registrera modeller som skapats utanför Azure Machine Learning och sedan distribuera dem som en webbtjänst eller Azure IoT Edge-modul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472383"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Använda en befintlig modell med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Lär dig hur du använder en befintlig maskininlärningsmodell med Azure Machine Learning.

Om du har en maskininlärningsmodell som har tränats utanför Azure Machine Learning kan du fortfarande använda tjänsten för att distribuera modellen som en webbtjänst eller till en IoT Edge-enhet. 

> [!TIP]
> Den här artikeln innehåller grundläggande information om hur du registrerar och distribuerar en befintlig modell. När Azure Machine Learning har distribuerats tillhandahåller du övervakning för din modell. Det låter dig också lagra indata som skickas till distributionen, som kan användas för datadrift analys eller utbildning nya versioner av modellen.
>
> Mer information om de begrepp och termer som används här finns i [Hantera, distribuera och övervaka maskininlärningsmodeller](concept-model-management-and-deployment.md).
>
> Allmän information om distributionsprocessen finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Krav

* En Azure Machine Learning-arbetsyta. Mer information finns i [Skapa en arbetsyta](how-to-manage-workspace.md).

    > [!TIP]
    > Python-exemplen i den `ws` här artikeln förutsätter att variabeln är inställd på din Azure Machine Learning-arbetsyta.
    >
    > CLI-exemplen använder en `myworkspace` `myresourcegroup`platshållare för och . Ersätt dessa med namnet på arbetsytan och resursgruppen som innehåller den.

* [Sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)för Azure Machine Learning .  

* [Azure CLI-](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och [Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md).

* En utbildad modell. Modellen måste sparas i en eller flera filer i utvecklingsmiljön.

    > [!NOTE]
    > Om du vill demonstrera registrering av en modell som är tränad utanför Azure Machine Learning använder exempelkodavsnitten i [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)den här artikeln de modeller som skapats av Paolo Ripamontis Twitter-sentimentanalysprojekt: .

## <a name="register-the-models"></a>Registrera modell(er)

Genom att registrera en modell kan du lagra, version och spåra metadata om modeller på arbetsytan. I följande Python- och `models` CLI-exempel `model.h5`innehåller `model.w2v` `encoder.pkl`katalogen `tokenizer.pkl` , , och filerna. I det här exemplet överförs `models` filerna i katalogen `sentiment`som en ny modellregistrering med namnet :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Mer information finns i referensen [Model.register().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Du kan också `tags` `properties` ange lägg till och ordlista objekt till den registrerade modellen. Dessa värden kan användas senare för att identifiera en viss modell. Till exempel den ram som används, utbildningsparametrar, etc.

Mer information finns i az [ml-modellregisterreferensen.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)


Mer information om modellregistrering i allmänhet finns i [Hantera, distribuera och övervaka maskininlärningsmodeller](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Definiera inferenskonfiguration

Inferenskonfigurationen definierar den miljö som används för att köra den distribuerade modellen. Inferenskonfigurationen refererar till följande entiteter, som används för att köra modellen när den distribueras:

* Ett startskript. Den här `score.py`filen (med namnet) läser in modellen när den distribuerade tjänsten startar. Den ansvarar också för att ta emot data, skicka den till modellen och sedan returnera ett svar.
* En Azure Machine [Learning-miljö](how-to-use-environments.md). En miljö definierar de programvaruberoenden som behövs för att köra modell- och inmatningsskriptet.

I följande exempel visas hur du använder SDK för att skapa en miljö och sedan använda den med en slutledningskonfiguration:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Mer information finns i följande artiklar:

+ [Så här använder du miljöer](how-to-use-environments.md).
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) referens.


CLI läser in inferenskonfigurationen från en YAML-fil:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Med CLI definieras conda-miljön i `myenv.yml` filen som refereras av inferenskonfigurationen. Följande YAML är innehållet i den här filen:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Mer information om inferenskonfiguration finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Inmatningsskript

Postskriptet har bara två `init()` `run(data)`nödvändiga funktioner och . Dessa funktioner används för att initiera tjänsten vid start och köra modellen med hjälp av begärandedata som skickas in av en klient. Resten av skriptet hanterar inläsning och körning av modeller.

> [!IMPORTANT]
> Det finns inte ett allmänt postskript som fungerar för alla modeller. Det är alltid specifikt för den modell som används. Den måste förstå hur du läser in modellen, det dataformat som modellen förväntar sig och hur du poängar data med hjälp av modellen.

Följande Python-kod är ett`score.py`exempelpostskript ( ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Mer information om inmatningsskript finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiera distribution

[Webservice-paketet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) innehåller de klasser som används för distribution. Den klass du använder avgör var modellen distribueras. Om du till exempel vill distribuera som en webbtjänst på Azure Kubernetes-tjänsten använder du [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) för att skapa distributionskonfigurationen.

Följande Python-kod definierar en distributionskonfiguration för en lokal distribution. Den här konfigurationen distribuerar modellen som en webbtjänst till den lokala datorn.

> [!IMPORTANT]
> En lokal distribution kräver en fungerande installation av [Docker](https://www.docker.com/) på den lokala datorn:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Mer information finns i referensen [LocalWebservice.deploy_configuration().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)

CLI läser in distributionskonfigurationen från en YAML-fil:

```YAML
{
    "computeType": "LOCAL"
}
```

Distribution till ett annat beräkningsmål, till exempel Azure Kubernetes Service i Azure-molnet, är lika enkelt som att ändra distributionskonfigurationen. Mer information finns i [Hur och var du kan distribuera modeller](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Distribuera modellen

I följande exempel läses information `sentiment`in på den registrerade modellen `sentiment`med namnet och distribueras den sedan som en tjänst med namnet . Under distributionen används inferenskonfigurationen och distributionskonfigurationen för att skapa och konfigurera tjänstmiljön:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Mer information finns i referensen [Model.deploy().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Om du vill distribuera modellen från CLI använder du följande kommando. Det här kommandot distribuerar version 1 av den registrerade modellen (`sentiment:1` `inferenceConfig.json` ) `deploymentConfig.json` med hjälp av inferens- och distributionskonfigurationen som lagras i filerna och:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Mer information finns i az [ml-modellens distributionsreferens.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

Mer information om distribution finns i [Hur och var du kan distribuera modeller](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Förbrukning av begäran-svar

Efter distributionen visas poäng-URI:n. Den här URI:n kan användas av klienter för att skicka begäranden till tjänsten. Följande exempel är en grundläggande Python-klient som skickar data till tjänsten och visar svaret:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Mer information om hur du använder den distribuerade tjänsten finns i [Skapa en klient](how-to-consume-web-service.md).

## <a name="next-steps"></a>Nästa steg

* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Så här skapar du en klient för en distribuerad modell](how-to-consume-web-service.md)
