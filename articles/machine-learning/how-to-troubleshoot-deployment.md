---
title: Distribuera felsökningsguide
titleSuffix: Azure Machine Learning
description: Lär dig hur du kan lösa och felsöka de vanliga Docker-distributionsfelen med Azure Kubernetes-tjänsten och Azure Container Instances med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399679"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Felsöka Azure Machine Learning Azure Kubernetes Service och Azure Container Instances-distribution

Lär dig hur du kan komma runt eller lösa vanliga Docker-distributionsfel med Azure Container Instances (ACI) och Azure Kubernetes Service (AKS) med Hjälp av Azure Machine Learning.

När du distribuerar en modell i Azure Machine Learning utför systemet ett antal uppgifter.

Den rekommenderade och mest uppdaterade metoden för modelldistribution är via [API:et Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) med hjälp av ett [miljöobjekt](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) som en indataparameter. I det här fallet kommer vår tjänst att skapa en basdockeravbildning åt dig under distributionsfasen och montera de nödvändiga modellerna allt i ett anrop. De grundläggande distributionsuppgifterna är:

1. Registrera modellen i arbetsytemodellregistret.

2. Definiera inferenskonfiguration:
    1. Skapa ett [miljöobjekt](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) baserat på de beroenden du anger i miljö yaml-filen eller använd en av våra upphandlade miljöer.
    2. Skapa en inferenskonfiguration (InferenceConfig-objekt) baserat på miljön och bedömningsskriptet.

3. Distribuera modellen till Azure Container Instance (ACI) tjänst eller till Azure Kubernetes Service (AKS).

