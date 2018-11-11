---
title: Distribuera modeller till Kubernetes från Azure Machine Learning-tjänsten | Microsoft Docs
description: Lär dig hur du distribuerar en modell från Azure Machine Learning-tjänsten till Azure Kubernetes Service. Modellen har distribuerats som en webbtjänst. Azure Kubernetes Service är bra för produktionsarbetsbelastningar med hög skalbarhet.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: efaaa196220213877283040120d6c2eeed86dbf3
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346328"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Hur du distribuerar modeller från Azure Machine Learning-tjänsten till Azure Kubernetes Service

Scenarier med hög skalbarhet produktion kan du distribuera din modell till Azure Kubernetes Service (AKS). Azure Machine Learning kan använda ett befintligt AKS-kluster eller ett nytt kluster som skapas under distributionen. Modellen har distribuerats till be som en webbtjänst.

Distribuera till AKS tillhandahåller automatisk skalning, loggning, insamling av modelldata och snabba svarstider för webbtjänsten.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- En Azure Machine Learning-tjänsten arbetsyta, en lokal katalog som innehåller dina skript och Azure Machine Learning-SDK för Python installerat. Lär dig hur du hämtar dessa krav med hjälp av den [så här konfigurerar du en utvecklingsmiljö](how-to-configure-environment.md) dokumentet.

- En tränad modell för maskininlärning. Om du inte har någon kan se den [träna bild klassificeringsmodellen](tutorial-train-models-with-aml.md) självstudien.

## <a name="initialize-the-workspace"></a>Initiera arbetsytan

För att initiera arbetsytan, läsa in den `config.json` -fil som innehåller informationen om arbetsytan.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>Registrera modellen

Ange modell-sökväg, beskrivning och taggar för att registrera en befintlig modell.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Skapa en Docker-avbildning

Azure Kubernetes Service använder Docker-avbildningar. Använd följande steg för att skapa avbildningen:

1. Om du vill konfigurera avbildningen måste du skapa ett bedömningsskript och miljöfil. Ett exempel för att skapa filen skript och miljö finns i följande avsnitt för till exempel klassificering bild:

    * [Skapa ett bedömningsskript (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

        > [!IMPORTANT]
        > Bedömningsskriptet tar emot data som skickats från klienter och skickar det till modell för bedömning. Dokumentera datastrukturen skript och modell du förväntar dig. Den här dokumentationen blir saker enklare när du skapar en klient för att använda webbtjänsten.

    * [Skapa en miljöfil (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   I följande exempel använder de här filerna för att konfigurera avbildningen:

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. Använd konfigurationen av modellen och avbildning för att skapa avbildningen. Den här åtgärden kan ta upp till 5 minuter för att slutföra:

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>Skapa AKS-kluster

Följande kodfragment visar hur du skapar AKS-klustret. Den här processen tar cirka 20 minuter för att slutföra:

> [!IMPORTANT]
> Skapa AKS-klustret är en tid processen för arbetsytan. När du skapat kan du återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resursgruppen som innehåller den, måste du skapa ett nytt kluster nästa gång du behöver distribuera.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>Bifoga befintlig AKS-kluster (valfritt)

Om du har befintliga AKS-kluster i Azure-prenumerationen kan använda du det för att distribuera din avbildning. Följande kodfragment visar hur du kopplar ett kluster till din arbetsyta. 

> [!IMPORTANT]
> Endast AKS version 1.11.2 stöds.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

## <a name="deploy-your-web-service"></a>Distribuera webbtjänsten

Följande kodfragment visar hur du distribuerar avbildningen till AKS-klustret:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> Om det uppstår fel under distributionen kan du använda `aks_service.get_logs()` att visa loggar för AKS-tjänsten. Loggade informationen kan tyda på orsaken till felet.

## <a name="test-the-web-service"></a>Testa webbtjänsten

Använd `aks_service.run()` att testa webbtjänsten. Följande kodfragment visar hur du skickar data till tjänsten och visa förutsägelser:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>Rensa

Om du vill ta bort tjänsten, bild och modell, kan du använda följande kodavsnitt:

```python
aks_service.delete()
image.delete()
model.delete()
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [förbruka en ML-modell som distribueras som en webbtjänst](how-to-consume-web-service.md).