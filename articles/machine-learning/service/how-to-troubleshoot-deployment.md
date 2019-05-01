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
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: f81aea22014a2c7d5b37c500a546f0b5350b6435
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925388"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Felsökning av Azure Machine Learning-tjänsten AKS och ACI-distributioner

I den här artikeln får lära du dig att undvika eller lösa vanliga Docker-distributionsfel med Azure Container Instances (ACI) och Azure Kubernetes Service (AKS) med hjälp av Azure Machine Learning-tjänsten.

När du distribuerar en modell i Azure Machine Learning-tjänsten, utförs ett antal uppgifter. Detta är en komplex händelsesekvens och ibland problem uppstår. Distribution aktiviteter är:

1. Registrera modellen i arbetsytan modellen registret.

2. Skapa en Docker-avbildning, inklusive:
    1. Ladda ned den registrerade modellen från registret. 
    2. Skapa en docker-fil med en Python-miljö baserat på de beroenden som du anger i miljön yaml-fil.
    3. Lägg till modellfiler och bedömningsskriptet som du anger i dockerfile.
    4. Skapa en ny Docker-avbildning med hjälp av dockerfile.
    5. Registrera Docker-avbildningen med Azure Container Registry som är associerade med arbetsytan.

3. Distribuera Docker-avbildningen till Azure Container Instance (ACI)-tjänsten eller till Azure Kubernetes Service (AKS).

4. Starta en ny behållare (eller behållare) i ACI eller AKS. 

Mer information om den här processen i den [modellhantering](concept-model-management-and-deployment.md) introduktion.

## <a name="before-you-begin"></a>Innan du börjar

Om du stöter på några problem, det första du ska göra är att bryta ned aktiviteten distribution (tidigare beskrivs) till enskilda steg för att isolera problemet. 

Det här är användbart om du använder den `Webservice.deploy` API, eller `Webservice.deploy_from_model` API, eftersom dessa funktioner gruppera de tidigare nämnda steg i en enda åtgärd. Dessa API: er är oftast praktiskt, men det hjälper dig för att dela upp stegen när du felsöker genom att ersätta dem med den nedan API-anrop.

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
Om systemet är det går inte att skapa Docker-avbildningen i `image.wait_for_creation()` anrop misslyckas med vissa felmeddelanden som kan erbjuda viss vägledning. Du kan också hitta mer information om felen från image build-loggen. Nedan är exempelkod som visar hur du identifierar build log-uri för avbildning.

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

Bild-versionen kan också misslyckas på grund av ett problem med åtkomstprincipen i Azure Key Vault. Detta kan inträffa när du använder en Azure Resource Manager-mall för att skapa arbetsyta och associerade resurser (inklusive Azure Key Vault), flera gånger. Till exempel med hjälp av mallen flera gånger med samma parametrar som en del av en kontinuerlig integrering och av distributionspipeline.

De flesta resursskapande åtgärder via mallar är idempotenta, men Key Vault rensar principer för åtkomst till varje gång mallen används. Detta bryter åtkomst till Key Vault för en befintlig arbetsyta som använder den. Detta resulterar i fel vid försök att skapa nya avbildningar. Här följer några exempel på de fel som du kan ta emot:

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
* Granska åtkomstprinciper för Key Vault och använda detta för att ange den `accessPolicies` egenskapen för mallen.
* Kontrollera om Key Vault-resursen finns redan. I annat fall du inte återskapa den via mallen. Lägg exempelvis till en parameter som gör det möjligt att inaktivera skapandet av Key Vault-resursen om den redan finns.

## <a name="service-launch-fails"></a>Starta tjänsten misslyckas
När avbildningen har har skapats, försöker systemet att starta en behållare i ACI eller AKS beroende på din konfiguration av distributionen. Det rekommenderas att använda en ACI-distribution först, eftersom det är en enklare distribution av enskild behållare. Det här sättet du kan sedan försäkra dig om eventuella problem med AKS.

Som en del i processen för att starta upp behållaren, den `init()` funktionen i din bedömningsskriptet anropas av systemet. Om det finns undantag utan felhantering i den `init()` fungerar, visas **CrashLoopBackOff** fel i felmeddelandet. Nedan följer några tips för att felsöka problemet.

### <a name="inspect-the-docker-log"></a>Inspektera Docker-loggen
Du kan skriva ut detaljerad Docker-motorn loggmeddelanden från objektet.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Felsöka Docker-avbildningen lokalt
Några tillfällen Docker-loggen inte genererar tillräckligt med information om vad som händer fel. Du kan gå ett steg längre och hämta Docker-avbildningen, starta en lokal behållare och Felsök direkt i live-behållaren interaktivt. Om du vill starta en lokal behållare, måste du ha en Docker-motor som körs lokalt och det är mycket enklare om du även har [azure cli-](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installerad.

Först måste vi ta reda på platsen:

```python
# print image location
print(image.image_location)
```

Bildplatsen har följande format: `<acr-name>.azurecr.io/<image-name>:<version-number>`, till exempel `myworkpaceacr.azurecr.io/myimage:3`. 

Gå nu till din kommandoradsfönster. Om du har installerat azure-cli kan skriva du följande kommandon för att logga in på ACR (Azure Container Registry) kopplade till arbetsytan där avbildningen finns sparad. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Om du inte har installerat azure-cli kan du använda `docker login` kommando för att logga in på ACR. Men du måste först hämta användarnamn och lösenord för ACR från Azure-portalen.

När du har loggat in till ACR, du kan hämta Docker-avbildningen och starta en behållare lokalt och sedan starta bash-session för felsökning med hjälp av den `docker run` kommando:

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

När du startar en bash-session på behållaren som körs hittar bedömnings skripten i den `/var/azureml-app` mapp. Du kan sedan starta en Python-session för att felsöka dina bedömnings-skript. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
Om du behöver en textredigerare för att ändra skripten kan du installera vim, nano, Emacs eller din favoritredigerare.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Du kan också starta webbtjänsten lokalt och skicka HTTP-trafik till den. Flask-servern i Docker-behållare körs på port 5001. Du kan mappa till alla andra portar som är tillgängliga på värddatorn.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Funktionen misslyckas: get_model_path()
Ofta, i den `init()` funktionen i bedömningsskriptet, `Model.get_model_path()` funktionen anropas för att hitta en modellfil eller en mapp med modellfiler i behållaren. Det här är ofta en felkälla om modellfilen eller mappen inte kan hittas. Det enklaste sättet att felsöka det här felet är att köra den nedan Python-kod i behållaren shell:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Detta skulle skriva ut den lokala sökvägen (relativt till `/var/azureml-app`) i behållaren där dina bedömningsskriptet förväntar sig att hitta modellfilen eller mappen. Därefter kan du kontrollera om filen eller mappen är verkligen där det förväntas vara.

Ange loggningsnivån till felsökning kan ge orsak ytterligare information som loggas, vilket kan vara användbart i identifiera felet.

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
**Obs!** Returnerar felmeddelanden från den `run(input_data)` anrop görs för felsökning endast syfte. Det kanske inte är en bra idé att göra detta i en produktionsmiljö av säkerhetsskäl.

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