Läs mer om den här processen i introduktionen [av modellhantering.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Krav

* En **Azure-prenumeration**. Om du inte har någon provar du den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* [Sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)för Azure Machine Learning .
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Om du vill felsöka lokalt måste du ha en fungerande Docker-installation på ditt lokala system.

    Om du vill verifiera Docker-installationen använder du kommandot `docker run hello-world` från en terminal eller kommandotolk. Information om hur du installerar Docker eller felsöker Docker-fel finns i [Docker-dokumentationen](https://docs.docker.com/).

## <a name="before-you-begin"></a>Innan du börjar

Om du stöter på något problem är det första du kan göra att dela upp distributionsaktiviteten (föregående beskrivs) i enskilda steg för att isolera problemet.

Förutsatt att du använder den nya/rekommenderade distributionsmetoden via [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API med ett [miljöobjekt](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) som indataparameter, kan koden delas upp i tre huvudsteg:

1. Registrera modellen. Här är några exempel kod:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definiera inferenskonfiguration för distribution:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Distribuera modellen med den inferenskonfiguration som skapades i föregående steg:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

När du har delat upp distributionsprocessen i enskilda uppgifter kan vi titta på några av de vanligaste felen.

## <a name="debug-locally"></a>Felsök lokalt

Om du stöter på problem med att distribuera en modell till ACI eller AKS kan du prova att distribuera den som en lokal webbtjänst. Om du använder en lokal webbtjänst blir det enklare att felsöka problem. Docker-avbildningen som innehåller modellen hämtas och startas på ditt lokala system.

> [!WARNING]
> Lokala webbtjänstdistributioner stöds inte för produktionsscenarier.

Om du vill distribuera lokalt `LocalWebservice.deploy_configuration()` ändrar du koden som ska användas för att skapa en distributionskonfiguration. Använd `Model.deploy()` sedan för att distribuera tjänsten. I följande exempel distribueras en modell (i modellvariabeln) som en lokal webbtjänst:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Observera att om du definierar din egen conda specifikation YAML, måste du lista azureml-standardvärden med version >= 1.0.45 som en pip beroende. Det här paketet innehåller de funktioner som behövs för att vara värd för modellen som en webbtjänst.

Nu kan du arbeta med tjänsten som vanligt. Följande kod visar till exempel att data skickas till tjänsten:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Mer information om hur du anpassar pythonmiljön finns i [Skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

### <a name="update-the-service"></a>Uppdatera tjänsten

Under lokala tester kan du `score.py` behöva uppdatera filen för att lägga till loggning eller försöka lösa eventuella problem som du har upptäckt. Om du vill `score.py` läsa in `reload()`ändringarna i filen igen använder du . Följande kod laddar till exempel om skriptet för tjänsten och skickar sedan data till den. Data poängsätts med `score.py` hjälp av den uppdaterade filen:

> [!IMPORTANT]
> Metoden `reload` är endast tillgänglig för lokala distributioner. Information om hur du uppdaterar en distribution till ett annat beräkningsmål finns i uppdateringsavsnittet [i Distribuera modeller](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skriptet laddas om från den plats `InferenceConfig` som anges av objektet som används av tjänsten.

Om du vill ändra modellen, Conda-beroenden eller distributionskonfigurationen använder du [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). I följande exempel uppdateras den modell som används av tjänsten:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Ta bort tjänsten

Om du vill ta bort tjänsten använder du [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Kontrollera Docker-loggen

Du kan skriva ut detaljerade Docker-motorloggmeddelanden från serviceobjektet. Du kan visa loggen för ACI-, AKS- och Local-distributioner. I följande exempel visas hur du skriver ut loggarna.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Tjänsten startar misslyckas

När avbildningen har skapats försöker systemet starta en behållare med distributionskonfigurationen. Som en del av processen `init()` för att starta behållaren anropas funktionen i bedömningsskriptet av systemet. Om det finns undantag `init()` för oavbrutet i funktionen kan det visas **CrashLoopBackOff-fel** i felmeddelandet.

Använd informationen i avsnittet [Inspektera Docker-loggen](#dockerlog) för att kontrollera loggarna.

## <a name="function-fails-get_model_path"></a>Funktionen misslyckas: get_model_path()

I `init()` funktionen Modell.get_model_path() anropas ofta funktionen [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) för att hitta en modellfil eller en mapp med modellfiler i behållaren. Om det inte går att hitta modellfilen eller mappen misslyckas funktionen. Det enklaste sättet att felsöka det här felet är att köra nedanstående Python-kod i behållarskalet:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

I det här exemplet skrivs `/var/azureml-app`den lokala sökvägen ut (i förhållande till) i behållaren där bedömningsskriptet förväntar sig att hitta modellfilen eller mappen. Då kan du kontrollera om filen eller mappen verkligen är där den förväntas vara.

Om du ställer in loggningsnivån på DEBUG kan det leda till att ytterligare information loggas, vilket kan vara användbart för att identifiera felet.

## <a name="function-fails-runinput_data"></a>Funktionen misslyckas: kör(input_data)

Om tjänsten har distribuerats, men den kraschar när du bokför data i bedömningsslutpunkten, kan du lägga till felfångande sats i din `run(input_data)` funktion så att den returnerar detaljerade felmeddelanden i stället. Ett exempel:

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

**Obs:** Returnera felmeddelanden `run(input_data)` från samtalet bör göras för felsökning ändamål endast. Av säkerhetsskäl bör du inte returnera felmeddelanden på det här sättet i en produktionsmiljö.

## <a name="http-status-code-502"></a>HTTP-statuskod 502

En 502-statuskod anger att tjänsten har kastat ett `run()` undantag eller kraschat i metoden för score.py filen. Använd informationen i den här artikeln för att felsöka filen.

## <a name="http-status-code-503"></a>HTTP-statuskod 503

Azure Kubernetes Service-distributioner stöder automatisk skalning, vilket gör att repliker kan läggas till för att stödja ytterligare belastning. Den automatiska skakningen är dock utformad för att hantera **gradvisa** förändringar i belastningen. Om du får stora toppar i begäranden per sekund kan klienter få en HTTP-statuskod 503.

Det finns två saker som kan förhindra 503 statuskoder:

* Ändra utnyttjandenivån där automatisk skalning skapar nya repliker.
    
    Som standard är automatisk skalning målanvändning inställd på 70%, vilket innebär att tjänsten kan hantera toppar i begäranden per sekund (RPS) på upp till 30%. Du kan justera användningsmålet `autoscale_target_utilization` genom att ange värdet till ett lägre värde.

    > [!IMPORTANT]
    > Den här ändringen gör inte att repliker skapas *snabbare*. I stället skapas de vid en lägre utnyttjandetröskel. I stället för att vänta tills tjänsten används till 70 %, gör om du ändrar värdet till 30 % repliker skapas när 30 % användning sker.
    
    Om webbtjänsten redan använder de aktuella maxrepliker och du fortfarande ser `autoscale_max_replicas` 503-statuskoder ökar du värdet för att öka det maximala antalet repliker.

* Ändra det minsta antalet repliker. Om du ökar minsta repliker finns en större pool för att hantera inkommande toppar.

    Om du vill öka det `autoscale_min_replicas` minsta antalet repliker anger du ett högre värde. Du kan beräkna de repliker som krävs med hjälp av följande kod och ersätta värden med värden som är specifika för projektet:

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
    > Om du får förfråggningsuppsättningar som är större än de nya minsta replikerna kan hantera kan du få 503:00 igen. När trafiken till tjänsten ökar kan du till exempel behöva öka de minsta replikerna.

Mer information om `autoscale_target_utilization` `autoscale_max_replicas`inställning `autoscale_min_replicas` , och för finns i [AksWebservice-modulreferensen.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)

## <a name="http-status-code-504"></a>HTTP-statuskod 504

En 504-statuskod anger att begäran har tajmar ut. Standardtidsgränsen är 1 minut.

Du kan öka timeouten eller försöka snabba upp tjänsten genom att ändra score.py för att ta bort onödiga samtal. Om dessa åtgärder inte åtgärdar problemet använder du informationen i den här artikeln för att felsöka score.py filen. Koden kan vara i ett hängt tillstånd eller en oändlig loop.

## <a name="advanced-debugging"></a>Avancerad felsökning

I vissa fall kan du behöva felsöka Python-koden som finns i din modelldistribution interaktivt. Om till exempel postskriptet misslyckas och orsaken inte kan fastställas genom ytterligare loggning. Genom att använda Visual Studio-kod och Python Tools for Visual Studio (PTVSD) kan du koppla till koden som körs i Docker-behållaren.

> [!IMPORTANT]
> Den här metoden för felsökning fungerar `Model.deploy()` `LocalWebservice.deploy_configuration` inte när du använder och distribuerar en modell lokalt. I stället måste du skapa en avbildning med metoden [Model.package().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Lokala webbtjänstdistributioner kräver en fungerande Docker-installation på ditt lokala system. Mer information om hur du använder Docker finns i [Docker-dokumentationen](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

1. Så här installerar du Python Tools for Visual Studio (PTVSD) i din lokala VS-kodutvecklingsmiljö:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Mer information om hur du använder PTVSD med VS-kod finns i [Fjärrfelsökning](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Om du vill konfigurera VS-kod för att kommunicera med Docker-avbildningen skapar du en ny felsökningskonfiguration:

    1. Välj menyn __Felsökning__ från VS-kod och välj sedan __Öppna konfigurationer__. En fil med namnet __launch.json__ öppnas.

    1. Leta reda på raden som innehåller `"configurations": [`i filen __launch.json__ och infoga följande text efter den:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Om det redan finns andra poster i avsnittet konfigurationer lägger du till ett kommatecken (,) efter koden som du infogade.

        Det här avsnittet fästs i Docker-behållaren med port 5678.

    1. Spara __filen launch.json.__

### <a name="create-an-image-that-includes-ptvsd"></a>Skapa en bild som innehåller PTVSD

1. Ändra conda-miljön för distributionen så att den innehåller PTVSD. I följande exempel visas att `pip_packages` du lägger till den med parametern:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Om du vill starta PTVSD och vänta på en anslutning när `score.py` tjänsten startar lägger du till följande högst upp i filen:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Skapa en avbildning baserat på miljödefinitionen och hämta avbildningen till det lokala registret. Under felsökning kanske du vill göra ändringar i filerna i bilden utan att behöva återskapa den. Om du vill installera en textredigerare (vim) i Docker-avbildningen `Environment.docker.base_image` använder du egenskaperna och: `Environment.docker.base_dockerfile`

    > [!NOTE]
    > Det här exemplet `ws` förutsätter att pekar på din `model` Azure Machine Learning-arbetsyta och det är den modell som distribueras. Filen `myenv.yml` innehåller conda-beroenden som skapats i steg 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    När bilden har skapats och hämtats visas bildsökvägen (innehåller databas, namn och tagg, som i det här fallet också är dess sammandrag) i ett meddelande som liknar följande:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Om du vill göra det enklare att arbeta med bilden använder du följande kommando för att lägga till en tagg. Ersätt `myimagepath` med platsvärdet från föregående steg.

    ```bash
    docker tag myimagepath debug:1
    ```

    För resten av stegen kan du referera till `debug:1` den lokala bilden som i stället för värdet för den fullständiga bildsökvägen.

### <a name="debug-the-service"></a>Felsöka tjänsten

> [!TIP]
> Om du anger en timeout för PTVSD-anslutningen i `score.py` filen måste du ansluta VS-koden till felsökningssessionen innan tidsgränsen går ut. Starta VS-kod, öppna `score.py`den lokala kopian av , ange en brytpunkt och ha den klar att gå innan du använder stegen i det här avsnittet.
>
> Mer information om felsökning och inställning av brytpunkter finns i [Felsökning](https://code.visualstudio.com/Docs/editor/debugging).

1. Så här startar du en Docker-behållare med avbildningen:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Om du vill koppla VS-kod till PTVSD i behållaren öppnar du VS-kod och använder F5-tangenten eller väljer __Felsök__. När du uppmanas till det väljer du Azure __Machine Learning: Docker Debug-konfigurationen.__ Du kan också välja felsökningsikonen från sidofältet, __Azure Machine Learning: Docker Debug-posten__ på rullgardinsmenyn Felsökning och sedan använda den gröna pilen för att bifoga felsökningshanteraren.

    ![Felsökningsikonen, start-felsökningsknappen och konfigurationsväljaren](./media/how-to-troubleshoot-deployment/start-debugging.png)

Vid denna punkt ansluter VS-kod till PTVSD inuti Docker-behållaren och stannar vid brytpunkten som du angav tidigare. Du kan nu gå igenom koden när den körs, visa variabler, etc.

Mer information om hur du använder VS-kod för att felsöka Python finns i [Felsöka din Python-kod](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Ändra behållarfilerna

Om du vill göra ändringar i filer i bilden kan du koppla till behållaren som körs och köra ett bash-skal. Därifrån kan du använda vim för att redigera filer:

1. Om du vill ansluta till behållaren som körs och starta ett bash-skal i behållaren använder du följande kommando:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Om du vill hitta de filer som används av tjänsten använder du följande kommando `/var/azureml-app`från bash-skalet i behållaren om standardkatalogen skiljer sig från:

    ```bash
    cd /var/azureml-app
    ```

    Härifrån kan du använda vim `score.py` för att redigera filen. Mer information om hur du använder vim finns i [Använda Vim-redigeraren](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Ändringar i en behållare sparas normalt inte. Om du vill spara alla ändringar du gör använder du följande kommando innan du avslutar skalet som startades i steget ovan (det vill säga i ett annat skal):

    ```bash
    docker commit debug debug:2
    ```

    Med det här kommandot `debug:2` skapas en ny bild med namnet som innehåller dina redigeringar.

    > [!TIP]
    > Du måste stoppa den aktuella behållaren och börja använda den nya versionen innan ändringarna börjar gälla.

1. Se till att de ändringar du gör i filer i behållaren synkroniseras med de lokala filer som VS-kod använder. Annars kommer felsökningsupplevelsen inte att fungera som förväntat.

### <a name="stop-the-container"></a>Stoppa containern

Om du vill stoppa behållaren använder du följande kommando:

```bash
docker stop debug
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om distribution:

* [Hur man distribuerar och var](how-to-deploy-and-where.md)
* [Självstudiekurs: Träna & distribuera modeller](tutorial-train-models-with-aml.md)
