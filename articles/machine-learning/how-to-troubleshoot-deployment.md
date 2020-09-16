---
title: Fel sökning av Docker-distribution
titleSuffix: Azure Machine Learning
description: Lär dig hur du arbetar runt, löser och felsöker vanliga Docker-distributions fel med Azure Kubernetes service och Azure Container Instances med Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python
ms.openlocfilehash: 22f9c709ced1069caa39ba2145981efa353caadf
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602641"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Felsöka Docker-distribution av modeller med Azure Kubernetes service och Azure Container Instances 

Lär dig att felsöka och lösa, eller Undvik, vanliga Docker-distributions fel med Azure Container Instances (ACI) och Azure Kubernetes service (AKS) med Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
* För att felsöka lokalt måste du ha en fungerande Docker-installation på det lokala systemet.

    Verifiera din Docker-installation genom att använda kommandot `docker run hello-world` från en terminal eller kommando tolk. Information om hur du installerar Docker eller felsöker Docker-fel finns i [Docker-dokumentationen](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Steg för Docker-distribution av Machine Learning-modeller

När du distribuerar en modell i Azure Machine Learning använder du API: t [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) och ett [miljö](how-to-use-environments.md) objekt. Tjänsten skapar en bas Docker-avbildning under distributions fasen och monterar de nödvändiga modellerna alla i ett anrop. De grundläggande distributions uppgifterna är:

1. Registrera modellen i arbets ytans modell register.

2. Definiera konfiguration av härledning:
    1. Skapa ett [miljö](how-to-use-environments.md) objekt. Det här objektet kan använda beroenden i en miljö yaml-fil, en av våra granskade miljöer.
    2. Skapa en konfiguration för en konfiguration (InferenceConfig-objekt) baserat på miljön och bedömnings skriptet.

3. Distribuera modellen till Azure Container instance-tjänsten (ACI) eller till Azure Kubernetes service (AKS).

Läs mer om den här processen i Introduktion till [modellhantering](concept-model-management-and-deployment.md) .

## <a name="before-you-begin"></a>Innan du börjar

Om du stöter på ett problem är det första du ska göra är att dela upp distributions aktiviteten (tidigare beskriven) i enskilda steg för att isolera problemet.

När du använder [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) med ett [miljö](how-to-use-environments.md) objekt som indataparameter, kan din kod delas upp i tre viktiga steg:

1. Registrera modellen. Här följer några exempel kod:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definiera konfiguration av drifts störningar för distribution:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Distribuera modellen med hjälp av den konfigurations konfiguration som skapades i föregående steg:

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

Genom att bryta Thee-distributions processen till enskilda aktiviteter blir det enklare att identifiera några av de vanligaste felen.

## <a name="debug-locally"></a>Felsök lokalt

Om du har problem när du distribuerar en modell till ACI eller AKS kan du distribuera den som en lokal webb tjänst. Med en lokal webbtjänst blir det enklare att felsöka problem.

Du hittar ett exempel på en [lokal distributions antecknings bok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) i  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) -lagrings platsen för att utforska ett körbara-exempel.

> [!WARNING]
> Distributioner av lokala webb tjänster stöds inte i produktions scenarier.

Om du vill distribuera lokalt ändrar du koden så att den används `LocalWebservice.deploy_configuration()` för att skapa en distributions konfiguration. Använd sedan `Model.deploy()` för att distribuera tjänsten. I följande exempel distribueras en modell (som finns i modell variabeln) som en lokal webb tjänst:

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

Om du definierar din egen Conda-specifikation YAML visar du azureml-defaults version >= 1.0.45 som ett pip-beroende. Det här paketet krävs för att vara värd för modellen som en webb tjänst.

Nu kan du arbeta med tjänsten som vanligt. Följande kod visar hur du skickar data till tjänsten:

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

Mer information om hur du anpassar din python-miljö finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

### <a name="update-the-service"></a>Uppdatera tjänsten

Under lokal testning kan du behöva uppdatera `score.py` filen för att lägga till loggning eller försöka lösa eventuella problem som du har identifierat. Om du vill läsa in ändringarna i `score.py` filen igen använder du `reload()` . Följande kod läser till exempel in skriptet för tjänsten och skickar sedan data till den. Data får poäng med den uppdaterade `score.py` filen:

