---
title: Distribuera felsökningsguide
titleSuffix: Azure Machine Learning service
description: Lär dig hur du kan undvika, lösa och felsöka vanliga Docker-distributionsfel med AKS och ACI med hjälp av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 05/02/2018
ms.custom: seodec18
ms.openlocfilehash: 90e85e0030a696dd024dd65d27a0f4dbdc7e3cdc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023663"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Felsökning av Azure Machine Learning-tjänsten AKS och ACI-distributioner

Lär dig hur du kan undvika eller lösa vanliga Docker-distributionsfel med Azure Container Instances (ACI) och Azure Kubernetes Service (AKS) med hjälp av Azure Machine Learning-tjänsten.

När du distribuerar en modell i Azure Machine Learning-tjänsten, utförs ett antal uppgifter. Distribution aktiviteter är:

1. Registrera modellen i arbetsytan modellen registret.

2. Skapa en Docker-avbildning, inklusive:
    1. Ladda ned den registrerade modellen från registret. 
    2. Skapa en docker-fil med en Python-miljö baserat på de beroenden som du anger i miljön yaml-fil.
    3. Lägg till modellfiler och bedömningsskriptet som du anger i dockerfile.
    4. Skapa en ny Docker-avbildning med hjälp av dockerfile.
    5. Registrera Docker-avbildningen med Azure Container Registry som är associerade med arbetsytan.

    > [!IMPORTANT]
    > Beroende på din kod, skapa avbildningar sker automatiskt utan att dina indata.

3. Distribuera Docker-avbildningen till Azure Container Instance (ACI)-tjänsten eller till Azure Kubernetes Service (AKS).

4. Starta en ny behållare (eller behållare) i ACI eller AKS. 

Mer information om den här processen i den [modellhantering](concept-model-management-and-deployment.md) introduktion.

## <a name="before-you-begin"></a>Innan du börjar

Om du stöter på några problem, det första du ska göra är att bryta ned aktiviteten distribution (tidigare beskrivs) till enskilda steg för att isolera problemet.

