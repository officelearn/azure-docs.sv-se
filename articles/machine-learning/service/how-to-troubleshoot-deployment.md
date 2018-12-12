---
title: Distribuera felsökningsguide
titleSuffix: Azure Machine Learning service
description: Lär dig hur till lösning kan lösa och felsöka vanliga Docker-distributionsfel med AKS och ACI med hjälp av Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6bd3bc86aa828ab28462de9d45f660889634cbd7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100522"
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

Detta är särskilt användbart om du använder den `Webservice.deploy` API, eller `Webservice.deploy_from_model` API, eftersom dessa funktioner gruppera de tidigare nämnda steg i en enda åtgärd. Vanligtvis dessa API: er är ganska enkelt, men det hjälper dig för att dela upp stegen när du felsöker genom att ersätta dem med den nedan API-anrop.

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
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items():
    print (img.name, img.version, img.image_build_log_uri)
```
Logg-uri för avbildning är en SAS-URL som pekar på en loggfil som lagras i Azure blob storage. Helt enkelt kopiera och klistra in URI: n i ett webbläsarfönster och du kan hämta och visa loggfilen.


## <a name="service-launch-fails"></a>Starta tjänsten misslyckas
När avbildningen har har skapats, försöker systemet att starta en behållare i ACI eller AKS beroende på din konfiguration av distributionen. Allmänt rekommenderar vi att du prova en ACI-distribution först, eftersom det är en enklare distribution av enskild behållare. Det här sättet du kan sedan försäkra dig om eventuella problem med AKS.

Som en del i processen för att starta upp behållaren, den `init()` funktionen i din bedömningsskriptet anropas av systemet. Om det finns undantag utan felhantering i den `init()` fungerar, visas **CrashLoopBackOff** fel i felmeddelandet. Nedan följer några tips för att felsöka problemet.

### <a name="inspect-the-docker-log"></a>Inspektera Docker-loggen
Du kan skriva ut detaljerad Docker-motorn loggmeddelanden från objektet.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
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
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Detta skulle skriva ut den lokala sökvägen (relativt till `/var/azureml-app`) i behållaren där dina bedömningsskriptet förväntar sig att hitta modellfilen eller mappen. Därefter kan du kontrollera om filen eller mappen är verkligen där det förväntas vara.


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
**Obs**: returnerar felmeddelanden från den `run(input_data)` anrop görs för felsökning endast syfte. Det kanske inte är en bra idé att göra detta i en produktionsmiljö av säkerhetsskäl.


## <a name="next-steps"></a>Nästa steg

Lär dig mer om distribution: 
* [Hur du distribuerar och var](how-to-deploy-and-where.md)

* [Självstudie: Skapa och distribuera modeller](tutorial-train-models-with-aml.md)