> [!IMPORTANT]
> `reload`Metoden är endast tillgänglig för lokala distributioner. Information om hur du uppdaterar en distribution till ett annat beräknings mål finns i [så här uppdaterar du din WebService](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skriptet läses in på nytt från den plats som anges av det `InferenceConfig` objekt som används av tjänsten.

Om du vill ändra modellen, Conda-beroenden eller distributions konfigurationen använder du [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#&preserve-view=trueupdate--args-). I följande exempel uppdateras modellen som används av tjänsten:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Ta bort tjänsten

Om du vill ta bort tjänsten använder du [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Granska Docker-loggen

Du kan skriva ut detaljerade logg meddelanden för Docker-motorn från serviceobjektet. Du kan visa loggen för ACI, AKS och lokala distributioner. Följande exempel visar hur du skriver ut loggarna.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Om du ser att linjen `Booting worker with pid: <pid>` inträffar flera gånger i loggarna innebär det att det inte finns tillräckligt med minne för att starta arbetaren.
Du kan åtgärda felet genom att öka värdet för `memory_gb` i `deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>Behållaren kan inte schemaläggas

När en tjänst distribueras till ett beräknings mål för Azure Kubernetes-tjänsten försöker Azure Machine Learning schemalägga tjänsten med den begärda mängden resurser. Om det inte finns några tillgängliga noder i klustret med en lämplig mängd resurser efter 5 minuter, kommer distributionen att Miss par. Fel meddelandet är `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Du kan åtgärda det här felet genom att antingen lägga till fler noder, ändra SKU: er för dina noder eller ändra resurs kraven för din tjänst. 

Fel meddelandet indikerar vanligt vis vilken resurs du behöver mer av – om du ser ett fel meddelande som anger att `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` tjänsten kräver GPU: er och det finns tre noder i klustret som inte har tillgängliga GPU: er. Detta kan åtgärdas genom att lägga till fler noder om du använder en GPU-SKU och växlar till en GPU-aktiverad SKU om du inte eller ändrar din miljö till att inte kräva GPU: er.  

## <a name="service-launch-fails"></a>Det går inte att starta tjänsten

När avbildningen har skapats försöker systemet starta en behållare med hjälp av distributions konfigurationen. Som en del av processen för container Start `init()` anropas funktionen i bedömnings skriptet av systemet. Om det finns undantag som inte har fångats i `init()` funktionen kan du se **CrashLoopBackOff** -fel i fel meddelandet.

Använd informationen i avsnittet [Granska Docker-loggen](#dockerlog) för att kontrol lera loggarna.

## <a name="function-fails-get_model_path"></a>Funktionen misslyckades: get_model_path ()

I `init()` funktionen i bedömnings skriptet, anropas ofta [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) för att hitta en modell fil eller en mapp med modell filer i behållaren. Om modell filen eller mappen inte kan hittas Miss lyckas funktionen. Det enklaste sättet att felsöka det här felet är att köra följande python-kod i container Shell:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Det här exemplet skriver ut den lokala sökvägen (i förhållande till `/var/azureml-app` ) i behållaren där bedömnings skriptet förväntar sig att hitta modell filen eller mappen. Sedan kan du kontrol lera om filen eller mappen verkligen är den förväntas vara.

Om du ställer in loggnings nivån på fel sökning kan det leda till att ytterligare information loggas, vilket kan vara användbart vid identifiering av felet.

## <a name="function-fails-runinput_data"></a>Funktionen misslyckades: kör (input_data)

Om tjänsten har distribuerats, men den kraschar när du skickar data till bedömnings slut punkten, kan du lägga till fel som fångar upp instruktionen i `run(input_data)` funktionen så att den returnerar ett detaljerat fel meddelande i stället. Exempel:

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

**Obs!** fel meddelanden som returneras från `run(input_data)` anropet ska endast göras för fel söknings syfte. Av säkerhets skäl bör du inte returnera fel meddelanden på det här sättet i en produktions miljö.

## <a name="http-status-code-502"></a>HTTP-statuskod 502

En status kod för 502 visar att tjänsten har utlöst ett undantag eller kraschat i `run()` metoden för score.py-filen. Använd informationen i den här artikeln för att felsöka filen.

## <a name="http-status-code-503"></a>HTTP-statuskod 503

Distributioner av Azure Kubernetes-tjänster stöder automatisk skalning, vilket gör att repliker kan läggas till för att stödja ytterligare belastning. Autoskalning är utformat för att hantera **gradvisa** ändringar i belastningen. Om du får stora toppar i begär Anden per sekund, kan klienterna få HTTP-statuskod 503. Även om autoskalning reagerar snabbt, tar det AKS en stor del av tiden för att skapa ytterligare behållare.

Beslut om att skala upp/ned baseras på användning av aktuella behållares repliker. Det totala antalet repliker som är upptagna (bearbetning av en begäran) dividerat med det totala antalet aktuella repliker är den aktuella användningen. Om det här värdet överskrider så `autoscale_target_utilization` skapas fler repliker. Om den är lägre minskas replikerna. Beslut om att lägga till repliker är Eager och snabbt (cirka 1 sekund). Beslut att ta bort repliker är försiktigt (cirka 1 minut). Som standard är den automatiska skalnings mål användningen inställd på **70%**, vilket innebär att tjänsten kan hantera toppar i begär Anden per sekund (RPS) på **upp till 30%**.

Det finns två saker som kan hjälpa till att förhindra 503 status koder:

> [!TIP]
> Dessa två metoder kan användas individuellt eller i kombination.

* Ändra användnings nivån där autoskalning skapar nya repliker. Du kan justera användnings målet genom `autoscale_target_utilization` att ange till ett lägre värde.

    > [!IMPORTANT]
    > Den här ändringen innebär inte att repliker skapas *snabbare*. I stället skapas de med ett lägre användnings tröskelvärde. I stället för att vänta tills tjänsten är 70% Använd, och om du ändrar värdet till 30%, så skapas repliker när 30% belastning sker.
    
    Om webb tjänsten redan använder de aktuella Max replikerna och du fortfarande ser 503 status koder ökar du `autoscale_max_replicas` värdet för att öka det maximala antalet repliker.

* Ändra det minsta antalet repliker. Att öka de minsta replikerna ger en större pool som hanterar inkommande toppar.

    Om du vill öka det lägsta antalet repliker anger `autoscale_min_replicas` du ett högre värde. Du kan beräkna de repliker som krävs genom att använda följande kod och ersätta värden med värden som är speciella för ditt projekt:

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
    > Om du får begär ande toppar som är större än de nya minsta replikerna kan hantera kan du få 503s igen. När trafik till tjänsten ökar kan du till exempel behöva öka de lägsta replikerna.

Mer information om hur du ställer in `autoscale_target_utilization` , `autoscale_max_replicas` och `autoscale_min_replicas` för finns i [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true) -modulens referens.

## <a name="http-status-code-504"></a>HTTP-statuskod 504

En status kod för 504 visar att tids gränsen för begäran har uppnåtts. Standardvärdet för timeout är 1 minut.

Du kan öka tids gränsen eller försöka påskynda tjänsten genom att ändra score.py för att ta bort onödiga anrop. Om de här åtgärderna inte löser problemet kan du använda informationen i den här artikeln för att felsöka score.py-filen. Koden kan vara i ett tillstånd som inte svarar eller en oändlig loop.

## <a name="advanced-debugging"></a>Avancerad fel sökning

Du kan behöva interaktivt felsöka python-koden som finns i modell distributionen. Om Entry-skriptet till exempel inte fungerar och orsaken inte kan fastställas av ytterligare loggning. Genom att använda Visual Studio Code och debugpy kan du koppla till koden som körs i Docker-behållaren.

Mer information finns [i interaktiv fel sökning i vs Code guide](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Användar forum för modell distribution](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om distribution:

* [Distribuera och var](how-to-deploy-and-where.md)
* [Självstudie: träna & distribuera modeller](tutorial-train-models-with-aml.md)
