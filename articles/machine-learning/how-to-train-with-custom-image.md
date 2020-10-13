---
title: Träna en modell med en anpassad Docker-avbildning
titleSuffix: Azure Machine Learning
description: Lär dig hur du tränar modeller med anpassade Docker-avbildningar i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8239d037d6bd68638998cbb36c47c7dac4bce30d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537624"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Träna en modell med en anpassad Docker-avbildning

I den här artikeln får du lära dig hur du använder en anpassad Docker-avbildning när du tränar modeller med Azure Machine Learning. 

Exempel skripten i den här artikeln används för att klassificera PET-bilder genom att skapa ett (convolutional neurala-nätverk. 

Även om Azure Machine Learning tillhandahåller en standard-Docker-basadress, kan du också använda Azure Machine Learning miljöer för att ange en bestämd bas avbildning, till exempel en uppsättning med [Azure ml](https://github.com/Azure/AzureML-Containers) -grundavbildningar eller din egen [anpassade avbildning](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Med anpassade bas avbildningar kan du noggrant hantera dina beroenden och upprätthålla bättre kontroll över komponent versioner när du kör utbildnings jobb. 

## <a name="prerequisites"></a>Förutsättningar 
Kör den här koden i någon av följande miljöer:
* Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs
    * Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    * Leta upp en slutförd antecknings bok i Azure Machine Learning [exempel lagrings plats](https://github.com/Azure/azureml-examples)genom att gå till den här katalogen: **How-to-use-azureml > ml framework > fastai > träna-with-Custom-Docker** 

* Din egen Jupyter Notebook Server
    * Skapa en [konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    * [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * En [Azure Container Registry](/azure/container-registry) eller ett annat Docker-register som är tillgängligt på Internet.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet 
I det här avsnittet anges övnings experimentet genom att initiera en arbets yta, skapa ett experiment och ladda upp utbildnings data och utbildnings skript.

### <a name="initialize-a-workspace"></a>Initiera en arbets yta
[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa ett [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) objekt.

Skapa ett objekt för arbets ytan från `config.json` filen som skapats i [avsnittet krav](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Förbereda skript
I den här självstudien finns **train.py** utbildnings skriptet Train.py [här](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py). I praktiken kan du ta med ett anpassat utbildnings skript, som det är och köra det med Azure Machine Learning.

### <a name="define-your-environment"></a>Definiera din miljö
Skapa ett miljö objekt och aktivera Docker. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Den angivna bas avbildningen nedan stöder fast.ai-biblioteket som möjliggör distribuerade djup inlärnings funktioner. Mer information finns i fast.ai- [DockerHub](https://hub.docker.com/u/fastdotai). 

När du använder din anpassade Docker-avbildning kanske du redan har konfigurerat python-miljön. I så fall ställer du in `user_managed_dependencies` flaggan på sant för att utnyttja den anpassade avbildningens inbyggda python-miljö. Som standard skapar Azure ML en Conda-miljö med beroenden som du har angett, och kör i den miljön i stället för att använda python-bibliotek som du har installerat på bas avbildningen.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Om du vill använda en avbildning från ett privat behållar register som inte finns i din arbets yta, måste du använda `docker.base_image_registry` för att ange adressen till lagrings platsen och ett användar namn och lösen ord:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Det är också möjligt att använda en anpassad Dockerfile. Använd den här metoden om du behöver installera icke-python-paket som beroenden och kom ihåg att ställa in bas avbildningen på ingen.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Mer information om hur du skapar och hanterar Azure ML-miljöer finns i [skapa & använda program miljöer](how-to-use-environments.md). 

### <a name="create-or-attach-existing-amlcompute"></a>Skapa eller koppla befintliga AmlCompute
Du måste skapa ett [beräknings mål](concept-azure-machine-learning-architecture.md#compute-targets) för att träna din modell. I den här självstudien skapar du AmlCompute som din utbildnings beräknings resurs.

Skapandet av AmlCompute tar cirka 5 minuter. Om AmlCompute med det namnet redan finns i arbets ytan hoppar den här koden över skapande processen.

Precis som med andra Azure-tjänster finns det gränser för vissa resurser (t. ex. AmlCompute) som är kopplade till tjänsten Azure Machine Learning. Läs [den här artikeln](how-to-manage-quotas.md) om standard gränserna och hur du begär mer kvot. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Skapa en ScriptRunConfig
Den här ScriptRunConfig kommer att konfigurera jobbet för körning på önskat [beräknings mål](how-to-set-up-training-targets.md).

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>Skicka in din körning
När en utbildnings körning skickas med ett ScriptRunConfig-objekt returnerar Submit-metoden ett objekt av typen ScriptRun. Det returnerade ScriptRun-objektet ger dig program mässig åtkomst till information om tränings körningen. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning kör utbildnings skript genom att kopiera hela käll katalogen. Om du har känsliga data som du inte vill överföra använder du en [. IGNORE-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i käll katalogen. I stället kan du komma åt dina data med hjälp av ett data [lager](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true).

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du tränat en modell med en anpassad Docker-avbildning. Mer information om Azure Machine Learning finns i de här artiklarna.
* [Spåra körnings mått](how-to-track-experiments.md) under träning
* [Distribuera en modell](how-to-deploy-custom-docker-image.md) med hjälp av en anpassad Docker-avbildning.
