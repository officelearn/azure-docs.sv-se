---
title: Hur du använder en befintlig modell
titleSuffix: Azure Machine Learning service
description: Lär dig hur du kan använda Azure Machine Learning-tjänsten med modeller som har tränats utanför tjänsten. Du kan registrera modeller som skapats utanför Azure Machine Learning-tjänsten och sedan distribuera dem som en webbtjänst eller Azure IoT Edge-modul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453689"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Hur du använder en befintlig modell med Azure Machine Learning-tjänsten

Lär dig hur du använder en befintlig maskininlärningsmodell med Azure Machine Learning-tjänsten.

Om du har en maskininlärningsmodell som har tränats utanför Azure Machine Learning-tjänsten kan använda du fortfarande tjänsten för att distribuera modellen som en webbtjänst eller till en IoT Edge-enhet. 

> [!TIP]
> Den här artikeln innehåller grundläggande information om registrering och distribution av en befintlig modell. När distribuerats tillhandahåller Azure Machine Learning-tjänsten övervakning för din modell. Du kan också att lagra indata som skickas till distributionen, som kan användas för data drift analys eller finjustera nya versioner av modellen.
>
> Mer information om begrepp och termer som används här finns [hantera, distribuera och övervaka maskininlärningsmodeller](concept-model-management-and-deployment.md).
>
> Allmän information om distributionsprocessen finns i [distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Förutsättningar

* En arbetsyta för Azure Machine Learning-tjänsten. Mer information finns i [skapa en arbetsyta](setup-create-workspace.md).

    > [!TIP]
    > Python-exempel i den här artikeln förutsätts att den `ws` variabeln anges till din arbetsyta för Azure Machine Learning-tjänsten.
    >
    > CLI-exempel används en platshållare för `myworkspace` och `myresourcegroup`. Ersätt dem med namnet på din arbetsyta och resursgruppen som innehåller den.

* Azure Machine Learning SDK. Mer information finns i avsnittet Python SDK i [skapa en arbetsyta](setup-create-workspace.md#sdk).

* Den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och [Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md).

* En tränad modell. Modellen måste sparas till en eller flera filer i din utvecklingsmiljö.

    > [!NOTE]
    > För att demonstrera registrerar en modell tränas utanför Azure Machine Learning-tjänsten, exempelkodstyckena i den här artikeln använder modeller som skapats av Paolo Ripamonti Twitter sentiment analysis project: [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrera modell(er)

Registrera en modell kan du lagra, version, och spåra metadata om modeller i din arbetsyta. I följande Python och CLI exempel har den `models` katalogen innehåller den `model.h5`, `model.w2v`, `encoder.pkl`, och `tokenizer.pkl` filer. Det här exemplet Överför filer i den `models` katalogen som en ny modell-registrering med namnet `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Mer information finns i den [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) referens.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Mer information finns i den [az ml-modellen registrera](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) referens.


Mer information om modellen i allmänhet finns [hantera, distribuera och övervaka maskininlärningsmodeller](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definiera inferens konfiguration

Inferens konfigurationen definierar den miljö som används för att köra distribuerad modell. Konfigurationen av inferens refererar till följande filer som används för att köra modellen när den har distribuerats:

* Körningen. Det enda giltiga värdet för runtime är för närvarande Python.
* En post-skript. Den här filen (med namnet `score.py`) läser in modellen när den distribuerade tjänsten startas. Det är också ansvarig för att ta emot data, skicka den till modellen och returnerar ett svar.
* En conda miljöfil. Den här filen definierar Python-paket som behövs för att köra skriptet modell och posten. 

I följande exempel visas en grundläggande inferens-konfiguration med hjälp av Python-SDK:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

Mer information finns i den [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) referens.

CLI läser in inferens konfigurationen från en YAML-fil:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Läs mer på inferens konfiguration, [distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Post-skript

Post-skriptet har bara två konfigurationsfunktionerna `init()` och `run(data)`. Dessa funktioner används för att initiera tjänsten vid start och kör modellen med begärandedata som skickades av en klient. Resten av skriptet hanterar läser in och kör modell(er).

> [!IMPORTANT]
> Det finns inte en allmän post-skript som fungerar för alla modeller. Det är alltid specifik för den modell som används. Den måste förstå hur du läser in modellen, dataformatet som förväntar sig att modellen och hur du bedöma data med hjälp av modellen.

Följande kod i Python är ett exempel på post-skript (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

Mer information om posten skript finns i [distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Conda-miljö

Följande YAML beskriver conda-miljön som behövs för att köra skriptet modell och post:

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
```

Mer information finns i [distribuera modeller med Azure Machine Learning-tjänsten](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiera distribution

Den [webbtjänsten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) paketet innehåller de klasser som används för distribution. Den klass som du använder avgör där modellen har distribuerats. Till exempel om du vill distribuera som en webbtjänst på Azure Kubernetes Service, använda [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) att skapa distributionskonfigurationen för.

Följande Python-kod definierar en distributionskonfiguration för en lokal distribution. Den här konfigurationen distribuerar modellen som en webbtjänst till din lokala dator.

> [!IMPORTANT]
> En lokal distribution kräver en fungerande installation av [Docker](https://www.docker.com/) på den lokala datorn:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Mer information finns i den [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) referens.

CLI läser in distributionskonfigurationen från en YAML-fil:

```YAML
{
    "computeType": "LOCAL"
}
```

Distribuera till en andra beräkningsmål, till exempel Azure Kubernetes Service i Azure-molnet är lika enkelt som att ändra distributionskonfigurationen för. Mer information finns i [hur och var du vill distribuera modeller](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Distribuera modellen

I följande exempel läser in information om den registrerade modellen med namnet `sentiment`, och distribuerar sedan som en tjänst med namnet `sentiment`. Under distributionen av används inferens konfigurationen och distributionskonfiguration för att skapa och konfigurera service-miljö:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Mer information finns i den [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) referens.

Använd följande kommando för att distribuera modellen från CLI. Det här kommandot distribuerar version 1 av den registrerade modellen (`sentiment:1`) med hjälp av konfigurationen av inferens och distribution som lagras i den `inferenceConfig.json` och `deploymentConfig.json` filer:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Mer information finns i den [az ml-modell distribuera](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referens.

Mer information om distribution finns i [hur och var du vill distribuera modeller](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Begäran / svar-förbrukning

Efter distributionen visas bedömnings-URI: N. URI: N kan användas av klienter för att skicka begäranden till tjänsten. I följande exempel är en grundläggande Python-klient som skickar data till tjänsten och visar svaret:

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

Mer information om hur du använder den distribuerade tjänsten finns i [skapa en klient](how-to-consume-web-service.md).

## <a name="next-steps"></a>Nästa steg

* [Övervaka dina Azure Machine Learning-modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Hur och var du vill distribuera modeller](how-to-deploy-and-where.md)
* [Så här skapar du en klient för en distribuerad modell](how-to-consume-web-service.md)