Dela upp distributionen i uppgifter är användbart om du använder den [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API, eller [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API, som båda dessa funktioner gör ovan nämnda som en enskild åtgärd. Dessa API: er är oftast praktiskt, men det hjälper dig för att dela upp stegen när du felsöker genom att ersätta dem med den nedan API-anrop.

1. Registrera modellen. Här är exempelkod:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Skapa avbildningen. Här är exempelkod:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Distribuera avbildningen som tjänst. Här är exempelkod:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

När du har uppdelade distributionsprocessen i enskilda aktiviteter kan vi titta på några av de vanligaste felen.

## <a name="image-building-fails"></a>Bild som att skapa misslyckas

Om Docker-avbildningen inte kan skapas, den [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) eller [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) anrop misslyckas med vissa felmeddelanden som kan erbjuda viss vägledning. Du kan också hitta mer information om felen från image build-loggen. Nedan är exempelkod som visar hur du identifierar build log-uri för avbildning.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

Logg-uri för avbildning är en SAS-URL som pekar på en loggfil som lagras i Azure blob storage. Helt enkelt kopiera och klistra in URI: n i ett webbläsarfönster och du kan hämta och visa loggfilen.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Åtkomstprincip för Azure Key Vault och Azure Resource Manager-mallar

Bild-versionen kan också misslyckas på grund av ett problem med åtkomstprincipen i Azure Key Vault. Den här situationen kan uppstå när du använder en Azure Resource Manager-mall för att skapa arbetsyta och associerade resurser (inklusive Azure Key Vault), flera gånger. Till exempel med hjälp av mallen flera gånger med samma parametrar som en del av en kontinuerlig integrering och av distributionspipeline.

De flesta resursskapande åtgärder via mallar är idempotenta, men Key Vault rensar principer för åtkomst till varje gång mallen används. Rensar radbrytningar åtkomst till Key Vault för en befintlig arbetsyta som använder den med principer för åtkomst. Det här tillståndet resulterar i fel vid försök att skapa nya avbildningar. Här följer några exempel på de fel som du kan ta emot:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Vi rekommenderar en av följande metoder för att undvika det här problemet:

* Distribuera inte mallen mer än en gång för samma parametrar. Eller ta bort befintliga resurser innan du använder mallen för att återskapa dem.
* Granska åtkomstprinciper för Key Vault och sedan använda dessa principer för att ange den `accessPolicies` egenskapen för mallen.
* Kontrollera om Key Vault-resursen finns redan. I annat fall du inte återskapa den via mallen. Lägg exempelvis till en parameter som gör det möjligt att inaktivera skapandet av Key Vault-resursen om den redan finns.

## <a name="debug-locally"></a>Felsöka lokalt

Om du får problem med att distribuera en modell till ACI eller AKS, försök att distribuera den som en lokal webbtjänst. Med hjälp av en lokal webbtjänsten gör det enklare att felsöka problem. Docker-avbildningen som innehåller modellen hämtas och startats på den lokala datorn.

> [!IMPORTANT]
> Lokala webbtjänstdistributioner kräver en fungerande installation av Docker på den lokala datorn. Docker måste köras innan du distribuerar en lokal webbtjänst. Information om att installera och använda Docker finns i [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Lokala webbtjänstdistributioner stöds inte för produktionsscenarier.

Ändra din kod som ska användas för att distribuera lokalt, `LocalWebservice.deploy_configuration()` att skapa en distributionskonfiguration. Använd sedan `Model.deploy()` att distribuera tjänsten. I följande exempel distribuerar en modell (som finns i den `model` variabeln) som en lokal webbtjänst:

```python
from azureml.core.model import InferenceConfig
from azureml.core.webservice import LocalWebservice

# Create inferencing configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   execution_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Nu kan du arbeta med tjänsten som vanligt. Följande kod visar till exempel skicka data till tjänsten:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Uppdatera tjänsten

Under lokal testning kan du behöva uppdatera den `score.py` filen för att lägga till loggning eller försök att lösa eventuella problem som du har identifierats. Att uppdatera ändringar i den `score.py` fil ska du använda `reload()`. Följande kod hämtar skriptet för tjänsten och skickar data till den. Data beräknas med hjälp av den uppdaterade `score.py` fil:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skriptet laddas från den plats som anges av den `InferenceConfig` objekt som används av tjänsten.

Du kan ändra modellen, Conda-beroenden eller distributionskonfiguration med [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). I följande exempel uppdaterar modellen som används av tjänsten:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Ta bort tjänsten

Ta bort tjänsten genom att använda [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Inspektera Docker-loggen

Du kan skriva ut detaljerad Docker-motorn loggmeddelanden från objektet. Du kan visa loggen för ACI och AKS lokala distributioner. I följande exempel visar hur du skriver ut loggarna.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Starta tjänsten misslyckas

När avbildningen har har skapats, försöker systemet att starta en behållare med din konfiguration av distributionen. Som en del i processen för att starta upp behållaren, den `init()` funktionen i din bedömningsskriptet anropas av systemet. Om det finns undantag utan felhantering i den `init()` fungerar, visas **CrashLoopBackOff** fel i felmeddelandet.

Med hjälp av informationen i den [inspektera Docker-loggen](#dockerlog) avsnitt för att kontrollera loggfilerna.

## <a name="function-fails-getmodelpath"></a>Funktionen misslyckas: get_model_path()

Ofta, i den `init()` funktionen i bedömningsskriptet, [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) funktionen anropas för att hitta en modellfil eller en mapp med modellfiler i behållaren. Om modellfilen eller mappen hittas, misslyckas åtgärden. Det enklaste sättet att felsöka det här felet är att köra den nedan Python-kod i behållaren shell:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Det här exemplet skriver ut den lokala sökvägen (relativt till `/var/azureml-app`) i behållaren där dina bedömningsskriptet förväntar sig att hitta modellfilen eller mappen. Därefter kan du kontrollera om filen eller mappen är verkligen där det förväntas vara.

Ange loggningsnivån till DEBUG leda till ytterligare information som loggas, vilket kan vara användbart i identifiera felet.

## <a name="function-fails-runinputdata"></a>Funktionen misslyckas: run(input_data)

Om tjänsten har distribuerats, men den kraschar när du publicerar data till bedömnings-slutpunkten, du kan lägga till fel vid identifiering av instruktionen i din `run(input_data)` så att den returnerar detaljerat felmeddelande i stället. Exempel:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Obs!** Returnerar felmeddelanden från den `run(input_data)` anrop görs för felsökning endast syfte. Av säkerhetsskäl bör ska du inte returnera felmeddelanden på så sätt i en produktionsmiljö.

## <a name="http-status-code-503"></a>HTTP-statuskod: 503

Azure Kubernetes Service-distributioner stöder automatisk skalning, vilket gör att repliker som ska läggas till stöd för ytterligare belastning. Dock autoskalningen är utformad för att hantera **gradvis** ändringar i belastningen. Om du får kraftigt i begäranden per sekund, får klienter en HTTP-statuskod: 503.

Det finns två saker som kan förhindra statuskoder som 503:

* Ändra användningsnivån på vilka autoskalning skapar nya repliker.
    
    Målanvändning för automatisk skalning är som standard till 70%, vilket innebär att tjänsten kan hantera toppar i begäranden per sekund (RPS) upp till 30%. Du kan justera målvärde för användning genom att ange den `autoscale_target_utilization` till ett lägre värde.

    > [!IMPORTANT]
    > Den här ändringen inte orsakar repliker skapas *snabbare*. I stället skapas de med ett lägre tröskelvärde för användning. I stället för att vänta tills tjänsten har 70% används, gör att ändra värdet till 30% repliker som ska skapas när 30% utnyttjande inträffar.
    
    Om webbtjänsten använder redan de aktuella max replikerna och du fortfarande ser statuskoder som 503, öka den `autoscale_max_replicas` värde att öka det maximala antalet repliker.

* Ändra det minsta antalet repliker. Öka de minsta replikerna ger en större pool för att hantera inkommande toppar.

    Om du vill öka det minsta antalet repliker, ange `autoscale_min_replicas` på ett högre värde. Du kan beräkna krävs replikerna med hjälp av följande kod, ersättning av värden med specifika värden i projektet:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Om du får större än de nya minsta replikerna kan hantera toppar i begäran, kan du få 503s igen. Som trafik till din tjänst ökar, kan du behöva öka de minsta replikerna.

Mer information om hur `autoscale_target_utilization`, `autoscale_max_replicas`, och `autoscale_min_replicas` , finns i den [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) modulreferens.


## <a name="next-steps"></a>Nästa steg

Lär dig mer om distribution:

* [Hur du distribuerar och var](how-to-deploy-and-where.md)
* [Självstudie: Träna och distribuera modeller](tutorial-train-models-with-aml.md)
