---
title: Då FPGA paketet för maskinvaruacceleration för Azure Machine Learning
description: Läs mer om de python-paket som är tillgängliga för användare i Azure Machine Learning.
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: routlaw
author: rloutlaw
ms.date: 05/07/2018
ms.openlocfilehash: 905f6943470acfd3051a33db6f6f3269470406d7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Azure Machine Learning maskinvaruacceleration-paketet

Azure Machine Learning maskinvaruacceleration paketet är ett pip-installeras Python-tillägg för Azure Machine Learning som möjliggör dataanalytiker och utvecklare av AI snabbt:

+ Featurize bilder med en quantized version ResNet 50

+ Träna klassificerare baserat på dessa funktioner

+ Distribuera modeller till [fältet programmable gate matriser (då FPGA)](concept-accelerate-with-fpgas.md) i Azure för mycket låg latens inferencing

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

1. Du måste skapa ett konto i Azure Machine Learning modellen Management. Mer information om hur du skapar kontot finns i [installationen av Azure Machine Learning Quickstart och arbetsstationen](../service/quickstart-installation.md) dokumentet. 

1. Paketet måste vara installerad. 

 
## <a name="how-to-install-the-package"></a>Så här installerar du paketet

1. Hämta och installera den senaste versionen av [Git](https://git-scm.com/downloads).

2. Installera [Anaconda (Python 3,6)](https://conda.io/miniconda.html)

3. Om du vill hämta en förkonfigurerad Anaconda-miljö, använder du följande kommando från Git-prompten:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
5. För att skapa miljön, öppna ett **Anaconda fråga** och använder du följande kommando:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

6. Om du vill aktivera miljön, använder du följande kommando:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Exempelkod

Den här exempelkoden vägleder dig genom använder SDK för att distribuera en modell till en då FPGA.

1. Importera paketet:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Förbearbeta avbildningen:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Featurize avbildningar:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Skapa en klassificerare:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. Skapa tjänstdefinitionen:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Förbered modellen ska köras på en då FPGA:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Distribuera modellen ska köras på en då FPGA:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Skapa klienten:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Anropa API: et:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Rapportera problem

Använd den [forum](https://aka.ms/aml-forum) rapportera eventuella problem uppstå med paketet.

## <a name="next-steps"></a>Nästa steg

[Distribuera en modell som en webbtjänst på en då FPGA](how-to-deploy-fpga-web-service.md)